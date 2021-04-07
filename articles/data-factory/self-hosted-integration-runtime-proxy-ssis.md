---
title: Skonfiguruj własne środowisko Integration Runtime jako serwer proxy dla usług SSIS
description: Dowiedz się, jak skonfigurować własne środowisko Integration Runtime jako serwer proxy dla Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/19/2020
ms.openlocfilehash: 5f393e95a7d468ea2c6130077e42b25b038e839d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376228"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Skonfiguruj własne środowisko IR jako serwer proxy dla Azure-SSIS IR w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób uruchamiania pakietów SQL Server Integration Services (SSIS) na Azure-SSIS Integration Runtime (Azure-SSIS IR) w Azure Data Factory z własnym hostowanym środowiskiem Integration Runtime (samoobsługowy IR) skonfigurowanym jako serwer proxy. 

Korzystając z tej funkcji, można uzyskać dostęp do danych lokalnie bez konieczności [przyłączania Azure-SSIS IR do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md). Ta funkcja jest przydatna, gdy sieć firmowa ma zbyt złożoną konfigurację lub że zasady są zbyt restrykcyjne, aby móc wstrzyknąć do niej Azure-SSIS IR.

Ta funkcja przerywa zadanie przepływu danych SSIS w dwóch zadaniach tymczasowych, gdy ma to zastosowanie: 
* **Lokalne zadanie przemieszczania**: to zadanie uruchamia składnik przepływu danych, który nawiązuje połączenie z lokalnym magazynem danych na własnym hostowanym środowisku IR. Przenosi dane z lokalnego magazynu danych do obszaru przejściowego w usłudze Azure Blob Storage lub na odwrót.
* **Zadanie przemieszczania w chmurze**: to zadanie służy do uruchamiania składnika przepływu danych, który nie łączy się z lokalnym magazynem danych w Azure-SSIS IR. Przenosi dane z obszaru przejściowego w usłudze Azure Blob Storage do magazynu danych w chmurze lub odwrotnie.

Jeśli zadanie przepływu danych przenosi dane z lokalizacji lokalnej do chmury, to pierwsze i drugie zadania etapowe będą odpowiednio wykonywane lokalnie i w chmurze. Jeśli zadanie przepływu danych przenosi dane z chmury do lokalnego, wówczas pierwsze i drugie zadania przemieszczania będą odpowiednio w chmurze i lokalnych zadaniach tymczasowych. Jeśli zadanie przepływu danych przenosi dane ze środowisk lokalnych do lokalnego, wówczas pierwsze i drugie zadania przemieszczania będą zarówno lokalnymi zadaniami przemieszczania. Jeśli zadanie przepływu danych przenosi dane z chmury do chmury, ta funkcja nie ma zastosowania.

Inne korzyści i możliwości tej funkcji pozwalają na przykład skonfigurować własne środowisko IR w regionach, które nie są jeszcze obsługiwane przez Azure-SSIS IR i zezwalać na publiczny statyczny adres IP własnego środowiska IR na zaporze źródeł danych.

## <a name="prepare-the-self-hosted-ir"></a>Przygotowywanie samodzielnego środowiska IR

