---
title: Używanie ustawień diagnostycznych do zbierania metryk platformy i dzienników oraz na platformie Azure
description: Wysyłaj metryki i dzienniki Azure Monitor platformy do dzienników Azure Monitor, Azure Storage lub Azure Event Hubs przy użyciu ustawienia diagnostycznego.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681121"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Tworzenie ustawień diagnostycznych w celu zbierania dzienników zasobów i metryk na platformie Azure

[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą. [Metryki platformy](data-platform-metrics.md) są zbierane domyślnie i zazwyczaj przechowywane w bazie danych metryk Azure monitor.

Ten artykuł zawiera szczegółowe informacje na temat tworzenia i konfigurowania ustawień diagnostycznych w celu wysyłania metryk platformy i dzienników platformy do różnych miejsc docelowych.

> [!IMPORTANT]
> Przed utworzeniem ustawienia diagnostycznego w celu zebrania dziennika aktywności należy najpierw wyłączyć starszą konfigurację. Aby uzyskać szczegółowe informacje, zobacz [zbieranie dziennika aktywności platformy Azure ze starszymi ustawieniami](diagnostic-settings-legacy.md) .

Każdy zasób platformy Azure wymaga własnego ustawienia diagnostycznego, które definiuje następujące kryteria:

- Kategorie dzienników i danych metryk wysyłanych do miejsc docelowych zdefiniowanych w ustawieniu. Dostępne kategorie różnią się w zależności od typu zasobu.
- Co najmniej jedno miejsce docelowe do wysłania dzienników. Bieżące miejsca docelowe obejmują obszar roboczy usługi Log Analytics, usługę Event Hubs i usługę Azure Storage.

Pojedyncze ustawienie diagnostyczne może definiować nie więcej niż jeden z elementów docelowych. Jeśli chcesz wysyłać dane do więcej niż jednego określonego typu miejsca docelowego (na przykład do dwóch różnych obszarów roboczych usługi Log Analytics), utwórz wiele ustawień. Każdy zasób może mieć do 5 ustawień diagnostycznych.

> [!NOTE]
> [Metryki platformy](metrics-supported.md) są zbierane automatycznie, aby [Azure monitor metryki](data-platform-metrics.md). Za pomocą ustawień diagnostycznych można zbierać metryki dla określonych usług platformy Azure w Azure Monitor dzienników do analizy z innymi danymi monitorowania przy użyciu [zapytań dzienników](../log-query/log-query-overview.md).

## <a name="destinations"></a>Miejsca docelowe

Dzienniki platformy i metryki mogą być wysyłane do miejsc docelowych w poniższej tabeli. Aby uzyskać szczegółowe informacje na temat wysyłania danych do tego miejsca docelowego, należy postępować zgodnie z poniższymi tabelami.

| Element docelowy | Opis |
|:---|:---|
| [Obszar roboczy usługi Log Analytics](resource-logs-collect-workspace.md) | Zbieranie dzienników i metryk w obszarze roboczym Log Analytics umożliwia analizowanie ich przy użyciu innych danych monitorowania zbieranych przez Azure Monitor przy użyciu zaawansowanych zapytań dzienników, a także korzystanie z innych funkcji Azure Monitor, takich jak alerty i wizualizacje. |
| [Centra zdarzeń](resource-logs-stream-event-hubs.md) | Wysyłanie dzienników i metryk do Event Hubs umożliwia przesyłanie strumieniowe danych do systemów zewnętrznych, takich jak rozwiązań Siem innych firm, oraz innych rozwiązań usługi log Analytics. |
| [Konto usługi Azure Storage](resource-logs-collect-storage.md) | Archiwizowanie dzienników i metryk na koncie usługi Azure Storage jest przydatne w przypadku inspekcji, statycznej analizy lub tworzenia kopii zapasowych. W porównaniu do Azure Monitor dzienników i Log Analytics obszaru roboczego usługa Azure Storage jest tańsza, a dzienniki mogą być przechowywane w nieskończoność. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Tworzenie ustawień diagnostycznych w Azure Portal

Ustawienia diagnostyczne można skonfigurować w Azure Portal z menu Azure Monitor lub z menu zasobów.

1. Konfigurowanie ustawień diagnostycznych w Azure Portal zależy od zasobu.

   - W przypadku pojedynczego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **monitor** w menu zasób.

        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-resource.png)

   - W przypadku co najmniej jednego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **ustawienia** w menu Azure Monitor a następnie kliknij zasób.

      ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-monitor.png)

   - W dzienniku aktywności kliknij pozycję **Dziennik aktywności** w menu **Azure monitor** a następnie wybierz polecenie **Ustawienia diagnostyczne**. Upewnij się, że dla dziennika aktywności została wyłączona każda starsza konfiguracja. Aby uzyskać szczegółowe informacje, zobacz temat [wyłączanie istniejących ustawień](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) .

        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-activity-log.png)

