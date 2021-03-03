---
title: Zbieranie źródeł danych wydajności systemów Windows i Linux za pomocą agenta Log Analytics w programie Azure Monitor
description: Liczniki wydajności są zbierane przez Azure Monitor w celu przeanalizowania wydajności agentów systemu Windows i Linux.  W tym artykule opisano sposób konfigurowania kolekcji liczników wydajności dla agentów systemu Windows i Linux. szczegóły są przechowywane w obszarze roboczym i sposób analizowania ich w Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: c06123b33c7f467e12742cf6180d821e647b5115
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711556"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Zbieranie źródeł danych wydajności systemów Windows i Linux za pomocą agenta Log Analytics
Liczniki wydajności w systemach Windows i Linux zapewniają wgląd w wydajność składników sprzętowych, systemów operacyjnych i aplikacji.  Azure Monitor może zbierać liczniki wydajności z agentów Log Analytics w częstych odstępach czasu dla analizy niemal w czasie rzeczywistym (NRT), a także do agregowania danych wydajności na potrzeby analizy i raportowania w dłuższym okresie.

> [!IMPORTANT]
> W tym artykule opisano zbieranie danych wydajności za pomocą [agenta log Analytics](./log-analytics-agent.md) , który jest jednym z agentów używanych przez Azure monitor. Inni Agenci zbierają różne dane i są skonfigurowani inaczej. Zobacz [Omówienie agentów Azure monitor](../agents/agents-overview.md) , aby uzyskać listę dostępnych agentów oraz dane, które mogą zbierać.

