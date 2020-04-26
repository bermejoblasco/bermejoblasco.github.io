---
id: 2841
title: Como exportar e importar una Azure SQL Database
date: 2016-04-14T20:18:01+01:00
author: rbermejo
layout: post
---
¿Quién no se ha encontrado alguna vez con la necesidad de exportar e importar� una base de datos?.

Seguramente es una operación que la mayoría de nosotros hemos realizado muchas veces _on-premise_, pero ¿como se realiza está operación si estamos utilizando Azure SQL Database?<!--break-->

El objetivo de este post es explicar que pasos seguir y que tener en cuenta a la hora de realizar este proceso.

Antes de empezar es necesario explicar dos conceptos que pueden conducir a confusión: **Built-in Backups** y **Import/Export**

##### **Built-in Backups**

**Azure SQL Database�** actualmente nos provee de la posibilidad de � realizar _backups_ de nuestra base de datos de forma programada y accesibles durante un periodo de tiempo: 7, 14 y 35 días dependiendo del plan que hayamos contratado� .

Con esta utilidad, el servicio nos permite tener _backups�_ de nuestras bases de datos de forma automática. Gracias a esta automatización el impacto sobre nuestros datos se minimiza cuando nos encontramos con algunos de los siguientes escenarios:

  * Recuperación ante un desastre (_recovery disaster_)
  * Recuperación ante datos corruptos.
  * Recuperación ante un borrado de datos.

