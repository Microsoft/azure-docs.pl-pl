---
author: baanders
description: Omówienie pliku dołączania w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205499"
---
>[!NOTE]
>Te operacje mają zostać wykonane przez użytkownika z możliwością zarządzania zasobami i dostępem użytkowników w ramach subskrypcji platformy Azure. Mimo że niektóre kroki można wykonać przy niższych uprawnieniach, aby całkowicie skonfigurować możliwe do użycia wystąpienie, współpraca osób z tymi uprawnieniami będzie wymagana. Więcej informacji na ten temat znajduje się w sekcji [*wymagania wstępne: wymagane uprawnienia*](#prerequisites-permission-requirements) poniżej.

Pełna Konfiguracja nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji składa się z dwóch części:
1. **Tworzenie wystąpienia**
2. **Konfigurowanie uprawnień dostępu użytkowników**: Użytkownicy platformy Azure muszą mieć rolę *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, aby móc zarządzać nim i danymi. W tym kroku użytkownik jest przypisany do osoby, która będzie zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji przez właściciela/administratora subskrypcji platformy Azure. Może to być samodzielne lub ktoś inny w organizacji.