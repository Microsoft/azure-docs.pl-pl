---
title: Identyfikowanie scenariuszy dla procesu nauka danych w zespole Azure Machine Learning
description: Wybierz odpowiednie scenariusze w celu przeprowadzenia zaawansowanej analizy predykcyjnej przy użyciu procesu nauki o danych zespołowych.
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
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93319298"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenariusze zaawansowanej analizy w usłudze Azure Machine Learning
W tym artykule opisano różne przykładowe źródła danych i scenariusze docelowe, które mogą być obsługiwane przez [zespół ds. analizy danych (przetwarzania TDSP)](overview.md). PRZETWARZANIA TDSP zawiera systematyczne podejście dla zespołów do współpracy nad tworzeniem inteligentnych aplikacji. Scenariusze przedstawione w tym miejscu ilustrują opcje dostępne w przepływie pracy przetwarzania danych, które są zależne od charakterystyki danych, lokalizacji źródłowych i repozytoriów docelowych na platformie Azure.

**Drzewo decyzyjne** służące do wybierania przykładowych scenariuszy, które są odpowiednie dla danych i celu, przedstawiono w ostatniej sekcji.

Każda z poniższych sekcji przedstawia przykładowy scenariusz. W każdym scenariuszu jest wyświetlany możliwy przepływ analizy danych lub zaawansowanego przetwarzania oraz obsługa zasobów platformy Azure.

