# CONACYT - SNI - User Interface

Sistema Nacional de Investigadores

## Requerimientos
- Cliente SVN
- [Código fuente UI](http://172.16.6.194:8888/conacyt-svn/CYT_componentes_transversales-conaCYT_ui/branches/CYT_componentes-transversales-conacyt_ui_13072015)
- JDK 7 u79
- Maven
- [Servidor Tomcat 7](https://tomcat.apache.org/download-70.cgi) u [Oracle WebLogic Server 12.1.3 (solo para desarrollo)](https://www.oracle.com/technetwork/middleware/weblogic/downloads/wls-main-097127.html): Clic en **"DownloadZip distribution Update 3 for Mac OSX, Windows, and Linux (190 MB)"**

Los siguientes recursos se proporcionan por USB:  
- Carpeta `configfiles`

## Instalación
### Descargar el código fuente:
	
	svn checkout http://172.16.6.194:8888/conacyt-svn/CYT_componentes_transversales-conaCYT_ui/branches/CYT_componentes-transversales-conacyt_ui_13072015 ui

### Construir el proyecto:
```
cd ui
mvn clean install
```

### Crear las variables de entorno requeridas:
- `MIIC_CONFIG={DIRECTORIO configfiles}` (ruta donde copiaste la carpeta `configfiles`)
- `APP_LOGS={CUALQUIER DIRECTORIO QUE CONTENGA UNA SUBCARPETA 'sni'}`
		
## Uso
- Desplegar el archivo `ui/generador-view-angular/target/generador-view-angular.war` en el servidor Weblogic manualmente o utilizar un plugin para tu IDE que realice esa tarea
- Visitar la siguiente URL en el navegador: [http://localhost:7780/generador-view-angular/index.html?application=SNI#/login](http://localhost:7780/generador-view-angular/index.html?application=SNI#/login)

## Preguntas frecuentes
