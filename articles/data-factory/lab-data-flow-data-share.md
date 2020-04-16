---
title: Integracja danych przy użyciu usługi Azure Data Factory i azure data share
description: Kopiowanie, przekształcanie i udostępnianie danych przy użyciu usługi Azure Data Factory i azure data share
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 7d453b2724c308e48366d653a51d9e6aa8e82c96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415922"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integracja danych przy użyciu usługi Azure Data Factory i azure data share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W miarę jak klienci rozpoczynają swoje nowoczesne projekty w zakresie hurtowni danych i analiz, wymagają one nie tylko większej ilości danych, ale także większego wglądu w ich dane w całym dziale danych. W tym warsztacie opisano, w jaki sposób ulepszenia usługi Azure Data Factory i usługi Azure Data Share upraszczają integrację danych i zarządzanie nimi na platformie Azure. 

Od włączenia bez kodu ETL/ELT do tworzenia kompleksowego widoku danych, ulepszenia usługi Azure Data Factory umożliwią inżynierom danych pewne wnoszenie większej ilości danych, a tym samym większej wartości do przedsiębiorstwa. Usługa Azure Data Share umożliwia prowadzenie działalności w celu udostępniania biznesowego w sposób regulowany.

W tym warsztacie użyjesz usługi Azure Data Factory (ADF) do pozyskiwania danych z bazy danych SQL (SQL DB) platformy Azure data lake w gen2 (ADLS gen2). Po wylądowaniu danych w jeziorze, można przekształcić go za pomocą przepływu danych mapowania, natywnej usługi transformacji fabryki danych i zatopić go w usłudze Azure Synapse Analytics (dawniej SQL DW). Następnie udostępnisz tabelę z przekształconymi danymi wraz z dodatkowymi danymi przy użyciu usługi Azure Data Share. 