> [!NOTE]
> **W przypadku wszystkich następujących scenariuszy należy wykonać następujące czynności:**
> <br/>
> 
> * [Tworzenie konta magazynu](../../storage/common/storage-account-create.md)
>   <br/>
> * [Tworzenie obszaru roboczego usługi Azure Machine Learning](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Scenariusz \# 1: mały i średni tabelaryczny zestaw danych w plikach lokalnych
![Małe i średnie pliki lokalne][1]

#### <a name="additional-azure-resources-none"></a>Dodatkowe zasoby platformy Azure: brak
1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Przekaż zestaw danych.
1. Kompiluj przepływ eksperymentu Azure Machine Learning, rozpoczynając od przekazanych zestawów danych.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Scenariusz \# 2: mały i średni zestaw danych dla plików lokalnych, które wymagają przetwarzania
![Małe i średnie pliki lokalne z przetwarzaniem][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer IPython Notes)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym notesem IPython.
1. Przekaż dane do kontenera usługi Azure Storage.
1. Wstępne przetwarzanie i czyszczenie danych w notesie IPython, uzyskiwanie dostępu do danych z kontenera usługi Azure Storage.
1. Przekształcanie danych w oczyszczoną postać tabelaryczną.
1. Zapisuj przekształcone dane w obiektach Blob platformy Azure.
1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytaj dane z obiektów blob platformy Azure przy użyciu modułu [Importuj dane][import-data] .
1. Kompiluj Azure Machine Learning przepływ eksperymentu, rozpoczynając od pozyskiwanych zestawów danych.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Scenariusz \# 3: duży zestaw danych plików lokalnych, przeznaczony dla obiektów blob platformy Azure
![Duże pliki lokalne][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer IPython Notes)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym notesem IPython.
1. Przekaż dane do kontenera usługi Azure Storage.
1. Wstępne przetwarzanie i czyszczenie danych w notesie IPython, uzyskiwanie dostępu do danych z obiektów blob platformy Azure.
1. Przekształć dane na wyczyszczony formularz tabelaryczny, jeśli jest to konieczne.
1. Eksplorowanie danych i tworzenie funkcji zgodnie z wymaganiami.
1. Wyodrębnij przykład małych i średnich danych.
1. Zapisz dane próbkowane w obiektach Blob platformy Azure.
1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytaj dane z obiektów blob platformy Azure przy użyciu modułu [Importuj dane][import-data] .
1. Kompilacja Azure Machine Learning przepływ eksperymentu, rozpoczynając od pozyskanych zestawów danych.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Scenariusz \# 4: mały i średni zestaw danych dla plików lokalnych, ukierunkowany SQL Server na maszynę wirtualną platformy Azure
![Małe i średnie lokalne pliki do bazy danych SQL na platformie Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer notesu SQL Server/IPython)
1. Utwórz maszynę wirtualną platformy Azure z systemem SQL Server i notesem IPython.
1. Przekaż dane do kontenera usługi Azure Storage.
1. Wstępne przetwarzanie i czyszczenie danych w kontenerze usługi Azure Storage za pomocą notesu IPython.
1. Przekształć dane na wyczyszczony formularz tabelaryczny, jeśli jest to konieczne.
1. Zapisywanie danych na maszynie wirtualnej — pliki lokalne (IPython Notes jest uruchomiony na maszynie wirtualnej, dyski lokalne odnoszą się do dysków maszyn wirtualnych).
1. Załaduj dane do bazy danych SQL Server działającej na maszynie wirtualnej platformy Azure.
   
   Opcja \# 1: używanie SQL Server Management Studio.
   
   * Logowanie do SQL Server maszyny wirtualnej
   * Uruchom SQL Server Management Studio.
   * Utwórz bazę danych i tabele docelowe.
   * Użyj jednej z metod importu zbiorczego, aby załadować dane z plików lokalnych maszyn wirtualnych.
   
   Opcja \# 2: korzystanie z notesu IPython — nie jest to zalecane dla średnich i większych zestawów danych
   
   <!-- -->    
   * Użyj parametrów połączenia ODBC, aby uzyskać dostęp do SQL Server na maszynie wirtualnej.
   * Utwórz bazę danych i tabele docelowe.
   * Użyj jednej z metod importu zbiorczego, aby załadować dane z plików lokalnych maszyn wirtualnych.
1. Eksploruj dane, Utwórz funkcje zgodnie z wymaganiami. Funkcje te nie muszą być w tabelach baz danych. Należy zwrócić uwagę na to, aby je utworzyć.
1. Określ rozmiar próbki danych, w razie potrzeby i/lub żądany.
1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytaj dane bezpośrednio z SQL Server przy użyciu modułu [Importuj dane][import-data] . Wklej wymagane zapytanie, które wyodrębnia pola, tworzy funkcje i próbkuje dane, jeśli jest to konieczne bezpośrednio w kwerendzie [Importuj dane][import-data] .
1. Kompilacja Azure Machine Learning przepływ eksperymentu, rozpoczynając od pozyskanych zestawów danych.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Scenariusz \# 5: duży zestaw danych w plikach lokalnych, docelowy SQL Server na maszynie wirtualnej platformy Azure
![Duże pliki lokalne do bazy danych SQL na platformie Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer notesu SQL Server/IPython)
1. Utwórz maszynę wirtualną platformy Azure z systemem SQL Server i serwerem notesu IPython.
1. Przekaż dane do kontenera usługi Azure Storage.
1. Obowiązkowe Wstępne przetwarzanie i czyszczenie danych.
   
    a.  Wstępne przetwarzanie i czyszczenie danych w notesie IPython, uzyskiwanie dostępu do danych z obiektów blob platformy Azure.
   
    b.  Przekształć dane na wyczyszczony formularz tabelaryczny, jeśli jest to konieczne.
   
    c.  Zapisywanie danych na maszynie wirtualnej — pliki lokalne (IPython Notes jest uruchomiony na maszynie wirtualnej, dyski lokalne odnoszą się do dysków maszyn wirtualnych).
1. Załaduj dane do bazy danych SQL Server działającej na maszynie wirtualnej platformy Azure.
   
    a.  Zaloguj się do SQL Server maszyny wirtualnej.
   
    b.  Jeśli dane nie zostały już zapisane, Pobierz pliki danych z kontenera usługi Azure Storage do folderu Local-VM.
   
    c.  Uruchom SQL Server Management Studio.
   
    d.  Utwórz bazę danych i tabele docelowe.
   
    e.  Aby załadować dane, użyj jednej z metod importu zbiorczego.
   
    f.  Jeśli są wymagane sprzężenia tabeli, Utwórz indeksy, aby przyspieszyć sprzężenia.
   
   > [!NOTE]
   > W celu szybszego ładowania dużych rozmiarów danych zaleca się tworzenie partycjonowanych tabel i zbiorcze Importowanie danych. Aby uzyskać więcej informacji, zobacz [Importowanie danych równoległych do tabel partycjonowanych SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Eksploruj dane, Utwórz funkcje zgodnie z wymaganiami. Funkcje te nie muszą być w tabelach baz danych. Należy zwrócić uwagę na to, aby je utworzyć.
1. Określ rozmiar próbki danych, w razie potrzeby i/lub żądany.
1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytaj dane bezpośrednio z SQL Server przy użyciu modułu [Importuj dane][import-data] . Wklej wymagane zapytanie, które wyodrębnia pola, tworzy funkcje i próbkuje dane, jeśli jest to konieczne bezpośrednio w kwerendzie [Importuj dane][import-data] .
1. Prosty przepływ eksperymentu Azure Machine Learning rozpoczynający się od przekazanego zestawu danych

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Scenariusz \# 6: duży zestaw danych w bazie danych SQL Server lokalnie, docelowy SQL Server na maszynie wirtualnej platformy Azure
![Duża baza danych SQL w systemie Premium do bazy danych SQL na platformie Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer notesu SQL Server/IPython)
1. Utwórz maszynę wirtualną platformy Azure z systemem SQL Server i serwerem notesu IPython.
1. Użyj jednej z metod eksportu danych, aby wyeksportować dane z SQL Server do plików zrzutu.
   
   > [!NOTE]
   > Jeśli zdecydujesz się przenieść wszystkie dane z lokalnej bazy danych, alternatywna (szybsza) Metoda przenoszenia pełnej bazy danych do wystąpienia SQL Server na platformie Azure. Pomiń procedurę eksportowania danych, tworzenia bazy danych i ładowania/importowania danych do docelowej bazy danych i postępuj zgodnie z alternatywną metodą.
   > 
   > 
1. Przekaż pliki zrzutu do kontenera usługi Azure Storage.
1. Załaduj dane do bazy danych SQL Server działającej na maszynie wirtualnej platformy Azure.
   
   a.  Zaloguj się do maszyny wirtualnej SQL Server.
   
   b.  Pobierz pliki danych z kontenera usługi Azure Storage do folderu Local-VM.
   
   c.  Uruchom SQL Server Management Studio.
   
   d.  Utwórz bazę danych i tabele docelowe.
   
   e.  Aby załadować dane, użyj jednej z metod importu zbiorczego.
   
   f.  Jeśli są wymagane sprzężenia tabeli, Utwórz indeksy, aby przyspieszyć sprzężenia.
   
   > [!NOTE]
   > Aby przyspieszyć ładowanie dużych rozmiarów danych, Utwórz partycjonowane tabele i zbiorczo Importuj dane równolegle. Aby uzyskać więcej informacji, zobacz [Importowanie danych równoległych do tabel partycjonowanych SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Eksploruj dane, Utwórz funkcje zgodnie z wymaganiami. Funkcje te nie muszą być w tabelach baz danych. Należy zwrócić uwagę na to, aby je utworzyć.
1. Określ rozmiar próbki danych, w razie potrzeby i/lub żądany.
1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytaj dane bezpośrednio z SQL Server przy użyciu modułu [Importuj dane][import-data] . Wklej wymagane zapytanie, które wyodrębnia pola, tworzy funkcje i próbkuje dane, jeśli jest to konieczne bezpośrednio w kwerendzie [Importuj dane][import-data] .
1. Prosty przepływ eksperymentu Azure Machine Learning rozpoczynający się od przekazanego zestawu danych.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatywna metoda kopiowania pełnej bazy danych z SQL Server lokalnego do Azure SQL Database
![Odłączanie lokalnej bazy danych i dołączanie do bazy danych SQL na platformie Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: maszyna wirtualna platformy Azure (serwer notesu SQL Server/IPython)
Aby zreplikować całą bazę danych SQL Server na maszynie wirtualnej SQL Server, należy skopiować bazę danych z jednej lokalizacji/serwera do innej, przy założeniu, że baza danych może zostać przełączona do trybu offline. Możesz użyć Eksplorator obiektów SQL Server Management Studio, lub użyć równoważnych poleceń języka Transact-SQL.

1. Odłącz bazę danych w lokalizacji źródłowej. Aby uzyskać więcej informacji, zobacz [Odłączanie bazy danych](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. W Eksploratorze Windows lub w oknie wiersza polecenia systemu Windows skopiuj odłączony plik lub pliki bazy danych oraz plik dziennika lub pliki do lokalizacji docelowej na maszynie wirtualnej SQL Server na platformie Azure.
1. Dołącz skopiowane pliki do docelowego wystąpienia SQL Server. Aby uzyskać więcej informacji, zobacz [dołączanie bazy danych](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Przenoszenie bazy danych przy użyciu funkcji odłączania i dołączania (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Scenariusz \# 7: dane Big Data w plikach lokalnych, docelowa baza danych programu Hive w klastrach Azure HDInsight Hadoop
![Dane Big Data w lokalnej gałęzi docelowej][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Dodatkowe zasoby platformy Azure: Azure HDInsight Hadoop klastrów i maszyn wirtualnych platformy Azure (serwer notesu IPython)
1. Utwórz maszynę wirtualną platformy Azure z uruchomionym serwerem notesu IPython.
1. Utwórz klaster Azure HDInsight Hadoop.
1. Obowiązkowe Wstępne przetwarzanie i czyszczenie danych.
   
   a.  Wstępne przetwarzanie i czyszczenie danych w notesie IPython, uzyskiwanie dostępu do danych z obiektów blob platformy Azure.
   
   b.  Przekształć dane na wyczyszczony formularz tabelaryczny, jeśli jest to konieczne.
   
   c.  Zapisywanie danych na maszynie wirtualnej — pliki lokalne (IPython Notes jest uruchomiony na maszynie wirtualnej, dyski lokalne odnoszą się do dysków maszyn wirtualnych).
1. Przekaż dane do domyślnego kontenera klastra usługi Hadoop wybranego w kroku 2.
1. Ładowanie danych do bazy danych programu Hive w klastrze Azure HDInsight Hadoop.
   
   a.  Logowanie do węzła głównego klastra usługi Hadoop
   
   b.  Otwórz wiersz polecenia usługi Hadoop.
   
   c.  Wprowadź polecenie Katalog główny Hive `cd %hive_home%\bin` w wierszu polecenia usługi Hadoop.
   
   d.  Uruchom zapytania programu Hive w celu utworzenia bazy danych i tabel oraz załadowania danych z usługi BLOB Storage do tabel programu Hive.
   
   > [!NOTE]
   > Jeśli dane są duże, użytkownicy mogą utworzyć tabelę programu Hive z partycjami. Następnie użytkownicy mogą użyć `for` pętli w wierszu polecenia usługi Hadoop w węźle głównym, aby załadować dane do tabeli programu Hive partycjonowanej według partycji.
   > 
   > 
1. Eksploruj dane i twórz funkcje zgodnie z wymaganiami w wierszu polecenia usługi Hadoop. Funkcje te nie muszą być w tabelach baz danych. Należy zwrócić uwagę na to, aby je utworzyć.
   
   a.  Logowanie do węzła głównego klastra usługi Hadoop
   
   b.  Otwórz wiersz polecenia usługi Hadoop.
   
   c.  Wprowadź polecenie Katalog główny Hive `cd %hive_home%\bin` w wierszu polecenia usługi Hadoop.
   
   d.  Uruchom zapytania programu Hive w wierszu polecenia usługi Hadoop w węźle głównym klastra Hadoop, aby eksplorować dane i tworzyć funkcje w razie potrzeby.
1. W razie potrzeby i/lub pożądanych, przykładowe dane do dopasowania do Azure Machine Learning Studio.
1. Zaloguj się do [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Odczytaj dane bezpośrednio z programu `Hive Queries` przy użyciu modułu [Importuj dane][import-data] . Wklej wymagane zapytanie, które wyodrębnia pola, tworzy funkcje i próbkuje dane, jeśli jest to konieczne bezpośrednio w kwerendzie [Importuj dane][import-data] .
1. Prosty przepływ eksperymentu Azure Machine Learning rozpoczynający się od przekazanego zestawu danych.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Drzewo decyzyjne dla wyboru scenariusza
---
Na poniższym diagramie zestawiono scenariusze opisane powyżej oraz Zaawansowana procedura analizy i wybór technologii, które przenoszą Cię do poszczególnych scenariuszy. Przetwarzanie danych, eksploracja, Inżynieria funkcji i próbkowanie może odbywać się w jednej lub większej liczbie metod/środowisk — w środowiskach źródłowych, pośrednich i/lub docelowych — i mogą być wykonywane iteracyjnie zgodnie z wymaganiami. Diagram służy tylko jako ilustracja niektórych możliwych przepływów i nie zapewnia wyczerpującego wyliczenia.

![Przykładowe scenariusze instruktażowego procesu DS][8]

### <a name="advanced-analytics-in-action-examples"></a>Przykłady akcji zaawansowanej analizy
Aby zapoznać się z kompleksowymi przewodnikami Azure Machine Learning, które wykorzystują proces zaawansowanej analizy i technologię przy użyciu publicznych zestawów danych, zobacz:

* [Proces analizy danych zespołu w działaniu: używanie SQL Server](sql-walkthrough.md).
* [Proces analizy danych zespołu w działaniu: korzystanie z klastrów usługi HDInsight Hadoop](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data