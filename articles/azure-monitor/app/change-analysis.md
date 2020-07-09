---
title: Użyj analizy zmian aplikacji w Azure Monitor, aby znaleźć problemy z aplikacją sieci Web | Microsoft Docs
description: Korzystając z analizy zmian aplikacji w Azure Monitor, można rozwiązywać problemy z aplikacjami w witrynach na żywo w programie Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: c287a2315f2b2319a6873ce84ee0e4e48bec8444
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82836817"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Korzystanie z analizy zmian aplikacji (wersja zapoznawcza) w Azure Monitor

Gdy wystąpi problem lub awaria witryny na żywo, szybkie określenie głównej przyczyny ma krytyczne znaczenie. Standardowe rozwiązania do monitorowania mogą powiadamiać o problemie. Mogą nawet wskazywać, który składnik kończy się niepowodzeniem. Jednak ten alert nie zawsze natychmiast wyjaśni przyczynę niepowodzenia. Wiadomo, że witryna działała pięć minut temu, a teraz jest ona uszkodzona. Co zmieniło się w ciągu ostatnich pięciu minut? Jest to pytanie, że analiza zmian aplikacji została zaprojektowana pod kątem odpowiedzi w Azure Monitor.

W oparciu o możliwości [grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)Analiza zmian zapewnia wgląd w zmiany aplikacji platformy Azure w celu zwiększenia zauważalności i zmniejszenia MTTR (średni czas naprawy).

> [!IMPORTANT]
> Analiza zmian jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Ta wersja nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="overview"></a>Omówienie

Analiza zmian wykrywa różne typy zmian z warstwy infrastruktury we wszystkich sposobach wdrażania aplikacji. Jest to dostawca zasobów platformy Azure na poziomie subskrypcji, który sprawdza zmiany zasobów w subskrypcji. Analiza zmian udostępnia dane dla różnych narzędzi diagnostycznych, które pomagają użytkownikom zrozumieć, jakie zmiany mogą powodować problemy.

Na poniższym diagramie przedstawiono architekturę analizy zmian:

![Diagram architektury sposobu, w jaki Analiza zmian pobiera zmiany danych i udostępnia je narzędziom klienckim](./media/change-analysis/overview.png)

## <a name="data-sources"></a>Źródła danych

Zapytania analizy zmian aplikacji dotyczące Azure Resource Manager śledzonych właściwości, konfiguracji serwera proxy i zmian aplikacji sieci Web w ramach gościa. Ponadto usługa śledzi zmiany zależności zasobów, aby zdiagnozować i monitorować kompleksową aplikację.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager zmian właściwości śledzonych

Przy użyciu [grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)Analiza zmian zawiera historyczny rekord, w jaki sposób zasoby platformy Azure obsługujące aplikację zostały zmienione w miarę upływu czasu. Można wykryć śledzone ustawienia, takie jak tożsamości zarządzane, uaktualnienie systemu operacyjnego platformy i nazwy hostów.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager zmiany ustawień serwera proxy

Ustawienia, takie jak reguła konfiguracji protokołu IP, ustawienia protokołu TLS i wersje rozszerzeń, nie są jeszcze dostępne w usłudze Azure Resource Graph, dlatego należy zmienić zapytania analizy i ponownie obliczyć te zmiany, aby uzyskać więcej szczegółów dotyczących zmian w aplikacji.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Zmiany w wdrażaniu i konfigurowaniu aplikacji sieci Web (zmiany w gościu)

Analiza zmian przechwytuje stan wdrożenia i konfiguracji aplikacji co 4 godziny. Może wykrywać na przykład zmiany w zmiennych środowiskowych aplikacji. Narzędzie oblicza różnice i prezentuje, co się zmieniło. W przeciwieństwie do Menedżer zasobów zmian, informacje o zmianie wdrożenia kodu mogą nie być dostępne natychmiast w narzędziu. Aby wyświetlić najnowsze zmiany w analizie zmiany, wybierz pozycję **Odśwież**.

