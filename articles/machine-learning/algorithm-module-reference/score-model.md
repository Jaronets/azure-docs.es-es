---
title: 'Puntuar modelo: referencia para los módulos'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el módulo Puntuar modelo en Azure Machine Learning para generar predicciones mediante un modelo entrenado de clasificación o regresión.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: e7ab953a7ac0907244ebaab70b3b86cbe6f0f4d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497645"
---
# <a name="score-model-module"></a>Score Model module

En este artículo se describe un módulo del diseñador de Azure Machine Learning (versión preliminar).

Utilice este módulo para generar predicciones con un modelo entrenado de clasificación o regresión.

## <a name="how-to-use"></a>Modo de uso

1. Agregue el módulo **Score Model** (Puntuar modelo) a la canalización.

2. Adjunte un modelo entrenado y un conjunto de datos que contenga los nuevos datos de entrada. 

    Los datos deben estar en un formato compatible con el tipo de modelo entrenado que usa. El esquema del conjunto de datos de entrada también debe coincidir generalmente con el esquema de los datos utilizados para entrenar el modelo.

3. Ejecución de la canalización

## <a name="results"></a>Results

Después de haber generado un conjunto de puntuaciones mediante [Puntuar modelo](./score-model.md):

+ Para generar un conjunto de métricas utilizadas para evaluar la precisión del modelo (rendimiento).  Puede conectar el conjunto de datos puntuado a [Evaluar modelo](./evaluate-model.md). 
+ Haga clic con el botón derecho en el módulo y seleccione **Visualizar** para ver un ejemplo de los resultados.
+ Guarde los resultados en un conjunto de datos.

La puntuación, o el valor de predicción, puede estar en muchos formatos diferentes, según el modelo y los datos de entrada:

- Para los modelos de clasificación, las salidas de [Puntuar modelo](./score-model.md) dan como resultado un valor de predicción para la clase, así como la probabilidad del valor de predicción.
- Para los modelos de regresión, [Puntuar modelo](./score-model.md) genera simplemente el valor numérico de predicción.
- Para los modelos de clasificación de imagen, la puntuación puede ser la clase de objeto en la imagen o un valor booleano que indica si se ha encontrado una característica determinada.

## <a name="publish-scores-as-a-web-service"></a>Publicar puntuaciones como servicio web

Un uso común de la puntuación es devolver la salida como parte de un servicio web predictivo. Para más información, consulte este tutorial sobre cómo crear un servicio web basado en una canalización en Azure Machine Learning:

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning. 