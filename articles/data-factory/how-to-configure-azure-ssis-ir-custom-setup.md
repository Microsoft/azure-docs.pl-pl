---
title: Dostosowywanie konfiguracji środowiska wykonawczego integracji usługi Azure-SSIS
description: W tym artykule opisano, jak używać niestandardowego interfejsu konfiguracji dla środowiska wykonawczego integracji platformy Azure-SSIS do instalowania dodatkowych składników lub zmieniania ustawień
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
ms.openlocfilehash: 3aabf68b6763e5815a03021cb02683d509c26190
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415054"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Dostosowywanie konfiguracji środowiska wykonawczego integracji usługi Azure-SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Niestandardowa konfiguracja środowiska uruchomieniowego integracji usług integracji usługi azure-SQL Server (Azure-SSIS IR) zapewnia interfejs do dodawania własnych kroków podczas konfigurowania lub ponownej konfiguracji usługi Azure-SSIS IR. 

Korzystając z instalacji niestandardowej, można zmienić domyślną konfigurację lub środowisko obsługi, aby na przykład uruchomić dodatkowe usługi systemu Windows, utrwalić poświadczenia dostępu dla udziałów plików lub użyć silnego protokołu kryptograficznego/bezpieczniejszego protokołu sieciowego (TLS 1.2). Lub można zainstalować dodatkowe składniki, takie jak zestawy, sterowniki lub rozszerzenia, w każdym węźle usługi Azure-SSIS IR.

Niestandardowe konfiguracje można wykonać na platformie Azure-SSIS IR na dwa sposoby: 
* **Ekspresowa konfiguracja niestandardowa bez skryptu:** Uruchom kilka typowych konfiguracji systemowych i poleceń systemu Windows lub zainstaluj niektóre popularne lub zalecane dodatkowe składniki bez użycia skryptów.
* **Standardowa konfiguracja niestandardowa ze skryptem:** Przygotuj skrypt i skojarzone z nim pliki i przekaż je razem do kontenera obiektów blob na koncie usługi Azure storage. Następnie podajesz identyfikator URI (Uniform Resource Identifier) (Shared Access Signature) (Shared Access Signature) dla kontenera podczas konfigurowania lub ponownego konfigurowania urządzenia Azure-SSIS IR. Każdy węzeł usługi Azure-SSIS IR pobiera skrypt i skojarzone z nim pliki z kontenera i uruchamia konfigurację niestandardową z podwyższonym poziomem uprawnień. Po zakończeniu instalacji niestandardowej każdy węzeł przekazuje standardowe dane wyjściowe wykonywania i inne dzienniki do kontenera.

