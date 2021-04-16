---
title: Integracja danych przy użyciu Azure Data Factory i Azure Data Share
description: Kopiowanie, przekształcanie i udostępnianie danych przy użyciu Azure Data Factory i Azure Data Share
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: d3924c38f760a9698735a2757bdad2af5beb0e24
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518820"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integracja danych przy użyciu Azure Data Factory i Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W związku z tym, że klienci rozpoczynają nowoczesne projekty magazynu danych i analizy, potrzebują nie tylko większej liczby danych, ale również większej widoczności swoich danych w całej infrastrukturze danych. W tych warsztatach opisano, w jaki sposób ulepszenia Azure Data Factory i Azure Data Share integracji danych i zarządzania nimi na platformie Azure. 

Od włączenia funkcji ETL/ELT bez kodu po tworzenie kompleksowego widoku danych — ulepszenia w platformie Azure Data Factory dają inżynierom danych możliwość większej liczby danych, a tym samym większej wartości, w przedsiębiorstwie. Azure Data Share umożliwia udostępnianie danych między firmami w sposób zarządzany.

W tych warsztatach użyjesz funkcji Azure Data Factory (ADF) do pozysowania danych z Azure SQL Database do Azure Data Lake Storage Gen2 (ADLS Gen2). Po wylądowaniu danych w usłudze Lake przekształcisz je za pośrednictwem przepływów danych mapowania, natywnej usługi przekształcania fabryki danych i ujścia ich do Azure Synapse Analytics. Następnie udostępnisz tabelę przekształcone dane wraz z dodatkowymi danymi przy użyciu Azure Data Share. 