Dane używane w tym laboratorium to dane taksówki w Nowym Jorku. Aby zaimportować go do bazy danych SQL platformy Azure, pobierz [plik bacpac danych taxi](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* **Usługa Azure SQL Database:** Jeśli nie masz bazy danych SQL, dowiedz się, jak [utworzyć konto bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Konto magazynu usługi Azure Data Lake Storage Gen2:** Jeśli nie masz konta magazynu ADLS Gen2, dowiedz się, jak [utworzyć konto magazynu ADLS Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

* **Usługa Azure Synapse Analytics (dawniej SQL DW)**: Jeśli nie masz usługi Azure Synapse Analytics (dawniej SQL DW), dowiedz się, jak [utworzyć wystąpienie usługi Azure Synapse Analytics.](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)

* **Usługa Azure Data Factory**: Jeśli nie utworzono fabryki danych, zobacz, jak [utworzyć fabrykę danych.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)

* **Udostępnianie danych platformy Azure:** Jeśli nie utworzono udziału danych, zobacz, jak [utworzyć udział danych](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Konfigurowanie środowiska usługi Azure Data Factory

W tej sekcji dowiesz się, jak uzyskać dostęp do środowiska użytkownika usługi Azure Data Factory (ADF UX) z witryny Azure portal. Po wejściu do środowiska użytkownika usługi ADF skonfigurujesz trzy połączone usługi dla każdego z używanych przez nas magazynów danych: Usługi Azure SQL DB, ADLS Gen2 i Usługi Azure Synapse Analytics.

W usłudze Azure Data Factory połączone usługi definiują informacje o połączeniu z zasobami zewnętrznymi. Usługa Azure Data Factory obsługuje obecnie ponad 85 łączników.

### <a name="open-the-azure-data-factory-ux"></a>Otwórz środowisko użytkownika usługi Azure Data Factory

1. Otwórz [portal Azure](https://portal.azure.com) w przeglądarce Microsoft Edge lub Google Chrome.
1. Korzystając z paska wyszukiwania u góry strony, wyszukaj hasło "Fabryki danych"

    ![Portal](media/lab-data-flow-data-share/portal1.png)
1. Kliknij zasób fabryki danych, aby otworzyć jego blok zasobów.

    ![Portal](media/lab-data-flow-data-share/portal2.png)
1. Kliknij **autora i monitora,** aby otworzyć środowisko użytkownika ADF. Do obiektu ADF UX można również uzyskać dostęp pod adf.azure.com.

    ![Portal](media/lab-data-flow-data-share/portal3.png)
1. Nastąpi przekierowanie na stronę główną środowiska użytkownika ADF. Ta strona zawiera szybkie uruchamianie, filmy instruktażowe i łącza do samouczków, aby dowiedzieć się pojęcia dotyczące fabryki danych. Aby rozpocząć tworzenie, kliknij ikonę ołówka na lewym pasku bocznym.

    ![Portal](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi bazy danych SQL platformy Azure

1. Strona tworzenia jest, gdzie można utworzyć zasoby fabryki danych, takich jak potoki, zestawy danych, przepływy danych, wyzwalacze i połączone usługi. Aby utworzyć usługę połączeniową, kliknij przycisk **Połączenia** w prawym dolnym rogu.

    ![Portal](media/lab-data-flow-data-share/configure2.png)
1. Na karcie Połączenia kliknij pozycję **Nowy,** aby dodać nową usługę połączeniową.

    ![Portal](media/lab-data-flow-data-share/configure3.png)
1. Pierwszą połączną usługą, którą skonfigurujesz, jest baza danych SQL platformy Azure. Za pomocą paska wyszukiwania można filtrować listę magazynu danych. Kliknij kafelek **bazy danych SQL azure** i kliknij przycisk Kontynuuj.

    ![Portal](media/lab-data-flow-data-share/configure4.png)
1. W okienku konfiguracji bazy danych SQL wprowadź "SQLDB" jako nazwę połączonej usługi. Wprowadź poświadczenia, aby umożliwić fabryczne łączenie się z bazą danych. Jeśli używasz uwierzytelniania SQL, wprowadź nazwę serwera, bazę danych, nazwę użytkownika i hasło. Informacje o połączeniu można sprawdzić, czy informacje o połączeniu są poprawne, klikając przycisk **Testuj połączenie**. Po zakończeniu kliknij **pozycję Utwórz.**

    ![Portal](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Tworzenie połączonej usługi Usługi Azure Synapse Analytics

1. Powtórz ten sam proces, aby dodać usługę połączony usługi Azure Synapse Analytics. Na karcie Połączenia kliknij pozycję **Nowy**. Wybierz kafelek **usługi Azure Synapse Analytics (dawniej SQL DW)** i kliknij przycisk Kontynuuj.

    ![Portal](media/lab-data-flow-data-share/configure6.png)
1. W okienku konfiguracji usługi połączonej wprowadź "SQLDW" jako nazwę połączonej usługi. Wprowadź poświadczenia, aby umożliwić fabryczne łączenie się z bazą danych. Jeśli używasz uwierzytelniania SQL, wprowadź nazwę serwera, bazę danych, nazwę użytkownika i hasło. Informacje o połączeniu można sprawdzić, czy informacje o połączeniu są poprawne, klikając przycisk **Testuj połączenie**. Po zakończeniu kliknij **pozycję Utwórz.**

    ![Portal](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Tworzenie połączonej usługi Usługi Usługi Azure Data Storage Gen2

1. Ostatnią połączonyą usługą wymaganą dla tego laboratorium jest usługa Azure Data Lake Storage gen2.  Na karcie Połączenia kliknij pozycję **Nowy**. Wybierz kafelek **Usługi Azure Data Lake Storage Gen2** i kliknij przycisk Kontynuuj.

    ![Portal](media/lab-data-flow-data-share/configure8.png)
1. W okienku konfiguracji usługi połączonej wprowadź "ADLSGen2" jako nazwę połączonej usługi. Jeśli używasz uwierzytelniania klucza konta, wybierz konto magazynu adls gen2 z listy rozwijanej **Nazwa konta magazynu.** Informacje o połączeniu można sprawdzić, czy informacje o połączeniu są poprawne, klikając przycisk **Testuj połączenie**. Po zakończeniu kliknij **pozycję Utwórz.**

    ![Portal](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Włączanie trybu debugowania przepływu danych

W sekcji *Przekształcanie danych przy użyciu przepływu danych mapowania*będzie tworzenie przepływów danych mapowania. Najlepszym rozwiązaniem przed tworzeniem przepływów danych mapowania jest włączenie trybu debugowania, który umożliwia testowanie logiki transformacji w ciągu kilku sekund w aktywnym klastrze iskrowym.

Aby włączyć debugowanie, kliknij suwak **debugowania przepływ danych** na pasku głównym fabryki. Kliknij przycisk ok, gdy pojawi się okno dialogowe potwierdzenia. Uruchomienie klastra zajmie około 5-7 minut. Kontynuuj do *pozyskiwania danych z usługi Azure SQL DB do ADLS gen2 przy użyciu działania kopiowania* podczas inicjowania.

![Portal](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Pogoń za pomocą działania kopiowania przy użyciu funkcji kopiowania

W tej sekcji utworzysz potok z działaniem kopiowania, który posuwa jedną tabelę z bazy danych SQL platformy Azure do konta magazynu ADLS gen2. Dowiesz się, jak dodać potok, skonfigurować zestaw danych i debugować potok za pośrednictwem środowiska użytkownika usługi ADF. Wzorzec konfiguracji używany w tej sekcji może mieć zastosowanie do kopiowania z relacyjnego magazynu danych do magazynu danych opartych na plikach.

W usłudze Azure Data Factory potok jest logiczną grupą działań, które razem wykonują zadanie. Działanie definiuje operację do wykonania na danych. Zestaw danych wskazuje dane, które mają być używane w połączonej usłudze.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Tworzenie potoku z działaniem kopiowania

1. W okienku zasobów fabrycznych kliknij ikonę plus, aby otworzyć nowe menu zasobów. Wybierz **opcję Potok**.

    ![Portal](media/lab-data-flow-data-share/copy1.png)
1. Na karcie **Ogólne** kanwy potoku nazwij potoku coś opisowego, takiego jak "IngestAndTransformTaxiData".

    ![Portal](media/lab-data-flow-data-share/copy2.png)
1. W okienku działań kanwy potoku otwórz akordeon **Przenieś i Przekształć** i przeciągnij działanie **Kopiuj dane** na kanwę. Nadaj działaniu kopiowania opisową nazwę, taką jak "IngestIntoADLS".

    ![Portal](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurowanie źródłowego zestawu danych bazy danych usługi Azure SQL

1. Kliknij kartę **Źródło** działania kopiowania. Aby utworzyć nowy zestaw danych, kliknij przycisk **Nowy**. Twoim źródłem będzie tabela dbo. TripData" znajduje się w połączonej usłudze "SQLDB" skonfigurowany wcześniej.

    ![Portal](media/lab-data-flow-data-share/copy4.png)
1. Wyszukaj **bazę danych SQL usługi Azure** i kliknij przycisk Kontynuuj.

    ![Portal](media/lab-data-flow-data-share/copy5.png)
1. Zadzwoń do zestawu danych "TripData". Wybierz "SQLDB" jako usługę połączony. Wybierz nazwę tabeli 'dbo. TripData' z listy rozwijanej nazwa tabeli. Importowanie schematu **Z połączenia/magazynu**. Po zakończeniu kliknij przycisk OK.

    ![Portal](media/lab-data-flow-data-share/copy6.png)

Pomyślnie utworzono źródłowy zestaw danych. Upewnij się, że w ustawieniach źródła w polu użyj **kwerendy** wybrano domyślną wartość Tabela.

### <a name="configure-adls-gen-2-sink-dataset"></a>Konfigurowanie zestawu danych ujścia ADLS Gen 2

1. Kliknij kartę **Zlew** działania kopiowania. Aby utworzyć nowy zestaw danych, kliknij przycisk **Nowy**.

    ![Portal](media/lab-data-flow-data-share/copy7.png)
1. Wyszukaj **usługę Azure Data Lake Storage Gen2** i kliknij przycisk Kontynuuj.

    ![Portal](media/lab-data-flow-data-share/copy8.png)
1. W okienku wyboru formatu wybierz pozycję **DelimitedText** podczas pisania w pliku csv. Kliknij przycisk Kontynuuj.

    ![Portal](media/lab-data-flow-data-share/copy9.png)
1. Nazwij swój zestaw danych ujścia "TripDataCSV". Wybierz "ADLSGen2" jako usługę połączony. Wpisz miejsce, w którym chcesz napisać plik csv. Na przykład można zapisać dane `trip-data.csv` do `staging-container`pliku w kontenerze . Ustaw **pierwszy wiersz jako nagłówek** na true, ponieważ chcesz, aby dane wyjściowe miały nagłówki. Ponieważ w miejscu docelowym nie istnieje jeszcze żaden plik, ustaw **schemat importu** na **Brak**. Po zakończeniu kliknij przycisk OK.

    ![Portal](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testowanie działania kopiowania przy uruchomieniu debugowania potoku

1. Aby sprawdzić, czy działanie kopiowania działa poprawnie, kliknij przycisk **Debugowanie** w górnej części kanwy potoku, aby wykonać uruchomienie debugowania. Uruchomienie debugowania umożliwia przetestowanie potoku end-to-end lub do momentu, gdy punkt przerwania przed opublikowaniem go w usłudze fabryki danych.

    ![Portal](media/lab-data-flow-data-share/copy11.png)
1. Aby monitorować uruchomienie debugowania, przejdź do karty **Dane wyjściowe** kanwy potoku. Ekran monitorowania będzie automatycznie migać co 20 sekund lub po ręcznym kliknięciu przycisku odświeżania. Działanie kopiowania ma specjalny widok monitorowania, do którego można uzyskać dostęp, klikając ikonę okularów w kolumnie **Akcje.**

    ![Portal](media/lab-data-flow-data-share/copy12.png)
1. Widok monitorowania kopii zawiera szczegóły wykonania działania i charakterystyki wydajności. Można zobaczyć informacje, takie jak dane odczytu/zapisu, wiersze odczytu / zapisu, pliki odczytu / zapisu i przepływności. Jeśli wszystko zostało skonfigurowane poprawnie, powinieneś zobaczyć 49 999 wierszy zapisanych w jednym pliku w zlewie ADLS.

    ![Portal](media/lab-data-flow-data-share/copy13.png)
1. Przed przejściem do następnej sekcji zaleca się opublikowanie zmian w usłudze fabryki danych, klikając pozycję **Opublikuj wszystko** na pasku głównym fabryki. Chociaż nie jest objęty w tym laboratorium, usługa Azure Data Factory obsługuje pełną integrację git. Integracja Git umożliwia kontrolę wersji, iteracyjne zapisywanie w repozytorium i współpracę na fabryce danych. Aby uzyskać więcej informacji, zobacz [formant źródła w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration).

    ![Portal](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Przekształcanie danych przy użyciu przepływu danych mapowania

Teraz, po pomyślnym skopiowaniu danych do usługi Azure Data Lake Storage, nadszedł czas, aby dołączyć i zagregować te dane do magazynu danych. Użyjemy przepływu danych mapowania, usługi transformacji usługi Azure Data Factory zaprojektowanej wizualnie. Mapowanie przepływów danych umożliwia użytkownikom tworzenie logiki transformacji bez kodu i wykonywanie ich w klastrach iskrowych zarządzanych przez usługę ADF.

Przepływ danych utworzony w tym kroku wewnętrzny łączy zestaw danych "TripDataCSV" utworzony w poprzedniej sekcji z tabelą 'dbo. TripFares" przechowywane w "SQLDB" na podstawie czterech kluczowych kolumn. Następnie dane są agregowane `payment_type` na podstawie kolumny, aby obliczyć średnią niektórych pól i zapisane w tabeli usługi Azure Synapse Analytics.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Dodawanie aktywności przepływu danych do potoku

1. W okienku działań kanwy potoku otwórz akordeon **Przenieś i Przekształć** i przeciągnij działanie **Przepływ danych** na kanwę.

    ![Portal](media/lab-data-flow-data-share/dataflow1.png)
1. W otwierane okienko boczne wybierz pozycję **Utwórz nowy przepływ danych** i wybierz pozycję **Mapowanie przepływu danych**. Kliknij przycisk **OK**.

    ![Portal](media/lab-data-flow-data-share/dataflow2.png)
1. Zostaniesz przekierowany do obszaru roboczego przepływu danych, gdzie będziesz budować logikę transformacji. Na karcie ogólne nazwa przepływu danych "JoinAndAggregateData".

    ![Portal](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Konfigurowanie źródła csv danych podróży

1. Pierwszą rzeczą, którą chcesz zrobić, to skonfigurować dwa transformacje źródła. Pierwsze źródło wskaż zestaw danych "TripDataCSV" DelimitedText. Aby dodać transformację źródłową, kliknij pole **Dodaj źródło** na kanwie.

    ![Portal](media/lab-data-flow-data-share/dataflow4.png)
1. Nazwij swoje źródło "TripDataCSV" i wybierz zestaw danych "TripDataCSV" z listy rozwijanej źródło. Jeśli pamiętasz, nie zaimportowałeś schematu początkowo podczas tworzenia tego zestawu danych, ponieważ nie było tam żadnych danych. Ponieważ `trip-data.csv` istnieje teraz, kliknij przycisk **Edytuj,** aby przejść do karty ustawień zestawu danych.

    ![Portal](media/lab-data-flow-data-share/dataflow5.png)
1. Przejdź do zakładki **Schemat** i kliknij pozycję **Importuj schemat**. Wybierz **opcję Z połączenia/magazynu,** aby zaimportować bezpośrednio z magazynu plików. Powinno pojawić się 14 kolumn ciągu typu.

    ![Portal](media/lab-data-flow-data-share/dataflow6.png)
1. Wróć do przepływu danych "JoinAndAggregateData". Jeśli klaster debugowania został uruchomiony (oznaczony zielonym kółkiem obok suwaka debugowania), można uzyskać migawkę danych na karcie **Podgląd danych.** Kliknij przycisk **Odśwież,** aby pobrać podgląd danych.

    ![Portal](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Podgląd danych nie zapisuje danych.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurowanie taryfy podróży źródło bazy danych SQL DB

1. Drugie źródło, które dodajesz, wskaż tabelę SQL DB 'dbo. TripFares". W obszarze źródła "TripDataCSV" pojawi się kolejne pole **Dodaj źródło.** Kliknij go, aby dodać nową transformację źródła.

    ![Portal](media/lab-data-flow-data-share/dataflow8.png)
1. Nazwij to źródło 'TripFaresSQL'. Kliknij **pozycję Nowy** obok źródłowego zestawu danych, aby utworzyć nowy zestaw danych bazy danych SQL.

    ![Portal](media/lab-data-flow-data-share/dataflow9.png)
1. Wybierz kafelek **bazy danych SQL platformy Azure** i kliknij przycisk Kontynuuj. *Uwaga: Można zauważyć, że wiele łączników w fabryce danych nie są obsługiwane w przepływie danych mapowania. Aby przekształcić dane z jednego z tych źródeł, pozyskuj je do obsługiwanego źródła przy użyciu działania kopiowania*.

    ![Portal](media/lab-data-flow-data-share/dataflow10.png)
1. Zadzwoń do swojego zestawu danych "TripFares". Wybierz "SQLDB" jako usługę połączony. Wybierz nazwę tabeli 'dbo. TripFares' z listy rozwijanej nazwa tabeli. Importowanie schematu **Z połączenia/magazynu**. Po zakończeniu kliknij przycisk OK.

    ![Portal](media/lab-data-flow-data-share/dataflow11.png)
1. Aby zweryfikować dane, pobierz podgląd danych na karcie **Podgląd danych.**

    ![Portal](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Wewnętrzne łączenie TripDataCSV i TripFaresSQL

1. Aby dodać nową transformację, kliknij ikonę plusa w prawym dolnym rogu "TripDataCSV". W obszarze **Wiele wejść/wyjść**wybierz pozycję **Dołącz**.

    ![Portal](media/lab-data-flow-data-share/join1.png)
1. Nazwij transformację sprzężenia "InnerJoinWithTripFares". Wybierz "TripFaresSQL" z listy rozwijanej. Wybierz **opcję Wewnętrzna** jako typ sprzężenia. Aby dowiedzieć się więcej o różnych typach sprzężeń w przepływie danych mapowania, zobacz [typy sprzężeń](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types).

    Wybierz kolumny, które chcesz dopasować z każdego strumienia za pośrednictwem listy rozwijanej **Warunki dołączania.** Aby dodać dodatkowy warunek sprzężenia, kliknij ikonę plus obok istniejącego warunku. Domyślnie wszystkie warunki sprzężenia są łączone z operatorem AND, co oznacza, że wszystkie warunki muszą być spełnione dla dopasowania. W tym laboratorium chcemy `medallion`dopasować `hack_license`się `vendor_id`do kolumn , , i`pickup_datetime`

    ![Portal](media/lab-data-flow-data-share/join2.png)
1. Sprawdź, czy pomyślnie dołączyłeś 25 kolumn wraz z podglądem danych.

    ![Portal](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agregacja o payment_type

1. Po zakończeniu transformacji sprzężenia dodaj transformację agregacji, klikając ikonę plus obok 'InnerJoinWithTripFares. Wybierz **pozycję Agreguj** w obszarze **modyfikator schematu**.

    ![Portal](media/lab-data-flow-data-share/agg1.png)
1. Nazwij swoją agregację transformacji "AggregateByPaymentType". Wybierz `payment_type` jako grupę według kolumny.

    ![Portal](media/lab-data-flow-data-share/agg2.png)
1. Przejdź do karty **Agreguj.** W tym miejscu określą dwa agregacje:
    * Średnia taryfa pogrupowana według typu płatności
    * Całkowita odległość podróży pogrupowana według typu płatności

    Najpierw utworzysz wyrażenie średniej taryfy. W polu tekstowym o nazwie **Dodaj lub wybierz kolumnę**wpisz "average_fare".

    ![Portal](media/lab-data-flow-data-share/agg3.png)
1. Aby wprowadzić wyrażenie agregacji, kliknij niebieskie pole z etykietą **Enter wyrażenie**. Spowoduje to otwarcie konstruktora wyrażeń przepływu danych, narzędzia używanego do wizualnego tworzenia wyrażeń przepływu danych przy użyciu schematu wejściowego, wbudowanych funkcji i operacji oraz parametrów zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji na temat możliwości konstruktora wyrażeń, zobacz [dokumentację konstruktora wyrażeń](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    Aby uzyskać średnią taryfę, `avg()` użyj funkcji agregacji, aby zagregować `total_amount` rzutowanie kolumny do liczby całkowitej za pomocą `toInteger()`programu . W języku wyrażeń przepływu danych `avg(toInteger(total_amount))`jest to zdefiniowane jako . Po zakończeniu **kliknij pozycję Zapisz i zakończ.**

    ![Portal](media/lab-data-flow-data-share/agg4.png)
1. Aby dodać dodatkowe wyrażenie agregacji, kliknij `average_fare`ikonę plus obok pozycji . Wybierz **pozycję Dodaj kolumnę**.

    ![Portal](media/lab-data-flow-data-share/agg5.png)
1. W polu tekstowym o nazwie **Dodaj lub wybierz kolumnę**wpisz "total_trip_distance". Podobnie jak w ostatnim kroku, otwórz konstruktora wyrażeń, aby wprowadzić je w wyrażeniu.

    Aby uzyskać całkowitą odległość podróży, użyj funkcji `sum()` `trip_distance` agregacji, aby zagregować kolumnę rzutowaną do liczby całkowitej za pomocą programu `toInteger()`. W języku wyrażeń przepływu danych `sum(toInteger(trip_distance))`jest to zdefiniowane jako . Po zakończeniu **kliknij pozycję Zapisz i zakończ.**

    ![Portal](media/lab-data-flow-data-share/agg6.png)
1. Przetestuj logikę transformacji na karcie **Podgląd danych.** Jak widać, jest znacznie mniej wierszy i kolumn niż poprzednio. Tylko trzy kolumny grupy według i agregacji zdefiniowane w tej transformacji są kontynuowane w dół. Ponieważ w próbce jest tylko pięć grup typów płatności, wyprowadzanych jest tylko pięć wierszy.

    ![Portal](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurowanie ujścia usługi Azure Synapse Analytics

1. Po zakończeniu logiki transformacji jesteśmy gotowi zatopić nasze dane w tabeli analizy synapse platformy Azure. Dodaj transformację ujścia w sekcji **Miejsce docelowe.**

    ![Portal](media/lab-data-flow-data-share/sink1.png)
1. Nazwij zlew "SQLDWSink". Kliknij **przycisk Nowy** obok pola zestawu danych ujścia, aby utworzyć nowy zestaw danych usługi Azure Synapse Analytics.

    ![Portal](media/lab-data-flow-data-share/sink2.png)

1. Wybierz kafelek **usługi Azure Synapse Analytics (dawniej SQL DW)** i kliknij przycisk Kontynuuj.

    ![Portal](media/lab-data-flow-data-share/sink3.png)
1. Wywołanie zestawu danych "AggregatedTaxiData". Wybierz "SQLDW" jako usługę połączony. Wybierz **pozycję Utwórz nową tabelę** i nazwij nowy dbo tabeli. AggregateTaxiData. Kliknij przycisk OK po zakończeniu

    ![Portal](media/lab-data-flow-data-share/sink4.png)
1. Przejdź do karty **Ustawienia** ujścia. Ponieważ tworzymy nową tabelę, musimy wybrać **utwórz ponownie tabelę** w obszarze akcji tabeli. Usuń **zaznaczenie opcji Włącz przemieszczania,** który przełącza, czy wstawiamy wiersz po wierszu, czy w partii.

    ![Portal](media/lab-data-flow-data-share/sink5.png)

Pomyślnie utworzono przepływ danych. Teraz nadszedł czas, aby uruchomić go w działania potoku.

### <a name="debug-your-pipeline-end-to-end"></a>Debugowanie potoku end-to-end

1. Wróć do karty potoku **IngestAndTransformData.** Zwróć uwagę na zielone pole na aktywności kopiowania "IngestIntoADLS". Przeciągnij go do działania przepływu danych "JoinAndAggregateData". Tworzy to "na sukces", co powoduje, że działanie przepływu danych jest uruchamiane tylko wtedy, gdy kopia zakończy się pomyślnie.

    ![Portal](media/lab-data-flow-data-share/pipeline1.png)
1. Tak jak w przypadku działania kopiowania, kliknij przycisk **Debugowanie,** aby wykonać uruchomienie debugowania. W przypadku przebiegów debugowania działanie przepływu danych będzie używać aktywnego klastra debugowania zamiast rozkłwojenia nowego klastra. Ten potok zajmie nieco ponad minutę, aby wykonać.

    ![Portal](media/lab-data-flow-data-share/pipeline2.png)
1. Podobnie jak działanie kopiowania, przepływ danych ma specjalny widok monitorowania dostępny przez ikonę okularów po zakończeniu działania.

    ![Portal](media/lab-data-flow-data-share/pipeline3.png)
1. W widoku monitorowania można wyświetlić uproszczony wykres przepływu danych wraz z czasem wykonywania i wierszami na każdym etapie wykonywania. Jeśli wykonane poprawnie, należy zagregować 49,999 wierszy w pięciu wierszach w tym działaniu.

    ![Portal](media/lab-data-flow-data-share/pipeline4.png)
1. Można kliknąć transformację, aby uzyskać dodatkowe szczegóły dotyczące jego wykonywania, takie jak informacje o partycjonowaniu i nowe/zaktualizowane/usunięte kolumny.

    ![Portal](media/lab-data-flow-data-share/pipeline5.png)

Ukończono teraz część fabryki danych tego laboratorium. Opublikuj swoje zasoby, jeśli chcesz operacjonalizacji ich za pomocą wyzwalaczy. Pomyślnie uruchomiono potok, który pozyskiwania danych z usługi Azure SQL Database do usługi Azure Data Lake Storage przy użyciu działania kopiowania, a następnie zagregowane te dane do usługi Azure Synapse Analytics. Można sprawdzić, czy dane zostały pomyślnie napisane, patrząc na sam program SQL Server.

## <a name="share-data-using-azure-data-share"></a>Udostępnianie danych przy użyciu udziału danych platformy Azure

W tej sekcji dowiesz się, jak skonfigurować nowy udział danych za pomocą witryny Azure portal. Będzie to obejmować utworzenie nowego udziału danych, który będzie zawierał zestawy danych z usługi Azure Data Lake Store Gen2 i usługi Azure SQL Data Warehouse. Następnie skonfigurujesz harmonogram migawek, który umożliwi konsumentom dane automatyczne odświeżanie udostępnianych im danych. Następnie zaprosisz odbiorców do udostępniania danych. 

Po utworzeniu udziału danych, będziesz następnie przełączyć kapelusze i stać się *konsumentem danych*. Jako konsument danych przejdziesz przez przepływ akceptowania zaproszenia do udostępniania danych, konfigurowania miejsca, w którym mają być odbierane dane, oraz mapowania zestawów danych do różnych lokalizacji magazynu. Następnie wyzwolisz migawkę, która skopiuje dane udostępnione do określonego miejsca docelowego. 

### <a name="sharing-data-data-provider-flow"></a>Udostępnianie danych (przepływ dostawcy danych)

1. Otwórz portal Azure w przeglądarce Microsoft Edge lub Google Chrome.

1. Korzystając z paska wyszukiwania u góry strony, wyszukaj **udziały danych**

    ![Portal](media/lab-data-flow-data-share/portal-ads.png)

1. Wybierz konto udostępniania danych z "Dostawcą" w nazwie. Na przykład **DataProvider0102**. 

1. Wybierz **pozycję Rozpocznij udostępnianie danych**

    ![Rozpocznij udostępnianie](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Wybierz **+Utwórz,** aby rozpocząć konfigurowanie nowego udziału danych. 

1. W **obszarze Nazwa udostępnienia**określ wybraną nazwę. Jest to nazwa udziału, który będzie widoczny przez konsumenta danych, więc należy nadać mu opisową nazwę, taką jak TaxiData.

1. W obszarze **Opis**umieść w zdaniu opisującym zawartość udziału danych. Udział danych będzie zawierać dane podróży taksówką na całym świecie, które są przechowywane w wielu sklepach, w tym usługi Azure SQL Data Warehouse i Azure Data Lake Store. 

1. W **warunkach użytkowania**określ zestaw warunków, których konsument ma przestrzegać. Niektóre przykłady obejmują "Nie rozpowszechniaj tych danych poza organizacją" lub "Zapoznaj się z umową prawną". 

    ![Udostępnianie szczegółów](media/lab-data-flow-data-share/ads-details.png)

1. Wybierz przycisk **Kontynuuj**. 

1. Wybierz **pozycję Dodaj zestawy danych** 

    ![Dodaj zestaw danych](media/lab-data-flow-data-share/add-dataset.png)

1. Wybierz **usługę Azure SQL Data Warehouse,** aby wybrać tabelę z usługi Azure SQL Data Warehouse, w którą wylądowały przekształcenia usługi ADF.

    ![Dodaj zestaw danych](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Usługa Azure SQL Data Warehouse jest teraz znana jako usługa Azure Synapse Analytics

1. Otrzymasz skrypt do uruchomienia, zanim będzie można kontynuować. Dostarczony skrypt tworzy użytkownika w bazie danych SQL, aby umożliwić msi udostępniania danych platformy Azure do uwierzytelniania w jego imieniu. 

> [!IMPORTANT]
> Przed uruchomieniem skryptu należy ustawić się jako administrator usługi Active Directory dla programu SQL Server. 

1. Otwórz nową kartę i przejdź do witryny Azure Portal. Skopiuj skrypt dostarczony, aby utworzyć użytkownika w bazie danych, z której chcesz udostępnić dane. W tym celu można zalogować się do bazy danych EDW przy użyciu Eksploratora zapytań (wersja zapoznawcza) przy użyciu uwierzytelniania AAD. 

    Należy zmodyfikować skrypt, tak aby utworzony użytkownik znajdował się w nawiasach. Np:
    
    utworzyć użytkownika [dataprovider-xxxx] z zewnętrznego loginu;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Przełącz się z powrotem do usługi Azure Data Share, gdzie dodajesz zestawy danych do udziału danych. 

1. Wybierz **pozycję EDW** dla magazynu danych SQL i wybierz pozycję **AggregatedTaxiData** dla tabeli. 

1. Wybierz **dodaj zestaw danych**

    Mamy teraz tabelę SQL, która jest częścią naszego zestawu danych. Następnie dodamy dodatkowe zestawy danych z usługi Azure Data Lake Store. 

1. Wybierz **pozycję Dodaj zestaw danych** i wybierz pozycję Azure Data Lake Store **Gen2**

    ![Dodaj zestaw danych](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Wybierz **przycisk Dalej**

1. Rozwiń *wwtaxidata*. Rozwiń *Dane Boston Taxi*. Należy zauważyć, że można udostępnić w dół do poziomu pliku. 

1. Wybierz folder *Boston Taxi Data,* aby dodać cały folder do udziału danych. 

1. Wybierz **pozycję Dodaj zestawy danych**

1. Przejrzyj dodane zestawy danych. Do udziału danych powinna być dodana tabela SQL i folder ADLSGen2. 

1. Wybierz **przycisk Kontynuuj**

1. Na tym ekranie można dodać adresatów do udziału danych. Dodawany odbiorca otrzymają zaproszenia do udostępniania danych. Na potrzeby tego laboratorium należy dodać 2 adresy e-mail:

    1. Adres e-mail subskrypcji platformy Azure, w której się mieszkasz. 

        ![Dodawanie adresatów](media/lab-data-flow-data-share/add-recipients.png)

    1. Dodaj fikcyjne dane *janedoe@fabrikam.com*konsumenta o nazwie .

1. Na tym ekranie można skonfigurować ustawienie migawki dla konsumenta danych. Umożliwi im to regularne aktualizowanie danych w określonym przez Ciebie odstępach czasu. 

1. Sprawdź **harmonogram migawki** i skonfiguruj cogodzinne odświeżanie danych przy użyciu listy rozwijanej *Cykl.*  

1. Wybierz pozycję **Utwórz**.

    Masz teraz aktywny udział danych. Umożliwia sprawdzenie, co można zobaczyć jako dostawca danych podczas tworzenia udziału danych. 

1. Wybierz utworzony udział danych zatytułowany **DataProvider**. Można przejść do niego, wybierając pozycję **Wysłane udziały** w **udziale danych**. 

1. Kliknij harmonogram migawki. Harmonogram migawki można wyłączyć, jeśli wybierzesz. 

1. Następnie wybierz kartę **Zestawy danych.** Po jego utworzeniu można dodać dodatkowe zestawy danych do tego udziału danych. 

1. Wybierz kartę **Udostępnij subskrypcje.** Nie istnieją jeszcze subskrypcje udostępniania, ponieważ konsument danych nie zaakceptował jeszcze zaproszenia.

1. Przejdź do karty **Zaproszenia.** W tym miejscu zostanie wyświetlona lista oczekujących zaproszeń. 

    ![Oczekujące zaproszenia](media/lab-data-flow-data-share/pending-invites.png)

1. Wybierz zaproszenie *janedoe@fabrikam.com*do . Wybierz pozycję Usuń. Jeśli odbiorca nie zaakceptował jeszcze zaproszenia, nie będzie już mógł tego zrobić. 

1. Wybierz kartę **Historia.** Nic nie jest jeszcze wyświetlane, ponieważ konsument danych nie zaakceptował jeszcze zaproszenia i wyzwolił migawkę. 

### <a name="receiving-data-data-consumer-flow"></a>Odbieranie danych (przepływ konsumenta danych)

Teraz, gdy sprawdziliśmy nasz udział w danych, jesteśmy gotowi zmienić kontekst i nosić nasz kapelusz konsumentów danych. 

Zaproszenie do udostępniania danych platformy Azure powinno być teraz dostępne w skrzynce odbiorczej platformy Microsoft Azure. Uruchom program Outlook Web Access (outlook.com) i zaloguj się przy użyciu poświadczeń dostarczonych dla subskrypcji platformy Azure.

W wiadomości e-mail, którą powinieneś otrzymać, kliknij "Wyświetl zaproszenie >". W tym momencie będziesz symulować środowisko konsumenta danych podczas akceptowania zaproszenia dostawców danych do ich udziału danych. 

![Zaproszenie e-mail](media/lab-data-flow-data-share/email-invite.png)

Może zostać wyświetlony monit o wybranie subskrypcji. Upewnij się, że wybrałeś subskrypcję, w której pracujesz dla tego laboratorium. 

1. Kliknij zaproszenie zatytułowane *DataProvider*. 

1. Na tym ekranie zaproszenia można zauważyć różne szczegóły dotyczące udziału danych, który został skonfigurowany wcześniej jako dostawca danych. Zapoznaj się ze szczegółami i zaakceptuj warunki użytkowania, jeśli są podane.

1. Wybierz subskrypcję i grupę zasobów, która już istnieje dla twojego laboratorium. 

1. W obszarze **Konto udostępniania danych**wybierz pozycję **DataConsumer**. Można również utworzyć nowe konto udziału danych. 

1. Obok **pozycji Odebrana nazwa udziału,** zauważysz, że domyślna nazwa udziału to nazwa określona przez dostawcę danych. Nadaj udziałowi przyjazną nazwę opisującą dane, które zamierzasz otrzymać, na przykład **TaxiDataShare**.

    ![Zaproszenie przyjmuje](media/lab-data-flow-data-share/consumer-accept.png)

1. Można wybrać **opcję Zaakceptuj i skonfiguruj teraz** lub **Zaakceptuj i skonfiguruj później**. Jeśli zdecydujesz się zaakceptować i skonfigurować teraz, określisz konto magazynu, na którym mają być kopiowane wszystkie dane. Jeśli zdecydujesz się zaakceptować i skonfigurować później, zestawy danych w udziale zostaną odmapowane i musisz je ręcznie zamapować. Zdecydujemy się na to później. 

1. Wybierz **pozycję Zaakceptuj i skonfiguruj później**. 

    Podczas konfigurowania tej opcji jest tworzona subskrypcja udziału, ale nie ma nigdzie, aby dane wylądowały, ponieważ nie zamapowano miejsca docelowego. 

    Następnie skonfigurujemy mapowania zestawów danych dla udziału danych. 

1. Wybierz odebrany udział (nazwę określoną w kroku 5).

    **Migawka wyzwalacza** jest wyszarzona, ale udział jest aktywny. 

1. Wybierz kartę **Zestawy danych.** Zwróć uwagę, że każdy zestaw danych jest niemapowany, co oznacza, że nie ma miejsca docelowego do kopiowania danych. 

    ![niezmapowane zestawy danych](media/lab-data-flow-data-share/unmapped.png)

1. Wybierz tabelę magazynu danych SQL, a następnie wybierz pozycję **+ Mapa do docelowej**.

1. Po prawej stronie ekranu wybierz pozycję **docelową listy rozwijanej Typ danych.** 

    Dane SQL można mapować do szerokiej gamy magazynów danych. W takim przypadku będziemy mapowania do usługi Azure SQL Database.

    ![mapowanie](media/lab-data-flow-data-share/mapping-options.png)
    
    (Opcjonalnie) Wybierz **usługę Azure Data Lake Store Gen2** jako docelowy typ danych. 
    
    (Opcjonalnie) Wybierz konto Subskrypcja, Grupa zasobów i Magazyn, w których pracujesz. 
    
    (Opcjonalnie) Możesz wybrać odbiór danych do usługi Data Lake w formacie csv lub parkietu. 

1. Obok **pozycji Typ danych docelowych**wybierz pozycję Azure SQL Database. 

1. Wybierz konto Subskrypcja, Grupa zasobów i Magazyn, w których pracujesz. 

    ![mapowanie do sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Przed kontynuowaniem należy utworzyć nowego użytkownika w programie SQL Server, uruchamiając dostarczony skrypt. Najpierw skopiuj skrypt dostarczony do schowka. 

1. Otwórz nową kartę portalu Azure. Nie zamykaj istniejącej karty, ponieważ za chwilę musisz wrócić do niej. 

1. Na otwartej nowej karcie przejdź do **baz danych SQL**.

1. Wybierz bazę danych SQL (w subskrypcji powinna znajdować się tylko jedna). Należy uważać, aby nie wybrać magazynu danych SQL. 

1. Wybieranie **edytora zapytań (wersja zapoznawcza)**

1. Użyj uwierzytelniania AAD, aby zalogować się do edytora zapytań. 

1. Uruchom kwerendę podana w udziale danych (skopiowaną do schowka w kroku 14). 

    To polecenie umożliwia usłudze Azure Data Share używanie tożsamości zarządzanych dla usług Platformy Azure do uwierzytelniania na serwerze SQL Server w celu skopiowania do niej danych. 

1. Wróć do oryginalnej karty i wybierz **pozycję Mapa do celu**.

1. Następnie wybierz folder Usługi Azure Data Lake Gen2, który jest częścią zestawu danych i zamapuj go na konto usługi Azure Blob Storage. 

    ![magazyn](media/lab-data-flow-data-share/storage-map.png)

    Po mapowaniu wszystkich zestawów danych możesz teraz rozpocząć odbieranie danych od dostawcy danych. 

    ![Mapowane](media/lab-data-flow-data-share/all-mapped.png)
    
1. Wybierz **pozycję Szczegóły**. 

    Należy zauważyć, że **migawka wyzwalacza** nie jest już wyszarzone, ponieważ udział danych ma teraz miejsca docelowe do skopiowania.

1. Wybierz migawkę wyzwalacza -> pełnej kopii. 

    ![Wyzwalacz](media/lab-data-flow-data-share/trigger-full.png)

    Spowoduje to rozpoczęcie kopiowania danych do nowego konta udziału danych. W rzeczywistym scenariuszu dane te pochodzą od strony trzeciej. 

    Natknąć się na dane na około 3-5 minut. Postęp można monitorować, klikając kartę **Historia.** 

    Podczas oczekiwania przejdź do oryginalnego udziału danych (DataProvider) i wyświetl stan subskrypcji **udostępniania** i **historii** kartę. 

1. Przejdź z powrotem do udziału danych konsumenta danych. Po pomyślnym podaniu stanu wyzwalacza przejdź do docelowej bazy danych SQL i usługi Data Lake, aby zobaczyć, że dane wylądowały w odpowiednich magazynach. 

Gratulacje, ukończyłeś laboratorium!