![Liczniki wydajności](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności
Skonfiguruj liczniki wydajności z [menu dane w obszarze Ustawienia zaawansowane](../agents/agent-data-sources.md#configuring-data-sources) dla obszaru roboczego log Analytics.

Podczas pierwszej konfiguracji liczników wydajności systemu Windows lub Linux dla nowego obszaru roboczego można szybko utworzyć kilka typowych liczników.  Na liście obok każdego z nich znajduje się pole wyboru.  Upewnij się, że wszystkie liczniki, które chcesz utworzyć, są zaznaczone, a następnie kliknij przycisk **Dodaj wybrane liczniki wydajności**.

W przypadku liczników wydajności systemu Windows można wybrać określone wystąpienie dla każdego licznika wydajności. W przypadku liczników wydajności systemu Linux wystąpienie każdego wybranego licznika ma zastosowanie do wszystkich liczników podrzędnych licznika nadrzędnego. W poniższej tabeli przedstawiono typowe wystąpienia, które są dostępne dla liczników wydajności systemu Linux i Windows.

| Nazwa wystąpienia | Opis |
| --- | --- |
| \_Łącznie |Suma wszystkich wystąpień |
| \* |Wszystkie wystąpienia |
| (/&#124;/var) |Dopasowuje wystąpienia o nazwie:/lub/var |

### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows

![Konfigurowanie liczników wydajności systemu Windows](media/data-sources-performance-counters/configure-windows.png)

Postępuj zgodnie z tą procedurą, aby dodać nowy licznik wydajności systemu Windows do zbierania danych. Należy pamiętać, że liczniki wydajności systemu Windows w wersji 2 nie są obsługiwane.

1. Wpisz nazwę licznika w polu tekstowym w *obiekcie format (wystąpienie) \Counter*.  Po rozpoczęciu wpisywania zostanie wyświetlona zgodna lista typowych liczników.  Możesz wybrać licznik z listy lub wpisać własny.  Możesz również zwrócić wszystkie wystąpienia dla określonego licznika, określając *object\counter*.  

    Podczas zbierania SQL Server liczników wydajności z nazwanych wystąpień wszystkie liczniki nazwanego wystąpienia zaczynają się od *MSSQL $* i po nim nazwa wystąpienia.  Na przykład, aby zebrać licznik Współczynnik trafień pamięci podręcznej dzienników dla wszystkich baz danych z obiektu wydajności bazy danych dla nazwanego wystąpienia SQL INST2, określ `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` .

2. Kliknij **+** lub naciśnij klawisz **Enter** , aby dodać licznik do listy.
3. Po dodaniu licznika zostanie użyta wartość domyślna wynosząca 10 sekund dla **interwału próbkowania**.  Można zmienić tę wartość na wyższą niż 1800 sekund (30 minut), jeśli chcesz zmniejszyć wymagania dotyczące magazynu zebranych danych wydajności.
4. Po zakończeniu dodawania liczników kliknij przycisk **Zapisz** znajdujący się u góry ekranu, aby zapisać konfigurację.

### <a name="linux-performance-counters"></a>Liczniki wydajności systemu Linux

![Konfigurowanie liczników wydajności systemu Linux](media/data-sources-performance-counters/configure-linux-1.png)

Postępuj zgodnie z tą procedurą, aby dodać nowy licznik wydajności systemu Linux do zebrania.

1. Wpisz nazwę licznika w polu tekstowym w *obiekcie format (wystąpienie) \Counter*.  Po rozpoczęciu wpisywania zostanie wyświetlona zgodna lista typowych liczników.  Możesz wybrać licznik z listy lub wpisać własny.  
1. Kliknij **+** lub naciśnij klawisz **Enter** , aby dodać licznik do listy innych liczników dla obiektu.
1. Wszystkie liczniki dla obiektu używają tego samego **interwału próbkowania**.  Wartość domyślna to 10 sekund.  Tę zmianę można zmienić na maksymalnie 1800 sekund (30 minut), jeśli chcesz zmniejszyć wymagania dotyczące magazynu zebranych danych wydajności.
1. Po zakończeniu dodawania liczników kliknij przycisk **Zapisz** znajdujący się u góry ekranu, aby zapisać konfigurację.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurowanie liczników wydajności systemu Linux w pliku konfiguracyjnym
Zamiast konfigurować liczniki wydajności systemu Linux przy użyciu Azure Portal istnieje możliwość edytowania plików konfiguracji w agencie systemu Linux.  Metryki wydajności do zebrania są kontrolowane przez konfigurację w **/etc/opt/Microsoft/omsagent/ \<workspace id\> /conf/omsagent.conf**.

Każdy obiekt lub kategoria metryk wydajności do zebrania należy zdefiniować w pliku konfiguracji jako pojedynczy `<source>` element. Składnia jest zgodna z wzorcem poniżej.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


Parametry w tym elemencie są opisane w poniższej tabeli.

| Parametry | Opis |
|:--|:--|
| \_Nazwa obiektu | Nazwa obiektu dla kolekcji. |
| \_wyrażenie regularne wystąpienia |  *Wyrażenie regularne* definiujące, które wystąpienia mają być zbierane. Wartość: `.*` określa wszystkie wystąpienia. Aby zbierać metryki procesora tylko dla \_ wystąpienia całkowitego, można określić `_Total` . Aby zbierać metryki procesów tylko dla wystąpień z identyfikatorem "lub" SSHD, można określić: `(crond\|sshd)` . |
| \_ \_ wyrażenie regularne nazwy licznika | *Wyrażenie regularne* definiujące, które liczniki (dla obiektu) mają być zbierane. Aby zebrać wszystkie liczniki dla obiektu, określ: `.*` . Aby zebrać tylko liczniki przestrzeni wymiany dla obiektu pamięci, można na przykład określić: `.+Swap.+` |
| interval | Częstotliwość, z jaką są zbierane liczniki obiektu. |


W poniższej tabeli wymieniono obiekty i liczniki, które można określić w pliku konfiguracji.  Dostępne są dodatkowe liczniki dla niektórych aplikacji, zgodnie z opisem w temacie [Zbieranie liczników wydajności dla aplikacji systemu Linux w Azure monitor](data-sources-linux-applications.md).

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Dysk logiczny | % Wolnego węzłów i |
| Dysk logiczny | Wolne miejsce (%) |
| Dysk logiczny | % Użytych węzłów i |
| Dysk logiczny | Zajęte miejsce (%) |
| Dysk logiczny | Bajty odczytu dysku/s |
| Dysk logiczny | Odczyty dysku/s |
| Dysk logiczny | Transfery dysku/s |
| Dysk logiczny | Bajty zapisu dysku/s |
| Dysk logiczny | Zapisy dysku/s |
| Dysk logiczny | Wolne megabajty |
| Dysk logiczny | Bajty dysku logicznego/s |
| Pamięć | Dostępna pamięć (%) |
| Pamięć | Dostępny obszar wymiany (%) |
| Pamięć | Używana pamięć (%) |
| Pamięć | Używany obszar wymiany (%) |
| Pamięć | Dostępna pamięć (MB) |
| Pamięć | Dostępny obszar wymiany (MB) |
| Pamięć | Odczyty stron/s |
| Pamięć | Zapisy stron/s |
| Pamięć | Stron/s |
| Pamięć | Używany obszar wymiany (MB) |
| Pamięć | Używana pamięć (MB) |
| Sieć | Całkowita liczba przesłanych bajtów |
| Sieć | Całkowita liczba odebranych bajtów |
| Sieć | Łączna liczba bajtów |
| Sieć | Całkowita liczba przesłanych pakietów |
| Sieć | Całkowita liczba odebranych pakietów |
| Sieć | Całkowita liczba błędów odbierania |
| Sieć | Całkowita liczba błędów transmisji |
| Sieć | Łączna liczba kolizji |
| Dysk fizyczny | Średni czas dysku w s/odczyt |
| Dysk fizyczny | Średni czas dysku w s/transfer |
| Dysk fizyczny | Średni czas dysku w s/zapis |
| Dysk fizyczny | Bajty dysku fizycznego/s |
| Proces | Czas uprzywilejowany PCT |
| Proces | Czas użytkownika PCT |
| Proces | Używana pamięć (w kilobajtach) |
| Proces | Wirtualna pamięć udostępniona |
| Procesor | Czas DPC (%) |
| Procesor | Czas bezczynności (%) |
| Procesor | Czas przerwań (%) |
| Procesor | Czas oczekiwania operacji we/wy (%) |
| Procesor | % Całkiem czasu |
| Procesor | Czas uprzywilejowany (%) |
| Procesor | Czas procesora (%) |
| Procesor | Czas użytkownika (%) |
| System | Wolna pamięć fizyczna |
| System | Wolne miejsce w plikach stronicowania |
| System | Wolna pamięć wirtualna |
| System | Procesy |
| System | Rozmiar zapisany w plikach stronicowania |
| System | Czas pracy |
| System | Użytkownicy |


Poniżej przedstawiono domyślną konfigurację metryk wydajności.

```xml
<source>
    type oms_omi
    object_name "Physical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Logical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>

<source>
    type oms_omi
    object_name "Memory"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```

## <a name="data-collection"></a>Zbieranie danych
Azure Monitor zbiera wszystkie określone liczniki wydajności w określonym interwale próbkowania na wszystkich agentach, na których zainstalowano ten licznik.  Dane nie są agregowane i dane pierwotne są dostępne we wszystkich widokach zapytania dziennika przez czas trwania określony przez obszar roboczy usługi log Analytics.

## <a name="performance-record-properties"></a>Właściwości rekordu wydajności
Rekordy wydajności mają typ **wydajności** i mają właściwości opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Komputer, z którego zostało zebrane zdarzenie. |
| CounterName |Nazwa licznika wydajności |
| CounterPath |Pełna ścieżka licznika w \\ \\ \<Computer> \\ obiekcie form (wystąpienie) \\ . |
| CounterValue |Wartość liczbowa licznika. |
| InstanceName |Nazwa wystąpienia zdarzenia.  Puste, jeśli żadne wystąpienie nie jest. |
| ObjectName |Nazwa obiektu wydajności |
| SourceSystem |Typ agenta, z którego zostały zebrane dane. <br><br>OpsManager — Agent systemu Windows, bezpośrednie połączenie lub SCOM <br> Linux — Wszyscy agenci systemu Linux  <br> AzureStorage — Diagnostyka Azure |
| TimeGenerated |Data i godzina próbkowania danych. |

## <a name="sizing-estimates"></a>Ustalanie wielkości oszacowań
 Przybliżone oszacowanie dla zbierania określonego licznika w 10-sekundowych odstępach wynosi około 1 MB dziennie na wystąpienie.  Wymagania dotyczące magazynu określonego licznika można oszacować przy użyciu następującej formuły.

> 1 MB x (liczba liczników) x (liczba agentów) x (liczba wystąpień)

## <a name="log-queries-with-performance-records"></a>Rejestruj zapytania z rekordami wydajności
W poniższej tabeli przedstawiono różne przykłady zapytań dzienników, które pobierają rekordy wydajności.

| Zapytanie | Opis |
|:--- |:--- |
| Wyd. |Wszystkie dane dotyczące wydajności |
| &#124; wydajności, gdzie Computer = = "MójKomputer" |Wszystkie dane dotyczące wydajności z określonego komputera |
| &#124; wydajności, gdzie CounterName = = "Bieżąca długość kolejki dysku" |Wszystkie dane dotyczące wydajności dla określonego licznika |
| &#124; wydajności, gdzie ObjectName = = "Processor" i CounterName = = "% Time procesora" i InstanceName = = "_Total" &#124; podsumowuje AVGCPU = AVG (CounterValue) według komputera |Średnie użycie procesora CPU na wszystkich komputerach |
| &#124; wydajności, gdzie CounterName = = "% Time procesora" &#124; podsumowanie AggregatedValue = Max (CounterValue) według komputera |Maksymalne wykorzystanie procesora CPU na wszystkich komputerach |
| &#124; wydajności, gdzie ObjectName = = "dysk logiczny" i CounterName = = "Bieżąca długość kolejki dysku" i Computer = = "Nazwa komputera" &#124; podsumowuje AggregatedValue = średnia (CounterValue) przez InstanceName |Średnia bieżąca długość kolejki dysku we wszystkich wystąpieniach danego komputera |
| &#124; wydajności, gdzie CounterName = = "transfery dysku/s" &#124; podsumowanie AggregatedValue = percentyl (CounterValue, 95) przez komputer |używany 95. percentyl transferu dysku/s na wszystkich komputerach |
| &#124; wydajności, gdzie CounterName = = "% Time procesora" i InstanceName = = "_Total" &#124; podsumowania AggregatedValue = AVG (CounterValue) według bin (TimeGenerated, 1 godz), komputer |Średnia godzinowa użycia procesora CPU na wszystkich komputerach |
| &#124; wydajności, gdzie Computer = = "MójKomputer" i CounterName startswith_cs "%" i InstanceName = = "_Total" &#124; podsumowania AggregatedValue = percentyl (CounterValue, 70) według bin (TimeGenerated, 1 godz), CounterName | Co godzinę 70 percentyl każdego licznika% procent dla określonego komputera |
| &#124; wydajności, gdzie CounterName = = "% Time procesora" i InstanceName = = "_Total" i Computer = = "MójKomputer" &#124; podsumowania ["min (CounterValue)"] = min (CounterValue), ["AVG (CounterValue)"] = AVG (CounterValue), ["percentile75 (CounterValue)"] = percentyl (CounterValue, 75), ["Max (CounterValue)"] = Max (CounterValue) według bin (TimeGenerated, 1 godz), Computer |Średnia godzinowa, minimalna, maksymalna i 75-percentyl użycia procesora CPU dla określonego komputera |
| &#124; wydajności, gdzie ObjectName = = "MSSQL $ INST2: Databases" i InstanceName = = "Master" | Wszystkie dane dotyczące wydajności z obiektu wydajności bazy danych dla bazy danych Master z nazwanego wystąpienia SQL Server INST2.  




## <a name="next-steps"></a>Następne kroki
* [Zbieraj liczniki wydajności z aplikacji systemu Linux, w](data-sources-linux-applications.md) tym MySQL i Apache HTTP Server.
* Informacje na temat [zapytań dzienników](../logs/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.  
* Eksportuj zebrane dane do [Power BI](../visualize/powerbi.md) , aby uzyskać dodatkowe wizualizacje i analizę.