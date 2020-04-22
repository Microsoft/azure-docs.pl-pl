---
title: Używanie ustawień diagnostycznych do zbierania metryk i dzienników platformy oraz na platformie Azure
description: Wysyłaj metryki i dzienniki platformy Azure Monitor do dzienników usługi Azure Monitor, usługi Azure Storage lub Usługi Azure Event Hubs przy użyciu ustawienia diagnostycznego.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681121"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Tworzenie ustawienia diagnostycznego do zbierania dzienników zasobów i metryk na platformie Azure

[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dzienniki aktywności platformy Azure i dzienniki zasobów, zawierają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, na których zależą. [Metryki platformy](data-platform-metrics.md) są zbierane domyślnie i zazwyczaj przechowywane w bazie danych metryk usługi Azure Monitor.

Ten artykuł zawiera szczegółowe informacje na temat tworzenia i konfigurowania ustawień diagnostycznych do wysyłania metryk platformy i dzienników platformy do różnych miejsc docelowych.

> [!IMPORTANT]
> Przed utworzeniem ustawienia diagnostycznego do zbierania dziennika aktywności należy najpierw wyłączyć wszystkie starsze konfiguracje. Zobacz [Zbieranie dziennika aktywności platformy Azure ze starszymi ustawieniami,](diagnostic-settings-legacy.md) aby uzyskać szczegółowe informacje.

Każdy zasób platformy Azure wymaga własnego ustawienia diagnostycznego, które definiuje następujące kryteria:

- Kategorie dzienników i danych metryk wysyłanych do miejsc docelowych zdefiniowanych w ustawieniu. Dostępne kategorie różnią się w zależności od typu zasobu.
- Co najmniej jedno miejsce docelowe do wysłania dzienników. Bieżące miejsca docelowe obejmują obszar roboczy usługi Log Analytics, usługę Event Hubs i usługę Azure Storage.

Pojedyncze ustawienie diagnostyczne można zdefiniować nie więcej niż jeden z każdego miejsca docelowego. Jeśli chcesz wysyłać dane do więcej niż jednego określonego typu miejsca docelowego (na przykład do dwóch różnych obszarów roboczych usługi Log Analytics), utwórz wiele ustawień. Każdy zasób może mieć do 5 ustawień diagnostycznych.

> [!NOTE]
> [Metryki platformy](metrics-supported.md) są zbierane automatycznie do [metryk usługi Azure Monitor](data-platform-metrics.md). Ustawienia diagnostyczne mogą służyć do zbierania metryk dla niektórych usług platformy Azure do dzienników usługi Azure Monitor do analizy z innymi danymi monitorowania przy użyciu [zapytań dziennika.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Miejsca docelowe

Dzienniki platformy i metryki mogą być wysyłane do miejsc docelowych w poniższej tabeli. Kliknij każdy link w poniższej tabeli, aby uzyskać szczegółowe informacje na temat wysyłania danych do tego miejsca docelowego.

| Element docelowy | Opis |
|:---|:---|
| [Obszar roboczy usługi Log Analytics](resource-logs-collect-workspace.md) | Zbieranie dzienników i metryk w obszarze roboczym usługi Log Analytics umożliwia ich analizowanie przy użyciu innych danych monitorowania zebranych przez usługę Azure Monitor przy użyciu zaawansowanych zapytań dziennika, a także w celu wykorzystania innych funkcji usługi Azure Monitor, takich jak alerty i wizualizacje. |
| [Centra zdarzeń](resource-logs-stream-event-hubs.md) | Wysyłanie dzienników i metryk do centrów zdarzeń umożliwia przesyłanie strumieniowe danych do systemów zewnętrznych, takich jak siemie największe firmy i inne rozwiązania do analizy dzienników. |
| [Konto magazynu platformy Azure](resource-logs-collect-storage.md) | Archiwizowanie dzienników i metryk do konta magazynu platformy Azure jest przydatne do inspekcji, analizy statycznej lub tworzenia kopii zapasowych. W porównaniu z dziennikami usługi Azure Monitor i obszarem roboczym usługi Log Analytics magazyn platformy Azure jest tańszy, a dzienniki mogą być przechowywane tam przez czas nieokreślony. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Tworzenie ustawień diagnostycznych w witrynie Azure portal

Ustawienia diagnostyczne można skonfigurować w witrynie Azure portal z menu Usługi Azure Monitor lub z menu zasobu.

1. Gdzie skonfigurować ustawienia diagnostyczne w witrynie Azure portal zależy od zasobu.

   - W przypadku pojedynczego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **Monitor** w menu zasobu.

        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-resource.png)

   - W przypadku co najmniej jednego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **Ustawienia** w menu Usługi Azure Monitor, a następnie kliknij zasób.

      ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-monitor.png)

   - W przypadku dziennika aktywności kliknij pozycję **Dziennik aktywności** w menu **Azure Monitor,** a następnie **w ustawieniach diagnostycznych**. Upewnij się, że wyłączysz wszystkie starsze konfiguracje dziennika aktywności. Zobacz [Wyłączanie istniejących ustawień,](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) aby uzyskać szczegółowe informacje.

        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-activity-log.png)

