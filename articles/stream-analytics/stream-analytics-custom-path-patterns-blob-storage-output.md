---
title: Azure Stream Analytics partycjonowanie niestandardowego obiektu BLOB
description: W tym artykule opisano niestandardowe wzorce ścieżki DateTime oraz funkcje niestandardowego pola lub atrybutów dla danych wyjściowych usługi BLOB Storage z zadań Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: cb9d8edd24dcc8809f2b207a4db80653b0e140e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98014040"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics partycjonowanie niestandardowego obiektu BLOB

Azure Stream Analytics obsługuje partycjonowanie niestandardowego obiektu BLOB przy użyciu niestandardowych pól lub atrybutów oraz niestandardowych wzorców ścieżki DateTime. 

## <a name="custom-field-or-attributes"></a>Niestandardowe pole lub atrybuty

Atrybuty pola niestandardowego lub danych wejściowych usprawniają przepływy pracy przetwarzania danych i raportowania przez umożliwienie większej kontroli nad danymi wyjściowymi.

### <a name="partition-key-options"></a>Opcje klucza partycji

Klucz partycji lub nazwa kolumny używana do partycjonowania danych wejściowych mogą zawierać dowolny znak, który jest akceptowany dla [nazw obiektów BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata). Nie można używać zagnieżdżonych pól jako klucza partycji, chyba że jest używany w połączeniu z aliasami, ale można użyć niektórych znaków do utworzenia hierarchii plików. Na przykład można użyć następującego zapytania, aby utworzyć kolumnę, która łączy dane z dwóch innych kolumn, aby utworzyć unikatowy klucz partycji.

```sql
SELECT name, id, CONCAT(name, "/", id) AS nameid
```

Klucz partycji musi być typu NVARCHAR (MAX), BIGINT, FLOAT lub BIT (poziom zgodności 1,2 lub wyższy). Typy DateTime, Array i Records nie są obsługiwane, ale mogą być używane jako klucze partycji, jeśli są konwertowane na ciągi. Aby uzyskać więcej informacji, zobacz [Azure Stream Analytics typów danych](/stream-analytics-query/data-types-azure-stream-analytics).

### <a name="example"></a>Przykład

Załóżmy, że zadanie pobiera dane wejściowe z sesji użytkownika na żywo połączone z zewnętrzną usługą gier wideo, w której pozyskiwane dane zawierają kolumnę **client_id** do identyfikowania sesji. Aby podzielić dane przez **client_id**, należy ustawić pole wzorca ścieżki obiektu BLOB w celu uwzględnienia tokenu partycji **{client_id}** we właściwościach danych wyjściowych obiektu BLOB podczas tworzenia zadania. Jako że dane zawierające różne wartości **client_id** przepływają przez zadanie Stream Analytics, dane wyjściowe są zapisywane w oddzielnych folderach na podstawie pojedynczej **client_id** wartości dla każdego folderu.

