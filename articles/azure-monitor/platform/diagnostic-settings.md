---
title: Tworzenie ustawień diagnostycznych w celu wysyłania metryk i dzienników platformy do różnych miejsc docelowych
description: Wysyłaj metryki i dzienniki Azure Monitor platformy do dzienników Azure Monitor, Azure Storage lub Azure Event Hubs przy użyciu ustawienia diagnostycznego.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: 29e50a5c9b306d0e4491852fd08ecdf73026ebc2
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882249"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Tworzenie ustawień diagnostycznych w celu wysyłania metryk i dzienników platformy do różnych miejsc docelowych
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą. [Metryki platformy](data-platform-metrics.md) są zbierane domyślnie i zazwyczaj przechowywane w bazie danych metryk Azure monitor. Ten artykuł zawiera szczegółowe informacje na temat tworzenia i konfigurowania ustawień diagnostycznych w celu wysyłania metryk platformy i dzienników platformy do różnych miejsc docelowych.

> [!IMPORTANT]
> Przed utworzeniem ustawienia diagnostycznego dla dziennika aktywności należy najpierw wyłączyć wszystkie starsze konfiguracje. Aby uzyskać szczegółowe informacje, zobacz [starsze metody zbierania danych](activity-log.md#legacy-collection-methods) .

Każdy zasób platformy Azure wymaga własnego ustawienia diagnostycznego, które definiuje następujące kryteria:

- Kategorie dzienników i danych metryk wysyłanych do miejsc docelowych zdefiniowanych w ustawieniu. Dostępne kategorie różnią się w zależności od typu zasobu.
- Co najmniej jedno miejsce docelowe do wysłania dzienników. Bieżące miejsca docelowe obejmują obszar roboczy usługi Log Analytics, usługę Event Hubs i usługę Azure Storage.

Pojedyncze ustawienie diagnostyczne może definiować nie więcej niż jeden z elementów docelowych. Jeśli chcesz wysyłać dane do więcej niż jednego określonego typu miejsca docelowego (na przykład do dwóch różnych obszarów roboczych usługi Log Analytics), utwórz wiele ustawień. Każdy zasób może mieć do 5 ustawień diagnostycznych.

Poniższy film wideo przeprowadzi Cię przez dzienniki platformy routingu z ustawieniami diagnostycznymi.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [Metryki platformy](metrics-supported.md) są wysyłane automatycznie do [metryk Azure monitor](data-platform-metrics.md). Ustawienia diagnostyczne mogą służyć do wysyłania metryk dla określonych usług platformy Azure do dzienników Azure Monitor do analizy z innymi danymi monitorowania przy użyciu [zapytań dzienników](../log-query/log-query-overview.md) z określonymi ograniczeniami. 
>  
>  
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach. *Na przykład*: metrykę "IOReadBytes" na łańcucha bloków można eksplorować i wykreślić na poziomie węzła. Jednak po wyeksportowaniu za pośrednictwem ustawień diagnostycznych Metryka wyeksportowana reprezentuje jako wszystkie bajty odczytu dla wszystkich węzłów. Ponadto ze względu na ograniczenia wewnętrzne nie wszystkie metryki są eksportowane do Azure Monitor dzienników/Log Analytics. Aby uzyskać więcej informacji, zobacz [listę metryk możliwych do eksportu](metrics-supported-export-diagnostic-settings.md). 
>  
>  
> Aby obejść te ograniczenia dotyczące określonych metryk, zalecamy ręczne wyodrębnienie ich przy użyciu [interfejsu API REST metryk](/rest/api/monitor/metrics/list) i zaimportowanie ich do dzienników Azure monitor przy użyciu [interfejsu API modułu zbierającego dane Azure monitor](data-collector-api.md).  


## <a name="destinations"></a>Miejsca docelowe
Dzienniki platformy i metryki mogą być wysyłane do miejsc docelowych w poniższej tabeli. 

| Element docelowy | Opis |
|:---|:---|
| [Obszar roboczy usługi Log Analytics](design-logs-deployment.md) | Wysyłanie dzienników i metryk do obszaru roboczego Log Analytics umożliwia analizowanie ich przy użyciu innych danych monitorowania zbieranych przez Azure Monitor przy użyciu zaawansowanych zapytań dzienników oraz korzystanie z innych funkcji Azure Monitor, takich jak alerty i wizualizacje. |
| [Centra zdarzeń](../../event-hubs/index.yml) | Wysyłanie dzienników i metryk do Event Hubs umożliwia przesyłanie strumieniowe danych do systemów zewnętrznych, takich jak rozwiązań Siem innych firm, oraz innych rozwiązań usługi log Analytics.  |
| [Konto usługi Azure Storage](../../storage/blobs/index.yml) | Archiwizowanie dzienników i metryk na koncie usługi Azure Storage jest przydatne w przypadku inspekcji, statycznej analizy lub tworzenia kopii zapasowych. W porównaniu do Azure Monitor dzienników i Log Analytics obszaru roboczego usługa Azure Storage jest tańsza, a dzienniki mogą być przechowywane w nieskończoność.  |


### <a name="destination-requirements"></a>Wymagania dotyczące miejsca docelowego

Przed utworzeniem ustawień diagnostycznych należy utworzyć wszystkie miejsca docelowe dla tego ustawienia diagnostycznego. Lokalizacja docelowa nie musi znajdować się w tej samej subskrypcji co zasób, który wysyła dzienniki, dopóki użytkownik, który konfiguruje ustawienie, ma odpowiedni dostęp do obu subskrypcji na platformie Azure. W poniższej tabeli przedstawiono unikatowe wymagania dla każdego miejsca docelowego, w tym wszystkie ograniczenia regionalne.

| Element docelowy | Wymagania |
|:---|:---|
| Obszar roboczy usługi Log Analytics | Obszar roboczy nie musi znajdować się w tym samym regionie co monitorowany zasób.|
| Usługa Event Hubs | Zasady dostępu współdzielonego dla przestrzeni nazw określają uprawnienia, które ma mechanizm przesyłania strumieniowego. Przesyłanie strumieniowe do Event Hubs wymaga uprawnień do zarządzania, wysyłania i nasłuchiwania. Aby zaktualizować ustawienie diagnostyczne w celu uwzględnienia przesyłania strumieniowego, musisz mieć uprawnienie ListKey dla tej reguły autoryzacji Event Hubs.<br><br>Przestrzeń nazw centrum zdarzeń musi znajdować się w tym samym regionie co monitorowany zasób, jeśli zasób jest regionalny. |
| Konto magazynu Azure | Nie należy używać istniejącego konta magazynu, które ma inne niemonitorowane dane, które są w nim przechowywane, dzięki czemu można lepiej kontrolować dostęp do danych. Jeśli archiwizowanie dzienników aktywności i dzienników zasobów odbywa się razem, możesz użyć tego samego konta magazynu, aby zachować wszystkie dane monitorowania w centralnej lokalizacji.<br><br>Aby wysłać dane do niezmiennego magazynu, należy ustawić niezmienne zasady dla konta magazynu zgodnie z opisem w temacie [Set and Manage niezmienności Policy for BLOB Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md). Należy wykonać wszystkie kroki opisane w tym artykule, w tym Włączanie chronionych zapisów obiektów BLOB.<br><br>Konto magazynu musi znajdować się w tym samym regionie co monitorowany zasób, jeśli zasób jest regionalny. |

> [!NOTE]
> Konta usługi Azure Data Lake Storage Gen2 nie są obecnie obsługiwane jako miejsce docelowe dla ustawień diagnostycznych, mimo że mogą być wymienione jako prawidłowa opcja w witrynie Azure Portal.

> [!NOTE]
> Azure Monitor (ustawienia diagnostyczne) nie mogą uzyskać dostępu do zasobów Event Hubs, gdy sieci wirtualne są włączone. Należy włączyć ustawienie Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tego ustawienia zapory w centrum zdarzeń, aby usługa Azure Monitor (ustawienia diagnostyczne) uzyskała dostęp do zasobów Event Hubs. 


## <a name="create-in-azure-portal"></a>Tworzenie w witrynie Azure Portal

Ustawienia diagnostyczne można skonfigurować w Azure Portal z menu Azure Monitor lub z menu zasobów.

1. Konfigurowanie ustawień diagnostycznych w Azure Portal zależy od zasobu.

   - W przypadku pojedynczego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **monitor** w menu zasób.

        ![Zrzut ekranu przedstawiający sekcję monitorowanie menu zasobów w Azure Portal z wyróżnionymi ustawieniami diagnostycznymi.](media/diagnostic-settings/menu-resource.png)

   - W przypadku co najmniej jednego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **ustawienia** w menu Azure Monitor a następnie kliknij zasób.

        ![Zrzut ekranu przedstawiający sekcję Ustawienia w menu Azure Monitor z wyróżnionymi ustawieniami diagnostycznymi.](media/diagnostic-settings/menu-monitor.png)

   - W dzienniku aktywności kliknij pozycję **Dziennik aktywności** w menu **Azure monitor** a następnie wybierz polecenie **Ustawienia diagnostyczne**. Upewnij się, że dla dziennika aktywności została wyłączona każda starsza konfiguracja. Aby uzyskać szczegółowe informacje, zobacz temat [wyłączanie istniejących ustawień](./activity-log.md#legacy-collection-methods) .

        ![Zrzut ekranu przedstawiający menu Azure Monitor z wybranym dziennikiem aktywności i ustawieniami diagnostycznymi wyróżnionymi na pasku menu Monitor-Activity log.](media/diagnostic-settings/menu-activity-log.png)

2. Jeśli w wybranym zasobie nie ma żadnych ustawień, zostanie wyświetlony monit o utworzenie ustawienia. Kliknij pozycję **Dodaj ustawienie diagnostyczne**.

   ![Dodaj ustawienie diagnostyczne — brak istniejących ustawień](media/diagnostic-settings/add-setting.png)

   Jeśli w zasobie istnieją istniejące ustawienia, zostanie wyświetlona lista ustawień, które zostały już skonfigurowane. Kliknij pozycję **Dodaj ustawienie diagnostyczne** , aby dodać nowe ustawienie lub **edytować ustawienie** , aby edytować istniejące. Każde ustawienie nie może mieć więcej niż jednego z typów docelowych.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-settings/edit-setting.png)

3. Nadaj ustawieniu nazwę, jeśli jeszcze jej nie ma.

    ![Dodaj ustawienie diagnostyczne](media/diagnostic-settings/setting-new-blank.png)

4. **Szczegóły kategorii (co do trasy)** — zaznacz pole wyboru dla każdej kategorii danych, które mają być wysyłane do miejsc docelowych określonych później. Lista kategorii różni się w zależności od usługi platformy Azure.

     - **AllMetrics** kieruje metryki platformy zasobu do magazynu dzienników platformy Azure, ale w formularzu dziennika. Te metryki są zwykle wysyłane tylko do bazy danych szeregów czasowych metryk Azure Monitor. Wysłanie ich do magazynu dzienników Azure Monitor (który można przeszukiwać za pośrednictwem Log Analytics) pomaga zintegrować je z zapytaniami, które przeszukują inne dzienniki. Ta opcja może być niedostępna dla wszystkich typów zasobów. Jeśli jest obsługiwana, [Azure monitor obsługiwane metryki](metrics-supported.md) wyświetlają metryki, które są zbierane dla typów zasobów.

       > [!NOTE]
       > Zobacz ograniczenie dotyczące metryk routingu, aby Azure Monitor dzienniki wcześniej w tym artykule.  


     - **Dzienniki** zawiera różne kategorie dostępne w zależności od typu zasobu. Sprawdź wszystkie kategorie, które chcesz skierować do miejsca docelowego.

5. **Szczegóły miejsca docelowego** — zaznacz pole wyboru dla każdego miejsca docelowego. Po zaznaczeniu każdego pola wyświetlane są opcje umożliwiające dodanie dodatkowych informacji.

      ![Wyślij do Log Analytics lub Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** — wprowadź subskrypcję i obszar roboczy.  Jeśli nie masz obszaru roboczego, musisz go [utworzyć przed kontynuowaniem](../learn/quick-create-workspace.md).

    1. **Centra zdarzeń** — określ następujące kryteria:
       - Subskrypcja, do której należy centrum zdarzeń
       - Przestrzeń nazw centrum zdarzeń — Jeśli jeszcze jej nie masz, musisz ją [utworzyć](../../event-hubs/event-hubs-create.md) .
       - Nazwa centrum zdarzeń (opcjonalnie), do którego mają zostać wysłane wszystkie dane. Jeśli nie określisz nazwy, centrum zdarzeń zostanie utworzone dla każdej kategorii dzienników. W przypadku wysyłania wielu kategorii warto określić nazwę, aby ograniczyć liczbę utworzonych centrów zdarzeń. Szczegóły można znaleźć w temacie limity [przydziału i limity Event Hubs platformy Azure](../../event-hubs/event-hubs-quotas.md) .
       - Zasady centrum zdarzeń (opcjonalnie) zasady definiują uprawnienia mechanizmu przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Event-Hubs-Features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Magazyn** — wybierz subskrypcję, konto magazynu i zasady przechowywania.

        ![Wyślij do magazynu](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Rozważ ustawienie zasad przechowywania na 0 i ręczne usunięcie danych z magazynu przy użyciu zaplanowanego zadania, aby uniknąć ewentualnych pomyłek w przyszłości.
        >
        > Po pierwsze, jeśli używasz magazynu do archiwizacji, zazwyczaj chcesz, aby dane były przechowywane przez ponad 365 dni. Po wybraniu zasad przechowywania, które są większe niż 0, Data wygaśnięcia jest dołączana do dzienników w momencie magazynowania. Nie można zmienić daty dla tych dzienników, gdy są przechowywane.
        >
        > Jeśli na przykład ustawisz zasady przechowywania dla elementu *WorkflowRuntime* na 180 dni, a następnie 24 godziny później ustawisz go na 365 dni, dzienniki przechowywane w ciągu pierwszych 24 godzin zostaną automatycznie usunięte po 180 dni, a wszystkie kolejne dzienniki tego typu zostaną automatycznie usunięte po upływie 365 dni. Zmiana zasad przechowywania w późniejszym czasie nie powoduje, że pierwsze 24 godziny dzienników pozostanie na około 365 dni.

6. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla tego zasobu, a dzienniki są przesyłane strumieniowo do określonych lokalizacji docelowych w miarę generowania nowych danych zdarzeń. Gdy zdarzenie jest emitowane i [pojawia się w obszarze roboczym log Analytics](data-ingestion-time.md), może upłynąć do 15 minut.

## <a name="create-using-powershell"></a>Tworzenie za pomocą programu PowerShell

Użyj polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) , aby utworzyć ustawienie diagnostyczne z [Azure PowerShell](../samples/powershell-samples.md). Zapoznaj się z dokumentacją tego polecenia cmdlet, aby zapoznać się z opisami jego parametrów.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj opcji [Utwórz ustawienia diagnostyczne w Azure monitor przy użyciu szablonu Menedżer zasobów](../samples/resource-manager-diagnostic-settings.md) , aby utworzyć szablon Menedżer zasobów i wdrożyć go przy użyciu programu PowerShell.

Poniżej znajduje się przykładowe polecenie cmdlet programu PowerShell służące do tworzenia ustawień diagnostycznych przy użyciu wszystkich trzech miejsc docelowych.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Tworzenie przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) , aby utworzyć ustawienie diagnostyczne przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/monitor?view=azure-cli-latest). Zapoznaj się z dokumentacją tego polecenia, aby zapoznać się z opisami jego parametrów.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj opcji [Utwórz ustawienia diagnostyczne w Azure monitor przy użyciu szablonu Menedżer zasobów](../samples/resource-manager-diagnostic-settings.md) , aby utworzyć szablon Menedżer zasobów i wdrożyć go przy użyciu interfejsu wiersza polecenia.

