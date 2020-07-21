---
title: Szybkie rozwiązywanie problemów z zaleceniami usługi Advisor
description: Wykonywanie korekty zbiorczej przy użyciu funkcji szybkiej naprawy w usłudze Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: ebd993733c1aca9cd4f7d92f0a75e22d68a87ffe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518066"
---
# <a name="quick-fix-remediation-for-advisor"></a>Szybkie korygowanie poprawek dla klasyfikatora
**Szybka poprawka** zapewnia szybszy i łatwiejszy sposób korygowania zaleceń dotyczących wielu zasobów. Zapewnia możliwość korygowania zbiorczego zasobów i pomaga zoptymalizować subskrypcje szybciej dzięki korygowaniu zasobów.
Funkcja jest dostępna tylko w przypadku niektórych zaleceń, za pośrednictwem Azure Portal.


## <a name="steps-to-use-quick-fix"></a>Procedura używania "szybkiej naprawy"

1. Na liście zaleceń, które mają etykietę **szybka naprawa** , kliknij zalecenie.

   ![Szybka naprawa usługi Advisor](./media/quick-fix-1.png)
   
   *Ceny na obrazie są tylko na przykład*

2. Na stronie Szczegóły zalecenia zobaczysz listę zasobów, dla których masz to zalecenie. Wybierz wszystkie zasoby, które chcesz skorygować dla zalecenia.

   ![Szybka naprawa usługi Advisor](./media/quick-fix-2.png)
   
   *Ceny na obrazie są tylko na przykład*

3. Po wybraniu zasobów kliknij przycisk **szybka naprawa** , aby przeprowadzić zbiorcze korygowanie.

   > [!NOTE]
   > Niektóre z wymienionych zasobów mogą być wyłączone, ponieważ nie masz odpowiednich uprawnień, aby je modyfikować.
   
   > [!NOTE]
   > W przypadku innych implikacji, oprócz korzyści wymienionych w usłudze Advisor, użytkownik zostanie powiadomiony o tym, aby ułatwić podejmowanie świadomych decyzji zaradczych.
   
4. Otrzymasz powiadomienie o ukończeniu korygowania. Jeśli istnieją zasoby, które nie są korygowane i zasoby w wybranym trybie w widoku listy zasobów, zostanie wyświetlony komunikat o błędzie.  


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
* [Interfejs API REST usługi Advisor](/rest/api/advisor/)