Dane używane w tym laboratorium to dane dotyczące taksówek w Nowym Jorku. Aby zaimportować go do bazy danych w SQL Database, pobierz plik bacpac danych [dotyczących taksówek.](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* **Azure SQL Database:** Jeśli nie masz bazy danych SQL, dowiedz się, jak [utworzyć konto usługi SQL DB](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 magazynu:** jeśli nie masz konta magazynu ADLS Gen2, dowiedz się, jak utworzyć [ADLS Gen2 magazynu.](../storage/common/storage-account-create.md)

* **Azure Synapse Analytics:** jeśli nie masz konta usługi Azure Synapse Analytics, dowiedz się, jak [utworzyć Azure Synapse Analytics wystąpienie.](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)

* **Azure Data Factory:** Jeśli nie utworzono fabryki danych, zobacz, jak [utworzyć fabrykę danych.](./quickstart-create-data-factory-portal.md)

* **Azure Data Share:** Jeśli nie utworzono udziału danych, zobacz, jak [utworzyć udział danych.](../data-share/share-your-data.md#create-a-data-share-account)

## <a name="set-up-your-azure-data-factory-environment"></a>Konfigurowanie środowiska Azure Data Factory danych

W tej sekcji dowiesz się, jak uzyskać dostęp do środowiska Azure Data Factory użytkownika (ADF UX) z Azure Portal. W interfejsie użytkownika usługi ADF skonfigurujesz trzy połączone usługi dla każdego z magazynów danych, których używamy: Azure SQL DB, ADLS Gen2 i Azure Synapse Analytics.

W Azure Data Factory usługi definiują informacje o połączeniu z zasobami zewnętrznymi. Azure Data Factory obecnie obsługuje ponad 85 łączników.

### <a name="open-the-azure-data-factory-ux"></a>Otwieranie środowiska Azure Data Factory użytkownika

1. Otwórz [Azure Portal](https://portal.azure.com) w programie Microsoft Edge lub Google Chrome.
1. Korzystając z paska wyszukiwania w górnej części strony, wyszukaj "Fabryki danych"

    ![Portal 1](media/lab-data-flow-data-share/portal1.png)
1. Kliknij zasób fabryki danych, aby otworzyć blok zasobów.

    ![Portal 2](media/lab-data-flow-data-share/portal2.png)
1. Kliknij pozycję **Tworzenie i monitorowanie,** aby otworzyć interfejs użytkownika usługi ADF. Dostęp do środowiska użytkownika ADF można również uzyskać na stronie adf.azure.com.

    ![Portal 3](media/lab-data-flow-data-share/portal3.png)
1. Nastąpi przekierowanie do strony głównej środowiska użytkownika ADF. Ta strona zawiera przewodnik Szybki start, instruktażowe wideo i linki do samouczków, w których poznasz pojęcia związane z fabryką danych. Aby rozpocząć tworzenie, kliknij ikonę ołówka na lewym pasku bocznym.

    ![Konfigurowanie portalu](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database

1. Aby utworzyć połączona usługę, wybierz pozycję Zarządzaj centrum  na pasku  bocznym po lewej stronie, w okienku Połączenia wybierz pozycję Połączone usługi, a następnie wybierz pozycję **Nowy,** aby dodać nową połączona usługę. 

    ![Konfiguracja portalu 2](media/lab-data-flow-data-share/configure2.png)
1. Pierwsza połączona usługa, która zostanie skonfigurowana, to Azure SQL DB. Listę magazynów danych można filtrować za pomocą paska wyszukiwania. Kliknij kafelek **Azure SQL Database** i kliknij przycisk Kontynuuj.

    ![Konfigurowanie portalu 4](media/lab-data-flow-data-share/configure-4.png)
1. W okienku konfiguracji usługi SQL DB wprowadź "SQLDB" jako nazwę połączonej usługi. Wprowadź swoje poświadczenia, aby zezwolić fabryce danych na łączenie się z bazą danych. Jeśli używasz uwierzytelniania SQL, wprowadź nazwę serwera, bazę danych, nazwę użytkownika i hasło. Aby sprawdzić, czy informacje o połączeniu są poprawne, **kliknij Test connection**. Po **zakończeniu kliknij** przycisk Utwórz.

    ![Konfigurowanie portalu 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Tworzenie Azure Synapse Analytics połączonej usługi

1. Powtórz ten sam proces, aby dodać Azure Synapse Analytics połączonej usługi. Na karcie Połączenia kliknij pozycję **Nowy.** Wybierz **kafelek Azure Synapse Analytics** i kliknij przycisk Kontynuuj.

    ![Konfigurowanie portalu 6](media/lab-data-flow-data-share/configure-6.png)
1. W okienku konfiguracji połączonej usługi wprowadź "SQLDW" jako nazwę połączonej usługi. Wprowadź swoje poświadczenia, aby zezwolić fabryce danych na łączenie się z bazą danych. Jeśli używasz uwierzytelniania SQL, wprowadź nazwę serwera, bazę danych, nazwę użytkownika i hasło. Aby sprawdzić, czy informacje o połączeniu są poprawne, **kliknij Test connection**. Po **zakończeniu kliknij** przycisk Utwórz.

    ![Konfigurowanie portalu 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Tworzenie Azure Data Lake Storage Gen2 połączonej usługi

1. Ostatnia połączona usługa potrzebna w tym laboratorium to Azure Data Lake Storage Gen2.  Na karcie Połączenia kliknij pozycję **Nowy.** Wybierz **kafelek Azure Data Lake Storage Gen2** kliknij przycisk Kontynuuj.

    ![Konfigurowanie portalu 8](media/lab-data-flow-data-share/configure8.png)
1. W okienku konfiguracji połączonej usługi wprowadź "ADLSGen2" jako nazwę połączonej usługi. Jeśli używasz uwierzytelniania za pomocą klucza konta, wybierz ADLS Gen2 magazynu z listy **rozwijanej Nazwa konta** magazynu. Aby sprawdzić poprawność informacji o połączeniu, kliknij **przycisk Test connection**. Po **zakończeniu kliknij** przycisk Utwórz.

    ![Konfigurowanie portalu 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Włączanie trybu debugowania przepływu danych

W sekcji *Przekształcanie danych przy użyciu przepływu* danych mapowania będziesz budować przepływy danych mapowania. Najlepszym rozwiązaniem przed tworzeniem przepływów danych mapowania jest włączenie trybu debugowania, który umożliwia testowanie logiki przekształcania w ciągu kilku sekund w aktywnym klastrze Spark.

Aby włączyć debugowanie, kliknij suwak **Debugowanie przepływu** danych na górnym pasku kanwy przepływu danych lub kanwy potoku, jeśli masz **działania przepływu** danych. Kliknij przycisk OK po wyskakującym oknie dialogowym potwierdzenia. Uruchomienie klastra potrwa około 5–7 minut. Kontynuuj *pozysucie danych z bazy Azure SQL DB* do ADLS Gen2 za pomocą działania kopiowania podczas inicjowania.

![Konfigurowanie portalu 10](media/lab-data-flow-data-share/configure10.png)

![Konfigurowanie portalu 11](media/lab-data-flow-data-share/configure11.png)

## <a name="ingest-data-using-the-copy-activity"></a>Pozysuj dane przy użyciu działania kopiowania

W tej sekcji utworzysz potok z działaniem kopiowania, które pozysuje jedną tabelę z bazy Azure SQL DB do ADLS Gen2 magazynu. Dowiesz się, jak dodać potok, skonfigurować zestaw danych i debugować potok za pomocą środowiska użytkownika usługi ADF. Wzorzec konfiguracji używany w tej sekcji można zastosować do kopiowania z magazynu danych relacyjnych do magazynu danych opartego na plikach.

W Azure Data Factory potok to logiczne grupowanie działań, które razem wykonują zadanie. Działanie definiuje operację do wykonania na danych. Zestaw danych wskazuje dane, które mają być wykorzystywane w połączonej usłudze.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Tworzenie potoku za pomocą działania kopiowania

1. W okienku zasobów fabryki kliknij ikonę plusa, aby otworzyć menu nowego zasobu. Wybierz pozycję **Potok.**

    ![Kopia portalu 1](media/lab-data-flow-data-share/copy1.png)
1. Na karcie **Ogólne** kanwy potoku nadaj potokowi nazwę opisową, taką jak "IngestAndTransformTaxiData".

    ![Kopia portalu 2](media/lab-data-flow-data-share/copy2.png)
1. W okienku działań kanwy potoku otwórz uchwyt **Przenieś** i przekształć i przeciągnij działanie **Kopiowanie** danych na kanwę. Nadaj działaniu kopiowania opisową nazwę, taką jak "IngestIntoADLS".

    ![Kopiowanie portalu 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurowanie Azure SQL źródłowego zestawu danych bazy danych

1. Kliknij **kartę Źródło** działania kopiowania. Aby utworzyć nowy zestaw danych, kliknij pozycję **Nowy.** Źródłem będzie tabela dbo. TripData" znajduje się w skonfigurowanej wcześniej połączonej usłudze "SQLDB".

    ![Kopia portalu 4](media/lab-data-flow-data-share/copy4.png)
1. Wyszukaj ciąg **Azure SQL Database** i kliknij przycisk Kontynuuj.

    ![Kopiowanie portalu 5](media/lab-data-flow-data-share/copy-5.png)
1. Wywołaj zestaw danych "TripData". Wybierz pozycję "SQLDB" jako usługę połączona. Wybierz nazwę tabeli 'dbo. TripData' z listy rozwijanej nazw tabel. Zaimportuj **schemat z połączenia/magazynu**. Po zakończeniu kliknij przycisk OK.

    ![Kopia portalu 6](media/lab-data-flow-data-share/copy6.png)

Źródłowy zestaw danych został pomyślnie utworzony. Upewnij się, że w ustawieniach źródła wartość domyślna **Tabela** jest zaznaczona w polu Użyj zapytania.

### <a name="configure-adls-gen2-sink-dataset"></a>Konfigurowanie ADLS Gen2 ujścia danych

1. Kliknij **kartę Ujścia** działania kopiowania. Aby utworzyć nowy zestaw danych, kliknij pozycję **Nowy.**

    ![Kopia portalu 7](media/lab-data-flow-data-share/copy7.png)
1. Wyszukaj ciąg **Azure Data Lake Storage Gen2** i kliknij przycisk Kontynuuj.

    ![Kopia portalu 8](media/lab-data-flow-data-share/copy8.png)
1. W okienku wybierania formatu wybierz pozycję **OgranicznikTekst** podczas zapisywania w pliku CSV. Kliknij przycisk Kontynuuj.

    ![Kopia portalu 9](media/lab-data-flow-data-share/copy9.png)
1. Nadaj zestawowi danych ujścia nazwę "TripDataCSV". Wybierz pozycję "ADLSGen2" jako usługę połączona. Wprowadź miejsce, w którym chcesz zapisać plik CSV. Możesz na przykład zapisać dane w pliku `trip-data.csv` w kontenerze `staging-container` . Ustaw **wartość Pierwszy wiersz jako nagłówek** na wartość true, ponieważ chcesz, aby dane wyjściowe zawierały nagłówki. Ponieważ plik nie istnieje jeszcze w miejscu docelowym, ustaw pozycję **Importuj schemat na** **wartość Brak.** Po zakończeniu kliknij przycisk OK.

    ![Kopia portalu 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testowanie działania kopiowania za pomocą uruchomienia debugowania potoku

1. Aby sprawdzić, czy działanie kopiowania działa prawidłowo, kliknij pozycję **Debuguj** w górnej części kanwy potoku, aby wykonać przebieg debugowania. Uruchomienie debugowania umożliwia testowanie potoku w trybie end-to-end lub do momentu punktu przerwania przed opublikowaniem go w usłudze fabryki danych.

    ![Kopia portalu 11](media/lab-data-flow-data-share/copy11.png)
1. Aby monitorować przebieg debugowania, przejdź do **karty Dane** wyjściowe kanwy potoku. Ekran monitorowania będzie automatycznie odświeżany co 20 sekund lub po ręcznym kliknięciu przycisku odświeżania. Działanie kopiowania ma specjalny widok monitorowania, do którego można uzyskać dostęp, klikając ikonę okularów w **kolumnie** Akcje.

    ![Kopia portalu 12](media/lab-data-flow-data-share/copy12.png)
1. Widok monitorowania kopiowania zawiera szczegóły wykonania działania i charakterystyki wydajności. Możesz wyświetlić informacje, takie jak odczyt/zapisu danych, odczytane/zapisane wiersze, odczytane/zapisane pliki i przepływność. Jeśli wszystko zostało skonfigurowane prawidłowo, powinno zostać wyświetlonych 49 999 wierszy zapisywanych w jednym pliku w ujściu usługi ADLS.

    ![Kopiowanie portalu 13](media/lab-data-flow-data-share/copy13.png)
1. Przed przejściem do następnej sekcji zalecamy opublikowanie zmian w usłudze fabryki danych, klikając pozycję **Opublikuj** wszystko na górnym pasku fabryki. Chociaż nie zostały one uwzględnione w tym laboratorium, Azure Data Factory pełną integrację z usługą Git. Integracja z usługą Git umożliwia kontrolę wersji, iteracyjne zapisywanie w repozytorium i współpracę w fabryce danych. Aby uzyskać więcej informacji, zobacz [kontrola źródła w Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    ![Publikowanie w portalu 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Przekształcanie danych przy użyciu przepływu danych mapowania

Teraz, po pomyślnym skopiowaniu danych do Azure Data Lake Storage, na teraz czas na przyłączenie i zagregowanie tych danych w magazynie danych. Użyjemy przepływu danych mapowania, Azure Data Factory projektowane wizualnie usługi transformacji. Przepływy danych mapowania umożliwiają użytkownikom tworzenie bez kodu logiki przekształcania i wykonywanie ich w klastrach Spark zarządzanych przez usługę ADF.

Przepływ danych utworzony w tym kroku łączy wewnętrzny zestaw danych "TripDataCSV" utworzony w poprzedniej sekcji z tabelą dbo. TripFares" przechowywany w bazie danych SQLDB na podstawie czterech kolumn klucza. Następnie dane są agregowane na podstawie kolumny w celu obliczenia średniej niektórych pól i zapisywane `payment_type` w Azure Synapse Analytics tabeli.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Dodawanie działania przepływu danych do potoku

1. W okienku działań na kanwie potoku otwórz uchwyt **Przenieś** i przekształć i przeciągnij działanie **Przepływ** danych na kanwę.

    ![Przepływ danych portalu 1](media/lab-data-flow-data-share/dataflow1.png)
1. W okienku bocznym, które zostanie otwarte, wybierz pozycję **Utwórz nowy przepływ danych, a** następnie wybierz pozycję Przepływ mapowania **danych.** Kliknij przycisk **OK**.

    ![Przepływ danych portalu 2](media/lab-data-flow-data-share/dataflow2.png)
1. Zostaniesz skierowany do kanwy przepływu danych, gdzie będziesz budować logikę przekształcania. Na karcie Ogólne nadaj przepływowi danych nazwę "JoinAndAggregateData".

    ![Przepływ danych portalu 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Konfigurowanie źródła csv danych podróży

1. Pierwszą rzeczą, którą chcesz zrobić, jest skonfigurowanie dwóch przekształceń źródła. Pierwsze źródło będzie wskazać zestaw danych DelimitedText "TripDataCSV". Aby dodać przekształcenie źródła, kliknij pole **Dodaj źródło** na kanwie.

    ![Przepływ danych portalu 4](media/lab-data-flow-data-share/dataflow4.png)
1. Nazwij źródło "TripDataCSV" i wybierz zestaw danych "TripDataCSV" z listy rozwijanej źródła. Jeśli pamiętasz, nie zaimportowano schematu początkowo podczas tworzenia tego zestawu danych, ponieważ nie było tam żadnych danych. Ponieważ `trip-data.csv` element istnieje teraz, kliknij pozycję **Edytuj,** aby przejść do karty ustawień zestawu danych.

    ![Przepływ danych portalu 5](media/lab-data-flow-data-share/dataflow5.png)
1. Przejdź do karty **Schemat i** kliknij pozycję **Importuj schemat.** Wybierz **pozycję Z połączenia/magazynu,** aby zaimportować bezpośrednio z magazynu plików. Powinno zostać wyświetlonych 14 kolumn typu ciąg.

    ![Przepływ danych portalu 6](media/lab-data-flow-data-share/dataflow6.png)
1. Wstecz do przepływu danych "JoinAndAggregateData". Jeśli klaster debugowania został uruchomiony (oznaczony zielonym okręgiem obok suwaka debugowania), możesz uzyskać migawkę danych na **karcie Podgląd** danych. Kliknij **przycisk Odśwież,** aby pobrać podgląd danych.

    ![Przepływ danych portalu 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Podgląd danych nie zapisuje danych.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurowanie źródła opłat za przejazd bazy danych SQL

1. Drugie źródło, które dodajesz, będzie wskaże tabelę bazy danych SQL "dbo". TripFares". W obszarze źródła "TripDataCSV" będzie dostępne kolejne **pole Dodaj źródło.** Kliknij go, aby dodać nowe przekształcenie źródła.

    ![Przepływ danych portalu 8](media/lab-data-flow-data-share/dataflow8.png)
1. Nadaj temu źródle nazwę "TripFaresSQL". Kliknij **pozycję Nowy** obok pola zestawu danych źródłowych, aby utworzyć nowy zestaw danych usługi SQL DB.

    ![Przepływ danych portalu 9](media/lab-data-flow-data-share/dataflow9.png)
1. Wybierz **kafelek Azure SQL Database** i kliknij przycisk Kontynuuj. *Uwaga: Możesz zauważyć, że wiele łączników w fabryce danych nie jest obsługiwanych w przepływie danych mapowania. Aby przekształcić dane z jednego z tych źródeł, pozysuj je do obsługiwanego źródła przy użyciu działania kopiowania*.

    ![Przepływ danych portalu 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Wywołaj zestaw danych "TripFares". Wybierz pozycję "SQLDB" jako usługę połączona. Wybierz nazwę tabeli 'dbo. TripFares' z listy rozwijanej nazw tabel. Zaimportuj **schemat z połączenia/magazynu**. Po zakończeniu kliknij przycisk OK.

    ![Przepływ danych portalu 11](media/lab-data-flow-data-share/dataflow11.png)
1. Aby zweryfikować dane, pobierz podgląd danych na **karcie Podgląd** danych.

    ![Przepływ danych portalu 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Sprzężenie wewnętrzne TripDataCSV i TripFaresSQL

1. Aby dodać nowe przekształcenie, kliknij ikonę plusa w prawym dolnym rogu okna "TripDataCSV". W **obszarze Wiele danych wejściowych/wyjściowych** wybierz pozycję **Połącz.**

    ![Dołącz do portalu 1](media/lab-data-flow-data-share/join1.png)
1. Nadaj przekształceniu sprzężenia nazwę "InnerJoinWithTripFares". Wybierz pozycję "TripFaresSQL" z listy rozwijanej strumienia po prawej stronie. Wybierz **typ sprzężenia Wewnętrzny.** Aby dowiedzieć się więcej o różnych typach sprzężenia w przepływie danych mapowania, zobacz [typy sprzężenia](./data-flow-join.md#join-types).

    Wybierz kolumny, które chcesz dopasować z każdego strumienia, za pomocą **listy rozwijanej Warunki** sprzężenia. Aby dodać dodatkowy warunek sprzężenia, kliknij ikonę plusa obok istniejącego warunku. Domyślnie wszystkie warunki sprzężenia są łączone z operatorem AND, co oznacza, że wszystkie warunki muszą być spełnione dla dopasowania. W tym laboratorium chcemy dopasować kolumny `medallion` , `hack_license` , `vendor_id` i `pickup_datetime`

    ![Dołączenie do portalu 2](media/lab-data-flow-data-share/join2.png)
1. Sprawdź, czy pomyślnie dołączeniu 25 kolumn wraz z podglądem danych.

    ![Dołączanie do portalu 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agregowanie według payment_type

1. Po zakończeniu przekształcania sprzężenia dodaj przekształcenie agregujące, klikając ikonę plusa obok pozycji "InnerJoinWithTripFares. Wybierz **pozycję Agreguj** **w obszarze Modyfikator schematu.**

    ![Portal agg 1](media/lab-data-flow-data-share/agg1.png)
1. Nazwij przekształcenie agregowane "AggregateByPaymentType". Wybierz `payment_type` jako grupę według kolumny.

    ![Portal agg 2](media/lab-data-flow-data-share/agg2.png)
1. Przejdź do **karty Agregacje.** W tym miejscu określisz dwie agregacje:
    * Średnia opłata pogrupowana według typu płatności
    * Łączna odległość podróży pogrupowana według typu płatności

    Najpierw utworzysz wyrażenie średniej opłaty. W polu tekstowym z **etykietą Dodaj lub wybierz kolumnę** wprowadź "average_fare".

    ![Portalgg 3](media/lab-data-flow-data-share/agg3.png)
1. Aby wprowadzić wyrażenie agregacji, kliknij niebieskie pole z etykietą **Enter expression (Wprowadź wyrażenie).** Spowoduje to otwarcie konstruktora wyrażeń przepływu danych, narzędzia używanego do wizualnego tworzenia wyrażeń przepływu danych przy użyciu schematu wejściowego, wbudowanych funkcji i operacji oraz parametrów zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji na temat możliwości konstruktora wyrażeń, zobacz dokumentację [konstruktora wyrażeń](./concepts-data-flow-expression-builder.md).

    Aby uzyskać średnią opłatę, użyj funkcji agregacji, aby agregować kolumnę rzutowania `avg()` na liczbę całkowitą za pomocą funkcji `total_amount` `toInteger()` . W języku wyrażeń przepływu danych jest to zdefiniowane jako `avg(toInteger(total_amount))` . Po **zakończeniu kliknij** przycisk Zapisz i zakończ.

    ![Portalgg 4](media/lab-data-flow-data-share/agg4.png)
1. Aby dodać dodatkowe wyrażenie agregacji, kliknij ikonę plusa obok `average_fare` . Wybierz **pozycję Dodaj kolumnę**.

    ![Portalgg 5](media/lab-data-flow-data-share/agg5.png)
1. W polu tekstowym z **etykietą Dodaj lub wybierz kolumnę** wprowadź "total_trip_distance". Tak jak w ostatnim kroku, otwórz konstruktor wyrażeń, aby wprowadzić wartość w wyrażeniu.

    Aby uzyskać łączną odległość podróży, użyj funkcji agregacji, aby agregować kolumnę rzutowania `sum()` na liczbę całkowitą z `trip_distance` `toInteger()` wartością . W języku wyrażeń przepływu danych jest to zdefiniowane jako `sum(toInteger(trip_distance))` . Po **zakończeniu kliknij** przycisk Zapisz i zakończ.

    ![Portalgg 6](media/lab-data-flow-data-share/agg6.png)
1. Przetestuj logikę przekształcania na **karcie Podgląd** danych. Jak widać, liczba wierszy i kolumn jest znacznie mniejsza niż wcześniej. Tylko trzy grupy według i kolumny agregacji zdefiniowane w tym przekształceniu są kontynuowane jako podrzędne. Ponieważ w przykładzie istnieje tylko pięć grup typów płatności, zwracanych jest tylko pięć wierszy.

    ![Portalgg 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurowanie Azure Synapse Analytics ujścia

1. Teraz, po zakończeniu logiki przekształcania, jesteśmy gotowi do ujścia danych w Azure Synapse Analytics tabeli. Dodaj przekształcenie ujścia w **sekcji Miejsce** docelowe.

    ![Ujścia 1 portalu](media/lab-data-flow-data-share/sink1.png)
1. Nadaj ujściu nazwę "SQLDWSink". Kliknij **pozycję Nowy** obok pola zestawu danych ujścia, aby utworzyć nowy Azure Synapse Analytics danych.

    ![Ujścia portalu 2](media/lab-data-flow-data-share/sink2.png)

1. Wybierz **kafelek Azure Synapse Analytics** i kliknij przycisk Kontynuuj.

    ![Ujścia portalu 3](media/lab-data-flow-data-share/sink-3.png)
1. Wywołaj zestaw danych "AggregatedTaxiData". Wybierz pozycję "SQLDW" jako usługę połączona. Wybierz **pozycję Utwórz nową tabelę** i nadaj nowej tabeli nazwę dbo. AggregateTaxiData. Po zakończeniu kliknij przycisk OK

    ![Ujścia portalu 4](media/lab-data-flow-data-share/sink4.png)
1. Przejdź do **karty Ustawienia** ujścia. Ponieważ tworzymy nową tabelę, musimy wybrać pozycję Utwórz ponownie tabelę **w** obszarze akcji tabeli. Usuń zaznaczenie **opcji Włącz przejściowe**, która przełącza, czy wstawiamy wiersz po wierszu, czy w partii.

    ![Ujścia portalu 5](media/lab-data-flow-data-share/sink5.png)

Przepływ danych został pomyślnie utworzony. Teraz czas na uruchomienie go w działaniu potoku.

### <a name="debug-your-pipeline-end-to-end"></a>Debugowanie potoku na wszystkich urządzeniach

1. Wstecz na kartę **potoku IngestAndTransformData.** Zwróć uwagę na zielone pole działania kopiowania "IngestIntoADLS". Przeciągnij ją do działania przepływu danych "JoinAndAggregateData". Spowoduje to utworzenie zdarzenia "w przypadku powodzenia", co spowoduje uruchomienie działania przepływu danych tylko w przypadku pomyślnego skopiowania.

    ![Potok 1 portalu](media/lab-data-flow-data-share/pipeline1.png)
1. Podobnie jak w przypadku działania kopiowania, kliknij pozycję **Debuguj,** aby wykonać przebieg debugowania. W przypadku przebiegów debugowania działanie przepływu danych będzie używać aktywnego klastra debugowania zamiast uruchamiać nowy klaster. Wykonanie tego potoku potrwa nieco ponad minutę.

    ![Potok portalu 2](media/lab-data-flow-data-share/pipeline2.png)
1. Podobnie jak działanie kopiowania, przepływ danych ma specjalny widok monitorowania dostępny za pomocą ikony okularów po zakończeniu działania.

    ![Potok portalu 3](media/lab-data-flow-data-share/pipeline3.png)
1. W widoku monitorowania można zobaczyć uproszczony wykres przepływu danych wraz z czasami wykonywania i wierszami na każdym etapie wykonywania. Jeśli ta czynność zostanie wykonana poprawnie, w tym działaniu powinno zostać zagregowanych 49 999 wierszy w pięciu wierszach.

    ![Potok portalu 4](media/lab-data-flow-data-share/pipeline4.png)
1. Możesz kliknąć przekształcenie, aby uzyskać dodatkowe szczegóły dotyczące jego wykonywania, takie jak informacje o partycjonowania i nowe/zaktualizowane/porzucone kolumny.

    ![Potok portalu 5](media/lab-data-flow-data-share/pipeline5.png)

Część tego laboratorium została ukończona w fabryce danych. Opublikuj zasoby, jeśli chcesz zoperacyjnie korzystać z wyzwalaczy. Pomyślnie uruchomiono potok, który pozysł dane z usługi Azure SQL Database do Azure Data Lake Storage przy użyciu działania kopiowania, a następnie agregował te dane do Azure Synapse Analytics. Możesz sprawdzić, czy dane zostały pomyślnie zapisane, spoglądać na SQL Server danych.

## <a name="share-data-using-azure-data-share"></a>Udostępnianie danych za pomocą usługi Azure Data Share

W tej sekcji dowiesz się, jak skonfigurować nowy udział danych przy użyciu Azure Portal. Obejmuje to utworzenie nowego udziału danych, który będzie zawierać zestawy danych z usługi Azure Data Lake Store Gen2 i Azure Synapse Analytics. Następnie skonfigurujesz harmonogram migawek, który zapewni użytkownikom danych opcję automatycznego odświeżania udostępnianych im danych. Następnie zaprosz adresatów do udziału danych. 

Po utworzeniu udziału danych przełączysz się do czadu i staniesz się *konsumentem danych.* Jako użytkownik danych będziesz przechodzić przez przepływ akceptowania zaproszenia do udziału danych, konfigurowania miejsca, w którym chcesz odbierać dane, i mapowania zestawów danych do różnych lokalizacji przechowywania. Następnie wyzwolisz migawkę, która skopiuje dane udostępnione Ci do określonego miejsca docelowego. 

### <a name="sharing-data-data-provider-flow"></a>Udostępnianie danych (przepływ Dostawca danych przepływu)

1. Otwórz Azure Portal w przeglądarce Microsoft Edge lub Google Chrome.

1. Korzystając z paska wyszukiwania w górnej części strony, wyszukaj **udziały danych**

    ![Reklamy w portalu](media/lab-data-flow-data-share/portal-ads.png)

1. Wybierz konto udziału danych z w nazwie "Dostawca". Na przykład **DataProvider0102.** 

1. Wybierz **pozycję Rozpocznij udostępnianie danych**

    ![Rozpoczynanie udostępniania](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Wybierz **pozycję +Utwórz,** aby rozpocząć konfigurowanie nowego udziału danych. 

1. W **obszarze Nazwa** udziału określ nazwę. Jest to nazwa udziału, która będzie widziana przez użytkownika danych, dlatego należy nadać mu opisową nazwę, taką jak TaxiData.

1. W **obszarze** Opis umieść w zdaniu, które opisuje zawartość udziału danych. Udział danych będzie zawierać dane dotyczące przejazdów taksówkami na całym świecie, które są przechowywane w wielu sklepach, w tym w Azure Synapse Analytics i Azure Data Lake Store. 

1. W **Warunki użytkowania** określ zestaw warunków, do których ma stosować się użytkownik danych. Niektóre przykłady obejmują "Nie dystrybuuj tych danych poza twoją organizację" lub "Zapoznaj się z umową prawna". 

    ![Udostępnianie szczegółów](media/lab-data-flow-data-share/ads-details.png)

1. Wybierz opcję **Kontynuuj**. 

1. Wybieranie **opcji Dodaj zestawy danych** 

    ![Dodawanie zestawu danych 1](media/lab-data-flow-data-share/add-dataset.png)

1. Wybierz **Azure Synapse Analytics,** aby wybrać tabelę z Azure Synapse Analytics, do których trafiły przekształcenia ADF.

    ![Dodawanie zestawu danych sql](media/lab-data-flow-data-share/add-dataset-sql.png)


1. Przed kontynuowaniem otrzymasz skrypt do uruchomienia. Podany skrypt tworzy użytkownika w bazie danych SQL, aby umożliwić uwierzytelnianie Azure Data Share MSI w jego imieniu. 

> [!IMPORTANT]
> Przed uruchomieniem skryptu należy ustawić siebie jako administratora usługi Active Directory dla SQL Server. 

1. Otwórz nową kartę i przejdź do Azure Portal. Skopiuj podany skrypt, aby utworzyć użytkownika w bazie danych, z której chcesz udostępnić dane. W tym celu zaloguj się do bazy danych EDW przy użyciu Eksploratora zapytań (wersja zapoznawcza) przy użyciu uwierzytelniania usługi AAD. 

    Należy zmodyfikować skrypt tak, aby utworzony użytkownik znajdował się w nawiasach kwadratowych. Np:
    
    tworzenie użytkownika [dataprovider-xxxx] z zewnętrznego logowania;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Wróć do Azure Data Share, w którym dodajemy zestawy danych do udziału danych. 

1. Wybierz **pozycję EDW,** a następnie **pozycję AggregatedTaxiData** dla tabeli. 

1. Wybieranie opcji **Dodaj zestaw danych**

    Mamy teraz tabelę SQL, która jest częścią naszego zestawu danych. Następnie dodamy dodatkowe zestawy danych z usługi Azure Data Lake Store. 

1. Wybierz **pozycję Dodaj zestaw danych** i wybierz pozycję Azure Data Lake Store **Gen2**

    ![Dodawanie adls zestawu danych](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Wybierz pozycję **Dalej**

1. Rozwiń *wwtaxidata*. Rozwiń *w ten sposób dane dotyczące taksówek w Bostonie.* Zwróć uwagę, że możesz udostępniać do poziomu pliku. 

1. Wybierz folder *Boston Taxi Data* (Dane taksówek w Bostonie), aby dodać cały folder do udziału danych. 

1. Wybieranie **opcji Dodaj zestawy danych**

1. Przejrzyj dodane zestawy danych. Musisz mieć tabelę SQL i folder ADLS Gen2 dodany do udziału danych. 

1. Wybierz przycisk **Kontynuuj**

1. Na tym ekranie możesz dodać adresatów do udziału danych. Adresaci, których dodasz, otrzymają zaproszenia do Twojego udziału danych. Na potrzeby tego laboratorium należy dodać 2 adresy e-mail:

    1. Adres e-mail subskrypcji platformy Azure, w ramach których jesteś. 

        ![Dodawanie adresatów](media/lab-data-flow-data-share/add-recipients.png)

    1. Dodaj fikcyjnego konsumenta danych o nazwie *janedoe@fabrikam.com* .

1. Na tym ekranie można skonfigurować ustawienie migawki dla użytkownika danych. Dzięki temu będą otrzymywać regularne aktualizacje danych w odstępach czasu zdefiniowanych przez Ciebie. 

1. Sprawdź **harmonogram migawek** i skonfiguruj godzinowe odświeżanie danych przy użyciu *listy rozwijanej* Cykl.  

1. Wybierz przycisk **Utwórz**.

    Masz teraz aktywny udział danych. Sprawdźmy, co można zobaczyć jako dostawca danych podczas tworzenia udziału danych. 

1. Wybierz utworzony udział danych o tytule **DataProvider**. Możesz przejść do niego, wybierając pozycję **Wysłane udziały** **w Data Share**. 

1. Kliknij pozycję Harmonogram migawek. Jeśli wybierzesz tę opcję, możesz wyłączyć harmonogram migawek. 

1. Następnie wybierz **kartę Zestawy** danych. Po utworzeniu udziału danych możesz dodać do niego dodatkowe zestawy danych. 

1. Wybierz **kartę Udostępnianie** subskrypcji. Nie istnieją jeszcze żadne subskrypcje udziałów, ponieważ konsument danych nie zaakceptował jeszcze zaproszenia.

1. Przejdź do **karty Zaproszenia.** W tym miejscu zostanie wyświetlona lista oczekujących zaproszeń. 

    ![Oczekujące zaproszenia](media/lab-data-flow-data-share/pending-invites.png)

1. Wybierz zaproszenie do *janedoe@fabrikam.com* . Wybierz pozycję Usuń. Jeśli adresat nie zaakceptował jeszcze zaproszenia, nie będzie mógł tego zrobić. 

1. Wybierz **kartę** Historia. Na razie nic nie jest wyświetlane, ponieważ konsument danych nie zaakceptował jeszcze zaproszenia i wyzwolił migawkę. 

### <a name="receiving-data-data-consumer-flow"></a>Odbieranie danych (przepływ konsumenta danych)

Teraz, gdy przejrzeliśmy nasz udział danych, jesteśmy gotowi do przełączenia kontekstu i nałożymy na użytkownika danych hat. 

Teraz w skrzynce odbiorczej Azure Data Share w skrzynce odbiorczej powinno być Microsoft Azure. Uruchom program Outlook Dostęp w sieci Web (outlook.com) i zaloguj się przy użyciu poświadczeń podanych dla subskrypcji platformy Azure.

W wiadomości e-mail, która powinna zostać odebrana, kliknij pozycję "Wyświetl zaproszenie >". W tym momencie będziesz symulować środowisko użytkownika danych podczas akceptowania zaproszenia dostawców danych do ich udziału danych. 

![Wiadomość e-mail z zaproszeniem](media/lab-data-flow-data-share/email-invite.png)

Może zostać wyświetlony monit o wybranie subskrypcji. Upewnij się, że wybierasz subskrypcję, w ramach których pracujesz w tym laboratorium. 

1. Kliknij zaproszenie o tytule *DataProvider*. 

1. Na tym ekranie Zaproszenie można zauważyć różne szczegóły dotyczące udziału danych, który został wcześniej skonfigurowany jako dostawca danych. Przejrzyj szczegóły i zaakceptuj warunki użytkowania, jeśli je podano.

1. Wybierz subskrypcję i grupę zasobów, które już istnieją dla Twojego laboratorium. 

1. W **przypadku konta udziału danych** wybierz pozycję **DaneKonsumer.** Możesz również utworzyć nowe konto udziału danych. 

1. Obok **pola Odebrano nazwę** udziału zauważysz, że domyślna nazwa udziału to nazwa określona przez dostawcę danych. Nadaj udziałowi przyjazną nazwę opisującą dane, które mają być odbierane, np. **TaxiDataShare**.

    ![Zaproszenia akceptują](media/lab-data-flow-data-share/consumer-accept.png)

1. Możesz wybrać opcję Akceptuj **i skonfiguruj teraz lub** Zaakceptuj i skonfiguruj **później.** Jeśli zdecydujesz się zaakceptować i skonfigurować teraz, określ konto magazynu, do którego mają zostać skopiowane wszystkie dane. Jeśli zdecydujesz się zaakceptować i skonfigurować później, zestawy danych w udziału zostaną niezamapowane i konieczne będzie ich ręczne mapowanie. Wybierzemy to później. 

1. Wybierz **pozycję Zaakceptuj i skonfiguruj później.** 

    Podczas konfigurowania tej opcji jest tworzona subskrypcja udziału, ale nie ma miejsca, w których dane mają trafiać, ponieważ żadne miejsce docelowe nie zostało zamapowane. 

    Następnie skonfigurujemy mapowania zestawu danych dla udziału danych. 

1. Wybierz odebrany udział (nazwę określoną w kroku 5).

    **Migawka wyzwalacza** jest wyszarana, ale udział jest aktywny. 

1. Wybierz **kartę Zestawy** danych. Zwróć uwagę, że każdy zestaw danych jest niezamapowany, co oznacza, że nie ma miejsca docelowego do skopiowania danych. 

    ![niezamapowane zestawy danych](media/lab-data-flow-data-share/unmapped.png)

1. Wybierz tabelę Azure Synapse Analytics, a następnie wybierz **pozycję + Mapuj na Wartość docelową.**

1. Po prawej stronie ekranu wybierz menu rozwijane **Typ** danych docelowych. 

    Dane SQL można mapować na szeroką gamę magazynów danych. W tym przypadku będziemy mapować do Azure SQL Database.

    ![mapowanie](media/lab-data-flow-data-share/mapping-options.png)
    
    (Opcjonalnie) Wybierz **pozycję Azure Data Lake Store Gen2** jako docelowy typ danych. 
    
    (Opcjonalnie) Wybierz subskrypcję, grupę zasobów i konto magazynu, z których pracujesz. 
    
    (Opcjonalnie) Możesz zdecydować się na odbieranie danych do data lake w formacie CSV lub Parquet. 

1. Obok opcji **Docelowy typ danych** wybierz pozycję Azure SQL Database. 

1. Wybierz subskrypcję, grupę zasobów i konto magazynu, z których pracujesz. 

    ![mapowanie na sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Przed kontynuowaniem należy utworzyć nowego użytkownika w skrypcie SQL Server uruchamiając podany skrypt. Najpierw skopiuj skrypt podany do schowka. 

1. Otwórz nową Azure Portal kartę. Nie zamykaj istniejącej karty, ponieważ za chwilę trzeba do niego wrócić. 

1. Na otwartej nowej karcie przejdź do bazy **danych SQL.**

1. Wybierz bazę danych SQL (w Twojej subskrypcji powinna być tylko jedna baza danych). Należy uważać, aby nie wybierać magazynu danych. 

1. Wybieranie **edytora zapytań (wersja zapoznawcza)**

1. Użyj uwierzytelniania usługi AAD, aby zalogować się do edytora zapytań. 

1. Uruchom zapytanie podane w twoim udziału danych (skopiowane do schowka w kroku 14). 

    To polecenie umożliwia usłudze Azure Data Share korzystanie z tożsamości zarządzanych dla usług platformy Azure w celu uwierzytelniania na SQL Server, aby móc kopiować do niej dane. 

1. Wstecz do oryginalnej karty, a następnie wybierz pozycję **Mapuj, aby wybrać element docelowy**.

1. Następnie wybierz folder usługi Azure Data Lake Gen2, który jest częścią zestawu danych, i zamapuj go na Azure Blob Storage danych. 

    ![magazyn](media/lab-data-flow-data-share/storage-map.png)

    Wszystkie zamapowane zestawy danych są teraz gotowe do rozpoczęcia odbierania danych od dostawcy danych. 

    ![Mapowane](media/lab-data-flow-data-share/all-mapped.png)
    
1. Wybierz **pozycję Szczegóły.** 

    Zauważ, **że migawka wyzwalacza** nie jest już wyszarana, ponieważ udział danych ma teraz miejsca docelowe do skopiowania.

1. Wybierz pozycję Migawka wyzwalacza > pełną kopię. 

    ![Wyzwalacz](media/lab-data-flow-data-share/trigger-full.png)

    Spowoduje to rozpoczęcie kopiowania danych na nowe konto udziału danych. W rzeczywistym scenariuszu te dane będą pochodzić od innej firmy. 

    Po około 3–5 minutach dane zostaną na nie natknąć. Możesz monitorować postęp, klikając **kartę** Historia. 

    Podczas oczekiwania przejdź do oryginalnego udziału danych (DataProvider) i wyświetl stan karty **Udostępnianie subskrypcji** **i** historii. Zwróć uwagę, że obecnie istnieje aktywna subskrypcja, a jako dostawca danych możesz również monitorować, kiedy konsument danych zaczął odbierać dane, które zostały im udostępnione. 

1. Przejdź z powrotem do udziału danych użytkownika danych. Po pomyślnym zakończeniu stanu wyzwalacza przejdź do docelowej bazy danych SQL i data lake, aby zobaczyć, że dane zostały trafione do odpowiednich magazynów. 

Gratulacje, laboratorium zakończyło się!