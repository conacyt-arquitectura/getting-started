# Diccionario de servicios
En esta sección encontrará un listado de los servicios comunes que puede usar en su aplicativo.

<ol type="1">
  <li>Catálogos</li>
  Contexto: servicios-catalogos-web/ws/GenericAdminCatalog
  <li>Cvu</li>
  Contexto: /ws/ServiciosCVU
  <li>Notificaciones</li>
  Contexto: componentes-comunes-web/ws/EnvioNotificacionService
  <li>Firma Electrónica</li>
  Contexto: componentes-comunes-web/ws/FirmaElectronica
  <li>Transversales</li>
  Aquí se encuentran los servicios para la firma Electrónica, el generador de reportes con JasperReports, la interfaz de comunicación con el cms(sharepoint microsoft), entre otros.
  El la url para este recurso se compone de la siguiente forma:
  Esta entrada se encuentra definida en el archivod de propiedades miic.properties
  ipTransversales=http://172.22.13.228:7780/
  y el contexto para consumir los recursos es el siguiente:
  componentes-transversales-web/ws/
  <li>Catálogos</li>
  La definición de los catálogos institucionales: área, campos, disciplina, subdisciplina, entre otros.
  <li>Comunes</li>
  Aquí se encuentra los servicios de notificaciones, la conexión con RENAPO, los servicios de auditoría y seguridad.  
</ol>

# Implementación

## Servicios Locales.
Para la creación de un servicio local, se define del siguiente modo:
<ol type="1">
<li>Defina el Bean de servicio en el archivo backend-services-context.xml</li> Por ejemplo para definir el bean de nombre
  <dl>
    <dt>AddOnReporteServiceBean: </dt>
    <dd>
      <em>`<bean id="AddOnReporteServiceBean"  class="mx.gob.conacyt.sni.monitoreo.web.service.impl.AddOnRepo> rteServiceImpl" />`
      </em>
    </dd>
  </dl>
<li>
  Agregue un <em>endpoint</em> para el servicio wsdl
  <dl>
    <dt>Endpoint WSDL: </dt>
    <dd>
      <em>
        `<jaxws:endpoint id="AddOnReporteService" implementor="#AddOnReporteServiceBean" address="/ws/AddOnReporteService" publish="true">		
          <jaxws:features>
              <bean class="org.apache.cxf.feature.LoggingFeature" />
          </jaxws:features>
        </jaxws:endpoint>`
      </em>
    </dd>
  </dl>
  <li>
    Agregue la entrada para el servicio WADL (jaxws)
    <dl>
      <dt>Endpoint WADL: </dt>
      <dd>
        <em>
          `<jaxrs:server id="AddOnReporteServiceRS" address="/rs/AddOnReporteService">
                <jaxrs:serviceBeans>
                      <bean class="mx.gob.conacyt.sni.monitoreo.web.service.impl.AddOnReporteServiceImpl" />
                </jaxrs:serviceBeans>
               <jaxrs:providers>
                     <bean id="jsonProvider" class="org.codehaus.jackson.jaxrs.JacksonJsonProvider"/>
               </jaxrs:providers>  
                <jaxrs:features>
                      <cxf:logging/>
                </jaxrs:features>
                <jaxrs:extensionMappings>
                      <entry key="json" value="application/json" />
                </jaxrs:extensionMappings>   
         </jaxrs:server>`
        </em>
      </dd>
    </dl>

  </li>
</li>
<li>Cree la interfaz y su implementación del servicio:
  <dl>
    <dt>Interfaz: </dt>
    <dd>
      <em>`
        @ApplicationPath("/")
        @Consumes("application/json; charset=UTF-8")
        @Produces("application/json; charset=UTF-8")
        @WebService(name = "AddOnReporteService", targetNamespace = "http://service.monitoreo.addonreport.sni.conacyt.gob.mx")
        @SOAPBinding(style = SOAPBinding.Style.DOCUMENT, use = SOAPBinding.Use.LITERAL)
        public interface AddOnReporteService {..}`
      </em>
    </dd>
    <dt>Implementación: </dt>
    <dd>
    <em>
          `@WebService(serviceName = "AddOnReporteService", endpointInterface = "mx.gob.conacyt.sni.sei.monitoreo.service.AddOnReporteService", targetNamespace = "http://service.monitoreo.addonreport.sni.conacyt.gob.mx")
      @XmlAccessorType(XmlAccessType.FIELD)
      public class AddOnReporteServiceImpl implements AddOnReporteService {...}`
    </em>
    </dd>
  </dl>
</li>

</ol>

## Servicios Remotos.
