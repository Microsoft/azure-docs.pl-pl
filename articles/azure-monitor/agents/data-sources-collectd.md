---
title: Zbieranie danych z zebranych w Azure Monitor | Microsoft Docs
description: Zebrany to demon Open Source systemu Linux, który okresowo zbiera dane z aplikacji i informacji o poziomie systemu.  Ten artykuł zawiera informacje dotyczące zbierania danych z zebranych w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: 6848b4e0463be803fb4f41797d933386cb575a0c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732109"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Zbieraj dane z zebranych w agencie systemu Linux w Azure Monitor
[Zebrany](https://collectd.org/) to demon Open Source systemu Linux, który okresowo zbiera metryki wydajności aplikacji i informacji o poziomie systemu. Przykładowe aplikacje obejmują wirtualna maszyna Java (JVM), MySQL Server i Nginx. Ten artykuł zawiera informacje dotyczące zbierania danych o wydajności z zebranych w Azure Monitor.

Pełną listę dostępnych wtyczek można znaleźć w [tabeli wtyczek](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Przegląd zebranych](media/data-sources-collectd/overview.png)

Następująca zebrana konfiguracja jest uwzględniona w agencie Log Analytics dla systemu Linux w celu rozesłania zebranych danych do agenta Log Analytics dla systemu Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

Ponadto w przypadku korzystania z wersji zebranych przed 5,5 Użyj poniższej konfiguracji.

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

Zebrana konfiguracja używa domyślnej `write_http` wtyczki do wysyłania danych metryk wydajności przez port 26000 do agenta log Analytics dla systemu Linux. 

> [!NOTE]
> Ten port można skonfigurować do niestandardowego portu zdefiniowanego w razie potrzeby.

Agent Log Analytics dla systemu Linux nasłuchuje również na porcie 26000 dla zebranych metryk, a następnie konwertuje je na metryki schematu Azure Monitor. Poniżej przedstawiono konfigurację agenta Log Analytics dla systemu Linux  `collectd.conf` .

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

> [!NOTE]
> Zbierane domyślnie są ustawione na odczyt wartości w [przedziale](https://collectd.org/wiki/index.php/Interval)10 sekund. Ponieważ ma to bezpośredni wpływ na ilość danych wysyłanych do dzienników Azure Monitor, może być konieczne dostrojenie tego interwału w ramach zebranej konfiguracji w celu uzyskania odpowiedniej równowagi między wymaganiami dotyczącymi monitorowania a związanymi z nimi kosztami i użyciem dzienników Azure Monitor.

## <a name="versions-supported"></a>Obsługiwane wersje
- Azure Monitor obecnie obsługuje zebraną wersję 4,8 lub nowszą.
- Agent Log Analytics dla systemu Linux v 1.1.0-217 lub nowszego jest wymagany dla kolekcji metryk zebranych.


## <a name="configuration"></a>Konfigurowanie
Poniżej przedstawiono podstawowe kroki konfigurowania kolekcji zbieranych danych w Azure Monitor.

1. Skonfiguruj zebrane dane w celu wysłania danych do agenta Log Analytics dla systemu Linux przy użyciu wtyczki write_http.  
2. Skonfiguruj Log Analytics agenta dla systemu Linux, aby nasłuchiwać zebranych danych na odpowiednim porcie.
3. Uruchom ponownie zebrane i Log Analytics agenta dla systemu Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurowanie zebranych danych do przesyłania dalej 

1. Aby skierować dane zebrane do agenta Log Analytics dla systemu Linux, należy `oms.conf` dodać je do katalogu konfiguracji zebranych. Miejsce docelowe tego pliku zależy od dystrybucji komputera z systemem Linux.

    Jeśli zebrany katalog konfiguracji znajduje się w/etc/collectd.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    Jeśli zebrany katalog konfiguracji znajduje się w/etc/collectd/collectd.conf.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >W przypadku wersji zebranych przed 5,5 należy zmodyfikować Tagi w `oms.conf` sposób pokazany powyżej.
    >

2. Kopiuj zebrane. conf do katalogu konfiguracji omsagent wybranego obszaru roboczego.

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. Uruchom ponownie zebrane i Log Analytics agenta dla systemu Linux przy użyciu następujących poleceń.

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Zebrane metryki do Azure Monitor konwersji schematu
Aby zachować znany model między metrykami infrastruktury już zebranymi przez agenta Log Analytics dla systemu Linux i są używane nowe metryki zbierane przez zebrane następujące mapowanie schematu:

| Pole metryki zebranej | Pole Azure Monitor |
|:--|:--|
| `host` | Computer (Komputer) |
| `plugin` | Brak |
| `plugin_instance` | Nazwa wystąpienia<br>Jeśli **plugin_instance** ma *wartość null* , następnie InstanceName = "*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Jeśli **type_instance** ma *wartość null* , CounterName =**puste** |
| `dsnames[]` | CounterName |
| `dstypes` | Brak |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Następne kroki
* Informacje na temat [zapytań dzienników](../logs/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań. 
* [Pola niestandardowe](../logs/custom-fields.md) służą do analizowania danych z rekordów dziennika systemowego do poszczególnych pól.