---
title: Konfigurowanie środowiska uruchomieniowego integracji hostowanego samodzielnie jako serwera proxy dla usługi SSIS
description: Dowiedz się, jak skonfigurować środowisko uruchomieniowe integracji hostowanego samodzielnie jako serwer proxy dla środowiska wykonawczego integracji platformy Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: ecfdf2a11f31c18064be9a607f2bb3938d26e661
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414898"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Konfigurowanie samodzielnie hostowanego podczerwona jako serwera proxy dla usługi Azure-SSIS IR w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób uruchamiania pakietów usług integracji programu SQL Server (SSIS) w czasie wykonywania integracji platformy Azure-SSIS (Azure-SSIS IR) w usłudze Azure Data Factory przy samodzielnym uruchomieniu integracji (samodzielne środowisko IR) skonfigurowanym jako serwer proxy. 

Dzięki tej funkcji można uzyskiwać dostęp do danych lokalnie bez konieczności [dołączania usługi Azure-SSIS IR do sieci wirtualnej.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) Ta funkcja jest przydatna, gdy sieć firmowa ma konfigurację zbyt złożoną lub zbyt restrykcyjną, aby można było wstrzyknąć do niej usługę Azure-SSIS IR.

Ta funkcja dzieli każde zadanie przepływu danych SSIS, które ma lokalne źródło danych, na dwa zadania przejściowe: 
* Pierwsze zadanie, które działa na własny hostowany podczerwony, najpierw przenosi dane z lokalnego źródła danych do obszaru przemieszczania w magazynie obiektów Blob platformy Azure.
* Drugie zadanie, które jest uruchamiane na platformie Azure-SSIS IR, a następnie przenosi dane z obszaru przemieszczania do zamierzonego miejsca docelowego danych.

Inne zalety i możliwości tej funkcji umożliwiają na przykład skonfigurowanie samodzielnej usługi IR w regionach, które nie są jeszcze obsługiwane przez usługę Azure-SSIS IR, i zezwalanie na publiczny statyczny adres IP samodzielnie hostowanego podczerwony na zaporze źródeł danych.

## <a name="prepare-the-self-hosted-ir"></a>Przygotowanie samodzielnego podczerwony

