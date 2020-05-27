---
title: Wyświetlanie zdarzeń dziennika aktywności platformy Azure w Azure Monitor
description: Wyświetl dziennik aktywności platformy Azure w Azure Monitor i Pobierz go za pomocą programu PowerShell, interfejsu wiersza polecenia i API REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3327f0bdaff641055cf84ab205d847f0fb73bfe8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834615"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure

[Dziennik aktywności platformy Azure](platform-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Ten artykuł zawiera szczegółowe informacje dotyczące różnych metod wyświetlania i pobierania zdarzeń dziennika aktywności.

## <a name="azure-portal"></a>Azure Portal
Wyświetl dziennik aktywności dla wszystkich zasobów z menu **Monitoruj** w Azure Portal. Wyświetl dziennik aktywności dla określonego zasobu z poziomu opcji **Dziennik aktywności** w menu tego zasobu.

![Wyświetl dziennik aktywności](./media/activity-logs-overview/view-activity-log.png)

Zdarzenia dziennika aktywności można filtrować według następujących pól:

* **TimeSpan**: godzina rozpoczęcia i zakończenia dla zdarzeń.
* **Kategoria**: Kategoria zdarzenia zgodnie z opisem w [kategorii w dzienniku aktywności](activity-log-view.md#categories-in-the-activity-log).
* **Subskrypcja**: co najmniej jedna nazwa subskrypcji platformy Azure.
* **Grupa zasobów**: co najmniej jedna grupa zasobów w ramach wybranych subskrypcji.
* **Zasób (nazwa)**: — Nazwa określonego zasobu.
* **Typ zasobu**: typ zasobu, na przykład _Microsoft. COMPUTE/virtualmachines_.
* **Nazwa operacji** — nazwa operacji Azure Resource Manager, na przykład _Microsoft. SQL/Servers/Write_.
* **Ważność**: poziom ważności zdarzenia. Dostępne wartości to _informacyjny_, _ostrzegawczy_, _błąd_, _krytyczny_.
* **Zdarzenie zainicjowane przez**: użytkownik, który wykonał operację.
* **Otwórz wyszukiwanie**: Otwórz pole wyszukiwania tekstu, które wyszukuje ten ciąg we wszystkich polach wszystkich zdarzeń.

## <a name="categories-in-the-activity-log"></a>Kategorie w dzienniku aktywności
Każde zdarzenie w dzienniku aktywności ma określoną kategorię, która została opisana w poniższej tabeli. Aby uzyskać szczegółowe informacje dotyczące schematu tych kategorii, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md). 

| Kategoria | Opis |
|:---|:---|
| Administracyjne | Zawiera rekord wszystkich operacji tworzenia, aktualizowania, usuwania i akcji wykonywanych za pomocą Menedżer zasobów. Przykłady zdarzeń administracyjnych obejmują _Utwórz maszynę wirtualną_ i _Usuń sieciową grupę zabezpieczeń_.<br><br>Każda Akcja podejmowana przez użytkownika lub aplikację przy użyciu Menedżer zasobów jest modelowana jako operacja dla określonego typu zasobu. Jeśli typem operacji jest _zapis_, _usuwanie_lub _Akcja_, rekordy zarówno rozpoczęcia, jak i sukcesu lub niepowodzenia tej operacji są rejestrowane w kategorii administracyjnej. Zdarzenia administracyjne zawierają również wszelkie zmiany w ramach kontroli dostępu opartej na rolach w ramach subskrypcji. |
| Service Health | Zawiera rekord wszystkich zdarzeń związanych z kondycją usług, które wystąpiły na platformie Azure. Przykładem zdarzenia Service Health _SQL Azure w regionie Wschodnie stany USA występuje przestój_. <br><br>Service Health zdarzenia są dostępne w sześciu odmianach: _wymagane działanie_, _pomocne odzyskiwanie_, _incydent_, _konserwacja_, _informacje_lub _zabezpieczenia_. Te zdarzenia są tworzone tylko wtedy, gdy w subskrypcji znajduje się zasób, na który wpłynie zdarzenie.
| Kondycja zasobów | Zawiera rekord wszystkich zdarzeń związanych z kondycją zasobów, które wystąpiły w Twoich zasobach platformy Azure. Przykładem zdarzenia Resource Health jest _stan kondycji maszyny wirtualnej zmieniony na niedostępny_.<br><br>Zdarzenia Resource Health mogą reprezentować jeden z czterech stanów kondycji: _dostępne_, _niedostępne_, _obniżone_i _nieznane_. Ponadto zdarzenia Resource Health mogą być kategoryzowane jako _zainicjowane przez platformę_ lub _zainicjowane przez użytkownika_. |
| Alerty | Zawiera rekord aktywacji dla alertów platformy Azure. Przykładem zdarzenia alertu jest _użycie procesora CPU w systemie 80 myVM w ciągu ostatnich 5 minut_.|
| Automatyczne skalowanie | Zawiera rekord wszystkich zdarzeń związanych z działaniem aparatu skalowania automatycznego na podstawie wszelkich ustawień automatycznego skalowania zdefiniowanych w ramach subskrypcji. Przykładem zdarzenia automatycznego skalowania jest _Akcja skalowania automatycznego w górę_. |
| Zalecenie | Zawiera zdarzenia rekomendacji z Azure Advisor. |
| Zabezpieczenia | Zawiera rekord wszystkich alertów wygenerowanych przez Azure Security Center. Przykład zdarzenia zabezpieczeń to _podejrzany plik o podwójnym rozszerzeniu_. |
| Zasady | Zawiera rekordy wszystkich operacji akcji wykonywanych przez Azure Policy. Przykłady zdarzeń zasad obejmują _inspekcję_ i _odmowę_. Wszystkie akcje podejmowane przez zasady są modelowane jako operacje na zasobach. |

## <a name="view-change-history"></a>Wyświetl historię zmian

Podczas przeglądania dziennika aktywności może pomóc w sprawdzaniu, jakie zmiany wystąpiły w czasie tego zdarzenia. Te informacje można wyświetlić za pomocą **historii zmian**. Wybierz zdarzenie z dziennika aktywności, do którego chcesz się zapoznać. Wybierz kartę **historia zmian (wersja zapoznawcza)** , aby wyświetlić wszystkie skojarzone zmiany z tym zdarzeniem.

![Zmień listę historii dla zdarzenia](media/activity-logs-overview/change-history-event.png)

Jeśli ze zdarzeniem są skojarzone jakiekolwiek zmiany, zostanie wyświetlona lista zmian, które można wybrać. Spowoduje to otwarcie strony **historia zmian (wersja zapoznawcza)** . Na tej stronie są widoczne zmiany w zasobie. Jak widać w poniższym przykładzie, można zobaczyć nie tylko zmiany rozmiarów maszyny wirtualnej, ale informacje o poprzednim rozmiarze maszyny wirtualnej były przed zmianą i zmianami w nim.

![Strona historii zmian przedstawiająca różnice](media/activity-logs-overview/change-history-event-details.png)

Aby dowiedzieć się więcej na temat historii zmian, zobacz [pobieranie zmian zasobów](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Użyj polecenia cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) , aby pobrać dziennik aktywności z programu PowerShell. Poniżej przedstawiono kilka typowych przykładów.

> [!NOTE]
> `Get-AzLog`zawiera tylko 15 dni historii. Użyj parametru **-MaxRecord** , aby wykonać zapytanie o ostatnie N zdarzeń poza 15 dni. Aby uzyskać dostęp do zdarzeń starszych niż 15 dni, należy użyć interfejsu API REST lub zestawu SDK. Jeśli nie dołączysz wartości **StartTime**, wartość domyślna to **Endtime** minus jedna godzina. Jeśli nie dołączysz **Endtime**, wartość domyślna to bieżąca godzina. Wszystkie czasy są w formacie UTC.


Pobierz wpisy dziennika utworzone po upływie określonego czasu:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Pobierz wpisy dziennika między zakresem daty i godziny:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika z określonej grupy zasobów:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Pobierz wpisy dziennika od określonego dostawcy zasobów między zakresem daty i godziny:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika z określonym obiektem wywołującym:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Pobierz ostatnie zdarzenia 1000:

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>Interfejs wiersza polecenia
Użyj polecenia [AZ monitor Activity-Log](../samples/cli-samples.md#view-activity-log-for-a-subscription) , aby pobrać dziennik aktywności z interfejsu CLI. Poniżej przedstawiono kilka typowych przykładów.


Wyświetl wszystkie dostępne opcje.

```azurecli
az monitor activity-log list -h
```

Pobierz wpisy dziennika z określonej grupy zasobów:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Pobierz wpisy dziennika z określonym obiektem wywołującym:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Pobierz dzienniki według obiektu wywołującego dla typu zasobu w ramach zakresu dat:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>Interfejs API REST
Użyj [interfejsu API REST Azure monitor](https://docs.microsoft.com/rest/api/monitor/) , aby pobrać dziennik aktywności z klienta Rest. Poniżej przedstawiono kilka typowych przykładów.

Pobierz dzienniki aktywności z filtrem:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Pobierz dzienniki aktywności z filtrem i wybierz:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Pobierz dzienniki aktywności przy użyciu funkcji Select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Pobierz dzienniki aktywności bez filtrowania lub wybierz:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z omówieniem dzienników platformy](platform-logs-overview.md)
* [Utwórz ustawienie diagnostyczne, aby wysyłać dzienniki aktywności do innych miejsc docelowych](diagnostic-settings.md)