2. Jeśli w wybranym zasobie nie istnieją żadne ustawienia, zostanie wyświetlony monit o utworzenie ustawienia. Kliknij **pozycję Dodaj ustawienie diagnostyczne**.

   ![Dodawanie ustawień diagnostycznych - brak istniejących ustawień](media/diagnostic-settings/add-setting.png)

   Jeśli w zasobie istnieją ustawienia, zostanie wyświetlona lista ustawień już skonfigurowanych. Kliknij pozycję **Dodaj ustawienie diagnostyczne,** aby dodać nowe ustawienie, lub **ustawienie Edytuj,** aby edytować istniejące. Każde ustawienie może mieć nie więcej niż jeden z każdego typu docelowego.

   ![Dodawanie ustawień diagnostycznych — istniejące ustawienia](media/diagnostic-settings/edit-setting.png)

3. Nadaj ustawieniu nazwę, jeśli jeszcze jej nie ma.

    ![Dodaj ustawienie diagnostyczne](media/diagnostic-settings/setting-new-blank.png)

4. **Szczegóły kategorii (co rozsyłać)** — zaznacz pole wyboru dla każdej kategorii danych, które chcesz wysłać do miejsc docelowych określonych później. Lista kategorii różni się dla każdej usługi platformy Azure.

     - **AllMetrics** kieruje metryki platformy zasobu do magazynu dzienników platformy Azure, ale w formie dziennika. Te metryki są zwykle wysyłane tylko do bazy danych szeregów czasowych metryk usługi Azure Monitor. Wysyłanie ich do magazynu dzienników usługi Azure Monitor (który można przeszukiwać za pośrednictwem usługi Log Analytics) w celu zintegrowania ich z zapytaniami, które wyszukują w innych dziennikach. Ta opcja może nie być dostępna dla wszystkich typów zasobów. Gdy jest obsługiwany, [Usługa Azure Monitor obsługiwane metryki](metrics-supported.md) wyświetla, jakie metryki są zbierane dla jakich typów zasobów.

       > [!NOTE]
       > Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
       >
       > *Na przykład:* Metryka "IOReadBytes" w łańcuchu bloków można eksplorować i wykresy na poziomie na węzeł. Jednak po wyeksportowane za pomocą ustawień diagnostycznych metryki eksportowane reprezentuje jako wszystkie bajty odczytu dla wszystkich węzłów.

     - **Dzienniki** zawierają listę różnych kategorii dostępnych w zależności od typu zasobu. Sprawdź wszystkie kategorie, które chcesz przekierować do miejsca docelowego.