![Zrzut ekranu przedstawiający przycisk "Skanuj zmiany teraz"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Zmiany zależności

Zmiany zależności zasobów mogą również powodować problemy w aplikacji sieci Web. Na przykład jeśli aplikacja sieci Web wywołuje w pamięci podręcznej Redis, jednostka SKU pamięci podręcznej Redis może mieć wpływ na wydajność aplikacji sieci Web. Aby wykryć zmiany w zależnościach, Analiza zmian sprawdza rekord DNS aplikacji sieci Web. W ten sposób identyfikuje zmiany we wszystkich składnikach aplikacji, które mogą powodować problemy.
Obecnie obsługiwane są następujące zależności:
- Web Apps
- Azure Storage
- Azure SQL

## <a name="application-change-analysis-service"></a>Usługa analizy zmian aplikacji

Usługa analizy zmian aplikacji oblicza i agreguje dane ze źródeł danych wymienionych powyżej. Zawiera zestaw analiz dla użytkowników, którzy mogą łatwo przechodzić przez wszystkie zmiany zasobów i identyfikować, które zmiany są istotne w kontekście rozwiązywania problemów lub monitorowania.
Dostawca zasobów "Microsoft. ChangeAnalysis" musi być zarejestrowany w ramach subskrypcji dla Azure Resource Manager śledzonych właściwości i ustawień serwera proxy Zmień dane na dostępne. Po wprowadzeniu narzędzia do diagnozowania i rozwiązywania problemów aplikacji sieci Web ten dostawca zasobów jest automatycznie rejestrowany. Nie ma żadnych implementacji wydajności ani kosztów dla Twojej subskrypcji. Włączenie zmiany analizy dla aplikacji sieci Web (lub włączenie narzędzia diagnozowanie i rozwiązywanie problemów) będzie miało niewielki wpływ na wydajność aplikacji sieci Web i nie ma kosztu rozliczeń.
W przypadku zmian w aplikacji sieci Web w gościu do skanowania plików kodu w ramach aplikacji sieci Web jest wymagane oddzielne Włączanie. Aby uzyskać więcej informacji, zobacz sekcję [zmiana analizy w sekcji diagnozowanie i rozwiązywanie problemów](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#application-change-analysis-in-the-diagnose-and-solve-problems-tool) w dalszej części tego artykułu, aby uzyskać więcej informacji.

## <a name="visualizations-for-application-change-analysis"></a>Wizualizacje do analizy zmian aplikacji

### <a name="standalone-ui"></a>Autonomiczny interfejs użytkownika

W Azure Monitor istnieje autonomiczne okienko do analizy zmian, aby wyświetlić wszystkie zmiany z wglądem w zależności aplikacji i zasoby.

Wyszukaj wartość Analiza zmian na pasku wyszukiwania na Azure Portal, aby uruchomić środowisko.

![Zrzut ekranu przeszukiwania analizy zmian w Azure Portal](./media/change-analysis/search-change-analysis.png)

Wszystkie zasoby w ramach wybranej subskrypcji są wyświetlane ze zmianami z ostatnich 24 godzin. Aby zoptymalizować wydajność ładowania strony, usługa wyświetla 10 zasobów jednocześnie. Kliknij pozycję następne strony, aby wyświetlić więcej zasobów. Pracujemy nad usunięciem tego ograniczenia.

![Zrzut ekranu przedstawiający blok analizy zmian w Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Kliknij zasób, aby wyświetlić wszystkie jego zmiany. W razie potrzeby przechodzenie do szczegółów w celu wyświetlenia szczegółów i szczegółowych informacji o zmianach w formacie JSON.

![Zrzut ekranu przedstawiający szczegóły zmiany](./media/change-analysis/change-details.png)

Aby dowolna opinia, użyj przycisku Wyślij opinię w bloku lub wiadomości e-mail changeanalysisteam@microsoft.com .

![Zrzut ekranu przycisku opinii w bloku Analiza zmian](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Aplikacja sieci Web diagnozowanie i rozwiązywanie problemów

W Azure Monitor analizy zmian są również wbudowane w funkcję **diagnozowania i rozwiązywania problemów** . Uzyskaj dostęp do tego środowiska na stronie **Przegląd** aplikacji App Service.

![Zrzut ekranu przedstawiający przycisk "przegląd" i przycisk "diagnozowanie i rozwiązywanie problemów"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analiza zmian aplikacji w narzędziu diagnozowanie i rozwiązywanie problemów

Analiza zmian aplikacji to autonomiczny detektor w aplikacji sieci Web diagnozowanie i rozwiązywanie problemów. Jest on również agregowany w przypadku **awarii aplikacji** i **detektorów w aplikacji sieci Web**. Po wprowadzeniu narzędzia do diagnozowania i rozwiązywania problemów dostawca zasobów **Microsoft. ChangeAnalysis** zostanie automatycznie zarejestrowany. Postępuj zgodnie z tymi instrukcjami, aby włączyć śledzenie zmian w aplikacji internetowej w trybie gościnnym.

1. Wybierz pozycję **dostępność i wydajność**.

    ![Zrzut ekranu przedstawiający opcje rozwiązywania problemów z "dostępnością i wydajnością"](./media/change-analysis/availability-and-performance.png)

2. Wybierz pozycję **zmiany aplikacji**. Ta funkcja jest również dostępna w obszarze **awarie aplikacji**.

   ![Zrzut ekranu przedstawiający przycisk "awarie aplikacji"](./media/change-analysis/application-changes.png)

3. Aby włączyć analizę zmian, wybierz pozycję **Włącz teraz**.

   ![Zrzut ekranu opcji "awarie aplikacji"](./media/change-analysis/enable-changeanalysis.png)

4. Włącz **analizę zmian** i wybierz pozycję **Zapisz**. Narzędzie wyświetla wszystkie aplikacje sieci Web w ramach planu App Service. Możesz użyć przełącznika poziomu planu, aby włączyć analizę zmian dla wszystkich aplikacji sieci Web w ramach planu.

    ![Zrzut ekranu przedstawiający interfejs użytkownika "Włącz analizę zmian"](./media/change-analysis/change-analysis-on.png)

5. Aby uzyskać dostęp do analizy zmian, wybierz opcję **diagnozowanie i rozwiązywanie problemów dotyczących**  >  **dostępności i wydajności**  >  **aplikacji**. Zobaczysz Wykres podsumowujący typ zmian w czasie wraz ze szczegółami dotyczącymi tych zmian. Domyślnie zmiany w ciągu ostatnich 24 godzin są wyświetlane, aby ułatwić natychmiastowe Rozwiązywanie problemów.

     ![Zrzut ekranu przedstawiający widok różnic między zmianami](./media/change-analysis/change-view.png)

### <a name="enable-change-analysis-at-scale"></a>Włącz analizę zmian na dużą skalę

Jeśli Twoja subskrypcja obejmuje wiele aplikacji sieci Web, włączenie usługi na poziomie aplikacji sieci Web byłoby niewydajne. Uruchom następujący skrypt, aby włączyć wszystkie aplikacje sieci Web w ramach subskrypcji.

Wymagania wstępne:

- PowerShell AZ module. Postępuj zgodnie z instrukcjami w [instalacji modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Uruchom poniższy skrypt:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnozowanie i rozwiązywanie problemów z maszyną wirtualną

Przejdź do narzędzia diagnozowanie i rozwiązywanie problemów dotyczących maszyny wirtualnej.  Przejdź do **Narzędzia do rozwiązywania problemów**, przejdź do strony i wybierz pozycję **Analizuj ostatnie zmiany** , aby wyświetlić zmiany na maszynie wirtualnej.

![Zrzut ekranu maszyny wirtualnej diagnozowanie i rozwiązywanie problemów](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Zrzut ekranu maszyny wirtualnej diagnozowanie i rozwiązywanie problemów](./media/change-analysis/analyze-recent-changes.png)

## <a name="next-steps"></a>Następne kroki

- Włącz Application Insights dla [aplikacji App Services platformy Azure](azure-web-apps.md).
- Włącz Application Insights dla [maszyny wirtualnej platformy Azure i zestawu skalowania maszyn wirtualnych platformy Azure dla aplikacji hostowanych przez usługi IIS](azure-vm-vmss-apps.md).
- Dowiedz się więcej o [usłudze Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), która ułatwia analizę zmian.
