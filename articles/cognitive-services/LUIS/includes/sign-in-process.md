---
title: archivo de inclusión
description: archivo de inclusión
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 5e7feac35ddd607d36cb0ddd3bae10cf1b9bc7a7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278594"
---
## <a name="sign-in-to-luis-portal"></a>Inicio de sesión en el portal de LUIS

Un nuevo usuario de LUIS debe seguir este procedimiento:

1. Inicie sesión en el [portal de LUIS (versión preliminar)](https://preview.luis.ai),seleccione su país y acepte las condiciones de uso. Si ve **Mis aplicaciones**, significa que ya existe un recurso de LUIS y que debe omitirlo para crear una aplicación.

1. Seleccione **Create Azure resource** (Crear recurso de Azure) y, después, seleccione **Create an authoring resource to migrate your apps to** (Crear un recurso de Azure al que migrar las aplicaciones).

    ![Elección de un tipo de recurso de creación de Language Understanding](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Rellene los detalles del recurso.

    ![Creación de un recurso de creación](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Al **crear un nuevo recurso de creación**, proporcione la información siguiente: 

    * **Nombre del recurso**: el nombre personalizado que elija, que se usa como parte de la dirección URL para las consultas de punto de conexión de creación y predicción.
    * **Inquilino**: el inquilino con el que está asociada la suscripción de Azure. 
    * **Nombre de la suscripción**: la suscripción que se facturará por el recurso.
    * **Grupo de recursos**: nombre del grupo de recursos personalizado que elija o cree. Los grupos de recursos le permiten agrupar los recursos de Azure para el acceso y la administración. 
    * **Ubicación**: la opción de ubicación se basa en la selección del **grupo de recursos**.
    * **Plan de tarifa**: el plan de tarifa determina el número máximo de transacciones por segundo y mes.

1. Se muestra un resumen del recurso que se va a crear. Seleccione **Next** (Siguiente).

    ![Creación de un recurso de creación](../media/sign-in/sign-in-confirm-key-selection.png)

1. Para confirmar, seleccione **Continuar**. 

    ![Creación de un recurso de creación](../media/sign-in/sign-in-confirm-continue.png)