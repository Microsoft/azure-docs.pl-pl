---
title: Inspekcja usługi Azure SQL
description: Inspekcja bazy danych SQL usługi Azure umożliwia śledzenie zdarzeń bazy danych w dzienniku inspekcji.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 4e20129502e7538bd2f3354b75b33095970e1595
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411860"
---
# <a name="azure-sql-auditing"></a>Inspekcja usługi Azure SQL

Inspekcja dla usługi Azure [SQL Database](sql-database-technical-overview.md) i usługi [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure, obszarze roboczym usługi Log Analytics lub centrach zdarzeń. 

Ponadto inspekcja:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

- Umożliwia i ułatwia przestrzeganie standardów zgodności, chociaż nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania platformy Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najnowszą listę certyfikatów zgodności bazy danych SQL.

> [!NOTE] 
> Ten temat dotyczy zarówno bazy danych Sql Database azure, jak i bazy danych usługi Azure Synapse Analytics. Dla uproszczenia baza danych SQL jest używana w odniesieniu zarówno do usługi Azure SQL Database, jak i usługi Azure Synapse Analytics.

## <a name="overview"></a><a id="overview"></a>Omówienie

Inspekcja bazy danych SQL umożliwia:

- **Zachowaj** ścieżkę inspekcji wybranych zdarzeń. Możesz zdefiniować kategorie akcji bazy danych, które mają być poddane inspekcji.
- **Raport** dotyczący aktywności bazy danych. Możesz użyć wstępnie skonfigurowanych raportów i pulpitu nawigacyjnego, aby szybko rozpocząć pracę z raportowaniem aktywności i zdarzeń.
- **Analizuj** raporty. Można znaleźć podejrzane zdarzenia, nietypową aktywność i trendy.

> [!IMPORTANT]
> - Inspekcja bazy danych SQL platformy Azure jest zoptymalizowana pod kątem dostępności & wydajności. Podczas bardzo wysokiej aktywności usługa Azure SQL Database umożliwia kontynuowanie operacji i może nie rejestrować niektórych zdarzeń inspekcji.

#### <a name="auditing-limitations"></a>Ograniczenia inspekcji

- **Magazyn w wersji Premium** nie jest obecnie **obsługiwany.**
- **Hierarchiczna przestrzeń nazw** dla **konta magazynu usługi Azure Data Lake Storage Gen2** nie jest obecnie **obsługiwana.**
- Włączanie inspekcji w wstrzymanym **usłudze Azure SQL Data Warehouse** nie jest obsługiwane. Aby włączyć inspekcję, wznowić magazyn danych.

## <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definiowanie zasad inspekcji na poziomie serwera a na poziomie bazy danych

Zasady inspekcji można zdefiniować dla określonej bazy danych lub jako domyślne zasady serwera:

- Zasady serwera mają zastosowanie do wszystkich istniejących i nowo utworzonych baz danych na serwerze.

- Jeśli *inspekcja obiektów blob serwera jest włączona,* *zawsze ma zastosowanie do bazy danych*. Baza danych zostanie poddana inspekcji, niezależnie od ustawień inspekcji bazy danych.

- Włączenie inspekcji obiektów blob w bazie danych lub magazynie danych, oprócz włączania go na serwerze, nie zastępuje ani *nie* zmienia żadnych ustawień inspekcji obiektów blob serwera. Oba audyty będą istniały obok siebie. Innymi słowy baza danych jest poddana inspekcji dwukrotnie równolegle; raz przez zasady serwera i raz przez zasady bazy danych.

   > [!NOTE]
   > Należy unikać włączania inspekcji obiektów blob serwera i inspekcji obiektów blob bazy danych razem, chyba że:
    > - Chcesz użyć innego *konta magazynu,* *okresu przechowywania* lub *obszaru roboczego usługi Log Analytics* dla określonej bazy danych.
    > - Chcesz inspekcji typów zdarzeń lub kategorii dla określonej bazy danych, które różnią się od pozostałych baz danych na serwerze. Na przykład może być wstawia tabeli, które muszą być poddane inspekcji tylko dla określonej bazy danych.
   >
   > W przeciwnym razie zaleca się włączenie tylko inspekcji obiektów blob na poziomie serwera i pozostawienie inspekcji na poziomie bazy danych wyłączone dla wszystkich baz danych.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Konfigurowanie inspekcji serwera

Domyślne zasady inspekcji obejmują wszystkie akcje i następujący zestaw grup akcji, które będą przeprowadzać inspekcję wszystkich zapytań i procedur przechowywanych wykonywanych w bazie danych, a także pomyślnych i nieudanych logowania:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
Inspekcję można skonfigurować dla różnych typów akcji i grup akcji przy użyciu programu PowerShell, zgodnie z opisem w sekcji Zarządzanie inspekcją bazy danych SQL przy użyciu programu [Azure PowerShell.](#manage-auditing)

Usługa Azure SQL Database Audit przechowuje 4000 znaków danych dla pól znaków w rekordzie inspekcji. Gdy **instrukcja** lub **data_sensitivity_information** wartości zwrócone z akcji podlegającej inspekcji zawierają więcej niż 4000 znaków, wszelkie dane poza pierwszymi 4000 znaków zostaną **obcięty i nie poddane inspekcji**.
W poniższej sekcji opisano konfigurację inspekcji przy użyciu witryny Azure portal.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Przejdź do **pozycji Inspekcja** w nagłówku Zabezpieczenia w okienku baza danych/serwer SQL.
3. Jeśli wolisz skonfigurować zasady inspekcji serwera, możesz wybrać łącze **Wyświetl ustawienia serwera** na stronie inspekcji bazy danych. Następnie można wyświetlić lub zmodyfikować ustawienia inspekcji serwera. Zasady inspekcji serwera mają zastosowanie do wszystkich istniejących i nowo utworzonych baz danych na tym serwerze.

    ![Okienko nawigacji](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. Jeśli wolisz włączyć inspekcję na poziomie bazy danych, przełącz **inspekcję** **na ON**. Jeśli inspekcja serwera jest włączona, inspekcja skonfigurowana do bazy danych będzie istnieć obok inspekcji serwera.

5. Dostępnych jest wiele opcji konfigurowania, gdzie będą zapisywane dzienniki inspekcji. Dzienniki można zapisywać na koncie magazynu platformy Azure, w obszarze roboczym usługi Log Analytics do użycia przez dzienniki usługi Azure Monitor (w wersji zapoznawczej) lub w centrum zdarzeń do użycia przy użyciu centrum zdarzeń (wersja zapoznawcza). Można skonfigurować dowolną kombinację tych opcji, a dzienniki inspekcji zostaną zapisane w każdej z nich.
  
   ![opcje przechowywania](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name=""></a><a id="audit-storage-destination">Inspekcja do miejsca docelowego magazynu</a>

Aby skonfigurować zapisywanie dzienników inspekcji na koncie magazynu, wybierz pozycję **Magazyn** i otwórz **szczegóły magazynu**. Wybierz konto magazynu platformy Azure, na którym będą zapisywane dzienniki, a następnie wybierz okres przechowywania. Następnie kliknij przycisk **OK**. Dzienniki starsze niż okres przechowywania są usuwane.

- Wartość domyślna dla okresu przechowywania wynosi 0 (nieograniczona retencja). Tę wartość można zmienić, przenosząc suwak **Przechowywanie (Dni)** w **ustawieniach magazynu** podczas konfigurowania konta magazynu do inspekcji.
  - Jeśli zmienisz okres przechowywania z 0 (nieograniczone przechowywanie) na dowolną inną wartość, pamiętaj, że przechowywanie będzie miało zastosowanie tylko do dzienników zapisanych po zmianie wartości przechowywania (dzienniki zapisane w okresie, gdy przechowywanie zostało ustawione na nieograniczone, są zachowywane, nawet po włączeniu przechowywania).

  ![konto magazynu](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>Uwagi

- Dzienniki inspekcji są zapisywane w **dołączaniu obiektów blob** w magazynie obiektów Blob platformy Azure w ramach subskrypcji platformy Azure
- Aby skonfigurować niezmienne magazynu dziennika dla zdarzenia inspekcji na poziomie serwera lub bazy danych postępuj zgodnie z [instrukcjami dostarczonymi przez usługę Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Upewnij się, że wybrano opcję **Zezwalaj na dodatkowe dołączanie** podczas konfigurowania magazynu obiektów blob niezmienne).
- Dzienniki inspekcji można zapisywać na koncie usługi Azure Storage za siecią wirtualną lub zaporą. Aby uzyskać szczegółowe instrukcje, [zobacz: Pisanie inspekcji na konto magazynu za siecią wirtualną i zaporą](create-auditing-storage-account-vnet-firewall.md).
- Po skonfigurowaniu ustawień inspekcji można włączyć nową funkcję wykrywania zagrożeń i skonfigurować wiadomości e-mail do otrzymywania alertów zabezpieczeń. Korzystając z wykrywania zagrożeń, są otrzymywane proaktywne alerty o nietypowych działaniach bazy danych, które mogą wskazywać potencjalne zagrożenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do wykrywania zagrożeń](sql-database-threat-detection-get-started.md).
- Aby uzyskać szczegółowe informacje na temat formatu dziennika, hierarchii folderu magazynu i konwencji nazewnictwa, zobacz [odwołanie do formatu dziennika inspekcji obiektów blob](https://go.microsoft.com/fwlink/?linkid=829599).
- Podczas korzystania z uwierzytelniania AAD rekordy logowania nie powiodło się *nie* pojawi się w dzienniku inspekcji SQL. Aby wyświetlić nieudane rekordy inspekcji logowania, należy odwiedzić [portal usługi Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), który rejestruje szczegóły tych zdarzeń.
- Inspekcja [replik tylko do odczytu](sql-database-read-scale-out.md) jest włączona automatycznie. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [format dziennika inspekcji bazy danych SQL](sql-database-audit-log-format.md). 

### <a name=""></a><a id="audit-log-analytics-destination">Inspekcja do usługi Log Analytics miejsce docelowe</a>
  
Aby skonfigurować zapisywanie dzienników inspekcji w obszarze roboczym usługi Log Analytics, wybierz pozycję **Usługa Log Analytics (Wersja zapoznawcza)** i otwórz **szczegóły usługi Log Analytics**. Wybierz lub utwórz obszar roboczy usługi Log Analytics, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**.
    
  > [!WARNING]
   > Włączenie inspekcji usługi Log Analytics spowoduje poniesienie kosztów na podstawie stawek pozyskiwania. Należy pamiętać o skojarzonych kosztów przy użyciu tej [opcji](https://azure.microsoft.com/pricing/details/monitor/)lub należy rozważyć przechowywanie dzienników inspekcji na koncie magazynu platformy Azure.
   
   ![Obszar analityczny LogAnalyticswork](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name=""></a><a id="audit-event-hub-destination">Inspekcja do centrum zdarzeń docelowego</a>

> [!WARNING]
> Włączenie inspekcji na serwerze, na którym znajduje się pula **SQL, powoduje wznowienie puli SQL i ponowne wstrzymanie,** co może ponieść opłaty rozliczeniowe.
> Włączenie inspekcji w wstrzymanym pula SQL nie jest możliwe. Aby ją włączyć, odłącz pulę SQL.

Aby skonfigurować zapisywanie dzienników inspekcji w centrum zdarzeń, wybierz Centrum **zdarzeń (Wersja zapoznawcza)** i otwórz **szczegóły Centrum zdarzeń**. Wybierz centrum zdarzeń, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**. Upewnij się, że centrum zdarzeń znajduje się w tym samym regionie co baza danych i serwer.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analizowanie dzienników inspekcji i raportów

Jeśli zdecydujesz się napisać dzienniki inspekcji w dziennikach usługi Azure Monitor:

- Użyj [portalu Azure](https://portal.azure.com).  Otwórz odpowiednią bazę danych. U góry strony **Inspekcja** bazy danych kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![wyświetlanie dzienników inspekcji](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Następnie masz dwa sposoby wyświetlania dzienników:
    
    Kliknięcie usługi **Log Analytics** u góry strony **Rekordy inspekcji** spowoduje otwarcie widoku Dzienniki w obszarze roboczym usługi Log Analytics, w którym można dostosować zakres czasu i zapytanie wyszukiwania.
    
    ![otwieranie w obszarze roboczym usługi Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Kliknięcie **przycisku Wyświetl pulpit nawigacyjny** u góry strony Rekordy **inspekcji** spowoduje otwarcie pulpitu nawigacyjnego wyświetlającego informacje o dziennikach inspekcji, na którym można przejść do szczegółów w witrynie Security Insights, Dostęp do poufnych danych i nie tylko. Ten pulpit nawigacyjny został zaprojektowany, aby ułatwić uzyskiwanie szczegółowych informacji o zabezpieczeniach danych.
    Można również dostosować zakres czasu i zapytanie wyszukiwania. 
    ![Wyświetl pulpit nawigacyjny analizy dzienników](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Pulpit nawigacyjny analizy dzienników](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Szczegółowe informacje o zabezpieczeniach usługi Log Analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Alternatywnie można również uzyskać dostęp do dzienników inspekcji z bloku usługi Log Analytics. Otwórz obszar roboczy usługi Log Analytics i w sekcji **Ogólne** kliknij pozycję **Dzienniki**. Można rozpocząć od prostej kwerendy, takiej jak: *wyszukiwanie "SQLSecurityAuditEvents",* aby wyświetlić dzienniki inspekcji.
    W tym miejscu można również użyć [dzienników usługi Azure Monitor](../log-analytics/log-analytics-log-search.md) do uruchamiania zaawansowanych wyszukiwań w danych dziennika inspekcji. Dzienniki usługi Azure Monitor zapewniają szczegółowe informacje operacyjne w czasie rzeczywistym przy użyciu zintegrowanych systemów wyszukiwania i niestandardowych pulpitów nawigacyjnych, aby łatwo analizować miliony rekordów we wszystkich obciążeniach i serwerach. Aby uzyskać dodatkowe przydatne informacje na temat języka wyszukiwania i poleceń dziennika usługi Azure Monitor, zobacz [Odwołanie do wyszukiwania dzienników usługi Azure Monitor](../log-analytics/log-analytics-log-search.md).

Jeśli wybrano zapisywanie dzienników inspekcji w Centrum zdarzeń:

- Aby korzystać z danych dzienników inspekcji z Usługi Event Hub, należy skonfigurować strumień, aby korzystać ze zdarzeń i zapisać je do obiektu docelowego. Aby uzyskać więcej informacji, zobacz [Dokumentacja usługi Azure Event Hubs](../event-hubs/index.yml).
- Dzienniki inspekcji w Centrum zdarzeń są przechwytywane w treści zdarzeń [Apache Avro](https://avro.apache.org/) i przechowywane przy użyciu formatowania JSON z kodowaniem UTF-8. Aby odczytać dzienniki inspekcji, można użyć [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) lub podobnych narzędzi, które przetwarzają ten format.

Jeśli zdecydujesz się napisać dzienniki inspekcji na koncie magazynu platformy Azure, istnieje kilka metod, których można użyć do wyświetlania dzienników:

- Dzienniki inspekcji są agregowane na koncie wybranym podczas instalacji. Dzienniki inspekcji można eksplorować za pomocą narzędzia, takiego jak [Eksplorator usługi Azure Storage.](https://storageexplorer.com/) W magazynie platformy Azure dzienniki inspekcji są zapisywane jako zbiór plików obiektów blob w kontenerze o nazwie **sqldbauditlogs**. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [format dziennika inspekcji bazy danych SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Użyj [portalu Azure](https://portal.azure.com).  Otwórz odpowiednią bazę danych. U góry strony **Inspekcja** bazy danych kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Okienko nawigacji](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    Zostanie otwarty **rekord inspekcji,** z którego będzie można wyświetlać dzienniki.

  - Możesz wyświetlić określone daty, klikając **pozycję Filtr** u góry strony **Rekordy inspekcji.**
  - Można przełączać się między rekordami inspekcji utworzonymi przez *zasady inspekcji serwera* a *zasadami inspekcji bazy danych,* przełączając **źródło inspekcji**.
  - Można wyświetlić tylko rekordy inspekcji związane z iniekcją SQL, zaznaczając pole wyboru **Pokaż tylko rekordy inspekcji dla iniekcji SQL.**

       ![Okienko nawigacji]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- Użyj funkcji systemu **sys.fn_get_audit_file** (T-SQL), aby zwrócić dane dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Użyj **plików inspekcji korespondencji seryjnej** w programie SQL Server Management Studio (począwszy od usługi SSMS 17):
    1. Z menu SSMS wybierz polecenie **File** > **Open** > **Merge Audit Files**.

        ![Okienko nawigacji](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. Zostanie otwarte okno dialogowe **Dodawanie plików inspekcji.** Wybierz jedną z opcji **Dodaj,** aby wybrać, czy pliki inspekcji mają być scalane z dysku lokalnego, czy importowanie ich z usługi Azure Storage. Musisz podać szczegóły usługi Azure Storage i klucz konta.

    3. Po dodaniu wszystkich plików do scalenia kliknij przycisk **OK,** aby zakończyć operację scalania.

    4. Scalony plik zostanie otwarty w systemie SSMS, gdzie można go przeglądać i analizować, a także eksportować do pliku XEL lub CSV lub do tabeli.

- Korzystanie z usługi Power BI. W usłudze Power BI można wyświetlać i analizować dane dziennika inspekcji. Aby uzyskać więcej informacji i uzyskać dostęp do szablonu do pobrania, zobacz [Analizowanie danych dziennika inspekcji w usłudze Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Pobierz pliki dziennika z kontenera obiektów blob usługi Azure Storage za pośrednictwem portalu lub za pomocą narzędzia, takiego jak [Eksplorator usługi Azure Storage.](https://storageexplorer.com/)
  - Po pobraniu pliku dziennika lokalnie kliknij dwukrotnie plik, aby otworzyć, wyświetlić i przeanalizować dzienniki w systemie SSMS.
  - Można również pobrać wiele plików jednocześnie za pośrednictwem Usługi Azure Storage Explorer. W tym celu kliknij prawym przyciskiem myszy określony podfolder i wybierz pozycję **Zapisz, aby** zapisać go w folderze lokalnym.

- Dodatkowe metody:

  - Po pobraniu kilku plików lub podfolderu zawierającego pliki dziennika można je scalić lokalnie, zgodnie z opisem opisanymi wcześniej instrukcjami scalania plików inspekcji SSMS.
  - Programowo wyświetlaj dzienniki inspekcji obiektów blob:

    - [Kwerenda rozszerzone pliki zdarzeń](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) przy użyciu programu PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Praktyki produkcyjne

<!--The description in this section refers to preceding screen captures.-->

#### <a name="auditing-geo-replicated-databases"></a>Inspekcja replikowanych geograficznie baz danych

W przypadku replikowanych geograficznie baz danych po włączeniu inspekcji w podstawowej bazie danych pomocnicza baza danych będzie miała identyczną zasadę inspekcji. Możliwe jest również skonfigurowanie inspekcji w pomocniczej bazie danych, włączając inspekcję na **serwerze pomocniczym,** niezależnie od podstawowej bazy danych.

- Poziom serwera (**zalecane):** Włącz inspekcję zarówno na **serwerze podstawowym,** jak i **na serwerze pomocniczym** - bazy danych podstawowych i pomocniczych będą poddawane inspekcji niezależnie na podstawie odpowiednich zasad na poziomie serwera.
- Na poziomie bazy danych: inspekcję na poziomie bazy danych dla pomocniczych baz danych można skonfigurować tylko z ustawień inspekcji podstawowej bazy danych.
  - Inspekcja musi być włączona w *samej podstawowej bazie danych,* a nie na serwerze.
  - Po włączeniu inspekcji w podstawowej bazie danych zostanie ona również włączona w pomocniczej bazie danych.

    >[!IMPORTANT]
    >Dzięki inspekcji na poziomie bazy danych ustawienia magazynu pomocniczej bazy danych będą identyczne z ustawieniami podstawowej bazy danych, powodując ruch międzyregionauałowy. Zaleca się włączenie tylko inspekcji na poziomie serwera i pozostawienie inspekcji na poziomie bazy danych wyłączonej dla wszystkich baz danych.

#### <a name="storage-key-regeneration"></a>Regeneracja klucza pamięci masowej

W produkcji jest prawdopodobne, aby okresowo odświeżyć klucze magazynu. Podczas pisania dzienników inspekcji w usłudze Azure Storage należy ponownie zapisać zasady inspekcji podczas odświeżania kluczy. Proces jest następujący:

1. Otwórz **szczegóły magazynu**. W polu **Klucz dostępu do magazynu** wybierz pozycję **Pomocniczy**i kliknij przycisk **OK**. Następnie kliknij przycisk **Zapisz** u góry strony konfiguracji inspekcji.

    ![Okienko nawigacji](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. Przejdź do strony konfiguracji magazynu i ponownie wygeneruj podstawowy klucz dostępu.

    ![Okienko nawigacji](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. Wróć do strony konfiguracji inspekcji, przełącz klucz dostępu magazynu z pomocniczego na podstawowy, a następnie kliknij przycisk **OK**. Następnie kliknij przycisk **Zapisz** u góry strony konfiguracji inspekcji.
4. Wróć do strony konfiguracji magazynu i ponownie wygeneruj pomocniczy klucz dostępu (w ramach przygotowań do cyklu odświeżania następnego klucza).

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>Zarządzanie inspekcją programu Sql Server i bazy danych platformy Azure

#### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

**Polecenia cmdlet programu PowerShell (w tym obsługa klauzuli WHERE dla dodatkowego filtrowania):**

- [Tworzenie lub aktualizowanie zasad inspekcji bazy danych (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Pobierz zasady inspekcji bazy danych (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Pobierz zasady inspekcji serwera (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Usuń zasady inspekcji bazy danych (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Usuń zasady inspekcji serwera (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Przykładowy skrypt zobacz [Konfigurowanie inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

#### <a name="using-rest-api"></a>Korzystanie z interfejsu API REST

**INTERFEJS SPOCZYNKOWY**:

- [Tworzenie lub aktualizowanie zasad inspekcji bazy danych](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz zasady inspekcji bazy danych](/rest/api/sql/database%20auditing%20settings/get)
- [Pobierz zasady inspekcji serwera](/rest/api/sql/server%20auditing%20settings/get)

Rozszerzone zasady z klauzulą WHERE obsługuje dodatkowe filtrowanie:

- [Tworzenie lub aktualizowanie zasad *rozszerzonej* inspekcji bazy danych](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Tworzenie lub aktualizowanie *zasad rozszerzonej* inspekcji serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz zasady *rozszerzonej* inspekcji bazy danych](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Pobierz zasady *rozszerzonej* inspekcji serwera](/rest/api/sql/server%20auditing%20settings/get)

#### <a name="using-azure-resource-manager-templates"></a>Używanie szablonów usługi Azure Resource Manager

Inspekcję bazy danych SQL platformy Azure można zarządzać przy użyciu szablonów [usługi Azure Resource Manager,](../azure-resource-manager/management/overview.md) jak pokazano w poniższych przykładach:

- [Wdrażanie programu Azure SQL Server z włączoną inspekcją w celu zapisywania dzienników inspekcji na koncie magazynu obiektów Blob platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Wdrażanie programu Azure SQL Server z włączoną inspekcją do pisania dzienników inspekcji w usłudze Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Wdrażanie programu Azure SQL Server z włączoną inspekcją w celu zapisywania dzienników inspekcji w centrach zdarzeń](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Połączone próbki znajdują się w zewnętrznym publicznym repozytorium i są dostarczane "tak jak jest", bez gwarancji i nie są obsługiwane w ramach żadnego programu pomocy technicznej firmy Microsoft/usługi.
