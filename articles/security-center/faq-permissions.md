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
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87824617"
---
# <a name="permissions"></a>Uprawnienia

## <a name="how-do-permissions-work-in-azure-security-center"></a>Jak uprawnienia działają w Azure Security Center?

Azure Security Center używa [kontroli dostępu opartej na rolach platformy Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), która udostępnia [wbudowane role](../role-based-access-control/built-in-roles.md) , które można przypisać do użytkowników, grup i usług na platformie Azure.

Security Center ocenia konfigurację zasobów w celu zidentyfikowania problemów i luk w zabezpieczeniach. W Security Center widoczne są tylko informacje związane z zasobem, gdy przypiszesz rolę właściciela, współautora lub czytelnika dla subskrypcji lub grupy zasobów, do której należy zasób.

Zobacz [uprawnienia w Azure Security Center](security-center-permissions.md) , aby dowiedzieć się więcej o rolach i dozwolonych akcjach w programie Security Center.



## <a name="who-can-modify-a-security-policy"></a>Kto może zmodyfikować zasady zabezpieczeń?

Aby zmodyfikować zasady zabezpieczeń, musisz być administratorem zabezpieczeń lub właścicielem subskrypcji lub współautorem.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md).