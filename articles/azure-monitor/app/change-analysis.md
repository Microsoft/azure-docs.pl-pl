---
title: Użyj analizy zmian aplikacji w Azure Monitor, aby znaleźć problemy z aplikacją sieci Web | Microsoft Docs
description: Korzystając z analizy zmian aplikacji w Azure Monitor, można rozwiązywać problemy z aplikacjami w witrynach na żywo w programie Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 43ece2cb0f5cb9428d8d73f769018e9fe2408ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655827"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Korzystanie z analizy zmian aplikacji (wersja zapoznawcza) w Azure Monitor

Gdy wystąpi problem lub awaria witryny na żywo, szybkie określenie głównej przyczyny ma krytyczne znaczenie. Standardowe rozwiązania do monitorowania mogą powiadamiać o problemie. Mogą nawet wskazywać, który składnik kończy się niepowodzeniem. Jednak ten alert nie zawsze natychmiast wyjaśni przyczynę niepowodzenia. Wiadomo, że witryna działała pięć minut temu, a teraz jest ona uszkodzona. Co zmieniło się w ciągu ostatnich pięciu minut? Jest to pytanie, że analiza zmian aplikacji została zaprojektowana pod kątem odpowiedzi w Azure Monitor.

W oparciu o możliwości [grafu zasobów platformy Azure](../../governance/resource-graph/overview.md)Analiza zmian zapewnia wgląd w zmiany aplikacji platformy Azure w celu zwiększenia zauważalności i zmniejszenia MTTR (średni czas naprawy).

> [!IMPORTANT]
> Analiza zmian jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Ta wersja nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="overview"></a>Omówienie

Analiza zmian wykrywa różne typy zmian z warstwy infrastruktury we wszystkich sposobach wdrażania aplikacji. Jest to dostawca zasobów platformy Azure na poziomie subskrypcji, który sprawdza zmiany zasobów w subskrypcji. Analiza zmian udostępnia dane dla różnych narzędzi diagnostycznych, które pomagają użytkownikom zrozumieć, jakie zmiany mogą powodować problemy.

Na poniższym diagramie przedstawiono architekturę analizy zmian:

