---
title: Zarządzanie zdarzeniami zabezpieczeń w Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia używanie Azure Security Center do zarządzania zdarzeniami zabezpieczeń.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415662"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Zarządzanie zdarzeniami zabezpieczeń w Azure Security Center

Klasyfikacja i badanie alertów zabezpieczeń może być czasochłonne dla nawet najbardziej wykwalifikowanych analityków zabezpieczeń, a dla wielu z nich trudno wiedzieć, gdzie zacząć. Używając [analizy](security-center-detection-capabilities.md) w celu powiązania informacji z różnych [alertów zabezpieczeń](security-center-managing-and-responding-alerts.md), usługa Security Center może dostarczyć pojedynczego widoku kampanii ataku i wszystkich powiązanych alertów — dzięki temu można szybko dowiedzieć się, jakie akcje zostały podjęte przez osobę atakującą i na jakie zasoby miały wpływ.

W tym temacie wyjaśniono informacje o zdarzeniach w Security Center i sposobach używania korygowania alertów.

## <a name="what-is-a-security-incident"></a>Co to jest zdarzenie naruszenia zabezpieczeń?

W usłudze Security Center zdarzenie zabezpieczeń to agregacja wszystkich alertów dotyczących zasobu, które są zgodne ze wzorcami ataku cybernetycznego typu [kill chain](alerts-reference.md#intentions). Zdarzenia są wyświetlane na liście [alerty zabezpieczeń](security-center-managing-and-responding-alerts.md) . Kliknij zdarzenie, aby wyświetlić powiązane alerty, co pozwala uzyskać więcej informacji na temat każdego wystąpienia.

## <a name="managing-security-incidents"></a>Zarządzanie zdarzeniami naruszenia zabezpieczeń

1. Na pulpicie nawigacyjnym Security Center kliknij kafelek **alerty zabezpieczeń** . Zdarzenia i alerty są wymienione na liście. Należy zwrócić uwagę, że ikona opisu zdarzenia naruszenia zabezpieczeń różni się od ikon innych alertów.

    ![Wyświetl zdarzenia dotyczące zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Aby wyświetlić szczegóły, kliknij zdarzenie. Blok **wykryte zdarzenia zabezpieczeń** wyświetla dalsze szczegóły. Sekcja **Informacje ogólne** może oferować wgląd w to, co wyzwolił alert zabezpieczeń. Są w nim wyświetlane informacje, takie jak zasób docelowy, źródłowy adres IP (jeśli ma zastosowanie), jeśli alert jest nadal aktywny i zalecenia dotyczące sposobu korygowania.  

    ![Reagowanie na zdarzenia związane z bezpieczeństwem w Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Aby uzyskać więcej informacji na temat każdego alertu, kliknij alert. Czynności naprawcze sugerowane w Centrum zabezpieczeń różnią się w zależności od alertu zabezpieczeń.

   > [!NOTE]
   > Ten sam alert może istnieć jako część zdarzenia, a także być widoczny jako alert autonomiczny.

    ![Szczegóły alertu](./media/security-center-incident/security-center-incident-alert.png)

1. Wykonaj czynności zaradcze podane dla każdego alertu.


## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób użycia funkcji zdarzeń naruszenia zabezpieczeń w usłudze Security Center. Aby uzyskać powiązane informacje, zobacz następujące tematy:

* [Ochrona przed zagrożeniami w usłudze Azure Security Center](threat-protection.md)
* [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](security-center-alerts-overview.md)
* [Zarządzanie alertami zabezpieczeń](security-center-managing-and-responding-alerts.md)