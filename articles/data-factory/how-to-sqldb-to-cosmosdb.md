---
title: Migrowanie tabel Azure SQL Database do usługi Azure CosmosDB za pomocą Azure Data Factory
description: Utwórz istniejący znormalizowany schemat bazy danych z Azure SQL Database i Przeprowadź migrację do nieznormalizowanego kontenera CosmosDB platformy Azure z Azure Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 3d67ac9474704fac39dbe7eb91aead5c4babc4ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383946"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Migrowanie znormalizowanego schematu bazy danych z usługi Azure SQL Database do nieznormalizowanego kontenera usługi Azure CosmosDB

W tym przewodniku wyjaśniono, jak zastosować istniejący znormalizowany schemat bazy danych w Azure SQL Database i przekonwertować go na schemat CosmosDB na platformie Azure w celu załadowania do usługi Azure CosmosDB.

Schematy języka SQL są zwykle modelowane przy użyciu trzeciej formy normalnej, co spowodowało znormalizowaną liczbę schematów, które zapewniają wysoki poziom integralności danych i mniej zduplikowanych wartości danych. Zapytania mogą sprzęgać jednostki w tabelach w celu odczytu. CosmosDB jest zoptymalizowany pod kątem krótkoterminowych transakcji i wykonywania zapytań w kolekcji lub kontenera za pośrednictwem nieznormalizowanych schematów z danymi zawartymi w dokumencie.

Korzystając z Azure Data Factory, utworzymy potok, który używa pojedynczego przepływu danych mapowania do odczytywania z dwóch Azure SQL Database znormalizowanych tabel zawierających klucze podstawowe i obce jako relacje jednostek. Moduł ADF dołączy te tabele do jednego strumienia przy użyciu aparatu Spark przepływu danych, zbiera dołączone wiersze do tablic i tworzy pojedyncze oczyszczone dokumenty do wstawienia do nowego kontenera usługi Azure CosmosDB.

Ten przewodnik spowoduje skompilowanie nowego kontenera o nazwie "Orders", który będzie używał ```SalesOrderHeader``` tabel i ```SalesOrderDetail``` ze standardowej przykładowej bazy danych AdventureWorks SQL Server. Te tabele reprezentują transakcje sprzedaży przyłączone przez ```SalesOrderID``` . Każdy unikatowy rekord szczegółowy ma swój własny klucz podstawowy ```SalesOrderDetailID``` . Relacja między nagłówkiem a szczegółem jest ```1:M``` . Dołączymy ```SalesOrderID``` się w usłudze ADF, a następnie przywrócisz każdy powiązany rekord szczegółowy do tablicy o nazwie "Detail".

Reprezentatywne zapytanie SQL dla tego przewodnika:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

W wyniku tego kontener CosmosDB osadzi wewnętrzne zapytanie w pojedynczy dokument i będzie wyglądać następująco:

![Kolekcja](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz pozycję **+ Nowy potok** , aby utworzyć nowy potok.

2. Dodawanie działania przepływu danych

3. W działaniu przepływu danych wybierz pozycję **Nowy mapowanie przepływu danych**.

4. Ten Graf przepływu danych zostanie skonstruowany poniżej

![Wykres przepływu danych](media/data-flow/cosmosb1.png)

5. Zdefiniuj Źródło dla "SourceOrderDetails". Dla zestawu danych Utwórz nowy Azure SQL Database zestaw danych, który wskazuje na ```SalesOrderDetail``` tabelę.

6. Zdefiniuj Źródło dla "SourceOrderHeader". Dla zestawu danych Utwórz nowy Azure SQL Database zestaw danych, który wskazuje na ```SalesOrderHeader``` tabelę.

7. W górnym źródle Dodaj transformację kolumn pochodnych po "SourceOrderDetails". Wywołaj nową transformację "rzutowanie". Musimy zaokrąglić ```UnitPrice``` kolumnę i rzutować ją na podwójny typ danych dla CosmosDB. Ustaw formułę na: ```toDouble(round(UnitPrice,2))``` .

8. Dodaj kolejną kolumnę pochodną i Wywołaj ją "MakeStruct". W tym miejscu utworzysz strukturę hierarchiczną, która będzie przechowywać wartości z tabeli szczegółów. Pamiętaj, że szczegóły są ```M:1``` relacjami z nagłówkiem. Nazwij nową strukturę ```orderdetailsstruct``` i Utwórz hierarchię w ten sposób, ustawiając każdą podkolumnę na nazwę kolumny przychodzącej:

![Utwórz strukturę](media/data-flow/cosmosb9.png)

9. Teraz przejdźmy do źródła nagłówka Sales. Dodaj transformację sprzężenia. Po prawej stronie wybierz pozycję "MakeStruct". Pozostaw ustawienie do sprzężenia wewnętrznego i wybierz ```SalesOrderID``` obie strony warunku sprzężenia.

10. Kliknij kartę Podgląd danych w nowym dodanym dołączeniu, aby zobaczyć wyniki do tego momentu. Powinny być widoczne wszystkie wiersze nagłówka dołączone do wierszy szczegółów. Jest to wynik tworzenia sprzężenia z ```SalesOrderID``` . Następnie połączymy szczegóły z wspólnych wierszy do struktury Details i agregują wspólne wiersze.

![Dołączanie](media/data-flow/cosmosb4.png)

11. Przed rozpoczęciem tworzenia tablic w celu denormalizacji tych wierszy najpierw należy usunąć niepożądane kolumny i upewnić się, że wartości danych będą zgodne z typami danych CosmosDB.

12. Dodaj kolejną transformację i ustaw mapowanie pola tak, aby wyglądać następująco:

![Przenaciskanie kolumny](media/data-flow/cosmosb5.png)

13. Teraz ponownie przetworzymy kolumnę waluta, tym razem ```TotalDue``` . Podobnie jak w kroku 7, ustaw formułę na: ```toDouble(round(TotalDue,2))``` .

14. W tym miejscu zostanie wykorzystana funkcja grupowania wierszy według wspólnego klucza ```SalesOrderID``` . Dodaj transformację zagregowaną i ustaw dla niej grupę ```SalesOrderID``` .

15. W formule agregującej Dodaj nową kolumnę o nazwie "Details" i Użyj tej formuły, aby zebrać wartości ze struktury utworzonej wcześniej o nazwie ```orderdetailsstruct``` : ```collect(orderdetailsstruct)``` .

16. Przekształcenie agregacji będzie dotyczyć tylko kolumn wyjściowych, które są częścią agregacji lub Grupuj według formuł. Dlatego musimy również uwzględnić kolumny w nagłówku sprzedaży. Aby to zrobić, Dodaj wzorzec kolumny w tej samej transformacji agregowanej. Ten wzorzec będzie zawierać wszystkie pozostałe kolumny w danych wyjściowych:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Użyj składni "This" w innych właściwościach, aby zachować te same nazwy kolumn i używać ```first()``` funkcji jako agregacji:

![Agregacja](media/data-flow/cosmosb6.png)

18. Jesteśmy gotowi do zakończenia przepływu migracji, dodając transformację ujścia. Kliknij pozycję "nowy" obok pozycji zestaw danych i Dodaj zestaw danych CosmosDB, który wskazuje bazę danych CosmosDB. W przypadku kolekcji zostanie wywołana "Orders" (zamówienia), która nie będzie zawierać schematu ani dokumentów, ponieważ zostanie utworzona na bieżąco.

19. W obszarze Ustawienia ujścia do akcji "Utwórz ponownie" należy wykonać akcję z kluczami partycji do ```\SalesOrderID``` i kolekcji. Upewnij się, że karta mapowanie wygląda następująco:

![Zrzut ekranu przedstawia kartę mapowanie.](media/data-flow/cosmosb7.png)

20. Kliknij pozycję Podgląd danych, aby upewnić się, że są widoczne następujące 32 wierszy, aby wstawiać nowe dokumenty do nowego kontenera:

![Zrzut ekranu przedstawia kartę Podgląd danych.](media/data-flow/cosmosb8.png)

Jeśli wszystko wygląda dobrze, możesz teraz utworzyć nowy potok, dodać to działanie przepływu danych do tego potoku i wykonać operację. Można wykonać polecenie z debugowania lub uruchamiania wyzwalanego. Po kilku minutach należy mieć nowy, nieznormalizowany kontener zamówień o nazwie "Orders" w bazie danych CosmosDB.

## <a name="next-steps"></a>Następne kroki

* Utwórz resztę logiki przepływu danych, korzystając z mapowania [przekształceń](concepts-data-flow-overview.md)przepływów danych.
* [Pobierz ukończony szablon potoku](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) dla tego samouczka i zaimportuj szablon do fabryki.
