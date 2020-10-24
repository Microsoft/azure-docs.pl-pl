---
author: baanders
description: Omówienie pliku dołączania w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: bf442da43fd7945bf69fbb889ef0c517b8832809
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478878"
---
>[!NOTE]
>Te operacje mają zostać wykonane przez użytkownika z możliwością zarządzania zasobami i dostępem użytkowników w ramach subskrypcji platformy Azure. Mimo że niektóre kroki można wykonać przy niższych uprawnieniach, aby całkowicie skonfigurować możliwe do użycia wystąpienie, współpraca osób z tymi uprawnieniami będzie wymagana. Więcej informacji na ten temat znajduje się w sekcji [*wymagania wstępne: wymagane uprawnienia*](#prerequisites-permission-requirements) poniżej.

Pełna Konfiguracja nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji składa się z dwóch części:
1. **Tworzenie wystąpienia**
2. **Konfigurowanie uprawnień dostępu użytkowników**: Użytkownicy platformy Azure muszą mieć rolę *właściciela danych Digital bliźniaczych reprezentacji* na platformie Azure w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, która będzie mogła zarządzać tym i danymi. W tym kroku użytkownik jest przypisany do osoby, która będzie zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji przez właściciela/administratora subskrypcji platformy Azure. Może to być samodzielne lub ktoś inny w organizacji.

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]