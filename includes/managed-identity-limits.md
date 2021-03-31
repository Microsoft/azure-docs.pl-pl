---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 6e5885e076222cd23ba127f3be41c1218f327ca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92379808"
---
- Każda tożsamość zarządzana liczy się do limitu przydziału obiektów w dzierżawie usługi Azure AD zgodnie z opisem w temacie [limity i ograniczenia usług Azure AD](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md).
-   Szybkość, z jaką można utworzyć tożsamości zarządzane, ma następujące ograniczenia:

    1. Za dzierżawę usługi Azure AD na region Azure: 200 operacji tworzenia na 20 sekund.
    2. Za subskrypcję platformy Azure dla regionu platformy Azure: 40 operacji tworzenia na 20 sekund.

- Podczas tworzenia tożsamości zarządzanych przypisanych przez użytkownika, obsługiwane są tylko znaki alfanumeryczne (0-9, a-z i A-Z) i łącznik (-). Aby przypisanie do maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych działało prawidłowo, nazwa jest ograniczona do 24 znaków.