Poniżej znajduje się przykładowe polecenie interfejsu wiersza polecenia służące do tworzenia ustawień diagnostycznych przy użyciu wszystkich trzech miejsc docelowych.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-resource-manager-template"></a>Tworzenie przy użyciu szablonu Menedżer zasobów
Zobacz [przykłady Menedżer zasobów szablonów dla ustawień diagnostycznych w Azure monitor](../samples/resource-manager-diagnostic-settings.md) do tworzenia lub aktualizowania ustawień diagnostycznych za pomocą szablonu Menedżer zasobów.

## <a name="create-using-rest-api"></a>Tworzenie za pomocą interfejsu API REST
Zobacz [Ustawienia diagnostyczne](/rest/api/monitor/diagnosticsettings) , aby utworzyć lub zaktualizować ustawienia diagnostyczne przy użyciu [interfejsu API REST Azure monitor](/rest/api/monitor/).

## <a name="create-using-azure-policy"></a>Utwórz za pomocą Azure Policy
Ponieważ dla każdego zasobu platformy Azure należy utworzyć ustawienie diagnostyczne, Azure Policy może służyć do automatycznego tworzenia ustawień diagnostycznych podczas tworzenia poszczególnych zasobów. Aby uzyskać szczegółowe informacje, zobacz [wdrażanie Azure monitor na dużą skalę przy użyciu Azure Policy](../deploy-scale.md) .


## <a name="next-steps"></a>Następne kroki

- [Przeczytaj więcej na temat dzienników platformy Azure](platform-logs-overview.md)
