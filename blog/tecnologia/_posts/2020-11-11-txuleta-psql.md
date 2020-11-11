---
layout: post
title: Txuleta psql
tags:
- psql
---

## psql

* connectar: `psql -d database user -W`
* llistar databases: `\l`
* canviar de database: `\c dbname username`
* llistar taules: `\dt`
* descriure taula: `\d table_name`
* llistar esquemes: `\dn`
* llistar funcions: `\df`
* llistar vistes `\dv`
* llistar usuaris i rols: `\du`
* versió del SGBD: `SELECT version()`
* repetir darrer comandament: `\g`
* historial de comandaments: `\s` salvar historial `\s filename`
* executar comandaments de fitxer extern: `\i filename`
* ajuda de comandament: `\h ALTER TABLE`
* veure temps d'execució: `\timing`
* editar a editor extern: `\e`
* editar funció a editor extern: `\ef`


## Backup i restore

* Backup: `pg_dump --username "username" dbname > file_name.sql`
* Restore: `psql -U user_name dbname < file_name.sql` (o usar `\i filename`)


## Crear rol

```
CREATE ROLE dbname_operator WITH
  LOGIN
  NOSUPERUSER
  INHERIT
  NOCREATEDB
  NOCREATEROLE
  NOREPLICATION;
```

## Crear DB

L'owner serà l'admin, sinó, especificar Owner=user_name

```
CREATE DATABASE database_name
    WITH     
    ENCODING = 'UTF8'
    LC_COLLATE = 'es_ES.UTF-8'
    LC_CTYPE = 'es_ES.UTF-8'    
    template=template0
    CONNECTION LIMIT = -1;
```

## Atorgar permissos

```
GRANT CONNECT, TEMPORARY ON DATABASE database_name TO PUBLIC;
GRANT ALL ON DATABASE database_name TO operator;
```

## Canviar owner

```
ALTER SEQUENCE public.seq_num OWNER TO operator;
```

## Executables

Si no tenim els executables al path, en osx estan a ```/Applications/pgAdmin 4.app/Contents/SharedSupport```
