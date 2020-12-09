---
title: Integracja danych przy użyciu Azure Data Factory i udziału danych platformy Azure
description: Kopiowanie, przekształcanie i udostępnianie danych przy użyciu Azure Data Factory i udziału danych platformy Azure
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: bdf9cbfef7dfdcf80976641b527ddeb61368d50b
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921038"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integracja danych przy użyciu Azure Data Factory i udziału danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Jako że klienci logują się do nowoczesnych magazynów danych i projektów analitycznych, wymagają nie tylko większej ilości danych, ale również mają większą widoczność w swoich danych. Ta warsztat omówieniach do sposobu, w jaki ulepszenia Azure Data Factory i udział danych platformy Azure upraszczają integrację danych i zarządzanie nimi na platformie Azure. 

W celu zapewnienia kompleksowego wglądu w dane w postaci ETL/ELT w celu utworzenia obszernego widoku danych ulepszenia w Azure Data Factory umożliwią inżynierom danych dołączenie większej ilości danych, a tym samym zwiększenie wartości do przedsiębiorstwa. Udział danych platformy Azure umożliwi prowadzenie biznesowego udostępniania w biznesie.

W tej warsztatie będziesz używać Azure Data Factory (ADF) do pozyskiwania danych z Azure SQL Database do Azure Data Lake Storage Gen2 (ADLS Gen2). Po pobraniu danych z usługi Lake przekształćsz je za pośrednictwem mapowania przepływów danych, natywnej usługi transformacji fabryki danych i ujścia do usługi Azure Synapse Analytics. Następnie udostępnimy tabelę z przekształconymi danymi wraz z dodatkowymi danymi za pomocą udziału danych platformy Azure. 

