---
title: Azure SQL inspekcji dla Azure SQL Database i Azure Synapse Analytics
description: Użyj Azure SQL Database inspekcji, aby śledzić zdarzenia bazy danych w dzienniku inspekcji.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/17/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: bc7ac6b97d10e5941e46b8be3e12baff32bded4a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483065"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Inspekcja Azure SQL Database i Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Inspekcja pod [Azure SQL Database](sql-database-paas-overview.md) i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage, w obszarze roboczym usługi Log Analytics lub Event Hubs.

Ponadto inspekcja:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

- Umożliwia i ułatwia przestrzeganie standardów zgodności, chociaż nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują standardy zgodności, zobacz witrynę [Centrum zaufania Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), w której można znaleźć aktualną listę certyfikatów zgodności usługi Azure SQL.

> [!NOTE]
> Aby uzyskać informacje Azure SQL Managed Instance inspekcji, zobacz następujący artykuł: Wprowadzenie do SQL Managed Instance [inspekcji.](../managed-instance/auditing-configure.md)

## <a name="overview"></a><a id="overview"></a>Omówienie

Możesz użyć SQL Database inspekcji, aby:

- **Zachowaj** dziennik inspekcji wybranych zdarzeń. Możesz zdefiniować kategorie akcji bazy danych, które mają być poddane inspekcji.
- **Raport** aktywności bazy danych. Możesz użyć wstępnie skonfigurowanych raportów i pulpitu nawigacyjnego, aby szybko rozpocząć pracę z raportowaniem aktywności i zdarzeń.
- **Analizowanie** raportów. Można znaleźć podejrzane zdarzenia, nietypową aktywność i trendy.

> [!IMPORTANT]
> Inspekcja Azure SQL Database i Azure Synapse jest zoptymalizowana pod kątem dostępności i wydajności. Podczas bardzo dużej aktywności lub wysokiego obciążenia sieci Azure SQL Database i Azure Synapse na kontynuowanie operacji i mogą nie rejestrować niektórych zdarzeń inspekcji.

### <a name="auditing-limitations"></a>Ograniczenia inspekcji

- **Usługa Premium Storage** nie **jest obecnie obsługiwana.**
- **Hierarchiczna przestrzeń nazw** **dla Azure Data Lake Storage Gen2 magazynu nie** jest obecnie **obsługiwana.**
- Włączanie inspekcji w wstrzymanym **Azure Synapse** nie jest obsługiwane. Aby włączyć inspekcję, wznów działanie usługi Azure Synapse.
- Inspekcja dla **Azure Synapse SQL obsługuje** tylko domyślne grupy akcji **inspekcji.**


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definiowanie zasad inspekcji na poziomie serwera i na poziomie bazy danych

Zasady inspekcji można zdefiniować dla określonej bazy [](logical-servers.md) danych lub jako domyślne zasady serwera na platformie Azure (które hostuje SQL Database lub Azure Synapse):

- Zasady serwera dotyczą wszystkich istniejących i nowo utworzonych baz danych na serwerze.

- Jeśli *inspekcja serwera jest włączona,* zawsze *ma zastosowanie do bazy danych*. Baza danych będzie podlegać inspekcji, niezależnie od ustawień inspekcji bazy danych.

- Jeśli zasady inspekcji są definiowane na poziomie bazy danych do obszaru roboczego usługi Log Analytics lub miejsca docelowego centrum zdarzeń, następujące operacje nie zachowają zasad inspekcji na poziomie źródłowej bazy danych:
    - [Kopia bazy danych](database-copy.md)
    - [Przywracanie do określonego momentu](recovery-using-backups.md)
    - [Replikacja geograficzna](active-geo-replication-overview.md) (pomocnicza baza danych nie będzie mieć inspekcji na poziomie bazy danych)