Można zainstalować zarówno bezpłatne, nielicencjonowane składniki, jak i płatne, licencjonowane komponenty z konfiguracją ekspresową i standardową. Jeśli jesteś niezależnym dostawcą oprogramowania (ISV), zobacz [Opracowywanie płatnych lub licencjonowanych składników dla usługi Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Aby korzystać z przyszłych ulepszeń, zaleca się używanie serii węzłów w wersji 3 lub nowszej dla usługi Azure-SSIS IR z konfiguracją niestandardową.

## <a name="current-limitations"></a>Bieżące ograniczenia

Następujące ograniczenia dotyczą tylko standardowych ustawień niestandardowych:

- Jeśli chcesz użyć *gacutil.exe* w skrypcie do zainstalowania zestawów w globalnej pamięci podręcznej zestawów (GAC), musisz podać *gacutil.exe* jako część konfiguracji niestandardowej. Możesz też użyć kopii dostarczonej w naszym kontenerze *podglądu publicznego,* omówionej później w sekcji "Instrukcje".

- Jeśli chcesz odwołać się do podfolderu w skrypcie, *msiexec.exe* nie obsługuje `.\` notacji, aby odwołać się do folderu głównego. Użyj polecenia, `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` takiego jak `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`zamiast .

- Udziały administracyjne lub ukryte udziały sieciowe, które są automatycznie tworzone przez system Windows, nie są obecnie obsługiwane w usłudze Azure-SSIS IR.

- Sterownik IBM iSeries Access ODBC nie jest obsługiwany w usłudze Azure-SSIS IR. Podczas instalacji niestandardowej mogą pojawić się błędy instalacji. Jeśli to zrobisz, skontaktuj się z pomocą techniczną IBM, aby uzyskać pomoc.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby dostosować identyfikator Azure-SSIS IR, potrzebujesz następujących elementów:

- [Subskrypcja platformy Azure](https://azure.microsoft.com/)

- [Aprowizuj urządzenie Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Konto magazynu platformy Azure](https://azure.microsoft.com/services/storage/). Nie jest wymagane w przypadku konfiguracji niestandardowych ekspresowych. W przypadku standardowych ustawień niestandardowych należy przekazać i zapisać niestandardowy skrypt instalacyjny i skojarzone z nim pliki w kontenerze obiektów blob. Proces instalacji niestandardowej również przekazuje jego dzienniki wykonywania do tego samego kontenera obiektów blob.

## <a name="instructions"></a>Instrukcje

1. Jeśli chcesz skonfigurować lub ponownie skonfigurować usługę Azure-SSIS IR za pomocą programu PowerShell, pobierz i zainstaluj [program Azure PowerShell](/powershell/azure/install-az-ps). W przypadku konfiguracji niestandardowych ekspresowych przejdź do kroku 4.

1. Przygotuj niestandardowy skrypt instalacyjny i skojarzone z nim pliki (na przykład .bat, cmd, .exe, .dll, msi lub .ps1).

   * Musisz mieć plik skryptu o nazwie *main.cmd*, który jest punktem wejścia konfiguracji niestandardowej.  
   * Aby upewnić się, że skrypt może być wykonywany w trybie cichym, zaleca się przetestowanie go najpierw na komputerze lokalnym.  
   * Jeśli chcesz, aby dodatkowe dzienniki generowane przez inne narzędzia (na przykład *msiexec.exe)* były przekazywane do `CUSTOM_SETUP_SCRIPT_LOG_DIR`kontenera, określ wstępnie zdefiniowaną zmienną środowiskową, jako folder dziennika w skryptach (na przykład *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Pobierz, zainstaluj i otwórz [Eksploratora usługi Azure Storage](https://storageexplorer.com/). W tym celu:

   a. W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy **pozycję Konta magazynu,** a następnie wybierz pozycję **Połącz z magazynem platformy Azure**.

      ![Łączenie z usługą Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Wybierz **pozycję Użyj nazwy i klucza konta magazynu,** a następnie wybierz pozycję **Dalej**.

      ![Używanie nazwy i klucza konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   d. Wprowadź nazwę i klucz konta magazynu platformy Azure, wybierz przycisk **Dalej**, a następnie wybierz pozycję **Połącz**.

      ![Podaj nazwę i klucz konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. W obszarze połączonego konta magazynu platformy Azure kliknij prawym przyciskiem myszy **kontenery obiektów Blob**, wybierz pozycję **Utwórz kontener obiektów blob**i nazwij nowy kontener.

      ![Tworzenie kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Wybierz nowy kontener i przekaż niestandardowy skrypt instalacyjny i skojarzone z nim pliki. Upewnij się, że przesyłasz *plik main.cmd* na najwyższym poziomie kontenera, a nie w żadnym folderze. Upewnij się również, że kontener zawiera tylko niezbędne niestandardowe pliki instalacyjne, dzięki czemu pobieranie ich do usługi Azure-SSIS IR później nie zajmie dużo czasu. Maksymalny czas trwania instalacji niestandardowej jest obecnie ustawiony na 45 minut przed jej przekroczaniem czasu. Obejmuje to czas, aby pobrać wszystkie pliki z kontenera i zainstalować je na platformie Azure-SSIS IR. Jeśli konfiguracja wymaga więcej czasu, podnieś bilet pomocy technicznej.

      ![Przekazywanie plików do kontenera obiektu blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Kliknij prawym przyciskiem myszy kontener, a następnie wybierz polecenie **Pobierz podpis dostępu współdzielonego**.

      ![Pobierz podpis dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Utwórz identyfikator URI sygnatury dostępu Współdzielonego dla kontenera z wystarczająco długim czasem wygaśnięcia i z uprawnieniami do odczytu/zapisu/listy. Identyfikator URI usługi SAS jest potrzebny do pobierania i uruchamiania niestandardowego skryptu konfiguracji i skojarzonych z nim plików za każdym razem, gdy dowolny węzeł urządzenia Azure-SSIS IR zostanie ponownie zaaimaniowany lub ponownie uruchomiony. Musisz napisać uprawnienia do przekazywania dzienników wykonywania konfiguracji.

      > [!IMPORTANT]
      > Upewnij się, że identyfikator URI sygnatury dostępu Współdzielonego nie wygasa, a zasoby konfiguracji niestandardowej są zawsze dostępne podczas całego cyklu życia usługi Azure-SSIS IR, od utworzenia do usunięcia, zwłaszcza jeśli regularnie zatrzymujesz i uruchomisz usługę Azure-SSIS IR w tym okresie.

      ![Generowanie podpisu dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopiowanie i zapisywanie identyfikatora URI sygnatury dostępu Współdzielonego kontenera.

      ![Kopiowanie i zapisywanie podpisu dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Podczas konfigurowania lub ponownego konfigurowania środowiska Azure-SSIS IR za pomocą interfejsu użytkownika fabryki danych można dodać lub usunąć ustawienia niestandardowe, zaznaczając pole wyboru **Dostosuj środowisko uruchomieniowe integracji platformy Azure-SSIS za pomocą dodatkowych konfiguracji systemu/instalacji składników** w sekcji Ustawienia **zaawansowane** **okienka konfiguracji środowiska uruchomieniowego integracji.** 

   Jeśli chcesz dodać standardowe ustawienia niestandardowe, wprowadź identyfikator URI sygnatury dostępu Współdzielonego kontenera w polu **Identyfikator URI URI kontenera instalacji niestandardowej.** 
   
   Jeśli chcesz dodać ustawienia niestandardowe ekspresowe, wybierz pozycję **Nowy,** aby otworzyć okienko **Dodaj ekspresową konfigurację niestandardową,** a następnie wybierz typ listy rozwijanej **Typ konfiguracji niestandardowej Express:**

   * Jeśli wybierzesz typ **polecenia Uruchom cmdkey,** możesz utrwalić poświadczenia dostępu dla udziałów plików lub udziałów usługi Azure Files w usłudze Azure-SSIS IR, wprowadzając docelową nazwę komputera lub nazwę domeny, nazwę konta lub nazwę użytkownika oraz klucza konta lub hasła w polach **/Add**, **/User**i **/Pass.** Jest to podobne do uruchamiania polecenia [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) systemu Windows na komputerze lokalnym.
   
   * Jeśli wybierzesz dodaj typ **zmiennej środowiskowej,** można dodać zmienne środowiskowe systemu Windows do użycia w pakietach, które są uruchamiane na platformie Azure-SSIS IR, wprowadzając nazwę i wartość zmiennej środowiskowej w polach **Nazwa zmiennej** i **Zmienna wartość.** Jest to podobne do uruchamiania polecenia [zestaw](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) systemu Windows na komputerze lokalnym.

   * Jeśli wybierzesz typ **składnika Install licensed,** możesz wybrać zintegrowany komponent od naszych partnerów niezależnego dostawcy energii z systemu isv na liście rozwijanej **Nazwa komponentu:**

     * Jeśli **wybierzesz składnik Fabryka zadań SentryOne,** możesz zainstalować pakiet składników [Fabryki zadań](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) z SentryOne na urządzeniu Azure-SSIS IR, wprowadzając zakupiony od nich klucz licencyjny produktu w polu **Klucz licencji.** Aktualna zintegrowana wersja to **2019.4.3**.

     * Jeśli wybierzesz **oh22's HEDDA. Komponent IO,** można zainstalować [HEDDA. ](https://hedda.io/ssis-component/)Jakość danych we//wysługoszczący składnik z oh22 na platformie Azure-SSIS IR po zakupie usługi. Aktualna zintegrowana wersja to **1.0.13**.

     * Jeśli wybierzesz **składnik SQLPhonetics.NET oh22,** możesz zainstalować składnik [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) jakości/dopasowania danych z oh22 na urządzeniu Azure-SSIS IR, wprowadzając klucz licencyjny produktu zakupiony od nich w polu **Klucz licencyjny.** Aktualna zintegrowana wersja to **1.0.43**.

     * Jeśli wybierzesz składnik **License key** **KingswaySoft Integration Toolkit,** możesz zainstalować pakiet łączników [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) dla aplikacji CRM/ERP/marketing/collaboration, takich jak Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud itp. Aktualna zintegrowana wersja to **2019.2**.

     * Jeśli wybierzesz składnik **SSIS Productivity Pack firmy KingswaySoft,** możesz zainstalować pakiet składników [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) firmy KingswaySoft na urządzeniu Azure-SSIS IR, wprowadzając klucz licencyjny produktu zakupiony od nich w polu Klucz **licencyjny.** Aktualna zintegrowana wersja to **10.0**.

     * Jeśli **wybierzesz składnik Xtract IS firmy Theobald Software,** możesz zainstalować pakiet łączników [Xtract IS](https://theobald-software.com/en/xtract-is/) dla systemu SAP (ERP, S/4HANA, BW) z oprogramowania Theobald na urządzeniu Azure-SSIS IR, przeciągając & upuszczanie/przesyłanie pliku licencji produktu zakupionego od nich do pola **pliku licencji.** Obecna zintegrowana wersja to **6.1.1.3**.

   Dodane ustawienia niestandardowe zostaną wyświetlone w sekcji **Ustawienia zaawansowane.** Aby je usunąć, zaznacz ich pola wyboru, a następnie wybierz pozycję **Usuń**.

   ![Ustawienia zaawansowane z niestandardowymi ustawieniami](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Po skonfigurowaniu lub ponownym skonfigurowaniu usługi Azure-SSIS IR za pomocą programu PowerShell można `Set-AzDataFactoryV2IntegrationRuntime` dodać lub usunąć ustawienia niestandardowe, uruchamiając polecenie cmdlet przed uruchomieniem usługi Azure-SSIS IR.
   
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
   
   Po zakończeniu standardowej konfiguracji niestandardowej i uruchomieniu usługi Azure-SSIS IR można znaleźć standardowe dane wyjściowe *main.cmd* i inne dzienniki wykonywania w folderze *main.cmd.log* kontenera magazynu.

1. Aby wyświetlić niektóre przykłady standardowych konfiguracji niestandardowych, połącz się z naszym kontenerem public preview przy użyciu Eksploratora usługi Azure Storage.

   a. W obszarze **(lokalny i dołączony)** kliknij prawym przyciskiem myszy **pozycję Konta magazynu**, wybierz pozycję **Połącz z magazynem platformy Azure**, wybierz pozycję Użyj ciągu połączenia lub **identyfikatora URI podpisu dostępu współdzielonego,** a następnie wybierz pozycję **Dalej**.

      ![Łączenie się z magazynem platformy Azure za pomocą podpisu dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Wybierz **pozycję Użyj identyfikatora URI sygnatury dostępu Współdzielonego,** a następnie w polu **identyfikator URI** wprowadź następujący identyfikator URI sygnatury dostępu Współdzielonego:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Podaj podpis dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   d. Wybierz **pozycję Dalej**, a następnie wybierz pozycję **Połącz**.

   d. W lewym okienku zaznacz połączony kontener **widoku publicznego,** a następnie kliknij dwukrotnie folder *CustomSetupScript.* W tym folderze znajdują się następujące elementy:

      * *Przykładowy* folder, który zawiera niestandardową konfigurację w celu zainstalowania podstawowego zadania w każdym węźle urządzenia Azure-SSIS IR. Zadanie nie robi nic, ale spać przez kilka sekund. Folder zawiera również folder *gacutil,* którego cała zawartość (*gacutil.exe*, *gacutil.exe.config*i *1033\gacutlrc.dll*) może być kopiowana tak, jak jest do kontenera.

      * Folder *UserScenarios,* który zawiera kilka przykładów konfiguracji niestandardowej ze scenariuszy rzeczywistych użytkowników.

        ![Zawartość publicznego kontenera podglądu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Kliknij dwukrotnie folder *UserScenarios,* aby znaleźć następujące elementy:

      * Folder *.NET FRAMEWORK 3.5,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* w celu zainstalowania wcześniejszej wersji programu .NET Framework, która może być wymagana dla składników niestandardowych w każdym węźle usługi Azure-SSIS IR.

      * Folder *BCP,* który zawiera niestandardowy skrypt instalacyjny *(main.cmd)* w celu zainstalowania narzędzi wiersza polecenia programu SQL Server (*MsSqlCmdLnUtils.msi*), w tym programu kopiowania zbiorczego *(bcp)* w każdym węźle usługi Azure-SSIS IR.

      * Folder *programu EXCEL,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* do instalowania zestawów i bibliotek języka C#, których można używać w zadaniach skryptów do dynamicznego odczytu i zapisu plików programu Excel w każdym węźle usługi Azure-SSIS IR. 
      
        Najpierw pobierz [*plik ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) i [*DocumentFormat.OpenXml.dll,*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)a następnie przekaż je razem z *plikem main.cmd* do kontenera. Alternatywnie, jeśli chcesz używać tylko standardowego Menedżera połączeń programu Excel, źródła programu Excel i miejsca docelowego programu Excel, wymagany dostęp jest już preinstalowany na platformie Azure-SSIS IR, więc nie potrzebujesz żadnej konfiguracji niestandardowej.
      
      * Folder *MYSQL ODBC,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* do zainstalowania sterowników OdBC MySQL w każdym węźle usługi Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem MySQL za pomocą Menedżera połączeń ODBC, źródła i miejsca docelowego. 
     
        Najpierw [pobierz najnowsze 64-bitowe i 32-bitowe wersje instalatorów sterowników MySQL ODBC](https://dev.mysql.com/downloads/connector/odbc/) (na przykład *mysql-connector-odbc-8.0.13-winx64.msi* i *mysql-connector-odbc-8.0.13-win32.msi),* a następnie prześlij je razem z *main.cmd* do kontenera.

      * Folder *ORACLE ENTERPRISE,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* i cichy plik konfiguracji instalacji *(client.rsp)* w celu zainstalowania łączników Oracle i sterownika OCI w każdym węźle usługi Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia łączenie się z serwerem Oracle Za pomocą programu Oracle Connection Manager, źródła i miejsca docelowego. 
      
        Najpierw pobierz microsoft connectors v5.0 dla Oracle *(AttunitySSISOraadaptersSetup.msi* i *AttunitySSISZazaptersSetup64.msi)* z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) i najnowszego klienta Oracle (na przykład *winx64_12102_client.zip)* z [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), a następnie prześlij je wszystkie razem z *main.cmd* i *client.rsp* do kontenera. Jeśli używasz TNS do łączenia się z Oracle, musisz również pobrać *tnsnames.ora*, edytować go i przesłać do kontenera, dzięki czemu można go skopiować do folderu instalacyjnego Oracle podczas konfiguracji.

      * Folder *ADO.NET ORACLE STANDARD,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* w celu zainstalowania sterownika ODP.NET Oracle w każdym węźle urządzenia Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle za pomocą ADO.NET Menedżera połączeń, źródła i miejsca docelowego. 
      
        Najpierw [pobierz najnowszy sterownik ODP.NET Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (na przykład *ODP.NET_Managed_ODAC122cR1.zip),* a następnie prześlij go razem z *main.cmd* do kontenera.
       
      * Folder *ORACLE STANDARD ODBC,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd),* aby zainstalować sterownik Oracle ODBC i skonfigurować nazwę źródła danych (DSN) w każdym węźle usługi Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle za pomocą Menedżera połączeń ODBC, źródła i miejsca docelowego lub menedżera połączeń dodatku Power Query i źródła z typem źródła danych ODBC. 
      
        Najpierw pobierz najnowszy klient błyskawiczny Oracle (pakiet podstawowy lub pakiet Basic Lite) i pakiet ODBC, a następnie prześlij je wszystkie razem z *main.cmd* do kontenera:
        * [Pobierz pakiety 64-bitowe](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pakiet podstawowy: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Podstawowy pakiet Lite: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Pakiet ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Pobierz pakiety 32-bitowe](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pakiet podstawowy: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Podstawowy pakiet Lite: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Pakiet ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Folder *ORACLE STANDARD OLEDB,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* w celu zainstalowania sterownika Oracle OLEDB w każdym węźle urządzenia Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle za pomocą Menedżera połączeń OLEDB, źródła i miejsca docelowego. 
     
        Najpierw [pobierz najnowszy sterownik Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (na przykład *ODAC122010Xcopy_x64.zip),* a następnie prześlij go razem z *main.cmd* do kontenera.

      * Folder *ODBC POSTGRESQL,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* do zainstalowania sterowników ODBC PostgreSQL w każdym węźle urządzenia Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem PostgreSQL za pomocą Menedżera połączeń ODBC, źródła i miejsca docelowego. 
     
        Najpierw [pobierz najnowsze 64-bitowe i 32-bitowe wersje instalatorów sterowników PostgreSQL ODBC](https://www.postgresql.org/ftp/odbc/versions/msi/) (na przykład *psqlodbc_x64.msi* i *psqlodbc_x86.msi),* a następnie prześlij je wszystkie razem z *main.cmd* do kontenera.

      * Folder *SAP BW,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* w celu zainstalowania zestawu łączników SAP .NET *(librfc32.dll)* w każdym węźle usługi Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia łączenie się z serwerem SAP BW za pomocą Menedżera połączeń SAP Business Warehouse (BW), źródła i miejsca docelowego. 
      
        Najpierw prześlij do kontenera 64-bitową lub 32-bitową wersję *librfc32.dll* z folderu instalacyjnego SAP wraz z *plikiem main.cmd.* Następnie skrypt kopiuje zestaw SAP do folderu *%windir%\SysWow64* lub *%windir%\System32* podczas instalacji.

      * Folder *STORAGE,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* do zainstalowania programu Azure PowerShell w każdym węźle urządzenia Azure-SSIS IR. Ta konfiguracja umożliwia wdrażanie i uruchamianie pakietów SSIS, które [uruchamiają skrypty programu PowerShell w celu manipulowania kontem magazynu platformy Azure.](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell) 
      
        Kopiuj *plik main.cmd*— przykładowy plik *AzurePowerShell.msi* (lub użyj najnowszej wersji) i *plik storage.ps1* do kontenera. Użyj *programu PowerShell.dtsx* jako szablonu dla pakietów. Szablon pakietu łączy [zadanie pobierania obiektów Blob platformy Azure,](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)które pobiera *plik storage.ps1* jako modyfikowalny skrypt programu PowerShell oraz [zadanie wykonywania procesu,](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)które wykonuje skrypt w każdym węźle.

      * Folder *TERADATA,* który zawiera niestandardowy skrypt instalacyjny *(main.cmd),* skojarzony z nim plik *(install.cmd)* i pakiety instalacyjne (*.msi*). Pliki te instalują łączniki Teradata, interfejs API Teradata Parallel Transporter (TPT) i sterownik ODBC w każdym węźle usługi Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia łączenie się z serwerem Teradata Connection Manager, source i destination za pomocą Menedżera połączeń Teradata. 
      
        Najpierw [pobierz plik zip Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (na przykład *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip),* a następnie prześlij go razem z wcześniej wymienionymi plikami *.cmd* i *msi* do kontenera.

      * Folder *TLS 1.2,* który zawiera niestandardowy skrypt konfiguracji *(main.cmd)* do używania silnej kryptografii/bezpieczniejszego protokołu sieciowego (TLS 1.2) i wyłączanie starszych wersji protokołu SSL/TLS w każdym węźle usługi Azure-SSIS IR.

      * Folder *ZULU OPENJDK,* który zawiera niestandardowy skrypt instalacyjny *(main.cmd)* i plik programu PowerShell *(install_openjdk.ps1*), aby zainstalować Zulu OpenJDK w każdym węźle urządzenia Azure-SSIS IR. Ta konfiguracja umożliwia korzystanie z usługi Azure Data Lake Store i łączników plików elastycznych do przetwarzania plików ORC i Parquet. Aby uzyskać więcej informacji, zobacz [Pakiet funkcji platformy Azure dla usług integracji](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Najpierw [pobierz najnowszy Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (na przykład *zulu8.33.0.1-jdk8.0.192-win_x64.zip),* a następnie prześlij go wraz z *main.cmd* i *install_openjdk.ps1* do kontenera.

        ![Foldery w folderze scenariuszy użytkownika](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Aby wypróbować te niestandardowe przykłady konfiguracji, skopiuj zawartość z wybranego folderu do kontenera.
   
      Po skonfigurowaniu lub ponownym skonfigurowaniu urządzenia Azure-SSIS IR przy użyciu interfejsu użytkownika fabryki danych zaznacz pole wyboru **Dostosuj środowisko uruchomieniowe integracji platformy Azure-SSIS za pomocą dodatkowych konfiguracji systemu/instalacji składników** w sekcji Ustawienia **zaawansowane,** a następnie wprowadź identyfikator URI sygnatury dostępu Współdzielonego kontenera w polu **Niestandardowy kontener ssania sygnatury dostępu współużytowego.**
   
      Po skonfigurowaniu lub ponownym skonfigurowaniu usługi Azure-SSIS IR za `Set-AzDataFactoryV2IntegrationRuntime` pomocą programu PowerShell uruchom polecenie cmdlet z identyfikatorem URI sygnatury dostępu Współdzielonego kontenera jako wartość `SetupScriptContainerSasUri` parametru.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie wersji Enterprise środowiska wykonawczego integracji azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Tworzenie płatnych lub licencjonowanych składników niestandardowych dla środowiska wykonawczego integracji azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
