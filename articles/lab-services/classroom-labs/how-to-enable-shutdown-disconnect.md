---
title: Habilitación del apagado de las máquinas virtuales al desconectar Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo habilitar o deshabilitar el apagado automático de las máquinas virtuales cuando se desconecta una conexión a escritorio remoto.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: e615170952ea2987639a0bfc269ad5a1692e1e59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475238"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Habilitación del apagado automático de las máquinas virtuales al desconectarse
En este artículo se muestra cómo habilitar o deshabilitar el apagado automático de máquinas virtuales de laboratorio **Windows 10** (plantilla o alumno) después de cerrar una conexión de escritorio remoto. También puede especificar cuánto tiempo deben esperar las máquinas virtuales para que el usuario se vuelva a conectar antes de que se cierren automáticamente.

Un administrador de cuentas de laboratorio puede configurar esta opción para la cuenta de laboratorio en la que crea laboratorios. Para obtener más información, consulte [Configurar el apagado automático de las máquinas virtuales al desconectarse de una cuenta de laboratorio](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Como propietario de un laboratorio, puede invalidar la configuración al crear un laboratorio o después de su creación. 

## <a name="configure-when-creating-a-lab"></a>Configuración al crear un laboratorio
En la página del paso 3 del Asistente para creación del laboratorio, puede habilitar o deshabilitar esta característica y también especificar cuánto tiempo debe esperar la máquina virtual para que el usuario se vuelva a conectar antes de cerrarse automáticamente. 

![Configuración al crear el laboratorio](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configuración después de crear el laboratorio
Puede configurar esta opción en la página **Configuración** tal y como se muestra en la siguiente imagen: 

![Configuración después de crear el laboratorio](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Panel para laboratorios de clase](use-dashboard.md)