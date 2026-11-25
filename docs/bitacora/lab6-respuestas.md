## 1-
Porque no todos los errores son igual de graves, podrían ser 20 errores sin importancia, lo cual sería menos alarmante que 1 solo error muy grave. Además, no contamos con una baseline con la que compararlo, no sabemos si es una cantidad de errores común o es una anomalía.

## 2-
Abre a las 22:00 y dura 4 horas (automatización.spool.log).
WEDNESDAY_WINDOW  freq=daily;byday=WED;byhour=22;byminute=0;bysecond=0                       4 TRUE

-auto optimizer stats collection: Recopila y actualiza las estadísticas de los objetos del diccionario y de usuario.

-auto space advisor (Segment Advisor): Analiza el uso de espacio dentro de los segmentos.

-sql tuning advisor: Evalúa las consultas SQL más costosas de la carga de trabajo y propone recomendaciones de ajuste.

## 3-
ORIGEN		        VALUE 				    ISSPECIFIED
SPFILE (archivo)    250					    TRUE
MEMORIA (en uso)    200

Sin el RESET, tras el arranque V$PARAMETER habría cambiado a 250 porque la línea db_files = 250 habría seguido grabada en el SPFILE.

## 4-
Processes aparece en el PFILE con un valor de 200, en cambio dbfiles no aparece:
*.processes=200 (/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/config/20261125T132952Z_02-init_desde_spfile.ora)

Porque dbfiles funcionaba con el valor por defecto, al aplicar el RESET tomó ese valor. En cambio, processes requería un parámetro concreto distinto al valor por defecto de oracle, por lo que al aplicar el RESET habría tomado un valor erróneo (El valor por defecto de oracle).

## 5-
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133007Z_02-estados-pdb.script.log)
NAME	   OPEN_MODE  RES ESTADO_GUARDADO
FREEPDB1   READ WRITE NO  OPEN

Se refleja el estado OPEN porque la CDB está levantada y abierta, pero probablemente
FREEPDB1 todavía seguía en MOUNTED, por eso devuelve el error "database closed".

## 6- 
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133223Z_03-sesiones.script.log)
  SID SERIAL# USERNAME	   SERVICE_NAME MODULE	   ACTION	  CLIENTE	  EVENT 	    CON SPID
   66	 2817 APP_ACADEMIA academia_app matriculas cierre-periodo ana.garcia@ucam PL/SQL lock timer   3 1948

   66 y 1948, con evento: PL/SQL lock timer.

-MODULE: Identifica el módulo, componente o subsistema de la aplicación desde donde nace la petición.
-ACTION: Concreta la operación o paso específico que ese módulo está ejecutando en ese instante.
-CLIENT_IDENTIFIER: Muestra la identidad del usuario final real que está detrás de un pool con USERNAMES idénticos.

## 7-
Tras CANCEL SQL quedaba 1, después de KILL SESSION quedaron 0.  
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133223Z_03-sesiones.script.log)
TRANSACCIONES_ABIERTAS_DE_LA_APP
			       1
== 5. KILL SESSION '66,2817' IMMEDIATE: la sesión termina y su UPDATE se DESHACE ==
ALTER SYSTEM KILL SESSION '66,2817' IMMEDIATE
*
ERROR at line 1:
ORA-00031: session marked for kill
Help: https://docs.oracle.com/error-help/db/ora-00031/
SESIONES_APP_VIVAS
------------------
		 0

El cambio se debe a que ALTER SYSTEM CANCEL SQL actúa únicamente sobre la llamada SQL o PL/SQL, pero
KILL SESSION destruye la sesión completa de la aplicación y fuerza la limpieza inmediata de sus recursos.

## 8-
C##
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133209Z_03-cuenta-app.script.log)
NAME		         VALUE
common_user_prefix   C##

Implica que todo usuario común que se cree debe empezar obligatoriamente por ese prefijo.

## 9-
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133317Z_04-tres-miradas.script.log)
C##OPS_RO      CDB$ROOT  
VISTA                               FILAS
USER_TABLES (lo mío)                    0
ALL_TABLES de ADMIN_ACADEMIA            0
ALL_USERS (los que veo)                37

C##OPS_RO      FREEPDB1  
VISTA                               FILAS
USER_TABLES (lo mío)                    0
ALL_TABLES de ADMIN_ACADEMIA            0
ALL_USERS (los que veo)                47

APP_ACADEMIA   FREEPDB1  
VISTA                               FILAS
USER_TABLES (lo mío)                    0
ALL_TABLES de ADMIN_ACADEMIA            1
ALL_USERS (los que veo)                47

