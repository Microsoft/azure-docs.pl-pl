---
title: Rozwiązywanie problemów z wykonywaniem pakietów w środowisku SSIS Integration Runtime
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z wykonaniem pakietu SSIS w środowisku SSIS Integration Runtime
ms.service: data-factory
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 2bc56d39de392c9e4c20c25b554e3bdeea048bfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361880"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Rozwiązywanie problemów z wykonywaniem pakietów w środowisku SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten artykuł zawiera najczęstsze błędy, które można napotkać podczas wykonywania pakietów SQL Server Integration Services (SSIS) w programie SSIS Integration Runtime. Opisuje potencjalne przyczyny i akcje rozwiązywania błędów.

## <a name="where-to-find-logs-for-troubleshooting"></a>Gdzie można znaleźć dzienniki do rozwiązywania problemów

Użyj portalu Azure Data Factory, aby sprawdzić dane wyjściowe działania wykonywania pakietu SSIS. Dane wyjściowe obejmują wyniki wykonywania, komunikaty o błędach i identyfikator operacji. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie potoku](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Użyj wykazu usług SSIS (SSISDB), aby sprawdzić dzienniki szczegółów wykonania. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie uruchomionych pakietów i innych operacji](/sql/integration-services/performance/monitor-running-packages-and-other-operations).

## <a name="common-errors-causes-and-solutions"></a>Typowe błędy, przyczyny i rozwiązania

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Komunikat o błędzie: "Upłynął limit czasu połączenia" lub "usługa napotkała błąd podczas przetwarzania żądania. Spróbuj ponownie”.

