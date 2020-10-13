---
title: Ładowanie terabajtów danych do usługi Azure Synapse Analytics
description: Pokazuje, jak 1 TB danych można załadować do usługi Azure Synapse Analytics w ciągu 15 minut z Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a5bf53597c0706a5ef435d6ab8cc06e14726db8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442483"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>Załaduj 1 TB do usługi Azure Synapse Analytics poniżej 15 minut z Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Kopiowanie danych do lub z usługi Azure Synapse Analytics (dawniej SQL Data Warehouse) przy użyciu Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) to oparta na chmurze baza danych skalowalna w poziomie, która umożliwia przetwarzanie ogromnych ilości danych, zarówno relacyjnych, jak i nierelacyjnych.  Usługa Azure Synapse Analytics oparta na architekturze wysoce Parallel Processing (MPP) jest zoptymalizowana pod kątem obciążeń związanych z magazynem danych w przedsiębiorstwie.  Oferuje elastyczność chmury i umożliwia niezależne skalowanie magazynu i obliczanie.

Wprowadzenie do usługi Azure Synapse Analytics jest teraz łatwiejsze niż kiedykolwiek wcześniej przy użyciu **Azure Data Factory**.  Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze, która może służyć do wypełniania analiz usługi Azure Synapse przy użyciu danych z istniejącego systemu i oszczędności czasu podczas oceniania usługi Azure Synapse Analytics i tworzenia rozwiązań analitycznych. Oto najważniejsze zalety ładowania danych do usługi Azure Synapse Analytics przy użyciu Azure Data Factory:

* **Łatwa konfiguracja**: Intuicyjny kreator 5-etapowy bez konieczności wykonywania skryptów.
* **Obsługa rozbudowanych magazynów danych**: Wbudowana obsługa bogatego zestawu lokalnych i opartych na chmurze magazynów danych.
* **Bezpieczeństwo i zgodność**: dane są przesyłane za pośrednictwem protokołu HTTPS lub ExpressRoute, a Globalna obecność usługi gwarantuje, że dane nigdy nie opuszczą granicy geograficznej
* **Niezrównana wydajność przy użyciu** bazy danych bazowych — korzystając z funkcji wielobase — jest to najbardziej wydajny sposób, aby przenieść dane do usługi Azure Synapse Analytics. Za pomocą funkcji tymczasowego obiektu BLOB można osiągnąć duże szybkości ładowania z wszystkich typów magazynów danych niż usługa Azure Blob Storage, które są obsługiwane domyślnie przez bazę.

W tym artykule pokazano, jak za pomocą Kreatora kopiowania Data Factory załadować dane o pojemności 1 TB z usługi Azure Blob Storage do usługi Azure Synapse Analytics w ciągu 15 minut, o ponad 1,2 GB/s.

Ten artykuł zawiera instrukcje krok po kroku dotyczące przemieszczania danych do usługi Azure Synapse Analytics przy użyciu Kreatora kopiowania.

