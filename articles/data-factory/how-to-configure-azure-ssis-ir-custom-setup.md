---
title: Dostosuj konfigurację dla Azure-SSIS Integration Runtime
description: W tym artykule opisano sposób korzystania z niestandardowego interfejsu Instalatora dla Azure-SSIS Integration Runtime instalowania dodatkowych składników lub zmiany ustawień
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: ab2ba31d6b712bd3399bc8bf5b491337d462dac9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606217"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Dostosuj konfigurację dla Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Konfiguracja niestandardowa dla Integration Runtime platformy Azure SQL Server Integration Services (Azure-SSIS IR) udostępnia interfejs do dodawania własnych kroków podczas instalacji lub ponownej konfiguracji Azure-SSIS IR. 

Korzystając z konfiguracji niestandardowej, można zmienić domyślną konfigurację lub środowisko na, na przykład, uruchomić dodatkowe usługi systemu Windows, zachować poświadczenia dostępu do udziałów plików lub użyć silnego kryptografii/bezpieczniejszego protokołu sieciowego (TLS 1,2). Można też zainstalować dodatkowe składniki, takie jak zestawy, sterowniki lub rozszerzenia, na każdym węźle Azure-SSIS IR.

Niestandardowe konfiguracje można wykonywać na Azure-SSIS IR na jeden z dwóch sposobów: 
* **Instalacja niestandardowa Express bez skryptu**: uruchamianie niektórych typowych konfiguracji systemu i poleceń systemu Windows lub instalowanie niektórych popularnych lub zalecanych dodatkowych składników bez użycia skryptów.
* **Standardowa Konfiguracja niestandardowa z skryptem**: Przygotuj skrypt i skojarzone z nim pliki i przekaż je ze sobą do kontenera obiektów BLOB na koncie usługi Azure Storage. Następnie należy podać Uniform Resource Identifier sygnatury dostępu współdzielonego (SAS) dla kontenera podczas konfigurowania lub zmiany konfiguracji Azure-SSIS IR. Każdy węzeł Azure-SSIS IR następnie pobiera skrypt i skojarzone z nim pliki z Twojego kontenera i uruchamia konfigurację niestandardową z podniesionymi uprawnieniami. Po zakończeniu instalacji niestandardowej każdy węzeł przekazuje standardowe dane wyjściowe wykonywania i innych dzienników do kontenera.