Por ejemplo C##OPS_RO ve menos usuarios con ALL_USERS desde la CDB root que desde la FREEPDB1, ya que desde
la primera solo se ven los usuarios comunes, mientras que desde la segunda se ven también todos los
usuarios locales de esa PDB.

## 10-
5371 Lecturas lógicas por recorrido, carga de 0.34 segundos.
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133435Z_04-deltas.script.log)
MAGNITUD			      DELTA
Tiempo de reloj (s)			.33
DB time (s)				.34
DB CPU (s)				 .3
Lecturas lógicas                      53711
Ejecuciones (incluye recursivas)	143
Lecturas lógicas por recorrido         5371

El tiempo de DB time es muy cercano al DB CPU, tomando en cuenta la relación que guardan:
0.34 = 0.3 + espera, hubo una espera de 0.04 seg. 

## 11-
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133703Z_05-almacenamiento.script.log)
TABLESPACE_NAME    ASIGNADO_MB	 LIBRE_MB     MAX_MB
TBS_ACADEMIA		   100	     76.8	1024
TBS_ACADEMIA_HIST	    10	      3.9	  40

La métrica sobre la que conviene alertar es la de MAX_DB, porque con poco espacio libre 
el espacio asignado puede aumentar automáticamente, pero acercarse al espacio máximo alcanzable
sí requeriría acción inmediata.

## 12- 
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133703Z_05-almacenamiento.script.log)
ORA-01950: The user CARGA_HIST has insufficient quota on tablespace TBS_ACADEMIA_HIST.
-SYS insertó, pero CARGA_HIST, que es el propietario de la tabla, no tenía quota.
-Aumentar el tablespace no lo solucionaría porque no es un problema de espacio físico,
es que el propietario ha alcanzado un tope de permiso de capacidad.

## 13-
Aparéntemente no estuvo nada de tiempo en espera, aunque tiene un timeout máximo de 180 segundos.
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133728Z_05-resumable.script.log)
SID USERNAME	  EVENT						                            SECONDS_IN_WAIT

57  APP_ACADEMIA  statement suspended, wait error to be cleared			0

## 14-
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T133806Z_05-hwm-shrink.script.log)
-Antes: 
MOMENTO 		    FILAS    BLOQUES	 MB
antes del DELETE	20000	 2944	     23

-Después:
MOMENTO 		    FILAS    BLOQUES	 MB
tras el DELETE		2000	 2944	     23

-Porque el DELETE marca las filas como borradas a la espera de ser
sobreescritas, pero no las elimina.

## 15-
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T171950Z_06-gobierno-acceso.script.log)
RESOURCE_NAME		   LIMIT
FAILED_LOGIN_ATTEMPTS	   10
INACTIVE_ACCOUNT_TIME	   365
PASSWORD_GRACE_TIME	   7
PASSWORD_LIFE_TIME	   180
PASSWORD_LOCK_TIME	   1
PASSWORD_REUSE_MAX	   UNLIMITED
PASSWORD_REUSE_TIME	   UNLIMITED
PASSWORD_ROLLOVER_TIME	   0
PASSWORD_VERIFY_FUNCTION   NULL

## 16-
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T172017Z_06-probar-acceso.script.log)
ORA-01017: invalid credential or not authorized; logon denied
-Porque tiene permiso para modificar alumnos, pero no otras columnas.

## 17-
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T172401Z_07-objetos.script.log)
(Igual tras los 3 cambios)
ACTIVOS se invalida tras el 2 cambio y TOTAL_ACTIVOS tras el 3er cambio.

## 18-
(/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T172426Z_07-memoria.script.log)
    PGA_MB     FACTOR    HIT_PCT  OVERALLOC
       128        .25         82          3
       256         .5         82          3
       384        .75        100          0
       512          1        100          0
      1024          2        100          0
-Aumentar el factor de 1 a 2 no tendría efecto.

## 19-
247
/home/pedro/oracle-database-lab/docs/bitacora/lab6/evidencia/script/20261125T172514Z_07-adr-trazas.script.log
call     count       cpu    elapsed       disk      query    current        rows
------- ------  -------- ---------- ---------- ---------- ----------  ----------
Parse        1      0.01       0.01          0          0          0           0
Execute      1      0.00       0.00          0          0          0           0
Fetch        2      0.00       0.00          0        247          0           1
------- ------  -------- ---------- ---------- ---------- ----------  ----------
total        4      0.01       0.01          0        247          0           1



