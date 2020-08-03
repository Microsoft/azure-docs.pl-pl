---
author: baanders
description: Omówienie pliku dołączania w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407526"
---
>[!NOTE]
>Te operacje mają zostać wykonane przez użytkownika z rolą *właściciela* w ramach subskrypcji platformy Azure. Chociaż niektóre elementy można wykonać bez tego podwyższonego poziomu uprawnień, współpraca właściciela będzie wymagała całkowitego skonfigurowania użytecznego wystąpienia. Więcej informacji na ten temat znajduje się w sekcji [*wymagania wstępne: wymagane uprawnienia*](#prerequisites-permission-requirements) poniżej.

Pełna Konfiguracja nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji składa się z trzech części:
1. **Tworzenie wystąpienia**
2. **Konfigurowanie uprawnień dostępu użytkowników**: Użytkownicy platformy Azure muszą mieć rolę *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, aby móc zarządzać nim i danymi. Ten krok polega na tym, że właściciel w ramach subskrypcji platformy Azure przypisze tę rolę do osoby, która będzie zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Może to być samodzielne lub ktoś inny w organizacji.
3. **Konfigurowanie uprawnień dostępu dla aplikacji klienckich**: często należy napisać aplikację kliencką, która będzie używana do współpracy z wystąpieniem. Aby aplikacja kliencka mogła uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, należy skonfigurować *rejestrację aplikacji* w [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , która będzie używana przez aplikację kliencką do uwierzytelniania w wystąpieniu.

Aby można było to zrobić, będzie potrzebna subskrypcja platformy Azure. Możesz bezpłatnie skonfigurować jedno z [nich.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