![Wzorzec ścieżki z identyfikatorem klienta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Podobnie, jeśli dane wejściowe zadania były danymi czujnika z milionów czujników, w których każdy czujnik miał **sensor_id**, wzorzec ścieżki będzie **{sensor_id}** do partycjonowania poszczególnych danych czujników w różnych folderach.  


W przypadku korzystania z interfejsu API REST sekcja Output pliku JSON użyta dla tego żądania może wyglądać następująco:  

![Wyjście interfejsu API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Po uruchomieniu zadania kontener *klienci* może wyglądać następująco:  

![Kontener klientów](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Każdy folder może zawierać wiele obiektów blob, w których każdy obiekt BLOB zawiera jeden lub więcej rekordów. W powyższym przykładzie istnieje pojedynczy obiekt BLOB w folderze o nazwie "06000000" z następującą zawartością:

![Zawartość obiektu BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Zwróć uwagę, że każdy rekord w obiekcie blob ma **client_id** kolumnę pasującą do nazwy folderu, ponieważ kolumna używana do partycjonowania danych wyjściowych w ścieżce danych wyjściowych została **client_id**.

### <a name="limitations"></a>Ograniczenia

1. W właściwości Output obiektu BLOB wzorca ścieżki jest dozwolony tylko jeden klucz partycji niestandardowej. Wszystkie następujące wzorce ścieżki są prawidłowe:

   * Klaster1/{Date}/{aFieldInMyData}  
   * Klaster1/{Time}/{aFieldInMyData}  
   * Klaster1/{aFieldInMyData}  
   * Klaster1/{Date}/{Time}/{aFieldInMyData} 
   
2. W kluczach partycji nie jest rozróżniana wielkość liter, dlatego klucze partycji, takie jak "Jan" i "Jan", są równoważne. Wyrażenia nie mogą być również używane jako klucze partycji. Na przykład **{ColumnA + ColumnB}** nie działa.  

3. Gdy strumień wejściowy składa się z rekordów z kardynalnością klucza partycji poniżej 8000, rekordy będą dołączane do istniejących obiektów blob i w razie potrzeby tworzyć nowe obiekty blob. Jeśli Kardynalność przekracza 8000, nie ma gwarancji, że istniejące obiekty blob zostaną zapisane w programie, a nowe obiekty blob nie zostaną utworzone dla dowolnej liczby rekordów z tym samym kluczem partycji.

4. Jeśli dane wyjściowe obiektu BLOB są [skonfigurowane jako niezmienne](../storage/blobs/storage-blob-immutable-storage.md), Stream Analytics utworzy nowy obiekt BLOB za każdym razem, gdy dane są wysyłane.

## <a name="custom-datetime-path-patterns"></a>Niestandardowe wzorce ścieżki DateTime

Niestandardowe wzorce ścieżki DateTime umożliwiają określenie formatu danych wyjściowych, który jest zgodny z konwencjami przesyłania strumieniowego Hive, dając Azure Stream Analytics możliwość wysyłania danych do usługi Azure HDInsight i Azure Databricks do przetwarzania podrzędnego. Niestandardowe wzorce ścieżki DateTime są łatwo implementowane za pomocą `datetime` słowa kluczowego w polu Prefiks ścieżki w danych wyjściowych obiektu BLOB wraz ze specyfikatorem formatu. Na przykład `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Obsługiwane tokeny

Poniższe tokeny specyfikatora formatu mogą być używane autonomicznie lub w połączeniu do osiągnięcia niestandardowych formatów DateTime:

|Specyfikator formatu   |Opis   |Wyniki dotyczące przykładowego czasu 2018 r-01-02T10:06:08|
|----------|-----------|------------|
|{DateTime: rrrr}|Rok jako numer czterocyfrowy|2018|
|{DateTime: MM}|Miesiąc od 01 do 12|01|
|{DateTime: M}|Miesiąc od 1 do 12|1|
|{DateTime: DD}|Dzień od 01 do 31|02|
|{DateTime: d}|Dzień od 1 do 31|2|
|{DateTime: gg}|Godzina przy użyciu formatu 24-godzinnego, od 00 do 23|10|
|{DateTime: mm}|Minuty od 00 do 60|06|
|{DateTime: m}|Minuty od 0 do 60|6|
|{DateTime: SS}|Sekund od 00 do 60|08|

Jeśli nie chcesz używać niestandardowych wzorców DateTime, możesz dodać token {date} i/lub {Time} do prefiksu ścieżki, aby wygenerować listę rozwijaną z wbudowanymi formatami DateTime.

![Stream Analytics stare formaty DateTime](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Rozszerzalność i ograniczenia

Możesz użyć dowolną liczbę tokenów, `{datetime:<specifier>}` tak jak w przypadku wzorca ścieżki, dopóki nie osiągniesz limitu znaków prefiksu ścieżki. Specyfikatory formatu nie mogą być łączone w pojedynczym tokenie poza kombinacje już wymienione na liście rozwijanej daty i godziny. 

Dla partycji ścieżki `logs/MM/dd` :

|Prawidłowe wyrażenie   |Nieprawidłowe wyrażenie   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Można użyć tego samego specyfikatora formatu wiele razy w prefiksie ścieżki. Token musi być powtarzany za każdym razem.

### <a name="hive-streaming-conventions"></a>Konwencje przesyłania strumieniowego Hive

Wzorce ścieżki niestandardowej dla magazynu obiektów BLOB mogą być używane z Konwencją przesyłania strumieniowego Hive, która oczekuje, że foldery mają być oznaczone jako `column=` Nazwa folderu.

Na przykład `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Niestandardowe dane wyjściowe eliminujeją problemy z modyfikowaniem tabel i ręcznie dodając partycje do danych portów między Azure Stream Analytics i Hive. Zamiast tego wiele folderów można dodać automatycznie przy użyciu:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Przykład

Utwórz konto magazynu, grupę zasobów, zadanie Stream Analytics i źródło danych wejściowych zgodnie z przewodnikiem Szybki Start dotyczącym [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) . Użyj tych samych przykładowych danych, które są używane w przewodniku Szybki Start, również dostępnych w witrynie [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Utwórz ujścia danych wyjściowych obiektu BLOB przy użyciu następującej konfiguracji:

![Stream Analytics utworzyć ujścia danych wyjściowych obiektu BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Wzorzec pełnej ścieżki jest następujący:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Po uruchomieniu zadania Struktura folderu oparta na wzorcu ścieżki zostanie utworzona w kontenerze obiektów BLOB. Możesz przejść do szczegółów na poziomie dnia.

![Stream Analytics dane wyjściowe obiektu BLOB ze wzorcem niestandardowej ścieżki](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Następne kroki

* [Poznanie danych wyjściowych z Azure Stream Analytics](stream-analytics-define-outputs.md)
