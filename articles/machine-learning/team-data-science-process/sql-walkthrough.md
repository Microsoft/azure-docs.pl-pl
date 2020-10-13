---
title: Kompilowanie i wdrażanie modelu na SQL Server maszynie wirtualnej — proces nauki danych zespołu
description: Kompiluj i wdrażaj model uczenia maszynowego przy użyciu SQL Server na maszynie wirtualnej platformy Azure z publicznie dostępnym zestawem danych.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d7c02e413fdaa54db431cdac7a3cf7af0bddeb98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331900"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Proces nauki danych zespołu w działaniu: używanie SQL Server
W tym samouczku przedstawiono proces kompilowania i wdrażania modelu uczenia maszynowego przy użyciu SQL Server i publicznie dostępnego zestawu danych — zestawu danych [podróży NYC z taksówkami](https://www.andresmh.com/nyctaxitrips/) . Procedura jest zgodna ze standardowym przepływem nauki o danych: pozyskiwanie i Eksplorowanie danych, inżynierów w celu ułatwienia uczenia się, a następnie kompilowania i wdrażania modelu.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Opis zestawu danych NYC taksówki
Dane dotyczące rejsu z NYC taksówkami dotyczą 20 GB skompresowanych plików CSV (~ 48 GB nieskompresowanych), składających się z ponad 173 000 000 pojedynczych podróży i opłat za każdą podróż. Każdy rekord rejsu zawiera Dropoff lokalizację i godzinę odbioru oraz czas, liczbę licencji () i Medallion (unikatowy identyfikator taksówki). Dane obejmują wszystkie podróże w roku 2013 i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

1. Wolumin CSV "trip_data" zawiera szczegóły dotyczące podróży, takie jak liczba pasażerów, punkty odbioru i Dropoff, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Wolumin CSV "trip_fare" zawiera szczegółowe informacje o opłatach za każdą podróż, takie jak typ płatności, kwota opłaty, opłata dodatkowa i podatki, porady i opłaty, a także łączną kwotę płatną. Oto kilka przykładowych rekordów:
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Unikatowy klucz do przyłączenia \_ danych podróży i \_ opłaty za podróż są złożone z pól: Medallion, \_ licencja na hakera i \_ Data i godzina pobrania.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Przykłady zadań przewidywania
Będziemy formułować trzy problemy z przewidywaniami na podstawie * \_ kwoty Porada*:

* Klasyfikacja binarna: przewidywanie, czy Porada została zapłacona za podróż, to oznacza, że jest to $0 *dodatnia \_ * * \_ wartość* , która jest większa niż $0
* Klasyfikacja wieloklasowa: przewidywanie zakresu porady dla podróży. Podziel * \_ kwotę* na pięć przedziałów lub klas:

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* Zadanie regresji: przewidywanie kwoty Porada płatnej dla podróży.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Konfigurowanie środowiska nauki o danych Azure na potrzeby zaawansowanej analizy
Jak widać w przewodniku [planowania środowiska](plan-your-environment.md) , istnieje kilka opcji współpracy z zestawem danych podróży NYC taksówkami na platformie Azure:

* Pracuj z danymi w obiektach Blob platformy Azure, a następnie modelem w Azure Machine Learning
* Załaduj dane do bazy danych SQL Server, a następnie model Azure Machine Learning

W tym samouczku przedstawimy równoległy import zbiorczy danych do SQL Server, eksploracji danych, inżynierii funkcji i próbkowania w dół przy użyciu SQL Server Management Studio, a także IPython notesu. [Przykładowe skrypty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) i [notesy IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) są udostępniane w serwisie GitHub. Przykładowy Notes IPython do pracy z danymi w obiektach Blob platformy Azure jest również dostępny w tej samej lokalizacji.

Aby skonfigurować środowisko nauki danych platformy Azure:

1. [Tworzenie konta magazynu](../../storage/common/storage-account-create.md)
2. [Tworzenie obszaru roboczego usługi Azure Machine Learning](../classic/create-workspace.md)
3. [Zapewnij Data Science Virtual Machine](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), który udostępnia SQL Server i serwer notesu IPython.
   
   > [!NOTE]
   > Przykładowe skrypty i notesy IPython zostaną pobrane do maszyny wirtualnej do nauki o danych podczas procesu instalacji. Po zakończeniu działania skryptu po instalacji na maszynie wirtualnej przykłady będą znajdować się w bibliotece dokumentów maszyny wirtualnej:  
   > 
   > * Przykładowe skrypty: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Przykładowe notesy IPython: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   gdzie `<user_name>` to nazwa logowania systemu Windows maszyny wirtualnej. Będziemy odwoływać się do przykładowych folderów jako **przykładowe skrypty** i **przykładowe notesy IPython**.
   > 
   > 

W oparciu o rozmiar zestawu danych, lokalizację źródła danych i wybrane środowisko docelowe platformy Azure, ten scenariusz jest podobny do [scenariusza \# 5: duży zestaw danych w plikach lokalnych, docelowy SQL Server na maszynie wirtualnej platformy Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Pobierz dane ze źródła publicznego
Aby pobrać zestaw danych [podróży z NYC taksówkami](https://www.andresmh.com/nyctaxitrips/) z lokalizacji publicznej, możesz użyć dowolnej z metod opisanych w temacie [przenoszenie danych do i z platformy Azure Blob Storage](move-azure-blob.md) , aby skopiować dane na nową maszynę wirtualną.

Aby skopiować dane przy użyciu AzCopy:

1. Zaloguj się do maszyny wirtualnej (VM)
2. Utwórz nowy katalog na dysku danych maszyny wirtualnej (Uwaga: nie używaj dysku tymczasowego, który jest dostarczany z maszyną wirtualną jako dysk danych).
3. W oknie wiersza polecenia uruchom następujący wiersz polecenia AzCopy, zastępując <path_to_data_folder> z folderem danych utworzonym w (2):

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    Po zakończeniu AzCopy, w folderze dane musi znajdować się łączna liczba 24 plików CSV (12 dla \_ danych podróży i 12 dla \_ taryfy za podróż).
4. Rozpakuj pobrane pliki. Zwróć uwagę na folder, w którym znajdują się nieskompresowane pliki. Ten folder będzie określany jako ścieżka <\_ do \_ \_ plików danych \> .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Zbiorcze importowanie danych do bazy danych SQL Server
Wydajność ładowania/przenoszenia dużych ilości danych do SQL Database i kolejnych zapytań można ulepszyć za pomocą *partycjonowanych tabel i widoków*. W tej sekcji będziemy postępować zgodnie z instrukcjami opisanymi w artykule [równoległe Importowanie danych zbiorczych przy użyciu tabel partycji SQL](parallel-load-sql-partitioned-tables.md) w celu utworzenia nowej bazy danych i załadowania danych do tabel partycjonowanych równolegle.

1. Po zalogowaniu się do maszyny wirtualnej Rozpocznij **SQL Server Management Studio**.
2. Nawiązywanie połączenia przy użyciu uwierzytelniania systemu Windows.
   
    ![Łączenie programu SSMS][12]
3. Jeśli nie zmieniono jeszcze trybu uwierzytelniania SQL Server i utworzono nowego użytkownika logowania SQL, Otwórz plik skryptu o nazwie **Zmień \_ AUTH. SQL** w folderze **przykładowe skrypty** . Zmień domyślną nazwę użytkownika i hasło. Kliknij przycisk **Execute (wykonaj** ) na pasku narzędzi, aby uruchomić skrypt.
   
    ![Wykonaj skrypt][13]
4. Sprawdź i/lub Zmień SQL Server domyślne foldery bazy danych i dziennika, aby upewnić się, że nowo utworzone bazy danych będą przechowywane na dysku z danymi. Obraz maszyny wirtualnej SQL Server zoptymalizowany pod kątem ładowania magazynu danych jest wstępnie skonfigurowany przy użyciu dysków danych i dzienników. Jeśli maszyna wirtualna nie zawiera dysku danych i dodano nowe wirtualne dyski twarde podczas procesu konfiguracji maszyny wirtualnej, Zmień foldery domyślne w następujący sposób:
   
   * Kliknij prawym przyciskiem myszy nazwę SQL Server w lewym panelu, a następnie kliknij pozycję **Właściwości**.
     
       ![Właściwości SQL Server][14]
   * Wybierz pozycję **Ustawienia bazy danych** z listy **Wybierz stronę** po lewej stronie.
   * Sprawdź i/lub Zmień **domyślne lokalizacje bazy danych** na wybrane lokalizacje **dysków danych** . W tej lokalizacji znajdują się nowe bazy danych, jeśli zostały utworzone z ustawieniami domyślnymi.
     
       ![SQL Database domyślne][15]  
5. Aby utworzyć nową bazę danych i zestaw grup plików do przechowywania partycjonowanych tabel, Otwórz przykładowy skrypt **Utwórz \_ bazę danych \_ default. SQL**. Skrypt utworzy nową bazę danych o nazwie **TaxiNYC** i 12 grupach plików w domyślnej lokalizacji danych. Każda grupa plików będzie przechowywać jeden miesiąc danych \_ i opłat za podróż \_ . W razie potrzeby zmodyfikuj nazwę bazy danych. Kliknij przycisk **Execute (wykonaj** ), aby uruchomić skrypt.
6. Następnie Utwórz dwie tabele partycji, jedną dla \_ danych podróży i drugą dla opłaty za podróż \_ . Otwórz przykładowy skrypt **Tworzenie \_ partycjonowanej \_ tabeli. SQL**, która będzie:
   
   * Utwórz funkcję partycji, aby podzielić dane na miesiąc.
   * Utwórz schemat partycji, aby zamapować dane każdego miesiąca do innej grupy plików.
   * Utwórz dwie partycjonowane tabele zamapowane na schemat partycji: ** \_ podróż nyctaxi** będzie przechowywać dane dotyczące podróży, \_ a ** \_ opłaty** za przejazd nyctaxi będą przechowywane w ramach taryfy czasowej \_ .
     
     Kliknij przycisk **Execute (wykonaj** ), aby uruchomić skrypt i utworzyć partycjonowane tabele.
7. W folderze **przykładowe skrypty** dostępne są dwa przykładowe skrypty programu PowerShell umożliwiające zaprezentowanie równoległych importów zbiorczych danych do tabel SQL Server.
   
   * Narzędzie **BCP \_ Parallel \_generic.ps1** to ogólny skrypt służący do równoległego importowania danych do tabeli. Zmodyfikuj ten skrypt, aby ustawić zmienne wejściowe i docelowe zgodnie ze wskazanymi w wierszach komentarza w skrypcie.
   * Narzędzie **BCP \_ Parallel \_nyctaxi.ps1** to wstępnie skonfigurowana wersja skryptu generycznego, której można użyć do załadowania obu tabel dla danych podróży NYC.  
8. Kliknij prawym przyciskiem myszy nazwę skryptu narzędzia **BCP \_ Parallel \_nyctaxi.ps1** i kliknij przycisk **Edytuj** , aby otworzyć go w programie PowerShell. Przejrzyj wstępnie zdefiniowane zmienne i zmodyfikuj je zgodnie z wybraną nazwą bazy danych, folderem danych wejściowych, docelowym folderem dziennika i ścieżkami do plików z przykładowym formatem **nyctaxi_trip.xml** i **nyctaxi \_fare.xml** (dostępne w folderze **przykładowe skrypty** ).
   
    ![Importuj zbiorczo dane][16]
   
    Można również wybrać tryb uwierzytelniania, domyślnie jest uwierzytelnianie systemu Windows. Kliknij zieloną strzałkę na pasku narzędzi, aby uruchomić. Skrypt uruchomi 24 zbiorcze operacje importu równolegle, 12 dla każdej partycjonowanej tabeli. Postęp importowania danych można monitorować, otwierając SQL Server domyślnego folderu danych zgodnie z powyższym ustawieniem.
9. Skrypt programu PowerShell zgłasza czas rozpoczęcia i zakończenia. Po zakończeniu wszystkich importów zbiorczych jest raportowany czas zakończenia. Sprawdź docelowy folder dziennika, aby sprawdzić, czy zbiorcze Importy zostały pomyślne, czyli nie zgłoszono błędów w docelowym folderze dziennika.
10. Baza danych jest teraz gotowa do eksploracji, Inżynieria funkcji i innych operacji. Ponieważ tabele są partycjonowane według pola ** \_ daty i godziny pobrania** , zapytania, które zawierają warunki **w klauzuli** ** \_ DateTime do pobrania** , będą korzystać z schematu partycji.
11. W **SQL Server Management Studio**poznanie dostarczonego przykładowego skryptu przykładowego ** \_ zapytania. SQL**. Aby uruchomić dowolne z przykładowych zapytań, zaznacz wiersze zapytania, a następnie kliknij przycisk **Execute (wykonaj** ) na pasku narzędzi.
12. Dane podróży z NYC taksówki są ładowane w dwóch oddzielnych tabelach. Aby poprawić operacje join, zdecydowanie zaleca się indeksowanie tabel. Przykładowy skrypt **tworzy \_ partycjonowany \_ indeks. SQL** tworzy partycjonowane indeksy na Medallion kluczowej sprzężenia, licencji na naprawę **i dacie \_ pobrania \_ **.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Eksploracja danych i inżynieria funkcji w SQL Server
W tej sekcji wykonamy eksplorowanie i generowanie funkcji, uruchamiając zapytania SQL bezpośrednio w **SQL Server Management Studio** przy użyciu utworzonej wcześniej bazy danych SQL Server. Przykładowy skrypt o nazwie **przykładowe \_ zapytania. SQL** jest dostępny w folderze **przykładowe skrypty** . Zmodyfikuj skrypt, aby zmienić nazwę bazy danych, jeśli różni się od domyślnego: **TaxiNYC**.

W tym ćwiczeniu będziemy:

* Połącz się z **SQL Server Management Studio** przy użyciu uwierzytelniania systemu Windows lub uwierzytelniania SQL i nazwy logowania SQL i hasła.
* Eksplorowanie dystrybucji danych kilku pól w różnych oknach czasu.
* Zbadaj jakość danych pól długości i szerokości geograficznej.
* Generowanie etykiet klasyfikacji danych binarnych i wieloklasowych na podstawie ** \_ kwoty**pozostałej.
* Generuj funkcje i odległość wyjazdu obliczeniowego/porównania.
* Dołącz dwie tabele i Wyodrębnij losową próbkę, która będzie używana do kompilowania modeli.

Gdy wszystko będzie gotowe do przejścia do Azure Machine Learning, możesz wykonać następujące czynności:  

1. Zapisz ostateczną kwerendę SQL w celu wyodrębnienia i próbkowania danych i skopiowania zapytania bezpośrednio do modułu [importowania danych][import-data] w Azure Machine Learning lub
2. Utrwalaj próbkowane i opracowane dane, które planujesz użyć do kompilowania modelu w nowej tabeli bazy danych, i użyj nowej tabeli w module [Importuj dane][import-data] w Azure Machine Learning.

W tej sekcji zapiszemy ostateczną kwerendę, aby wyodrębnić i próbkować dane. Druga metoda jest przedstawiona w sekcji " [Eksploracja danych i inżynieria funkcji" w notesie IPython](#ipnb) .

Aby uzyskać szybką weryfikację liczby wierszy i kolumn w tabelach wypełnionych wcześniej przy użyciu równoległego importowania zbiorczego,

- Zgłoś liczbę wierszy w tabeli nyctaxi_trip bez skanowania tabeli: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- Raportuj liczbę kolumn w tabeli nyctaxi_trip: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>Eksploracja: dystrybucja podróży według Medallion
W tym przykładzie zidentyfikowano Medallion (liczby taksówki) z ponad 100 podróży w danym okresie. Zapytanie jest korzystne z poziomu dostępu do partycjonowanej tabeli, ponieważ jest ono warunkiem schematu partycji typu ** \_ DateTime**. Wykonywanie zapytania dotyczącego pełnego zestawu danych spowoduje również użycie partycjonowanej tabeli i/lub skanowania indeksu.

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Eksploracja: dystrybucja podróży według Medallion i hack_license

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Ocena jakości danych: Weryfikuj rekordy z nieprawidłową długością geograficzną i/lub szerokością geograficzną
Ten przykład sprawdza, czy którekolwiek z pól długości geograficznej i/lub szerokości zawiera nieprawidłową wartość (w radianach stopni powinna należeć do zakresu od-90 do 90), czy ma (0, 0) współrzędnych.

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Eksploracja: rozchylony a przechylony
Ten przykład wyszukuje liczbę podróży, które zostały przechylone i nie zostały przechylone w danym okresie (lub w pełnym zestawie danych, jeśli obejmują cały rok). Ta dystrybucja odzwierciedla rozkład etykiet binarnych, który będzie później używany do modelowania klasyfikacji binarnej.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>Eksploracja: rozmieszczenie klasy/zakresu etykiet
Ten przykład oblicza rozkład zakresów etykiet w danym okresie (lub w pełnym zestawie danych, jeśli obejmują cały rok). Ta dystrybucja klas etykiet zostanie później użyta w przypadku modelowania klasyfikacji wieloklasowej.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>Eksploracja: Obliczanie i porównywanie odległości podróży
Ten przykład konwertuje czas odbioru i Dropoff oraz szerokość geograficzną na punkty geograficzne SQL, oblicza odległość podróży przy użyciu różnic punktów geograficznych SQL i zwraca losową próbkę wyników do porównania. Przykład ogranicza wyniki do prawidłowych współrzędnych tylko przy użyciu zapytania oceny jakości danych pokrytego wcześniej.

```sql
SELECT
pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
,trip_distance
,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
FROM nyctaxi_trip
tablesample(0.01 percent)
WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

#### <a name="feature-engineering-in-sql-queries"></a>Inżynieria funkcji w zapytaniach SQL
Zapytania eksploracji generacji etykiet i przeprowadzenia konwersji geografii mogą również służyć do generowania etykiet/funkcji przez usunięcie części zliczania. Dodatkowe przykłady SQL dla inżynierów funkcji są dostępne w sekcji [eksplorowanie i opracowywanie funkcji w notesie IPython](#ipnb) . Bardziej wydajne jest uruchamianie zapytań generacji funkcji na pełnym zestawie danych lub w dużym podzestawie przy użyciu zapytań SQL, które są uruchamiane bezpośrednio w wystąpieniu bazy danych SQL Server. Zapytania mogą być wykonywane w **SQL Server Management Studio**, IPython notesie lub dowolnym narzędziu programistycznym lub środowisku, które może uzyskać dostęp do bazy danych lokalnie lub zdalnie.

#### <a name="preparing-data-for-model-building"></a>Przygotowywanie danych do kompilowania modelu
Następujące zapytanie sprzęga **nyctaxie \_ ** i **nyctaxi opłaty za \_ przejazd** , generuje binarną etykietę klasyfikacji, ** \_ klasę**etykietki klasyfikacji z wieloklasową **, a**następnie wyodrębnia losowo 1% próbek z pełnego dołączonego zestawu danych. To zapytanie można skopiować, a następnie wkleić bezpośrednio do modułu [Azure Machine Learning Studio](https://studio.azureml.net) [Importowanie danych][import-data] w celu bezpośredniego pozyskiwania danych z wystąpienia bazy danych SQL Server na platformie Azure. Zapytanie wyklucza rekordy z nieprawidłowymi współrzędnymi (0, 0).

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_trip t, nyctaxi_fare f
TABLESAMPLE (1 percent)
WHERE t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Eksploracja danych i inżynieria funkcji w notesie IPython
W tej sekcji będziemy przetwarzać eksplorację danych i generować funkcję przy użyciu zapytań Python i SQL dla utworzonej wcześniej bazy danych SQL Server. Przykładowy Notes IPython o nazwie **Machine-Learning-Data-nauka-Process-SQL-historie. ipynb** jest dostępny w folderze **Sample notesy IPython** . Ten Notes jest również dostępny w witrynie [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Podczas pracy z danymi Big Data postępuj zgodnie z tą zalecaną sekwencją:

* Zapoznaj się z małą próbką danych do ramki danych znajdującej się w pamięci.
* Wykonaj wizualizacje i eksploracje przy użyciu danych próbkowanych.
* Eksperymentuj z funkcjami inżynierii przy użyciu danych próbkowanych.
* Aby uzyskać więcej eksploracji danych, manipulowania danymi i inżynierii funkcji, Użyj języka Python, aby wystawiać zapytania SQL bezpośrednio z bazą danych SQL Server na maszynie wirtualnej platformy Azure.
* Określ rozmiar próbki, który ma być używany do kompilowania modelu Azure Machine Learning.

Gdy wszystko będzie gotowe do przejścia do Azure Machine Learning, możesz wykonać następujące czynności:  

1. Zapisz ostateczną kwerendę SQL w celu wyodrębnienia i próbkowania danych i skopiowania zapytania bezpośrednio do modułu [importowania danych][import-data] w Azure Machine Learning. Ta metoda jest przedstawiona w sekcji [Tworzenie modeli w Azure Machine Learning](#mlmodel) .    
2. Utrwalaj próbkowane i zaprojektowane dane, które planujesz użyć do kompilowania modelu w nowej tabeli bazy danych, a następnie użyj nowej tabeli w module [Importuj dane][import-data] .

Poniżej przedstawiono kilka przykładów eksploracji danych, wizualizacji danych i inżynierów funkcji. Aby uzyskać więcej przykładów, zobacz przykładowy Notes SQL IPython w folderze **Sample Notess IPython** .

#### <a name="initialize-database-credentials"></a>Zainicjuj poświadczenia bazy danych
Zainicjuj ustawienia połączenia z bazą danych w następujących zmiennych:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>Utwórz połączenie z bazą danych

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Liczba raportów o liczbie wierszy i kolumn w tabeli nyctaxi_trip

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('nyctaxi_trip')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('nyctaxi_trip')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Łączna liczba wierszy = 173179759  
* Łączna liczba kolumn = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Zapoznaj się z niewielką próbką danych z bazy danych SQL Server

```sql
t0 = time.time()

query = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (0.05 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

Czas odczytu tabeli przykładowej to 6,492000 sekund  
Liczba pobranych wierszy i kolumn = (84952, 21)

#### <a name="descriptive-statistics"></a>Statystyki opisowe
Teraz możesz eksplorować dane próbkowane. Zaczynamy od przejrzenia statystyk opisowych dla ** \_ odległości podróży** (lub dowolnego innego) pola:

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>Wizualizacja: przykład wykresu skrzynkowego
Następnie Spójrzmy na wykres w polu odległość podróży, aby wizualizować quantiles

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![#1 wykresu][1]

#### <a name="visualization-distribution-plot-example"></a>Wizualizacja: przykład wykresu dystrybucji

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![#2 wykresu][2]

#### <a name="visualization-bar-and-line-plots"></a>Wizualizacja: wykresy słupkowe i liniowe
W tym przykładzie przedziały czas podróży do pięciu pojemników i wizualizacji wyników pakowania.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Można wykreślić powyższą dystrybucję pojemników na słupku lub linii liniowych jak poniżej

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![#3 wykresu][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![#4 wykresu][4]

#### <a name="visualization-scatterplot-example"></a>Wizualizacja: przykład Scatterplot
Pokazujemy wykres punktowy **między \_ czasem podróży \_ w \_ sekundach** i ** \_ odległości podróży** , aby sprawdzić, czy istnieje korelacja

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![#6 wykresu][6]

Podobnie możemy sprawdzić relację między ** \_ kodem częstotliwości** i ** \_ odległością podróży**.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![#8 wykresu][8]

### <a name="sub-sampling-the-data-in-sql"></a>Sub-Sampling danych w SQL
Podczas przygotowywania danych do kompilowania modeli w [Azure Machine Learning Studio](https://studio.azureml.net)można zdecydować, czy **zapytanie SQL ma być używane bezpośrednio w module Importuj dane** , czy utrwalać przetworzone i próbkowane dane w nowej tabeli, której można użyć w module [Import danych][import-data] z prostym **SELECT * FROM <\_ nową \_ \_ nazwą tabeli>**.

W tej sekcji utworzymy nową tabelę do przechowywania danych próbkowanych i przetworzonych. Przykład bezpośredniej kwerendy SQL na potrzeby konstruowania modelu znajduje się w sekcji [Eksploracja danych i inżynieria funkcji w SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Utwórz przykładową tabelę i wypełnij ją 1% sprzężonych tabel. Najpierw Porzuć tabelę, jeśli istnieje.
W tej sekcji dołączymy tabele **nyctaxie \_ ** i ** \_ opłaty za nyctaxi**, Wyodrębnij losową próbę z 1% i Utrwalaj dane przykładowe w nowej tabeli o nazwie **nyctaxi 1 \_ \_ procent**:

```sql
cursor = conn.cursor()

drop_table_if_exists = '''
    IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
'''

nyctaxi_one_percent_insert = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
    INTO nyctaxi_one_percent
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
'''

cursor.execute(drop_table_if_exists)
cursor.execute(nyctaxi_one_percent_insert)
cursor.commit()
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Eksploracja danych przy użyciu zapytań SQL w notesie IPython
W tej sekcji eksplorujemy dystrybucje danych przy użyciu danych z próbką 1%, które są utrwalane w nowo utworzonej tabeli. Podobne eksploracje mogą być wykonywane przy użyciu oryginalnych tabel, **opcjonalnie przy użyciu** tabeli odnoszącej, aby ograniczyć próbkę eksploracji lub ograniczając wyniki do danego przedziału czasu przy użyciu partycji ** \_ DateTime odbioru** , jak pokazano w sekcji [Eksploracja danych i inżynieria funkcji w SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Eksploracja: codzienne rozpowszechnianie podróży

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Eksploracja: rozkład podróży na Medallion

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generowanie funkcji przy użyciu zapytań SQL w notesie IPython
W tej sekcji wygenerujemy nowe etykiety i funkcje bezpośrednio przy użyciu zapytań SQL, które działają w tabeli przykładowej 1% utworzonej w poprzedniej sekcji.

#### <a name="label-generation-generate-class-labels"></a>Generowanie etykiety: generowanie etykiet klas
W poniższym przykładzie Wygenerowano dwa zestawy etykiet do użycia podczas modelowania:

1. Przerzucane **tipped** etykiety klas binarnych (przewidywanie, jeśli zostanie podaną wskazówką)
2. ** \_ Klasa** etykiet dla wielu klas (przewidywanie pojemnika lub zakresu końcówki)

```sql   
    nyctaxi_one_percent_add_col = '''
        ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
    '''
   
    cursor.execute(nyctaxi_one_percent_add_col)
    cursor.commit()
   
    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET
           tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
           tip_class = CASE WHEN (tip_amount = 0) THEN 0
                            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                            ELSE 4
                        END
    '''
   
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Inżynieria funkcji: liczba funkcji dla kolumn kategorii
Ten przykład przekształca pole kategorii w pole liczbowe, zastępując każdą kategorię liczbą wystąpień w danych.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B AS
    (
        SELECT medallion, hack_license,
            SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
            SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
        FROM nyctaxi_one_percent
        GROUP BY medallion, hack_license
    )

    UPDATE nyctaxi_one_percent
    SET nyctaxi_one_percent.cmt_count = B.cmt_count,
        nyctaxi_one_percent.vts_count = B.vts_count
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion AND A.hack_license = B.hack_license
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Inżynieria funkcji: funkcje bin dla kolumn liczbowych
Ten przykład przekształca ciągłe pole liczbowe do wstępnie ustawionych zakresów kategorii, czyli Przekształć pole numeryczne w pole kategorii.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
    (
        SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
        NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
    )

    UPDATE nyctaxi_one_percent
    SET trip_time_bin = B.BinNumber
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion
    AND A.hack_license = B.hack_license
    AND A.pickup_datetime = B.pickup_datetime
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Inżynieria funkcji: Wyodrębnij funkcje lokalizacji z dziesiętnej szerokości/długości geograficznej
Ten przykład dzieli dziesiętną reprezentację pola szerokości geograficznej i/lub długości geograficznej na kilka regionów pól o różnych stopnia szczegółowości, takich jak kraj/region, miasto, miejscowość, blok itp. Nowe pola geograficzne nie są zamapowane na rzeczywiste lokalizacje. Aby uzyskać informacje na temat mapowania lokalizacji geokodu, zobacz [usługi Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent
    ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
        l5 varchar(3), l6 varchar(3), l7 varchar(3)
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    UPDATE nyctaxi_one_percent
    SET l1=round(pickup_longitude,0)
        , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
        , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
        , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
        , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
        , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
        , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Sprawdź ostateczną formę tabeli featurized

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

Teraz możemy przystąpić do tworzenia modeli i wdrażania modeli w [Azure Machine Learning](https://studio.azureml.net). Dane są gotowe do dowolnego z wymienionych wcześniej problemów przewidywania, mianowicie:

1. Klasyfikacja binarna: w celu przewidywania, czy Porada została zapłacona za podróż.
2. Klasyfikacja wieloklasowa: aby przewidzieć zakres płatnej porady zgodnie z wcześniej zdefiniowanymi klasami.
3. Zadanie regresji: przewidywanie kwoty Porada płatnej dla podróży.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Kompilowanie modeli w Azure Machine Learning
Aby rozpocząć ćwiczenie modelowania, zaloguj się do obszaru roboczego Azure Machine Learning. Jeśli nie utworzono jeszcze obszaru roboczego uczenia maszynowego, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](../classic/create-workspace.md).

1. Aby rozpocząć pracę z Azure Machine Learning, zobacz [co to jest Azure Machine Learning Studio?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net).
3. Strona główna programu Studio zawiera mnóstwo informacji, filmów wideo, samouczków, linków do odwołań do modułów i innych zasobów. Aby uzyskać więcej informacji na temat Azure Machine Learning, zapoznaj się z [centrum dokumentacji Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typowy eksperyment szkoleniowy składa się z następujących kroków:

1. Utwórz **Nowy** eksperyment.
2. Pobierz dane do Azure Machine Learning.
3. Przed przetworzeniem i przekształcaniem danych oraz manipulowanie nimi.
4. Generuj funkcje zgodnie z wymaganiami.
5. Podziel dane na szkolenia/sprawdzanie poprawności/testowanie zestawów danych (lub Podziel zestawy danych dla każdego z nich).
6. Wybierz co najmniej jeden algorytm uczenia maszynowego w zależności od problemu szkoleniowego do rozwiązania. Na przykład klasyfikacja binarna, klasyfikacja wieloklasowa, regresja.
7. Uczenie jednego lub kilku modeli przy użyciu zestawu danych szkoleniowych.
8. Ocena zestawu danych walidacji przy użyciu przeszkolonych modeli.
9. Oceń modele, aby obliczyć odpowiednie metryki dla problemu szkoleniowego.
10. Dostosuj modele i wybierz najlepszy model do wdrożenia.

W tym ćwiczeniu zostały już omówione i zaprojektowane dane w SQL Server i podjęto decyzję o wielkości próbki do pozyskania w Azure Machine Learning. W celu utworzenia co najmniej jednego modelu predykcyjnego postanowiono:

1. Pobierz dane do Azure Machine Learning przy użyciu modułu [Importuj dane][import-data] dostępne w sekcji **dane wejściowe i wyjściowe** . Aby uzyskać więcej informacji, zobacz stronę odwołania modułu [importowania danych][import-data] .
   
    ![Azure Machine Learning Importuj dane][17]
2. Wybierz **Azure SQL Database** jako **Źródło danych** w panelu **Właściwości** .
3. Wprowadź nazwę DNS bazy danych w polu **Nazwa serwera bazy danych** . Formatowanie `tcp:<your_virtual_machine_DNS_name>,1433`
4. Wprowadź **nazwę bazy danych** w odpowiednim polu.
5. Wprowadź **nazwę użytkownika SQL** w polu **nazwa konta użytkownika serwera**i **hasło** w polu **hasło konta użytkownika serwera**.
7. W obszarze tekst **kwerendy bazy danych** Edytuj zapytanie, które wyodrębnia niezbędne pola bazy danych (w tym wszystkie pola obliczane, takie jak etykiety), i w dół próbkuje dane do żądanego rozmiaru próbki.

Przykładem eksperymentu klasyfikacji binarnej odczytującego dane bezpośrednio z bazy danych SQL Server znajduje się na poniższej ilustracji. Podobne eksperymenty mogą być zbudowane w przypadku problemów klasyfikacji i regresji wieloklasowej.

![Uczenie Azure Machine Learning][10]

> [!IMPORTANT]
> W zapytaniach dotyczących wyodrębniania i próbkowania danych modelowania, które zostały podane w poprzednich sekcjach, **wszystkie etykiety dla trzech ćwiczeń modelowania są zawarte w zapytaniu**. Ważne (wymagane) krok w każdym z ćwiczeń modelowania polega na **wykluczeniu** niepotrzebnych etykiet dla innych dwóch problemów oraz wszelkich innych **przecieków docelowych**. Na przykład, w przypadku używania klasyfikacji binarnej, użyj etykiety **przechylonej** i Wyklucz ** \_ klasę etykietki**pól, ** \_ kwotę wskazówki**i **łączną \_ kwotę**. Te ostatnie są wyciekami docelowymi, ponieważ implikują zapłacone wskazówki.
> 
> Aby wykluczyć niepotrzebne kolumny i/lub wycieki docelowe, możesz użyć modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ) lub [edytować metadane][edit-metadata]. Aby uzyskać więcej informacji, zobacz [Wybieranie kolumn w zestawie danych][select-columns] i edytowanie stron odwołań do [metadanych][edit-metadata] .
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Wdrażanie modeli w Azure Machine Learning
Gdy model jest gotowy, możesz go łatwo wdrożyć jako usługę sieci Web bezpośrednio z eksperymentu. Aby uzyskać więcej informacji na temat wdrażania usług sieci Web Azure Machine Learning, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).

Aby wdrożyć nową usługę sieci Web, należy:

1. Utwórz eksperyment oceniania.
2. Wdróż usługę sieci Web.

Aby utworzyć eksperyment oceniania z **gotowego** eksperymentu szkoleniowego, kliknij pozycję **Utwórz eksperyment oceniania** na niższym pasku akcji.

![Ocenianie platformy Azure][18]

Azure Machine Learning podejmie próbę utworzenia eksperymentu oceniania na podstawie składników eksperymentu szkoleniowego. W szczególności będzie:

1. Zapisz przeszkolony model i Usuń moduły szkoleń modeli.
2. Określ logiczny **port wejściowy** reprezentujący oczekiwany schemat danych wejściowych.
3. Określ logiczny **port wyjściowy** reprezentujący oczekiwany schemat danych wyjściowych usługi sieci Web.

Gdy zostanie utworzony eksperyment oceniania, przejrzyj go i Dostosuj odpowiednio do wymagań. Typowym dopasowaniem jest zastępowanie wejściowego zestawu danych i/lub zapytania, które wyklucza pola etykiety, ponieważ te etykiety nie będą dostępne w schemacie, gdy usługa zostanie wywołana. Dobrym sposobem jest zmniejszenie rozmiaru wejściowego zestawu danych i/lub zapytania do kilku rekordów, wystarczająco że wskazuje schemat wejściowy. Dla portu wyjściowego często wyklucza wszystkie pola wejściowe i zawiera tylko **etykiety z wynikami** i **oceny prawdopodobieństwa** w danych wyjściowych za pomocą modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych][select-columns] ).

Przykładowy eksperyment oceniania znajduje się na poniższej ilustracji. Gdy wszystko będzie gotowe do wdrożenia, kliknij przycisk **Publikuj usługę sieci Web** na dolnym pasku akcji.

![Azure Machine Learning publikowanie][11]

Do Podsumowanie w tym samouczku przedstawiono tworzenie środowiska nauki o danych platformy Azure, w którym działa duży publiczny zestaw danych, w ramach pozyskiwania danych do modelowania szkoleń i wdrażania usługi sieci Web Azure Machine Learning.

### <a name="license-information"></a>Informacje o licencji
Ten przykładowy przewodnik i towarzyszące mu skrypty i notesy IPython są udostępniane przez firmę Microsoft w ramach licencji MIT. Aby uzyskać więcej informacji, zapoznaj się z plikiem LICENSE.txt w katalogu przykładowego kodu w witrynie GitHub.

### <a name="references"></a>Odwołania
•    [Strona pobierania Andrés MONROY NYC TRIPS](https://www.andresmh.com/nyctaxitrips/)  
•    [Dane dotyczące podróży z NYCą w folii przez Krzysztof Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [Badania i statystyka NYCych taksówki oraz Komisji Limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
