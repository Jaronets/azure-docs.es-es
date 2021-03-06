---
title: Implementación de un contenedor de LUIS en Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implemente el contenedor de LUIS en una instancia de Azure Container y pruébelo en un explorador web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 30fd19634f6054b8b636dabcb4ef83b118554468
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689438"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Implementación del contenedor de Language Understanding (LUIS) en instancias de Azure Container

Aprenda a implementar el contenedor de [LUIS](luis-container-howto.md) de Cognitive Services en [instancias de Azure Container](https://docs.microsoft.com/azure/container-instances/). Este procedimiento muestra la creación de un recurso de Anomaly Detector. Luego se trata la extracción de la imagen de contenedor asociada. Por último, se resalta la posibilidad de aprovechar la orquestación de los dos desde un explorador. El uso de contenedores puede desviar la atención de los desarrolladores de la administración de la infraestructura y centrarla en el desarrollo de aplicaciones.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure

El contenedor de LUIS requiere un archivo de modelo `.gz` que se extrae en tiempo de ejecución. El contenedor debe poder acceder a este archivo de modelo a través del montaje de un volumen desde la instancia de contenedor. Para obtener información acerca de cómo crear un recurso compartido de archivos de Azure, consulte [Creación de un recurso compartido de archivos en Azure Files](../../storage/files/storage-how-to-create-file-share.md). Tome nota tanto del nombre de la cuenta de Azure Storage, como de su clave y del nombre del recurso compartido de archivos, ya que los necesitará más adelante.

### <a name="export-and-upload-packaged-luis-app"></a>Exportación y carga de una aplicación de LUIS empaquetada

Para cargar el modelo de LUIS (aplicación empaquetada) en el recurso compartido de archivos de Azure, necesitará <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">exportarlo antes desde el portal de LUIS <span class="docon docon-navigate-external x-hidden-focus"></span></a>. En Azure Portal, vaya a la página de **información general** del recurso de la cuenta de almacenamiento y seleccione **Recursos compartidos de archivos**. Seleccione el nombre del recurso compartido de archivos que ha creado recientemente y, después, seleccione el botón **Cargar**.

> [!div class="mx-imgBorder"]
> ![Carga en el recurso compartido de archivos](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Cargue el modelo de archivo de LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
