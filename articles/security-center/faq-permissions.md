---
title: Azure Security Center często zadawane pytania — pytania dotyczące uprawnień
description: Ten często zadawane pytania zawierają odpowiedzi na pytania dotyczące uprawnień w Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 89b3233966ac53d6c384ca6bd5ac30322fbb0b02
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106597"
---
# <a name="permissions"></a>Uprawnienia

## <a name="how-do-permissions-work-in-azure-security-center"></a>Jak uprawnienia działają w Azure Security Center?

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Security Center ocenia konfigurację zasobów w celu zidentyfikowania problemów i luk w zabezpieczeniach. W Security Center widoczne są tylko informacje związane z zasobem, gdy przypiszesz rolę właściciela, współautora lub czytelnika dla subskrypcji lub grupy zasobów, do której należy zasób.

Zobacz [uprawnienia w Azure Security Center](security-center-permissions.md) , aby dowiedzieć się więcej o rolach i dozwolonych akcjach w programie Security Center.



## <a name="who-can-modify-a-security-policy"></a>Kto może zmodyfikować zasady zabezpieczeń?

Aby zmodyfikować zasady zabezpieczeń, musisz być administratorem zabezpieczeń lub właścicielem subskrypcji lub współautorem.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md).