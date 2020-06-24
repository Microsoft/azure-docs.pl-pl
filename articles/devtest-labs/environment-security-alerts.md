---
title: Alerty zabezpieczeń dla środowisk w Azure DevTest Labs
description: W tym artykule opisano sposób wyświetlania alertów zabezpieczeń dla środowiska w programie DevTest Labs i podejmowania odpowiednich działań.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898402"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alerty zabezpieczeń dla środowisk w Azure DevTest Labs
Jako użytkownik laboratorium możesz teraz wyświetlać alerty Azure Security Center dla środowisk laboratoryjnych. Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku. [Dowiedz się więcej o alertach zabezpieczeń w Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Wymagania wstępne
Obecnie można wyświetlać alerty zabezpieczeń tylko dla środowisk typu platforma jako usługa (PaaS) wdrożonych w środowisku laboratoryjnym. Aby przetestować lub użyć tej funkcji, [Wdróż środowisko w środowisku laboratoryjnym](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Wyświetlanie alertów zabezpieczeń dla środowiska

1. Na stronie głównej laboratorium wybierz pozycję **alerty zabezpieczeń** w menu po lewej stronie. Powinna zostać wyświetlona liczba alertów zabezpieczeń (wysoka, średnia i niska). Dowiedz się więcej na [temat sposobu klasyfikowania alertów](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Alerty zabezpieczeń — omówienie](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Kliknij prawym przyciskiem myszy trzy kropki (...) w ostatniej kolumnie, a następnie wybierz pozycję **Wyświetl alerty zabezpieczeń**. 

    ![Wyświetlanie alertów zabezpieczeń](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Zobaczysz więcej szczegółowych informacji na temat alertów i zaleceń usługi Advisor. Dowiedz się więcej na temat [zarządzania alertami zabezpieczeń i reagowania na nie w programie Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Wyświetlanie alertów zabezpieczeń](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o środowiskach, zobacz następujące artykuły:

- [Tworzenie środowisk z obsługą wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych](devtest-lab-configure-use-public-environments.md)
