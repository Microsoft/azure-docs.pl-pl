---
title: Szybkie rozwiązywanie problemów z zaleceniami usługi Advisor
description: Wykonywanie korekty zbiorczej przy użyciu funkcji szybkiej naprawy w usłudze Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: a9c86a7ae510d9657f64c71db2aa8c4e3e558f52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968597"
---
# <a name="quick-fix-remediation-for-advisor"></a>Szybkie korygowanie poprawek dla klasyfikatora
**Szybka poprawka** zapewnia szybszy i łatwiejszy sposób korygowania zaleceń dotyczących wielu zasobów. Zapewnia możliwość korygowania zbiorczego zasobów i pomaga zoptymalizować subskrypcje szybciej dzięki korygowaniu zasobów.
Funkcja jest dostępna tylko w przypadku niektórych zaleceń, za pośrednictwem Azure Portal.


## <a name="steps-to-use-quick-fix"></a>Procedura używania "szybkiej naprawy"

1. Na liście zaleceń, które mają etykietę **szybka naprawa** , kliknij zalecenie.

   :::image type="content" source="./media/quick-fix-1.png" alt-text="{Zrzut ekranu przedstawiający Azure Advisor pokazujący szybkie poprawki etykiet w zaleceniach.}":::
   
   *Ceny na obrazie są tylko na przykład*

2. Na stronie Szczegóły zalecenia zobaczysz listę zasobów, dla których masz to zalecenie. Wybierz wszystkie zasoby, które chcesz skorygować dla zalecenia.

   :::image type="content" source="./media/quick-fix-2.png" alt-text="{Zrzut ekranu przedstawiający Azure Advisor pokazujący szybkie poprawki etykiet w zaleceniach.}":::
   
   *Ceny na obrazie są tylko na przykład*

3. Po wybraniu zasobów kliknij przycisk **szybka naprawa** , aby przeprowadzić zbiorcze korygowanie.

   > [!NOTE]
   > Niektóre z wymienionych zasobów mogą być wyłączone, ponieważ nie masz odpowiednich uprawnień, aby je modyfikować.
   
   > [!NOTE]
   > W przypadku innych implikacji, oprócz korzyści wymienionych w usłudze Advisor, użytkownik zostanie powiadomiony o tym, aby ułatwić podejmowanie świadomych decyzji zaradczych.
   
4. Otrzymasz powiadomienie o ukończeniu korygowania. Jeśli istnieją zasoby, które nie są korygowane i zasoby w wybranym trybie w widoku listy zasobów, zostanie wyświetlony komunikat o błędzie.  


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
* [Interfejs API REST usługi Advisor](/rest/api/advisor/)
