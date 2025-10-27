# Activitat Pràctica P0.0  ![logo git](./cap_mark/logo_git.png)

**Elaborat per:** Adrián González, Sharam Khan, Carlos Rodríguez, Francisco Díaz  
**Data:** Octubre 2025/26  

**Pràctica:**  
Desplegament d’infraestructura

![cap 0](./cap_mark/cap_0.png)

## Índex

1. [Introducció](#introducció)
2. [Esquema de les màquines](#esquema-de-les-màquines)
3. [Màquines Virtuals](@maquines-virtuals)  
    - [Web Server](#web-server)
    - [DHCP i DNS](#dhcp-i-dns)
    - [FTP](#ftp)
    - [BBDD](#bbdd)
    - [SSH](#ssh)
4. [Conclusions](#conclusions)

## Introducció
En aquesta pràctica es prepara i desplega la infraestructura d’una aplicació multicapa que integra diversos serveis de xarxa i sistemes, com ara servidor web, monitor de xarxa, accés SSH, base de dades, serveis DHCP, DNS i FTP. L’objectiu és dissenyar, configurar i documentar un entorn complet que permeti el funcionament coordinat d’aquests serveis dins d’una arquitectura organitzada en diferents xarxes (DMZ, Intranet i NAT).

El projecte es desenvoluparà durant sis setmanes, dividit en tres sprints quinzenals, i inclourà la planificació de tasques al Proofhub, la configuració dels equips, la creació d’un repositori Git amb tota la documentació i la implementació d’una aplicació que mostri les dades carregades a la base de dades.


## Esquema de les màquines
Hem decidit distribuir les màquines i els serveis d’aquesta manera, ja que considerem que és l’opció més òptima i senzilla de configurar i gestionar.

![cap web 0](./cap_mark/cap_1.png)


## Màquines Virtuals

### Web Server
Configurem el nom de l’equip (hostname) i la xarxa (adreça IP).

![cap web 0](./cap_mark/cap_web_0.png)
---

Instal·lem Nginx.

```bash
sudo apt install nginx
```

```bash
sudo systemctl status nginx
```

![cap web 1](./cap_mark/cap_web_1.png)
---

Obrim el navegador i comprovem que funciona.

![cap web 2](./cap_mark/cap_web_2.png)

```bash
http://IP_WEB_SERVER
```

![cap web 3](./cap_mark/cap_web_3.png)
-
### DHCP i DNS


### FTP
Creem un usuari i li assignem els permisos corresponents.

![cap ftp 1](./cap_mark/cap_ftp_1.png)
---

Canviem el nom de l’equip (hostname).

![cap ftp 2](./cap_mark/cap_ftp_2.png)
---

Configurem les adreces IP amb Netplan.

![cap ftp 3](./cap_mark/cap_ftp_3.png)

![cap ftp 4](./cap_mark/cap_ftp_4.png)
---

Instal·lem el servidor FTP.

```bash
sudo apt install vsftpd -y
```

![cap ftp 5](./cap_mark/cap_ftp_5.png)
---

Configuració FTP (/etc/vsftpd.conf)

```bash
sudo nano /etc/vsftpd.conf
```

![cap ftp 6](./cap_mark/cap_ftp_6.png)
---

Recarreguem i activem el servei.

```bash
sudo systemctl restart vsftpd
```
```bash
sudo systemctl enable nginx
```

![cap ftp 7](./cap_mark/cap_ftp_7.png)
---

Creem la carpeta destinada al servidor FTP.

```bash
sudo mkdir -p ftp/files
```

![cap ftp 8](./cap_mark/cap_ftp_8.png)
---

Assignem els permisos corresponents.

```bash
sudo chown root:root /home/USER/ftp
```
```bash
sudo chown -R root:root /home/USER/ftp
```
```bash
sudo chmod 755 /home/USER/ftp
```

![cap ftp 9](./cap_mark/cap_ftp_9.png)
---

Comprovem el funcionament del servidor FTP.

```bash
sftp IP_SERVER_FTP
```

![cap ftp 10](./cap_mark/cap_ftp_10.png)
---

### BBDD

Configuració de xarxa

![cap bbdd 1](./cap_mark/cap_bbdd_1.png)
---

Creem l’usuari bchecker i li assignem els privilegis corresponents

![cap bbdd 2](./cap_mark/cap_bbdd_2.png)
---

Canviem el nom de l’equip (hostname)

![cap bbdd 3](./cap_mark/cap_bbdd_3.png)
---

Instal·lem MySQL

```bash
sudo apt install mysql-server -y
```

```bash
sudo systemctl status mysql
```

![cap bbdd 4](./cap_mark/cap_bbdd_4.png)
---

Creem l’usuari bchecker amb els privilegis corresponents.

![cap bbdd 5](./cap_mark/cap_bbdd_5.png)
---

Creació de la taula, i dels seus atributs:

```bash
show tables
```

![cap bbdd 5](./cap_mark/cap_bbdd_6.png)

```bash
desc NOM_TAULA
```

![cap bbdd 5](./cap_mark/cap_bbdd_7.png)

```bash
mysql> LOAD DATA INFILE '/var/lib/mysql-files/escuelas_barna.csv' 
    -> INTO TABLE escuelas_barna_plana 
    -> CHARACTER SET utf8mb4 
    -> FIELDS TERMINATED BY ',' 
    -> ENCLOSED BY '"' 
    -> LINES TERMINATED BY '\n' 
    -> IGNORE 1 ROWS;
ERROR 1300 (HY000): Invalid utf8mb4 character string: ''
mysql> 

```
---

Hem decidit injectar la informació a través d’un LOAD TABLE del .csv, ja que ho vam considerar molt més pràctic que introduir les columnes una per una:

```bash
LOAD DATA INFILE '/var/lib/mysql-files/escuelas_barna_utf8.csv'
INTO TABLE escuelas_barna_plana
CHARACTER SET utf8mb4
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
(
    -- Identificadors principals
    @temp_register_id,
    name,
    @temp_institution_id,
    institution_name,

    -- Dates de creació i modificació
    created,
    modified,

    -- Adreça
    addresses_roadtype_id,
    addresses_roadtype_name,
    @temp_addresses_road_id,
    addresses_road_name,
    addresses_start_street_number,
    addresses_end_street_number,
    addresses_neighborhood_id,
    addresses_neighborhood_name,
    addresses_district_id,
    addresses_district_name,
    addresses_zip_code,
    addresses_town,
    @temp_addresses_main_address,
    addresses_type,

    -- Valors i atributs
    @temp_values_id,
    @temp_values_attribute_id,
    values_category,
    values_attribute_name,
    values_value,
    @temp_values_outstanding,
    values_description,

    -- Filtres secundaris
    @temp_secondary_filters_id,
    secondary_filters_name,
    secondary_filters_fullpath,
    secondary_filters_tree,
    @temp_secondary_filters_asia_id,

    -- Coordenades geogràfiques
    @temp_geo_epgs_25831_x,  
    @temp_geo_epgs_25831_y,  
    @temp_geo_epgs_4326_lat, 
    @temp_geo_epgs_4326_lon, 

    -- Dates i horaris
    estimated_dates,
    @temp_start_date,
    @temp_end_date,
    timetable
)
```
```bash
SET 
    -- Identificadors processats
    register_id                = SUBSTRING(@temp_register_id, 2) + 0,
    institution_id             = NULLIF(@temp_institution_id, ''),
    values_id                  = NULLIF(@temp_values_id, ''),
    secondary_filters_id       = NULLIF(@temp_secondary_filters_id, ''),
    secondary_filters_asia_id  = NULLIF(@temp_secondary_filters_asia_id, ''),
    values_attribute_id        = NULLIF(@temp_values_attribute_id, ''),
    addresses_road_id          = NULLIF(@temp_addresses_road_id, ''),

    -- Valors booleans
    addresses_main_address     = IF(@temp_addresses_main_address = 'True', 1, 0),
    values_outstanding         = IF(@temp_values_outstanding = 'True', 1, 0),

    -- Dates
    start_date                 = NULLIF(@temp_start_date, ''),
    end_date                   = NULLIF(@temp_end_date, ''),

    -- Coordenades
    geo_epgs_25831_x           = NULLIF(@temp_geo_epgs_25831_x, ''),  
    geo_epgs_25831_y           = NULLIF(@temp_geo_epgs_25831_y, ''), 
    geo_epgs_4326_lat          = NULLIF(@temp_geo_epgs_4326_lat, ''), 
    geo_epgs_4326_lon          = NULLIF(@temp_geo_epgs_4326_lon, '');
```

---

Durant el procés vam patir diversos errors de codificació del CSV, per tant, vam haver de passar l’arxiu a utf-8 per evitar problemes.

Una vegada hem fet les correccions, comprovem que les dades s’han inserit de forma correcta:

```bash
SELECT COUNT(*) FROM NOM_TAULA
```

![cap bbdd 5](./cap_mark/cap_bbdd_8.png)




### SSH

## Conclusions