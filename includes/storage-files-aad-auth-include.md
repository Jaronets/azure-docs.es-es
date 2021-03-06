---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854579"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) admite la autenticación basada en identidades mediante el Bloque de mensajes del servidor (SMB) con [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Las máquinas virtuales Windows unidas a un dominio pueden acceder entonces a los recursos compartidos de archivos de Azure con las credenciales de [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md).

Puede administrar el nivel de acceso a un recurso compartido de Azure Files para una identidad como un usuario o grupo de Azure AD con el [control de acceso basado en rol (RBAC)](../articles/role-based-access-control/overview.md). Puede definir roles de RBAC personalizados que incluyan los conjuntos de permisos comunes utilizados para acceder a Azure Files. Al asignar un rol de RBAC personalizado a una identidad de Azure AD, a esta última se le concede acceso a un recurso compartido de archivos de Azure según esos permisos.

Azure Files también admite la conservación, herencia y aplicación de las [DACL de NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) en todos los archivos y directorios de un recurso compartido de archivos. Si copia datos desde un recurso compartido de archivos a Azure Files, o viceversa, puede especificar se mantengan las DACL de NTFS. De este modo, puede implementar escenarios de copia de seguridad mediante Azure Files, conservando las DACL de NTFS entre el recurso compartido de archivos local y el recurso compartido de archivos en la nube. 

> [!NOTE]
> - No se admite la autenticación de Azure AD DS para el acceso al Bloque de mensajes del servidor (SMB) en máquinas virtuales Linux. Solo se admiten máquinas virtuales Windows.
> - No se admite la autenticación de Azure AD DS para el acceso SMB en las máquinas virtuales unidas al dominio de Active Directory. Mientras tanto, considere la posibilidad de usar [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) para empezar a migrar los datos a Azure Files y para seguir aplicando el control de acceso mediante el uso de las credenciales de Active Directory desde las máquinas locales unidas al dominio de Active Directory. 
> - La autenticación de Azure AD DS para el acceso SMB solo está disponible para las cuentas de almacenamiento creadas después del 24 de septiembre de 2018.
> - No se admite la autenticación de Azure AD DS para el acceso SMB ni NTFS DACL persistentes en recursos compartidos de Azure Files administrados por Azure File Sync.
> - La autenticación de Azure AD DS no admite la autenticación en cuentas de máquina creadas en Azure AD DS.