![Diagram architektury sposobu, w jaki Analiza zmian pobiera zmiany danych i udostępnia je narzędziom klienckim](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Obsługiwane typy zasobów

Usługa analizy zmian aplikacji obsługuje zmiany poziomu właściwości zasobów we wszystkich typach zasobów platformy Azure, w tym w przypadku typowych zasobów, takich jak:
- Maszyna wirtualna
- Zestaw skalowania maszyn wirtualnych
- App Service
- Usługa Azure Kubernetes
- Funkcja platformy Azure
- Zasoby sieciowe: sieciowa Grupa zabezpieczeń, Virtual Network, Application Gateway itd.
- Usługi danych: Storage, SQL, Redis Cache, Cosmos DB itd.

## <a name="data-sources"></a>Źródła danych

Zapytania analizy zmian aplikacji dotyczące Azure Resource Manager śledzonych właściwości, konfiguracji serwera proxy i zmian aplikacji sieci Web w ramach gościa. Ponadto usługa śledzi zmiany zależności zasobów, aby zdiagnozować i monitorować kompleksową aplikację.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager zmian właściwości śledzonych

Przy użyciu [grafu zasobów platformy Azure](../../governance/resource-graph/overview.md)Analiza zmian zawiera historyczny rekord, w jaki sposób zasoby platformy Azure obsługujące aplikację zostały zmienione w miarę upływu czasu. Można wykryć śledzone ustawienia, takie jak tożsamości zarządzane, uaktualnienie systemu operacyjnego platformy i nazwy hostów.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager zmiany ustawień serwera proxy

Ustawienia, takie jak reguła konfiguracji protokołu IP, ustawienia protokołu TLS i wersje rozszerzeń, nie są jeszcze dostępne w usłudze Azure Resource Graph, dlatego należy zmienić zapytania analizy i ponownie obliczyć te zmiany, aby uzyskać więcej szczegółów dotyczących zmian w aplikacji.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Zmiany w wdrażaniu i konfigurowaniu aplikacji sieci Web (zmiany w gościu)

Analiza zmian przechwytuje stan wdrożenia i konfiguracji aplikacji co 4 godziny. Może wykrywać na przykład zmiany w zmiennych środowiskowych aplikacji. Narzędzie oblicza różnice i prezentuje, co się zmieniło. W przeciwieństwie do Menedżer zasobów zmian, informacje o zmianie wdrożenia kodu mogą nie być dostępne natychmiast w narzędziu. Aby wyświetlić najnowsze zmiany w analizie zmiany, wybierz pozycję **Odśwież**.

![Zrzut ekranu przedstawiający przycisk "Skanuj zmiany teraz"](./media/change-analysis/scan-changes.png)

Obecnie obsługiwane są wszystkie pliki tekstowe w katalogu głównym witryny **wwwroot** z następującymi rozszerzeniami:
- plik *. config
- *. XML
- *. JSON
- *. rozwiązania Gem
- *. yml
- *. txt
- *. ini
- *. env

### <a name="dependency-changes"></a>Zmiany zależności

Zmiany zależności zasobów mogą również powodować problemy w zasobie. Na przykład jeśli aplikacja sieci Web wywołuje w pamięci podręcznej Redis, jednostka SKU pamięci podręcznej Redis może mieć wpływ na wydajność aplikacji sieci Web. Innym przykładem jest to, że port 22 został zamknięty w sieciowej grupie zabezpieczeń maszyny wirtualnej, spowoduje błędy łączności.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Aplikacja sieci Web diagnozowanie i rozwiązywanie problemów Nawigator (wersja zapoznawcza)

Aby wykryć zmiany w zależnościach, Analiza zmian sprawdza rekord DNS aplikacji sieci Web. W ten sposób identyfikuje zmiany we wszystkich składnikach aplikacji, które mogą powodować problemy.
W aplikacji sieci Web są obecnie obsługiwane następujące zależności: **diagnozowanie i rozwiązywanie problemów | Nawigator (wersja zapoznawcza)**:

- Web Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>Powiązane zasoby

Analiza zmian aplikacji wykrywa powiązane zasoby. Typowe przykłady to sieciowe grupy zabezpieczeń, Virtual Network, Application Gateway i Load Balancer powiązane z maszyną wirtualną.
Zasoby sieciowe są zwykle automatycznie inicjowane w tej samej grupie zasobów co używane zasoby, więc filtrowanie zmian według grupy zasobów będzie zawierać wszystkie zmiany dotyczące maszyny wirtualnej i powiązanych zasobów sieciowych.

![Zrzut ekranu przedstawiający zmiany w sieci](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Włączenie usługi analizy zmian aplikacji

Usługa analizy zmian aplikacji oblicza i agreguje dane ze źródeł danych wymienionych powyżej. Zawiera zestaw analiz dla użytkowników, którzy mogą łatwo przechodzić przez wszystkie zmiany zasobów i identyfikować, które zmiany są istotne w kontekście rozwiązywania problemów lub monitorowania.
Dostawca zasobów "Microsoft. ChangeAnalysis" musi być zarejestrowany w ramach subskrypcji dla Azure Resource Manager śledzonych właściwości i ustawień serwera proxy Zmień dane na dostępne. Po wprowadzeniu narzędzia do diagnozowania i rozwiązywania problemów aplikacji sieci Web ten dostawca zasobów jest automatycznie rejestrowany.
W przypadku zmian w aplikacji sieci Web w gościu do skanowania plików kodu w ramach aplikacji sieci Web jest wymagane oddzielne Włączanie. Aby uzyskać więcej informacji, zobacz sekcję [zmiana analizy w sekcji diagnozowanie i rozwiązywanie problemów](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) w dalszej części tego artykułu, aby uzyskać więcej informacji.

## <a name="cost"></a>Koszt
Analiza zmian aplikacji to bezpłatna usługa — nie wiąże się z tym kosztem rozliczeniowym w przypadku subskrypcji z włączonym systemem. Usługa nie ma również wpływu na wydajność skanowania zmian właściwości zasobów platformy Azure. Po włączeniu zmiany analizy dla aplikacji sieci Web w trybie gościnnym (lub włączeniu narzędzia do diagnozowania i rozwiązywania problemów) będzie mieć niewielki wpływ na wydajność aplikacji sieci Web i nie ma kosztu rozliczeń.


## <a name="enable-change-analysis-at-scale-for-web-app-in-guest-file-and-environment-variable-changes"></a>Włącz analizę zmian w odpowiedniej skali dla plików gościa i zmiennych środowiskowych w aplikacji sieci Web

Jeśli Twoja subskrypcja obejmuje wiele aplikacji sieci Web, włączenie usługi na poziomie aplikacji sieci Web byłoby niewydajne. Uruchom następujący skrypt, aby włączyć wszystkie aplikacje sieci Web w ramach subskrypcji.

Wymagania wstępne:

- PowerShell AZ module. Postępuj zgodnie z instrukcjami w [instalacji modułu Azure PowerShell](/powershell/azure/install-az-ps)

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

## <a name="next-steps"></a>Następne kroki

- Informacje o [wizualizacjach w analizie zmian](change-analysis-visualizations.md)
- Dowiedz się, jak [rozwiązywać problemy związane z analizą zmian](change-analysis-troubleshoot.md)
- Włącz Application Insights dla [aplikacji App Services platformy Azure](azure-web-apps.md).
- Włącz Application Insights dla [maszyny wirtualnej platformy Azure i zestawu skalowania maszyn wirtualnych platformy Azure dla aplikacji hostowanych przez usługi IIS](azure-vm-vmss-apps.md).
