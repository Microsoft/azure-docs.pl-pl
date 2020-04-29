---
title: Zarządzanie alertami zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu funkcji Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 8e44ce594375deeac47f037515d96c57d15c8359
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80398400"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center

W tym temacie pokazano, jak wyświetlać i przetwarzać alerty otrzymane w celu ochrony Twoich zasobów. 

* Aby dowiedzieć się więcej o różnych typach alertów, zobacz [typy alertów zabezpieczeń](alerts-reference.md).
* Aby zapoznać się z omówieniem Security Center generowania alertów, zobacz [jak Azure Security Center wykrywać zagrożenia i reagować na](security-center-alerts-overview.md)nie.

> [!NOTE]
> Aby włączyć wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. Dostępna jest bezpłatna wersja próbna. W celu uaktualnienia wybierz pozycję warstwa cenowa w obszarze [zasady zabezpieczeń](tutorial-security-policy.md). Aby dowiedzieć się więcej, zobacz [cennik usługi Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.

> [!NOTE]
> Aby uzyskać więcej informacji na temat sposobu działania funkcji wykrywania Security Center, zobacz [jak Azure Security Center wykrywać zagrożenia i reagować na](security-center-alerts-overview.md#detect-threats)nie.

## <a name="manage-your-security-alerts"></a>Zarządzanie alertami zabezpieczeń

1. Na pulpicie nawigacyjnym Security Center zapoznaj się z kafelkiem **Ochrona przed zagrożeniami** , aby wyświetlić i zapoznać się z alertami.

    ![Kafelek Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Aby wyświetlić więcej szczegółów na temat alertów, kliknij kafelek.

   ![Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Aby odfiltrować wyświetlane alerty, kliknij przycisk **Filtruj**, a następnie w bloku **filtru** , który zostanie otwarty, wybierz opcje filtru, które chcesz zastosować. Lista jest aktualizowana zgodnie z wybranym filtrem. Filtrowanie może być bardzo przydatne. Możesz na przykład sprawdzić alerty zabezpieczeń, które wystąpiły w ciągu ostatnich 24 godzin, ponieważ badasz potencjalne naruszenie zabezpieczeń systemu.

    ![Filtrowanie alertów w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Odpowiadanie na alerty zabezpieczeń

1. Na liście **alerty zabezpieczeń** Kliknij alert zabezpieczeń. Pokazywane są zasoby i kroki, które należy wykonać w celu rozwiązania ataku.

    ![Odpowiadanie na alerty zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Po przejrzeniu informacji kliknij zaatakowany zasób.

    ![Sugestie dotyczące czynności, które należy wykonać, aby uzyskać informacje o alertach zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Sekcja **Informacje ogólne** może oferować wgląd w to, co wyzwolił alert zabezpieczeń. Są w nim wyświetlane informacje, takie jak zasób docelowy, źródłowy adres IP (jeśli ma zastosowanie), jeśli alert jest nadal aktywny i zalecenia dotyczące sposobu korygowania.  

    > [!NOTE]
    >W niektórych przypadkach źródłowy adres IP nie jest dostępny, a niektóre dzienniki zdarzeń zabezpieczeń systemu Windows nie zawierają adresu IP.

1. Czynności naprawcze sugerowane w usłudze Security Center różnią się w zależności od alertu zabezpieczeń. Obserwuj je dla każdego alertu. 

    W niektórych przypadkach w celu ograniczenia alertu zabezpieczeń może być konieczne użycie innych usług lub kontroli platformy Azure w celu zaimplementowania zalecanej korekty. 

## <a name="see-also"></a>Zobacz także

W tym dokumencie przedstawiono sposób konfigurowania zasad zabezpieczeń w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

- [Moduł Microsoft Learn dotyczący sposobu korzystania z funkcji alertów Azure Security Center do oglądania i reagowania na zagrożenia](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
* [Alerty zabezpieczeń w Azure Security Center](security-center-alerts-overview.md).
* [Obsługa zdarzeń związanych z zabezpieczeniami](security-center-incident.md)