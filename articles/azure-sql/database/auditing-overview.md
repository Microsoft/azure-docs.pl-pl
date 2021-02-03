---
title: Inspekcja usługi Azure SQL dla Azure SQL Database i usługi Azure Synapse Analytics
description: Użyj inspekcji Azure SQL Database do śledzenia zdarzeń bazy danych w dzienniku inspekcji.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/03/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: e96aa32c6b67290fc4b4ee62ae98c4e72399fd4b
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509591"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Inspekcja Azure SQL Database i usługi Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Inspekcja dla [Azure SQL Database](sql-database-paas-overview.md) i [usługi Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage, log Analytics obszarze roboczym lub Event Hubs.

Ponadto inspekcja:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

- Umożliwia i ułatwia przestrzeganie standardów zgodności, chociaż nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , w którym można znaleźć najbardziej aktualną listę certyfikatów zgodności z usługą Azure SQL.

> [!NOTE]
> Aby uzyskać informacje na temat inspekcji wystąpienia zarządzanego usługi Azure SQL, zobacz następujący artykuł: [wprowadzenie do inspekcji wystąpienia zarządzanego SQL](../managed-instance/auditing-configure.md).

## <a name="overview"></a><a id="overview"></a>Omówienie

Inspekcji SQL Database można użyć do:

- **Zachowaj** dziennik inspekcji dla wybranych zdarzeń. Możesz zdefiniować kategorie akcji bazy danych, które mają być poddane inspekcji.
- **Raport** dotyczący działania bazy danych. Możesz użyć wstępnie skonfigurowanych raportów i pulpitu nawigacyjnego, aby szybko rozpocząć pracę z raportowaniem aktywności i zdarzeń.
- **Analizuj** raporty. Można znaleźć podejrzane zdarzenia, nietypową aktywność i trendy.

> [!IMPORTANT]
> Inspekcja dla Azure SQL Database i usługi Azure Synapse jest zoptymalizowana pod kątem dostępności i wydajności. Podczas bardzo dużego działania lub dużego obciążenia sieciowego, Azure SQL Database i Azure Synapse umożliwiają wykonywanie operacji i może nie rejestrować niektórych zdarzeń poddawanych inspekcji.

### <a name="auditing-limitations"></a>Ograniczenia inspekcji

- Usługa **Premium Storage** nie jest obecnie **obsługiwana**.
- **Hierarchiczna przestrzeń nazw** dla **konta usługi Azure Data Lake Storage Gen2 Storage** nie jest obecnie **obsługiwana**.
- Włączanie inspekcji wstrzymanej **usługi Azure Synapse** nie jest obsługiwane. Aby włączyć inspekcję, Wznów działanie usługi Azure Synapse.

#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definiowanie zasad inspekcji na poziomie serwera i na poziomie bazy danych

Zasady inspekcji można zdefiniować dla konkretnej bazy danych lub jako domyślne zasady [serwera](logical-servers.md) na platformie Azure (które hosty SQL Database lub Azure Synapse):

- Zasady serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na serwerze.

- Jeśli *Inspekcja serwera jest włączona*, *zawsze ma zastosowanie do bazy danych programu*. Baza danych będzie poddawana inspekcji, niezależnie od ustawień inspekcji bazy danych.

- Włączenie inspekcji bazy danych, poza włączeniem jej na serwerze, *nie przesłania ani* nie zmienia żadnych ustawień inspekcji serwera. Obie inspekcje będą istnieć obok siebie. Inaczej mówiąc, baza danych jest monitorowana dwukrotnie. raz przez zasady serwera i jeden raz przez zasady bazy danych.

   > [!NOTE]
   > Należy unikać jednoczesnego włączania inspekcji serwera i inspekcji obiektów BLOB bazy danych, chyba że:
    >
    > - Chcesz użyć innego *konta magazynu*, *okresu przechowywania* lub *log Analytics obszaru roboczego* dla określonej bazy danych.
    > - Chcesz przeprowadzić inspekcję typów zdarzeń lub kategorii dla określonej bazy danych, która różni się od reszty baz danych na serwerze. Na przykład można mieć wstawienia tabeli, które muszą być poddane inspekcji tylko dla określonej bazy danych.
   >
   > W przeciwnym razie zalecamy włączenie tylko inspekcji na poziomie serwera i pozostawienie wyłączonej inspekcji na poziomie bazy danych dla wszystkich baz danych.

