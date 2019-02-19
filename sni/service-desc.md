# Diccionario de servicios
En esta sección encontrará un listado de los servicios que comunmente se usan en el SNI y que puede resultar de utilidad para el mantenimiento o creación de un nuevo servicio.

1. Catálogos  
  Contexto: `servicios-catalogos-web/ws/GenericAdminCatalog`
2. CVU  
  Contexto: `/ws/ServiciosCVU`
3. Notificaciones  
  Contexto: `componentes-comunes-web/ws/EnvioNotificacionService`
4. Firma electrónica  
  Contexto: `componentes-comunes-web/ws/FirmaElectronica`

Los servicios mencionados anteriormente se encuentran publicados en los siguientes proyectos web, donde también se define la ip del ambiente de desarrollo:

## Transversales
Aquí se alojan los servicios para la firma Electrónica, el generador de reportes con JasperReports, la interfaz de comunicación con el cms(sharepoint microsoft), entre otros.

La url para este recurso se compone de la siguiente manera:

    ipTransversales=http://172.22.13.228:7780/

contexto: `componentes-transversales-web/ws`

Esta entrada se encuentra definida en el archivo de propiedades miic.properties

## Catálogos
Aquí se encuentra la definición de los catálogos institucionales de: área, campos, disciplina, subdisciplina, entre otros.
    ipCatalogos=http://172.22.13.228:7780/
  
contexto: `servicios-catalogos-web/ws`

## Comunes
Aquí se encuentra los servicios de notificaciones, la conexión con RENAPO, los servicios de auditoría y seguridad.
    ipCatalogos=http://172.22.13.228:7780/
  
contexto: `componentes-comunes-web/ws`

## Implementación

### Servicios Locales.
Para la creación de un servicio local, se define del siguiente modo:
1. Defina el Bean de servicio en el archivo backend-services-context.xml. Por ejemplo para definir el bean de nombre __AddOnReporteServiceBean__:
```xml
 <bean id="AddOnReporteServiceBean"  class="mx.gob.conacyt.sni.monitoreo.web.service.impl.AddOnReporteServiceImpl"/>
```
2. Agregue un __endpoint__ para el servicio wsdl

__Endpoint WSDL__:  
```xml
<jaxws:endpoint id="AddOnReporteService" implementor="#AddOnReporteServiceBean" address="/ws/AddOnReporteService" publish="true">		
    <jaxws:features>
        <bean class="org.apache.cxf.feature.LoggingFeature"/>
    </jaxws:features>
</jaxws:endpoint>
```
3. Agregue la entrada para el servicio WADL (jaxws)

__Endpoint WADL__: 
```xml
<jaxrs:server id="AddOnReporteServiceRS" address="/rs/AddOnReporteService">
    <jaxrs:serviceBeans>
        <bean class="mx.gob.conacyt.sni.monitoreo.web.service.impl.AddOnReporteServiceImpl"/>
    </jaxrs:serviceBeans>
    <jaxrs:providers>
        <bean id="jsonProvider" class="org.codehaus.jackson.jaxrs.JacksonJsonProvider"/>
    </jaxrs:providers>  
    <jaxrs:features>
        <cxf:logging/>
    </jaxrs:features>
    <jaxrs:extensionMappings>
        <entry key="json" value="application/json"/>
    </jaxrs:extensionMappings>   
</jaxrs:server>
```

4. Cree la interfaz y su implementación del servicio:
__Interfaz__:
```java
@ApplicationPath("/")
@Consumes("application/json; charset=UTF-8")
@Produces("application/json; charset=UTF-8")
@WebService(name = "AddOnReporteService", targetNamespace = "http://service.monitoreo.addonreport.sni.conacyt.gob.mx")
@SOAPBinding(style = SOAPBinding.Style.DOCUMENT, use = SOAPBinding.Use.LITERAL)
public interface AddOnReporteService {..}
```

__Implementación__:
```java
@WebService(serviceName = "AddOnReporteService", endpointInterface = "mx.gob.conacyt.sni.sei.monitoreo.service.AddOnReporteService", targetNamespace = "http://service.monitoreo.addonreport.sni.conacyt.gob.mx")
@XmlAccessorType(XmlAccessType.FIELD)
public class AddOnReporteServiceImpl implements AddOnReporteService {...}
```

## Servicios Remotos.