- Włączenie inspekcji bazy danych, oprócz włączenia jej na  serwerze, nie powoduje zastąpienia ani zmiany żadnych ustawień inspekcji serwera. Oba inspekcje będą istnieć obok siebie. Innymi słowy baza danych jest poddana inspekcji dwa razy równolegle. przez zasady serwera i raz przez zasady bazy danych.

   > [!NOTE]
   > Należy unikać włączania jednocześnie inspekcji serwerów i inspekcji obiektów blob bazy danych, chyba że:
    >
    > - Chcesz użyć innego konta magazynu, *okresu* *przechowywania* lub obszaru roboczego *usługi Log Analytics* dla określonej bazy danych.
    > - Chcesz inspekcji typów zdarzeń lub kategorii dla określonej bazy danych, które różnią się od pozostałych baz danych na serwerze. Na przykład możesz mieć wstawki tabeli, które muszą być poddana inspekcji tylko dla określonej bazy danych.
   >
   > W przeciwnym razie zalecamy włączenie tylko inspekcji na poziomie serwera i pozostawienie wyłączonej inspekcji na poziomie bazy danych dla wszystkich baz danych.

#### <a name="remarks"></a>Uwagi

- Dzienniki inspekcji są zapisywane w **uzupełnianych obiektach blob** w usłudze Azure Blob Storage w ramach subskrypcji platformy Azure
- Dzienniki inspekcji są w formacie łódki i można je otwierać za [SQL Server Management Studio pomocą programu SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms)
- Aby skonfigurować niezmienny magazyn dzienników dla zdarzeń inspekcji na poziomie serwera lub bazy danych, postępuj zgodnie z [instrukcjami dostarczonymi przez usługę Azure Storage.](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes) Upewnij się, że podczas **konfigurowania** niezmiennego magazynu obiektów blob wybrano opcję Zezwalaj na dodatkowe dołączania.
- Dzienniki inspekcji można zapisywać na koncie usługi Azure Storage za siecią wirtualną lub zaporą. Aby uzyskać szczegółowe instrukcje, zobacz Write audit to a storage account behind VNet and firewall (Zapis inspekcji na [koncie magazynu za siecią wirtualną i zaporą).](audit-write-storage-account-behind-vnet-firewall.md)
- Aby uzyskać szczegółowe informacje na temat formatu dziennika, hierarchii folderu magazynu i konwencji nazewnictwa, zobacz Odwołanie do formatu dziennika inspekcji [obiektów blob](./audit-log-format.md).
- Inspekcja [replik tylko do odczytu](read-scale-out.md) jest włączana automatycznie. Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [SQL Database format](audit-log-format.md)dziennika inspekcji .
- W przypadku korzystania z uwierzytelniania usługi Azure AD rekordy nieudanych logowań nie *będą wyświetlane* w dzienniku inspekcji SQL. Aby wyświetlić rekordy inspekcji nieudanych logowań, należy odwiedzić Azure Active Directory [portal](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), w którym są rejestrowane szczegóły tych zdarzeń.
- Nazwy logowania są kierowane przez bramę do określonego wystąpienia, w którym znajduje się baza danych.  W przypadku logowań usługi AAD poświadczenia są weryfikowane przed próbą użycia tego użytkownika zalogowania się do żądanej bazy danych.  W przypadku niepowodzenia żądana baza danych nigdy nie jest używana, więc Inspekcja nie jest przeprowadzana.  W przypadku identyfikatorów logowania SQL poświadczenia są weryfikowane na podstawie żądanych danych, więc w tym przypadku można je poddać inspekcji.  Pomyślne logowania, które oczywiście docierają do bazy danych, są poddawane inspekcji w obu przypadkach.
- Po skonfigurowaniu ustawień inspekcji można włączyć nową funkcję wykrywania zagrożeń i skonfigurować wiadomości e-mail do odbierania alertów zabezpieczeń. W przypadku korzystania z wykrywania zagrożeń otrzymujesz proaktywne alerty dotyczące anomalii działań bazy danych, które mogą wskazywać potencjalne zagrożenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do wykrywania zagrożeń.](threat-detection-overview.md)

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Konfigurowanie inspekcji dla serwera