> [!NOTE]
>  Aby uzyskać ogólne informacje o możliwościach Data Factory przenoszenia danych do/z usługi Azure Synapse Analytics, zobacz [przenoszenie danych do i z usługi Azure Synapse Analytics przy użyciu Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artykułu.
>
> Można również tworzyć potoki przy użyciu programu Visual Studio, programu PowerShell itd. Zobacz [Samouczek: kopiowanie danych z usługi Azure BLOB do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) , aby uzyskać krótki przewodnik z instrukcjami krok po kroku dotyczącymi używania działania kopiowania w programie Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Wymagania wstępne
* Azure Blob Storage: ten eksperyment używa platformy Azure Blob Storage (GRS) do przechowywania zestawu danych testów TPC-H.  Jeśli nie masz konta usługi Azure Storage, Dowiedz się, [jak utworzyć konto magazynu](../../storage/common/storage-account-create.md).
* Dane [TPC-h](http://www.tpc.org/tpch/) : będziemy używać TPC-h jako zestawu danych testowych.  Aby to zrobić, należy użyć narzędzia `dbgen` TPC-H Toolkit, które pomaga wygenerować zestaw danych.  Możesz pobrać kod źródłowy `dbgen` z [narzędzi TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp) i skompilować go samodzielnie lub pobrać skompilowany plik binarny z usługi [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Uruchom dbgen.exe z poniższymi poleceniami, aby wygenerować plik prosty o pojemności 1 TB na potrzeby `lineitem` rozmieszczenia tabel w 10 plikach:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Teraz Skopiuj wygenerowane pliki do obiektu blob platformy Azure.  Zapoznaj się z tematem [przenoszenie danych do i z lokalnego systemu plików przy użyciu Azure Data Factory](data-factory-onprem-file-system-connector.md) , jak to zrobić za pomocą kopii ADF.    
* Azure Synapse Analytics: ten eksperyment ładuje dane do usługi Azure Synapse Analytics utworzonej przy użyciu 6 000 jednostek dwu

    Zapoznaj się z tematem [Tworzenie usługi Azure Synapse Analytics](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) , aby uzyskać szczegółowe instrukcje dotyczące tworzenia bazy danych analiz usługi Azure Synapse.  Aby uzyskać najlepszą możliwą wydajność ładowania do usługi Azure Synapse Analytics przy użyciu platformy Base, wybieramy maksymalną liczbę jednostek magazynu danych (jednostek dwu) dozwoloną w ustawieniu wydajności, co to jest 6 000 jednostek dwu.

  > [!NOTE]
  > Podczas ładowania z obiektu blob platformy Azure wydajność ładowania danych jest bezpośrednio proporcjonalna do liczby jednostek dwu skonfigurowanych dla usługi Azure Synapse Analytics:
  >
  > Ładowanie 1 TB do 1 000 jednostek dwu Azure Synapse Analytics trwa 87 minut (~ 200 przepustowość MB/s) ładowanie 1 TB do programu 2 000 jednostek dwu Azure Synapse analiza trwa przez minut (~ 46 MB/s) ładowanie 1 TB do 380 jednostek dwu Azure Synapse Analytics trwa 14 minut (~ 6 000 przepływności GB/s)
  >
  >

    Aby utworzyć Synapse pulę SQL z 6 000 jednostek dwu, przesuń suwak wydajności do prawej:

    ![Suwak wydajności](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Dla istniejącej bazy danych, która nie jest skonfigurowana przy użyciu 6 000 jednostek dwu, można skalować ją przy użyciu Azure Portal.  Przejdź do bazy danych w Azure Portal, a w panelu **Przegląd** widoczny jest przycisk **Skala** na poniższej ilustracji:

    ![Przycisk skalowania](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kliknij przycisk **skalowanie** , aby otworzyć Poniższy panel, przesuń suwak do wartości maksymalnej, a następnie kliknij przycisk **Zapisz** .

    ![Okno dialogowe skalowania](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Ten eksperyment ładuje dane do usługi Azure Synapse Analytics przy użyciu `xlargerc` klasy zasobów.

    Aby osiągnąć najlepszą możliwą przepustowość, należy wykonać kopię przy użyciu użytkownika usługi Azure Synapse Analytics należącego do `xlargerc` klasy zasobów.  Dowiedz się, jak to zrobić, wykonując następujące czynności: [Zmień przykład klasy zasobów użytkownika](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Utwórz schemat tabeli docelowej w bazie danych analizy usługi Azure Synapse, uruchamiając następującą instrukcję języka DDL:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Po ukończeniu kroków wymagań wstępnych teraz wszystko jest gotowe do skonfigurowania działania kopiowania przy użyciu Kreatora kopiowania.

## <a name="launch-copy-wizard"></a>Uruchamianie Kreatora kopiowania
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób** w lewym górnym rogu, kliknij pozycję **Analiza i analiza**, a następnie kliknij pozycję **Data Factory**.
3. W okienku **Nowa fabryka danych** :

   1. Wprowadź **LoadIntoSQLDWDataFactory** dla **nazwy**.
       Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli wystąpi błąd: **Nazwa fabryki danych "LoadIntoSQLDWDataFactory" jest niedostępna**, Zmień nazwę fabryki danych (np. yournameLoadIntoSQLDWDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.  
   2. Wybierz swoją **subskrypcję**platformy Azure.
   3. Wykonaj jedną z następujących czynności dotyczącą grupy zasobów:
      1. Wybierz pozycję **Użyj istniejącej**, aby wybrać istniejącą grupę zasobów.
      2. Wybierz pozycję **Utwórz nowy**, aby wprowadzić nazwę grupy zasobów.
   4. Wybierz **lokalizację** fabryki danych.
   5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.  
   6. Kliknij przycisk **Utwórz**.
4. Po zakończeniu tworzenia zostanie wyświetlony blok **Data Factory** , jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na stronie głównej usługi Fabryka danych kliknij kafelek **Kopiowanie danych**, aby uruchomić **Kreatora kopiowania**.

   > [!NOTE]
   > Jeśli widać, że przeglądarka sieci Web jest zablokowana podczas wyświetlania komunikatu „Autoryzowanie...”, wyłącz ustawienie **Blokuj pliki cookie i dane witryn innych firm** lub pozostaw je włączone i utwórz wyjątek dla witryny **login.microsoftonline.com**, a następnie spróbuj ponownie uruchomić kreatora.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Krok 1. Konfigurowanie harmonogramu ładowania danych
Pierwszym krokiem jest skonfigurowanie harmonogramu ładowania danych.  

Na stronie **Właściwości**:

1. Wprowadź **CopyFromBlobToAzureSqlDataWarehouse** dla **nazwy zadania**
2. Wybierz opcję **Uruchom raz teraz** .   
3. Kliknij przycisk **Dalej**.  

    ![Kreator kopiowania — Strona właściwości](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Krok 2. Konfigurowanie źródła
W tej sekcji przedstawiono kroki konfigurowania źródła: obiekt blob platformy Azure zawierający pliki elementów wiersza 1-TB TPC-H.

1. Wybierz **BLOB Storage platformy Azure** jako magazyn danych, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — Wybieranie strony źródłowej](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Wprowadź informacje o połączeniu dla konta usługi Azure Blob Storage, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — informacje o połączeniu ze źródłem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Wybierz **folder** zawierający pliki elementu wiersza TPC-H i kliknij przycisk **dalej**.

    ![Kreator kopiowania — Wybieranie folderu wejściowego](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Po kliknięciu **przycisku Dalej**ustawienia formatu pliku są wykrywane automatycznie.  Upewnij się, że ogranicznik kolumny to "|" zamiast domyślnego przecinka ",".  Po przejrzeniu danych kliknij przycisk **dalej** .

    ![Kreator kopiowania — ustawienia formatu pliku](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Krok 3. Konfigurowanie miejsca docelowego
W tej sekcji przedstawiono sposób konfigurowania lokalizacji docelowej: `lineitem` tabeli w bazie danych usługi Azure Synapse Analytics.

1. Wybierz pozycję **Azure Synapse Analytics** jako magazyn docelowy, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — Wybieranie docelowego magazynu danych](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Wprowadź informacje o połączeniu dla usługi Azure Synapse Analytics.  Upewnij się, że określono użytkownika, który jest członkiem roli `xlargerc` (zobacz sekcję **wymagania wstępne** , aby uzyskać szczegółowe instrukcje), a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — informacje o połączeniu docelowym](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Wybierz tabelę docelową, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — strona mapowania tabeli](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na stronie mapowanie schematu Pozostaw zaznaczenie pola wyboru Zastosuj Mapowanie kolumn, a następnie kliknij przycisk **dalej**.

## <a name="step-4-performance-settings"></a>Krok 4. ustawienia wydajności

Opcja **Zezwalaj na bazę wielobazową** jest domyślnie zaznaczona.  Kliknij przycisk **Dalej**.

![Kreator kopiowania — Strona mapowanie schematu](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Krok 5. wdrażanie i monitorowanie wyników ładowania
1. Kliknij przycisk **Zakończ** , aby wdrożyć.

    ![Kreator kopiowania — strona podsumowania 1](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Po zakończeniu wdrażania kliknij, `Click here to monitor copy pipeline` Aby monitorować postęp przebiegu kopiowania. Wybierz potok kopiowania utworzony na liście **okna działania** .

    ![Kreator kopiowania — strona podsumowania 2](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Możesz wyświetlić szczegóły przebiegu kopiowania w **Eksploratorze okien działania** w prawym panelu, włącznie z woluminem danych odczytywanym ze źródła i zapisanym w miejscu docelowym, czasie trwania oraz średnią przepływność dla przebiegu.

    Jak widać na poniższym zrzucie ekranu, kopiowanie 1 TB z platformy Azure Blob Storage do usługi Azure Synapse Analytics zajęło 14 minut, co skutecznie osiąga 1,22 GB/s!

    ![Kreator kopiowania — okno dialogowe powiodło się](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Najlepsze rozwiązania
Oto kilka najlepszych rozwiązań dotyczących uruchamiania bazy danych usługi Azure Synapse Analytics:

* Użycie większej klasy zasobów podczas ładowania do KLASTROWAnego indeksu magazynu kolumn.
* Aby zwiększyć efektywność sprzężeń, należy rozważyć użycie dystrybucji skrótów przez wybór kolumny zamiast domyślnego rozkładu okrężnego.
* Aby zwiększyć szybkość ładowania, należy rozważyć użycie sterty dla danych przejściowych.
* Utwórz statystyki po zakończeniu ładowania do usługi Azure Synapse Analytics.

Aby uzyskać szczegółowe informacje, zobacz [najlepsze rozwiązania dotyczące usługi Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) .

## <a name="next-steps"></a>Następne kroki
* [Kreator kopiowania Data Factory](data-factory-copy-wizard.md) — ten artykuł zawiera szczegółowe informacje na temat Kreatora kopiowania.
* [Przewodnik dotyczący wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md) — ten artykuł zawiera referencyjne pomiary wydajności i przewodnik dostrajania.
