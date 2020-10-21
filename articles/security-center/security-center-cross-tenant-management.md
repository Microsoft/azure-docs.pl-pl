---
title: Zarządzanie między dzierżawcami w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak skonfigurować zarządzanie pośrednictwem wielu dzierżawców w celu zarządzania stanami w ramach usług w Security Center z użyciem delegowanego zarządzania zasobami platformy Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 68338341d47b8fa15d0bd88ea23e951210105fc4
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340788"
---
# <a name="cross-tenant-management-in-security-center"></a>Zarządzanie między dzierżawcami w Security Center

Zarządzanie między dzierżawcami pozwala wyświetlać stan zabezpieczeń wielu dzierżawców w usłudze Security Center i zarządzać nimi przy użyciu [delegowanego zarządzania zasobami platformy Azure](../lighthouse/concepts/azure-delegated-resource-management.md) . Wydajnie Zarządzaj wieloma dzierżawcami, z poziomu jednego widoku, bez konieczności logowania się do katalogu każdej dzierżawy.

- Dostawcy usług mogą zarządzać stanem zabezpieczeń zasobów dla wielu klientów w ramach własnej dzierżawy.

- Zespoły ds. zabezpieczeń organizacji z wieloma dzierżawcami mogą wyświetlać stan zabezpieczeń i zarządzać nimi z jednej lokalizacji.

## <a name="set-up-cross-tenant-management"></a>Konfigurowanie zarządzania wieloma dzierżawami

Skonfiguruj zarządzanie pośrednictwem wielu dzierżawców, delegując dostęp do zasobów zarządzanych dzierżawców do własnej dzierżawy przy użyciu funkcji [zarządzania zasobami delegowanych przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> Zarządzanie zasobami delegowanymi przez platformę Azure to jeden z najważniejszych składników usługi Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Jak działa zarządzanie między dzierżawcami w Security Center

Możesz przeglądać subskrypcje wielu dzierżawców i zarządzać nimi w taki sam sposób, w jaki zarządzasz wieloma subskrypcjami w ramach jednej dzierżawy.

Na górnym pasku menu kliknij ikonę filtru, a następnie wybierz subskrypcje, z katalogu każdego dzierżawy, chcesz wyświetlić.

  ![Filtrowanie dzierżawców](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Widoki i akcje są zasadniczo takie same. Oto kilka przykładów:

- **Zarządzanie zasadami zabezpieczeń**: w jednym widoku Zarządzaj Stanami zabezpieczeń wielu zasobów przy użyciu [zasad](tutorial-security-policy.md), podejmuj działania z zaleceniami dotyczącymi zabezpieczeń, a następnie Zbieraj dane związane z zabezpieczeniami i zarządzaj nimi.
- **Poprawa bezpiecznego oceny i zgodności stan**: widoczność między dzierżawcami umożliwia wyświetlenie ogólnych stan związanych z bezpieczeństwem wszystkich dzierżawców oraz, gdzie i jak najlepiej poprawić [bezpieczną ocenę](secure-score-security-controls.md) i [zgodność stan](security-center-compliance-dashboard.md) dla każdego z nich.
- **Skoryguj zalecenia**: Monitoruj i Koryguj [zalecenia](security-center-recommendations.md) dotyczące wielu zasobów z różnych dzierżawców jednocześnie. Następnie można natychmiast zaradzić sobie z lukami w zabezpieczeniach, które stwarzają największe ryzyko dla wszystkich dzierżawców.
- **Zarządzanie alertami**: wykrywanie [alertów](security-center-alerts-overview.md) w różnych dzierżawach. Wykonaj działania dotyczące zasobów, które są niezgodne z [krokami korygowania](security-center-managing-and-responding-alerts.md)akcji.

- **Zarządzaj zaawansowanymi funkcjami obrony w chmurze i nie tylko**: Zarządzaj różnymi usługami ochrony przed zagrożeniami, takimi jak [dostęp do maszyn wirtualnych just-in-Time (JIT)](security-center-just-in-time.md), [adaptacyjne Zabezpieczanie sieci](security-center-adaptive-network-hardening.md), [adaptacyjne kontrolki aplikacji](security-center-adaptive-application.md)i nie tylko.
 
## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, jak zarządzanie przez wiele dzierżawców działa w Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Wzmocnij stan zabezpieczeń dzięki Azure Security Center](security-center-monitoring.md) — Dowiedz się, jak monitorować kondycję zasobów platformy Azure.
* [Azure Security Center często zadawane](faq-general.md) pytania — Znajdź często zadawane pytania dotyczące korzystania z usługi.
* [Dowiedz się więcej o usłudze Azure Lighthouse w scenariuszach dla przedsiębiorstw](../lighthouse/concepts/enterprise.md) — Odkryj, jak usługa Azure Lighthouse może uprościć zarządzanie różnymi dzierżawcami w przedsiębiorstwie, które korzystają z wielu dzierżawców usługi Azure AD.