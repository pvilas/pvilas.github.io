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