Dane używane w tym laboratorium to dane z taksówki w Nowym Jorku. Aby zaimportować go do bazy danych w SQL Database, Pobierz [plik taksówka-dane BACPAC](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* **Azure SQL Database**: Jeśli nie masz bazy danych SQL, Dowiedz się, jak [utworzyć konto bazy danych SQL](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 konta magazynu**: Jeśli nie masz konta magazynu ADLS Gen2, Dowiedz się, jak [utworzyć konto magazynu ADLS Gen2](../storage/common/storage-account-create.md).

* **Azure Synapse Analytics**: Jeśli nie masz usługi Azure Synapse Analytics, Dowiedz się, jak [utworzyć wystąpienie usługi Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* **Azure Data Factory**: Jeśli Fabryka danych nie została utworzona, zapoznaj się z tematem jak [utworzyć fabrykę danych](./quickstart-create-data-factory-portal.md).

* **Udział danych platformy Azure**: Jeśli nie utworzono udziału danych, zapoznaj się z tematem jak [utworzyć udział danych](../data-share/share-your-data.md#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Skonfiguruj środowisko Azure Data Factory

W tej sekcji dowiesz się, jak uzyskać dostęp do środowiska użytkownika Azure Data Factory (UX APD) z Azure Portal. Raz w środowisku APD ADF skonfigurujesz trzy połączone usługi dla każdego z używanych przez nas magazynów danych: Azure SQL DB, ADLS Gen2 i Azure Synapse Analytics.

W obszarze Azure Data Factory połączone usługi definiują informacje o połączeniu z zasobami zewnętrznymi. Azure Data Factory obecnie obsługuje ponad 85 łączników.

### <a name="open-the-azure-data-factory-ux"></a>Otwórz środowisko Azure Data Factory

1. Otwórz [Azure Portal](https://portal.azure.com) w przeglądarce Microsoft Edge lub Google Chrome.
1. Korzystając z paska wyszukiwania w górnej części strony, wyszukaj frazę "fabryki danych".

    ![Portal 1](media/lab-data-flow-data-share/portal1.png)
1. Kliknij zasób fabryki danych, aby otworzyć jego blok zasobów.

    ![Portal 2](media/lab-data-flow-data-share/portal2.png)
1. Kliknij pozycję **Tworzenie i monitorowanie** , aby otworzyć środowisko ADF. Dostęp do środowiska ADF można także uzyskać pod adresem adf.azure.com.

    ![Portal 3](media/lab-data-flow-data-share/portal3.png)
1. Nastąpi przekierowanie do strony głównej środowiska ADF. Ta strona zawiera Przewodniki Szybki Start, wideo z instrukcjami i linki do samouczków umożliwiających zapoznanie się z pojęciami dotyczącymi fabryki danych. Aby rozpocząć tworzenie, kliknij ikonę ołówka na pasku po lewej stronie.

    ![Konfiguracja portalu](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database

1. Na stronie Tworzenie można tworzyć zasoby usługi Data Factory, takie jak potoki, zestawy danych, przepływy, dane, wyzwalacze i połączone usługi. Aby utworzyć połączoną usługę, kliknij przycisk **połączenia** w prawym dolnym rogu.

    ![Portal — Konfigurowanie 2](media/lab-data-flow-data-share/configure2.png)
1. Na karcie połączenia kliknij przycisk **Nowy** , aby dodać nową połączoną usługę.

    ![Konfiguracja portalu 3](media/lab-data-flow-data-share/configure3.png)
1. Pierwsza połączona usługa, którą skonfigurujesz, to baza danych SQL platformy Azure. Korzystając z paska wyszukiwania, można filtrować listę magazynów danych. Kliknij kafelek **Azure SQL Database** i kliknij przycisk Kontynuuj.

    ![Konfiguracja portalu 4](media/lab-data-flow-data-share/configure4.png)
1. W okienku Konfiguracja bazy danych SQL wprowadź wartość "SQLDB" jako nazwę połączonej usługi. Wprowadź swoje poświadczenia, aby umożliwić usłudze Data Factory łączenie się z bazą danych. Jeśli używasz uwierzytelniania SQL, wprowadź wartość w polu Nazwa serwera, baza danych, nazwa użytkownika i hasło. Aby sprawdzić, czy informacje o połączeniu są poprawne, kliknij przycisk **Test connection**. Po zakończeniu kliknij przycisk **Utwórz** .

    ![Konfiguracja portalu 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Tworzenie połączonej usługi Azure Synapse Analytics

1. Powtórz ten sam proces, aby dodać połączoną usługę Azure Synapse Analytics. Na karcie połączenia kliknij pozycję **Nowy**. Wybierz kafelek **Azure Synapse Analytics** , a następnie kliknij przycisk Kontynuuj.

    ![Konfiguracja portalu 6](media/lab-data-flow-data-share/configure6.png)
1. W okienku Konfiguracja połączonej usługi wprowadź wartość "SQLDW" jako nazwę połączonej usługi. Wprowadź swoje poświadczenia, aby umożliwić usłudze Data Factory łączenie się z bazą danych. Jeśli używasz uwierzytelniania SQL, wprowadź wartość w polu Nazwa serwera, baza danych, nazwa użytkownika i hasło. Aby sprawdzić, czy informacje o połączeniu są poprawne, kliknij przycisk **Test connection**. Po zakończeniu kliknij przycisk **Utwórz** .

    ![Konfiguracja portalu 7](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Tworzenie połączonej usługi Azure Data Lake Storage Gen2

1. Ostatnia połączona usługa wymagana dla tego laboratorium to Azure Data Lake Storage Gen2.  Na karcie połączenia kliknij pozycję **Nowy**. Wybierz kafelek **Azure Data Lake Storage Gen2** i kliknij przycisk Kontynuuj.

    ![Portal — Konfigurowanie 8](media/lab-data-flow-data-share/configure8.png)
1. W okienku Konfiguracja połączonej usługi wprowadź wartość "ADLSGen2" jako nazwę połączonej usługi. Jeśli używasz uwierzytelniania klucza konta, wybierz konto usługi ADLS Gen2 Storage z listy rozwijanej **nazwa konta magazynu** . Aby sprawdzić, czy informacje o połączeniu są poprawne, kliknij przycisk **Test connection**. Po zakończeniu kliknij przycisk **Utwórz** .

    ![Portal — Konfigurowanie 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Włącz tryb debugowania przepływu danych

W sekcji *Przekształcanie danych przy użyciu funkcji mapowania przepływu danych* będziesz tworzyć przepływy danych mapowania. Najlepszym rozwiązaniem przed kompilacją przepływu danych mapowania jest włączenie trybu debugowania, co pozwala na przetestowanie logiki transformacji w ciągu kilku sekund w aktywnym klastrze Spark.

Aby włączyć debugowanie, kliknij suwak **debugowania przepływu danych** na górnym pasku fabryki. Kliknij przycisk OK po wyświetleniu okna dialogowego potwierdzenia. Uruchomienie klastra zajmie około 5-7 minut. Kontynuuj, aby pozyskiwać *dane z usługi Azure SQL DB do ADLS Gen2 przy użyciu działania kopiowania* podczas jego inicjowania.

![Portal — Konfigurowanie 10](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Pozyskiwanie danych przy użyciu działania kopiowania

W tej sekcji utworzysz potok za pomocą działania kopiowania, które pozyskuje jedną tabelę z bazy danych SQL Azure do konta magazynu ADLS Gen2. Dowiesz się, jak dodać potok, skonfigurować zestaw danych i debugować potok za pośrednictwem środowiska ADF. Wzorzec konfiguracji używany w tej sekcji może zostać zastosowany do kopiowania z relacyjnego magazynu danych do magazynu danych opartego na plikach.

W Azure Data Factory potok jest logicznym grupą działań, które wspólnie wykonują zadanie. Działanie definiuje operację do wykonania na danych. Zestaw danych wskazuje dane, które mają być używane w połączonej usłudze.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Tworzenie potoku za pomocą działania kopiowania

1. W okienku zasoby fabryki kliknij ikonę znaku plus, aby otworzyć menu nowy zasób. Wybierz pozycję **potok**.

    ![Kopia portalu 1](media/lab-data-flow-data-share/copy1.png)
1. Na karcie **Ogólne** kanwy potoku Nazwij swój potok, na przykład "IngestAndTransformTaxiData".

    ![Kopia portalu 2](media/lab-data-flow-data-share/copy2.png)
1. W okienku działania kanwy potoku Otwórz pozycję **przenoszenie i przekształcanie** , a następnie przeciągnij działanie **Kopiuj dane** na kanwę. Nadaj działanie kopiowania opisową nazwę, taką jak "IngestIntoADLS".

    ![Kopia portalu 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurowanie źródłowego zestawu danych usługi Azure SQL DB

1. Kliknij kartę **Źródło** działania kopiowania. Aby utworzyć nowy zestaw danych, kliknij przycisk **Nowy**. Źródłem będzie tabela "dbo. TripData ' znajduje się w wcześniej skonfigurowanym elemencie "SQLDB" połączonej usługi.

    ![Kopia portalu 4](media/lab-data-flow-data-share/copy4.png)
1. Wyszukaj **Azure SQL Database** i kliknij przycisk Kontynuuj.

    ![Kopia portalu 5](media/lab-data-flow-data-share/copy5.png)
1. Wywołaj zestaw danych "TripData". Wybierz pozycję "SQLDB" jako połączoną usługę. Wybierz nazwę tabeli "dbo. TripData ' z listy rozwijanej Nazwa tabeli. Zaimportuj schemat **z połączenia/magazynu**. Po zakończeniu kliknij przycisk OK.

    ![Kopia portalu 6](media/lab-data-flow-data-share/copy6.png)

Zestaw danych źródłowych został pomyślnie utworzony. Upewnij się, że w ustawieniach źródła jest wybrana **tabela** wartość domyślna w polu Użyj zapytania.

### <a name="configure-adls-gen2-sink-dataset"></a>Konfigurowanie zestawu danych ujścia ADLS Gen2

1. Kliknij kartę **ujścia** działania kopiowania. Aby utworzyć nowy zestaw danych, kliknij przycisk **Nowy**.

    ![Kopia portalu 7](media/lab-data-flow-data-share/copy7.png)
1. Wyszukaj **Azure Data Lake Storage Gen2** i kliknij przycisk Kontynuuj.

    ![Kopia portalu 8](media/lab-data-flow-data-share/copy8.png)
1. W okienku wybierz Format wybierz pozycję **DelimitedText** podczas zapisywania do pliku CSV. Kliknij przycisk Kontynuuj.

    ![Kopia portalu 9](media/lab-data-flow-data-share/copy9.png)
1. Nazwij zestaw danych ujścia "TripDataCSV". Wybierz pozycję "ADLSGen2" jako połączoną usługę. Wprowadź miejsce zapisania pliku CSV. Można na przykład napisać dane do pliku `trip-data.csv` w kontenerze `staging-container` . Ustaw **pierwszy wiersz jako nagłówek** na wartość true, ponieważ chcesz, aby dane wyjściowe miały nagłówki. Ponieważ plik nie istnieje jeszcze w miejscu docelowym, ustaw dla opcji **Importuj schemat** **wartość Brak**. Po zakończeniu kliknij przycisk OK.

    ![Kopia portalu 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Przetestuj działanie kopiowania z przebiegiem debugowania potoku

1. Aby sprawdzić, czy działanie kopiowania działa prawidłowo, kliknij pozycję **Debuguj** w górnej części kanwy potoku, aby wykonać przebieg debugowania. Uruchomienie debugowania umożliwia przetestowanie potoku albo zakończenie do końca, albo do punktu przerwania przed opublikowaniem go w usłudze Data Factory.

    ![Kopia portalu 11](media/lab-data-flow-data-share/copy11.png)
1. Aby monitorować przebieg debugowania, przejdź do karty **dane wyjściowe** kanwy potoku. Ekran monitorowania będzie odświeżany co 20 sekund lub po ręcznym kliknięciu przycisku Odśwież. Działanie kopiowania ma specjalny widok monitorowania, do którego można uzyskać dostęp, klikając ikonę okularów oczu w kolumnie **Akcje** .

    ![Kopia portalu 12](media/lab-data-flow-data-share/copy12.png)
1. Widok Monitorowanie kopiowania zawiera szczegóły i charakterystyki wydajności działania. Można wyświetlić informacje takie jak Odczyt/zapis danych, wiersze do odczytu/zapisu, pliki odczytu/zapisu oraz przepływność. Jeśli wszystko zostało poprawnie skonfigurowane, powinny pojawić się wiersze 49 999 zapisane w jednym pliku w ujściach ADLS.

    ![Kopia portalu 13](media/lab-data-flow-data-share/copy13.png)
1. Przed przejściem do następnej sekcji zaleca się opublikowanie zmian w usłudze Data Factory, klikając przycisk **Opublikuj wszystko** na górnym pasku fabryki. Chociaż nie zostało to omówione w tym laboratorium, Azure Data Factory obsługuje pełną integrację narzędzia Git. Integracja z usługą git pozwala na kontrolę wersji, iteracyjne zapisywanie w repozytorium i współpracę w fabryce danych. Aby uzyskać więcej informacji, zobacz [Kontrola źródła w Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    ![Publikowanie w portalu 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Przekształcanie danych przy użyciu przepływu danych mapowania

Teraz, gdy dane zostały pomyślnie skopiowane do Azure Data Lake Storage, można przyłączyć i agregować te dane do magazynu danych. Będziemy używać mapowania przepływu danych, Azure Data Factory zaprojektowanej wizualnie usłudze transformacji. Mapowanie przepływów danych pozwala użytkownikom na tworzenie kodu logiki transformacji — bezpłatnie i wykonywanie ich w klastrach Spark zarządzanych przez usługę ADF.

Przepływ danych utworzony w tym kroku wewnętrzny przyłączy zestaw danych "TripDataCSV" utworzony w poprzedniej sekcji za pomocą tabeli "dbo. TripFares "przechowywany w" SQLDB "na podstawie czterech kolumn klucza. Następnie dane są agregowane w oparciu o kolumnę `payment_type` , aby obliczyć średnią niektórych pól i zapisaną w tabeli analizy usługi Azure Synapse.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Dodawanie działania przepływu danych do potoku

1. W okienku działania kanwy potoku Otwórz przystawkę **przenoszenie i przekształcanie** i przeciągnij aktywność **przepływu danych** na kanwę.

    ![Przepływ danych portalu 1](media/lab-data-flow-data-share/dataflow1.png)
1. W okienku po otwarciu wybierz pozycję **Utwórz nowy przepływ danych** i wybierz pozycję **Mapowanie przepływu danych**. Kliknij przycisk **OK**.

    ![Przepływ danych portalu 2](media/lab-data-flow-data-share/dataflow2.png)
1. Nastąpi przekierowanie do kanwy przepływu danych, w której będziesz kompilować logikę transformacji. Na karcie Ogólne Nadaj nazwę przepływowi danych "JoinAndAggregateData".

    ![Przepływ danych portalu 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Skonfiguruj źródło CSV danych dla podróży

1. Najpierw należy skonfigurować dwa przekształcenia źródłowe. Pierwsze Źródło wskaże zestaw danych DelimitedText "TripDataCSV". Aby dodać transformację źródłową, kliknij pole **Dodaj źródło** na kanwie.

    ![Przepływ danych portalu 4](media/lab-data-flow-data-share/dataflow4.png)
1. Nazwij Źródło "TripDataCSV" i wybierz zestaw danych "TripDataCSV" z listy rozwijanej Źródło. Jeśli zapamiętasz, schemat nie został wcześniej zaimportowany podczas tworzenia tego zestawu danych, ponieważ nie było tam żadnych danych. Ponieważ `trip-data.csv` już istnieje, kliknij przycisk **Edytuj** , aby przejść do karty ustawienia zestawu danych.

    ![Przepływ danych portalu 5](media/lab-data-flow-data-share/dataflow5.png)
1. Przejdź do **schematu** karty i kliknij pozycję **Importuj schemat**. Wybierz pozycję **połączenie/magazyn** , aby zaimportować bezpośrednio z magazynu plików. 14 kolumn typu String powinna pojawić się.

    ![Przepływ danych portalu 6](media/lab-data-flow-data-share/dataflow6.png)
1. Wróć do przepływu danych "JoinAndAggregateData". Jeśli klaster debugowania został uruchomiony (wskazany przez zielony okrąg obok suwaka debugowania), możesz uzyskać migawkę danych na karcie **Podgląd danych** . Kliknij przycisk **Odśwież** , aby pobrać Podgląd danych.

    ![Przepływ danych portalu 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Podgląd danych nie zapisuje danych.

### <a name="configure-your-trip-fares-sql-db-source"></a>Skonfiguruj źródło bazy danych SQL opłaty za podróż

1. Drugie dodawane Źródło będzie wskazywało obiekt dbo tabeli bazy danych SQL. TripFares'. W źródle "TripDataCSV" pojawi się inne pole **źródłowe** . Kliknij go, aby dodać nową transformację źródłową.

    ![Przepływ danych portalu 8](media/lab-data-flow-data-share/dataflow8.png)
1. Nadaj nazwę temu źródłowi "TripFaresSQL". Kliknij przycisk **nowe** obok pola zestaw danych źródłowych, aby utworzyć nowy zestaw danych bazy danych SQL.

    ![Przepływ danych portalu 9](media/lab-data-flow-data-share/dataflow9.png)
1. Wybierz kafelek **Azure SQL Database** i kliknij przycisk Kontynuuj. *Uwaga: możesz zauważyć, że wiele łączników w usłudze Fabryka danych nie jest obsługiwane w mapowaniu przepływu danych. Aby przekształcić dane z jednego z tych źródeł, należy pozyskać je do obsługiwanego źródła za pomocą działania kopiowania*.

    ![Przepływ danych portalu 10](media/lab-data-flow-data-share/dataflow10.png)
1. Wywołaj zestaw danych "TripFares". Wybierz pozycję "SQLDB" jako połączoną usługę. Wybierz nazwę tabeli "dbo. TripFares ' z listy rozwijanej Nazwa tabeli. Zaimportuj schemat **z połączenia/magazynu**. Po zakończeniu kliknij przycisk OK.

    ![Przepływ danych portalu 11](media/lab-data-flow-data-share/dataflow11.png)
1. Aby sprawdzić dane, Pobierz Podgląd danych na karcie **Podgląd danych** .

    ![Przepływ danych portalu 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Wewnętrzne sprzężenie TripDataCSV i TripFaresSQL

1. Aby dodać nową transformację, kliknij ikonę znaku plus w prawym dolnym rogu elementu "TripDataCSV". W obszarze **wielu danych wejściowych/wyjściowych** wybierz pozycję **Dołącz**.

    ![Dołączenie do portalu 1](media/lab-data-flow-data-share/join1.png)
1. Nazwij transformację join "InnerJoinWithTripFares". Wybierz pozycję "TripFaresSQL" z listy rozwijanej odpowiedniego strumienia. Wybierz pozycję **wewnętrzne** jako typ sprzężenia. Aby dowiedzieć się więcej o różnych typach sprzężeń w mapowaniu przepływu danych, zobacz [typy sprzężeń](./data-flow-join.md#join-types).

    Wybierz kolumny, które mają być zgodne z każdym strumieniem za pomocą listy rozwijanej **warunki sprzężenia** . Aby dodać dodatkowy warunek sprzężenia, kliknij ikonę znaku plus obok istniejącego warunku. Domyślnie wszystkie warunki sprzężenia są łączone z operatorem AND, co oznacza, że wszystkie warunki muszą zostać spełnione w celu dopasowania. W tym laboratorium chcemy dopasować kolumny `medallion` , `hack_license` , `vendor_id` i `pickup_datetime`

    ![Dołącz do portalu 2](media/lab-data-flow-data-share/join2.png)
1. Sprawdź, czy pomyślnie dołączono 25 kolumn wraz z podglądem danych.

    ![Dołącz do portalu 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agreguj według payment_type

1. Po zakończeniu transformacji sprzężenia Dodaj transformację agregacji, klikając ikonę znaku plus obok pozycji "InnerJoinWithTripFares". Wybierz pozycję **Agreguj** pod **modyfikatorem schematu**.

    ![Portal AGG 1](media/lab-data-flow-data-share/agg1.png)
1. Nadaj agregacji agregowanie "AggregateByPaymentType". Wybierz `payment_type` jako kolumnę Grupuj według.

    ![Portal AGG 2](media/lab-data-flow-data-share/agg2.png)
1. Przejdź do karty **agregaty** . W tym miejscu określisz dwie agregacje:
    * Średnia opłata według typu płatności
    * Łączna odległość dla podróży pogrupowana według typu płatności

    Najpierw utworzysz wyrażenie średniej opłaty. W polu tekstowym **Dodaj lub zaznacz kolumnę** wpisz "average_fare".

    ![Portal AGG 3](media/lab-data-flow-data-share/agg3.png)
1. Aby wprowadzić wyrażenie agregacji, kliknij niebieskie pole z etykietą **wprowadź wyrażenie**. Spowoduje to otwarcie konstruktora wyrażeń przepływu danych, narzędzia służącego do wizualnego tworzenia wyrażeń przepływu danych przy użyciu schematu wejściowego, wbudowanych funkcji i operacji oraz parametrów zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji o możliwościach konstruktora wyrażeń, zobacz dokumentację programu [Expression Builder](./concepts-data-flow-expression-builder.md).

    Aby uzyskać średnią opłatę, użyj `avg()` funkcji agregacji do agregowania `total_amount` rzutowania kolumny do liczby całkowitej za pomocą `toInteger()` . W języku wyrażeń przepływu danych jest to zdefiniowane jako `avg(toInteger(total_amount))` . Po zakończeniu kliknij przycisk **Zapisz i Zakończ** .

    ![Portal AGG 4](media/lab-data-flow-data-share/agg4.png)
1. Aby dodać dodatkowe wyrażenie agregacji, kliknij ikonę znaku plus obok pozycji `average_fare` . Wybierz pozycję **Dodaj kolumnę**.

    ![Portal AGG 5](media/lab-data-flow-data-share/agg5.png)
1. W polu tekstowym **Dodaj lub zaznacz kolumnę** wpisz "total_trip_distance". Jak w ostatnim kroku, Otwórz konstruktora wyrażeń, aby wprowadzić wyrażenie.

    Aby uzyskać łączną odległość podróży, użyj `sum()` funkcji agregacji, aby agregować `trip_distance` rzutowanie kolumny na liczbę całkowitą z `toInteger()` . W języku wyrażeń przepływu danych jest to zdefiniowane jako `sum(toInteger(trip_distance))` . Po zakończeniu kliknij przycisk **Zapisz i Zakończ** .

    ![Portal AGG 6](media/lab-data-flow-data-share/agg6.png)
1. Przetestuj logikę transformacji na karcie **Podgląd danych** . Jak widać, istnieje znacznie mniej wierszy i kolumn niż poprzednio. Tylko trzy grupy według i kolumny agregacji zdefiniowane w tym przekształceniu są kontynuowane. Ponieważ w przykładzie istnieje tylko pięć grup typów płatności, są zwracane tylko pięć wierszy.

    ![Portal AGG 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurowanie ujścia usługi Azure Synapse Analytics

1. Po ukończeniu naszej logiki transformacji jesteśmy gotowi do ujścia naszych danych w tabeli usługi Azure Synapse Analytics. Dodaj transformację ujścia w sekcji **docelowej** .

    ![Ujścia portalu 1](media/lab-data-flow-data-share/sink1.png)
1. Nazwij obiekt ujścia "SQLDWSink". Kliknij przycisk **nowe** obok pola zestaw danych ujścia, aby utworzyć nowy zestaw danych usługi Azure Synapse Analytics.

    ![Portal portalu 2](media/lab-data-flow-data-share/sink2.png)

1. Wybierz kafelek **Azure Synapse Analytics** , a następnie kliknij przycisk Kontynuuj.

    ![Ujścia portalu 3](media/lab-data-flow-data-share/sink3.png)
1. Wywołaj zestaw danych "AggregatedTaxiData". Wybierz pozycję "SQLDW" jako połączoną usługę. Wybierz pozycję **Utwórz nową tabelę** i nazwij nową tabelę dbo. AggregateTaxiData. Po zakończeniu kliknij przycisk OK.

    ![Portal portalu 4](media/lab-data-flow-data-share/sink4.png)
1. Przejdź do karty **Ustawienia** ujścia. Ponieważ tworzymy nową tabelę, musimy wybrać polecenie **Utwórz ponownie tabelę** w obszarze Akcja tabeli. Usuń zaznaczenie opcji **Włącz proces przemieszczania**, który przełącza, czy wstawiasz wiersz po wierszu czy w usłudze Batch.

    ![Ujścia portalu 5](media/lab-data-flow-data-share/sink5.png)

Przepływ danych został pomyślnie utworzony. Teraz czas na uruchomienie go w działaniu potoku.

### <a name="debug-your-pipeline-end-to-end"></a>Debugowanie końca potoku

1. Wróć do karty dla potoku **IngestAndTransformData** . Zwróć uwagę na zieloną ramkę działania kopiowania "IngestIntoADLS". Przeciągnij go do działania przepływu danych "JoinAndAggregateData". Spowoduje to utworzenie elementu "on Success", co spowoduje, że działanie przepływu danych zostanie uruchomione tylko w przypadku pomyślnego wykonania kopii.

    ![Potok portalu 1](media/lab-data-flow-data-share/pipeline1.png)
1. Tak jak w przypadku działania kopiowania kliknij polecenie **Debuguj** , aby wykonać przebieg debugowania. W przypadku przebiegów debugowania działanie przepływu danych będzie używać aktywnego klastra debugowania zamiast wychodzić z nowego klastra. Wykonanie tego potoku zajmie trochę czasu.

    ![Potok portalu 2](media/lab-data-flow-data-share/pipeline2.png)
1. Podobnie jak w przypadku działania kopiowania przepływ danych ma specjalny widok monitorowania, do którego uzyskuje dostęp ikona okularów po zakończeniu działania.

    ![Potok portalu 3](media/lab-data-flow-data-share/pipeline3.png)
1. W widoku monitorowanie można zobaczyć uproszczony wykres przepływu danych wraz z czasem wykonywania i wierszami w każdym etapie wykonywania. W przypadku poprawnego działania należy agregować 49 999 wierszy w pięć wierszy w tym działaniu.

    ![Potok portalu 4](media/lab-data-flow-data-share/pipeline4.png)
1. Możesz kliknąć przekształcenie, aby uzyskać dodatkowe szczegóły dotyczące jego wykonania, takie jak informacje o partycjonowaniu i nowe/zaktualizowane/opuszczone kolumny.

    ![Potok portalu 5](media/lab-data-flow-data-share/pipeline5.png)

Część fabryki danych w tym laboratorium została ukończona. Opublikuj zasoby, jeśli chcesz je operacjonalizować z wyzwalaczami. Pomyślnie uruchomiono potok, który pozyskał dane z Azure SQL Database, aby Azure Data Lake Storage przy użyciu działania kopiowania, a następnie agregować te dane w usłudze Azure Synapse Analytics. Możesz sprawdzić, czy dane zostały prawidłowo napisane, patrząc na SQL Server samego siebie.

## <a name="share-data-using-azure-data-share"></a>Udostępnianie danych za pomocą usługi Azure Data Share

W tej sekcji dowiesz się, jak skonfigurować nowy udział danych przy użyciu Azure Portal. Obejmuje to utworzenie nowego udziału danych, który będzie zawierać zestawy DataSet z Azure Data Lake Store Gen2 i Azure Synapse Analytics. Następnie skonfigurujesz harmonogram migawek, który zapewni odbiorcom danych opcję automatycznego odświeżania danych, które są im udostępniane. Następnie zapraszasz adresatów do udziału danych. 

Po utworzeniu udziału danych następnie Przełącz systemy i Zostań *konsumentem danych*. Jako odbiorca danych postanowisz o zaakceptowaniu zaproszenia do udziału danych, konfigurowaniu miejsca, w którym dane mają być odbierane, oraz mapowania zestawów danych do różnych lokalizacji magazynu. Następnie wyzwolisz migawkę, która skopiuje dane udostępnione Tobie do określonego miejsca docelowego. 

### <a name="sharing-data-data-provider-flow"></a>Udostępnianie danych (przepływ Dostawca danych)

1. Otwórz Azure Portal w przeglądarce Microsoft Edge lub Google Chrome.

1. Korzystając z paska wyszukiwania w górnej części strony, Wyszukaj **udziały danych** .

    ![Reklamy portalu](media/lab-data-flow-data-share/portal-ads.png)

1. Wybierz konto udziału danych z nazwą "Provider". Na przykład **DataProvider0102**. 

1. Wybierz pozycję **Rozpocznij udostępnianie danych**

    ![Rozpocznij udostępnianie](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Wybierz pozycję **+ Utwórz** , aby rozpocząć konfigurowanie nowego udziału danych. 

1. W obszarze **Nazwa udziału** Określ wybraną nazwę. Jest to nazwa udziału, która będzie widoczna dla konsumenta danych, dlatego należy nadać jej nazwę opisową, taką jak TaxiData.

1. W obszarze **Opis** Umieść zdanie, które opisuje zawartość udziału danych. Udział danych będzie zawierać ogólnoświatowe dane o podróży z taksówkami, które są przechowywane w wielu sklepach, w tym na platformie Azure Synapse Analytics i Azure Data Lake Store. 

1. W obszarze **warunki użytkowania** Określ zestaw warunków, które mają być zgodne z klientem danych. Oto kilka przykładów: "nie Dystrybuuj tych danych poza Twoją organizację" lub "Zapoznaj się z umową prawną". 

    ![Szczegóły udostępniania](media/lab-data-flow-data-share/ads-details.png)

1. Wybierz opcję **Kontynuuj**. 

1. Wybierz pozycję **Dodaj zestawy danych** 

    ![Dodaj zestaw danych 1](media/lab-data-flow-data-share/add-dataset.png)

1. Wybierz pozycję **Azure Synapse Analytics** , aby wybrać tabelę z usługi Azure Synapse Analytics, w której wykorzystano przekształcenia ADF.

    ![Dodaj zestaw danych SQL](media/lab-data-flow-data-share/add-dataset-sql.png)


1. Otrzymasz skrypt do uruchomienia, zanim będzie można wykonać operację. Dostarczony skrypt tworzy użytkownika w bazie danych SQL, aby zezwolić na uwierzytelnianie za pomocą pliku MSI udostępniania danych platformy Azure w jego imieniu. 

> [!IMPORTANT]
> Przed uruchomieniem skryptu należy ustawić jako administratora Active Directory SQL Server. 

1. Otwórz nową kartę i przejdź do Azure Portal. Skopiuj podany skrypt, aby utworzyć użytkownika w bazie danych, z której chcesz udostępnić dane. W tym celu zaloguj się do bazy danych rozszerzenia przy użyciu Eksploratora zapytań (wersja zapoznawcza) przy użyciu uwierzytelniania usługi AAD. 

    Należy zmodyfikować skrypt, tak aby utworzony przez niego użytkownik był zawarty w nawiasach. Zwrotnym
    
    Utwórz użytkownika [DataProvider-xxxx] z logowania zewnętrznego;  db_owner sp_addrolemember exec, [DataProvider-xxxx];
    
1. Przełącz się z powrotem do udziału danych platformy Azure, w którym dodano zestawy danych do swojego udziału. 

1. Wybierz pozycję **rozszerzenia**, a następnie wybierz pozycję **AggregatedTaxiData** dla tabeli. 

1. Wybierz pozycję **Dodaj zestaw danych**

    Mamy teraz tabelę SQL, która jest częścią naszego zestawu danych. Następnie dodamy dodatkowe zestawy danych z Azure Data Lake Store. 

1. Wybierz pozycję **Dodaj zestaw danych** i wybierz pozycję **Azure Data Lake Store Gen2**

    ![Dodaj zestaw danych ADLS](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Wybierz pozycję **Dalej**

1. Rozwiń węzeł *wwtaxidata*. Rozwiń pozycję *dane dotyczące taksówki Boston*. Zwróć uwagę, że możesz udostępnić na poziomie pliku. 

1. Wybierz folder *dane z taksówką Boston* , aby dodać cały folder do udziału danych. 

1. Wybierz pozycję **Dodaj zestawy danych**

1. Przejrzyj zestawy danych, które zostały dodane. Do udziału danych powinna zostać dodana tabela SQL i folder ADLS Gen2. 

1. Wybierz przycisk **Kontynuuj**

1. Na tym ekranie możesz dodać odbiorców do udziału danych. Dodanie adresatów spowoduje odebranie zaproszeń do udziału danych. Na potrzeby tego laboratorium należy dodać 2 adresy e-mail:

    1. Adres e-mail subskrypcji platformy Azure, w której jesteś. 

        ![Dodawanie adresatów](media/lab-data-flow-data-share/add-recipients.png)

    1. Dodaj do fikcyjnego konsumenta danych o nazwie *janedoe@fabrikam.com* .

1. Na tym ekranie można skonfigurować ustawienie migawki dla konsumenta danych. Dzięki temu będą one otrzymywać regularne aktualizacje danych w określonym przez siebie interwale. 

1. Sprawdź **harmonogram migawek** i skonfiguruj co godzinę odświeżanie danych przy użyciu listy rozwijanej *cykl* .  

1. Wybierz przycisk **Utwórz**.

    Masz już aktywny udział danych. Umożliwia przegląd informacji o tym, co można zobaczyć jako dostawca danych podczas tworzenia udziału danych. 

1. Wybierz utworzony udział danych, zatytułowany **DataProvider**. Możesz przejść do niego, wybierając pozycję **wysłane udziały** w **udziale danych**. 

1. Kliknij pozycję harmonogram migawek. Harmonogram migawek można wyłączyć, jeśli wybierzesz opcję. 

1. Następnie wybierz kartę **zestawy danych** . Po utworzeniu tego udziału danych można dodać do niego kolejne zestawy. 

1. Wybierz kartę **udostępnianie subskrypcji** . Nie istnieją jeszcze subskrypcje udziałów, ponieważ odbiorca danych nie zaakceptował jeszcze zaproszenia.

1. Przejdź do karty **zaproszenia** . W tym miejscu zostanie wyświetlona lista oczekujących zaproszeń. 

    ![Oczekujące zaproszenia](media/lab-data-flow-data-share/pending-invites.png)

1. Wybierz zaproszenie do *janedoe@fabrikam.com* . Wybierz pozycję Usuń. Jeśli odbiorca nie zaakceptował jeszcze zaproszenia, nie będzie już można tego robić. 

1. Wybierz kartę **historia** . Nic nie jest jeszcze wyświetlane, ponieważ odbiorca danych nie zaakceptował jeszcze zaproszenia i wyzwolił migawkę. 

### <a name="receiving-data-data-consumer-flow"></a>Otrzymywanie danych (przepływ odbiorcy danych)

Po przejrzeniu udziału danych jesteśmy gotowi do przełączania się do kontekstu i używania naszego użytkownika danych. 

Teraz masz zaproszenie do udziału danych platformy Azure w skrzynce odbiorczej z Microsoft Azure. Uruchom program Outlook Dostęp w sieci Web (outlook.com) i zaloguj się przy użyciu poświadczeń dostarczonych dla subskrypcji platformy Azure.

W wiadomości e-mail, która powinna zostać odebrana, kliknij pozycję "Wyświetl zaproszenie >". W tym momencie będziesz mieć możliwość symulowania środowiska użytkownika danych w przypadku zaakceptowania przez nich zaproszenia od dostawców danych. 

![Wiadomość e-mail z zaproszeniem](media/lab-data-flow-data-share/email-invite.png)

Może zostać wyświetlony monit o wybranie subskrypcji. Upewnij się, że wybrano subskrypcję, dla której wykonano pracę w ramach tego laboratorium. 

1. Kliknij zaproszenie zatytułowane *DataProvider*. 

1. Na tym ekranie zaproszenia zobaczysz różne szczegóły dotyczące udziału danych, który został wcześniej skonfigurowany jako dostawca danych. Przejrzyj szczegóły i zaakceptuj warunki użytkowania, jeśli zostały podane.

1. Wybierz subskrypcję i grupę zasobów, która już istnieje dla laboratorium. 

1. W obszarze **konto udostępniania danych** wybierz pozycję **dataconsumer**. Możesz również utworzyć nowe konto udostępniania danych. 

1. Obok pozycji **odebrana nazwa udziału** zobaczysz domyślną nazwę udziału, która została określona przez dostawcę danych. Nadaj udziałowi przyjazną nazwę opisującą dane, które chcesz otrzymywać, np. **TaxiDataShare**.

    ![Akceptacja zaproszenia](media/lab-data-flow-data-share/consumer-accept.png)

1. Możesz **zaakceptować i skonfigurować teraz** lub **zaakceptować i skonfigurować później**. Jeśli zdecydujesz się zaakceptować i skonfigurować teraz, określ konto magazynu, dla którego mają zostać skopiowane wszystkie dane. Jeśli zdecydujesz się zaakceptować i skonfigurować później, zestawy danych w udziale zostaną odmapowane i trzeba będzie je ręcznie zmapować. Będziemy w przyszłości wybrać tę opcję. 

1. Wybierz pozycję **Akceptuj i skonfiguruj później**. 

    W przypadku konfigurowania tej opcji zostanie utworzona subskrypcja udziału, ale nie ma miejsca, w którym dane mają być gruntowe, ponieważ nie zamapowana miejsca docelowego. 

    Następnie skonfigurujemy mapowania zestawu danych dla udziału danych. 

1. Wybierz otrzymany udział (nazwę określoną w kroku 5).

    **Migawka wyzwalacza** jest wyszarzona, ale udział jest aktywny. 

1. Wybierz kartę **zestawy danych** . Zwróć uwagę, że każdy zestaw danych jest niezamapowany, co oznacza, że nie ma miejsca docelowego do kopiowania danych. 

    ![niezamapowane zestawy danych](media/lab-data-flow-data-share/unmapped.png)

1. Wybierz tabelę usługi Azure Synapse Analytics, a następnie wybierz pozycję **+ Mapuj, aby określić element docelowy**.

1. Po prawej stronie ekranu wybierz listę rozwijaną **docelowy typ danych** . 

    Dane SQL można mapować do szerokiego zakresu magazynów danych. W takim przypadku będziemy mapować do Azure SQL Database.

    ![mapowanie](media/lab-data-flow-data-share/mapping-options.png)
    
    Obowiązkowe Wybierz **Azure Data Lake Store Gen2** jako docelowy typ danych. 
    
    Obowiązkowe Wybierz subskrypcję, grupę zasobów i konto magazynu, w którym pracujesz. 
    
    Obowiązkowe Dane można odbierać do danych w usłudze Data Lake w formacie CSV lub Parquet. 

1. Obok pozycji **docelowy typ danych** wybierz pozycję Azure SQL Database. 

1. Wybierz subskrypcję, grupę zasobów i konto magazynu, w którym pracujesz. 

    ![Mapuj na SQL](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Przed kontynuowaniem należy utworzyć nowego użytkownika w SQL Server, uruchamiając podany skrypt. Najpierw skopiuj skrypt udostępniony do Schowka. 

1. Otwórz nową kartę Azure Portal. Nie zamykaj istniejącej karty, ponieważ musisz wrócić do niej w chwilę. 

1. Na nowo otwartej karcie przejdź do **bazy danych SQL**.

1. Wybierz bazę danych SQL (w subskrypcji powinna być tylko jedna). Należy zachować ostrożność, aby nie wybierać magazynu danych. 

1. Wybieranie **edytora zapytań (wersja zapoznawcza)**

1. Zaloguj się do edytora zapytań przy użyciu uwierzytelniania usługi AAD. 

1. Uruchom zapytanie podane w udziale danych (skopiowane do Schowka w kroku 14). 

    To polecenie umożliwia usłudze udostępnianie danych platformy Azure używanie tożsamości zarządzanych dla usług platformy Azure do uwierzytelniania w SQL Server, aby można było skopiować do niego dane. 

1. Wróć do oryginalnej karty i wybierz pozycję **Mapuj do lokalizacji docelowej**.

1. Następnie wybierz folder Azure Data Lake Gen2, który jest częścią zestawu danych, i zamapuj go na konto usługi Azure Blob Storage. 

    ![magazyn](media/lab-data-flow-data-share/storage-map.png)

    Wszystkie zamapowane zestawy danych umożliwiają rozpoczęcie otrzymywania danych od dostawcy danych. 

    ![mapowane](media/lab-data-flow-data-share/all-mapped.png)
    
1. Wybierz pozycję **szczegóły**. 

    Zwróć uwagę, że **migawka wyzwalacza** nie jest już wyszarzona, ponieważ udział danych ma teraz miejsca docelowe do kopiowania.

1. Wybierz pozycję wyzwalanie migawki — > pełna kopia. 

    ![uruchamiać](media/lab-data-flow-data-share/trigger-full.png)

    Spowoduje to rozpoczęcie kopiowania danych do nowego konta udziału danych. W realnym świecie dane te byłyby pochodzące od innych firm. 

    Dane będą się znajdować w około 3-5 minutach. Postęp można monitorować, klikając kartę **historia** . 

    Podczas oczekiwania przejdź do oryginalnego udziału danych (DataProvider) i Wyświetl stan karty **udostępnianie subskrypcji** i **historii** . Zwróć uwagę, że istnieje już aktywna subskrypcja i jako dostawca danych, możesz również monitorować, kiedy odbiorca danych rozpoczął odbieranie danych, które są do nich udostępniane. 

1. Przejdź z powrotem do udziału danych odbiorcy danych. Po pomyślnym ukończeniu stanu wyzwalacza przejdź do docelowej bazy danych SQL i Data Lake, aby zobaczyć, że dane zostały załadowane w odpowiednich magazynach. 

Gratulacje, udało Ci się ukończyć laboratorium!