2. Jeśli w wybranym zasobie nie ma żadnych ustawień, zostanie wyświetlony monit o utworzenie ustawienia. Kliknij pozycję **Dodaj ustawienie diagnostyczne**.

   ![Dodaj ustawienie diagnostyczne — brak istniejących ustawień](media/diagnostic-settings/add-setting.png)

   Jeśli w zasobie istnieją istniejące ustawienia, zostanie wyświetlona lista ustawień, które zostały już skonfigurowane. Kliknij pozycję **Dodaj ustawienie diagnostyczne** , aby dodać nowe ustawienie lub **edytować ustawienie** , aby edytować istniejące. Każde ustawienie nie może mieć więcej niż jednego z typów docelowych.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-settings/edit-setting.png)

3. Nadaj ustawieniu nazwę, jeśli jeszcze jej nie ma.

    ![Dodaj ustawienie diagnostyczne](media/diagnostic-settings/setting-new-blank.png)

4. **Szczegóły kategorii (co do trasy)** — zaznacz pole wyboru dla każdej kategorii danych, które mają być wysyłane do miejsc docelowych określonych później. Lista kategorii różni się w zależności od usługi platformy Azure.

     - **AllMetrics** kieruje metryki platformy zasobu do magazynu dzienników platformy Azure, ale w formularzu dziennika. Te metryki są zwykle wysyłane tylko do bazy danych szeregów czasowych metryk Azure Monitor. Wysłanie ich do magazynu dzienników Azure Monitor (który można przeszukiwać za pośrednictwem Log Analytics), aby zintegrować je z kwerendami, które przeszukują inne dzienniki. Ta opcja może być niedostępna dla wszystkich typów zasobów. Jeśli jest obsługiwana, [Azure monitor obsługiwane metryki](metrics-supported.md) wyświetlają metryki, które są zbierane dla typów zasobów.

       > [!NOTE]
       > Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
       >
       > *Na przykład*: metrykę "IOReadBytes" na łańcucha bloków można eksplorować i wykreślić na poziomie węzła. Jednak po wyeksportowaniu za pośrednictwem ustawień diagnostycznych Metryka wyeksportowana reprezentuje jako wszystkie bajty odczytu dla wszystkich węzłów.

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

6. Kliknij przycisk **Zapisz**.

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla tego zasobu, a dzienniki są przesyłane strumieniowo do określonych lokalizacji docelowych w miarę generowania nowych danych zdarzeń. Gdy zdarzenie jest emitowane i [pojawia się w obszarze roboczym log Analytics](data-ingestion-time.md), może upłynąć do 15 minut.

## <a name="create-diagnostic-settings-using-powershell"></a>Tworzenie ustawień diagnostycznych przy użyciu programu PowerShell

Użyj polecenia cmdlet [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) , aby utworzyć ustawienie diagnostyczne z [Azure PowerShell](powershell-quickstart-samples.md). Zapoznaj się z dokumentacją tego polecenia cmdlet, aby zapoznać się z opisami jego parametrów.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj opcji [Utwórz ustawienia diagnostyczne w Azure monitor przy użyciu szablonu Menedżer zasobów](diagnostic-settings-template.md) , aby utworzyć szablon Menedżer zasobów i wdrożyć go przy użyciu programu PowerShell.

Poniżej znajduje się przykładowe polecenie cmdlet programu PowerShell służące do tworzenia ustawień diagnostycznych przy użyciu wszystkich trzech miejsc docelowych.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>Tworzenie ustawień diagnostycznych przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ monitor Diagnostic-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) , aby utworzyć ustawienie diagnostyczne przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Zapoznaj się z dokumentacją tego polecenia, aby zapoznać się z opisami jego parametrów.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj opcji [Utwórz ustawienia diagnostyczne w Azure monitor przy użyciu szablonu Menedżer zasobów](diagnostic-settings-template.md) , aby utworzyć szablon Menedżer zasobów i wdrożyć go przy użyciu interfejsu wiersza polecenia.

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

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurowanie ustawień diagnostycznych przy użyciu interfejsu API REST

Zobacz [Ustawienia diagnostyczne](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) , aby utworzyć lub zaktualizować ustawienia diagnostyczne przy użyciu [interfejsu API REST Azure monitor](https://docs.microsoft.com/rest/api/monitor/).

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurowanie ustawień diagnostycznych przy użyciu szablonu Menedżer zasobów

Zobacz [Tworzenie ustawień diagnostycznych w Azure monitor przy użyciu szablonu Menedżer zasobów](diagnostic-settings-template.md) do tworzenia lub aktualizowania ustawień diagnostycznych za pomocą szablonu Menedżer zasobów.

## <a name="next-steps"></a>Następne kroki

- [Przeczytaj więcej na temat dzienników platformy Azure](platform-logs-overview.md)
