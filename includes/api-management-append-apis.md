---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186831"
---
## <a name="append-other-apis"></a>Anexión de otras API

Una API puede constar de varias API expuestas por diferentes servicios, entre las que se incluyen la especificación OpenAPI, una API de SOAP, la característica API Apps de Azure App Service, Azure Function App, Azure Logic Apps y Azure Service Fabric.

![Importación de una API](./media/api-management-append-apis/import.png)

Para anexar una API diferente a la API existente, complete los pasos siguientes. Al importar otra API, las operaciones se anexan a la API actual.

1. Vaya a la instancia de Azure API Management en Azure Portal.
2. Seleccione **API** del menú de la izquierda.
3. Haga clic en **...** junto a la API a la que desea anexar otra API.
4. Seleccione **Importar** en el menú desplegable.
5. Seleccione un servicio desde el que se va a importar una API.