---
title: Ejemplos de Docker de contenedor de análisis de sentimiento
titleSuffix: Azure Cognitive Services
description: Ejemplos de Docker de contenedor de análisis de sentimiento
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: c384d9d8af932db343aad106b6e89ea87821d900
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148488"
---
### <a name="sentiment-analysis-container-docker-examples"></a>Ejemplos de Docker de contenedor de análisis de sentimiento

Los siguientes ejemplos de Docker son para el contenedor de análisis de sentimiento.

#### <a name="basic-example"></a>Ejemplo básico 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>Ejemplo de registro 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
