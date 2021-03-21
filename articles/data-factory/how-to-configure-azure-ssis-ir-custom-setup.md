---
title: Dostosuj konfigurację dla Azure-SSIS Integration Runtime
description: W tym artykule opisano sposób korzystania z niestandardowego interfejsu Instalatora dla Azure-SSIS Integration Runtime instalowania dodatkowych składników lub zmiany ustawień
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387737"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Dostosuj konfigurację dla Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Możesz dostosować swoje Integration Runtime platformy Azure — SQL Server Integration Services (SSIS) w Azure Data Factory (ADF) za pośrednictwem konfiguracji niestandardowych. Umożliwiają one Dodawanie własnych kroków podczas aprowizacji lub ponownej konfiguracji Azure-SSIS IR. 

Za pomocą konfiguracji niestandardowych można zmienić domyślną konfigurację lub środowisko Azure-SSIS IR. Na przykład aby uruchomić dodatkowe usługi systemu Windows, należy zachować poświadczenia dostępu do udziałów plików lub użyć silnego szyfrowania/bezpieczniejszego protokołu sieciowego (TLS 1,2). Można też zainstalować dodatkowe składniki, takie jak zestawy, sterowniki lub rozszerzenia, na każdym węźle Azure-SSIS IR. Mogą to być składniki niestandardowe, Open Source lub inne firmy. Aby uzyskać więcej informacji na temat wbudowanych/preinstalowanych składników, zobacz [składniki wbudowane/preinstalowane na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).

Niestandardowe konfiguracje można wykonywać na Azure-SSIS IR na jeden z dwóch sposobów: 
* **Standardowa Konfiguracja niestandardowa z skryptem**: Przygotuj skrypt i skojarzone z nim pliki i przekaż je ze sobą do kontenera obiektów BLOB na koncie usługi Azure Storage. Następnie należy podać Uniform Resource Identifier sygnatury dostępu współdzielonego (SAS) dla kontenera podczas konfigurowania lub zmiany konfiguracji Azure-SSIS IR. Każdy węzeł Azure-SSIS IR następnie pobiera skrypt i skojarzone z nim pliki z Twojego kontenera i uruchamia konfigurację niestandardową z podniesionymi uprawnieniami. Po zakończeniu instalacji niestandardowej każdy węzeł przekazuje standardowe dane wyjściowe wykonywania i innych dzienników do kontenera.
* **Instalacja niestandardowa Express bez skryptu**: uruchamianie niektórych typowych konfiguracji systemu i poleceń systemu Windows lub instalowanie niektórych popularnych lub zalecanych dodatkowych składników bez użycia skryptów.

