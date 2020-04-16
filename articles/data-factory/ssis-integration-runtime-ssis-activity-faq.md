---
title: Rozwiązywanie problemów z wykonywaniem pakietów w czasie wykonywania integracji SSIS
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z wykonywaniem pakietów SSIS w czasie wykonywania integracji SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 8c85a652cde840336c51e1a5b5459f9dc591e0be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414684"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Rozwiązywanie problemów z wykonywaniem pakietów w czasie wykonywania integracji SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten artykuł zawiera najczęstsze błędy, które mogą wystąpić podczas wykonywania pakietów usług integracyjnych programu SQL Server (SSIS) w czasie wykonywania integracji SSIS. Opisano w nim potencjalne przyczyny i działania mające na celu rozwiązanie błędów.

## <a name="where-to-find-logs-for-troubleshooting"></a>Gdzie można znaleźć dzienniki do rozwiązywania problemów

Użyj portalu usługi Azure Data Factory, aby sprawdzić dane wyjściowe działania wykonywania pakietu SSIS. Dane wyjściowe obejmują wynik wykonania, komunikaty o błędach i identyfikator operacji. Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie potoku](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Użyj katalogu SSIS (SSISDB), aby sprawdzić dzienniki szczegółów dla wykonania. Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie uruchomionych pakietów i innych operacji](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Typowe błędy, przyczyny i rozwiązania

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Komunikat o błędzie: "Limit czasu połączenia wygasł" lub "Usługa napotkała błąd przetwarzania żądania. Spróbuj ponownie”.

Oto potencjalne przyczyny i zalecane działania:
* Źródło danych lub miejsce docelowe jest przeciążone. Sprawdź obciążenie źródła danych lub miejsca docelowego i sprawdź, czy ma wystarczającą pojemność. Na przykład jeśli używano usługi Azure SQL Database, należy rozważyć skalowanie w górę, jeśli baza danych może się skończyć.
* Sieć między środowiskiem uruchomieniowym integracji SSIS a źródłem danych lub miejscem docelowym jest niestabilna, szczególnie gdy połączenie jest międzyregionowe lub między środowiskiem lokalnym a platformą Azure. Zastosuj wzorzec ponawiania w pakiecie SSIS, wykonując następujące kroki:
  * Upewnij się, że pakiety SSIS można ponownie uruchomić po awarii bez skutków ubocznych (na przykład utraty danych lub powielania danych).
  * Konfigurowanie **interwału ponawiania ponawiania** i ![ **ponawiania prób** wykonania działania pakietu **SSIS** na karcie **Ogólne.** Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * W przypadku ADO.NET i ole db składnika źródłowego lub docelowego, ustaw **ConnectRetryCount** i **ConnectRetryInterval** w Menedżerze połączeń w pakiecie SSIS lub SSIS działania.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Komunikat o błędzie: "Źródło ADO NET nie udało się uzyskać połączenia '...'" z "Wystąpił błąd związany z siecią lub wystąpieniem podczas ustanawiania połączenia z programem SQL Server. Serwer nie został znaleziony lub nie był dostępny."

Ten problem zwykle oznacza, że źródło danych lub miejsce docelowe są niedostępne z poziomu środowiska SSIS Integration Runtime. Przyczyny mogą być różne. Wypróbuj następujące czynności:
* Upewnij się, że prawidłowo przekazujesz nazwę źródła danych lub miejsca docelowego/ADRES IP.
* Upewnij się, że zapora jest prawidłowo ustawiona.
* Upewnij się, że sieć wirtualna jest poprawnie skonfigurowana, jeśli źródło danych lub miejsce docelowe jest lokalne:
  * Można sprawdzić, czy problem jest z konfiguracji sieci wirtualnej przez inicjowanie obsługi administracyjnej maszyny Wirtualnej platformy Azure w tej samej sieci wirtualnej. Następnie sprawdź, czy źródło danych lub miejsce docelowe można uzyskać z maszyny Wirtualnej platformy Azure.
  * Więcej informacji na temat korzystania z sieci wirtualnej z środowiskiem uruchomieniowym integracji SSIS można znaleźć w [środowisku uruchomieniowym integracji platformy Azure-SSIS z siecią wirtualną.](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Komunikat o błędzie: "Źródło ADO NET nie udało się uzyskać połączenia '...'" z "Nie można utworzyć menedżera zarządzanych połączeń."

Potencjalną przyczyną jest to, że dostawca ADO.NET używany w pakiecie nie jest zainstalowany w czasie wykonywania integracji SSIS. Dostawcę można zainstalować przy użyciu konfiguracji niestandardowej. Więcej informacji na temat konfiguracji niestandardowej można znaleźć w obszarze [Dostosowywanie konfiguracji dla środowiska wykonawczego integracji platformy Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Komunikat o błędzie: "Połączenie '...' nie znaleziono"

Ten błąd może powodować znany problem w starszych wersjach programu SQL Server Management Studio (SSMS). Jeśli pakiet zawiera składnik niestandardowy (na przykład dodatek SSIS Azure Feature Pack lub składniki partnera), który nie jest zainstalowany na maszynie, na której przeprowadzane jest wdrożenie za pomocą programu SSMS, program SSMS usunie ten składnik i spowoduje wystąpienie błędu. Uaktualnij [system SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) do najnowszej wersji, która ma rozwiązany problem.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Komunikat o błędzie:"Kod zakończenia wykonawca SSIS: -1073741819."

* Potencjalna przyczyna i zalecana akcja:
  * Ten błąd może być spowodowane ograniczeniem dla źródła i miejsca docelowego programu Excel, gdy wiele źródeł lub miejsc docelowych programu Excel są wykonywane równolegle w wielu wątkach. Można obejść to ograniczenie, zmienić składniki programu Excel do wykonania w sekwencji lub oddzielić je na różne pakiety i wyzwolić za pomocą "Execute Package Task" z ExecuteOutOfProcess właściwości ustawionej jako True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Komunikat o błędzie: "Na dysku jest za mało miejsca"

Ten błąd oznacza, że dysk lokalny jest używany w węźle środowiska wykonawczego integracji SSIS. Sprawdź, czy pakiet lub konfiguracja niestandardowa zużywa dużo miejsca na dysku:
* Jeśli dysk jest zużywany przez pakiet, zostanie zwolniony po zakończeniu wykonywania pakietu.
* Jeśli dysk jest zużywany przez konfigurację niestandardową, należy zatrzymać środowisko uruchomieniowe integracji SSIS, zmodyfikować skrypt i ponownie uruchomić środowisko wykonawcze integracji. Cały kontener obiektów blob platformy Azure, który został określony dla konfiguracji niestandardowej zostaną skopiowane do węzła wykonawczego integracji SSIS, więc sprawdź, czy w tym kontenerze jest jakaś niepotrzebna zawartość.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Komunikat o błędzie: "Nie można pobrać zasobu z wzorca. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Kod:300004. Opis:Załadowanie pliku "***" nie powiodło się."

* Potencjalna przyczyna i zalecana akcja:
  * Jeśli działanie SSIS wykonuje pakiet z systemu plików (plik pakietu lub pliku projektu), ten błąd wystąpi, jeśli plik projektu, pakietu lub konfiguracji nie jest dostępny z poświadczeniem dostępu do pakietu podanym w działaniu SSIS
    * Jeśli używasz pliku platformy Azure:
      * Ścieżka pliku powinna \\ \\ \<zaczynać\>się od nazwy\\\<konta magazynu .file.core.windows.net ścieżka udziału plików\>
      * Domena powinna być "Azure"
      * Nazwa użytkownika \<powinna być nazwą konta magazynu\>
      * Hasło powinno \<być kluczem dostępu do pamięci masowej\>
    * Jeśli korzystasz z pliku lokalnego, sprawdź, czy w sieci wirtualnej, poświadczenia dostępu do pakietu i uprawnienia są poprawnie skonfigurowane, aby środowisko uruchomieniowe integracji platformy Azure-SSIS miało dostęp do lokalnego udziału plików

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Komunikat o błędzie: "Nazwa pliku '...' w połączeniu nie była prawidłowa"

* Potencjalna przyczyna i zalecana akcja:
  * Określono nieprawidłową nazwę pliku
  * Upewnij się, że używasz nazwy FQDN (w pełni kwalifikowana nazwa domeny) zamiast krótkiego czasu w menedżerze połączeń

### <a name="error-message-cannot-open-file-"></a>Komunikat o błędzie: "Nie można otworzyć pliku '...""

Ten błąd występuje, gdy wykonanie pakietu nie można znaleźć pliku na dysku lokalnym w czasie wykonywania integracji SSIS. Wypróbuj następujące czynności:
* Nie należy używać ścieżki bezwzględnej w pakiecie, który jest wykonywany w czasie wykonywania integracji SSIS. Użyj bieżącego katalogu roboczego wykonywania (.) lub folderu tymczasowego (%TEMP%) Zamiast.
* Jeśli chcesz utrwalić niektóre pliki w węzłach środowiska wykonawczego integracji SSIS, przygotuj pliki zgodnie z opisem w [polu Dostosuj konfigurację](how-to-configure-azure-ssis-ir-custom-setup.md). Wszystkie pliki w katalogu roboczym zostaną wyczyszczone po zakończeniu wykonywania.
* Użyj usługi Azure Files zamiast przechowywania pliku w węźle środowiska wykonawczego integracji SSIS. Aby uzyskać szczegółowe informacje, zobacz [Używanie udziałów plików platformy Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Komunikat o błędzie: "Baza danych 'SSISDB' osiągnęła swój przydział rozmiaru"

Możliwą przyczyną jest osiągnięcie przydziału rozmiaru przez bazę danych SSISDB, która została utworzona w bazie danych Azure SQL Database lub wystąpieniu zarządzanym podczas tworzenia środowiska SSIS Integration Runtime. Wypróbuj następujące czynności:
* Rozważ zwiększenie liczby jednostek DTU bazy danych. Aby uzyskać więcej informacji, zobacz [Limity zasobów bazy danych SQL Database dla serwera usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Sprawdź, czy pakiet wygeneruje wiele dzienników. Jeśli tak, możesz skonfigurować zadanie elastyczne, które oczyści te dzienniki. Aby uzyskać szczegółowe informacje, zobacz [Oczyszczanie dzienników SSISDB za pomocą zadań Azure Elastic Database](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Komunikat o błędzie: "Limit żądań dla bazy danych wynosi ... i został osiągnięty."

Jeśli wiele pakietów są uruchomione równolegle w czasie wykonywania integracji SSIS, ten błąd może wystąpić, ponieważ SSISDB ma trafić limit żądań. Należy rozważyć zwiększenie DTC SSISDB, aby rozwiązać ten problem. Aby uzyskać więcej informacji, zobacz [Limity zasobów bazy danych SQL Database dla serwera usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Komunikat o błędzie: "Operacja SSIS nie powiodła się z nieoczekiwanym stanem operacji: ..."

Błąd jest głównie spowodowane przez przejściowy problem, więc spróbuj ponownie uruchomić wykonanie pakietu. Zastosuj wzorzec ponawiania w pakiecie SSIS, wykonując następujące kroki:

* Upewnij się, że pakiety SSIS można ponownie uruchomić po awarii bez skutków ubocznych (na przykład utraty danych lub powielania danych).
* Konfigurowanie **interwału ponawiania ponawiania** i ![ **ponawiania prób** wykonania działania pakietu **SSIS** na karcie **Ogólne.** Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* W przypadku ADO.NET i ole db składnika źródłowego lub docelowego, ustaw **ConnectRetryCount** i **ConnectRetryInterval** w Menedżerze połączeń w pakiecie SSIS lub SSIS działania.

### <a name="error-message-there-is-no-active-worker"></a>Komunikat o błędzie: "Nie ma aktywnego pracownika."

Ten błąd zwykle oznacza, że środowisko uruchomieniowe integracji SSIS ma stan złej kondycji. Sprawdź portal platformy Azure pod kątem stanu i szczegółowych błędów. Aby uzyskać więcej informacji, zobacz [środowisko uruchomieniowe integracji platformy Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Komunikat o błędzie: "Nie można uaktualnić środowiska wykonawczego integracji i ostatecznie przestanie działać, ponieważ nie możemy uzyskać dostępu do kontenera obiektów blob platformy Azure, który został dostarczony dla konfiguracji niestandardowej."

Ten błąd występuje, gdy środowisko uruchomieniowe integracji SSIS nie może uzyskać dostępu do magazynu skonfigurowane do instalacji niestandardowej. Sprawdź, czy podany identyfikator URI podpisu dostępu udostępnionego (SAS) jest prawidłowy i nie wygasł.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Komunikat o błędzie: "Dostawca bazy danych OLE firmy Microsoft dla usług analysis services. 'Hresult: 0x80004005 Opis:' Błąd COM: błąd COM: mscorlib; Wyjątek został zgłoszony przez cel wywołania"

Jedną z potencjalnych przyczyn jest to, że nazwa użytkownika lub hasło z włączoną obsługą uwierzytelniania wieloskładnikowego platformy Azure jest skonfigurowane do uwierzytelniania usługi Azure Analysis Services. To uwierzytelnianie nie jest obsługiwane w czasie wykonywania integracji SSIS. Spróbuj użyć jednostki usługi do uwierzytelniania usługi Azure Analysis Services:

1. Przygotuj jednostkę usługi zgodnie z opisem w [automatyzacji z jednostkami usługi.](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
2. W Menedżerze połączeń skonfiguruj **Użyj określonej nazwy użytkownika i hasła:** ustaw **AppID** jako nazwę użytkownika i **clientSecret** jako hasło.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Komunikat o błędzie: "Źródło ADONET nie udało się uzyskać połączenia {GUID} z następującym komunikatem o błędzie: Nie można zalogować się dla użytkownika 'NT AUTHORITY\ANONYMOUS LOGON'" podczas korzystania z tożsamości zarządzanej

Upewnij się, że nie skonfigurowano metody uwierzytelniania Menedżera połączeń jako **uwierzytelniania hasłem usługi Active Directory,** gdy parametr *ConnectUsingManagedIdentity* jest **true**. Zamiast tego można skonfigurować go jako **uwierzytelnianie SQL,** które jest ignorowane, jeśli jest ustawiona *funkcja ConnectUsingManagedIdentity.*

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Komunikat o błędzie: "0xC020801F at ..., OData Source [...]: Nie można uzyskać zarządzanego połączenia z menedżera połączeń w czasie wykonywania"

Jedną z potencjalnych przyczyn jest to, że transport warstwy zabezpieczeń (TLS) nie jest włączona w czasie wykonywania integracji SSIS, który jest wymagany przez źródło OData. Funkcję TLS można włączyć w czasie wykonywania integracji ssis przy użyciu konfiguracji Dostosuj. Więcej szczegółów można znaleźć na stronie [Nie można połączyć usługi Project Online Odata z SSIS](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) i [dostosuj konfigurację dla środowiska wykonawczego integracji azure-SSIS.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Komunikat o błędzie: "Żądanie zadania przemieszczania z operacją guid ... nie powiódł się od czasu błędu: nie można wysłać operacji przejściowej z komunikatem o błędzie: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Nie można załadować serwera proxy danych."

Upewnij się, że środowisko uruchomieniowe integracji platformy Azure-SSIS jest skonfigurowane przy własnym hostowanym czasie pracy integracji. Więcej szczegółów można znaleźć na [stronie Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Komunikat o błędzie: "Stan zadania przemieszczania: nie powiodło się. Błąd zadania przemieszczania: ErrorCode: 2010, ErrorMessage: Self-hosted Integration Runtime ... jest w trybie offline"

Upewnij się, że środowisko uruchomieniowe integracji hostowane samodzielnie jest zainstalowane i uruchomione. Więcej szczegółów można znaleźć na stronie [Tworzenie i konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Komunikat o błędzie: "Błąd zadania przemieszczania: ErrorCode: 2906, ErrorMessage: Wykonanie pakietu nie powiodło się., Wyjście: {"OperationErrorMessages": "Błąd: Żądany dostawca OLE DB ... nie jest zarejestrowany. Jeśli sterownik 64-bitowy nie jest zainstalowany, uruchom pakiet w trybie 32-bitowym..."

Upewnij się, że odpowiedni dostawca używany przez łączniki OLE DB w pakiecie są poprawnie zainstalowane na komputerze uruchomieniowym integracji hostowanego samodzielnie. Więcej szczegółów można znaleźć na stronie [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Komunikat o błędzie: "Błąd zadania przemieszczania: Kod błędu: 2906, ErrorMessage: Wykonanie pakietu nie powiodło się., Dane wyjściowe: {"OperationErrorMessages": "Error: System.IO.FileLoadException: Nie można załadować pliku lub zestawu 'Microsoft.WindowsAzure.Storage, Version=..., Culture=neutral, PublicKeyToken=31bf3856ad364e35' lub jednej z jego zależności. Definicja manifestu znajdującego się zestawu nie jest zgodna z odniesieniem do złożenia." ..."

Jedną z potencjalnych przyczyn jest środowisko uruchomieniowe integracji self-hosted nie jest poprawnie zainstalowany lub uaktualniony. Zasugeruj pobranie i ponowne zainstalowanie najnowszego środowiska wykonawczego integracji hostowanego samodzielnie. Więcej szczegółów można znaleźć na stronie [Tworzenie i konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Komunikat o błędzie: "Połączenie jest wymagane podczas żądania metadanych. Jeśli pracujesz w trybie offline, wyeznańcz opcję Praca w trybie offline w menu SSIS, aby włączyć połączenie"

* Potencjalna przyczyna i zalecana akcja:
  * Jeśli istnieje również komunikat ostrzegawczy "Składnik nie obsługuje przy użyciu menedżera połączeń z ConnectByProxy ustawienie wartości true" w dzienniku wykonywania, oznacza to, że menedżer połączeń jest używany na składnik, który nie obsługuje jeszcze "ConnectByProxy" jeszcze. Obsługiwane składniki można znaleźć w [witrynie Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Dziennik wykonywania można znaleźć w [raporcie SSMS](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) lub w folderze dziennika określonym w działaniu wykonywania pakietu SSIS.
  * VNet może również służyć do uzyskiwania dostępu do danych lokalnych jako alternatywa. Więcej szczegółów można znaleźć na stronie [Dołącz do środowiska wykonawczego integracji platformy Azure-SSIS z siecią wirtualną](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Komunikat o błędzie: "Stan zadania przemieszczania: nie powiodło się. Błąd zadania przemieszczania: ErrorCode: 2906, ErrorMessage: Wykonanie pakietu nie powiodło się., Dane wyjściowe: {"OperationErrorMessages": "SSIS Executor exit code: -1.\n", "LogLocation": "... \\SSISTelemetry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "durationInQueue": { "integrationRuntimeQueue": ... }}"

Upewnij się, że środowisko wykonawcze Visual C++ jest zainstalowane na komputerze środowiska uruchomieniowego integracji hostowanego samodzielnie. Więcej szczegółów można znaleźć na stronie [Configure Self-Hosted IR as a proxy for Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Wykonywanie wielu pakietów jest wyzwalane nieoczekiwanie

* Potencjalna przyczyna i zalecana akcja:
  * Działanie procedury składowanej usługi ADF lub działanie odnośnika są używane do wyzwalania wykonywania pakietu SSIS. Polecenie t-sql może spowodować przemijające wystąpienie i wyzwolić ponowne uruchomienie, co spowodowałoby wykonanie wielu pakietów.
  * Użyj ExecuteSSISPackage działania zamiast tego, który zapewnia wykonanie pakietu nie zostanie ponownie uruchom, chyba że użytkownik ustaw liczbę ponownych prób w działaniu. Szczegóły można znaleźć na stronie[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Uściślaj swoje polecenie t-sql, aby móc ponownie uruchomić, sprawdzając, czy wykonanie zostało już wyzwolone

### <a name="package-execution-takes-too-long"></a>Wykonanie pakietu trwa zbyt długo

Oto potencjalne przyczyny i zalecane działania:

* Zaplanowano zbyt wiele wykonań pakietów w czasie wykonywania integracji SSIS. Wszystkie te egzekucje będą czekać w kolejce do swojej tury.
  * Określ wartość maksymalną za pomocą tej formuły:

    Maksymalna liczba wykonań równoległych na IR = liczba węzłów * maksymalne wykonanie równoległe na węzeł
  * Aby dowiedzieć się, jak ustawić liczbę węzłów i maksymalne równoległe wykonanie na węzeł, zobacz [Tworzenie środowiska wykonawczego integracji platformy Azure-SSIS w usłudze Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Środowisko uruchomieniowe integracji SSIS jest zatrzymane lub ma stan złej kondycji. Aby dowiedzieć się, jak sprawdzić stan i błędy środowiska wykonawczego integracji SSIS, zobacz [Środowisko uruchomieniowe integracji platformy Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Zaleca się również ustawienie limitu czasu na ![karcie **Ogólne:** Ustaw](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)właściwości na karcie Ogólne .

### <a name="poor-performance-in-package-execution"></a>Niska wydajność w wykonywaniu pakietów

Wypróbuj następujące czynności:

* Upewnij się, że środowisko uruchomieniowe integracji SSIS znajduje się w tym samym regionie co źródło danych i miejsce docelowe.

* Ustaw poziom rejestrowania wykonywania pakietu na **Wydajność,** aby zbierać informacje o czasie trwania dla każdego składnika w wykonaniu. Aby uzyskać szczegółowe informacje, zobacz [rejestrowanie usług integracji (SSIS).](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Sprawdź wydajność węzła podczerwa w witrynie Azure portal:
  * Aby uzyskać informacje dotyczące monitorowania środowiska wykonawczego integracji SSIS, zobacz [środowisko uruchomieniowe integracji platformy Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Historię procesora CPU/pamięci dla środowiska wykonawczego integracji SSIS można znaleźć, wyświetlając metryki fabryki danych w witrynie Azure portal.
    ![Monitorowanie metryk środowiska wykonawczego integracji SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