Aby użyć tej funkcji, należy najpierw utworzyć fabrykę danych i skonfigurować azure-SSIS IR w nim. Jeśli jeszcze tego nie zrobiono, postępuj zgodnie z instrukcjami w [obszarze Konfigurowanie usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Następnie skonfiguruj samodzielnie hostowane podczerwone urządzenie IR w tej samej fabryce danych, w której skonfigurowano usługę Azure-SSIS IR. Aby to zrobić, zobacz [Tworzenie samodzielnego podczerwonyej osoby.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

Na koniec można pobrać i zainstalować najnowszą wersję samodzielnego środowiska IR, a także dodatkowe sterowniki i środowisko wykonawcze na komputerze lokalnym lub maszynie wirtualnej platformy Azure (VM), w następujący sposób:
- Pobierz i zainstaluj najnowszą wersję [samodzielnego podczerwonyego podczerwionego.](https://www.microsoft.com/download/details.aspx?id=39717)
- Jeśli używasz łączników łączenia i osadzania obiektów (OLEDB) w pakietach, pobierz i zainstaluj odpowiednie sterowniki OLEDB na tym samym komputerze, na którym jest zainstalowana samodzielna podczerwona, jeśli jeszcze tego nie zrobiono.  

  Jeśli używasz wcześniejszej wersji sterownika OLEDB dla programu SQL Server (klient macierzysty programu SQL Server [SQLNCLI]), [pobierz wersję 64-bitową](https://www.microsoft.com/download/details.aspx?id=50402).  

  Jeśli używasz najnowszej wersji sterownika OLEDB dla programu SQL Server (MSOLEDBSQL), [pobierz wersję 64-bitową](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Jeśli używasz sterowników OLEDB dla innych systemów baz danych, takich jak PostgreSQL, MySQL, Oracle i tak dalej, możesz pobrać wersje 64-bitowe z ich witryn sieci Web.
- Jeśli jeszcze tego nie zrobiono, [pobierz i zainstaluj 64-bitową wersję środowiska wykonawczego programu Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) na tym samym komputerze, na którym jest zainstalowana samodzielna podczerwona.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Przygotowanie usługi połączonej z magazynem obiektów Blob platformy Azure do przemieszczania

Jeśli jeszcze tego nie zrobiono, utwórz usługę połączone z magazynem obiektów Blob platformy Azure w tej samej fabryce danych, w której skonfigurowana jest usługa Azure-SSIS IR. Aby to zrobić, zobacz [Tworzenie usługi połączonej z danymi platformy Azure](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Pamiętaj, aby wykonać następujące czynności:
- W przypadku **magazynu danych**wybierz pozycję **Azure Blob Storage**.  
- W przypadku **połączenia za pośrednictwem środowiska wykonawczego integracji**wybierz **autoresolveintegrationruntime** (nie azure-SSIS IR ani samodzielnie hostowane IR), ponieważ używamy domyślnego środowiska Azure IR do pobierania poświadczeń dostępu do usługi Azure Blob Storage.
- W przypadku **metody uwierzytelniania**wybierz **pozycję Klucz konta,** **IDENTYFIKATOR URI sygnatury dostępu**Współdzielonego lub **Główny podmiot usługi**.  

    >[!TIP]
    >Jeśli wybierzesz **service principal** metody, przyznać jednostki usługi co najmniej *roli współautora danych blob* magazynu. Aby uzyskać więcej informacji, zobacz [łącznik magazynu obiektów Blob platformy Azure](connector-azure-blob-storage.md#linked-service-properties).

![Przygotowanie usługi połączonej z magazynem obiektów Blob platformy Azure do przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurowanie podczerwa ir usługi Azure-SSIS z własnym hostowanym podczerwem jako serwerem proxy

Po przygotowaniu samodzielnie hostowanych usług IR i usługi Azure Blob do magazynu połączonego do przemieszczania, można teraz skonfigurować nowy lub istniejący identyfikator IR azure-SSIS z własnym hostowanym podczerwem jako serwerem proxy w portalu lub aplikacji fabryki danych. Zanim to zrobisz, jeśli istniejąca usługa Azure-SSIS IR jest już uruchomiona, zatrzymaj ją, a następnie uruchom ponownie.

1. W okienku **konfiguracji środowiska uruchomieniowego integracja** pomiń przejście obok sekcji **Ustawienia ogólne** i **Ustawienia SQL,** wybierając pozycję **Dalej**. 

1. W sekcji **Ustawienia zaawansowane** wykonaj następujące czynności:

   1. Zaznacz pole wyboru **Konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie jako serwera proxy dla środowiska wykonawczego integracji platformy Azure-SSIS.** 

   1. Na liście rozwijanej **Self-Hosted Integration Runtime** wybierz istniejącą, samodzielnie hostowane środowisko IR jako serwer proxy dla usługi Azure-SSIS IR.

   1. Na liście rozwijanej **Usługi połączonej magazyn przemieszczania** wybierz istniejącą usługę połączone z magazynem obiektów Blob platformy Azure lub utwórz nową usługę przejściową.

   1. W polu **Ścieżka przemieszczania** określ kontener obiektów blob na wybranym koncie magazynu obiektów Blob platformy Azure lub pozostaw go pustym, aby użyć domyślnego kontenera dla przemieszczania.

   1. Wybierz przycisk **Kontynuuj**.

   ![Ustawienia zaawansowane z samodzielnym podczerwonym](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Można również skonfigurować nowy lub istniejący identyfikator IR usługi Azure-SSIS z własnym hostowanym podczerwem jako serwerem proxy przy użyciu programu PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Włączanie łączenia pakietów SSIS przez serwer proxy

Korzystając z najnowszego pliku SSDT z rozszerzeniem SSIS Projects dla programu Visual Studio `ConnectByProxy` lub autonomicznego instalatora, można znaleźć nową właściwość, która została dodana w menedżerach połączeń OLEDB lub Flat File.
* [Pobierz rozszerzenie SSDT z SSIS Projects dla programu Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Pobierz samodzielny instalator](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Podczas projektowania nowych pakietów, które zawierają zadania przepływu danych z OLEDB lub płaskich źródeł plików, które umożliwiają dostęp do baz danych lub plików w środowisku lokalnym, można włączyć tę właściwość, ustawiając ją na *True* w **właściwości okienku** odpowiednich menedżerów połączeń.

![Włącz ConnectByProxy, właściwość](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Można również włączyć tę właściwość po uruchomieniu istniejących pakietów, bez konieczności ręcznej zmiany ich jeden po drugim.  Dostępne są dwie opcje:
- **Opcja A:** Otwórz, odbuduj i ponownie wdrożyć projekt zawierający te pakiety z najnowszym zestawem SSDT do uruchomienia na urządzeniu Azure-SSIS IR. Następnie można włączyć właściwość, ustawiając ją na *Wartość True* dla odpowiednich menedżerów połączeń. Gdy są uruchomione pakiety z usługi SSMS, te menedżery połączeń są wyświetlane na **menedżerów połączeń** kartę w oknie podręcznym **Wykonywanie pakietu.**

  ![Włącz ConnectByProxy, właściwość2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Można również włączyć właściwość, ustawiając ją na *True* dla odpowiednich menedżerów połączeń, które pojawiają się na karcie **Menedżerowie połączeń** [w wykonaniu działania pakietu SSIS,](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) gdy są uruchomione pakiety w potokach fabryki danych.
  
  ![Włącz ConnectByProxy, właściwość3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opcja B:** Ponowne wdrożenie projektu zawierającego te pakiety do uruchomienia na SSIS IR. Następnie można `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`włączyć właściwość, podając jej ścieżkę właściwości i ustawiając ją na *True* jako zastąpienie właściwości na karcie **Zaawansowane** okno podręczne Wykonywanie **pakietu** podczas uruchamiania pakietów z usługi SSMS.

  ![Włącz ConnectByProxy, właściwość4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Można `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`również włączyć właściwość, podając jej ścieżkę właściwości i ustawiając ją na *True* jako zastępowanie właściwości na karcie **Zastępowanie właściwości** działania Wykonywanie pakietu [SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) podczas uruchamiania pakietów w potokach fabryki danych.
  
  ![Włącz ConnectByProxy, właściwość5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Debugowanie pierwszego i drugiego zadania przemieszczania

Na samodzielnym podczerwieni można znaleźć dzienniki środowiska wykonawczego w folderze *C:\ProgramData\SSISTelemetry i* dzienniki wykonywania pierwszych zadań przemieszczania w folderze *C:\ProgramData\SSISTelemetry\ExecutionLog.*  Dzienniki wykonywania drugich zadań przemieszczania można znaleźć w witrynie SSISDB lub określonych ścieżkach rejestrowania, w zależności od tego, czy pakiety są przechowywane w USPDB, czy w systemie plików, udziałach plików czy w usłudze Azure Files. Unikatowe identyfikatory pierwszych zadań przejściowych można również znaleźć w dziennikach wykonywania drugich zadań przejściowych. 

![Unikatowy identyfikator pierwszego zadania przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Używanie uwierzytelniania systemu Windows w zadaniach przemieszczania

Jeśli zadania przemieszczania na samodzielnym podczerweniu wymagają uwierzytelniania systemu Windows, [skonfiguruj pakiety SSIS tak, aby używały tego samego uwierzytelniania systemu Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Zadania przemieszczania będą wywoływane przy użyciu samodzielnego konta usługi podczerwonej *(NT SERVICE\DIAHostService*, domyślnie), a dostęp do magazynów danych będzie dostępny za pomocą konta uwierzytelniania systemu Windows. Oba konta wymagają przypisania do nich pewnych zasad zabezpieczeń. Na komputerze z własnym podczerwem przejdź do usługi**Przypisywanie praw użytkownika****zasad** >  > zabezpieczeń **lokalnych,** a następnie wykonaj następujące czynności:

1. Przypisz *przydziały pamięci dla procesu* i *zastąp* zasady tokenów na poziomie procesu do samodzielnego konta usługi podczerwony. Powinno to nastąpić automatycznie podczas instalowania samodzielnego podczerwania z domyślnym kontem usługi. Jeśli tak nie jest, przypisz te zasady ręcznie. Jeśli używasz innego konta usługi, przypisz do niego te same zasady.

1. Przypisz *zasadę Logowanie jako usługę* do konta uwierzytelniania systemu Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Rozliczanie pierwszego i drugiego zadania przemieszczania

Pierwsze zadania przemieszczania, które są uruchamiane na własny hostowane IR są rozliczane oddzielnie, podobnie jak wszystkie działania przenoszenia danych, które są uruchamiane na własny hostowane IR są rozliczane. Jest to określone w [artykule cenniku potoku danych usługi Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

Drugie zadania przemieszczania, które są uruchamiane na platformie Azure-SSIS IR nie są rozliczane oddzielnie, ale uruchomiony azure-SSIS IR jest rozliczane zgodnie z określonymi w [artykule cenowym usługi Azure-SSIS IR.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>Włączanie protokołu TLS 1.2

Jeśli chcesz użyć silnego protokołu kryptograficznego/bezpieczniejszego protokołu sieciowego (TLS 1.2) i wyłączyć starsze wersje SSL/TLS na samodzielnym podczerwieni, możesz pobrać i uruchomić skrypt *main.cmd,* który można znaleźć w *folderze CustomSetupScript/UserScenarios/TLS 1.2* naszego publicznego kontenera podglądu.  Za pomocą [Eksploratora usługi Azure Storage](https://storageexplorer.com/)można połączyć się z naszym publicznym kontenerem w wersji zapoznawczej, wprowadzając następujący identyfikator URI sygnatury dostępu Współdzielonego:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Bieżące ograniczenia

- Obecnie obsługiwane są tylko zadania przepływu danych z otwartymi źródłami łączności bazy danych (ODBC)/OLEDB/Flat File lub OLEDB. 
- Obecnie obsługiwane są tylko usługi połączone z magazynem obiektów Blob platformy Azure, które są skonfigurowane przy użyciu *klucza konta*, *identyfikatora URI (Shared Access Signature)* lub *Service Principal* authentication.
- *ParameterMapping* w źródle OLEDB nie jest jeszcze obsługiwany. Aby obejść ten problem, użyj *polecenia SQL From Variable* jako *accessmode* i użyj *wyrażenia,* aby wstawić zmienne/parametry w poleceniu SQL. Na ilustracji zobacz *pakiet ParameterMappingSample.dtsx,* który można znaleźć w folderze *SelfHostedIRProxy/Limitations* naszego publicznego kontenera podglądu. Korzystając z Eksploratora usługi Azure Storage, możesz połączyć się z naszym publicznym kontenerem w wersji zapoznawczej, wprowadzając powyższy identyfikator URI sygnatury dostępu Współdzielonego.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu samodzielnie hostowanego podczerwony jako serwera proxy dla usługi Azure-SSIS IR, można wdrożyć i uruchomić pakiety, aby uzyskać dostęp do danych lokalnie jako wykonywanie działań pakietu SSIS w potokach fabryki danych. Aby dowiedzieć się, jak to zrobić, zobacz [Uruchamianie pakietów SSIS jako wykonywanie działań pakietu SSIS w potokach usługi Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
