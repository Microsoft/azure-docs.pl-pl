---
title: Inspekcja usługi Azure SQL
description: Funkcja inspekcji usługi Azure SQL Database umożliwia śledzenie zdarzeń bazy danych w dzienniku inspekcji.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 57c4b22dfe6ef6cf44be64a4b5c042403f64ccf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096660"
---
# <a name="azure-sql-auditing"></a>Inspekcja usługi Azure SQL

Inspekcja dla usługi Azure [SQL Database](sql-database-technical-overview.md) i [usługi Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage, log Analytics obszarze roboczym lub Event Hubs. 

Ponadto inspekcja:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

- Umożliwia i ułatwia przestrzeganie standardów zgodności, chociaż nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , gdzie można znaleźć najbardziej aktualną listę certyfikatów zgodności SQL Database.

> [!NOTE] 
> Ten temat ma zastosowanie do baz danych programu Azure SQL Database i usługi Azure Synapse Analytics. Dla uproszczenia SQL Database jest używany podczas odnoszących się do Azure SQL Database i usługi Azure Synapse Analytics.

## <a name="overview"></a><a id="overview"></a>Omówienie

Inspekcja bazy danych SQL umożliwia:

- **Zachowaj** dziennik inspekcji dla wybranych zdarzeń. Możesz zdefiniować kategorie akcji bazy danych, które mają być poddane inspekcji.
- **Raport** dotyczący działania bazy danych. Możesz użyć wstępnie skonfigurowanych raportów i pulpitu nawigacyjnego, aby szybko rozpocząć pracę z raportowaniem aktywności i zdarzeń.
- **Analizuj** raporty. Można znaleźć podejrzane zdarzenia, nietypową aktywność i trendy.

> [!IMPORTANT]
> - Inspekcja Azure SQL Database jest zoptymalizowana pod kątem dostępności & wydajności. Podczas bardzo dużego działania Azure SQL Database umożliwia wykonywanie operacji i może nie rejestrować niektórych zdarzeń poddawanych inspekcji.

### <a name="auditing-limitations"></a>Ograniczenia inspekcji

- Usługa **Premium Storage** nie jest obecnie **obsługiwana**.
- **Hierarchiczna przestrzeń nazw** dla **konta usługi Azure Data Lake Storage Gen2 Storage** nie jest obecnie **obsługiwana**.
- Włączanie inspekcji wstrzymanej **Azure SQL Data Warehouse** nie jest obsługiwane. Aby włączyć inspekcję, Wznów działanie hurtowni danych.

#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definiowanie zasad inspekcji na poziomie serwera i na poziomie bazy danych

Zasady inspekcji można zdefiniować dla konkretnej bazy danych lub jako domyślne zasady serwera:

- Zasady serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na serwerze.

- Jeśli *Inspekcja serwera jest włączona*, *zawsze ma zastosowanie do bazy danych programu*. Baza danych będzie poddawana inspekcji, niezależnie od ustawień inspekcji bazy danych.

- Włączenie inspekcji bazy danych lub magazynu danych (oprócz włączenia jej na serwerze) *nie przesłania ani* nie zmienia żadnych ustawień inspekcji serwera. Obie inspekcje będą istnieć obok siebie. Inaczej mówiąc, baza danych jest monitorowana dwukrotnie. raz przez zasady serwera i jeden raz przez zasady bazy danych.

   > [!NOTE]
   > Należy unikać jednoczesnego włączania inspekcji serwera i inspekcji obiektów BLOB bazy danych, chyba że:
    > - Chcesz użyć innego *konta magazynu*, *okresu przechowywania* lub *log Analytics obszaru roboczego* dla określonej bazy danych.
    > - Chcesz przeprowadzić inspekcję typów zdarzeń lub kategorii dla określonej bazy danych, która różni się od reszty baz danych na serwerze. Na przykład można mieć wstawienia tabeli, które muszą być poddane inspekcji tylko dla określonej bazy danych.
   >
   > W przeciwnym razie zalecamy włączenie tylko inspekcji na poziomie serwera i pozostawienie wyłączonej inspekcji na poziomie bazy danych dla wszystkich baz danych.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Skonfiguruj inspekcję serwera

Domyślne zasady inspekcji obejmują wszystkie akcje i następujący zestaw grup akcji, który przeprowadzi inspekcję wszystkich zapytań i procedur składowanych wykonywanych w bazie danych, jak również pomyślnie i nieudanych logowań:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
Inspekcję dla różnych typów akcji i grup akcji można skonfigurować przy użyciu programu PowerShell, zgodnie z opisem w sekcji [Zarządzanie inspekcją usługi SQL Database przy użyciu Azure PowerShell](#manage-auditing) .

Azure SQL Database inspekcji przechowuje 4000 znaków danych dla pól znaków w rekordzie inspekcji. Gdy **instrukcja** lub **data_sensitivity_information** wartości zwracane z akcji objętej inspekcją zawierają więcej niż 4000 znaków, wszystkie dane spoza pierwszych 4000 znaków będą **obcinane i nieobjęte inspekcją**.
W poniższej sekcji opisano konfigurację inspekcji przy użyciu Azure Portal.

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Przejdź do opcji **Inspekcja** pod nagłówkiem zabezpieczenia w okienku baza danych SQL/serwer.
3. Jeśli wolisz skonfigurować zasady inspekcji serwera, możesz wybrać łącze **Wyświetl ustawienia serwera** na stronie Inspekcja bazy danych. Następnie można wyświetlić lub zmodyfikować ustawienia inspekcji serwera. Zasady inspekcji serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na tym serwerze.

    ![Okienko nawigacji](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. Jeśli wolisz włączyć inspekcję na poziomie bazy danych, przełącz **inspekcję** na wartość **włączone**. Jeśli Inspekcja serwera jest włączona, inspekcja skonfigurowana dla bazy danych będzie istnieć równolegle z inspekcją serwera.

5. Istnieje wiele opcji konfigurowania, w których będą zapisywane dzienniki inspekcji. Dzienniki można zapisywać na koncie usługi Azure Storage, w obszarze roboczym Log Analytics do użycia przez dzienniki Azure Monitor (wersja zapoznawcza) lub do centrum zdarzeń w celu użycia przy użyciu centrum zdarzeń (wersja zapoznawcza). Można skonfigurować dowolną kombinację tych opcji, a dzienniki inspekcji będą zapisywane w każdym z nich.
  
   ![Opcje magazynu](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Inspekcja w miejscu docelowym magazynu

Aby skonfigurować zapisywanie dzienników inspekcji na koncie magazynu, wybierz pozycję **Magazyn** i Otwórz **szczegóły magazynu**. Wybierz konto usługi Azure Storage, na którym będą zapisywane dzienniki, a następnie wybierz okres przechowywania. Następnie kliknij przycisk **OK**. Dzienniki starsze niż okres przechowywania są usuwane.

- Wartość domyślna okresu przechowywania to 0 (nieograniczony czas przechowywania). Tę wartość można zmienić, przesuwając suwak **przechowywanie (dni)** w **ustawieniach magazynu** podczas konfigurowania konta magazynu na potrzeby inspekcji.
  - Jeśli zmienisz okres przechowywania z 0 (nieograniczony czas przechowywania) na inną wartość, należy pamiętać, że przechowywanie będzie dotyczyło tylko dzienników utworzonych po zmianie wartości przechowywania (dzienniki zapisane w okresie, gdy czas przechowywania był ustawiony na nieograniczony, nawet po włączeniu przechowywania).

  ![konto magazynu](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>Uwagi

- Dzienniki inspekcji są zapisywane w celu **dołączania obiektów BLOB** w usłudze Azure Blob Storage w ramach subskrypcji platformy Azure
- Aby skonfigurować niezmienny magazyn dzienników dla zdarzeń inspekcji na poziomie serwera lub bazy danych, postępuj zgodnie z [instrukcjami dostarczonymi przez usługę Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Upewnij się, że wybrano opcję **Zezwalaj na dodatkowe dołączenia** podczas konfigurowania niezmiennego magazynu obiektów BLOB).
- Dzienniki inspekcji można zapisywać na koncie usługi Azure Storage za siecią wirtualną lub zaporą. Aby uzyskać szczegółowe instrukcje, zobacz [Inspekcja zapisu na koncie magazynu za siecią wirtualną i zaporą](create-auditing-storage-account-vnet-firewall.md).
- Po skonfigurowaniu ustawień inspekcji można włączyć nową funkcję wykrywania zagrożeń i skonfigurować wiadomości e-mail w celu otrzymywania alertów zabezpieczeń. W przypadku korzystania z wykrywania zagrożeń otrzymywane są aktywne alerty dotyczące nietypowych działań bazy danych, które mogą wskazywać na potencjalne zagrożenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [wprowadzenie do wykrywania zagrożeń](sql-database-threat-detection-get-started.md).
- Aby uzyskać szczegółowe informacje na temat formatu dziennika, hierarchii folderu magazynu i konwencji nazewnictwa, zobacz [dokumentacja formatu dziennika inspekcji obiektów BLOB](https://go.microsoft.com/fwlink/?linkid=829599).
- W przypadku korzystania z uwierzytelniania usługi AAD rekordy nieudanych logowań *nie* będą widoczne w dzienniku inspekcji SQL. Aby wyświetlić nieudane rekordy inspekcji logowania, należy odwiedzić [portal Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), w którym znajdują się szczegóły dotyczące tych zdarzeń.
- Inspekcja [replik tylko do odczytu](sql-database-read-scale-out.md) jest włączana automatycznie. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [format dziennika inspekcji SQL Database](sql-database-audit-log-format.md). 

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Inspekcja w celu Log Analytics lokalizacji docelowej
  
Aby skonfigurować zapisywanie dzienników inspekcji do obszaru roboczego Log Analytics, wybierz pozycję **log Analytics (wersja zapoznawcza)** i Otwórz **log Analytics szczegóły**. Wybierz lub Utwórz obszar roboczy Log Analytics, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**.
   
   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Inspekcja w miejscu docelowym centrum zdarzeń

> [!WARNING]
> Włączenie inspekcji na serwerze, na którym znajduje się pula SQL, **powoduje, że Pula SQL jest wznawiana i ponownie wstrzymywana** , co może spowodować naliczenie opłat.
> Włączanie inspekcji wstrzymanej puli SQL nie jest możliwe. Aby ją włączyć, Cofnij wstrzymanie puli SQL.

Aby skonfigurować zapisywanie dzienników inspekcji do centrum zdarzeń, wybierz pozycję **centrum zdarzeń (wersja zapoznawcza)** i Otwórz **szczegóły centrum zdarzeń**. Wybierz centrum zdarzeń, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**. Upewnij się, że centrum zdarzeń znajduje się w tym samym regionie, w którym znajduje się baza danych i serwer.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analizowanie dzienników i raportów inspekcji

W przypadku wybrania opcji zapisania dzienników inspekcji do Azure Monitor dzienników:

- Użyj [Azure Portal](https://portal.azure.com).  Otwórz odpowiednią bazę danych. W górnej części strony **Inspekcja** bazy danych kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Wyświetlanie dzienników inspekcji](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Następnie masz dwa sposoby wyświetlania dzienników:
    
    Kliknięcie **log Analytics** w górnej części strony **rekordy inspekcji** spowoduje otwarcie widoku dzienniki w obszarze roboczym log Analytics, gdzie można dostosować zakres czasu i zapytanie wyszukiwania.
    
    ![Otwórz w obszarze roboczym Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Kliknięcie przycisku **Wyświetl pulpit nawigacyjny** w górnej części strony **rekordy inspekcji** spowoduje otwarcie pulpitu nawigacyjnego wyświetlającego informacje o dziennikach inspekcji, w którym można przejść do szczegółowych informacji o zabezpieczeniach, uzyskać dostęp do poufnych danych i nie tylko. Ten pulpit nawigacyjny umożliwia uzyskanie szczegółowych informacji o zabezpieczeniach danych.
    Możesz również dostosować zakres czasu i zapytanie wyszukiwania. 
    ![Wyświetl pulpit nawigacyjny Log Analytics](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Pulpit nawigacyjny Log Analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics szczegółowe informacje o zabezpieczeniach](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Alternatywnie można również uzyskać dostęp do dzienników inspekcji w bloku Log Analytics. Otwórz obszar roboczy Log Analytics i w sekcji **Ogólne** kliknij pozycję **dzienniki**. Możesz zacząć od prostego zapytania, takiego jak: *Search "SQLSecurityAuditEvents"* , aby wyświetlić dzienniki inspekcji.
    W tym miejscu możesz również użyć [dzienników Azure monitor](../log-analytics/log-analytics-log-search.md) , aby uruchomić zaawansowane wyszukiwania w danych dziennika inspekcji. Dzienniki Azure Monitor udostępniają usługi operacyjne w czasie rzeczywistym przy użyciu zintegrowanego wyszukiwania i niestandardowych pulpitów nawigacyjnych, które umożliwiają łatwe analizowanie milionów rekordów na wszystkich obciążeniach i serwerach. Aby uzyskać dodatkowe informacje dotyczące Azure Monitor języka i poleceń wyszukiwania dzienników, zobacz artykuł [Azure monitor Logs Search Reference](../log-analytics/log-analytics-log-search.md).

W przypadku wybrania opcji zapisania dzienników inspekcji do centrum zdarzeń:

- Aby korzystać z danych inspekcji dzienników z centrum zdarzeń, należy skonfigurować strumień, który będzie korzystał z zdarzeń i zapisywać je w celu. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Event Hubs](../event-hubs/index.yml).
- Dzienniki inspekcji w centrum zdarzeń są przechwytywane w treści zdarzeń [Apache Avro](https://avro.apache.org/) i przechowywane przy użyciu formatowania JSON przy użyciu kodowania UTF-8. Aby odczytać dzienniki inspekcji, można użyć [narzędzi Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) lub podobnych narzędzi, które przetwarzają ten format.

W przypadku wybrania opcji zapisania dzienników inspekcji na koncie usługi Azure Storage istnieje kilka metod wyświetlania dzienników:

- Dzienniki inspekcji są agregowane na koncie wybranym podczas instalacji. Dzienniki inspekcji można eksplorować przy użyciu narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/). W usłudze Azure Storage dzienniki inspekcji są zapisywane jako kolekcja plików obiektów BLOB w kontenerze o nazwie **sqldbauditlogs**. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [format dziennika inspekcji SQL Database](https://go.microsoft.com/fwlink/?linkid=829599).

- Użyj [Azure Portal](https://portal.azure.com).  Otwórz odpowiednią bazę danych. W górnej części strony **Inspekcja** bazy danych kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Okienko nawigacji](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    **Rekordy inspekcji** są otwierane, z poziomu którego będziesz mieć możliwość wyświetlania dzienników.

  - Aby wyświetlić określone daty, kliknij pozycję **Filtruj** w górnej części strony **Rejestrowanie inspekcji** .
  - Można przełączać się między rekordami inspekcji, które zostały utworzone przez *zasady inspekcji serwera* i *zasad inspekcji bazy danych* przez przełączenie **źródła inspekcji**.
  - Można wyświetlić tylko rekordy inspekcji powiązane z iniekcją SQL, zaznaczając pole wyboru **Pokaż tylko rekordy inspekcji dla iniekcji kodu SQL** .

       ![Okienko nawigacji]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- Użyj funkcji system **sys. fn_get_audit_file** (T-SQL), aby zwrócić dane dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [sys. fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Użyj **plików inspekcji scalania** w SQL Server Management Studio (począwszy od programu SSMS 17):
    1. Z menu programu SSMS wybierz pozycję **plik** > **Otwórz** > **pliki inspekcji scalania**.

        ![Okienko nawigacji](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. Zostanie otwarte okno dialogowe **Dodawanie plików inspekcji** . Wybierz jedną z opcji **dodawania** , aby wybrać, czy pliki inspekcji mają być scalane z dysku lokalnego, czy też zaimportować je z usługi Azure Storage. Musisz podać szczegóły i klucz konta usługi Azure Storage.

    3. Po dodaniu wszystkich plików do scalenia kliknij przycisk **OK** , aby ukończyć operację scalania.

    4. Scalony plik zostanie otwarty w programie SSMS, gdzie można go wyświetlić i przeanalizować, a także wyeksportować do pliku XEL lub CSV lub do tabeli.

- Użyj Power BI. Można wyświetlać i analizować dane dziennika inspekcji w Power BI. Aby uzyskać więcej informacji i uzyskać dostęp do możliwego do pobrania szablonu, zobacz [Analizowanie danych dziennika inspekcji w Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Pobierz pliki dziennika z kontenera obiektów BLOB usługi Azure Storage za pośrednictwem portalu lub za pomocą narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).
  - Po pobraniu pliku dziennika lokalnie kliknij dwukrotnie plik, aby otworzyć, wyświetlić i analizować dzienniki w programie SSMS.
  - Możesz również pobrać wiele plików jednocześnie za pośrednictwem Eksplorator usługi Azure Storage. Aby to zrobić, kliknij prawym przyciskiem myszy określony podfolder i wybierz polecenie **Zapisz jako** do zapisania w folderze lokalnym.

- Dodatkowe metody:

  - Po pobraniu kilku plików lub podfolderu, który zawiera pliki dziennika, można scalić je lokalnie zgodnie z opisem w artykule informacje o plikach inspekcji scalania programu SSMS opisane wcześniej.
  - Programowe Wyświetlanie dzienników inspekcji obiektów blob:

    - [Wykonywanie zapytań dotyczących plików zdarzeń rozszerzonych](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) za pomocą programu PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Praktyki produkcyjne

<!--The description in this section refers to preceding screen captures.-->

### <a name="auditing-geo-replicated-databases"></a>Inspekcja replikowanych geograficznie baz danych

Po włączeniu inspekcji podstawowej bazy danych przy użyciu baz danych z replikacją geograficzną pomocnicza baza danych będzie miała identyczne zasady inspekcji. Istnieje również możliwość skonfigurowania inspekcji pomocniczej bazy danych przez włączenie inspekcji na **serwerze pomocniczym**niezależnie od podstawowej bazy danych.

- Poziom serwera (**zalecane**): Włącz inspekcję zarówno na **serwerze podstawowym** , jak i na **serwerze pomocniczym** — podstawowe i pomocnicze bazy danych zostaną poddane inspekcji niezależnie od ich odpowiednich zasad na poziomie serwera.
- Poziom bazy danych: Inspekcja na poziomie bazy danych dla pomocniczych baz danych można skonfigurować tylko przy użyciu ustawień inspekcji podstawowej bazy danych.
  - Inspekcja musi być włączona w *podstawowej bazie danych*, a nie na serwerze.
  - Po włączeniu inspekcji w podstawowej bazie danych zostanie ona również włączona w pomocniczej bazie danych.

    >[!IMPORTANT]
    >W przypadku inspekcji na poziomie bazy danych ustawienia magazynu dla pomocniczej bazy danych będą takie same, jak w przypadku ruchu międzyregionalnego. Zalecamy włączenie tylko inspekcji na poziomie serwera i pozostawienie wyłączonej inspekcji na poziomie bazy danych dla wszystkich baz danych.

### <a name="storage-key-regeneration"></a>Ponowne generowanie klucza magazynu

W środowisku produkcyjnym można okresowo odświeżać klucze magazynu. Podczas zapisywania dzienników inspekcji w usłudze Azure Storage należy ponownie zapisać zasady inspekcji podczas odświeżania kluczy. Proces jest następujący:

1. Otwórz **szczegóły magazynu**. W polu **klucz dostępu do magazynu** wybierz pozycję **pomocniczy**, a następnie kliknij przycisk **OK**. Następnie kliknij pozycję **Zapisz** w górnej części strony Konfiguracja inspekcji.

    ![Okienko nawigacji](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. Przejdź do strony Konfiguracja magazynu i ponownie Wygeneruj podstawowy klucz dostępu.

    ![Okienko nawigacji](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. Wróć do strony Konfiguracja inspekcji, przełącz klucz dostępu do magazynu z pomocnicza na podstawowy, a następnie kliknij przycisk **OK**. Następnie kliknij pozycję **Zapisz** w górnej części strony Konfiguracja inspekcji.
4. Wróć do strony Konfiguracja magazynu i Wygeneruj ponownie pomocniczy klucz dostępu (w przygotowaniu dla cyklu odświeżania następnego klucza).

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>Zarządzanie usługą Azure SQL Server i inspekcją bazy danych

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

**Polecenia cmdlet programu PowerShell (w tym obsługa dodatkowych funkcji filtrowania)**:

- [Tworzenie lub aktualizowanie zasad inspekcji bazy danych (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Pobierz zasady inspekcji bazy danych (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Pobierz zasady inspekcji serwera (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Usuń zasady inspekcji bazy danych (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Usuń zasady inspekcji serwera (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Aby zapoznać się z przykładem skryptu, zobacz [Konfigurowanie inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

### <a name="using-rest-api"></a>Korzystanie z interfejsu API REST

**interfejs API REST**:

- [Utwórz lub zaktualizuj zasady inspekcji bazy danych](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz zasady inspekcji bazy danych](/rest/api/sql/database%20auditing%20settings/get)
- [Pobierz zasady inspekcji serwera](/rest/api/sql/server%20auditing%20settings/get)

Rozszerzone zasady z klauzulą WHERE obsługują dodatkowe filtrowanie:

- [Utwórz lub zaktualizuj zasady *rozszerzonej* inspekcji bazy danych](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Tworzenie lub aktualizowanie zasad inspekcji *rozszerzonej* serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz zasady inspekcji *rozszerzonej* bazy danych](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Pobieranie zasad inspekcji *rozszerzonej* serwera](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-resource-manager-templates"></a>Używanie szablonów usługi Azure Resource Manager

Inspekcją usługi Azure SQL Database można zarządzać przy użyciu szablonów [Azure Resource Manager](../azure-resource-manager/management/overview.md) , jak pokazano w poniższych przykładach:

- [Wdrożenie SQL Server platformy Azure z włączoną inspekcją w celu zapisania dzienników inspekcji na koncie usługi Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Wdróż SQL Server platformy Azure z włączoną inspekcją w celu zapisania dzienników inspekcji do Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Wdróż SQL Server platformy Azure z włączoną inspekcją w celu zapisania dzienników inspekcji do Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Połączone przykłady znajdują się w zewnętrznym repozytorium publicznym i są dostarczane w postaci "AS IS", bez rękojmi i nie są obsługiwane w ramach żadnego programu lub usługi pomocy technicznej firmy Microsoft.
