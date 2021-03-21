---
title: Zarządzanie kosztami bezserwerowej puli SQL
description: W tym dokumencie opisano, jak zarządzać kosztami puli SQL bezserwerowej i sposobem obliczania danych przetwarzanych podczas wykonywania zapytań dotyczących danych w usłudze Azure Storage.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491588"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Zarządzanie kosztami dla puli SQL bezserwerowej w usłudze Azure Synapse Analytics

W tym artykule wyjaśniono, jak można oszacować i zarządzać kosztami bezserwerowych puli SQL w usłudze Azure Synapse Analytics:
- Szacowana ilość danych przetworzonych przed wygenerowaniem zapytania
- Ustawianie budżetu przy użyciu funkcji kontroli kosztów

Należy zrozumieć, że koszty puli SQL bezserwerowej w usłudze Azure Synapse Analytics są jedynie częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Jeśli używasz innych usług platformy Azure, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm. W tym artykule wyjaśniono, jak planować i zarządzać kosztami bezserwerowych puli SQL w usłudze Azure Synapse Analytics.

## <a name="data-processed"></a>Ilość przetworzonych danych

*Przetworzone dane* to ilość danych, które system tymczasowo przechowuje w czasie wykonywania zapytania. Przetwarzane dane składają się z następujących ilości:

- Ilość danych odczytanych z magazynu. Ta kwota obejmuje:
  - Dane są odczytywane podczas odczytywania danych.
  - Dane są odczytywane podczas odczytywania metadanych (dla formatów plików zawierających metadane, takie jak parquet).
- Ilość danych w wynikach pośrednich. Te dane są przenoszone między węzłami, gdy zapytanie działa. Obejmuje on transfer danych do punktu końcowego w nieskompresowanym formacie. 
- Ilość danych zapisywana w magazynie. Jeśli używasz CETAS do eksportowania zestawu wyników do magazynu, ilość danych wypisanych jest dodawana do ilości danych przetworzonych dla wybranej części CETAS.

Odczytywanie plików z magazynu jest wysoce zoptymalizowane. Proces używa:

- Pobieranie z wyprzedzeniem, co może spowodować zwiększenie nakładu pracy na ilość odczytywanych danych. Jeśli zapytanie odczytuje cały plik, nie ma żadnych nakładów. Jeśli plik jest odczytywany częściowo, podobnie jak w pierwszych N kwerendach, to dane są odczytywane przy użyciu funkcji pobierania z wyprzedzeniem.
- Zoptymalizowany Analizator wartości rozdzielanych przecinkami (CSV). Jeśli używasz PARSER_VERSION = "2.0" do odczytywania plików CSV, ilości danych odczytywane z magazynu nieco rosną. Zoptymalizowany Analizator CSV odczytuje pliki równolegle, w fragmentach o równym rozmiarze. Fragmenty nie muszą zawierać całych wierszy. Aby zapewnić przeanalizowanie wszystkich wierszy, zoptymalizowany Analizator CSV odczytuje również małe fragmenty sąsiadujących fragmentów. Ten proces powoduje dodanie niewielkiej ilości kosztów.

## <a name="statistics"></a>Statystyki

Optymalizator zapytań puli SQL bezserwerowej opiera się na statystyce do generowania optymalnych planów wykonywania zapytań. Statystyki można tworzyć ręcznie. W przeciwnym razie Pula SQL bezserwerowa tworzy je automatycznie. W obu przypadkach statystyki są tworzone przez uruchomienie oddzielnego zapytania, które zwraca określoną kolumnę w podanej szybkości próbkowania. To zapytanie ma skojarzoną ilość przetworzonych danych.

W przypadku uruchomienia tego samego lub innego zapytania, które byłyby korzystne dla utworzonych statystyk, statystyki są ponownie używane, jeśli jest to możliwe. Nie są przetwarzane żadne dodatkowe dane na potrzeby tworzenia statystyk.

W przypadku tworzenia statystyk dla kolumny Parquet tylko odpowiednia kolumna jest odczytywana z plików. W przypadku tworzenia statystyk dla kolumny CSV wszystkie pliki są odczytywane i analizowane.

## <a name="rounding"></a>Zaokrąglanie

Ilość przetworzonych danych jest zaokrąglana do najbliższej liczby MB na zapytanie. Każde zapytanie ma co najmniej 10 MB przetworzonych danych.

## <a name="what-data-processed-doesnt-include"></a>Przetworzone dane nie obejmują

- Metadane na poziomie serwera (takie jak nazwy logowania, role i poświadczenia na poziomie serwera).
- Bazy danych tworzone w punkcie końcowym. Te bazy danych zawierają tylko metadane (takie jak użytkownicy, role, schematy, widoki, wbudowane funkcje z wartościami przechowywanymi w tabeli [TVFs], procedury składowane, poświadczenia w zakresie bazy danych, zewnętrzne źródła danych, zewnętrzne formaty plików i tabele zewnętrzne).
  - Jeśli używasz wnioskowania schematu, fragmenty plików są odczytywane do nazw kolumn i typów danych, a ilość odczytanych danych jest dodawana do ilości przetworzonych danych.
- Instrukcje języka definicji danych (DDL), z wyjątkiem instrukcji CREATE STATISTICs, ponieważ przetwarzają dane z magazynu na podstawie podanego procentu próbki.
- Zapytania zawierające tylko metadane.

## <a name="reducing-the-amount-of-data-processed"></a>Zmniejszenie ilości przetworzonych danych

Możesz zoptymalizować ilość przetworzonych danych i poprawić wydajność, partycjonowanie i konwertowanie danych na skompresowany format oparty na kolumnie, taki jak Parquet.

