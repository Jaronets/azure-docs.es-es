---
title: Back-end de varios inquilinos
titleSuffix: Azure Application Gateway
description: En esta página se proporciona una introducción a la compatibilidad de Application Gateway con los servidores back-end multiinquilino.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: efa2885ce0534c5d78bb08bbf24da59850f6ea22
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075188"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Compatibilidad de Application Gateway con back-ends multiinquilino como App Service

En los diseños arquitectónicos con varios inquilinos de los servidores web, se ejecutan varios sitios web se ejecutan en la misma instancia del servidor web. Los nombres de host se utilizan para diferenciar las distintas aplicaciones que se hospedan. De forma predeterminada, la puerta de enlace de aplicaciones no cambia el encabezado del host HTTP de entrada del cliente y lo envía sin alterar al back-end. Esta funciona bien para los miembros del grupo de back-end, conjuntos de escalado de máquinas virtuales, direcciones IP públicas, direcciones IP internas y FQDN, ya que no usan un encabezado de host específico o una extensión SNI para resolver en el punto de conexión correcto. Sin embargo, hay muchos servicios, como App Service Web Apps de Azure y Azure API Management que son multiinquilino por naturaleza y usan un encabezado de host concreto o una extensión SNI para resolver en el punto de conexión correcto. Normalmente, el nombre DNS de la aplicación, que a su vez es el nombre DNS asociado con la puerta de enlace de aplicaciones, no es el mismo que el nombre de dominio del servicio back-end. Por lo tanto, el encabezado de host de la solicitud original recibido por la puerta de enlace de la aplicación no es el mismo que el nombre de host del servicio back-end. Por este motivo, a menos que el encabezado de host de la solicitud de la puerta de enlace de aplicaciones back-end, se cambia al nombre de host del servicio back-end, los back-end multiinquilino no pueden resolver la solicitud en el punto de conexión correcto. 

Application Gateway proporciona una funcionalidad que permite a los usuarios reemplazar el encabezado de host HTTP de la solicitud según el nombre de host del servidor back-end. Esta funcionalidad permite la compatibilidad con servidores back-end multiinquilino, aplicaciones web de Azure App Service y API Management. Esta funcionalidad está disponible para las versiones v1 y v2 estándar y las SKU del WAF. 

![reemplazo del host](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> No se puede aplicar a Azure App Service Environment (ASE), ya que ASE es un recurso dedicado, a diferencia de Azure App Service, que es un recurso multiinquilino.

## <a name="override-host-header-in-the-request"></a>Reemplazo del encabezado de host en la solicitud

La posibilidad de especificar un reemplazo del host se define en la [configuración de HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) y se puede aplicar a cualquier grupo de back-end durante la creación de reglas. Se admiten las dos formas siguientes de reemplazar el encabezado de host y la extensión SNI en los back-end multiinquilino:

- La posibilidad de establecer el nombre de host en un valor fijo que se especifica de forma explícita en la configuración de HTTP. Esta funcionalidad garantiza que el encabezado de host se reemplaza por este valor para todo el tráfico que va al grupo de servidores back-end donde se aplica la configuración de HTTP concreta. Al usar SSL de extremo a extremo, este nombre de host invalidado se usa en la extensión SNI. Esta funcionalidad permite escenarios donde un grupo de servidores back-end espera un encabezado de host que es diferente del encabezado de host del cliente de entrada.

- La posibilidad de obtener el nombre de host de la dirección IP o FQDN de los miembros del grupo de servidores back-end. La configuración de HTTP también permite seleccionar dinámicamente el nombre de host del FQDN de un miembro del grupo de back-end si está configurado con la opción de derivar el nombre de host de un miembro de grupo de back-end individual. Al usar SSL de extremo a extremo, este nombre de host se obtiene del FQDN y se usa en la extensión SNI. Esta funcionalidad permite escenarios donde un grupo de servidores back-end puede tener dos o más servicios PaaS multiinquilino, como Azure Web Apps y el encabezado de host de la solicitud para que cada miembro contenga el nombre de host obtenido de su FQDN. Para implementar este escenario se usa un modificador en Configuración de HTTP llamado [Elegir nombre de host de dirección de back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) que reemplazará dinámicamente el encabezado de host invalidará el encabezado de host en la solicitud original por el mencionado en el grupo de back-end.  Por ejemplo, si el FQDN del grupo de back-end contiene "contoso11.azurewebsites.net" y "contoso22.azurewebsites.net", se reemplazará el encabezado de host de la solicitud original, que es contoso.com, por contoso11.azurewebsites.net o contoso22.azurewebsites.net cuando la solicitud se envíe al servidor back-end adecuado. 

  ![escenario de aplicación web](./media/application-gateway-web-app-overview/scenario.png)

Con esta funcionalidad, los clientes especifican las opciones en la configuración de HTTP y los sondeos personalizados para la configuración adecuada. Esta configuración se asocia luego a un agente de escucha y un grupo de servidores back-end mediante una regla.

## <a name="special-considerations"></a>Consideraciones especiales

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Terminación SSL y SSL de un extremo a otro con servicios multiinquilino

Tanto la terminación SSL como el cifrado SSL de un extremo a otro son compatibles con los servicios multiinquilino. En el caso de la terminación SSL en la puerta de enlace de aplicaciones, sigue siendo necesario agregar el certificado SSL al cliente de escucha de la puerta de enlace de aplicaciones. Sin embargo, en el caso del SSL de un extremo a otro, los servicios de Azure de confianza, como App Service Web Apps de Azure, no requieren crear listas blancas en los back-end de la puerta de enlace de aplicaciones. Por lo tanto, no hay necesidad de agregar certificados de autenticación. 

![SSL de un extremo a otro](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Tenga en cuenta que en la imagen anterior, no se necesita agregar certificados de autenticación cuando App Service se selecciona como back-end.

### <a name="health-probe"></a>Sondeo de mantenimiento

El reemplazo del encabezado de host en la **configuración de HTTP** solo afecta a la solicitud y a su enrutamiento, no afecta el comportamiento del sondeo de estado. Para que la funcionalidad de extremo a extremo funcione, el sondeo y la configuración de HTTP se deben modificar para reflejar la configuración correcta. Además de proporcionar la capacidad de especificar un encabezado de host en la configuración del sondeo, los sondeos personalizados admiten también la posibilidad de derivar el encabezado de host de la configuración de HTTP actual. Esta configuración puede especificarse mediante el parámetro `PickHostNameFromBackendHttpSettings` en la configuración de sondeo.

### <a name="redirection-to-app-services-url-scenario"></a>Escenario de redireccionamiento a la dirección URL de App Service

Puede haber escenarios en los que el nombre de host de la respuesta de App Service pueda dirigir el explorador del usuario final al nombre de host *. azurewebsites.net, en lugar de al dominio asociado con Application Gateway. Este problema puede ocurrir cuando:

- Ha configurado el redireccionamiento en su instancia de App Service. El redireccionamiento puede ser tan sencillo como agregar una barra diagonal final a la solicitud.
- Tiene la autenticación de Azure AD, lo que provoca el redireccionamiento.

Para resolver estos casos, consulte [Solución de problemas de redireccionamiento a la dirección URL de App Service](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar una puerta de enlace de aplicaciones con una aplicación multiinquilino como App Service Web Apps de Azure como miembro del grupo de back-ends, visite [Configuración de App Service Web Apps con Application Gateway](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