Można zainstalować zarówno bezpłatne (nielicencjonowane) i płatne (licencjonowane) składniki z konfiguracjami standardowymi i ekspresowymi. Jeśli jesteś niezależnym dostawcą oprogramowania (ISV), zobacz temat [Tworzenie składników płatnych lub licencjonowanych dla Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Aby skorzystać z przyszłych ulepszeń, zalecamy korzystanie z serii dla Azure-SSIS IR w wersji 3 lub nowszej z konfiguracją niestandardową.

## <a name="current-limitations"></a>Bieżące ograniczenia

Następujące ograniczenia mają zastosowanie tylko do standardowych konfiguracji niestandardowych:

- Jeśli chcesz użyć *gacutil.exe* w skrypcie do zainstalowania zestawów w globalnej pamięci podręcznej zestawów (GAC), musisz podać *gacutil.exe* w ramach konfiguracji niestandardowej. Możesz też użyć kopii podanej w *przykładowym* folderze naszego publicznego kontenera w *wersji zapoznawczej* , aby zapoznać się z sekcją **standardowe przykłady instalacji niestandardowej** poniżej.

- Jeśli chcesz odwołać się do podfolderu w skrypcie, *msiexec.exe* nie obsługuje `.\` notacji, aby odwołać się do folderu głównego. Użyj polecenia, takiego jak `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` zamiast `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- Udziały administracyjne lub ukryte udziały sieciowe, które są tworzone automatycznie przez system Windows, nie są obecnie obsługiwane w Azure-SSIS IR.

- Sterownik ODBC IBM iSeries Access nie jest obsługiwany w Azure-SSIS IR. Podczas instalacji niestandardowej mogą pojawić się błędy instalacji. W takim przypadku skontaktuj się z pomocą techniczną firmy IBM w celu uzyskania pomocy.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby dostosować Azure-SSIS IR, potrzebne są następujące elementy:

- [Subskrypcja platformy Azure](https://azure.microsoft.com/)

- [Inicjowanie obsługi administracyjnej Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)

- [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/). Niewymagane w przypadku instalacji niestandardowych Express. W przypadku standardowych konfiguracji niestandardowych można przekazać i zapisać niestandardowy skrypt instalacyjny oraz skojarzone z nim pliki w kontenerze obiektów BLOB. Proces instalacji niestandardowej umożliwia również przekazanie dzienników wykonywania do tego samego kontenera obiektów BLOB.

## <a name="instructions"></a>Instrukcje

Możesz zainicjować lub zmienić konfigurację Azure-SSIS IR przy użyciu konfiguracji niestandardowych w interfejsie użytkownika funkcji ADF. Jeśli chcesz zrobić to samo przy użyciu programu PowerShell, Pobierz i zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Standardowa Konfiguracja niestandardowa

W celu aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu standardowych konfiguracji niestandardowych w interfejsie użytkownika funkcji ADF wykonaj następujące czynności.

1. Przygotowanie niestandardowego skryptu instalacji i skojarzonych z nim plików (na przykład. bat,. cmd,. exe,. dll,. msi lub pliki ps1).

   * Musisz mieć plik skryptu o nazwie *Main. cmd*, który jest punktem wejścia konfiguracji niestandardowej.  
   * Aby upewnić się, że skrypt może być wykonywany dyskretnie, należy najpierw go przetestować na komputerze lokalnym.  
   * Jeśli chcesz, aby dodatkowe dzienniki wygenerowane przez inne narzędzia (na przykład *msiexec.exe*) zostały przekazane do kontenera, określ wstępnie zdefiniowaną zmienną środowiskową, `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako folder dziennika w skryptach (na przykład *msiexec/i xxx.msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Pobierz, zainstaluj i Otwórz [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

   a. W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu**, a następnie wybierz pozycję **Połącz z usługą Azure Storage**.

      ![Łączenie z usługą Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Wybierz pozycję **Użyj nazwy i klucza konta magazynu**, a następnie wybierz przycisk **dalej**.

      ![Używanie nazwy i klucza konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Wprowadź nazwę i klucz konta usługi Azure Storage, wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

      ![Podaj nazwę i klucz konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. W obszarze połączone konto usługi Azure Storage kliknij prawym przyciskiem myszy pozycję **kontenery obiektów BLOB**, wybierz pozycję **Utwórz kontener obiektów BLOB** i nazwij nowy kontener.

      ![Tworzenie kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Wybierz nowy kontener i przekaż niestandardowy skrypt instalacji oraz skojarzone z nim pliki. Upewnij się, że przekazujesz *Main. cmd* na najwyższym poziomie kontenera, a nie w żadnym folderze. Kontener powinien zawierać tylko niezbędne pliki instalacji niestandardowej, więc pobranie ich do Azure-SSIS IR później nie zajmie dużo czasu. Maksymalny czas trwania konfiguracji niestandardowej jest obecnie ustawiony na 45 minut przed upływem limitu czasu. Obejmuje to czas pobierania wszystkich plików z kontenera i instalowania ich na Azure-SSIS IR. Jeśli Instalator wymaga więcej czasu, zgłoś bilet pomocy technicznej.

      ![Przekazywanie plików do kontenera obiektów BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**.

      ![Uzyskaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   przykład Utwórz identyfikator URI sygnatury dostępu współdzielonego dla kontenera o wystarczająco długim czasie wygaśnięcia oraz uprawnienia do odczytu/zapisu/listy. Wymagany jest identyfikator URI sygnatury dostępu współdzielonego, który umożliwia pobranie i uruchomienie niestandardowego skryptu instalacji i skojarzonych z nim plików. Dzieje się tak, gdy dowolny węzeł Azure-SSIS IR zostanie odgraficzny lub ponownie uruchomiony. Musisz również mieć uprawnienia do zapisu w celu przekazania dzienników wykonywania Instalatora.

      > [!IMPORTANT]
      > Upewnij się, że identyfikator URI sygnatury dostępu współdzielonego nie wygasa, a niestandardowe zasoby instalacyjne są zawsze dostępne w całym cyklu życia Azure-SSIS IR, od tworzenia do usunięcia, zwłaszcza jeśli regularnie zatrzymasz i uruchomisz Azure-SSIS IR w tym okresie.

      ![Generowanie sygnatury dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Skopiuj i Zapisz identyfikator URI sygnatury dostępu współdzielonego kontenera.

      ![Kopiowanie i zapisywanie sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Zaznacz pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** na stronie **Ustawienia zaawansowane** w okienku **Konfiguracja środowiska Integration Runtime** . Następnie wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera w polu tekstowym **Identyfikator URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych** .

   ![Ustawienia zaawansowane z konfiguracjami niestandardowymi](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

Po zakończeniu standardowej instalacji niestandardowej i uruchomieniu Azure-SSIS IR można znaleźć wszystkie niestandardowe dzienniki instalacji w folderze *Main. cmd. log* kontenera. Obejmują one standardowe dane wyjściowe *Main. cmd* i innych dzienników wykonania.

### <a name="express-custom-setup"></a>Konfiguracja niestandardowa Express

W celu aprowizacji lub zmiany konfiguracji Azure-SSIS IR za pomocą instalacji niestandardowych Express w interfejsie użytkownika funkcji ADF wykonaj następujące czynności.

1. Zaznacz pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** na stronie **Ustawienia zaawansowane** w okienku **Konfiguracja środowiska Integration Runtime** . 

1. Wybierz pozycję **Nowy** , aby otworzyć okienko **Dodawanie ekspresowej instalacji niestandardowej** , a następnie wybierz typ z listy rozwijanej **Typ instalacji niestandardowej** . Obecnie oferujemy niestandardowe ustawienia konfiguracji dla polecenia cmdkey, Dodawanie zmiennych środowiskowych, instalowanie Azure PowerShell i Instalowanie licencjonowanych składników.

#### <a name="running-cmdkey-command"></a>Uruchamianie polecenia cmdkey

W przypadku wybrania typu **polecenia Uruchom cmdkey** dla niestandardowej konfiguracji ekspresowej można uruchomić polecenie Windows cmdkey na Azure-SSIS IR. W tym celu wprowadź odpowiednio nazwę komputera lub nazwę domeny, nazwę użytkownika lub konto oraz hasło lub klucz konta w polach tekstowych **/Add**, **/User** i **/Pass** . Umożliwi to utrwalanie poświadczeń dostępu dla serwerów SQL, udziałów plików lub Azure Files na Azure-SSIS IR. Na przykład w celu uzyskania dostępu do Azure Files można odpowiednio wprowadzić, `YourAzureStorageAccountName.file.core.windows.net` `azure\YourAzureStorageAccountName` , i `YourAzureStorageAccountKey` dla **/Add**, **/User** i **/Pass**. Jest to podobne do uruchamiania polecenia Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) na komputerze lokalnym. Dla tej pory jest obsługiwane tylko jedno Express Konfiguracja niestandardowa do uruchomienia polecenia cmdkey. Aby uruchomić wiele poleceń cmdkey, zamiast tego należy użyć standardowej instalacji niestandardowej.

#### <a name="adding-environment-variables"></a>Dodawanie zmiennych środowiskowych

W przypadku wybrania typu **zmiennej Dodawanie środowiska** dla instalacji niestandardowej można dodać zmienną środowiskową systemu Windows do Azure-SSIS IR. Aby to zrobić, wprowadź odpowiednio nazwę zmiennej środowiskowej i wartość w polach tekstowych **Nazwa zmiennej** i **wartość zmiennej** . Umożliwi to użycie zmiennej środowiskowej w pakietach, które działają na Azure-SSIS IR, na przykład w obszarze składniki skryptów/zadania. Jest to podobne do uruchamiania polecenia [Set](/windows-server/administration/windows-commands/set_1) systemu Windows na komputerze lokalnym.

#### <a name="installing-azure-powershell"></a>Instalowanie Azure PowerShell

W przypadku wybrania typu **Azure PowerShell instalacji** dla niestandardowej instalacji niestandardowej można zainstalować moduł AZ module programu PowerShell na Azure-SSIS IR. Aby to zrobić, wprowadź wartość AZ module numer wersji (x. y. z) z [listy obsługiwanych](https://www.powershellgallery.com/packages/az). Umożliwi to uruchamianie Azure PowerShell poleceń cmdlet/skryptów w Twoich pakietach w celu zarządzania zasobami platformy Azure, na przykład [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md).

#### <a name="installing-licensed-components"></a>Instalowanie licencjonowanych składników

Jeśli wybierzesz opcję **Zainstaluj licencjonowany typ składnika** dla instalacji niestandardowej, możesz wybrać zintegrowany składnik od partnerów niezależnego dostawcy oprogramowania z listy rozwijanej **Nazwa składnika** :

   * W przypadku wybrania składnika **fabryki zadań SentryOne** można zainstalować pakiet [fabryki zadań](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) dla składników z SentryOne na Azure-SSIS IR. W tym celu wprowadź wcześniej zakupiony klucz licencji produktu w polu tekstowym **klucz licencji** . Aktualna wersja zintegrowana to **2020.1.3**.

   * W przypadku wybrania **OH22'S HEDDA. Składnik we/wy** , można zainstalować [HEDDA.](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) Składnik jakości/oczyszczania danych we/wy z oh22 na Azure-SSIS IR. Aby to zrobić, musisz najpierw zakupić swoją usługę. Aktualna wersja zintegrowana to **1.0.14**.

   * W przypadku wybrania składnika **oh22's SQLPhonetics.NET** można zainstalować składnik [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) (jakość danych/dopasowanie) z oh22 na Azure-SSIS IR. W tym celu wprowadź wcześniej zakupiony klucz licencji produktu w polu tekstowym **klucz licencji** . Aktualna wersja zintegrowana to **1.0.45**.

   * W przypadku wybrania składnika **KingswaySoft usług SSIS Integration** Toolkit można zainstalować pakiet [narzędzi SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) dla aplikacji CRM/ERP/Marketing/Collaboration, takich jak Microsoft Dynamics/SharePoint/Project Server, Chmura marketingowa Oracle/Salesforce, itp. z KingswaySoft na Azure-SSIS IR. W tym celu wprowadź wcześniej zakupiony klucz licencji produktu w polu tekstowym **klucz licencji** . Bieżąca wersja zintegrowana to **2020,1**.

   * W przypadku wybrania składnika **pakietu do produkcji SSIS KingswaySoft** można zainstalować pakiet usług [SSIS pakietu Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) dla składników z KingswaySoft na Azure-SSIS IR. W tym celu wprowadź wcześniej zakupiony klucz licencji produktu w polu tekstowym **klucz licencji** . Bieżąca wersja zintegrowana to **20,1**.

   * Jeśli wybierzesz składnik **Theobald oprogramowania yodrębnij** , możesz zainstalować [yodrębnij to](https://theobald-software.com/en/xtract-is/) pakiet łączników dla systemów SAP (ERP, s/4HANA, BW) z oprogramowania Theobald na Azure-SSIS IR. Aby to zrobić, przeciągnij & upuść/Przekaż plik licencji produktu, który został zakupiony wcześniej w polu wejściowym **plik licencji** . Aktualna wersja zintegrowana to **6.1.1.3**.

   * W przypadku wybrania składnika **usługi integracji AecorSoft** można zainstalować pakiet [usług Integration](https://www.aecorsoft.com/en/products/integrationservice) Connector dla systemów SAP i Salesforce z AecorSoft na Azure-SSIS IR. W tym celu wprowadź wcześniej zakupiony klucz licencji produktu w polu tekstowym **klucz licencji** . Aktualna wersja zintegrowana to **3.0.00**.

   * W przypadku wybrania składnika oprogramowania usług **SSIS w warstwie Standardowa** można zainstalować pakiet usług [SSIS w warstwie Standardowa](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) dla najpopularniejszych składników z CDATA, takich jak łączniki programu Microsoft SharePoint, na Azure-SSIS IR. W tym celu wprowadź wcześniej zakupiony klucz licencji produktu w polu tekstowym **klucz licencji** . Bieżąca wersja zintegrowana to **19,7354**.

   * W przypadku wybrania składnika pakietu usług SSIS w warstwie **rozszerzonej CDATA** można zainstalować pakiet [SSIS Extended pakietu](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) dla wszystkich składników z CDATA, takich jak łączniki Microsoft Dynamics 365 Business Central i inne składniki w **pakiecie SSIS Standard**, na Azure-SSIS IR. W tym celu wprowadź wcześniej zakupiony klucz licencji produktu w polu tekstowym **klucz licencji** . Bieżąca wersja zintegrowana to **19,7354**. Ze względu na jego duży rozmiar, aby uniknąć limitu czasu instalacji, upewnij się, że Azure-SSIS IR ma co najmniej 4 rdzenie procesora CPU na węzeł.

Dodane ustawienia Express Custom Setup zostaną wyświetlone na stronie **Ustawienia zaawansowane** . Aby je usunąć, zaznacz ich pola wyboru, a następnie wybierz pozycję **Usuń**.

### <a name="azure-powershell"></a>Azure PowerShell

W celu aprowizacji lub zmiany konfiguracji Azure-SSIS IR za pomocą konfiguracji niestandardowych przy użyciu Azure PowerShell wykonaj następujące czynności.

1. Jeśli Azure-SSIS IR jest już uruchomiona/uruchomiona, Zatrzymaj ją w pierwszej kolejności.

1. Następnie można dodać lub usunąć niestandardowe konfiguracje, uruchamiając `Set-AzDataFactoryV2IntegrationRuntime` polecenie cmdlet przed rozpoczęciem Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
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
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
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

### <a name="standard-custom-setup-samples"></a>Standardowe przykłady instalacji niestandardowej

Aby wyświetlić i ponownie użyć niektórych przykładów standardowych konfiguracji niestandardowych, wykonaj następujące czynności.

1. Połącz się z naszym publicznym kontenerem w wersji zapoznawczej przy użyciu Eksplorator usługi Azure Storage.

   a. W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu**, wybierz pozycję **Połącz z usługą Azure Storage**, wybierz polecenie **Użyj parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego**, a następnie wybierz przycisk **dalej**.

      ![Nawiązywanie połączenia z usługą Azure Storage za pomocą sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Wybierz pozycję **Użyj identyfikatora URI sygnatury dostępu współdzielonego** , a następnie w polu tekstowym **Identyfikator URI** wprowadź następujący identyfikator URI sygnatury dostępu współdzielonego:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Podaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

   d. W lewym okienku wybierz kontener podłączony **publicpreview** , a następnie kliknij dwukrotnie folder *CustomSetupScript* . W tym folderze znajdują się następujące elementy:

      * *Przykładowy* folder zawierający niestandardową konfigurację służącą do instalowania podstawowego zadania na każdym węźle Azure-SSIS IR. Zadanie nie wykonuje żadnych operacji, ale w stanie uśpienia przez kilka sekund. Folder zawiera również folder *Gacutil* , którego cała zawartość (*gacutil.exe*, *gacutil.exe.config* i *1033\gacutlrc.dll*) może zostać skopiowana do kontenera.

      * Folder *UserScenarios* , który zawiera kilka przykładów instalacji niestandardowej z scenariuszy użytkowników rzeczywistych. Jeśli chcesz zainstalować wiele przykładów na Azure-SSIS IR, możesz połączyć ich niestandardowe pliki skryptu Instalatora (*Main. cmd*) w jeden i przekazać je ze wszystkimi skojarzonymi z nimi plikami do kontenera.

        ![Zawartość kontenera publicznej wersji zapoznawczej](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Kliknij dwukrotnie folder *UserScenarios* , aby znaleźć następujące elementy:

      * Folder *programu .NET FRAMEWORK 3,5* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*), aby zainstalować wcześniejszą wersję .NET Framework na każdym węźle Azure-SSIS IR. Ta wersja może być wymagana przez niektóre składniki niestandardowe.

      * Folder *BCP* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania SQL Server narzędzi wiersza polecenia (*MsSqlCmdLnUtils.msi*) w każdym węźle Azure-SSIS IR. Jednym z tych narzędzi jest program do kopiowania masowego (*BCP*).

      * Folder *sufiksu DNS* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu dołączenia własnego sufiksu dns (na przykład *test.com*) do dowolnej niekwalifikowanej nazwy domeny o pojedynczej etykiecie i przekształcenie jej w w pełni kwalifikowaną nazwę domeny (FQDN) przed użyciem jej w zapytaniach DNS z Azure-SSIS IR.

      * Folder *programu Excel* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania niektórych zestawów i bibliotek języka C# w każdym węźle Azure-SSIS IR. Można ich używać w zadaniach skryptów do dynamicznego odczytywania i zapisywania plików programu Excel. 
      
        Najpierw pobierz [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) i [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), a następnie Przekaż je wszystkie wraz z *Main. cmd* do kontenera. Alternatywnie, jeśli chcesz tylko używać standardowych łączników programu Excel (Menedżera połączeń, źródła i miejsca docelowego), pakiet redystrybucyjny dostępu, który zawiera te elementy, jest już wstępnie zainstalowany na Azure-SSIS IR, więc nie jest potrzebna żadna Konfiguracja niestandardowa.
      
      * Folder *ODBC programu MySQL* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania sterowników ODBC MYSQL na każdym węźle Azure-SSIS IR. Ta konfiguracja pozwala używać łączników ODBC (Menedżera połączeń, źródła i miejsca docelowego) do łączenia się z serwerem MySQL. 
     
        Najpierw należy [pobrać najnowszą wersję 64-bitową i 32-bitową instalatorów sterowników ODBC MySQL](https://dev.mysql.com/downloads/connector/odbc/) (na przykład *mysql-connector-odbc-8.0.13-winx64.msi* i *mysql-connector-odbc-8.0.13-win32.msi*), a następnie przekazać je razem z *Main. cmd* do kontenera.

      * Folder *Oracle Enterprise* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) i plik konfiguracji instalacji dyskretnej (*Client. rsp*) w celu zainstalowania łączników Oracle i sterownika OCI na każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem Oracle przy użyciu Menedżera połączeń Oracle, źródła i miejsca docelowego. 
      
        Najpierw pobierz łączniki Microsoft Konektors v 5.0 for Oracle (*AttunitySSISOraAdaptersSetup.msi* i *AttunitySSISOraAdaptersSetup64.msi*) z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) i najnowszego klienta Oracle (na przykład *winx64_12102_client.zip*) z [bazy danych Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html). Następnie Przekaż je wszystkie wraz z *Main. cmd* i *Client. rsp* do kontenera. Jeśli używasz TNS do łączenia się z bazą danych Oracle, musisz również pobrać plik *pliku tnsnames. ora*, edytować go i przekazać do kontenera. W ten sposób można je skopiować do folderu instalacyjnego programu Oracle podczas instalacji.

      * Folder *standard ADO.NET firmy Oracle* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania sterownika Oracle ODP.NET na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle przy użyciu Menedżera połączeń ADO.NET, źródła i miejsca docelowego. 
      
        Najpierw [Pobierz najnowszy sterownik Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (na przykład *ODP.NET_Managed_ODAC122cR1.zip*), a następnie Przekaż go wraz z *Main. cmd* do kontenera.
       
      * *Standardowy folder ODBC programu Oracle* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*), aby zainstalować sterownik Oracle ODBC na każdym węźle Azure-SSIS IR. Skrypt konfiguruje również nazwę źródła danych (DSN). Ta konfiguracja umożliwia użycie Menedżera połączeń ODBC, źródła i miejsca docelowego lub Power Query Menedżera połączeń i źródła z typem źródła danych ODBC, aby połączyć się z serwerem Oracle. 
      
        Najpierw pobierz najnowszą wersję programu Oracle Instant Client (pakiet podstawowy lub podstawowy pakiet w języku Basic) i pakiet ODBC, a następnie Przekaż je razem z *głównym programem. cmd* do kontenera:
        * [Pobierz pakiety 64-bitowe](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pakiet podstawowy: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Podstawowy pakiet w wersji Lite: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Pakiet ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Pobierz pakiety 32-bitowe](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pakiet podstawowy: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Podstawowy pakiet w wersji Lite: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Pakiet ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * *Standardowy folder OLEDB firmy Oracle* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*), aby zainstalować sterownik Oracle OLEDB na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle przy użyciu Menedżera połączeń OLEDB, źródła i miejsca docelowego. 
     
        Najpierw [Pobierz najnowszy sterownik Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (na przykład *ODAC122010Xcopy_x64.zip*), a następnie Przekaż go wraz z *Main. cmd* do kontenera.

      * Folder *POSTGRESQL ODBC* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania sterowników ODBC POSTGRESQL na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem PostgreSQL za pomocą Menedżera połączeń ODBC, źródła i miejsca docelowego. 
     
        Najpierw [Pobierz najnowsze 64-bitowe i 32-bitowe wersje instalatorów sterowników ODBC PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (na przykład *psqlodbc_x64.msi* i *psqlodbc_x86.msi*), a następnie Przekaż je wszystkie razem z *Main. cmd* do kontenera.

      * Folder *SAP BW* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) do zainstalowania zestawu łączników SAP .NET (*librfc32.dll*) w każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem SAP BW za pomocą Menedżera połączeń SAP BW, źródła i miejsca docelowego. 
      
        Najpierw przekaż 64-bitową lub 32-bitową wersję *librfc32.dll* z folderu instalacji SAP wraz z *Main. cmd* do kontenera. Skrypt następnie kopiuje zestaw SAP do folderu *%windir%\syswow64* lub *%windir%\system32* podczas instalacji.

      * Folder *magazynu* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) w celu zainstalowania Azure PowerShell na każdym węźle Azure-SSIS IR. Ta konfiguracja pozwala wdrażać i uruchamiać pakiety usług SSIS, które uruchamiają [Azure PowerShell polecenia cmdlet/skrypty do zarządzania usługą Azure Storage](../storage/blobs/storage-quickstart-blobs-powershell.md). 
      
        Skopiuj *Main. cmd*, przykładową *AzurePowerShell.msi* (lub użyj najnowszej wersji) i *storage.ps1* do kontenera. Użyj *programu PowerShell. dtsx* jako szablonu dla pakietów. Szablon pakietu łączy [zadanie pobierania obiektów blob platformy Azure](/sql/integration-services/control-flow/azure-blob-download-task), które pobiera modyfikowalny skrypt programu PowerShell (*storage.ps1*) i [zadanie wykonywania procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), które wykonuje skrypt w każdym węźle.

      * Folder programu *Teradata* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*), skojarzony z nim plik (*install. cmd*) i pakiety Instalatora (*MSI*). Te pliki instalują łączniki programu Teradata, interfejs API Parallel transportowy (TPT) programu Teradata i sterownik ODBC na każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem Teradata przy użyciu Menedżera połączeń programu Teradata, źródła i miejsca docelowego. 
      
        Najpierw [Pobierz narzędzia programu Teradata oraz plik zip 15. x](http://partnerintelligence.teradata.com) (na przykład  *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*), a następnie Przekaż go wraz z wcześniej wymienionymi plikami *. cmd* i *. msi* do kontenera.

      * Folder *TLS 1,2* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) do korzystania z mocnej kryptografii i bezpieczniejszego protokołu sieciowego (TLS 1,2) w każdym węźle Azure-SSIS IR. Skrypt wyłącza również starsze wersje protokołu SSL/TLS.

      * Folder *Zulu OPENJDK* , który zawiera niestandardowy skrypt instalacyjny (*Main. cmd*) i plik programu PowerShell (*install_openjdk.ps1*), aby zainstalować OPENJDK Zulu na każdym węźle Azure-SSIS IR. Ta konfiguracja pozwala używać Azure Data Lake Store i elastycznych łączników plików do przetwarzania plików ORC i Parquet. Aby uzyskać więcej informacji, zobacz [pakiet Azure Feature Pack dla usług Integration Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java). 
      
        Najpierw [Pobierz najnowszą Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (na przykład *zulu8.33.0.1-jdk8.0.192-win_x64.zip*), a następnie Przekaż go wraz z *Main. cmd* i *install_openjdk.ps1* do kontenera.

        ![Foldery w folderze scenariusze użytkownika](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Aby ponownie użyć tych standardowych przykładów instalacji niestandardowej, skopiuj zawartość wybranego folderu do kontenera.

1. Podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR w interfejsie użytkownika funkcji ADF zaznacz pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** na stronie **Ustawienia zaawansowane** w okienku **Konfiguracja środowiska Integration Runtime** . Następnie wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera w polu tekstowym **Identyfikator URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych** .
   
1. Podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu Azure PowerShell, Zatrzymaj ją, jeśli jest już uruchomiona/uruchomiona, uruchom `Set-AzDataFactoryV2IntegrationRuntime` polecenie cmdlet z identyfikatorem URI sygnatury dostępu współdzielonego kontenera jako wartość `SetupScriptContainerSasUri` parametru, a następnie uruchom Azure-SSIS IR.

1. Po zakończeniu standardowej instalacji niestandardowej i uruchomieniu Azure-SSIS IR można znaleźć wszystkie niestandardowe dzienniki instalacji w folderze *Main. cmd. log* kontenera. Obejmują one standardowe dane wyjściowe *Main. cmd* i innych dzienników wykonania.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie Azure-SSIS IR Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Utwórz składniki płatne lub licencjonowane dla Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