#### <a name="remarks"></a>Uwagi

- Dzienniki inspekcji są zapisywane w celu **dołączania obiektów BLOB** w usłudze Azure Blob Storage w ramach subskrypcji platformy Azure
- Dzienniki inspekcji są w formacie. XEL i mogą być otwierane za pomocą [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- Aby skonfigurować niezmienny magazyn dzienników dla zdarzeń inspekcji na poziomie serwera lub bazy danych, postępuj zgodnie z [instrukcjami dostarczonymi przez usługę Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). Upewnij się, że wybrano opcję **Zezwalaj na dodatkowe dołączenia** podczas konfigurowania niezmiennego magazynu obiektów BLOB.
- Dzienniki inspekcji można zapisywać na koncie usługi Azure Storage za siecią wirtualną lub zaporą. Aby uzyskać szczegółowe instrukcje, zobacz [Inspekcja zapisu na koncie magazynu za siecią wirtualną i zaporą](audit-write-storage-account-behind-vnet-firewall.md).
- Aby uzyskać szczegółowe informacje na temat formatu dziennika, hierarchii folderu magazynu i konwencji nazewnictwa, zobacz [dokumentacja formatu dziennika inspekcji obiektów BLOB](./audit-log-format.md).
- Inspekcja [replik tylko do odczytu](read-scale-out.md) jest włączana automatycznie. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [format dziennika inspekcji SQL Database](audit-log-format.md).
- W przypadku korzystania z uwierzytelniania usługi Azure AD rekordy nieudanych logowań *nie* będą widoczne w dzienniku inspekcji SQL. Aby wyświetlić nieudane rekordy inspekcji logowania, należy odwiedzić [portal Azure Active Directory](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), w którym znajdują się szczegóły dotyczące tych zdarzeń.
- Nazwy logowania są kierowane przez bramę do określonego wystąpienia, w którym znajduje się baza danych.  W przypadku logowań usługi AAD poświadczenia są weryfikowane przed próbą użycia tego użytkownika do zalogowania się do żądanej bazy danych.  W przypadku awarii żądana baza danych nigdy nie jest używana, więc Inspekcja nie jest przeprowadzana.  W przypadku nazw logowania SQL poświadczenia są weryfikowane na żądanych danych, więc w tym przypadku mogą być poddane inspekcji.  Pomyślne logowania, które oczywiście docierają do bazy danych, są poddawane inspekcji w obu przypadkach.
- Po skonfigurowaniu ustawień inspekcji można włączyć nową funkcję wykrywania zagrożeń i skonfigurować wiadomości e-mail w celu otrzymywania alertów zabezpieczeń. W przypadku korzystania z wykrywania zagrożeń otrzymywane są aktywne alerty dotyczące nietypowych działań bazy danych, które mogą wskazywać na potencjalne zagrożenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [wprowadzenie do wykrywania zagrożeń](threat-detection-overview.md).

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Skonfiguruj inspekcję serwera

Domyślne zasady inspekcji obejmują wszystkie akcje i następujący zestaw grup akcji, który przeprowadzi inspekcję wszystkich zapytań i procedur składowanych wykonywanych w bazie danych, jak również pomyślnie i nieudanych logowań:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
Inspekcję dla różnych typów akcji i grup akcji można skonfigurować przy użyciu programu PowerShell, zgodnie z opisem w sekcji [Zarządzanie inspekcją SQL Database przy użyciu Azure PowerShell](#manage-auditing) .

Azure SQL Database i usługa Azure Synapse Audit przechowuje 4000 znaków danych dla pól znaków w rekordzie inspekcji. Gdy **instrukcja** lub **data_sensitivity_information** wartości zwracane z akcji objętej inspekcją zawierają więcej niż 4000 znaków, wszystkie dane spoza pierwszych 4000 znaków będą **obcinane i nieobjęte inspekcją**.
W poniższej sekcji opisano konfigurację inspekcji przy użyciu Azure Portal.

  > [!NOTE]
  > Włączanie inspekcji wstrzymanej dedykowanej puli SQL nie jest możliwe. Aby włączyć inspekcję, Cofnij wstrzymanie dedykowanej puli SQL. Dowiedz się więcej na temat [dedykowanej puli SQL](../..//synapse-analytics/sql/best-practices-sql-pool.md).

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do opcji **Inspekcja** pod nagłówkiem zabezpieczenia w okienku **bazy danych SQL** lub **SQL Server** .
3. Jeśli wolisz skonfigurować zasady inspekcji serwera, możesz wybrać łącze **Wyświetl ustawienia serwera** na stronie Inspekcja bazy danych. Następnie można wyświetlić lub zmodyfikować ustawienia inspekcji serwera. Zasady inspekcji serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na tym serwerze.

    ![Zrzut ekranu pokazujący link Wyświetl ustawienia serwera wyróżniony na stronie Inspekcja bazy danych.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Jeśli wolisz włączyć inspekcję na poziomie bazy danych, przełącz **inspekcję** na wartość **włączone**. Jeśli Inspekcja serwera jest włączona, inspekcja skonfigurowana dla bazy danych będzie istnieć równolegle z inspekcją serwera.

5. Istnieje wiele opcji konfigurowania, w których będą zapisywane dzienniki inspekcji. Dzienniki można zapisywać na koncie usługi Azure Storage, w obszarze roboczym Log Analytics do użycia przez dzienniki Azure Monitor (wersja zapoznawcza) lub do centrum zdarzeń w celu użycia przy użyciu centrum zdarzeń (wersja zapoznawcza). Można skonfigurować dowolną kombinację tych opcji, a dzienniki inspekcji będą zapisywane w każdym z nich.
  
   ![Opcje magazynu](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations-preview"></a><a id="auditing-of-microsoft-support-operations"></a>Inspekcja operacji pomoc techniczna firmy Microsoft (wersja zapoznawcza)

Inspekcja operacji pomoc techniczna firmy Microsoft (wersja zapoznawcza) dla usługi Azure SQL Server umożliwia inspekcję operacji inżynierów pomocy technicznej firmy Microsoft, gdy potrzebują oni uzyskać dostęp do serwera w trakcie żądania obsługi. Korzystanie z tej funkcji, wraz z inspekcją, umożliwia większej przejrzystości pracowników i pozwala na wykrywanie anomalii, wizualizację trendu i Zapobieganie utracie danych.

Aby włączyć inspekcję operacji pomoc techniczna firmy Microsoft (wersja zapoznawcza), przejdź do pozycji **Inspekcja** w obszarze zabezpieczenia w okienku **programu Azure SQL Server** i Przełącz **inspekcję operacji pomocy technicznej firmy Microsoft (wersja zapoznawcza)** na wartość **włączone**.

  > [!IMPORTANT]
  > Inspekcja operacji pomocy technicznej firmy Microsoft (wersja zapoznawcza) nie obsługuje miejsca docelowego konta magazynu. Aby włączyć tę funkcję, należy skonfigurować obszar roboczy Log Analytics lub miejsce docelowe centrum zdarzeń.

![Zrzut ekranu przedstawiający operacje pomoc techniczna firmy Microsoft](./media/auditing-overview/support-operations.png)

Aby przejrzeć dzienniki inspekcji pomoc techniczna firmy Microsoft operacji w obszarze roboczym Log Analytics, użyj następującego zapytania:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Inspekcja w miejscu docelowym magazynu

Aby skonfigurować zapisywanie dzienników inspekcji na koncie magazynu, wybierz pozycję **Magazyn** i Otwórz **szczegóły magazynu**. Wybierz konto usługi Azure Storage, na którym będą zapisywane dzienniki, a następnie wybierz okres przechowywania. Następnie kliknij przycisk **OK**. Dzienniki starsze niż okres przechowywania są usuwane.

- Wartość domyślna okresu przechowywania to 0 (nieograniczony czas przechowywania). Tę wartość można zmienić, przesuwając suwak **przechowywanie (dni)** w **ustawieniach magazynu** podczas konfigurowania konta magazynu na potrzeby inspekcji.
  - Jeśli zmienisz okres przechowywania z 0 (nieograniczony czas przechowywania) na inną wartość, należy pamiętać, że przechowywanie będzie dotyczyło tylko dzienników utworzonych po zmianie wartości przechowywania (dzienniki zapisane w okresie, gdy czas przechowywania był ustawiony na nieograniczony, nawet po włączeniu przechowywania).

  ![konto magazynu](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Inspekcja w celu Log Analytics lokalizacji docelowej
  
Aby skonfigurować zapisywanie dzienników inspekcji do obszaru roboczego Log Analytics, wybierz pozycję **log Analytics (wersja zapoznawcza)** i Otwórz **log Analytics szczegóły**. Wybierz lub Utwórz obszar roboczy Log Analytics, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**.

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Aby uzyskać więcej informacji na temat Azure Monitor Log Analytics obszarze roboczym, zobacz [projektowanie dzienników Azure monitor Deployment](../../azure-monitor/platform/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Inspekcja w miejscu docelowym centrum zdarzeń

Aby skonfigurować zapisywanie dzienników inspekcji do centrum zdarzeń, wybierz pozycję **centrum zdarzeń (wersja zapoznawcza)** i Otwórz **szczegóły centrum zdarzeń**. Wybierz centrum zdarzeń, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**. Upewnij się, że centrum zdarzeń znajduje się w tym samym regionie, w którym znajduje się baza danych i serwer.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analizowanie dzienników i raportów inspekcji

W przypadku wybrania opcji zapisania dzienników inspekcji do Azure Monitor dzienników:

- Użyj witryny [Azure Portal](https://portal.azure.com). Otwórz odpowiednią bazę danych. W górnej części strony **Inspekcja** bazy danych wybierz opcję **Wyświetl dzienniki inspekcji**.

    ![Wyświetlanie dzienników inspekcji](./media/auditing-overview/auditing-view-audit-logs.png)

- Następnie masz dwa sposoby wyświetlania dzienników:

    Kliknięcie **log Analytics** w górnej części strony **rekordy inspekcji** spowoduje otwarcie widoku dzienniki w obszarze roboczym log Analytics, gdzie można dostosować zakres czasu i zapytanie wyszukiwania.

    ![Otwórz w obszarze roboczym Log Analytics](./media/auditing-overview/auditing-log-analytics.png)

    Kliknięcie przycisku **Wyświetl pulpit nawigacyjny** w górnej części strony **rekordy inspekcji** spowoduje otwarcie pulpitu nawigacyjnego wyświetlającego informacje o dziennikach inspekcji, w którym można przejść do szczegółowych informacji o zabezpieczeniach, uzyskać dostęp do poufnych danych i nie tylko. Ten pulpit nawigacyjny umożliwia uzyskanie szczegółowych informacji o zabezpieczeniach danych.
    Możesz również dostosować zakres czasu i zapytanie wyszukiwania.
    ![Wyświetl pulpit nawigacyjny Log Analytics](media/auditing-overview/auditing-view-dashboard.png)

    ![Pulpit nawigacyjny Log Analytics](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics szczegółowe informacje o zabezpieczeniach](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Alternatywnie można również uzyskać dostęp do dzienników inspekcji w bloku Log Analytics. Otwórz obszar roboczy Log Analytics i w sekcji **Ogólne** kliknij pozycję **dzienniki**. Możesz zacząć od prostego zapytania, takiego jak: *Search "SQLSecurityAuditEvents"* , aby wyświetlić dzienniki inspekcji.
    W tym miejscu możesz również użyć [dzienników Azure monitor](../../azure-monitor/log-query/log-query-overview.md)  , aby uruchomić zaawansowane wyszukiwania w danych dziennika inspekcji. Dzienniki Azure Monitor udostępniają usługi operacyjne w czasie rzeczywistym przy użyciu zintegrowanego wyszukiwania i niestandardowych pulpitów nawigacyjnych, które umożliwiają łatwe analizowanie milionów rekordów na wszystkich obciążeniach i serwerach. Aby uzyskać dodatkowe informacje dotyczące Azure Monitor języka i poleceń wyszukiwania dzienników, zobacz artykuł [Azure monitor Logs Search Reference](../../azure-monitor/log-query/log-query-overview.md).

W przypadku wybrania opcji zapisania dzienników inspekcji do centrum zdarzeń:

- Aby korzystać z danych inspekcji dzienników z centrum zdarzeń, należy skonfigurować strumień, który będzie korzystał z zdarzeń i zapisywać je w celu. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Event Hubs](../index.yml).
- Dzienniki inspekcji w centrum zdarzeń są przechwytywane w treści zdarzeń [Apache Avro](https://avro.apache.org/) i przechowywane przy użyciu formatowania JSON przy użyciu kodowania UTF-8. Aby odczytać dzienniki inspekcji, można użyć [narzędzi Avro](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) lub podobnych narzędzi, które przetwarzają ten format.

W przypadku wybrania opcji zapisania dzienników inspekcji na koncie usługi Azure Storage istnieje kilka metod wyświetlania dzienników:

- Dzienniki inspekcji są agregowane na koncie wybranym podczas instalacji. Dzienniki inspekcji można eksplorować przy użyciu narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/). W usłudze Azure Storage dzienniki inspekcji są zapisywane jako kolekcja plików obiektów BLOB w kontenerze o nazwie **sqldbauditlogs**. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [format dziennika inspekcji SQL Database](./audit-log-format.md).

- Użyj witryny [Azure Portal](https://portal.azure.com).  Otwórz odpowiednią bazę danych. W górnej części strony **Inspekcja** bazy danych kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Zrzut ekranu pokazujący przycisk Wyświetl dzienniki inspekcji wyróżniony na stronie Inspekcja bazy danych.](./media/auditing-overview/7_auditing_get_started_blob_view_audit_logs.png)

    **Rekordy inspekcji** są otwierane, z poziomu którego będziesz mieć możliwość wyświetlania dzienników.

  - Aby wyświetlić określone daty, kliknij pozycję **Filtruj** w górnej części strony **Rejestrowanie inspekcji** .
  - Można przełączać się między rekordami inspekcji, które zostały utworzone przez *zasady inspekcji serwera* i *zasad inspekcji bazy danych* przez przełączenie **źródła inspekcji**.
  - Można wyświetlić tylko rekordy inspekcji powiązane z iniekcją SQL, zaznaczając pole wyboru  **Pokaż tylko rekordy inspekcji dla iniekcji kodu SQL** .

       ![Zrzut ekranu przedstawiający opcje wyświetlania rekordów inspekcji.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Użyj funkcji systemowej **sys.fn_get_audit_file** (T-SQL), aby zwrócić dane dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Użyj **plików inspekcji scalania** w SQL Server Management Studio (począwszy od programu SSMS 17):
    1. Z menu programu SSMS wybierz pozycję **plik**  >  **Otwórz**  >  **pliki inspekcji scalania**.

        ![Zrzut ekranu pokazujący opcję menu Scal pliki inspekcji.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Zostanie otwarte okno dialogowe **Dodawanie plików inspekcji** . Wybierz jedną z opcji **dodawania** , aby wybrać, czy pliki inspekcji mają być scalane z dysku lokalnego, czy też zaimportować je z usługi Azure Storage. Musisz podać szczegóły i klucz konta usługi Azure Storage.

    3. Po dodaniu wszystkich plików do scalenia kliknij przycisk **OK** , aby ukończyć operację scalania.

    4. Scalony plik zostanie otwarty w programie SSMS, gdzie można go wyświetlić i przeanalizować, a także wyeksportować do pliku XEL lub CSV lub do tabeli.

- Użyj Power BI. Można wyświetlać i analizować dane dziennika inspekcji w Power BI. Aby uzyskać więcej informacji i uzyskać dostęp do możliwego do pobrania szablonu, zobacz [Analizowanie danych dziennika inspekcji w Power BI](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895).
- Pobierz pliki dziennika z kontenera obiektów BLOB usługi Azure Storage za pośrednictwem portalu lub za pomocą narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).
  - Po pobraniu pliku dziennika lokalnie kliknij dwukrotnie plik, aby otworzyć, wyświetlić i analizować dzienniki w programie SSMS.
  - Możesz również pobrać wiele plików jednocześnie za pośrednictwem Eksplorator usługi Azure Storage. Aby to zrobić, kliknij prawym przyciskiem myszy określony podfolder i wybierz polecenie **Zapisz jako** do zapisania w folderze lokalnym.

- Dodatkowe metody:

  - Po pobraniu kilku plików lub podfolderu, który zawiera pliki dziennika, można scalić je lokalnie zgodnie z opisem w artykule informacje o plikach inspekcji scalania programu SSMS opisane wcześniej.
  - Programowe Wyświetlanie dzienników inspekcji obiektów blob: [wykonywanie zapytań o pliki zdarzeń rozszerzonych](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) za pomocą programu PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Praktyki produkcyjne


### <a name="auditing-geo-replicated-databases"></a>Inspekcja replikowanych geograficznie baz danych

Po włączeniu inspekcji podstawowej bazy danych przy użyciu baz danych z replikacją geograficzną pomocnicza baza danych będzie miała identyczne zasady inspekcji. Istnieje również możliwość skonfigurowania inspekcji pomocniczej bazy danych przez włączenie inspekcji na **serwerze pomocniczym** niezależnie od podstawowej bazy danych.

- Poziom serwera (**zalecane**): Włącz inspekcję zarówno na **serwerze podstawowym** , jak i na **serwerze pomocniczym** — podstawowe i pomocnicze bazy danych zostaną poddane inspekcji niezależnie od ich odpowiednich zasad na poziomie serwera.
- Poziom bazy danych: Inspekcja na poziomie bazy danych dla pomocniczych baz danych można skonfigurować tylko przy użyciu ustawień inspekcji podstawowej bazy danych.
  - Inspekcja musi być włączona w *podstawowej bazie danych*, a nie na serwerze.
  - Po włączeniu inspekcji w podstawowej bazie danych zostanie ona również włączona w pomocniczej bazie danych.

    > [!IMPORTANT]
    > W przypadku inspekcji na poziomie bazy danych ustawienia magazynu dla pomocniczej bazy danych będą takie same, jak w przypadku ruchu międzyregionalnego. Zalecamy włączenie tylko inspekcji na poziomie serwera i pozostawienie wyłączonej inspekcji na poziomie bazy danych dla wszystkich baz danych.

### <a name="storage-key-regeneration"></a>Ponowne generowanie klucza magazynu

W środowisku produkcyjnym można okresowo odświeżać klucze magazynu. Podczas zapisywania dzienników inspekcji w usłudze Azure Storage należy ponownie zapisać zasady inspekcji podczas odświeżania kluczy. Przebieg procesu:

1. Otwórz **szczegóły magazynu**. W polu **klucz dostępu do magazynu** wybierz pozycję **pomocniczy**, a następnie kliknij przycisk **OK**. Następnie kliknij pozycję **Zapisz** w górnej części strony Konfiguracja inspekcji.

    ![Zrzut ekranu pokazujący proces wybierania klucza dostępu do magazynu pomocniczego.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Przejdź do strony Konfiguracja magazynu i ponownie Wygeneruj podstawowy klucz dostępu.

    ![Okienko nawigacji](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Wróć do strony Konfiguracja inspekcji, przełącz klucz dostępu do magazynu z pomocnicza na podstawowy, a następnie kliknij przycisk **OK**. Następnie kliknij pozycję **Zapisz** w górnej części strony Konfiguracja inspekcji.
4. Wróć do strony Konfiguracja magazynu i Wygeneruj ponownie pomocniczy klucz dostępu (w przygotowaniu dla cyklu odświeżania następnego klucza).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Zarządzanie inspekcją Azure SQL Database

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

**Polecenia cmdlet programu PowerShell (w tym obsługa dodatkowych funkcji filtrowania)**:

- [Tworzenie lub aktualizowanie zasad inspekcji bazy danych (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Pobierz zasady inspekcji bazy danych (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Pobierz zasady inspekcji serwera (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Usuń zasady inspekcji bazy danych (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Usuń zasady inspekcji serwera (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Aby zapoznać się z przykładem skryptu, zobacz [Konfigurowanie inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

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

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

- [Zarządzanie zasadami inspekcji serwera](/cli/azure/sql/server/audit-policy?view=azure-cli-latest)
- [Zarządzanie zasadami inspekcji bazy danych](/cli/azure/sql/db/audit-policy?view=azure-cli-latest)

### <a name="using-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager

Inspekcją Azure SQL Database można zarządzać przy użyciu szablonów [Azure Resource Manager](../../azure-resource-manager/management/overview.md) , jak pokazano w poniższych przykładach:

- [Wdrożenie Azure SQL Database z włączoną inspekcją w celu zapisania dzienników inspekcji na koncie usługi Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Wdróż Azure SQL Database z włączoną inspekcją w celu zapisania dzienników inspekcji do Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Wdróż Azure SQL Database z włączoną inspekcją w celu zapisania dzienników inspekcji do Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Połączone przykłady znajdują się w zewnętrznym repozytorium publicznym i są dostarczane w postaci "AS IS", bez rękojmi i nie są obsługiwane w ramach żadnego programu lub usługi pomocy technicznej firmy Microsoft.
