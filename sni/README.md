# CONACYT - SNI

Sistema Nacional de Investigadores

## Requerimientos
- Cliente SVN
- [Código fuente SNI](http://172.16.6.194:8888/conacyt-svn/CYT_sni/SNI/)
- JDK 7
- Maven
- [Oracle WebLogic Server 12.1.3 (solo para desarrollo)](https://www.oracle.com/technetwork/middleware/weblogic/downloads/wls-main-097127.html): Clic en **"Zip distribution Update 3 for Mac OSX, Windows, and Linux (190 MB)"**
- SOAPUI (opcional) para consumir los servicios

Los siguientes recursos se proporcionan por USB:  
- `settings.xml` (configuraciones de Maven)
- Carpeta `configfiles`

## Instalación
### Descargar el código fuente:
	
	svn checkout --username={MI_NOMBRE_DE_USUARIO} http://172.16.6.194:8888/conacyt-svn/CYT_sni/SNI/trunk sni

### Sobreescribir configuraciones de Maven
1. Copiar el archivo `settings.xml` a la carpeta `{MI_CARPETA_DE_USUARIO/.m2/}`. Esto sobreescribe configuraciones de Maven a nivel de usuario
### Construir el proyecto:

```
cd sni
mvn clean install -DskipTests
```

### Crear las variables de entorno requeridas:
- `MIIC_CONFIG={DIRECTORIO configfiles}` (ruta donde copiaste la carpeta `configfiles`)
- `APP_LOGS={CUALQUIER DIRECTORIO QUE CONTENGA UNA SUBCARPETA 'sni'}`

### Levantar con WebLogic
#### Instalar el Servidor WebLogic 
1. Descomprimir el zip del **Oracle WebLogic Server 12.1.3**
2. Seguir las instrucciones del `README.txt` para Linux o `README_WIN.txt` para Windows. Es recomendable no crear un dominio por defecto mediante consola, en su lugar se puede utilizar la interfaz gráfica ejecutando `wlserver/common/bin/config.{sh|cmd}`

#### Cambiar el puerto del Servidor WebLogic
1. [Entorno/Servidores](http://localhost:7001/console/console.portal?_nfpb=true&_pageLabel=CoreServerServerTablePage)
2. Clic en tu servidor
3. Puerto de recepción: `8004`
4. [Guardar]

#### Crear JNDI
1. [Servicios/Orígenes de Datos](http://localhost:8004/console/console.portal?_nfpb=true&_pageLabel=GlobalJDBCDataSourceTablePage)
2. [Nuevo]/[Origen de Datos Genérico]
3. Nombre: `JDBC Data Source-SNI`
4. Nombre de JNDI: `jdbc/miic/APPL_SNI`
5. Tipo de Base de datos: `Oracle`
6. Controlador de Base de datos: `Oracle's Driver (Thin) for Instance connections; Versions:Any` (Opción 5)
7. Propiedades de la conexión (Obtener la información del archivo `miic-sni-web/src/main/resources/webapp/WEB-INF/config/spring/backend-db-context.xml`):
	- Nombre de la base de datos:
	- Nombre del host:
	- Puerto:
	- Nombre de usuario de Base de Datos:
	- Contraseña: 
	- Usa el botón **[Probar conexión]** para verificar que los datos de conexión son correctos
	- [Siguiente]
	- Seleccionar destinos: __Marcar la casilla de "AdminServer"__
	- [Terminar]

#### Crear un servidor JMS
1. [Servicios/Mensajes/Servidores JMS](http://localhost:8004/console/console.portal?_nfpb=true&_pageLabel=JmsServerJMSServerTablePage)
2. [Nuevo]
3. Nombre: 
4. [Siguiente]
5. Seleccionar Destinos/Destino: [AdminServer]
6. [Terminar]

#### Crear un nuevo Módulo JMS
1. [Servicios/Mensajes/Módulos JMS](http://localhost:8004/console/console.portal?_nfpb=true&_pageLabel=JmsModulesTablePage)
2. [Nuevo]
3. Nombre:
4. Destinos: __Marcar la casilla de "AdminServer"__
5. [Terminar]

#### Crear una fábrica de conexiones: 
1. Clic en el módulo JMS recién creado
2. [Nuevo]
3. Marcar la opción __Fábrica de conexiones__
4. Nombre del JNDI: `jms/DocuQueueConnectionFactory`
5. Selecionar el servidor destino
6. Terminar

#### Crear colas de mensajes
1. Clic en el módulo JMS recién creado
2. [Nuevo]
3. Marcar la opción __Cola__
4. Introducir el nombre del JNDI: `jms/DictamenDocsQueue`
5. Despliegues secundarios: [Creación de Nuevo Despliegue Secundario]
6. Seleccionar despliegue secundario recién creado
7. Selecionar el servidor destino
8. [Terminar]  

- repetir los pasos anteriores para crear otras dos colas con los nombres de JNDI:
	- `jms/DocuQueue`
	- `jms/ReconsideracionDocsQueue`

### Levantar con Tomcat
#### Deshabilitar la carga del contexto JMS
En el archivo `miic-sni-web/src/main/webapp/WEB-INF/config/spring/backend-application-context.xml` se debe comentar la importación del contexto JMS para que quede de la siguiente manera:  

```xml
	<import resource="backend-business-context.xml" />
	<import resource="backend-db-context.xml" />
	<import resource="backend-services-context.xml" />
	<import resource="backend-security-context.xml" />
<!--<import resource="backend-jms-context.xml" /> -->

```

#### Modificar los componentes `DocumentoMessageSender` y `DocumentoMessageListener`
Se modifican de la siguiente manera:  

```java
public class DocumentoMessageSender {

	//...

	@Autowired(required = false)
	@Qualifier("jmsTemplate")
  	private JmsTemplate jmsTemplate;
 
	@Autowired(required = false)
	@Qualifier("jmsTemplateDictamen")
	private JmsTemplate jmsTemplateDictamen;
	  
	@Autowired(required = false)
	@Qualifier("jmsTemplateReconsideracion")
	private JmsTemplate jmsTemplateReconsideracion;

	//...
}
```
```java
public class DocumentoMessageListener {

	//...
	
	@Autowired(required = false)
	protected FirmaDao firmaDao;
	
	@Autowired(required = false)
	protected FirmaElectronicaSniBO firmaElectronicaSniBO;
	
	//...
}
```

#### Adición de dependencia JMS API
Se añade la dependencia `javax.jms-api` al proyecto web:

En el archivo `miic-sni-web/pom.xml` se añade la siguiente dependencia:

```xml
	<dependencies>
		<!-- ... -->
		<dependency>
			<groupId>javax.jms</groupId>
			<artifactId>javax.jms-api</artifactId>
			<version>2.0.1</version>
		</dependency>
	</dependencies>
```

#### Cambia el tipo de _Data Source_
Cambia el tipo de _Data Source_ de JNDI por JDBC:

```xml
	<!-- Beans para DOCS -->
	
	<!--
  	<bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean">  
		<property name="jndiName" value="${jndiSNI}" /> 
  	</bean>   
  	-->
		    
  	<bean id="dataSource"  
		  class="org.springframework.jdbc.datasource.TransactionAwareDataSourceProxy"> 
		  <property name="targetDataSource">
			   <bean class="org.apache.commons.dbcp2.BasicDataSource">
				    <property name="driverClassName" value="oracle.jdbc.OracleDriver" />
					<property name="url" value="jdbc:oracle:thin:@{IP}:{PUERTO}:{NOMBRE_DE_BD}" /> 
		            <property name="username" value="{NOMBRE_DE_USUARIO}" />
		            <property name="password" value="{CONTRASEÑA}" />
	            </bean>
            </property>
     </bean>
```

#### Argumentos para ejecutar con Tomcat
Añade los siguientes argumentos para pasar a la VM:  

```
 -DMIIC_CONFIG="{RUTA_DE_CARPETA_CONFIGFILES}" -DAPP_LOGS="{RUTA_DONDE_ESCRIBIR_LOGS}" -Xms1024m -Xmx1536m -XX:NewSize=256m -XX:MaxNewSize=356m -XX:PermSize=256m -XX:MaxPermSize=768m -Dfile.encoding=UTF-8 -DLOG_APP_LEVEL=error -DLOG_ROOT_LEVEL=error -DLOG_CXF_LEVEL=debug
```

## Uso
- Desplegar el archivo `sni/miic-sni-web/target/miic-sni-web.war` en el servidor Weblogic manualmente o utilizar un plugin para tu IDE que realice esa tarea
- Verificar que se muestra el índice de servicios en [http://localhost:8004/miic-sni-web](http://localhost:8004/miic-sni-web)

### Autenticación
Obtener el header `x-auth-token` de la respuesta de la siguiente petición (reemplazar `MY_USERNAME` y `MY_PASSWORD` por tus credenciales):

	curl -v -XPOST -H "Content-type: application/json" -d '{"username": "{MY_USERNAME}", "password": "{MY_PASSWORD}", "grecaptcharesponse": ""}' 'http://172.22.13.228:7780/generador-api/auth/login'

## Preguntas frecuentes
### Faltan dependencias de Oracle `ojdbc` y/o `xdb6`  
También puedes instalar las dependencias de Oracle a mano:
- [Driver JDBC Oracle 11.2.0.1](https://www.oracle.com/technetwork/apps-tech/jdbc-112010-090769.html)  
		
		mvn install:install-file -Dfile=ojdbc6.jar -DgroupId=com.oracle -DartifactId=xdb6 -Dversion=11.2.0.4 -Dpackaging=jar

- [Driver XDB6 Oracle](https://www.oracle.com/technetwork/apps-tech/jdbc-112010-090769.html)
	
		mvn install:install-file -Dfile=xdb6.jar -DgroupId=com.oracle -DartifactId=xdb6 -Dversion=11.2.0.4 -Dpackaging=jar

### Problema con la variable de entorno APP_LOGS
En el archivo `sni/miic-sni-web/src/main/resources/log4j2.xml` reemplaza la línea `<Property name="log-path">${sys:APP_LOGS}</Property>` por:  
- `<Property name="log-path">${env:APP_LOGS}</Property>` para que busque APP_LOGS como variable de entorno
- ó `<Property name="log-path">${C:\Users\CONACYT\Documents\logs}</Property>` para definir de manera estática la ruta (esta carpeta debe contener una subcarpeta `sni`)

### Could not obtain the localhost address. The most likely cause is an error in the network configuration of this machine
Clic para ver la [Solución](https://blogs.oracle.com/luzmestre/javalangassertionerror:-could-not-obtain-the-localhost-address-in-a-new-12c-install)

### ¿Cómo depurar el código JS en el front-end?
En la url de la aplicación sustituye la palabra `index` por `dev` para cargar el código JavaScript sin minificar ni ofuscar.

### El front-end no realiza la petición a mi servidor local
Algunas de las posibles soluciones son:
- Asegúrate de haber modificado el __servicio__ en la base de datos para que apunte a tu servidor local (el campo `ruta_servicio` de la tabla `cat_servicio`)
- Asegúrate de que tu servidor local esté escuchando en un puerto dentro del rango __8001 - 8015__, de otra manera no funcionará.

### ¿Cómo recargar una pantalla sin que me regrese a la pantalla de autenticación?
Simula un envío de parámetros añadiendo un signo `?` al final de la url