## <a name="examples"></a>Przykłady

Wyobraź sobie trzy tabele.

- Tabela population_csv jest obsługiwana przez 5 TB plików CSV. Pliki są zorganizowane w pięć kolumn o równym rozmiarze.
- Tabela population_parquet ma te same dane co tabela population_csv. Jest ona obsługiwana przez 1 TB plików Parquet. Ta tabela jest mniejsza niż poprzednia, ponieważ dane są kompresowane w formacie Parquet.
- Very_small_csv tabeli jest obsługiwane przez 100 KB plików CSV.

**Zapytanie 1**: wybór sum (populacji) z population_csv

To zapytanie odczytuje i analizuje całe pliki, aby uzyskać wartości dla kolumny populacji. Węzły przetwarzają fragmenty tej tabeli i łączną populacją dla każdego fragmentu są przesyłane między węzłami. Końcowa suma jest transferowana do punktu końcowego. 

To zapytanie przetwarza 5 TB danych i niewielkie obciążenie związane z transferem sum fragmentów.

**Zapytanie 2**: wybór sum (populacji) z population_parquet

Podczas wykonywania zapytań o formaty skompresowane i oparte na kolumnach, takie jak Parquet, mniejsze dane są odczytywane niż w zapytaniu 1. Zobaczysz ten wynik, ponieważ pula SQL bezserwerowa odczytuje pojedynczą skompresowaną kolumnę zamiast całego pliku. W tym przypadku jest odczytywane 0,2 TB. (Pięć kolumn o równym rozmiarze jest 0,2 TB dla każdej z nich). Węzły przetwarzają fragmenty tej tabeli i łączną populacją dla każdego fragmentu są przesyłane między węzłami. Końcowa suma jest transferowana do punktu końcowego. 

To zapytanie przetwarza 0,2 TB oraz niewielką ilość kosztów związanych z transferem sum fragmentów.

**Zapytanie 3**: select * from population_parquet

To zapytanie odczytuje wszystkie kolumny i przesyła wszystkie dane w nieskompresowanym formacie. Jeśli format kompresji to 5:1, zapytanie przetwarza 6 TB, ponieważ odczytuje 1 TB i transferuje 5 TB nieskompresowanych danych.

**Zapytanie 4**: wybór liczby (*) z very_small_csv

To zapytanie odczytuje całe pliki. Łączny rozmiar plików w magazynie dla tej tabeli to 100 KB. Węzły przetwarzają fragmenty tej tabeli i łączą dla każdego fragmentu są przenoszone między węzłami. Końcowa suma jest transferowana do punktu końcowego. 

To zapytanie przetwarza nieco więcej niż 100 KB danych. Ilość danych przetworzonych dla tego zapytania jest zaokrąglana do 10 MB, jak określono w sekcji [Zaokrąglenie](#rounding) tego artykułu.

## <a name="cost-control"></a>Kontrola kosztów

Funkcja kontroli kosztów w puli SQL bezserwerowej umożliwia ustawienie budżetu dla ilości przetworzonych danych. Możesz ustawić budżet w TB danych przetworzonych w ciągu dnia, tygodnia i miesiąca. W tym samym czasie można określić co najmniej jeden budżet. Aby skonfigurować kontrolę kosztów dla puli SQL bezserwerowej, można użyć programu Synapse Studio lub T-SQL.

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>Konfigurowanie kontroli kosztów dla puli SQL bezserwerowej w programie Synapse Studio
 
Aby skonfigurować kontrolę kosztów dla puli SQL bezserwerowej w programie Synapse Studio, przejdź do pozycji Zarządzaj elementem w menu po lewej stronie, a następnie wybierz pozycję element puli SQL w obszarze pule analityczne. Po umieszczeniu wskaźnika myszy w puli SQL bezserwerowej zobaczysz ikonę kontroli kosztów — kliknij tę ikonę.

![Nawigacja w formancie kosztów](./media/data-processed/cost-control-menu.png)

Po kliknięciu ikony kontroli kosztów zostanie wyświetlony pasek boczny:

![Konfiguracja kontroli kosztów](./media/data-processed/cost-control-sidebar.png)

Aby ustawić jeden lub więcej budżetów, należy najpierw kliknąć przycisk Włącz przycisk radiowy dla budżetu, który ma zostać ustawiony, a nie wprowadzić wartości całkowitej w polu tekstowym. Jednostką dla tej wartości jest TBs. Po skonfigurowaniu odpowiednich budżetów kliknij przycisk Zastosuj w dolnej części paska bocznego. Oznacza to, że budżet jest teraz ustawiony.

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>Konfigurowanie kontroli kosztów dla puli SQL bezserwerowej w języku T-SQL

Aby skonfigurować kontrolę kosztów dla puli SQL bezserwerowej w języku T-SQL, należy wykonać jedną lub więcej z poniższych procedur składowanych.

```sql
sp_set_data_processed_limit
    @type = N'daily',
    @limit_tb = 1

sp_set_data_processed_limit
    @type= N'weekly',
    @limit_tb = 2

sp_set_data_processed_limit
    @type= N'monthly',
    @limit_tb = 3334
```

Aby wyświetlić bieżącą konfigurację, wykonaj następującą instrukcję T-SQL:

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

Aby sprawdzić, ile danych zostało przetworzonych w bieżącym dniu, tygodniu lub miesiącu, wykonaj następującą instrukcję T-SQL:

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zoptymalizować zapytania pod kątem wydajności, zobacz [najlepsze rozwiązania dotyczące puli SQL bezserwerowej](best-practices-sql-on-demand.md).
