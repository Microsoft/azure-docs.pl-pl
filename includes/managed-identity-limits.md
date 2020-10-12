---
title: plik dołączany
description: plik dołączany
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971034"
---
- Każda tożsamość zarządzana liczy się do limitu przydziału obiektów w dzierżawie usługi Azure AD zgodnie z opisem w temacie [limity i ograniczenia usług Azure AD](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md).
-   Szybkość, z jaką można utworzyć tożsamości zarządzane, ma następujące ograniczenia:

    1. Za dzierżawę usługi Azure AD na region Azure: 200 operacji tworzenia na 20 sekund.
    2. Za subskrypcję platformy Azure dla regionu platformy Azure: 40 operacji tworzenia na 20 sekund.

- Podczas tworzenia tożsamości zarządzanych przypisanych przez użytkownika, obsługiwane są tylko znaki alfanumeryczne (0-9, a-z i A-Z) i łącznik (-). Aby przypisanie do maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych działało prawidłowo, nazwa jest ograniczona do 24 znaków.
