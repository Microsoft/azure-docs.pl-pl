---
title: Ładowanie terabajtów danych do magazynu danych SQL
description: Pokazuje, jak 1 TB danych można załadować do usługi Azure SQL Data Warehouse w ciągu 15 minut dzięki usłudze Azure Data Factory
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
ms.openlocfilehash: 0bef6b5e87e7f0964989db371014c305b97f1d12
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419310"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Ładowanie 1 TB do usługi Azure SQL Data Warehouse w ciągu 15 minut za pomocą fabryki danych
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Kopiowanie danych do lub z usługi Azure SQL Data Warehouse przy użyciu usługi Data Factory](../connector-azure-sql-data-warehouse.md).


[Usługa Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) to oparta na chmurze, skalowawska baza danych umożliwiająca przetwarzanie ogromnych ilości danych, zarówno relacyjnych, jak i nierelacyjnych.  Zbudowany na architekturze przetwarzania równoległego (MPP), usługa SQL Data Warehouse jest zoptymalizowana pod kątem obciążeń magazynu danych przedsiębiorstwa.  Oferuje elastyczność chmury z elastycznością skalowania pamięci masowej i obliczania niezależnie.

Wprowadzenie do usługi Azure SQL Data Warehouse jest teraz łatwiejsze niż kiedykolwiek przy użyciu **usługi Azure Data Factory.**  Usługa Azure Data Factory to w pełni zarządzana usługa integracji danych w chmurze, która może służyć do wypełniania magazynu danych SQL danymi z istniejącego systemu i oszczędzając cenny czas podczas oceny usługi SQL Data Warehouse i tworzenia rozwiązań analitycznych. Oto najważniejsze zalety ładowania danych do usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory:

* **Łatwy w konfiguracji:** 5-stopniowy intuicyjny kreator bez konieczności tworzenia skryptów.
* **Obsługa magazynu danych:** wbudowana obsługa bogatego zestawu lokalnych i chmurowych magazynów danych.
* **Bezpieczne i zgodne:** dane są przesyłane za pośrednictwem protokołu HTTPS lub usługi ExpressRoute, a globalna obecność usługi gwarantuje, że dane nigdy nie opuszczają granicy geograficznej
* **Niezrównana wydajność przy użyciu PolyBase** — korzystanie z Polybase jest najbardziej efektywnym sposobem przenoszenia danych do usługi Azure SQL Data Warehouse. Za pomocą funkcji obiektu blob przemieszczania, można osiągnąć wysokie prędkości obciążenia ze wszystkich typów magazynów danych oprócz magazynu obiektów Blob platformy Azure, który Polybase obsługuje domyślnie.

W tym artykule pokazano, jak za pomocą Kreatora kopiowania fabryki danych do ładowania danych o pojemności 1 TB z usługi Azure Blob Storage do usługi Azure SQL Data Warehouse w mniej niż 15 minut, przy przepływności ponad 1,2 GBps.

Ten artykuł zawiera instrukcje krok po kroku dotyczące przenoszenia danych do usługi Azure SQL Data Warehouse przy użyciu Kreatora kopiowania.