5. **Szczegóły miejsca docelowego** — zaznacz pole wyboru dla każdego miejsca docelowego. Po zaznaczeniu każdego pola wyboru zostaną wyświetlone opcje umożliwiające dodawanie dodatkowych informacji.

      ![Wyślij do usługi Log Analytics lub Centrum zdarzeń](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Usługa Log Analytics** — wprowadź subskrypcję i obszar roboczy.  Jeśli nie masz obszaru roboczego, musisz [go utworzyć przed kontynuowaniem](../learn/quick-create-workspace.md).

    1. **Centra zdarzeń** — określ następujące kryteria:
       - Subskrypcja, której częścią jest Centrum zdarzeń
       - Obszar nazw Centrum zdarzeń — jeśli jeszcze jej nie masz, musisz [go utworzyć](../../event-hubs/event-hubs-create.md)
       - Nazwa Centrum zdarzeń (opcjonalnie), do aby wysłać wszystkie dane. Jeśli nie określisz nazwy, centrum zdarzeń zostanie utworzone dla każdej kategorii dziennika. Jeśli wysyłasz wiele kategorii, możesz określić nazwę, aby ograniczyć liczbę utworzonych centrów zdarzeń. Szczegółowe informacje można znaleźć w [przypadku przydziałów i limitów usługi Azure Event Hubs.](../../event-hubs/event-hubs-quotas.md)
       - Zasady Centrum zdarzeń (opcjonalnie) Zasady definiuje uprawnienia, które ma mechanizm przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Magazyn** — wybierz zasady subskrypcji, magazynu i przechowywania.

        ![Wyślij do magazynu](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Należy rozważyć ustawienie zasad przechowywania na 0 i ręcznie usunięcie danych z magazynu przy użyciu zaplanowanego zadania, aby uniknąć ewentualnych nieporozumień w przyszłości.
        >
        > Po pierwsze, jeśli używasz pamięci masowej do archiwizacji, zazwyczaj chcesz, aby twoje dane były dostępne przez ponad 365 dni. Po drugie, jeśli wybierzesz zasady przechowywania, która jest większa niż 0, data wygaśnięcia jest dołączony do dzienników w czasie przechowywania. Nie można zmienić daty tych dzienników po zapisaniu.
        >
        > Na przykład jeśli ustawisz zasady przechowywania *dla WorkflowRuntime* na 180 dni, a następnie 24 godziny później ustawisz go na 365 dni, dzienniki przechowywane w ciągu tych pierwszych 24 godzin zostaną automatycznie usunięte po 180 dniach, podczas gdy wszystkie kolejne dzienniki tego typu zostaną automatycznie usunięte po 365 dniach. Późniejsza zmiana zasad przechowywania nie sprawia, że pierwsze 24 godziny dzienników pozostaną w pobliżu przez 365 dni.

6. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie pojawi się na liście ustawień dla tego zasobu, a dzienniki są przesyłane strumieniowo do określonych miejsc docelowych podczas generowania nowych danych zdarzeń. Może upłynąć do 15 minut między emitowaniem zdarzenia a [pojawia się w obszarze roboczym usługi Log Analytics.](data-ingestion-time.md)

## <a name="create-diagnostic-settings-using-powershell"></a>Tworzenie ustawień diagnostycznych przy użyciu programu PowerShell

Użyj polecenia cmdlet [Set-AzDiagnosticSetting,](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) aby utworzyć ustawienie diagnostyczne za pomocą [programu Azure PowerShell](powershell-quickstart-samples.md). Opisy jego parametrów można znaleźć w dokumentacji tego polecenia cmdlet.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj [tworzenie ustawień diagnostycznych w usłudze Azure Monitor przy użyciu szablonu Menedżera zasobów,](diagnostic-settings-template.md) aby utworzyć szablon Menedżera zasobów i wdrożyć go za pomocą programu PowerShell.

Poniżej przedstawiono przykład polecenia cmdlet programu PowerShell, aby utworzyć ustawienie diagnostyczne przy użyciu wszystkich trzech miejsc docelowych.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>Tworzenie ustawień diagnostycznych przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [tworzenia ustawień diagnostycznych az monitora,](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) aby utworzyć ustawienie diagnostyczne za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)CLI . Opisy jego parametrów można znaleźć w dokumentacji tego polecenia.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj [tworzenie ustawień diagnostycznych w usłudze Azure Monitor przy użyciu szablonu Menedżera zasobów,](diagnostic-settings-template.md) aby utworzyć szablon Menedżera zasobów i wdrożyć go w celu utworzenia interfejsu wiersza polecenia.

Poniżej przedstawiono przykładowe polecenie interfejsu wiersza polecenia, aby utworzyć ustawienie diagnostyczne przy użyciu wszystkich trzech miejsc docelowych.

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

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurowanie ustawień diagnostycznych przy użyciu interfejsu REST API

Zobacz [Ustawienia diagnostyczne,](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) aby utworzyć lub zaktualizować ustawienia diagnostyczne przy użyciu [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurowanie ustawień diagnostycznych przy użyciu szablonu Menedżera zasobów

Zobacz [Tworzenie ustawień diagnostycznych w usłudze Azure Monitor przy użyciu szablonu Menedżera zasobów](diagnostic-settings-template.md) w celu utworzenia lub zaktualizowania ustawień diagnostycznych za pomocą szablonu Menedżera zasobów.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o dziennikach platformy Azure](platform-logs-overview.md)