Można zainstalować zarówno bezpłatne, nielicencjonowane składniki i płatne składniki licencjonowane za pomocą instalacji niestandardowych Express i Standard. Jeśli jesteś niezależnym dostawcą oprogramowania (ISV), zobacz [opracowywanie składników płatnych lub licencjonowanych dla Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Aby skorzystać z przyszłych ulepszeń, zalecamy korzystanie z serii dla Azure-SSIS IR w wersji 3 lub nowszej z konfiguracją niestandardową.

## <a name="current-limitations"></a>Bieżące ograniczenia

Następujące ograniczenia mają zastosowanie tylko do standardowych konfiguracji niestandardowych:

- Jeśli chcesz użyć *Gacutil. exe* w skrypcie do instalacji zestawów w globalnej pamięci podręcznej zestawów (GAC), musisz podać *Gacutil. exe* jako część konfiguracji niestandardowej. Możesz też użyć kopii podanej w naszym kontenerze *publicznej wersji zapoznawczej* , omówionej dalej w sekcji "instrukcje".

- Jeśli chcesz odwołać się do podfolderu w skrypcie, *msiexec. exe* nie obsługuje `.\` notacji, aby odwołać się do folderu głównego. Użyj polecenia, takiego jak `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` zamiast `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Udziały administracyjne lub ukryte udziały sieciowe, które są tworzone automatycznie przez system Windows, nie są obecnie obsługiwane w Azure-SSIS IR.

- Sterownik ODBC IBM iSeries Access nie jest obsługiwany w Azure-SSIS IR. Podczas instalacji niestandardowej mogą pojawić się błędy instalacji. W takim przypadku skontaktuj się z pomocą techniczną firmy IBM w celu uzyskania pomocy.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby dostosować Azure-SSIS IR, potrzebne są następujące elementy:

- [Subskrypcja platformy Azure](https://azure.microsoft.com/)

- [Inicjowanie obsługi administracyjnej Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/). Niewymagane w przypadku instalacji niestandardowych Express. W przypadku standardowych konfiguracji niestandardowych można przekazać i zapisać niestandardowy skrypt instalacyjny oraz skojarzone z nim pliki w kontenerze obiektów BLOB. Proces instalacji niestandardowej umożliwia również przekazanie dzienników wykonywania do tego samego kontenera obiektów BLOB.

## <a name="instructions"></a>Instrukcje

1. Jeśli chcesz skonfigurować lub zmienić konfigurację Azure-SSIS IR przy użyciu programu PowerShell, Pobierz i zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps). W przypadku instalacji niestandardowych Express przejdź do kroku 4.

1. Przygotowanie niestandardowego skryptu instalacji i skojarzonych z nim plików (na przykład. bat,. cmd,. exe,. dll,. msi lub pliki ps1).

   * Musisz mieć plik skryptu o nazwie *Main. cmd*, który jest punktem wejścia konfiguracji niestandardowej.  
   * Aby upewnić się, że skrypt może być wykonywany w trybie dyskretnym, zalecamy najpierw przetestowanie go na komputerze lokalnym.  
   * Jeśli chcesz, aby dodatkowe dzienniki wygenerowane przez inne narzędzia (na przykład *msiexec. exe*) zostały przekazane do kontenera, określ wstępnie zdefiniowaną zmienną `CUSTOM_SETUP_SCRIPT_LOG_DIR`środowiskową, jako folder dziennika w skryptach (na przykład *msiexec/i XXX. msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Pobierz, zainstaluj i Otwórz [Eksplorator usługi Azure Storage](https://storageexplorer.com/). W tym celu:

   a. W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu**, a następnie wybierz pozycję **Połącz z usługą Azure Storage**.

      ![Łączenie z usługą Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Wybierz pozycję **Użyj nazwy i klucza konta magazynu**, a następnie wybierz przycisk **dalej**.

      ![Używanie nazwy i klucza konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Wprowadź nazwę i klucz konta usługi Azure Storage, wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

      ![Podaj nazwę i klucz konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. W obszarze połączone konto usługi Azure Storage kliknij prawym przyciskiem myszy pozycję **kontenery obiektów BLOB**, wybierz pozycję **Utwórz kontener obiektów BLOB**i nazwij nowy kontener.

      ![Tworzenie kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Wybierz nowy kontener i przekaż niestandardowy skrypt instalacji oraz skojarzone z nim pliki. Upewnij się, że przekazujesz *Main. cmd* na najwyższym poziomie kontenera, a nie w żadnym folderze. Upewnij się również, że kontener zawiera tylko niezbędne pliki instalacji niestandardowej, dzięki czemu pobieranie ich do Azure-SSIS IR później nie zajmie dużo czasu. Maksymalny czas trwania konfiguracji niestandardowej jest obecnie ustawiony na 45 minut przed upływem limitu czasu. Obejmuje to czas pobierania wszystkich plików z kontenera i instalowania ich na Azure-SSIS IR. Jeśli Instalator wymaga więcej czasu, zgłoś bilet pomocy technicznej.

      ![Przekazywanie plików do kontenera obiektów BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**.

      ![Uzyskaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Utwórz identyfikator URI sygnatury dostępu współdzielonego dla kontenera o wystarczająco długim czasie wygaśnięcia oraz uprawnienia do odczytu/zapisu/listy. Wymagany jest identyfikator URI sygnatury dostępu współdzielonego, który umożliwia pobranie i uruchomienie niestandardowego skryptu instalacji i skojarzonych z nim plików za każdym razem, gdy dowolny węzeł Azure-SSIS IR zostanie odgraficzny lub uruchomiony ponownie. Musisz mieć uprawnienia do zapisu w celu przekazania dzienników wykonywania Instalatora.

      > [!IMPORTANT]
      > Upewnij się, że identyfikator URI sygnatury dostępu współdzielonego nie wygasa, a niestandardowe zasoby instalacyjne są zawsze dostępne w całym cyklu życia Azure-SSIS IR, od tworzenia do usunięcia, zwłaszcza jeśli regularnie zatrzymasz i uruchomisz Azure-SSIS IR w tym okresie.

      ![Generowanie sygnatury dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Skopiuj i Zapisz identyfikator URI sygnatury dostępu współdzielonego kontenera.

      ![Kopiowanie i zapisywanie sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Po skonfigurowaniu lub ponownym skonfigurowaniu Azure-SSIS IR za pomocą interfejsu użytkownika fabryki danych można dodać lub usunąć niestandardowe ustawienia, zaznaczając pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** w sekcji **Ustawienia zaawansowane** w okienku **Konfiguracja środowiska Integration Runtime** . 

   Jeśli chcesz dodać standardowe konfiguracje niestandardowe, wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera w polu **Identyfikator URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych** . 
   
   Jeśli chcesz dodać niestandardowe ustawienia instalacji, wybierz pozycję **Nowy** , aby otworzyć okienko **Dodaj ekspresową instalację niestandardową** , a następnie wybierz typ z listy rozwijanej **Typ instalacji niestandardowej** :

   * W przypadku wybrania typu **polecenia Uruchom cmdkey** można zachować poświadczenia dostępu do udziałów plików lub Azure Files udziały w Azure-SSIS IR, wprowadzając dodaną nazwę komputera lub nazwę domeny, nazwę konta lub nazwę użytkownika oraz klucz konta lub hasło w polach **/Add**, **/User**i **/Pass** . Jest to podobne do uruchamiania polecenia Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) na komputerze lokalnym.
   
   * Jeśli zostanie wybrana opcja **Dodaj typ zmiennej środowiskowej** , można dodać zmienne środowiskowe systemu Windows do użycia w pakietach, które są uruchamiane na Azure-SSIS IR, wprowadzając nazwę zmiennej środowiskowej i wartość w polach **Nazwa zmiennej** i **wartość zmiennej** . Jest to podobne do uruchamiania polecenia [Set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) systemu Windows na komputerze lokalnym.

   * Jeśli wybierzesz opcję **Zainstaluj licencjonowany typ składnika** , możesz wybrać zintegrowany składnik od partnerów niezależnego dostawcy oprogramowania z listy rozwijanej **Nazwa składnika** :

     * W przypadku wybrania składnika **fabryki zadań SentryOne** można zainstalować pakiet [fabryk zadań](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) składników z SentryOne na Azure-SSIS IR, wprowadzając klucz licencji produktu zakupiony z nich w polu **klucz licencji** . Aktualna wersja zintegrowana to **2019.4.3**.

     * W przypadku wybrania **OH22'S HEDDA. Składnik we/wy** , można zainstalować [HEDDA. ](https://hedda.io/ssis-component/)Składnik jakości/oczyszczania danych we/wy z oh22 na Azure-SSIS IR po zakupie usługi. Aktualna wersja zintegrowana to **1.0.13**.

     * W przypadku wybrania składnika **oh22's SQLPhonetics.NET** można zainstalować składnik [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) Data/dopasowanie z oh22 na Azure-SSIS IR, wprowadzając klucz licencji produktu zakupiony z nich w polu **klucz licencji** . Aktualna wersja zintegrowana to **1.0.43**.

     * W przypadku wybrania składnika **KingswaySoft usługi SSIS Integration** Toolkit można zainstalować pakiet [narzędzi SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) dla aplikacji CRM/ERP/Marketing/Collaboration, takich jak Microsoft Dynamics/SharePoint/Project Server, Chmura marketingowa Oracle/Salesforce, itp. z KingswaySoft na Azure-SSIS IR, wprowadzając klucz licencji produktu nabyty z nich w polu **klucz licencji** . Bieżąca wersja zintegrowana to **2019,2**.

     * W przypadku wybrania składnika **pakietu do produkcji SSIS KingswaySoft** można zainstalować pakiet usług [SSIS pakietu Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) dla składników z KingswaySoft na Azure-SSIS IR przez wprowadzenie klucza licencji produktu zakupionego z nich w polu **klucz licencji** . Bieżąca wersja zintegrowana to **10,0**.

     * W przypadku wybrania składnika **Yodrębnij oprogramowania Theobald** można zainstalować [yodrębnij to](https://theobald-software.com/en/xtract-is/) pakiet łączników dla systemu SAP system (ERP, s/4HANA, BW) firmy Theobald na Azure-SSIS IR przez przeciąganie & upuszczenie/przekazanie pliku licencji produktu zakupionego z nich w polu **plik licencji** . Aktualna wersja zintegrowana to **6.1.1.3**.

   Dodane ustawienia Express Custom Setup zostaną wyświetlone w sekcji **Ustawienia zaawansowane** . Aby je usunąć, zaznacz ich pola wyboru, a następnie wybierz pozycję **Usuń**.

   ![Ustawienia zaawansowane z konfiguracjami niestandardowymi](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Po skonfigurowaniu lub ponownym skonfigurowaniu Azure-SSIS IR przy użyciu programu PowerShell można dodać lub usunąć niestandardowe ustawienia, uruchamiając `Set-AzDataFactoryV2IntegrationRuntime` polecenie cmdlet przed rozpoczęciem Azure-SSIS IR.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   Po zakończeniu standardowej instalacji niestandardowej i rozpoczęciu Azure-SSIS IR można znaleźć standardowe dane wyjściowe *głównego. cmd* i innych dzienników wykonywania w folderze *Main. cmd. log* kontenera magazynu.

1. Aby wyświetlić przykłady standardowych konfiguracji niestandardowych, Połącz się z naszym publicznym kontenerem w wersji zapoznawczej przy użyciu Eksplorator usługi Azure Storage.

   a. W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu**, wybierz pozycję **Połącz z usługą Azure Storage**, wybierz polecenie **Użyj parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego**, a następnie wybierz przycisk **dalej**.

      ![Nawiązywanie połączenia z usługą Azure Storage za pomocą sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Wybierz pozycję **Użyj identyfikatora URI sygnatury dostępu współdzielonego** , a następnie w polu **Identyfikator URI** wprowadź następujący identyfikator URI sygnatury dostępu współdzielonego:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Podaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

   d. W lewym okienku wybierz kontener podłączony **publicpreview** , a następnie kliknij dwukrotnie folder *CustomSetupScript* . W tym folderze znajdują się następujące elementy:

      * *Przykładowy* folder zawierający niestandardową konfigurację służącą do instalowania podstawowego zadania na każdym węźle Azure-SSIS IR. Zadanie nie wykonuje żadnych operacji, ale w stanie uśpienia przez kilka sekund. Folder zawiera również folder *Gacutil* , którego cała zawartość (*Gacutil. exe*, *Gacutil. exe. config*i *1033 \ gacutlrc. dll*) może zostać skopiowana do kontenera.

      * Folder *UserScenarios* , który zawiera kilka przykładów instalacji niestandardowej z scenariuszy użytkowników rzeczywistych.

        ![Zawartość kontenera publicznej wersji zapoznawczej](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Kliknij dwukrotnie folder *UserScenarios* , aby znaleźć następujące elementy:

      * Folder *programu .NET FRAMEWORK 3,5* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*), aby zainstalować wcześniejszą wersję .NET Framework, która może być wymagana dla składników niestandardowych w każdym węźle Azure-SSIS IR.

      * Folder *BCP* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania SQL Server narzędzi wiersza polecenia (*MsSqlCmdLnUtils. msi*), w tym programu do kopiowania zbiorczego (*BCP*), na każdym węźle Azure-SSIS IR.

      * Folder *programu Excel* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) do instalowania zestawów i bibliotek języka C#, których można użyć w zadaniach skryptów do dynamicznego odczytywania i zapisywania plików programu EXCEL w każdym węźle Azure-SSIS IR. 
      
        Najpierw pobierz [*ExcelDataReader. dll*](https://www.nuget.org/packages/ExcelDataReader/) i [*DocumentFormat. OPENXML. dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), a następnie Przekaż je wszystkie razem z *Main. cmd* do kontenera. Alternatywnie, jeśli chcesz używać tylko standardowego Menedżera połączeń programu Excel, źródła programu Excel i miejsca docelowego programu Excel, wymagany pakiet redystrybucyjny dostępu jest już wstępnie zainstalowany na Azure-SSIS IR, więc nie jest potrzebna żadna Konfiguracja niestandardowa.
      
      * Folder *ODBC programu MySQL* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania sterowników ODBC MYSQL na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem MySQL za pomocą Menedżera połączeń ODBC, źródła i miejsca docelowego. 
     
        Najpierw [Pobierz najnowszą wersję 64-bitową i 32-bitową instalatorów sterowników ODBC MySQL](https://dev.mysql.com/downloads/connector/odbc/) (na przykład *MySQL-Connector-ODBC-8.0.13-Winx64. msi* i *MySQL-Connector-ODBC-8.0.13-Win32. msi*), a następnie Przekaż je wszystkie razem z *głównym. cmd* do kontenera.

      * Folder *Oracle Enterprise* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) i plik konfiguracji instalacji dyskretnej (*Client. rsp*) w celu zainstalowania łączników Oracle i sterownika OCI na każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem Oracle przy użyciu Menedżera połączeń Oracle, źródła i miejsca docelowego. 
      
        Najpierw pobierz łączniki firmy Microsoft w wersji 5.0 dla programu Oracle (*AttunitySSISOraAdaptersSetup. msi* i *AttunitySSISOraAdaptersSetup64. msi*) z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) i najnowszego klienta Oracle (na przykład *Winx64_12102_client. zip*) z [bazy danych Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), a następnie Przekaż je wszystkie wraz z *Main. cmd* i *Client. rsp* do kontenera. Jeśli używasz TNS do łączenia się z bazą danych Oracle, musisz również pobrać plik *pliku tnsnames. ora*, edytować go i przekazać do kontenera, aby można go było skopiować do folderu instalacyjnego programu Oracle podczas instalacji.

      * Folder *standard ADO.NET firmy Oracle* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania sterownika Oracle ODP.NET na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle przy użyciu Menedżera połączeń ADO.NET, źródła i miejsca docelowego. 
      
        Najpierw [Pobierz najnowszy sterownik Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (na przykład *Odp. NET_Managed_ODAC122cR1. zip*), a następnie Przekaż go wraz z *Main. cmd* do kontenera.
       
      * *Standardowy folder ODBC programu Oracle* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*), aby zainstalować sterownik Oracle ODBC i skonfigurować nazwę źródła danych (DSN) w każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia użycie Menedżera połączeń ODBC, źródła i miejsca docelowego lub Power Query Menedżera połączeń i źródła z typem źródła danych ODBC, aby połączyć się z serwerem Oracle. 
      
        Najpierw pobierz najnowszą wersję programu Oracle Instant Client (pakiet podstawowy lub podstawowy pakiet w języku Basic) i pakiet ODBC, a następnie Przekaż je razem z *głównym programem. cmd* do kontenera:
        * [Pobierz pakiety 64-bitowe](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pakiet podstawowy: *instantclient-Basic-Windows. x64-18.3.0.0.0 dbru. zip*; Podstawowy pakiet w wersji Lite: *instantclient-basiclite-Windows. x64-18.3.0.0.0 dbru. zip*; Pakiet ODBC: *instantclient-ODBC-Windows. x64-18.3.0.0.0 dbru. zip*) 
        * [Pobierz pakiety 32-bitowe](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pakiet podstawowy: *instantclient-Basic-NT-18.3.0.0.0 dbru. zip*; Podstawowy pakiet w wersji Lite: *instantclient-basiclite-NT-18.3.0.0.0 dbru. zip*; Pakiet ODBC: *instantclient-ODBC-NT-18.3.0.0.0 dbru. zip*)

      * *Standardowy folder OLEDB firmy Oracle* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*), aby zainstalować sterownik Oracle OLEDB na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle przy użyciu Menedżera połączeń OLEDB, źródła i miejsca docelowego. 
     
        Najpierw [Pobierz najnowszy sterownik Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (na przykład *ODAC122010Xcopy_x64. zip*), a następnie Przekaż go wraz z *Main. cmd* do kontenera.

      * Folder *POSTGRESQL ODBC* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania sterowników ODBC POSTGRESQL na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem PostgreSQL za pomocą Menedżera połączeń ODBC, źródła i miejsca docelowego. 
     
        Najpierw [Pobierz najnowszą wersję 64-bitową i 32-bitową instalatorów sterowników ODBC PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (na przykład *psqlodbc_x64. msi* i *psqlodbc_x86. msi*), a następnie Przekaż je wszystkie wraz z *Main. cmd* do kontenera.

      * Folder *SAP BW* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania zestawu SAP .NET Connector (*librfc32. dll*) w każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem SAP BW przy użyciu Menedżera połączeń SAP Business Warehouse (BW), źródła i miejsca docelowego. 
      
        Najpierw przekaż 64-bitową lub 32-bitową wersję *librfc32. dll* z folderu instalacji SAP wraz z *Main. cmd* do kontenera. Skrypt następnie kopiuje zestaw SAP do folderu *%windir%\syswow64* lub *%windir%\system32* podczas instalacji.

      * Folder *magazynu* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania Azure PowerShell na każdym węźle Azure-SSIS IR. Dzięki tej konfiguracji można wdrażać i uruchamiać pakiety usług SSIS, które uruchamiają [skrypty programu PowerShell w celu manipulowania kontem usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Skopiuj plik *Main. cmd*, przykład *AzurePowerShell. msi* (lub użyj najnowszej wersji) i *Storage. ps1* do kontenera. Użyj *programu PowerShell. dtsx* jako szablonu dla pakietów. Szablon pakietu łączy [zadanie pobierania obiektów blob platformy Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), które pobiera *Magazyn. ps1* jako modyfikowalny skrypt programu PowerShell i [zadanie wykonywania procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), które wykonuje skrypt w każdym węźle.

      * Folder programu *Teradata* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*), skojarzony z nim plik (*install. cmd*) i pakiety Instalatora (*MSI*). Te pliki instalują łączniki programu Teradata, interfejs API Parallel transportowy (TPT) programu Teradata i sterownik ODBC na każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem Teradata przy użyciu Menedżera połączeń programu Teradata, źródła i miejsca docelowego. 
      
        Najpierw [Pobierz narzędzia programu Teradata i pliki zip 15. x](http://partnerintelligence.teradata.com) (na przykład *TeradataToolsAndUtilitiesBase__windows_indep. 15.10.22.00. zip*), a następnie Przekaż je wraz z wymienionymi wcześniej plikami *cmd* i *MSI* do kontenera.

      * Folder *TLS 1,2* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) do korzystania z mocnej kryptografii/bezpieczniejszego protokołu sieciowego (TLS 1,2) i Wyłącz starsze wersje protokołu SSL/TLS w każdym węźle Azure-SSIS IR.

      * Folder *Zulu OPENJDK* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) i plik programu PowerShell (*install_openjdk. ps1*), aby zainstalować Zulu OPENJDK na każdym węźle Azure-SSIS IR. Ta konfiguracja pozwala używać Azure Data Lake Store i elastycznych łączników plików do przetwarzania plików ORC i Parquet. Aby uzyskać więcej informacji, zobacz [pakiet Azure Feature Pack dla usług Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Najpierw [Pobierz najnowszą Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (na przykład *Zulu 8.33.0.1-jdk 8.0.192-win_x64. zip*), a następnie Przekaż go razem z *Main. cmd* i *install_openjdk. ps1* do kontenera.

        ![Foldery w folderze scenariusze użytkownika](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Aby wypróbować te próbki instalacji niestandardowej, skopiuj zawartość z wybranego folderu do kontenera.
   
      Po skonfigurowaniu lub ponownym skonfigurowaniu Azure-SSIS IR przy użyciu interfejsu użytkownika Data Factory, zaznacz pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** w sekcji **Ustawienia zaawansowane** , a następnie wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera w polu **Identyfikator URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych** .
   
      Po skonfigurowaniu lub ponownym skonfigurowaniu Azure-SSIS IR przy użyciu programu PowerShell uruchom `Set-AzDataFactoryV2IntegrationRuntime` polecenie cmdlet z identyfikatorem URI sygnatury dostępu współdzielonego `SetupScriptContainerSasUri` kontenera jako wartość parametru.

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj wersję Enterprise Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Rozwijaj płatne lub licencjonowane składniki niestandardowe dla Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