> [!NOTE]
>  Aby uzyskać ogólne informacje na temat możliwości fabryki danych w przenoszeniu danych do/z usługi Azure SQL Data Warehouse, zobacz [Przenoszenie danych do i z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artykułu.
>
> Można również tworzyć potoki przy użyciu programu Visual Studio, programu PowerShell itp. Zobacz [samouczek: Kopiowanie danych z obiektu blob platformy Azure do usługi Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) w celu szybkiego instruktażu z instrukcjami krok po kroku dotyczącymi korzystania z działania kopiowania w fabryce danych platformy Azure.  
>
>

## <a name="prerequisites"></a>Wymagania wstępne
* Usługa Azure Blob Storage: ten eksperyment używa usługi Azure Blob Storage (GRS) do przechowywania zestawu danych testowych TPC-H.  Jeśli nie masz konta magazynu platformy Azure, dowiedz się, [jak utworzyć konto magazynu](../../storage/common/storage-account-create.md).
* [Dane TPC-H:](http://www.tpc.org/tpch/) będziemy używać TPC-H jako zestawu danych testowych.  Aby to zrobić, należy `dbgen` użyć z zestawu narzędzi TPC-H, który pomaga wygenerować zestaw danych.  Możesz pobrać kod źródłowy `dbgen` z [TPC Tools](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) i skompilować go samodzielnie, albo pobrać skompilowany plik binarny z [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Uruchom program dbgen.exe za pomocą następujących poleceń, `lineitem` aby wygenerować płaski plik o pojemności 1 TB dla tabeli rozłożonej na 10 plików:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Teraz skopiuj wygenerowane pliki do obiektu Blob platformy Azure.  Zapoznaj się [z przenieść dane do i z lokalnego systemu plików przy użyciu usługi Azure Data Factory,](data-factory-onprem-file-system-connector.md) aby dowiedzieć się, jak to zrobić przy użyciu usługi ADF Copy.    
* Usługa Azure SQL Data Warehouse: ten eksperyment ładuje dane do usługi Azure SQL Data Warehouse utworzonej za pomocą 6000 jednostek DWU

    Szczegółowe instrukcje dotyczące tworzenia bazy danych usługi SQL Data Warehouse można znaleźć w witrynie [Tworzenie magazynu danych SQL.](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)  Aby uzyskać najlepszą możliwą wydajność ładowania do usługi SQL Data Warehouse przy użyciu polybase, wybieramy maksymalną liczbę jednostek magazynu danych (DWU) dozwoloną w ustawieniu Wydajność, które wynosi 6000 jednostek DWU.

  > [!NOTE]
  > Podczas ładowania z usługi Azure Blob wydajność ładowania danych jest wprost proporcjonalna do liczby jednostek DWU skonfigurowanych w magazynie danych SQL:
  >
  > Ładowanie 1 TB do 1000 DWU SQL Data Warehouse trwa 87 minut (~ 200 MB/s przepływności) Ładowanie 1 TB do 2000 DWU SQL Data Warehouse trwa 46 minut (~ 380 MB/s przepływność) Ładowanie 1 TB do 6000 DWU SQL Data Warehouse trwa 14 minut (~1.2 GBps throughput)
  >
  >

    Aby utworzyć magazyn danych SQL z 6000 jednostek DWU, przesuń suwak Wydajność w prawo:

    ![Suwak wydajności](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    W przypadku istniejącej bazy danych, która nie jest skonfigurowana z 6000 jednostek DWU, można ją skalować w górę za pomocą witryny Azure portal.  Przejdź do bazy danych w witrynie Azure portal, a w panelu **Przegląd** pokazano na poniższej ilustracji przycisk **Scale:**

    ![Przycisk Skaluj](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kliknij przycisk **Skaluj,** aby otworzyć następujący panel, przesuń suwak do wartości maksymalnej i kliknij przycisk **Zapisz.**

    ![Okno dialogowe Skalowanie](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Ten eksperyment ładuje dane do `xlargerc` usługi Azure SQL Data Warehouse przy użyciu klasy zasobów.

    Aby osiągnąć najlepszą możliwą przepływność, kopiowanie musi `xlargerc` być wykonywane przy użyciu użytkownika usługi SQL Data Warehouse należącego do klasy zasobów.  Dowiedz się, jak to zrobić, wykonując następujące [zmiany przykładu klasy zasobów użytkownika](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Utwórz schemat tabeli docelowej w bazie danych usługi Azure SQL Data Warehouse, uruchamiając następującą instrukcję DDL:

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
  Po wykonaniu czynności wstępnych jesteśmy gotowi skonfigurować działanie kopiowania za pomocą Kreatora kopiowania.

## <a name="launch-copy-wizard"></a>Uruchamianie Kreatora kopiowania
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij **pozycję Utwórz zasób** w lewym górnym rogu, kliknij pozycję **Analiza + analiza**i kliknij pozycję **Fabryka danych**.
3. W okienku **Nowa fabryka danych:**

   1. Wprowadź **LoadIntoSQLDWDataFactory** dla **nazwy**.
       Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **Nazwa fabryki danych "LoadIntoSQLDWDataFactory" nie jest dostępna,** zmień nazwę fabryki danych (na przykład yournameLoadIntoSQLDWDataFactory) i spróbuj utworzyć ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.  
   2. Wybierz swoją **subskrypcję** platformy Azure.
   3. Wykonaj jedną z następujących czynności dotyczącą grupy zasobów:
      1. Wybierz pozycję **Użyj istniejącej**, aby wybrać istniejącą grupę zasobów.
      2. Wybierz pozycję **Utwórz nowy**, aby wprowadzić nazwę grupy zasobów.
   4. Wybierz **lokalizację** fabryki danych.
   5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.  
   6. Kliknij przycisk **Utwórz**.
4. Po zakończeniu tworzenia zostanie wyświetlony **data factory** bloku, jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na stronie głównej usługi Fabryka danych kliknij kafelek **Kopiowanie danych**, aby uruchomić **Kreatora kopiowania**.

   > [!NOTE]
   > Jeśli widać, że przeglądarka sieci Web jest zablokowana podczas wyświetlania komunikatu „Autoryzowanie...”, wyłącz ustawienie **Blokuj pliki cookie i dane witryn innych firm** lub pozostaw je włączone i utwórz wyjątek dla witryny **login.microsoftonline.com**, a następnie spróbuj ponownie uruchomić kreatora.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Krok 1: Konfigurowanie harmonogramu ładowania danych
Pierwszym krokiem jest skonfigurowanie harmonogramu ładowania danych.  

Na stronie **Właściwości**:

1. Wprowadź **copyfromblobToAzureSqlDataWarehouse** for **Nazwa zadania**
2. Wybierz opcję **Uruchom teraz raz.**   
3. Kliknij przycisk **Dalej**.  

    ![Kreator kopiowania — strona Właściwości](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Krok 2: Konfigurowanie źródła
W tej sekcji przedstawiono kroki konfigurowania źródła: Azure Blob zawierający pliki elementów zamówienia TPC-H 1 TB.

1. Wybierz **usługę Azure Blob Storage** jako magazyn danych i kliknij przycisk **Dalej**.

    ![Kreator kopiowania — wybierz stronę źródłową](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Wypełnij informacje o połączeniu dla konta magazynu obiektów Blob platformy Azure i kliknij przycisk **Dalej**.

    ![Kreator kopiowania — informacje o połączeniu źródłowym](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Wybierz **folder** zawierający pliki elementów zamówienia TPC-H i kliknij przycisk **Dalej**.

    ![Kreator kopiowania — wybierz folder wejściowy](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Po kliknięciu **przycisku Dalej**ustawienia formatu pliku są wykrywane automatycznie.  Sprawdź, czy ogranicznik kolumny to "|" zamiast domyślnego przecinka ','.  Kliknij **przycisk Dalej** po wyświetleniu podglądu danych.

    ![Kreator kopiowania — ustawienia formatu pliku](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Krok 3: Konfigurowanie miejsca docelowego
W tej sekcji pokazano, jak `lineitem` skonfigurować tabelę docelową: w bazie danych usługi Azure SQL Data Warehouse.

1. Wybierz **pozycję Azure SQL Data Warehouse** jako magazyn docelowy i kliknij przycisk **Dalej**.

    ![Kreator kopiowania — wybierz docelowy magazyn danych](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Wypełnij informacje o połączeniu dla usługi Azure SQL Data Warehouse.  Upewnij się, że określono użytkownika, który `xlargerc` jest członkiem roli (zobacz sekcję **wymagań wstępnych,** aby uzyskać szczegółowe instrukcje) i kliknij przycisk **Dalej**.

    ![Kreator kopiowania — informacje o połączeniu docelowym](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Wybierz tabelę docelową i kliknij przycisk **Dalej**.

    ![Kreator kopiowania — strona mapowania tabel](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na stronie Mapowanie schematu pozostaw opcję "Zastosuj mapowanie kolumn" jako niezaznaczone i kliknij przycisk **Dalej**.

## <a name="step-4-performance-settings"></a>Krok 4: Ustawienia wydajności

**Zezwalaj na polibazę** jest zaznaczona domyślnie.  Kliknij przycisk **Dalej**.

![Kreator kopiowania — strona mapowania schematu](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Krok 5: Wdrażanie i monitorowanie wyników obciążenia
1. Kliknij przycisk **Zakończ,** aby wdrożyć.

    ![Kreator kopiowania — strona podsumowania](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Po zakończeniu wdrażania kliknij, `Click here to monitor copy pipeline` aby monitorować postęp uruchamiania kopii. Wybierz potok kopiowania utworzony na liście **Działania systemu Windows.**

    ![Kreator kopiowania — strona podsumowania](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Szczegóły przebiegu kopiowania można wyświetlić w **Eksploratorze okien działania** w prawym panelu, w tym wolumin danych odczytany ze źródła i zapisany w miejscu docelowym, czasie trwania i średniej przepływności dla przebiegu.

    Jak widać na poniższym zrzucie ekranu, kopiowanie 1 TB z usługi Azure Blob Storage do magazynu sql data zajęło 14 minut, skutecznie osiągając przepustowość 1,22 GBps!

    ![Kreator kopiowania — okno dialogowe zakończyło się pomyślnie](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Najlepsze rozwiązania
Oto kilka sprawdzonych rozwiązań dotyczących uruchamiania bazy danych usługi Azure SQL Data Warehouse:

* Podczas ładowania do indeksu magazynu kolumn klastrowanych należy użyć większej klasy zasobów.
* Aby uzyskać bardziej wydajne sprzężenia, należy rozważyć użycie dystrybucji skrótu przez kolumnę wyboru zamiast domyślnej dystrybucji okrężnej.
* Aby uzyskać większą szybkość ładowania, należy rozważyć użycie sterty dla danych przejściowych.
* Tworzenie statystyk po zakończeniu ładowania usługi Azure SQL Data Warehouse.

Szczegółowe informacje można znaleźć [w witrynie Azure SQL Data Warehouse.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)

## <a name="next-steps"></a>Następne kroki
* [Kreator kopiowania w fabryce danych](data-factory-copy-wizard.md) — ten artykuł zawiera szczegółowe informacje o Kreatorze kopiowania.
* [Kopiuj wydajność działania i dostrajanie —](data-factory-copy-activity-performance.md) ten artykuł zawiera referencyjne pomiary wydajności i przewodnik dostrajania.
