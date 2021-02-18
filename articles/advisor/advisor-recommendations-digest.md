---
title: Podsumowanie rekomendacji dla Azure Advisor
description: Pobierz okresowe podsumowanie Twoich aktywnych zaleceń
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 1c82abf3c8bea07941f860b94fcfe92954fb7641
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579822"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Skonfiguruj okresowe Podsumowanie dla zaleceń

Skróty doradcze **rekomendacji** zapewniają łatwy i aktywny sposób, aby zachować swoje aktywne zalecenia w różnych kategoriach. Ta funkcja umożliwia skonfigurowanie okresowych powiadomień w celu podsumowania wszystkich aktywnych zaleceń w różnych kategoriach. Możesz wybrać żądany kanał dla powiadomień, takich jak poczta e-mail, wiadomości SMS lub inne, przy użyciu grup akcji. W tym artykule opisano sposób konfigurowania **podsumowania rekomendacji** dla zaleceń usługi Advisor.


## <a name="setting-up-your-recommendation-digest"></a>Konfigurowanie podsumowania rekomendacji 

Środowisko tworzenia podsumowania **rekomendacji** pomaga skonfigurować podsumowanie. Dla konfiguracji można wybrać następujące parametry:
1. Kategoria: mamy takie kategorie, jak koszt, wysoka dostępność, wydajność i doskonałości operacyjne. Ta funkcja nie jest jeszcze dostępna w przypadku zaleceń dotyczących zabezpieczeń.
2. Częstotliwość Digest: częstotliwość dla powiadomień podsumowujących może być co tydzień, co dwa tygodnie i co miesiąc.
3. Grupa akcji: możesz wybrać istniejącą grupę akcji lub utworzyć nową grupę akcji. Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/alerts/action-groups.md).
4. Język dla skrótu
5. Nazwa skrócona zalecenia: można użyć ciągu przyjaznego dla użytkownika, aby lepiej śledzić i monitorować skróty.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Procedura tworzenia podsumowania rekomendacji w Azure Portal

Poniżej przedstawiono procedurę tworzenia **podsumowania rekomendacji:**
* **Krok 1:** W Azure Portal przejdź do usługi **Advisor** i w obszarze **monitorowanie** wybierz pozycję **zalecenie skrócone** 

   ![Punkt wejścia podsumowania zalecenia](./media/digest-0.png)

* **Krok 2:** Wybierz pozycję **nowy skrót rekomendacji** na górnym pasku poniżej:

   ![Utwórz skrót rekomendacji](./media/digest-5.png)

* **Krok 3.** W sekcji **zakres** wybierz **subskrypcję** dla swojego skrótu

   ![Podaj dane wejściowe podsumowania rekomendacji](./media/digest-1.png)

* **Krok 4.** W sekcji **warunek** Wybierz konfiguracje, takie jak **Kategoria**, **częstotliwość** i **Język** .

   ![Podaj warunki wejściowe podsumowania rekomendacji](./media/digest-2.png)

* **Krok 5.** W sekcji **Grupa akcji** wybierz **grupę akcji** dla tego podsumowania. Więcej informacji można znaleźć tutaj — [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/alerts/action-groups.md)

   ![Podaj grupę akcji wejścia podsumowania rekomendacji](./media/digest-3.png)

* **Krok 6.** W tej ostatniej sekcji dla **szczegółów podsumowania** można przypisać nazwę i stan do podsumowania rekomendacji. Naciśnij pozycję **Utwórz skrót rekomendacji** , aby zakończyć konfigurację.
   ![Ukończ tworzenie podsumowania rekomendacji](./media/digest-4.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
* [Interfejs API REST usługi Advisor](/rest/api/advisor/)