Aby użyć tej funkcji, należy najpierw utworzyć fabrykę danych i skonfigurować w niej Azure-SSIS IR. Jeśli jeszcze tego nie zrobiono, postępuj zgodnie z instrukcjami podanymi w temacie [konfigurowanie Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

Następnie możesz skonfigurować własne środowisko IR w tej samej fabryce danych, w której skonfigurowano Azure-SSIS IR. Aby to zrobić, zobacz [Tworzenie własnego środowiska IR](./create-self-hosted-integration-runtime.md).

Na koniec pobierasz i instalujesz najnowszą wersję samoobsługowego środowiska IR, a także dodatkowe sterowniki i środowisko uruchomieniowe na maszynie lokalnej lub maszynie wirtualnej platformy Azure w następujący sposób:
- Pobierz i zainstaluj najnowszą wersję [samodzielnego środowiska IR](https://www.microsoft.com/download/details.aspx?id=39717).
- W przypadku używania łączników obiektów do łączenia i osadzania baz danych (OLEDB), Open Database Connectivity (ODBC) lub ADO.NET w pakietach, Pobierz i zainstaluj odpowiednie sterowniki na tym samym komputerze, na którym zainstalowano własne środowisko IR, jeśli jeszcze tego nie zrobiono.  

  Jeśli używasz starszej wersji sterownika OLEDB dla SQL Server (SQL Server Native Client [SQLNCLI]), [Pobierz wersję 64-bitową](https://www.microsoft.com/download/details.aspx?id=50402).  

  Jeśli używasz najnowszej wersji sterownika OLEDB dla SQL Server (MSOLEDBSQL), [Pobierz wersję 64-bitową](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Jeśli używasz sterowników OLEDB/ODBC/ADO. NET dla innych systemów baz danych, takich jak PostgreSQL, MySQL, Oracle i tak dalej, możesz pobrać 64-bitowe wersje z ich witryn sieci Web.
- Jeśli jeszcze tego nie zrobiono, [Pobierz i zainstaluj 64-bitową wersję środowiska uruchomieniowego Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) na tym samym komputerze, na którym zainstalowano własne środowisko IR.

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>Włącz uwierzytelnianie systemu Windows dla lokalnych zadań przemieszczania

Jeśli lokalne zadania przemieszczania w samoobsługowym środowisku IR wymagają uwierzytelniania systemu Windows, [Skonfiguruj pakiety usług SSIS w taki sposób, aby korzystały z tego samego uwierzytelniania systemu Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). 

Lokalne zadania przemieszczania będą wywoływane przy użyciu samodzielnego konta usługi IR (domyślnie *NT SERVICE\DIAHostService*), a Twoje magazyny danych będą dostępne przy użyciu konta uwierzytelniania systemu Windows. Oba konta wymagają przypisania pewnych zasad zabezpieczeń do nich. Na komputerze z własnym obsługą podczerwieni przejdź do pozycji **zasady zabezpieczeń lokalnych**  >  **Zasady lokalne**  >  **Przypisywanie praw użytkownika**, a następnie wykonaj następujące czynności:

1. Przypisz *limity przydziałów pamięci dla procesu* i *Zastąp zasady tokenów na poziomie procesu* do samodzielnego konta usługi IR. To powinno nastąpić automatycznie podczas instalacji własnego środowiska IR przy użyciu domyślnego konta usługi. Jeśli nie, przypisz te zasady ręcznie. W przypadku użycia innego konta usługi Przypisz do niego te same zasady.

1. Przypisz *Logowanie jako zasady usługi* do konta uwierzytelniania systemu Windows.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Przygotowywanie usługi połączonej Blob Storage platformy Azure na potrzeby przemieszczania

Jeśli jeszcze tego nie zrobiono, Utwórz połączoną usługę Azure Blob Storage w tej samej fabryce danych, w której skonfigurowano Azure-SSIS IR. Aby to zrobić, zobacz [Tworzenie połączonej usługi Azure Data Factory](./quickstart-create-data-factory-portal.md#create-a-linked-service). Pamiętaj, aby wykonać następujące czynności:
- W obszarze **Magazyn danych** wybierz pozycję **Azure Blob Storage**.  
- W przypadku **połączenia za pośrednictwem środowiska Integration Runtime** wybierz pozycję **AutoResolveIntegrationRuntime** (nie Azure-SSIS IR ani własne środowisko IR), ponieważ używamy domyślnego Azure IR do pobierania poświadczeń dostępu dla BLOB Storage platformy Azure.
- W **obszarze Metoda uwierzytelniania** wybierz pozycję **klucz konta**, **Identyfikator URI sygnatury dostępu współdzielonego**, **nazwę główną usługi** lub **tożsamość zarządzana**.  

>[!TIP]
>W przypadku wybrania metody **głównej usługi** Udziel nazwy głównej usługi co najmniej roli *współautor danych obiektu blob magazynu* . Aby uzyskać więcej informacji, zobacz [Łącznik usługi Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties). W przypadku wybrania metody **zarządzanej tożsamości** Udziel swojej tożsamości zarządzanej przez usługę ADF odpowiednie role, aby uzyskać dostęp do usługi Azure Blob Storage. Aby uzyskać więcej informacji, zobacz [dostęp do usługi Azure Blob Storage przy użyciu uwierzytelniania Azure Active Directory za pomocą tożsamości zarządzanej ADF](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication).

![Przygotowywanie usługi połączonej Azure Blob Storage do przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurowanie Azure-SSIS IR przy użyciu własnego środowiska IR jako serwera proxy

Po przygotowaniu samodzielnego środowiska IR i usługi połączonej Azure Blob Storage na potrzeby przemieszczania można teraz skonfigurować nowe lub istniejące Azure-SSIS IR przy użyciu samodzielnego środowiska IR jako serwera proxy w portalu lub aplikacji usługi Fabryka danych. Przed wykonaniem tej czynności, jeśli istniejący Azure-SSIS IR jest już uruchomiony, Zatrzymaj go, a następnie uruchom go ponownie.

1. W okienku **Konfiguracja środowiska Integration Runtime** Pomiń poprzednie sekcje **Ustawienia ogólne** i **Ustawienia SQL** , wybierając pozycję **dalej**. 

1. W sekcji **Ustawienia zaawansowane** wykonaj następujące czynności:

   1. Zaznacz pole wyboru **skonfiguruj Self-Hosted Integration Runtime jako serwer proxy dla Azure-SSIS Integration Runtime** . 

   1. Z listy rozwijanej **Samodzielna Integration Runtime** Wybierz istniejące własne środowisko IR jako serwer proxy dla Azure-SSIS IR.

   1. Z listy rozwijanej **połączona usługa magazynu przemieszczania** wybierz istniejącą usługę Azure Blob Storage lub Utwórz nową na potrzeby przemieszczania.

   1. W polu **ścieżka przemieszczania** Określ kontener obiektów BLOB na wybranym koncie usługi Azure Blob Storage lub pozostaw to pole puste, aby użyć domyślnego ustawienia do przemieszczania.

   1. Wybierz opcję **Kontynuuj**.

   ![Ustawienia zaawansowane przy użyciu samodzielnego środowiska IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Możesz również skonfigurować nowe lub istniejące Azure-SSIS IR za pomocą samoobsługowego środowiska IR jako serwera proxy przy użyciu programu PowerShell.

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Zezwól na łączenie pakietów SSIS z serwerem proxy

Przy użyciu najnowszej SSDT jako rozszerzenia projektów SSIS dla programu Visual Studio lub autonomicznego Instalatora, można znaleźć nową `ConnectByProxy` Właściwość, która została dodana w menedżerach połączeń dla obsługiwanych składników przepływu danych.
* [Pobierz rozszerzenie projekty SSIS dla programu Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Pobierz instalatora autonomicznego](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)   

Podczas projektowania nowych pakietów zawierających zadania przepływu danych ze składnikami, które uzyskują dostęp do danych lokalnie, można włączyć tę właściwość, ustawiając dla niej *wartość true* w okienku **Właściwości** odpowiednich menedżerów połączeń.

![Włącz Właściwość ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Możesz również włączyć tę właściwość, gdy uruchamiasz istniejące pakiety, bez konieczności ręcznego zmiany ich po jednym.  Dostępne są dwie opcje:
- **Opcja A**: Otwórz, Odbuduj i ponownie Wdróż projekt zawierający te pakiety z najnowszą SSDTą do uruchomienia na Azure-SSIS IR. Następnie można włączyć właściwość przez ustawienie dla odpowiednich menedżerów połączeń *wartości true* . W przypadku uruchamiania pakietów z programu SSMS menedżerowie połączeń są wyświetlani na karcie **menedżerowie połączeń** okna podręcznego **Wykonaj pakiet** .

  ![Włącz ConnectByProxy Property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Możesz również włączyć właściwość przez *ustawienie dla odpowiednich* menedżerów połączeń, które są wyświetlane na karcie **menedżerowie połączeń** w [działaniu pakiet SSIS](./how-to-invoke-ssis-package-ssis-activity.md) , gdy używasz pakietów w Data Factory potoków.
  
  ![Włącz ConnectByProxy Property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opcja B:** Wdróż ponownie projekt zawierający te pakiety do uruchomienia na urządzeniu SSIS IR. Następnie można włączyć właściwość, dostarczając jej ścieżkę właściwości, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` i ustawiając ją na *wartość true* jako przesłonięcie właściwości na karcie **Zaawansowane** w oknie podręcznym **Wykonaj pakiet** , gdy korzystasz z pakietów z programu SSMS.

  ![Włącz ConnectByProxy Property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Możesz również włączyć właściwość, dostarczając jej ścieżkę właściwości, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` i ustawiając ją na *wartość true* w ramach przesłonięcia właściwości na karcie **przesłonięcie** [działania pakietu SSIS](./how-to-invoke-ssis-package-ssis-activity.md) w przypadku uruchamiania pakietów w potokach Data Factory.
  
  ![Włącz ConnectByProxy Property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Debugowanie zadań przemieszczania lokalnego i w chmurze

Na własnym hostowanym środowisku IR można znaleźć dzienniki środowiska uruchomieniowego w folderze *C:\ProgramData\SSISTelemetry* oraz dzienniki wykonywania lokalnych zadań tymczasowych w folderze *C:\ProgramData\SSISTelemetry\ExecutionLog* .  Dzienniki wykonywania zadań przemieszczania w chmurze można znaleźć w SSISDB, określonych ścieżkach plików rejestrowania lub Azure Monitor w zależności od tego, czy pakiety są przechowywane w SSISDB, Włącz [integrację Azure monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)itp. W dziennikach wykonywania zadań przemieszczania w chmurze można również znaleźć unikatowe identyfikatory lokalnych zadań tymczasowych. 

![Unikatowy identyfikator pierwszego zadania przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

Jeśli zostały zgłoszone bilety obsługi klienta, możesz wybrać przycisk **Wyślij dzienniki** na karcie **diagnostyka** w **Microsoft Integration Runtime Configuration Manager** zainstalowanej na samoobsługowym środowisku IR, aby wysłać najnowsze dzienniki operacji/wykonywania.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Rozliczenia dla zadań przemieszczania lokalnego i w chmurze

Opłaty za lokalne zadania przemieszczania uruchamiane w ramach własnego środowiska IR są rozliczane osobno, podobnie jak wszystkie działania związane z przenoszeniem danych, które są uruchamiane na samoobsługowym środowisku IR, są rozliczane. Ta wartość jest określona w artykule [ceny potoku danych Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Zadania przemieszczania w chmurze, które są uruchamiane w Azure-SSIS IR nie są rozliczane oddzielnie, ale w przypadku Azure-SSIS IR, w którym jest naliczana wartość określona w artykule [cennik Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="enable-custom3rd-party-components"></a>Włącz składniki niestandardowe/inne firmy 

Aby włączyć składniki niestandardowe/inne firmy do uzyskiwania dostępu do danych lokalnych przy użyciu samodzielnego środowiska IR jako serwera proxy dla Azure-SSIS IR, wykonaj następujące instrukcje:

1. Zainstaluj składniki niestandardowe/innych firm ukierunkowane na SQL Server 2017 na Azure-SSIS IR za pośrednictwem [konfiguracji niestandardowych Standard/Express](./how-to-configure-azure-ssis-ir-custom-setup.md).

1. Utwórz następujące klucze rejestru DTSPath w samoobsługowym środowisku IR, jeśli jeszcze nie istnieją:
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` Ustaw na `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` Ustaw na `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. Zainstaluj składniki niestandardowe/inne firmy przeznaczone dla SQL Server 2017 na samoobsługowym środowisku IR w DTSPath powyżej i upewnij się, że proces instalacji:

   1. Tworzy `<DTSPath>` , `<DTSPath>/Connections` , `<DTSPath>/PipelineComponents` , i `<DTSPath>/UpgradeMappings` foldery, jeśli jeszcze nie istnieją.
   
   1. Tworzy własny plik XML dla mapowań rozszerzeń w `<DTSPath>/UpgradeMappings` folderze.
   
   1. Instaluje wszystkie zestawy, do których odwołują się zestawy składników niestandardowych/innych firm w globalnej pamięci podręcznej zestawów (GAC).

Poniżej przedstawiono przykłady naszych partnerów, [Theobald oprogramowania](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) i [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir), które dostosowują swoje składniki do korzystania z konfiguracji Express Custom i samodzielnego środowiska IR jako serwera proxy dla Azure-SSIS IR.

## <a name="enforce-tls-12"></a>Wymuszanie protokołu TLS 1.2

Jeśli musisz użyć silnego szyfrowania/bezpieczniejszego protokołu sieciowego (TLS 1,2) i wyłączyć starsze wersje protokołu SSL/TLS na własnym hostowanym środowisku IR, możesz pobrać i uruchomić główny skrypt *. cmd* , który znajduje się w folderze *CustomSetupScript/UserScenarios/TLS 1,2* naszego publicznego kontenera w wersji zapoznawczej.  Korzystając z [Eksplorator usługi Azure Storage](https://storageexplorer.com/), możesz nawiązać połączenie z naszym publicznym kontenerem w wersji zapoznawczej, wprowadzając następujący identyfikator URI SAS:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Bieżące ograniczenia

- Obecnie obsługiwane są tylko składniki przepływu danych wbudowane/preinstalowane w systemie Azure-SSIS IR Standard Edition, z wyjątkiem składników Hadoop/HDFS/DQS, zobacz [wszystkie wbudowane/preinstalowane składniki w Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).
- Obecnie obsługiwane są tylko składniki przepływu danych inne niż niestandardowe/inne firmy, które są zapisywane w kodzie zarządzanym (.NET Framework) — te w kodzie natywnym (C++) nie są obecnie obsługiwane.
- Zmiana wartości zmiennych w zadaniach tymczasowych i w chmurze nie jest obecnie obsługiwana.
- Zmiana wartości zmiennych typu Object w lokalnych zadaniach tymczasowych nie zostanie odzwierciedlona w innych zadaniach.
- *ParameterMapping* w źródle OLEDB nie jest obecnie obsługiwany. Aby obejść ten element, użyj *polecenia SQL ze zmiennej* jako *AccessMode* i USE *Expression* , aby wstawić zmienne/parametry do polecenia SQL. Jako ilustracja Zobacz pakiet *ParameterMappingSample. dtsx* , który można znaleźć w folderze *SelfHostedIRProxy/ograniczenia* naszego publicznego kontenera w wersji zapoznawczej. Korzystając z Eksplorator usługi Azure Storage, możesz nawiązać połączenie z naszym publicznym kontenerem w wersji zapoznawczej, wprowadzając powyższy identyfikator URI SAS.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu własnego środowiska IR jako serwera proxy dla Azure-SSIS IR można wdrożyć i uruchomić pakiety w celu uzyskania dostępu do danych lokalnych jako działania pakietu SSIS w Data Factory potoków. Aby dowiedzieć się, jak to zrobić, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w Data Factory potoków](./how-to-invoke-ssis-package-ssis-activity.md).