Oto potencjalne przyczyny i zalecane akcje:
* Źródło danych lub miejsce docelowe są przeciążone. Sprawdź obciążenie źródła danych lub miejsca docelowego i zobacz, czy ma ono wystarczającą pojemność. Na przykład jeśli używasz Azure SQL Database, rozważ skalowanie w górę, jeśli baza danych prawdopodobnie przekroczy limit czasu.
* Sieć między środowiskiem programu SSIS Integration Runtime a źródłem danych lub miejscem docelowym jest niestabilna, szczególnie w przypadku, gdy połączenie jest w wielu regionach lub między środowiskiem lokalnym i platformą Azure. Zastosuj wzorzec ponawiania prób w pakiecie SSIS, wykonując następujące czynności:
  * Upewnij się, że pakiety SSIS mogą być ponownie uruchamiane po awarii bez efektów ubocznych (na przykład utrata danych lub duplikowanie danych).
  * Skonfiguruj **ponowną próbę** i **interwał ponawiania próby** **wykonania pakietu SSIS** na karcie **Ogólne** . ![ Ustawianie właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * W przypadku składnika ADO.NET i źródła OLE DB lub docelowego ustaw wartość **ConnectRetryCount** i **ConnectRetryInterval** w Menedżerze połączeń w ramach pakietu SSIS lub działania SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Komunikat o błędzie: "Źródło sieci ADO nie może uzyskać połączenia"... " podczas ustanawiania połączenia z usługą SQL Server Wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został odnaleziony lub był niedostępny.

Ten problem zwykle oznacza, że źródło danych lub miejsce docelowe są niedostępne z poziomu środowiska SSIS Integration Runtime. Przyczyny mogą być różne. Wypróbuj następujące czynności:
* Upewnij się, że źródło danych lub nazwa/adres IP zostały prawidłowo przeszukane.
* Upewnij się, że Zapora jest prawidłowo ustawiona.
* Upewnij się, że sieć wirtualna jest prawidłowo skonfigurowana, jeśli źródło danych lub miejsce docelowe są lokalne:
  * Możesz sprawdzić, czy problem pochodzi z konfiguracji sieci wirtualnej, udostępniając MASZYNę wirtualną platformy Azure w tej samej sieci wirtualnej. Następnie sprawdź, czy można uzyskać dostęp do źródła danych lub miejsca docelowego z maszyny wirtualnej platformy Azure.
  * Więcej szczegółów na temat używania sieci wirtualnej z usługą SSIS Integration runtime można znaleźć w [przyłączeniu do sieci wirtualnej środowiska Azure-SSIS Integration Runtime](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Komunikat o błędzie: "Źródło sieci ADO nie może uzyskać połączenia"... " za pomocą "nie można utworzyć zarządzanego Menedżera połączeń".

Potencjalną przyczyną jest to, że dostawca ADO.NET używany w pakiecie nie jest zainstalowany w środowisku SSIS Integration Runtime. Dostawcę można zainstalować przy użyciu konfiguracji niestandardowej. Więcej informacji na temat konfiguracji niestandardowej można znaleźć w temacie [Dostosowywanie konfiguracji dla środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Komunikat o błędzie: "połączenie"... " nie znaleziono "

Ten błąd może powodować znany problem w starszych wersjach programu SQL Server Management Studio (SSMS). Jeśli pakiet zawiera składnik niestandardowy (na przykład dodatek SSIS Azure Feature Pack lub składniki partnera), który nie jest zainstalowany na maszynie, na której przeprowadzane jest wdrożenie za pomocą programu SSMS, program SSMS usunie ten składnik i spowoduje wystąpienie błędu. Uaktualnij narzędzie [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) do najnowszej wersji o rozwiązanym problemie.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Komunikat o błędzie: "kod zakończenia modułu SSIS:-1073741819".

* Potencjalna przyczyna i zalecana akcja:
  * Przyczyną tego błędu może być ograniczenie źródłowej i docelowej programu Excel, gdy wiele źródeł lub miejsc docelowych programu Excel jest wykonywanych równolegle w wielu wątkach. To ograniczenie można obejść przez zmianę składników programu Excel na wykonywanie w sekwencji lub oddzielenie ich na różne pakiety i wyzwalacze za pomocą właściwości ExecuteOutOfProcess o wartości true.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Komunikat o błędzie: "za mało miejsca na dysku"

Ten błąd oznacza, że dysk lokalny jest używany w węźle SSIS Integration Runtime. Sprawdź, czy pakiet lub Konfiguracja niestandardowa zużywa dużo miejsca na dysku:
* Jeśli dysk jest zużywany przez pakiet, zostanie zwolniony po zakończeniu wykonywania pakietu.
* Jeśli dysk jest używany przez konfigurację niestandardową, należy zatrzymać program SSIS Integration Runtime, zmodyfikować skrypt i ponownie uruchomić środowisko Integration Runtime. Cały kontener obiektów blob platformy Azure określony dla konfiguracji niestandardowej zostanie skopiowany do węzła SSIS Integration Runtime, dlatego sprawdź, czy w tym kontenerze nie ma żadnej niepotrzebnej zawartości.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Komunikat o błędzie: "nie można pobrać zasobu z serwera głównego. Microsoft. SqlServer. IntegrationServices. Scale. ScaleoutContract. Common. MasterResponseFailedException: code: 300004. Opis: nie można załadować pliku "* * *".

* Potencjalna przyczyna i zalecana akcja:
  * Jeśli działanie programu SSIS wykonuje pakiet z systemu plików (plik pakietu lub plik projektu), ten błąd wystąpi, jeśli projekt, pakiet lub plik konfiguracji nie będą dostępne z poświadczeniami dostępu do pakietu dostarczonymi w działaniu usług SSIS.
    * Jeśli używasz usługi Azure File:
      * Ścieżka pliku powinna rozpoczynać się od \\ \\ \<storage account name\> . File.Core.Windows.NET\\\<file share path\>
      * Domena powinna mieć wartość "Azure"
      * Nazwa użytkownika powinna mieć wartość \<storage account name\>
      * Hasło powinno być \<storage access key\>
    * Jeśli używasz pliku lokalnego, sprawdź, czy sieć wirtualna, poświadczenia dostępu do pakietu i uprawnienia są prawidłowo skonfigurowane, aby środowisko Azure-SSIS Integration Runtime miało dostęp do lokalnego udziału plików

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Komunikat o błędzie: "nazwa pliku"... " określone w połączeniu nie są prawidłowe "

* Potencjalna przyczyna i zalecana akcja:
  * Określono nieprawidłową nazwę pliku
  * Upewnij się, że używasz nazwy FQDN (w pełni kwalifikowanej nazwy domeny) zamiast godziny krótkiej w Menedżerze połączeń

### <a name="error-message-cannot-open-file-"></a>Komunikat o błędzie: "nie można otworzyć pliku"... "

Ten błąd występuje, gdy wykonanie pakietu nie może znaleźć pliku na dysku lokalnym w programie SSIS Integration Runtime. Wypróbuj następujące czynności:
* Nie używaj ścieżki bezwzględnej w pakiecie, który jest wykonywany w programie SSIS Integration Runtime. Użyj bieżącego katalogu roboczego wykonywania (.) lub folderu tymczasowego (% TEMP%) INSTEAD.
* Aby zachować niektóre pliki w węzłach SSIS Integration Runtime, przygotuj pliki zgodnie z opisem w temacie [Dostosowywanie Instalatora](how-to-configure-azure-ssis-ir-custom-setup.md). Wszystkie pliki w katalogu roboczym zostaną oczyszczone po zakończeniu wykonywania.
* Użyj Azure Files zamiast przechowywania pliku w węźle SSIS Integration Runtime. Aby uzyskać szczegółowe informacje, zobacz [Korzystanie z udziałów plików platformy Azure](/sql/integration-services/lift-shift/ssis-azure-files-file-shares#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Komunikat o błędzie: "w bazie danych" SSISDB "Osiągnięto limit przydziału rozmiaru"

Potencjalną przyczyną jest to, że baza danych SSISDB utworzona w Azure SQL Database lub w wystąpieniu zarządzanym SQL osiągnął swój przydział. Wypróbuj następujące czynności:
* Rozważ zwiększenie liczby jednostek DTU bazy danych. Szczegółowe informacje można znaleźć w [limitach SQL Database dla serwera logicznego](../azure-sql/database/resource-limits-logical-server.md).
* Sprawdź, czy pakiet wygeneruje wiele dzienników. Jeśli tak, możesz skonfigurować zadanie elastyczne, które oczyści te dzienniki. Aby uzyskać szczegółowe informacje, zobacz [Oczyszczanie dzienników SSISDB za pomocą zadań Azure Elastic Database](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Komunikat o błędzie: "limit żądań dla bazy danych to... i został osiągnięty. "

Jeśli wiele pakietów jest uruchomionych równolegle w programie SSIS Integration Runtime, ten błąd może wystąpić, ponieważ SSISDB osiągnął limit żądań. Aby rozwiązać ten problem, należy rozważyć zwiększenie poziomu usługi DTC SSISDB. Szczegółowe informacje można znaleźć w [limitach SQL Database dla serwera logicznego](../azure-sql/database/resource-limits-logical-server.md).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Komunikat o błędzie: "operacja SSIS nie powiodła się z powodu nieoczekiwanego stanu operacji:..."

Ten błąd jest głównie spowodowany przez przejściowy problem, dlatego spróbuj ponownie uruchomić wykonywanie pakietu. Zastosuj wzorzec ponawiania prób w pakiecie SSIS, wykonując następujące czynności:

* Upewnij się, że pakiety SSIS mogą być ponownie uruchamiane po awarii bez efektów ubocznych (na przykład utrata danych lub duplikowanie danych).
* Skonfiguruj **ponowną próbę** i **interwał ponawiania próby** **wykonania pakietu SSIS** na karcie **Ogólne** . ![ Ustawianie właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* W przypadku składnika ADO.NET i źródła OLE DB lub docelowego ustaw wartość **ConnectRetryCount** i **ConnectRetryInterval** w Menedżerze połączeń w ramach pakietu SSIS lub działania SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Komunikat o błędzie: "nie istnieje aktywny proces roboczy".

Ten błąd zazwyczaj oznacza, że środowisko SSIS Integration Runtime ma stan złej kondycji. Sprawdź Azure Portal stanu i szczegółowe błędy. Aby uzyskać więcej informacji, zobacz [Azure-SSIS Integration Runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Komunikat o błędzie: "nie można uaktualnić środowiska Integration Runtime i ostatecznie przestanie działać, ponieważ nie można uzyskać dostępu do kontenera obiektów blob platformy Azure podanego dla konfiguracji niestandardowej".

Ten błąd występuje, gdy środowisko SSIS Integration Runtime nie może uzyskać dostępu do magazynu skonfigurowanego na potrzeby instalacji niestandardowej. Sprawdź, czy podany identyfikator URI sygnatury dostępu współdzielonego (SAS) jest prawidłowy i nie wygasł.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Komunikat o błędzie: "dostawca OLE DB firmy Microsoft dla Analysis Services. "HRESULT: 0x80004005 Description:" COM Error: błąd COM: mscorlib; Element docelowy wywołania zgłosił wyjątek.

Jedną z potencjalnych przyczyn jest to, że nazwa użytkownika lub hasło z włączonym Multi-Factor Authentication usługi Azure AD jest skonfigurowane pod kątem Azure Analysis Services uwierzytelniania. To uwierzytelnianie nie jest obsługiwane w programie SSIS Integration Runtime. Spróbuj użyć jednostki usługi do uwierzytelniania Azure Analysis Services:

1. Przygotuj jednostkę usługi zgodnie z opisem w temacie [Automatyzacja przy użyciu jednostek usługi](../analysis-services/analysis-services-service-principal.md).
2. W Menedżerze połączeń Skonfiguruj **użycie określonej nazwy użytkownika i hasła**: Ustaw **Identyfikator aplikacji** jako nazwę użytkownika i **clientSecret** jako hasło.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Komunikat o błędzie: "Źródło ADONET nie uzyskało połączenia {GUID} z następującym komunikatem o błędzie: Logowanie użytkownika" NT AUTHORITY\ANONYMOUS LOGON "" przy użyciu tożsamości zarządzanej nie powiodło się.

Upewnij się, że nie skonfigurowano metody uwierzytelniania Menedżera połączeń jako **Active Directory uwierzytelniania hasła** , gdy parametr *ConnectUsingManagedIdentity* ma **wartość true**. Zamiast tego można skonfigurować go jako **uwierzytelnianie SQL** , który jest ignorowany, jeśli ustawiono *ConnectUsingManagedIdentity* .

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Komunikat o błędzie: "0xC020801F at..., Źródło OData [...]: nie można uzyskać połączenia zarządzanego z Menedżera połączeń czasu wykonywania"

Jedną z potencjalnych przyczyn jest to, że Transport Layer Security (TLS) nie jest włączana w programie SSIS Integration Runtime, co jest wymagane przez źródło danych OData. Protokół TLS można włączyć w środowisku SSIS Integration Runtime przy użyciu opcji Dostosuj Instalatora. Więcej szczegółów można znaleźć w lokalizacji [nie można połączyć usługi Project Online OData z usług SSIS](/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) i [dostosować konfigurację dla środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Komunikat o błędzie: "Żądaj zadania przemieszczania z identyfikatorem GUID operacji... Niepowodzenie z powodu błędu: nie można wysłać operacji przemieszczania z komunikatem o błędzie: Microsoft. SqlServer. IntegrationServices. AisAgentCore. AisAgentException: nie można załadować serwera proxy danych ".

Upewnij się, że środowisko Azure-SSIS Integration Runtime jest skonfigurowane z Self-Hosted Integration Runtime. Więcej szczegółów można znaleźć w temacie [konfigurowanie Self-Hosted IR jako serwera proxy dla Azure-SSIS IR w usłudze ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Komunikat o błędzie: "stan zadania przemieszczania: niepowodzenie. Błąd zadania przemieszczania: kod błędu: 2010, ErrorMessage: samodzielny Integration Runtime... jest w trybie offline "

Upewnij się, że Self-Hosted Integration Runtime została zainstalowana i uruchomiona. Więcej szczegółów można znaleźć w tematach [Tworzenie i Konfigurowanie własnego środowiska Integration Runtime](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Komunikat o błędzie: "błąd zadania przemieszczania: ErrorCode: 2906, ErrorMessage: wykonywanie pakietu nie powiodło się., wynik: {" OperationErrorMessages ":" błąd: żądany dostawca OLE DB... nie jest zarejestrowany. Jeśli sterownik 64-bitowy nie został zainstalowany, Uruchom pakiet w trybie 32-bitowym... "

Upewnij się, że odpowiedni Dostawca używany przez łączniki OLE DB w pakiecie jest zainstalowany na maszynie Self-Hosted Integration Runtime prawidłowo. Więcej szczegółów można znaleźć w temacie [konfigurowanie Self-Hosted IR jako serwera proxy dla Azure-SSIS IR w usłudze ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Komunikat o błędzie: "błąd zadania przemieszczania: ErrorCode: 2906, ErrorMessage: wykonywanie pakietu nie powiodło się. wynik: {" OperationErrorMessages ":" Error: System. IO. FileLoadException: nie można załadować pliku lub zestawu "Microsoft. WindowsAzure. Storage, Version =..., Culture = neutral, PublicKeyToken = 31bf3856ad364e35" lub jednej z jego zależności. Zlokalizowana definicja manifestu zestawu nie odpowiada odwołaniu do zestawu. ..."

Jedną z potencjalnych przyczyn jest to, że Self-Hosted Integration Runtime nie jest poprawnie zainstalowana lub uaktualniona. Zaproponuj pobranie i ponowne zainstalowanie najnowszego środowiska Integration Runtime. Więcej szczegółów można znaleźć w tematach [Tworzenie i Konfigurowanie własnego środowiska Integration Runtime](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Komunikat o błędzie: "podczas żądania metadanych wymagane jest połączenie. Jeśli pracujesz w trybie offline, usuń zaznaczenie pola Pracuj w trybie offline w menu SSIS, aby włączyć połączenie "

* Potencjalna przyczyna i zalecana akcja:
  * Jeśli jest również wyświetlany komunikat ostrzegawczy "składnik nie obsługuje używania Menedżera połączeń z ustawieniem wartości ConnectByProxy wartość true" w dzienniku wykonywania, oznacza to, że Menedżer połączeń jest używany w składniku, który nie obsługuje jeszcze "ConnectByProxy". Obsługiwane składniki można znaleźć w temacie [konfigurowanie Self-Hosted IR jako serwera proxy dla Azure-SSIS IR w usłudze ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Dziennik wykonywania można znaleźć w [raportach programu SSMS](/sql/integration-services/performance/monitor-running-packages-and-other-operations#reports) lub w folderze dziennika określonym w działaniu pakietu usług SSIS.
  * Sieć wirtualna może być również używana do uzyskiwania dostępu do danych lokalnych jako alternatywy. Więcej szczegółów można znaleźć w witrynie [Azure-SSIS Integration Runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Komunikat o błędzie: "stan zadania przemieszczania: niepowodzenie. Błąd zadania przemieszczania: ErrorCode: 2906, ErrorMessage: wykonywanie pakietu nie powiodło się., dane wyjściowe: {"OperationErrorMessages": "kod zakończenia modułu SSIS:-1. \ n", "LogLocation": "... \\ SSISTelemetry \\ ExecutionLog \\ ... "," effectiveIntegrationRuntime ":"... "," executionDuration ":...," durationInQueue ": {" integrationRuntimeQueue ":...}}"

Upewnij się, że środowisko uruchomieniowe Visual C++ jest zainstalowane na komputerze z programem Self-Hosted Integration Runtime. Więcej szczegółów można znaleźć w temacie [konfigurowanie Self-Hosted IR jako serwera proxy dla Azure-SSIS IR w usłudze ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Nieoczekiwane wyzwolenie wielu wykonań pakietów

* Potencjalna przyczyna i zalecana akcja:
  * Działanie procedury składowanej modułu ADF lub działanie wyszukiwania służy do wyzwalania wykonywania pakietu SSIS. Polecenie t-SQL może napotkać przejściowy problem i wyzwolić ponowne uruchomienie, co może spowodować wykonanie wielu pakietów.
  * Zamiast tego użyj działania ExecuteSSISPackage, aby zapewnić, że wykonanie pakietu nie zostanie uruchomione ponownie, chyba że w działaniu zostanie określona liczba ponownych prób Szczegóły można znaleźć pod adresem [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](./how-to-invoke-ssis-package-ssis-activity.md)
  * Uściślij polecenie t-SQL, aby umożliwić ponowne uruchomienie, sprawdzając, czy wykonywanie zostało już wyzwolone

### <a name="package-execution-takes-too-long"></a>Wykonanie pakietu trwa zbyt długo

Oto potencjalne przyczyny i zalecane akcje:

* Zaplanowano zbyt wiele wykonań pakietów w programie SSIS Integration Runtime. Wszystkie te wykonania będą oczekiwać w kolejce na ich włączenie.
  * Określ maksymalną wartość przy użyciu tej formuły:

    Maksymalna liczba równoległych wykonań na IR = liczba węzłów * maksymalne wykonywanie równoległe na węzeł
  * Aby dowiedzieć się, jak ustawić liczbę węzłów i maksymalne wykonywanie równoległe na węzeł, zobacz temat [Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Środowisko uruchomieniowe środowiska SSIS zostało zatrzymane lub ma stan złej kondycji. Aby dowiedzieć się, jak sprawdzić stan i błędy programu SSIS Integration Runtime, zobacz [Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Zalecamy również ustawienie limitu czasu na karcie **Ogólne** : ![ Ustaw właściwości na karcie Ogólne ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) .

### <a name="poor-performance-in-package-execution"></a>Niska wydajność podczas wykonywania pakietu

Wypróbuj następujące czynności:

* Upewnij się, że środowisko w środowisku SSIS Integration Runtime znajduje się w tym samym regionie, co źródło danych i miejsce docelowe.

* Ustaw poziom rejestrowania wykonania pakietu na **wydajność** , aby zbierać informacje o czasie trwania dla każdego składnika w ramach wykonywania. Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie usług Integration Services (SSIS)](/sql/integration-services/performance/integration-services-ssis-logging).

* Sprawdź wydajność węzłów IR w Azure Portal:
  * Aby uzyskać informacje o sposobach monitorowania środowiska usług SSIS Integration Runtime, zobacz [Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Historię procesora CPU/pamięci dla środowiska SSIS Integration runtime można znaleźć, wyświetlając metryki fabryki danych w Azure Portal.
    ![Monitoruj metryki programu SSIS Integration Runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)