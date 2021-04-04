---
title: Inspekcja i raportowanie użytkownika współpracy B2B — Azure AD
description: Właściwości użytkownika gościa można konfigurować w Azure Active Directory współpracy B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a805f643e5f93270fe4721f527073baa9a1e4c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87909694"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Inspekcja i raportowanie użytkownika współpracy B2B
Użytkownicy-Goście mają możliwość inspekcji, podobnie jak w przypadku użytkowników będących członkami. 

## <a name="access-reviews"></a>Przeglądy dostępu
Za pomocą przeglądów dostępu można okresowo weryfikować, czy użytkownicy-Goście nadal potrzebują dostępu do zasobów. Funkcja **przeglądy dostępu** jest dostępna w **Azure Active Directory** w obszarze   >  **przeglądy dostępu do** tożsamości zewnętrznych. Możesz również wyszukać "przeglądy dostępu" ze **wszystkich usług** w Azure Portal. Aby dowiedzieć się, jak korzystać z przeglądów dostępu, zobacz [Zarządzanie dostępem gościa za pomocą przeglądów dostępu w usłudze Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji usługi Azure AD zawierają rekordy działań systemowych i użytkowników, w tym działania zainicjowane przez użytkowników-Gości. Aby uzyskać dostęp do dzienników inspekcji, w **Azure Active Directory** w obszarze **monitorowanie** wybierz pozycję **dzienniki inspekcji**. Oto przykład zaproszenia i historii umorzenia zaproszonego użytkownika oogle:

![Zrzut ekranu przedstawiający przykładowe dane wyjściowe dziennika inspekcji](./media/auditing-and-reporting/audit-log.png)

Aby uzyskać szczegółowe informacje, możesz szczegółowe do każdego z tych zdarzeń. Na przykład przyjrzyjmy się szczegółom akceptacji.

![Zrzut ekranu przedstawiający przykładowe dane wyjściowe dotyczące działania](./media/auditing-and-reporting/activity-details.png)

Możesz również wyeksportować te dzienniki z usługi Azure AD i użyć wybranego narzędzia do raportowania, aby uzyskać raporty niestandardowe.

### <a name="next-steps"></a>Następne kroki

- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)

