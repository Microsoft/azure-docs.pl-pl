---
title: Tworzenie ustawień diagnostycznych w celu wysyłania metryk i dzienników platformy do różnych miejsc docelowych
description: Wysyłaj Azure Monitor i dzienniki platformy do dzienników Azure Monitor, magazynu platformy Azure lub Azure Event Hubs przy użyciu ustawienia diagnostycznego.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 60ac56cfda026871afa1725bbd54625b7ce7585e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789199"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Tworzenie ustawień diagnostycznych w celu wysyłania metryk i dzienników platformy do różnych miejsc docelowych
[Dzienniki platformy na](./platform-logs-overview.md) platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zawierają szczegółowe informacje diagnostyczne i inspekcji dotyczące zasobów platformy Azure i platformy Azure, od której zależą. [Metryki platformy są](./data-platform-metrics.md) zbierane domyślnie i zwykle przechowywane w Azure Monitor metryk. Ten artykuł zawiera szczegółowe informacje dotyczące tworzenia i konfigurowania ustawień diagnostycznych w celu wysyłania metryk platformy i dzienników platformy do różnych miejsc docelowych.

> [!IMPORTANT]
> Przed utworzeniem ustawienia diagnostycznego dla dziennika aktywności należy najpierw wyłączyć wszystkie starsze konfiguracje. Aby uzyskać [szczegółowe informacje, zobacz Metody starszej](../essentials/activity-log.md#legacy-collection-methods) kolekcji.

Każdy zasób platformy Azure wymaga własnego ustawienia diagnostycznego, które definiuje następujące kryteria:

- Kategorie dzienników i danych metryk wysyłanych do miejsc docelowych zdefiniowanych w ustawieniu. Dostępne kategorie różnią się w zależności od typu zasobu.
- Co najmniej jedno miejsce docelowe do wysłania dzienników. Bieżące miejsca docelowe obejmują obszar roboczy usługi Log Analytics, usługę Event Hubs i usługę Azure Storage.

Pojedyncze ustawienie diagnostyczne może definiować nie więcej niż jedną z miejsc docelowych. Jeśli chcesz wysyłać dane do więcej niż jednego określonego typu miejsca docelowego (na przykład do dwóch różnych obszarów roboczych usługi Log Analytics), utwórz wiele ustawień. Każdy zasób może mieć do 5 ustawień diagnostycznych.

Poniższy film wideo pokazuje dzienniki platformy routingu z ustawieniami diagnostycznym.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [Metryki platformy są](./metrics-supported.md) wysyłane automatycznie do Azure Monitor [Metryki.](./data-platform-metrics.md) Ustawienia diagnostyczne mogą służyć do wysyłania metryk dla niektórych usług platformy Azure do [](../logs/log-query-overview.md) dzienników Azure Monitor do analizy z innymi danymi monitorowania przy użyciu zapytań dzienników z pewnymi ograniczeniami. 
>  
>  
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach. *Na przykład:* metrykę "IOReadBytes" dla łańcucha bloków można eksplorować i wykresować na poziomie węzła. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych wyeksportowana metryka reprezentuje jako wszystkie bajty odczytu dla wszystkich węzłów. Ponadto ze względu na ograniczenia wewnętrzne nie wszystkie metryki można eksportować do dzienników Azure Monitor /Log Analytics. Aby uzyskać więcej informacji, zobacz [listę metryk, które można eksportować.](./metrics-supported-export-diagnostic-settings.md) 
>  
>  
> Aby ominąć te ograniczenia dotyczące określonych metryk, zalecamy ich ręczne wyodrębnianie przy użyciu interfejsu [API REST](/rest/api/monitor/metrics/list) metryk i importowanie ich do dzienników usługi Azure Monitor przy użyciu interfejsu API modułu zbierającego dane usługi [Azure Monitor.](../logs/data-collector-api.md)  


## <a name="destinations"></a>Miejsca docelowe
Dzienniki i metryki platformy można wysyłać do miejsc docelowych w poniższej tabeli. 

| Element docelowy | Opis |
|:---|:---|
| [Obszar roboczy usługi Log Analytics](../logs/design-logs-deployment.md) | Wysyłanie dzienników i metryk do obszaru roboczego usługi Log Analytics umożliwia ich analizowanie przy użyciu innych danych monitorowania zbieranych przez usługę Azure Monitor przy użyciu zaawansowanych zapytań dotyczących dzienników, a także korzystanie z innych funkcji usługi Azure Monitor, takich jak alerty i wizualizacje. |
| [Centra zdarzeń](../../event-hubs/index.yml) | Wysyłanie dzienników i metryk do Event Hubs umożliwia przesyłanie strumieniowe danych do systemów zewnętrznych, takich jak rozwiązania SIEM innych firm i inne rozwiązania analizy dzienników.  |
| [Konto usługi Azure Storage](../../storage/blobs/index.yml) | Archiwizowanie dzienników i metryk na koncie usługi Azure Storage jest przydatne w przypadku inspekcji, analizy statycznej lub tworzenia kopii zapasowej. W porównaniu z Azure Monitor i obszarem roboczym usługi Log Analytics usługa Azure Storage jest tańsza, a dzienniki mogą być tam przechowywane przez czas nieokreślony.  |


### <a name="destination-requirements"></a>Wymagania dotyczące miejsca docelowego

Wszystkie miejsca docelowe dla ustawienia diagnostycznego należy utworzyć przed utworzeniem ustawień diagnostycznych. Miejsce docelowe nie musi być w tej samej subskrypcji co zasób wysyłający dzienniki, o ile użytkownik konfigurujący to ustawienie ma odpowiedni dostęp RBAC platformy Azure do obu subskrypcji. W poniższej tabeli przedstawiono unikatowe wymagania dla każdego miejsca docelowego, w tym ograniczenia regionalne.

| Element docelowy | Wymagania |
|:---|:---|
| Obszar roboczy usługi Log Analytics | Obszar roboczy nie musi być w tym samym regionie co monitorowany zasób.|
| Usługa Event Hubs | Zasady dostępu współdzielone dla przestrzeni nazw definiują uprawnienia, które ma mechanizm przesyłania strumieniowego. Przesyłanie strumieniowe do Event Hubs wymaga uprawnień do zarządzania, wysyłania i nasłuchiwać. Aby zaktualizować ustawienie diagnostyczne w celu dołączyć przesyłanie strumieniowe, musisz mieć uprawnienie ListKey dla tej Event Hubs autoryzacji.<br><br>Przestrzeń nazw centrum zdarzeń musi być w tym samym regionie co monitorowany zasób, jeśli zasób jest regionalny. |
| Konto magazynu Azure | Nie należy używać istniejącego konta magazynu, które ma przechowywane inne, nie monitorujące dane, aby można było lepiej kontrolować dostęp do danych. Jeśli jednak archiwizowasz dziennik aktywności i dzienniki zasobów razem, możesz użyć tego samego konta magazynu, aby zachować wszystkie dane monitorowania w centralnej lokalizacji.<br><br>Aby wysłać dane do magazynu niezmiennego, ustaw niezmienne zasady dla konta magazynu zgodnie z opisem w tece Ustawianie zasad niezmienności dla usługi Blob Storage i zarządzanie [nimi.](../../storage/blobs/storage-blob-immutability-policies-manage.md) Należy wykonać wszystkie kroki opisane w tym artykule, w tym włączyć chronione uzupełnialne obiekty blob.<br><br>Konto magazynu musi być w tym samym regionie co monitorowany zasób, jeśli zasób jest regionalny. |

> [!NOTE]
> Konta usługi Azure Data Lake Storage Gen2 nie są obecnie obsługiwane jako miejsce docelowe dla ustawień diagnostycznych, mimo że mogą być wymienione jako prawidłowa opcja w witrynie Azure Portal.

> [!NOTE]
> Azure Monitor (ustawienia diagnostyczne) nie mogą uzyskać dostępu do Event Hubs, gdy sieci wirtualne są włączone. Należy włączyć ustawienie Zezwalaj zaufanym usługi firmy Microsoft na obejście tego ustawienia zapory w centrum zdarzeń, aby usługa Azure Monitor (ustawienia diagnostyczne) uzyskać dostęp do zasobów Event Hubs danych. 


## <a name="create-in-azure-portal"></a>Tworzenie w witrynie Azure Portal

Ustawienia diagnostyczne można skonfigurować w Azure Portal z menu Azure Monitor lub z menu zasobu.

1. Miejsce konfigurowania ustawień diagnostycznych w Azure Portal zależy od zasobu.

   - W przypadku pojedynczego zasobu kliknij pozycję **Ustawienia diagnostyczne** w **obszarze Monitoruj** w menu zasobu.

        ![Zrzut ekranu przedstawiający sekcję Monitorowanie menu zasobów w menu Azure Portal wyróżnione ustawienia diagnostyczne.](media/diagnostic-settings/menu-resource.png)

   - W przypadku co najmniej jednego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze Ustawienia **Azure Monitor** menu, a następnie kliknij zasób.

        ![Zrzut ekranu przedstawiający sekcję Ustawienia w menu Azure Monitor z wyróżnione ustawienia diagnostyczne.](media/diagnostic-settings/menu-monitor.png)

   - W przypadku dziennika aktywności kliknij pozycję **Dziennik aktywności** w menu **Azure Monitor** a następnie pozycję **Ustawienia diagnostyczne.** Upewnij się, że wszystkie starsze konfiguracje dziennika aktywności są wyłączone. Aby [uzyskać szczegółowe informacje, zobacz Wyłączanie](./activity-log.md#legacy-collection-methods) istniejących ustawień.

        ![Zrzut ekranu przedstawiający menu Azure Monitor z wybranym dziennikiem aktywności i wyróżnione ustawienia diagnostyczne na pasku Monitor-Activity dziennika aktywności.](media/diagnostic-settings/menu-activity-log.png)

2. Jeśli dla wybranego zasobu nie istnieją żadne ustawienia, zostanie wyświetlony monit o utworzenie ustawienia. Kliknij **pozycję Dodaj ustawienie diagnostyczne.**

   ![Dodawanie ustawienia diagnostycznego — brak istniejących ustawień](media/diagnostic-settings/add-setting.png)

   Jeśli istnieją ustawienia zasobu, zostanie wyświetlona lista ustawień, które zostały już skonfigurowane. Kliknij pozycję **Dodaj ustawienie diagnostyczne,** aby dodać nowe ustawienie, lub pozycję **Edytuj ustawienie,** aby edytować istniejące. Każde ustawienie może mieć nie więcej niż jeden z typów docelowych.

   ![Dodawanie ustawienia diagnostycznego — istniejące ustawienia](media/diagnostic-settings/edit-setting.png)

3. Nadaj nazwę ustawieniu, jeśli jeszcze go nie ma.

    ![Dodawanie ustawienia diagnostycznego](media/diagnostic-settings/setting-new-blank.png)

4. **Szczegóły kategorii (co należy rozsyłać)** — zaznacz pole wyboru dla każdej kategorii danych, które chcesz wysłać do określonych później miejsc docelowych. Lista kategorii różni się dla każdej usługi platformy Azure.

     - **AllMetrics** kieruje metryki platformy zasobu do magazynu dzienników platformy Azure, ale w postaci dziennika. Te metryki są zwykle wysyłane tylko do bazy Azure Monitor danych szeregów czasowych metryk. Wysyłanie ich do magazynu dzienników Azure Monitor (który można przeszukiwać za pośrednictwem usługi Log Analytics) ułatwia ich integrację z zapytaniami, które wyszukują inne dzienniki. Ta opcja może nie być dostępna dla wszystkich typów zasobów. Gdy ta metryka jest [obsługiwana, Azure Monitor metryki](./metrics-supported.md) wyświetla, jakie metryki są zbierane dla jakich typów zasobów.

       > [!NOTE]
       > Zobacz ograniczenie dotyczące routingu metryk do dzienników Azure Monitor w tym artykule.  


     - **W** dziennikach są dostępne różne kategorie w zależności od typu zasobu. Sprawdź wszystkie kategorie, które mają być kierowane do miejsca docelowego.

5. **Szczegóły miejsca** docelowego — zaznacz pole wyboru dla każdego miejsca docelowego. Po zaznaczdaniu każdego pola zostaną wyświetlone opcje umożliwiające dodanie dodatkowych informacji.

      ![Wysyłanie do usługi Log Analytics lub Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** — wprowadź subskrypcję i obszar roboczy.  Jeśli nie masz obszaru roboczego, musisz go utworzyć [przed przystąpieniem do pracy.](../logs/quick-create-workspace.md)

    1. **Event Hubs** — określ następujące kryteria:
       - Subskrypcja, do której należy centrum zdarzeń
       - Przestrzeń nazw centrum zdarzeń — jeśli jeszcze jej nie masz, musisz [utworzyć przestrzeń nazw](../../event-hubs/event-hubs-create.md)
       - Nazwa centrum zdarzeń (opcjonalnie), do których mają być wysyłane wszystkie dane. Jeśli nie określisz nazwy, centrum zdarzeń zostanie utworzone dla każdej kategorii dziennika. Jeśli wysyłasz wiele kategorii, możesz określić nazwę, aby ograniczyć liczbę utworzonych centrów zdarzeń. Zobacz [Azure Event Hubs limity przydziału i limity,](../../event-hubs/event-hubs-quotas.md) aby uzyskać szczegółowe informacje.
       - Zasady centrum zdarzeń (opcjonalnie) Zasady definiują uprawnienia, które ma mechanizm przesyłania strumieniowego. Aby uzyskać więcej informacji, [zobacz Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Magazyn** — wybierz subskrypcję, konto magazynu i zasady przechowywania.

        ![Wysyłanie do magazynu](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Rozważ ustawienie zasad przechowywania na 0 i ręczne usunięcie danych z magazynu przy użyciu zaplanowanego zadania, aby uniknąć nieporozumień w przyszłości.
        >
        > Po pierwsze, jeśli używasz magazynu do archiwizowania, zwykle potrzebujesz danych przez ponad 365 dni. Po drugie, jeśli wybierzesz zasady przechowywania większe niż 0, data wygaśnięcia zostanie dołączona do dzienników w czasie przechowywania. Nie można zmienić daty przechowywania tych dzienników.
        >
        > Jeśli na przykład ustawisz zasady przechowywania dla wartości *WorkflowRuntime* na 180 dni, a następnie 24 godziny później ustawisz ją na 365 dni, dzienniki przechowywane w ciągu tych pierwszych 24 godzin zostaną automatycznie usunięte po 180 dniach, podczas gdy wszystkie kolejne dzienniki tego typu zostaną automatycznie usunięte po 365 dniach. Zmiana zasad przechowywania później nie sprawi, że pierwsze 24 godziny dzienników będą pozostawać w okolicy 365 dni.

6. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla tego zasobu, a dzienniki będą przesyłane strumieniowo do określonych miejsc docelowych w momencie generowania nowych danych zdarzeń. Może upłynieć do 15 minut między emitem zdarzenia a jego pojawianiem się [w obszarze roboczym usługi Log Analytics.](../logs/data-ingestion-time.md)

## <a name="create-using-powershell"></a>Tworzenie za pomocą programu PowerShell

Użyj polecenia cmdlet [Set-AzDiagnosticSetting,](/powershell/module/az.monitor/set-azdiagnosticsetting) aby utworzyć ustawienie diagnostyczne za pomocą polecenia [Azure PowerShell](../powershell-samples.md). Zapoznaj się z dokumentacją tego polecenia cmdlet, aby uzyskać opisy jego parametrów.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj [polecenia Create diagnostic setting in Azure Monitor using a Resource Manager template](./resource-manager-diagnostic-settings.md) (Utwórz ustawienie diagnostyczne w programie Resource Manager szablonu), aby utworzyć szablon Resource Manager i wdrożyć go przy użyciu programu PowerShell.

Poniżej przedstawiono przykładowe polecenie cmdlet programu PowerShell służące do tworzenia ustawienia diagnostycznego przy użyciu wszystkich trzech miejsc docelowych.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Tworzenie przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [az monitor diagnostic-settings create,](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) aby utworzyć ustawienie diagnostyczne za pomocą interfejsu wiersza [polecenia platformy Azure.](/cli/azure/monitor) Zapoznaj się z dokumentacją tego polecenia, aby uzyskać opisy jego parametrów.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego należy użyć polecenia Create diagnostic setting in Azure Monitor using a Resource Manager template (Utwórz ustawienie diagnostyczne w programie Resource Manager [szablonu),](./resource-manager-diagnostic-settings.md) aby utworzyć szablon Resource Manager i wdrożyć go za pomocą interfejsu wiersza polecenia.

Poniżej przedstawiono przykładowe polecenie interfejsu wiersza polecenia służące do tworzenia ustawienia diagnostycznego przy użyciu wszystkich trzech miejsc docelowych. Składnia jest nieco różna w zależności od klienta.

# <a name="cmd"></a>[Cmd](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Tworzenie przy użyciu Resource Manager szablonu
Zobacz [Resource Manager przykłady szablonów](./resource-manager-diagnostic-settings.md) ustawień diagnostycznych w Azure Monitor, aby utworzyć lub zaktualizować ustawienia diagnostyczne przy użyciu Resource Manager szablonu.

## <a name="create-using-rest-api"></a>Tworzenie za pomocą interfejsu API REST
Zobacz [Ustawienia diagnostyczne,](/rest/api/monitor/diagnosticsettings) aby utworzyć lub zaktualizować ustawienia diagnostyczne przy użyciu [interfejsu API REST Azure Monitor .](/rest/api/monitor/)

## <a name="create-using-azure-policy"></a>Tworzenie przy użyciu Azure Policy
Ponieważ należy utworzyć ustawienie diagnostyczne dla każdego zasobu platformy Azure, Azure Policy automatycznie utworzyć ustawienie diagnostyczne podczas tworzenia każdego zasobu. Aby [uzyskać szczegółowe Azure Monitor, zobacz](../deploy-scale.md) Deploy Azure Monitor at scale using Azure Policy (Wdrażanie aplikacji na dużą skalę przy użyciu usługi Azure Policy).

## <a name="metric-category-is-not-supported-error"></a>Błąd kategoria metryki nie jest obsługiwana
Podczas wdrażania ustawienia diagnostycznego jest wyświetlany następujący komunikat o błędzie:

   "Kategoria metryki '*xxxx*' nie jest obsługiwana"

Na przykład: 

   "Kategoria metryki "ActionsFailed" nie jest obsługiwana"

gdzie wcześniej wdrożenie zakończyło się pomyślnie. 

Ten problem występuje podczas korzystania z szablonu Resource Manager, interfejsu API REST ustawień diagnostycznych, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. Ustawienia diagnostyczne utworzone za pośrednictwem Azure Portal nie mają wpływu, ponieważ prezentowane są tylko obsługiwane nazwy kategorii.

Problem jest spowodowany niedawną zmianą w bazowym interfejsie API. Kategorie metryk inne niż "AllMetrics" nie są obsługiwane i nigdy nie były wyjątkiem kilku bardzo konkretnych usług platformy Azure. W przeszłości inne nazwy kategorii były ignorowane podczas wdrażania ustawienia diagnostycznego. W Azure Monitor zaplecza po prostu przekierowano te kategorie do "AllMetrics".  Od lutego 2021 r. zaktualizowano zaplecza w celu potwierdzenia, że podana kategoria metryki jest dokładna. Ta zmiana spowodowała, że niektóre wdrożenia zakończyły się niepowodzeniem.

Jeśli wystąpi ten błąd, zaktualizuj wdrożenia, aby zastąpić nazwy kategorii metryk wartością "AllMetrics", aby rozwiązać ten problem. Jeśli wdrożenie wcześniej dodajeło wiele kategorii, należy zachować tylko jedną z odwołaniami "AllMetrics". Jeśli problem nadal występuje, skontaktuj się z pomocą techniczną platformy Azure za pośrednictwem Azure Portal. 



## <a name="next-steps"></a>Następne kroki

- [Przeczytaj więcej na temat dzienników platformy Azure](./platform-logs-overview.md)
