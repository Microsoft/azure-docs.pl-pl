---
title: Rozwiązanie danych telekomunikacyjnych w Azure Monitor | Microsoft Docs
description: Dane przewodowe to skonsolidowane dane dotyczące sieci i wydajności z komputerów z agentami Log Analytics. Dane sieciowe są połączone z danymi Twojego dziennika, aby ułatwić korelowanie danych.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732027"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Rozwiązanie Wire Data 2.0 (wersja zapoznawcza) w Azure Monitor (wycofane)

![Symbol rozwiązania Dane o komunikacji sieciowej](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>Rozwiązanie do przesyłania danych o komunikacji sieciowej zostało zastąpione usługą [VM Insights](../vm/vminsights-overview.md) i [Service map rozwiązanie](../vm/service-map.md).  Zarówno Agent Log Analytics, jak i Agent zależności, służą do zbierania danych połączenia sieciowego do Azure Monitor.
>
>Obsługa rozwiązań do obsługi danych sieci szkieletowej kończy się **31 marca 2022**.  Do momentu wycofania istniejących klientów korzystających z rozwiązania Wire Data 2.0 (wersja zapoznawcza) może nadal z niego korzystać.
>
>Nowi i istniejący klienci powinni zainstalować rozwiązanie [VM Insights](../vm/vminsights-enable-overview.md) lub [Service map](../vm/service-map.md).  Zbierany zbiór danych z mapy jest porównywalny z zestawem danych Wire Data 2.0 (wersja zapoznawcza).  Usługi VM Insights zawierają zestaw danych Service Map wraz z dodatkowymi danymi i funkcjami wydajności na potrzeby analizy. Obie oferty mają [połączenia z platformą Azure — wskaźnikiem](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options).
 

Dane przewodowe to skonsolidowane dane dotyczące sieci i wydajności zebrane z komputerów połączonych z systemem Windows i Linux z agentem Log Analytics, w tym te monitorowane przez Operations Manager w danym środowisku. Dane sieciowe są łączone z innymi danymi Twojego dziennika, aby ułatwić korelowanie danych.

Oprócz agenta Log Analytics rozwiązanie do transmisji danych używa agentów zależności firmy Microsoft instalowanych na komputerach w infrastrukturze IT. Agenci Dependency Agent monitorują dane sieciowe wysyłane do i z Twoich komputerów dla poziomów sieci 2 i 3 w [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), włącznie z różnymi używanymi protokołami i portami. Dane są następnie wysyłane do Azure Monitor przy użyciu agentów.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Migrowanie do Azure Monitor szczegółowych informacji o maszynie wirtualnej lub Service Map

W wielu przypadkach widzimy, że klienci często mają zarówno Wire Data 2.0 (wersja zapoznawcza), jak i usługi  [VM Insights](../vm/vminsights-overview.md) lub [Service map rozwiązanie](../vm/service-map.md) już włączone na tych samych maszynach wirtualnych.  Oznacza to, że oferta zastępcza jest włączona na maszynie wirtualnej.  Możesz po prostu [usunąć rozwiązanie Wire Data 2.0 (wersja zapoznawcza) z obszaru roboczego log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

Jeśli masz maszyny wirtualne, na których włączono tylko Wire Data 2.0 (wersja zapoznawcza), możesz dołączyć maszyny wirtualne do usługi [VM Insights](../vm/vminsights-enable-overview.md) lub [Service map rozwiązanie](../vm/service-map.md) , a następnie [usunąć rozwiązanie Wire Data 2.0 (wersja zapoznawcza) z obszaru roboczego log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Migruj zapytania do tabeli VMConnection z szczegółowych informacji o maszynie wirtualnej Azure Monitor

### <a name="agents-providing-data"></a>Agenci udostępniający dane

#### <a name="wire-data-20-query"></a>Zapytanie Wire Data 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>Szczegółowe informacje o usłudze VM i zapytanie Service Map

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>Adresy IP agentów udostępniających dane

#### <a name="wire-data-20-query"></a>Zapytanie Wire Data 2.0

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>Szczegółowe informacje o usłudze VM i zapytanie Service Map

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Cała komunikacja wychodząca według zdalnego adresu IP

#### <a name="wire-data-20-query"></a>Zapytanie Wire Data 2.0

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>Szczegółowe informacje o usłudze VM i zapytanie Service Map

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Bajty odebrane według nazwy protokołu

#### <a name="wire-data-20-query"></a>Zapytanie Wire Data 2.0

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>Szczegółowe informacje o usłudze VM i zapytanie Service Map

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Ilość ruchu sieciowego (w bajtach) według procesu

#### <a name="wire-data-20-query"></a>Zapytanie Wire Data 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>Szczegółowe informacje o usłudze VM i zapytanie Service Map

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Więcej przykładów zapytań

Dodatkowe przykładowe zapytania można znaleźć w [dokumentacji wyszukiwania dzienników usługi VM Insights](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search) oraz w [dokumentacji alertów dotyczących usługi VM Insights](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries) .

## <a name="uninstall-wire-data-20-solution"></a>Odinstaluj rozwiązanie Wire Data 2.0

Aby odinstalować Wire Data 2.0 wystarczy usunąć rozwiązanie z Log Analytics obszarów roboczych.  Spowoduje to następujące skutki:

* Pakiet Zarządzanie danymi sieci jest usuwany z maszyn wirtualnych, które są połączone z obszarem roboczym 
* Typ danych sieci szkieletowej nie pojawia się już w obszarze roboczym

Postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution) , aby usunąć rozwiązanie do przesyłania danych.

>[!NOTE]
>Jeśli masz rozwiązanie Service Map lub usługi VM Insights w obszarze roboczym, pakiet administracyjny nie zostanie usunięty, ponieważ te rozwiązania również używają tego pakietu administracyjnego.

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 pakiety administracyjne

Gdy rozwiązanie Dane o komunikacji sieciowej zostanie uaktywnione w obszarze roboczym usługi Log Analytics, pakiet administracyjny o wielkości 300 KB jest wysyłany do wszystkich serwerów z systemem Windows w tym obszarze roboczym. Jeśli używasz agentów programu System Center Operations Manager w [połączonej grupie zarządzania](../agents/om-agents.md), pakiet administracyjny monitora zależności zostanie wdrożony z programu System Center Operations Manager. Jeśli agenci są połączeni bezpośrednio, Azure Monitor dostarcza pakiet administracyjny.

Pakiet administracyjny nosi nazwę Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Jest on zapisywany w folderze: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Źródło danych, którego używa pakiet administracyjny, to: % Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>Odinstalowywanie agenta zależności

>[!NOTE]
>Jeśli planujesz zamienić dane przewodowe na Service Map lub informacje o maszynie wirtualnej, nie należy usuwać agenta zależności.

Skorzystaj z poniższych sekcji, aby usunąć agenta zależności.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalowywanie agenta zależności w systemie Windows

Administrator może odinstalować agenta zależności dla systemu Windows za pomocą panelu sterowania.

Administrator może również uruchomić Agent\Uninstall.exe zależności w celu odinstalowania agenta zależności.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalowywanie agenta zależności w systemie Linux

Aby całkowicie odinstalować agenta zależności z systemu Linux, należy usunąć samego agenta i łącznik, który jest instalowany automatycznie z agentem. Oba te elementy można usunąć przy użyciu następującego pojedynczego polecenia:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Używanie rozwiązania Dane o komunikacji sieciowej 2.0

Na stronie **Omówienie** Twojego obszaru roboczego usługi Log Analytics w witrynie Azure Portal kliknij kafelek **Dane o komunikacji sieciowej 2.0**, aby otworzyć pulpit nawigacyjny rozwiązania Dane o komunikacji sieciowej. Na pulpicie nawigacyjnym znajdują się bloki wymienione w poniższej tabeli. Każdy blok zawiera do 10 elementów spełniających kryteria tego bloku dla określonego zakresu i czasu. Możesz uruchomić przeszukiwanie dzienników, które zwróci wszystkie rekordy. W tym celu kliknij przycisk **Zobacz wszystko** na dole bloku lub kliknij nagłówek bloku.

| **Blok** | **Opis** |
| --- | --- |
| Agenci przechwytujący ruch sieciowy | Pokazuje liczbę agentów, którzy przechwytują ruch sieciowy, i listę 10 najważniejszych komputerów przechwytujących ruch. Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Kliknij komputer na liście, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę przechwyconych bajtów. |
| Lokalne podsieci | Pokazuje liczbę lokalnych podsieci, które zostały odnalezione przez agentów.  Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>, które wyświetli listę wszystkich podsieci wraz z liczbą bajtów przesłanych w ramach każdej z nich. Kliknij podsieć na liście, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę bajtów przesłanych w tej podsieci. |
| Protokoły poziomu aplikacji | Pokazuje liczbę używanych protokołów poziomu aplikacji wykrytych przez agentów. Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Kliknij protokół, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę bajtów wysłanych przy użyciu tego protokołu. |

![Pulpit nawigacyjny rozwiązania Dane o komunikacji sieciowej](./media/wire-data/wire-data-dash.png)

Możesz użyć bloku **Agenci przechwytujący ruch sieciowy**, aby ustalić, jaka część przepustowości sieci jest zużywana przez komputery. Ten blok może pomóc łatwo odnaleźć _najbardziej rozgadany_ komputer w Twoim środowisku. Takie komputery mogą być przeciążone, działać inaczej niż zwykle lub zużywać więcej zasobów sieciowych niż zwykle.

![Zrzut ekranu przedstawiający blok ruch sieciowy z agentów przechwytywania na pulpicie nawigacyjnym Wire Data 2.0, w którym jest wyświetlana przepustowość sieci używana przez poszczególne komputery.](./media/wire-data/log-search-example01.png)

Analogicznie możesz użyć bloku **Podsieci lokalne**, aby określić, ile ruchu sieciowego przechodzi przez Twoje podsieci. Użytkownicy często definiują podsieci wokół najważniejszych obszarów dla swoich aplikacji. Ten blok oferuje wgląd w te obszary.

![Zrzut ekranu bloku podsieci lokalnych na pulpicie nawigacyjnym Wire Data 2.0 przedstawiający przepustowość sieci wykorzystywaną przez każdy LocalSubnet.](./media/wire-data/log-search-example02.png)

Blok **Protokoły poziomu aplikacji** przydaje się, ponieważ pomaga dowiedzieć się, jakie protokoły są używane. Na przykład możesz spodziewać się, że protokół SSH nie będzie używany w Twoim środowisku sieciowym. Wyświetlanie informacji dostępnych w bloku może szybko potwierdzić lub obalić Twoje oczekiwania.

![Zrzut ekranu przedstawiający blok protokołów poziomu aplikacji na pulpicie nawigacyjnym Wire Data 2.0, w którym jest wyświetlana przepustowość sieci używana przez poszczególne protokoły.](./media/wire-data/log-search-example03.png)

Warto również wiedzieć, czy ruch w ramach protokołu zwiększa się, czy też zmniejsza wraz z upływem czasu. Jeśli na przykład wzrasta ilość danych przesyłanych przez aplikację, być może warto zwrócić uwagę.

## <a name="input-data"></a>Dane wejściowe

Dane o komunikacji sieciowej gromadzą metadane dotyczące ruchu sieciowego przy użyciu agentów, którzy zostali włączeni. Każdy agent wysyła dane co około 15 s.

## <a name="output-data"></a>Dane wyjściowe

Rekord o typie _WireData_ jest tworzony dla każdego typu danych wejściowych. Rekordy rozwiązania Dane o komunikacji sieciowej mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|---|---|
| Computer (Komputer) | Nazwa komputera, gdzie są gromadzone dane |
| TimeGenerated | Czas rekordu |
| LocalIP | Adres IP komputera lokalnego |
| SessionState | Połączone lub rozłączone |
| ReceivedBytes | Liczba odebranych bajtów |
| ProtocolName | Nazwa używanego protokołu sieciowego |
| IPVersion | Wersja protokołu IP |
| Kierunek | Ruch przychodzący lub wychodzący |
| MaliciousIP | Adres IP znanego złośliwego źródła |
| Ważność | Ważność podejrzanego złośliwego oprogramowania |
| RemoteIPCountry | Kraj/region zdalnego adresu IP |
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager |
| SourceSystem | Źródło, gdzie zostały zebrane dane |
| SessionStartTime | Godzina rozpoczęcia sesji |
| SessionEndTime | Godzina zakończenia sesji |
| LocalSubnet | Podsieć, gdzie zostały zebrane dane |
| LocalPortNumber | Numer portu lokalnego |
| RemoteIP | Zdalny adres IP używany przez komputer zdalny |
| RemotePortNumber | Numer portu używany przez zdalny adres IP |
| SessionID | Unikatowa wartość, która identyfikuje sesję komunikacji między dwoma adresami IP |
| SentBytes | Liczba wysłanych bajtów |
| TotalBytes | Całkowita liczba bajtów wysłanych podczas sesji |
| ApplicationProtocol | Typ użytego protokołu sieciowego   |
| ProcessId | Identyfikator procesu systemu Windows |
| ProcessName | Ścieżka i nazwa pliku procesu |
| RemoteIPLongitude | Wartość długości geograficznej IP |
| RemoteIPLatitude | Wartość szerokości geograficznej IP |

## <a name="next-steps"></a>Następne kroki

- Zobacz [wdrażanie usługi VM Insights](./vminsights-enable-overview.md) , aby poznać wymagania i metody umożliwiające monitorowanie maszyn wirtualnych.
- [Wyszukaj dzienniki](../logs/log-query-overview.md), aby wyświetlić szczegółowe rekordy wyszukiwania rozwiązania Dane o komunikacji sieciowej.