Y no solo nos permite realizar estas copias de forma automática, si no que nos provee de dos formas de poder restaurarlas:

  1. **[Point in Time Restore:](https://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/ "")**� Permite restaurar la base de datos en cualquier punto en el tiempo a nivel de milésimas. Lógicamente dentro de los periodos de accesibilidad anteriormente mencionados .
  2. **Geo-Restore:�** Utiliza la última versión del _geo-redundat backup_ para realizar el _restore_.

##### **Import/Export**

**Azure SQL Database�** también nos da la posibilidad de realizar estas operaciones de forma manual mediante la exportación y la importación.

¿En que casos nos es útil?

<li style="text-align: left;">
  Cuando queremos mover la base de datos de un servidor a otro.
</li>
<li style="text-align: left;">
  Cuando queremos migrar una base de datos� <em>on-premise� </em>a <strong>Azure</strong>.
</li>

Vamos a ver más en detalle como realizar estas operaciones desde el portal de Azure, también se puede realizar mediante powershell.

# **Export**

Cuando exportamos una base de datos **SQL Azure** nos lo hace en un fichero� **BACPAC�** que contiene los datos y el esquema de la base de datos.

Cuando realizamos la exportación del fichero� **BACPAC�** este es almacenado en un **Azure blob storage**.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/exportSQLProcess.jpg" target="_blank" rel="attachment wp-att-2952"><img class="alignnone wp-image-2952 size-full" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/exportSQLProcess.jpg" alt="exportSQLProcess" width="370" height="263" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/exportSQLProcess.jpg 370w, https://www.robertbermejo.com/wp-content/uploads/2016/04/exportSQLProcess-300x213.jpg 300w" sizes="(max-width: 370px) 100vw, 370px" /></a>

Cuando se va a realizar esta operación se deben tener en cuenta varias consideraciones:

  1. El máximo tamaño del archivo **BACPAC�** que se puede almacenar en un **Azure blob storage�** es de 200 GB. Si supera este tamaño se debería utilizar� [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx ""), que es una herramienta de consola que nos permite automatizar diferentes tareas que podemos realizar sobre� la base de datos, entre ellas la exportación.
  2. El proceso no puede durar más de 20 horas, si supera este limite la operación se cancela.
  3. Esta operación no está soportada para **Azure premiun storage**.
  4. Para mantener la consistencia, se debe estar seguro de que no se realizarán escrituras sobre la base de datos mientras se está realizando la operación de exportación.

Una vez tenemos la foto global, vamos a ver como realizar la exportación.

1- Entramos en el portal

2-Buscamos la base de datos que queremos exportar.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/selectexportsql.jpg" target="_blank" rel="attachment wp-att-2962"><img class="alignnone wp-image-2962 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/selectexportsql-1024x616.jpg" alt="selectexportsql" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/selectexportsql-1024x616.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/selectexportsql-300x180.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/selectexportsql-768x462.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/selectexportsql.jpg 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

3-Click en Export.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/selectbuttonexport.jpg" target="_blank" rel="attachment wp-att-2951"><img class="alignnone wp-image-2951 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/selectbuttonexport-1024x616.jpg" alt="selectbuttonexport" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/selectbuttonexport-1024x616.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/selectbuttonexport-300x180.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/selectbuttonexport-768x462.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/selectbuttonexport.jpg 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

4-Por último seleccionamos la cuenta de _storage_ y seleccionamos el tipo de autenticación que queremos.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/exportselectstorage2.jpg" target="_blank" rel="attachment wp-att-3011"><img class="alignnone wp-image-3011 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/exportselectstorage2-1024x618.jpg" alt="exportselectstorage2" width="660" height="398" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/exportselectstorage2-1024x618.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/exportselectstorage2-300x181.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/exportselectstorage2-768x464.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/exportselectstorage2.jpg 1681w" sizes="(max-width: 660px) 100vw, 660px" /></a>

Mientras se esta realizando la exportación, podemos ver su progreso en su servidor de base de datos:

1-Buscamos el servidor de base de datos.

2- Buscamos el servidor de SQL de la base de datos, y hacemos _scroll_ hasta ver la opción _Import/Export History_� y lo seleccionamos� click.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/sqlserverexport.jpg" target="_blank" rel="attachment wp-att-2982"><img class="alignnone wp-image-2982 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/sqlserverexport-1024x614.jpg" alt="sqlserverexport" width="660" height="396" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/sqlserverexport-1024x614.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/sqlserverexport-300x180.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/sqlserverexport-768x460.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/sqlserverexport.jpg 1697w" sizes="(max-width: 660px) 100vw, 660px" /></a>

Una vez en el campo St_atus�_ ponga _Completed_ se habrá finalizado el proceso de exportación y podemos pasar a Importarla.

# **Import**

Para importar la base de datos debemos seguir los siguientes pasos:

1- Crear un nuevo Azure SQL Server o elegir uno existen. Para este post creamos uno nuevo.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/newSQLServer.jpg" target="_blank" rel="attachment wp-att-3031"><img class="alignnone wp-image-3031 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/newSQLServer-1024x616.jpg" alt="newSQLServer" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/newSQLServer-1024x616.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newSQLServer-300x180.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newSQLServer-768x462.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newSQLServer.jpg 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

2-Rellenamos los datos de creación del servidor.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer.jpg" target="_blank" rel="attachment wp-att-3041"><img class="alignnone wp-image-3041 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-1024x616.jpg" alt="newDataSqlServer" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-1024x616.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-300x180.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-768x462.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer.jpg 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

3-Click en el botón� Import database.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/importBD1.jpg" target="_blank" rel="attachment wp-att-3051"><img class="alignnone wp-image-3051 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/importBD1-1024x616.jpg" alt="importBD1" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/importBD1-1024x616.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/importBD1-300x180.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/importBD1-768x462.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/importBD1.jpg 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

4-Seleccionamos el fichero **BACPAC** del blob� storage e introducimos los datos de autenticación (los mismos que en la exportación).

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-1.jpg" target="_blank" rel="attachment wp-att-3071"><img class="alignnone wp-image-3071 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-1-1024x616.jpg" alt="newDataSqlServer" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-1-1024x616.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-1-300x180.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-1-768x462.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/newDataSqlServer-1.jpg 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

Ya tenemos disponible nuestra base de datos en el nuevo servidor.

<a href="http://www.robertbermejo.com/wp-content/uploads/2016/04/ok.jpg" target="_blank" rel="attachment wp-att-3081"><img class="alignnone wp-image-3081 size-large" src="http://www.robertbermejo.com/wp-content/uploads/2016/04/ok-1024x616.jpg" alt="ok" width="660" height="397" srcset="https://www.robertbermejo.com/wp-content/uploads/2016/04/ok-1024x616.jpg 1024w, https://www.robertbermejo.com/wp-content/uploads/2016/04/ok-300x180.jpg 300w, https://www.robertbermejo.com/wp-content/uploads/2016/04/ok-768x462.jpg 768w, https://www.robertbermejo.com/wp-content/uploads/2016/04/ok.jpg 1680w" sizes="(max-width: 660px) 100vw, 660px" /></a>

Como podéis ver es un proceso muy sencillo que si se sigue paso a paso no tiene ninguna complicación.

Hasta la próxima.