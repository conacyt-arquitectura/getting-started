# CONACYT - SNI

Sistema Nacional de Investigadores

## Requerimientos
- SVN client
- [Código fuente SNI](http://172.16.6.194:8888/conacyt-svn/CYT_sni/SNI/)
- JDK 7 u79
- Maven
- WebLogic
- [Driver JDBC Oracle 11.2.0.1](https://www.oracle.com/technetwork/apps-tech/jdbc-112010-090769.html)  
			
		mvn install:install-file -Dfile=ojdbc6.jar -DgroupId=com.oracle -DartifactId=xdb6 -Dversion=11.2.0.4 -Dpackaging=jar

- [Driver XDB6 Oracle](https://www.oracle.com/technetwork/apps-tech/jdbc-112010-090769.html)
		
		mvn install:install-file -Dfile=xdb6.jar -DgroupId=com.oracle -DartifactId=xdb6 -Dversion=11.2.0.4 -Dpackaging=jar

- settings.xml (sobreescribe configuraciones de usuario para Maven)

## Instalación
- Descargar el código fuente:
	
		svn checkout http://172.16.6.194:8888/conacyt-svn/CYT_sni/SNI/trunk SNI

- Crear las siguientes variables de entorno:
	- `MIIC_CONFIG={DIRECTORIO configfiles}`
	- `APP_LOGS={CUALQUIER DIRECTORIO QUE CONTENGA UNA SUBCARPETA 'sni'}`
- Instalar el Servidor WebLogic 
	1. Descomprimir `wls1213_dev.zip`
	2. Seguir las instrucciones del `README.txt` para Linux o `README_WIN.txt` para Windows
- [Cambiar el puerto del Servidor WebLogic](http://localhost:7001/console/console.portal?_nfpb=true&_pageLabel=CoreServerServerTablePage)
	1. [Entorno]/[Servidores]
	2. Clic en [AdminServer]
	3. Puerto de recepción: `8004`
	4. [Guardar]
- [Crear JNDI](http://localhost:8004/console/console.portal?_nfpb=true&_pageLabel=GlobalJDBCDataSourceTablePage):
	1. Servicios/Orígenes de Datos
	2. [Nuevo]/[Origen de Datos Genérico]
	3. Nombre de JNDI: `jdbc/miic/APPL_SNI`
	4. Controlador de Base de datos: `Oracle's Driver (Thin) for Instance connections; Versions:Any` (Opción 5)
	5. Propiedades de la conexión (Obtener la información del archivo `miic-sni-web/src/main/resources/webapp/WEB-INF/config/spring/backend-db-context.xml`):
		- Nombre de la base de datos:
		- Nombre del host:
		- Puerto:
		- Nombre de usuario de Base de Datos:
		- Contraseña: 
		- Seleccionar destinos: __Marcar la casilla de "AdminServer"__
		- [Terminar]
- [Crear un servidor JMS](http://localhost:8004/console/console.portal?_nfpb=true&_pageLabel=JmsServerJMSServerTablePage):
	1. [Servicios]/[Mensajes]/[Servidores JMS]
	2. [Nuevo]
	3. Seleccionar Destinos/Destino: [AdminServer]
	4. [Terminar]
- [Crear un nuevo Módulo JMS](http://localhost:8004/console/console.portal?_nfpb=true&_pageLabel=JmsModulesTablePage)
	1. [Servidores]/[Mensajes]/[Módulos JMS]
	1. [Nuevo]
	2. Nombre:
	3. Destinos: __Marcar la casilla de "AdminServer"__
	4. [Terminar]
- Crear una fábrica de conexiones: 
	1. Clic en el módulo JMS recién creado
	2. [Nuevo]
	3. Marcar la opción __Fábrica de conexiones__
	4. Nombre del JNDI: `jms/DocuQueueConnectionFactory`
	5. Terminar
- Crear colas de mensajes
	- con los nombres de JNDI:
		- `jms/DictamenDocsQueue`
		- `jms/DocuQueue`
		- `jms/ReconsideracionDocsQueue`
	1. Clic en el módulo JMS recién creado
	2. [Nuevo]
	3. Marcar la opción __Cola__
	4. Introducir el nombre del JNDI
	5. Despliegues secundarios: [Creación de Nuevo Despliegue Secundario]
	6. Seleccionar despliegue secundario recién creado
	7. [Terminar]
		
## Uso

### Autenticación
Obtener el header `x-auth-token` de la respuesta de la siguiente petición:

		curl -v -XPOST -H "Content-type: application/json" -d '{"username": "personal.conacyt@gmail.com", "password": "dGVtcG9yYWwxREVWJTQw", "grecaptcharesponse": ""}' 'http://172.22.13.228:7780/generador-api/auth/login'

## FAQ

