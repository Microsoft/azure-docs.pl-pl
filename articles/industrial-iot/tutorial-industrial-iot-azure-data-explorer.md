---
title: Ściąganie danych z usługi Azure Industrial IoT do ADX
description: W tym samouczku dowiesz się, jak ściągać dane IIoT do ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787776"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Samouczek: ściąganie danych z usługi Azure Industrial IoT do ADX

Platforma Azure Industrial IoT (IIoT) łączy moduły brzegowe i mikrousługi w chmurze z szeregiem usług Azure PaaS Services w celu zapewnienia możliwości wykrywania zasobów przemysłowych i zbierania danych z tych zasobów przy użyciu OPC UA. [Usługa Azure Eksplorator danych (ADX)](https://docs.microsoft.com/azure/data-explorer) to naturalne miejsce docelowe danych IIoT z funkcjami analizy danych, które umożliwiają uruchamianie elastycznych zapytań dotyczących pozyskiwanych danych z serwerów OPC UA podłączonych do IoT Hub za pomocą wydawcy OPC. Mimo że klaster ADX może pozyskać dane bezpośrednio z IoT Hub, platforma IIoT wykonuje dalsze przetwarzanie danych, aby była bardziej przydatna przed umieszczeniem jej w centrum zdarzeń, gdy zostanie użyte pełne wdrożenie mikrousług (zapoznaj się z architekturą platformy IIoT).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie tabeli w ADX
> * Łączenie centrum zdarzeń z klastrem ADX
> * Analizowanie danych w ADX

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>Jak zapewnić dostępność danych w klastrze ADX w celu efektywnego wykonywania zapytań 

Jeśli przeszukasz format komunikatu z centrum zdarzeń (zgodnie z definicją klasy Microsoft. Azure. IIoT. OpcUa. Subscriber. models. MonitoredItemMessageModel), zobaczymy wskazówkę dotyczącą struktury, która jest potrzebna dla schematu tabeli ADX.

![Struktura](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Poniżej przedstawiono kroki, które należy wykonać, aby zapewnić dostęp do danych w klastrze ADX i efektywnie wysyłać zapytania o dane.  
1. Utwórz klaster ADX. Jeśli nie masz już klastra ADX z platformą IIoT lub jeśli chcesz użyć innego klastra, wykonaj kroki opisane [tutaj](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster). 
2. Włącz pozyskiwanie strumieniowe w klastrze ADX, jak wyjaśniono [tutaj](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster). 
3. Utwórz bazę danych ADX, wykonując kroki opisane [tutaj](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database).

W poniższym kroku będziemy używać [interfejsu internetowego ADX](https://docs.microsoft.com/azure/data-explorer/web-query-data) do uruchamiania wymaganych zapytań. Upewnij się, że klaster został dodany do interfejsu sieci Web, jak wyjaśniono w łączu.  
 
4. Utwórz tabelę w ADX, aby umieścić pozyskane dane w.  Chociaż Klasa MonitoredItemMessageModel może służyć do definiowania schematu tabeli ADX, zaleca się pozyskiwanie danych najpierw w tabeli przemieszczania z jedną kolumną typu [dynamicznego](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic). Zapewnia to nam większą elastyczność obsługi danych i przetwarzania w innych tabelach (potencjalnie łącząc je z innymi źródłami danych), które spełniają potrzeby wielu przypadków użycia. Następujące zapytanie ADX tworzy tabelę przemieszczania "iiot_stage" z jedną kolumną "ładunek",

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Musimy również dodać mapowanie pozyskiwania danych JSON, aby nakazać klastrowi umieszczenie całego komunikatu JSON z centrum zdarzeń w tabeli przemieszczania,

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. Nasza tabela jest teraz gotowa do odbierania danych z centrum zdarzeń. 
6. Skorzystaj z instrukcji znajdujących się [tutaj](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) , aby połączyć centrum zdarzeń z klastrem ADX i rozpocząć pozyskiwanie danych do naszej tabeli tymczasowej. Musimy utworzyć połączenie tylko wtedy, gdy centrum zdarzeń jest już obsługiwane przez platformę IIoT.  
7. Po zweryfikowaniu połączenia dane zaczynają przepływać do naszej tabeli i po krótkim opóźnieniu będziemy mogli rozpocząć badanie danych w naszej tabeli. Użyj następującego zapytania w interfejsie sieci Web ADX, aby przyjrzeć się próbce danych 10 wierszy. Możemy zobaczyć, jak dane w ładunku są podobne do klasy MonitoredItemMessageModel wymienionej wcześniej.

![Zapytanie](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. Zezwól nam na teraz uruchamianie niektórych analiz dotyczących tych danych, przeanalizowany dane dynamiczne bezpośrednio w kolumnie "ładunek". W tym przykładzie obliczy średnią wartość telemetrii identyfikowaną przez "DisplayName": "PositiveTrendData", w okresie Windows 10 minut, na wszystkie rekordy pozyskiwane od określonego momentu (zdefiniowanego przez zmienną min_t) let min_t = DateTime (2020-10-23); iiot_stage | gdzie ToDateTime (ładunek. Sygnatura czasowa) > min_t | gdzie ToString (ładunek) DisplayName) = = "PositiveTrendData" | Sumuj event_avg = AVG (ToDouble — (ładunek. Wartość)) według bin (ToDateTime (ładunek. Sygnatura czasowa), 10 m)
 
Ponieważ nasza kolumna "ładunek" zawiera dane dynamiczne, musimy wykonać konwersję danych w czasie wykonywania zapytania, aby nasze obliczenia były wykonywane w przypadku prawidłowych typów danych.

![Sygnatura czasowa ładunku](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Jak wspomniano wcześniej, pozyskiwanie danych OPC UA do tabeli przemieszczania z jedną kolumną "dynamiczny" zapewnia elastyczność. Jednak konieczność uruchamiania konwersji typów danych w czasie zapytania może spowodować opóźnienia wykonywania zapytań szczególnie wtedy, gdy wolumin danych jest duży i jeśli istnieje wiele współbieżnych zapytań. Na tym etapie możemy utworzyć kolejną tabelę o typach danych, które zostały już określone, aby uniknąć konwersji typów danych w czasie wykonywania zapytania.
 
9. Utwórz nową tabelę dla przeanalizowanych danych składających się z ograniczonego wyboru z zawartości dynamicznego elementu "ładunek" w tabeli przemieszczania. Należy zauważyć, że dla każdego z oczekiwanych typów danych w naszym telemetrii została utworzona kolumna wartości.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Utwórz funkcję (na poziomie bazy danych), aby zaprojektować wymagane dane z tabeli przemieszczania. W tym miejscu wybieramy elementy "timestamp", "PublisherId", "DisplayName", "DataType" i "NodeId" z kolumny "ładunek" i "Tag_Timestamp", "Tag_PublisherId", "tag", "Tag_Datatype", "Tag_NodeId". Element "value" jest rzutowany jako trzy różne części na podstawie typu "DataType".

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Aby uzyskać więcej informacji na temat mapowania typów danych w programie ADX, zobacz [tutaj](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic), a dla funkcji w ADX możesz zacząć od tego [miejsca](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions).
 
11. Zastosuj funkcję z poprzedniego kroku do przeanalizowanej tabeli przy użyciu zasad aktualizacji. [Zasady](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) aktualizacji instruują ADX, aby automatycznie dołączać dane do tabeli docelowej za każdym razem, gdy nowe dane są wstawiane do tabeli źródłowej, na podstawie zapytania przekształcenia, które jest uruchamiane na danych wstawionych do tabeli źródłowej. Można użyć następującego zapytania, aby przypisać przeanalizowanej tabeli jako lokalizację docelową i tabelę etapów jako źródło zasad aktualizacji zdefiniowane przez utworzoną funkcję w poprzednim kroku.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Zaraz po wykonaniu powyższego zapytania dane zaczynają przepływać i wypełniać tabelę docelową "iiot_parsed". Dane można przyjrzeć do "iiot_parsed w następujący sposób".

![Przeanalizowana tabela](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Poinformuj nas o tym, jak możemy powtórzyć analizę, która była w poprzednim kroku; Oblicz średnią wartość telemetrii identyfikowaną przez "DisplayName": "PositiveTrendData", w okresie Windows 10 minut, na wszystkie rekordy pozyskane od określonego momentu (zdefiniowanego przez zmienną min_t). Ponieważ teraz wartości tagu "PositveTrendData" są przechowywane w kolumnie typu Double Data, oczekiwano poprawy wydajności zapytań.

![Powtórz analizę](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Pozwól nam porównać wydajność zapytań w obu przypadkach. Można znaleźć czas potrzebny do wykonania zapytania przy użyciu "statystyki" w interfejsie użytkownika ADX (który może znajdować się powyżej wyników zapytania).  

![Wydajność zapytań 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Wydajność zapytań 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Możemy zobaczyć, że zapytanie korzystające z przeanalizowanej tabeli jest bardzo dwa razy równie szybkie, jak w przypadku tabeli przemieszczania. W tym przykładzie mamy mały zestaw danych i nie ma uruchomionych współbieżnych zapytań, więc wpływ na czas wykonywania zapytania nie jest dobry, jednak w przypadku realistycznego obciążenia może wystąpić duży wpływ na wydajność. Dlatego ważne jest, aby rozważyć rozdzielenie różnych typów danych do różnych kolumn.

> [!NOTE] 
> Zasady aktualizacji działają tylko na danych, które są pozyskiwane w tabeli przemieszczania po skonfigurowaniu zasad i nie mają zastosowania do żadnych istniejących wcześniej danych. Należy wziąć pod uwagę, gdy na przykład trzeba zmienić zasady aktualizacji. Pełne szczegóły można znaleźć w dokumentacji ADX.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak zmienić wartości domyślne konfiguracji, możesz 

> [!div class="nextstepaction"]
> [Skonfiguruj przemysłowe składniki IoT](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Wizualizuj i analizuj dane przy użyciu Time Series Insights](tutorial-visualize-data-time-series-insights.md)