Domyślne zasady inspekcji obejmują wszystkie akcje i następujący zestaw grup akcji, które będą inspekcji wszystkich zapytań i procedur składowanych wykonanych względem bazy danych, a także pomyślne i nieudane logowania:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
Inspekcję dla różnych typów akcji i grup akcji można skonfigurować przy użyciu programu PowerShell, zgodnie z opisem w sekcji Zarządzanie inspekcją SQL Database przy użyciu [Azure PowerShell](#manage-auditing) akcji.

Azure SQL Database i Azure Synapse Audit przechowuje 4000 znaków danych dla pól znaków w rekordzie inspekcji. Gdy  instrukcja lub  data_sensitivity_information zwrócone z akcji z możliwością inspekcji zawierają więcej niż 4000 znaków, wszystkie dane wykraczające poza pierwsze 4000 znaków zostaną obcięte i nie będą **podlegać** inspekcji .
W poniższej sekcji opisano konfigurację inspekcji przy użyciu Azure Portal.

  > [!NOTE]
  > - Włączenie inspekcji wstrzymanej dedykowanej puli SQL nie jest możliwe. Aby włączyć inspekcję, wstrzymaj dedykowaną pulę SQL. Dowiedz się więcej o [dedykowanej puli SQL.](../..//synapse-analytics/sql/best-practices-dedicated-sql-pool.md)
  > - Jeśli inspekcja jest skonfigurowana w obszarze roboczym usługi Log Analytics lub w miejscu docelowym centrum [](../../azure-monitor/essentials/diagnostic-settings.md) zdarzeń za pomocą polecenia cmdlet programu Azure Portal lub PowerShell, zostanie utworzone ustawienie diagnostyczne z włączoną kategorią "SQLSecurityAuditEvents".

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do **pozycji Inspekcja** w obszarze Zabezpieczenia w okienku **bazy danych SQL lub** serwera **SQL.**
3. Jeśli wolisz skonfigurować zasady inspekcji serwera, możesz wybrać link **Wyświetl** ustawienia serwera na stronie inspekcji bazy danych. Następnie można wyświetlić lub zmodyfikować ustawienia inspekcji serwera. Zasady inspekcji serwera mają zastosowanie do wszystkich istniejących i nowo utworzonych baz danych na tym serwerze.

    ![Zrzut ekranu przedstawiający link Wyświetl ustawienia serwera wyróżniony na stronie inspekcji bazy danych.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Jeśli wolisz włączyć inspekcję na poziomie bazy danych, przełącz **opcję Inspekcja na** **WŁ.** Jeśli inspekcja serwera jest włączona, inspekcja skonfigurowana w bazie danych będzie istnieć obok inspekcji serwera.

5. Istnieje wiele opcji konfigurowania miejsca, w którym będą zapisywane dzienniki inspekcji. Dzienniki można zapisywać na koncie usługi Azure Storage, w obszarze roboczym usługi Log Analytics do użycia przez Azure Monitor lub w centrum zdarzeń do użycia przy użyciu centrum zdarzeń. Można skonfigurować dowolną kombinację tych opcji, a dzienniki inspekcji będą zapisywane w każdej z nich.
  
   ![opcje magazynu](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Inspekcja operacji Pomoc techniczna Microsoft danych

Inspekcja operacji Pomoc techniczna Microsoft dla programu Azure SQL Server umożliwia inspekcję operacji inżynierów pomocy technicznej firmy Microsoft, gdy muszą oni uzyskać dostęp do serwera podczas żądania pomocy technicznej. Korzystanie z tej funkcji, a także inspekcja, zapewnia większą przejrzystość pracowników i umożliwia wykrywanie anomalii, wizualizację trendów i zapobieganie utracie danych.

Aby włączyć inspekcję operacji Pomoc techniczna Microsoft  przejdź do pozycji Inspekcja w obszarze Nagłówka zabezpieczeń w **okienku serwera Azure SQL** i przełącz opcję Inspekcja operacji pomocy technicznej firmy **Microsoft** na **pozycję WŁ.**

  > [!IMPORTANT]
  > Inspekcja operacji pomocy technicznej firmy Microsoft nie obsługuje miejsca docelowego konta magazynu. Aby włączyć tę możliwość, należy skonfigurować obszar roboczy usługi Log Analytics lub miejsce docelowe centrum zdarzeń.

![Zrzut ekranu przedstawiający operacje Pomoc techniczna Microsoft danych](./media/auditing-overview/support-operations.png)

Aby przejrzeć dzienniki inspekcji Pomoc techniczna Microsoft w obszarze roboczym usługi Log Analytics, użyj następującego zapytania:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Inspekcja w miejscu docelowym magazynu

Aby skonfigurować zapisywanie dzienników inspekcji na koncie magazynu, wybierz pozycję **Magazyn** po dojecheniu do **sekcji Inspekcja.** Wybierz konto usługi Azure Storage, na którym będą zapisywane dzienniki, a następnie wybierz okres przechowywania, otwierając **pozycję Właściwości zaawansowane.** Następnie kliknij przycisk **Zapisz**. Dzienniki starsze niż okres przechowywania są usuwane.

- Wartość domyślna okresu przechowywania to 0 (nieograniczone przechowywanie). Tę wartość można zmienić, przesuwając suwak **Okres przechowywania (dni)** w oknie Właściwości **zaawansowane** podczas konfigurowania konta magazynu do inspekcji.
  - Jeśli zmienisz okres przechowywania z 0 (nieograniczone przechowywanie) na dowolną inną wartość, pamiętaj, że okres przechowywania będzie miał zastosowanie tylko do dzienników zapisywanych po zmianie wartości przechowywania (dzienniki zapisane w okresie, w którym okres przechowywania został ustawiony na nieograniczone, zostaną zachowane, nawet po włączeniu przechowywania).

  ![konto magazynu](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Inspekcja w miejscu docelowym usługi Log Analytics
  
Aby skonfigurować zapisywanie dzienników inspekcji w obszarze roboczym usługi Log Analytics, wybierz pozycję **Log Analytics** i otwórz szczegóły usługi **Log Analytics.** Wybierz obszar roboczy usługi Log Analytics, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK.** Jeśli nie utworzono obszaru roboczego usługi Log Analytics, zobacz Tworzenie obszaru roboczego [usługi Log Analytics w Azure Portal](../../azure-monitor/logs/quick-create-workspace.md).

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Aby uzyskać więcej informacji na temat Azure Monitor obszaru roboczego usługi Log Analytics, zobacz [Projektowanie wdrożenia Azure Monitor Logs](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Inspekcja w miejscu docelowym centrum zdarzeń

Aby skonfigurować zapisywanie dzienników inspekcji w centrum zdarzeń, wybierz **pozycję Centrum zdarzeń**. Wybierz centrum zdarzeń, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **Zapisz.** Upewnij się, że centrum zdarzeń znajduje się w tym samym regionie co baza danych i serwer.

   ![EventHub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analizowanie dzienników inspekcji i raportów

Jeśli wybrano opcję zapisu dzienników inspekcji w Azure Monitor dziennikach:

- Użyj witryny [Azure Portal](https://portal.azure.com). Otwórz odpowiednią bazę danych. W górnej części strony Inspekcja **bazy** danych wybierz pozycję Wyświetl **dzienniki inspekcji.**

    ![wyświetlanie dzienników inspekcji](./media/auditing-overview/auditing-view-audit-logs.png)

- Następnie masz dwa sposoby wyświetlania dzienników:

    Kliknięcie pozycji **Log Analytics** w  górnej części strony Rekordy inspekcji spowoduje otwarcie widoku Dzienniki w obszarze roboczym usługi Log Analytics, w którym można dostosować zakres czasu i zapytanie wyszukiwania.

    ![otwieranie w obszarze roboczym usługi Log Analytics](./media/auditing-overview/auditing-log-analytics.png)

    Kliknięcie **pozycji Wyświetl pulpit**  nawigacyjny w górnej części strony Rekordy inspekcji spowoduje otwarcie pulpitu nawigacyjnego z informacjami o dziennikach inspekcji, na którym można przejść do szczegółowych informacji o zabezpieczeniach, dostępu do danych poufnych i nie tylko. Ten pulpit nawigacyjny został zaprojektowany w celu uzyskania szczegółowych informacji o zabezpieczeniach dotyczących danych.
    Możesz również dostosować zakres czasu i zapytanie wyszukiwania.
    ![Wyświetlanie pulpitu nawigacyjnego usługi Log Analytics](media/auditing-overview/auditing-view-dashboard.png)

    ![Pulpit nawigacyjny usługi Log Analytics](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Możesz również uzyskać dostęp do dzienników inspekcji z bloku Log Analytics. Otwórz obszar roboczy usługi Log Analytics i w **sekcji Ogólne** kliknij pozycję **Dzienniki**. Możesz rozpocząć od prostego zapytania, takiego jak: *wyszukiwanie "SQLSecurityAuditEvents",* aby wyświetlić dzienniki inspekcji.
    W tym miejscu można również używać [dzienników Azure Monitor do](../../azure-monitor/logs/log-query-overview.md)  uruchamiania zaawansowanych wyszukiwań danych dziennika inspekcji. Azure Monitor nawigacyjne dają wgląd w dane operacyjne w czasie rzeczywistym, korzystając ze zintegrowanego wyszukiwania i niestandardowych pulpitów nawigacyjnych, aby łatwo analizować miliony rekordów we wszystkich obciążeniach i serwerach. Aby uzyskać dodatkowe przydatne informacje na temat języka Azure Monitor i poleceń wyszukiwania dzienników, zobacz [Azure Monitor dzienniki wyszukiwania](../../azure-monitor/logs/log-query-overview.md).

Jeśli wybrano opcję zapisu dzienników inspekcji w centrum zdarzeń:

- Aby korzystać z danych dzienników inspekcji z centrum zdarzeń, należy skonfigurować strumień, aby korzystać ze zdarzeń i zapisywać je w celu. Aby uzyskać więcej informacji, [zobacz dokumentację Azure Event Hubs .](../index.yml)
- Dzienniki inspekcji w centrum zdarzeń są przechwytywane w treści zdarzeń [Apache Avro](https://avro.apache.org/) i przechowywane przy użyciu formatowania JSON z kodowaniem UTF-8. Aby odczytać dzienniki inspekcji, można użyć [narzędzi Avro](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) lub podobnych narzędzi, które przetwarzają ten format.

Jeśli wybierzesz opcję zapisu dzienników inspekcji na koncie usługi Azure Storage, istnieje kilka metod, których można użyć do wyświetlania dzienników:

- Dzienniki inspekcji są agregowane na koncie, które wybrano podczas instalacji. Dzienniki inspekcji można eksplorować za pomocą narzędzia, takiego [jak Eksplorator usługi Azure Storage](https://storageexplorer.com/). W usłudze Azure Storage dzienniki inspekcji są zapisywane jako kolekcja plików obiektów blob w kontenerze o nazwie **sqldbauditlogs.** Aby uzyskać więcej informacji na temat hierarchii folderów magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [SQL Database format](./audit-log-format.md)dziennika inspekcji .

- Użyj witryny [Azure Portal](https://portal.azure.com).  Otwórz odpowiednią bazę danych. W górnej części strony Inspekcja **bazy** danych kliknij pozycję Wyświetl **dzienniki inspekcji.**

    ![wyświetlanie dzienników inspekcji](./media/auditing-overview/auditing-view-audit-logs.png)

    **Zostaną otwarte** rekordy inspekcji, z których będzie można wyświetlać dzienniki.

  - Możesz wyświetlić określone daty, klikając pozycję **Filtruj** w **górnej części strony Rekordy** inspekcji.
  - Możesz przełączać się między rekordami  inspekcji utworzonymi  przez zasady inspekcji serwera i zasadami inspekcji bazy danych przez przełączenie źródła **inspekcji**.

       ![Zrzut ekranu przedstawiający opcje wyświetlania rekordów inspekcji.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Użyj funkcji **systemowej sys.fn_get_audit_file** (T-SQL), aby zwrócić dane dziennika inspekcji w formacie tabelaryki. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, [zobacz sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Użyj **scalania plików inspekcji** w programie SQL Server Management Studio (począwszy od programu SSMS 17):
    1. Z menu programu SSMS wybierz pozycję **Plik**  >  **Otwórz**  >  **scal pliki inspekcji.**

        ![Zrzut ekranu przedstawiający opcję menu Scal pliki inspekcji.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Zostanie **otwarte okno dialogowe Dodawanie** plików inspekcji. Wybierz jedną z opcji **Dodaj,** aby wybrać, czy scalić pliki inspekcji z dysku lokalnego, czy zaimportować je z usługi Azure Storage. Musisz podać szczegóły usługi Azure Storage i klucz konta.

    3. Po dodaniu wszystkich plików do scalenia kliknij przycisk **OK,** aby zakończyć operację scalania.

    4. Scalony plik zostanie otwarty w programie SSMS, w którym można go wyświetlać i analizować, a także eksportować do pliku SCAL lub CSV albo do tabeli.

- Użyj Power BI. Dane dziennika inspekcji można wyświetlać i analizować w Power BI. Aby uzyskać więcej informacji i uzyskać dostęp do szablonu do pobrania, zobacz [Analizowanie danych](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895)dziennika inspekcji w Power BI .
- Pobierz pliki dziennika z kontenera obiektów blob usługi Azure Storage za pośrednictwem portalu lub za pomocą narzędzia, takiego [jak Eksplorator usługi Azure Storage](https://storageexplorer.com/).
  - Po pobraniu lokalnego pliku dziennika kliknij go dwukrotnie, aby otworzyć, wyświetlić i przeanalizować dzienniki w programie SSMS.
  - Możesz również pobrać wiele plików jednocześnie za pośrednictwem Eksplorator usługi Azure Storage. W tym celu kliknij prawym przyciskiem myszy określony podfolder i wybierz polecenie **Zapisz** jako, aby zapisać w folderze lokalnym.

- Dodatkowe metody:

  - Po pobraniu kilku plików lub podfolderu zawierającego pliki dziennika można scalić je lokalnie, zgodnie z opisem w instrukcjach scalania plików inspekcji programu SSMS opisanych wcześniej.
  - Programowe wyświetlanie dzienników inspekcji obiektów blob: [wykonywanie zapytań o pliki zdarzeń rozszerzonych](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) przy użyciu programu PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Praktyki produkcyjne


### <a name="auditing-geo-replicated-databases"></a>Inspekcja baz danych replikowanych geograficznie

W przypadku baz danych replikowanych geograficznie po włączeniu inspekcji podstawowej bazy danych pomocnicza baza danych będzie mieć identyczne zasady inspekcji. Istnieje również możliwość skonfigurowania inspekcji pomocniczej bazy danych przez włączenie inspekcji na serwerze pomocniczym, niezależnie od podstawowej bazy danych.

- Na poziomie serwera **(** zalecane): włącz  inspekcję zarówno na  serwerze podstawowym, jak i pomocniczym — podstawowa i pomocnicza baza danych będzie podlegać inspekcji niezależnie na podstawie odpowiednich zasad na poziomie serwera.
- Poziom bazy danych: Inspekcję na poziomie bazy danych dla pomocniczych baz danych można skonfigurować tylko z poziomu ustawień inspekcji podstawowej bazy danych.
  - Inspekcja musi być włączona w samej *podstawowej bazie danych,* a nie na serwerze.
  - Po włączeniu inspekcji podstawowej bazy danych zostanie ona również włączona w pomocniczej bazie danych.

    > [!IMPORTANT]
    > W przypadku inspekcji na poziomie bazy danych ustawienia magazynu pomocniczej bazy danych będą identyczne z ustawieniami podstawowej bazy danych, co powoduje ruch między regionami. Zalecamy włączenie tylko inspekcji na poziomie serwera i pozostawienie wyłączonej inspekcji na poziomie bazy danych dla wszystkich baz danych.

### <a name="storage-key-regeneration"></a>Regenerowanie klucza magazynu

W środowisku produkcyjnym prawdopodobnie będziesz okresowo odświeżać klucze magazynu. Podczas zapisywania dzienników inspekcji w usłudze Azure Storage należy ponownie za pomocą zasad inspekcji odświeżyć klucze. Przebieg procesu:

1. Otwórz **okno Właściwości zaawansowane** w obszarze **Magazyn**. W polu **Klucz dostępu do magazynu** wybierz pozycję **Pomocniczy**. Następnie kliknij **przycisk Zapisz** w górnej części strony konfiguracji inspekcji.

    ![Zrzut ekranu przedstawiający proces wybierania pomocniczego klucza dostępu do magazynu.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Przejdź do strony konfiguracji magazynu i ponownie wygeneruj podstawowy klucz dostępu.

    ![Okienko nawigacji](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Wstecz na stronę konfiguracji inspekcji przełącz klucz dostępu magazynu z pomocniczego na podstawowy, a następnie kliknij przycisk **OK.** Następnie kliknij **przycisk Zapisz** w górnej części strony konfiguracji inspekcji.
4. Wstecz na stronę konfiguracji magazynu i ponownie wygeneruj pomocniczy klucz dostępu (w ramach przygotowania do następnego cyklu odświeżania klucza).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Zarządzanie Azure SQL Database inspekcji

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

**Polecenia cmdlet programu PowerShell (w tym obsługa klauzul WHERE w celu dodatkowego filtrowania):**

- [Tworzenie lub aktualizowanie zasad inspekcji bazy danych (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Pobierz zasady inspekcji bazy danych (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Pobierz zasady inspekcji serwera (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Usuwanie zasad inspekcji bazy danych (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Usuwanie zasad inspekcji serwera (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Aby uzyskać przykładowy skrypt, zobacz Configure auditing and threat detection using PowerShell (Konfigurowanie inspekcji i [wykrywania zagrożeń przy użyciu programu PowerShell).](scripts/auditing-threat-detection-powershell-configure.md)

### <a name="using-rest-api"></a>Korzystanie z interfejsu API REST

**Interfejs API REST:**

- [Tworzenie lub aktualizowanie zasad inspekcji bazy danych](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Tworzenie lub aktualizowanie zasad inspekcji serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz zasady inspekcji bazy danych](/rest/api/sql/database%20auditing%20settings/get)
- [Pobierz zasady inspekcji serwera](/rest/api/sql/server%20auditing%20settings/get)

Rozszerzone zasady z obsługą klauzuli WHERE w celu dodatkowego filtrowania:

- [Tworzenie lub aktualizowanie *rozszerzonych zasad* inspekcji bazy danych](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Tworzenie lub aktualizowanie *rozszerzonych zasad* inspekcji serwera](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz rozszerzone *zasady* inspekcji bazy danych](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Pobierz rozszerzone *zasady* inspekcji serwera](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

- [Zarządzanie zasadami inspekcji serwera](/cli/azure/sql/server/audit-policy)
- [Zarządzanie zasadami inspekcji bazy danych](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager

Inspekcją danych Azure SQL Database zarządzać przy [użyciu Azure Resource Manager](../../azure-resource-manager/management/overview.md) szablonów, jak pokazano w tych przykładach:

- [Wdrażanie serwera Azure SQL Database z włączoną inspekcją w celu zapisu dzienników inspekcji na koncie usługi Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Wdrażanie serwera Azure SQL Database z włączoną inspekcją w celu zapisu dzienników inspekcji do usługi Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Wdrażanie serwera Azure SQL Database z włączoną inspekcją w celu zapisu dzienników inspekcji w Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Połączone przykłady znajdują się w zewnętrznym publicznym repozytorium i są udostępniane w sposób "bez gwarancji" i nie są obsługiwane w żadnym programie/usłudze pomocy technicznej firmy Microsoft.
