---
author: baanders
description: Omówienie pliku dołączania w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560785"
---
Pełna Konfiguracja nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji składa się z dwóch części:
1. **Tworzenie wystąpienia**
2. **Konfigurowanie uprawnień dostępu użytkowników**: Użytkownicy platformy Azure muszą mieć rolę *właściciela danych Digital bliźniaczych reprezentacji* na platformie Azure w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, która będzie mogła zarządzać tym i danymi. W tym kroku użytkownik jest przypisany do osoby, która będzie zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji przez właściciela/administratora subskrypcji platformy Azure. Może to być samodzielne lub ktoś inny w organizacji.
 
>[!NOTE]
>Te operacje mają zostać wykonane przez użytkownika z możliwością zarządzania zasobami i dostępem użytkowników w ramach subskrypcji platformy Azure. Mimo że niektóre kroki można wykonać przy niższych uprawnieniach, aby całkowicie skonfigurować możliwe do użycia wystąpienie, współpraca osób z tymi uprawnieniami będzie wymagana. Więcej informacji na ten temat znajduje się w sekcji [*wymagania wstępne: wymagane uprawnienia*](#prerequisites-permission-requirements) poniżej.