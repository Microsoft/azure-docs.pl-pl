---
title: Wizualizacje do analizy zmian aplikacji — Azure Monitor
description: Dowiedz się, jak używać wizualizacji w analizie zmian aplikacji w Azure Monitor.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 643645eb3b361cd68def1177ba7e8af89f1963bc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521091"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Wizualizacje do analizy zmian aplikacji (wersja zapoznawcza)

## <a name="standalone-ui"></a>Autonomiczny interfejs użytkownika

W Azure Monitor istnieje autonomiczne okienko do analizy zmian, aby wyświetlić wszystkie zmiany z wglądem w zależności aplikacji i zasoby.

Wyszukaj wartość Analiza zmian na pasku wyszukiwania na Azure Portal, aby uruchomić środowisko.

![Zrzut ekranu przeszukiwania analizy zmian w Azure Portal](./media/change-analysis/search-change-analysis.png)

Wszystkie zasoby w ramach wybranej subskrypcji są wyświetlane ze zmianami z ostatnich 24 godzin. Aby zoptymalizować wydajność ładowania strony, usługa wyświetla 10 zasobów jednocześnie. Wybierz następną stronę, aby wyświetlić więcej zasobów. Pracujemy nad usunięciem tego ograniczenia.

![Zrzut ekranu przedstawiający blok analizy zmian w Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Kliknij zasób, aby wyświetlić wszystkie jego zmiany. W razie potrzeby przechodzenie do szczegółów w celu wyświetlenia szczegółów i szczegółowych informacji o zmianach w formacie JSON.

![Zrzut ekranu przedstawiający szczegóły zmiany](./media/change-analysis/change-details.png)

Aby dowolna opinia, użyj przycisku Wyślij opinię lub wiadomości e-mail changeanalysisteam@microsoft.com .

![Zrzut ekranu przycisku opinii na karcie zmiana analizy](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Obsługa wielu subskrypcji

Interfejs użytkownika obsługuje Wybieranie wielu subskrypcji w celu wyświetlenia zmian zasobów. Użyj filtru subskrypcji:

![Zrzut ekranu filtru subskrypcji, który obsługuje Wybieranie wielu subskrypcji](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Aplikacja sieci Web diagnozowanie i rozwiązywanie problemów

W Azure Monitor analizy zmian są również wbudowane w funkcję **diagnozowania i rozwiązywania problemów** . Uzyskaj dostęp do tego środowiska na stronie **Przegląd** aplikacji App Service.

![Zrzut ekranu przedstawiający przycisk "przegląd" i przycisk "diagnozowanie i rozwiązywanie problemów"](./media/change-analysis/change-analysis.png)

## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analiza zmian aplikacji w narzędziu diagnozowanie i rozwiązywanie problemów

Analiza zmian aplikacji to autonomiczny detektor w aplikacji sieci Web diagnozowanie i rozwiązywanie problemów. Jest on również agregowany w przypadku **awarii aplikacji** i **detektorów w aplikacji sieci Web**. Po wprowadzeniu narzędzia do diagnozowania i rozwiązywania problemów dostawca zasobów **Microsoft. ChangeAnalysis** zostanie automatycznie zarejestrowany. Postępuj zgodnie z tymi instrukcjami, aby włączyć śledzenie zmian w aplikacji internetowej w trybie gościnnym.

1. Wybierz pozycję **dostępność i wydajność**.

    ![Zrzut ekranu przedstawiający opcje rozwiązywania problemów z "dostępnością i wydajnością"](./media/change-analysis/availability-and-performance.png)

2. Wybierz pozycję **zmiany aplikacji**. Ta funkcja jest również dostępna w obszarze **awarie aplikacji**.

   ![Zrzut ekranu przedstawiający przycisk "awarie aplikacji"](./media/change-analysis/application-changes.png)

3. Łącze prowadzi do interfejsu użytkownika analizy zmian aplikacji w zakresie aplikacji sieci Web. Jeśli śledzenie zmian w aplikacji internetowej w trybie gościa nie jest włączone, postępuj zgodnie z transparentem, aby uzyskać zmiany ustawień plików i aplikacji.

   ![Zrzut ekranu opcji "awarie aplikacji"](./media/change-analysis/enable-changeanalysis.png)

4. Włącz **analizę zmian** i wybierz pozycję **Zapisz**. Narzędzie wyświetla wszystkie aplikacje sieci Web w ramach planu App Service. Możesz użyć przełącznika poziomu planu, aby włączyć analizę zmian dla wszystkich aplikacji sieci Web w ramach planu.

    ![Zrzut ekranu przedstawiający interfejs użytkownika "Włącz analizę zmian"](./media/change-analysis/change-analysis-on.png)

5. Dane dotyczące zmiany są również dostępne w obszarze Wybierz **aplikacje sieci Web** i wykrywacze **awarii aplikacji** . Zobaczysz Wykres podsumowujący typ zmian w czasie wraz ze szczegółami dotyczącymi tych zmian. Domyślnie zmiany w ciągu ostatnich 24 godzin są wyświetlane, aby ułatwić natychmiastowe Rozwiązywanie problemów.

     ![Zrzut ekranu przedstawiający widok różnic między zmianami](./media/change-analysis/change-view.png)

## <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnozowanie i rozwiązywanie problemów z maszyną wirtualną

Przejdź do narzędzia diagnozowanie i rozwiązywanie problemów dotyczących maszyny wirtualnej.  Przejdź do **Narzędzia do rozwiązywania problemów**, przejdź do strony i wybierz pozycję **Analizuj ostatnie zmiany** , aby wyświetlić zmiany na maszynie wirtualnej.

![Zrzut ekranu maszyny wirtualnej diagnozowanie i rozwiązywanie problemów](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analizator zmian w narzędziach do rozwiązywania problemów](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Historia zmian dziennika aktywności

Funkcja [Wyświetl historię zmian](../platform/activity-log.md#view-change-history) w dzienniku aktywności wywołuje zaplecze usługi analizy zmian aplikacji w celu uzyskania zmian skojarzonych z operacją. **Historia zmian** używana do bezpośredniego wywoływania [grafu zasobów platformy Azure](../../governance/resource-graph/overview.md) , ale zastąpi zaplecze w celu wywołania analizy zmian aplikacji, w związku z czym zwrócone zmiany będą obejmowały zmiany poziomu zasobów z [wykresu zasobów platformy Azure](../../governance/resource-graph/overview.md), właściwości zasobów z [Azure Resource Manager](../../azure-resource-manager/management/overview.md)i zmiany w gościu z usług PaaS Services, takich jak App Services aplikacji sieci Web. Aby usługa analizy zmian aplikacji mogła skanować w poszukiwaniu zmian w subskrypcjach użytkowników, należy zarejestrować dostawcę zasobów. Po pierwszym wprowadzeniu karty **historia zmian** narzędzie automatycznie rozpocznie rejestrowanie dostawcy zasobów **Microsoft. ChangeAnalysis** . Po zarejestrowaniu zmiany z **grafu zasobów platformy Azure** będą dostępne natychmiast i będzie obejmować ostatnie 14 dni. Zmiany z innych źródeł będą dostępne po upływie około 4 godzin od momentu dołączenia subskrypcji.

![Integracja historii zmian dziennika aktywności](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>Integracja usługi VM Insights

Użytkownicy korzystający z usługi [VM Insights](../insights/vminsights-overview.md) mogą zobaczyć, co zmieniło się na swoich maszynach wirtualnych, które mogą spowodować, że wszystkie skoki na wykresie metryk, takie jak procesor CPU lub pamięć. Zmiany danych są zintegrowane na pasku nawigacyjnym po stronie usługi VM Insights. Użytkownik może zobaczyć, czy wprowadzono jakiekolwiek zmiany w maszynie wirtualnej i wybrać opcję **Zbadaj zmiany** , aby wyświetlić szczegóły zmian w oknie autonomiczny interfejs użytkownika analizy zmian aplikacji.

[![Integracja usługi VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy związane z analizą zmian](change-analysis-troubleshoot.md)
