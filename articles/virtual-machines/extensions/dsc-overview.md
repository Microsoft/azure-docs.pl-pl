---
title: Omówienie konfiguracji żądanego stanu dla platformy Azure
description: Dowiedz się, jak używać programu obsługi rozszerzeń platformy Microsoft Azure dla konfiguracji żądanego stanu programu PowerShell (DSC). Artykuł zawiera wymagania wstępne, architekturę i polecenia cmdlet.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: adb36e14bb7b772d3e28361bdc48d248bd84f41f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458968"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Wprowadzenie do procedury obsługi rozszerzenia Azure Desired State Configuration

Agent maszyny wirtualnej platformy Azure i skojarzone rozszerzenia są częścią usług infrastruktury platformy Microsoft Azure. Rozszerzenia maszyn wirtualnych to składniki oprogramowania, które rozszerzają funkcjonalność maszyn wirtualnych i upraszczają różne operacje zarządzania maszynami wirtualnymi.

Podstawowym przypadkiem użycia rozszerzenia konfiguracji żądanego stanu platformy Azure (DSC) jest uruchamianie wersji maszynowej do [usługi konfiguracji stanu automatyzacji platformy Azure (DSC).](../../automation/automation-dsc-overview.md)
Usługa zapewnia [korzyści,](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) które obejmują bieżące zarządzanie konfiguracją maszyny Wirtualnej i integracji z innymi narzędziami operacyjnymi, takimi jak monitorowanie platformy Azure.
Za pomocą rozszerzenia do rejestrowania maszyn wirtualnych do usługi zapewnia elastyczne rozwiązanie, które działa nawet w ramach subskrypcji platformy Azure.

Rozszerzenia DSC można używać niezależnie od usługi Automation DSC.
Jednak to tylko wypchnąć konfigurację do maszyny Wirtualnej.
Nie jest dostępne żadne bieżące raportowanie, inne niż lokalnie na maszynie wirtualnej.

Ten artykuł zawiera informacje dotyczące obu scenariuszy: przy użyciu rozszerzenia DSC dla automatyzacji dołączania i przy użyciu rozszerzenia DSC jako narzędzie do przypisywania konfiguracji do maszyn wirtualnych przy użyciu zestawu Azure SDK.

## <a name="prerequisites"></a>Wymagania wstępne

- **Komputer lokalny:** Aby wchodzić w interakcje z rozszerzeniem maszyny Wirtualnej platformy Azure, należy użyć witryny Azure portal lub zestaw SDK programu Azure PowerShell.
- **Agent gościa:** Maszyna wirtualna platformy Azure skonfigurowana przez konfigurację DSC musi być systemem operacyjnym obsługującym platformę Windows Management Framework (WMF) 4.0 lub nowszą. Aby uzyskać pełną listę obsługiwanych wersji systemu operacyjnego, zobacz [historię wersji rozszerzenia DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

## <a name="terms-and-concepts"></a>Terminy i pojęcia

W tym przewodniku przyjęto zapoznanie się z następującymi pojęciami:

- **Konfiguracja**: Dokument konfiguracyjny DSC.
- **Węzeł:** Miejsce docelowe dla konfiguracji DSC. W tym dokumencie *węzeł* zawsze odwołuje się do maszyny Wirtualnej platformy Azure.
- **Dane konfiguracyjne**: Plik .psd1, który zawiera dane środowiskowe dla konfiguracji.

## <a name="architecture"></a>Architektura

Rozszerzenie usługi Azure DSC używa struktury agenta maszyny wirtualnej platformy Azure do dostarczania, uchwalania i raportowania konfiguracji DSC uruchomionych na maszynach wirtualnych platformy Azure. Rozszerzenie DSC akceptuje dokument konfiguracyjny i zestaw parametrów. Jeśli nie podano pliku, [domyślny skrypt konfiguracji](#default-configuration-script) jest osadzony z rozszerzeniem. Domyślny skrypt konfiguracji służy tylko do ustawiania metadanych w [lokalnym programie Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Gdy rozszerzenie jest wywoływane po raz pierwszy, instaluje wersję WMF przy użyciu następującej logiki:

- Jeśli systemem operacyjnym maszyny Wirtualnej platformy Azure jest system Windows Server 2016, nie są podejmowane żadne działania. W systemie Windows Server 2016 zainstalowano już najnowszą wersję programu PowerShell.
- Jeśli **wmfVersion** właściwość jest określona, ta wersja WMF jest zainstalowany, chyba że ta wersja jest niezgodna z systemu operacyjnego maszyny Wirtualnej.
- Jeśli nie **wmfVersion** właściwość jest określona, najnowsza odpowiednia wersja WMF jest zainstalowany.

Instalacja WMF wymaga ponownego uruchomienia. Po ponownym uruchomieniu rozszerzenie pobiera plik .zip, który jest określony we właściwości **modulesUrl,** jeśli jest podana. Jeśli ta lokalizacja znajduje się w magazynie obiektów Blob platformy Azure, można określić token sygnatury dostępu współdzielonego we właściwości **sasToken,** aby uzyskać dostęp do pliku. Po pobraniu i rozpakowaniu pliku .zip funkcja konfiguracji zdefiniowana w **configurationFunction** jest uruchamiana w celu wygenerowania pliku .mof([Managed Object Format).](https://docs.microsoft.com/windows/win32/wmisdk/managed-object-format--mof-) Rozszerzenie jest `Start-DscConfiguration -Force` następnie uruchamiane przy użyciu wygenerowanego pliku .mof. Rozszerzenie przechwytuje dane wyjściowe i zapisuje je w kanale stanu platformy Azure.

### <a name="default-configuration-script"></a>Domyślny skrypt konfiguracji

Rozszerzenie usługi Azure DSC zawiera domyślny skrypt konfiguracji, który jest przeznaczony do użycia podczas dołączania maszyny Wirtualnej do usługi Usługi Usługi DC automatyzacji usługi Azure Automation. Parametry skryptu są wyrównane z konfigurowalnymi właściwościami [lokalnego programu Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig). W przypadku parametrów skryptu zobacz [Domyślny skrypt konfiguracji](dsc-template.md#default-configuration-script) w [rozszerzeniu konfiguracji żądanego stanu z szablonami usługi Azure Resource Manager](dsc-template.md). Aby uzyskać pełny skrypt, zobacz [szablon szybki start platformy Azure w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informacje dotyczące rejestrowania się w usłudze konfiguracji stanu automatyzacji platformy Azure (DSC)

Podczas korzystania z rozszerzenia DSC zarejestrować węzeł z usługą konfiguracji stanu, trzy wartości będą musiały być dostarczone.

- RegistrationUrl - adres https konta usługi Azure Automation
- RegistrationKey - udostępniony klucz tajny używany do rejestrowania węzłów w usłudze
- NodeConfigurationName - nazwa konfiguracji węzła (MOF) do wyciągnięcia z usługi w celu skonfigurowania roli serwera

Te informacje można zobaczyć w witrynie Azure portal lub można użyć programu PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

W przypadku nazwy konfiguracji węzła upewnij się, że konfiguracja węzła istnieje w konfiguracji stanu platformy Azure.  Jeśli tak się nie stanie, wdrożenie rozszerzenia zwróci błąd.  Upewnij się również, że używasz nazwy *konfiguracji węzła,* a nie konfiguracji.
Konfiguracja jest zdefiniowana w skrypcie używanym [do kompilowania pliku Konfiguracja węzła (MOF).](https://docs.microsoft.com/azure/automation/automation-dsc-compile)
Nazwa będzie zawsze konfiguracja następuje `.` kropka `localhost` i albo lub określonej nazwy komputera.

## <a name="dsc-extension-in-resource-manager-templates"></a>Rozszerzenie DSC w szablonach Menedżera zasobów

W większości scenariuszy szablony wdrażania Menedżera zasobów są oczekiwanym sposobem pracy z rozszerzeniem DSC. Aby uzyskać więcej informacji i przykłady dołączania rozszerzenia DSC w szablonach wdrażania Menedżera zasobów, zobacz [Rozszerzenie konfiguracji żądanego stanu za pomocą szablonów usługi Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Rozszerzenia DSC polecenia cmdlet programu PowerShell

Polecenia cmdlet programu PowerShell, które są używane do zarządzania rozszerzeniem DSC, są najlepiej używane w interaktywnych scenariuszach rozwiązywania problemów i zbierania informacji. Polecenia cmdlet służy do pakowania, publikowania i monitorowania wdrożeń rozszerzenia DSC. Polecenia cmdlet dla rozszerzenia DSC nie są jeszcze aktualizowane w celu pracy z [domyślnym skryptem konfiguracji](#default-configuration-script).

Polecenie cmdlet **publish-AzVMDscConfiguration** przyjmuje plik konfiguracyjny, skanuje je w poszukiwaniu zasobów dsc zależnych, a następnie tworzy plik zip. Plik zip zawiera konfigurację i zasoby DSC, które są potrzebne do uchwalenia konfiguracji. Polecenie cmdlet można również utworzyć pakiet lokalnie przy użyciu *Parametru -OutputArchivePath.* W przeciwnym razie polecenie cmdlet publikuje plik zip do magazynu obiektów blob, a następnie zabezpiecza go za pomocą tokenu sygnatury dostępu Współdzielonego.

Skrypt konfiguracyjny .ps1, który tworzy polecenie cmdlet, znajduje się w pliku zip w katalogu głównym folderu archiwum. Folder modułu jest umieszczany w folderze archiwum w zasobach.

Polecenie cmdlet **Set-AzVMDscExtension** wstrzykuje ustawienia, których wymaga rozszerzenie DSC programu PowerShell do obiektu konfiguracji maszyny Wirtualnej.

Polecenie cmdlet **Get-AzVMDscExtension** pobiera stan rozszerzenia DSC określonej maszyny Wirtualnej.

Polecenie cmdlet **Get-AzVMDscExtensionStatus** pobiera stan konfiguracji DSC, która jest uchwalona przez program obsługi rozszerzeń DSC. Tę akcję można wykonać na jednej maszynie wirtualnej lub na grupie maszyn wirtualnych.

Polecenie cmdlet **Remove-AzVMDscExtension** usuwa program obsługi rozszerzeń z określonej maszyny Wirtualnej. To polecenie cmdlet *nie* usuwa konfiguracji, odinstalowywania WMF ani nie zmienia zastosowanych ustawień na maszynie Wirtualnej. Usuwa tylko program obsługi rozszerzeń. 

Ważne informacje o poleceniach cmdlet rozszerzenia DSC Menedżera zasobów:

- Polecenia cmdlet usługi Azure Resource Manager są synchroniczne.
- Wymagane są parametry *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version*i *Location.*
- *ArchiveResourceGroupName* jest parametrem opcjonalnym. Ten parametr można określić, gdy konto magazynu należy do innej grupy zasobów niż ta, w której tworzona jest maszyna wirtualna.
- Użyj przełącznika **Autoupdate,** aby automatycznie zaktualizować program obsługi rozszerzeń do najnowszej wersji, gdy jest ona dostępna. Ten parametr może spowodować ponowne uruchomienie maszyny Wirtualnej po wydaniu nowej wersji WMF.

### <a name="get-started-with-cmdlets"></a>Wprowadzenie do poleceń cmdlet

Rozszerzenie usługi Azure DSC może używać dokumentów konfiguracji DSC do bezpośredniego konfigurowania maszyn wirtualnych platformy Azure podczas wdrażania. Ten krok nie rejestruje węzła do automatyzacji. Węzeł *nie* jest zarządzany centralnie.

W poniższym przykładzie przedstawiono prosty przykład konfiguracji. Zapisz konfigurację lokalnie jako iisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Poniższe polecenia umieszczają skrypt iisInstall.ps1 na określonej maszynie wirtualnej. Polecenia również wykonać konfigurację, a następnie raport z powrotem na stan.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure może służyć do wdrażania rozszerzenia DSC do istniejącej maszyny wirtualnej.

Dla maszyny wirtualnej z systemem Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Dla maszyny wirtualnej z systemem Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Funkcjonalność witryny Azure portal

Aby skonfigurować dsc w portalu:

1. Przejdź do maszyny Wirtualnej.
2. W obszarze **Ustawienia** wybierz pozycję **Rozszerzenia**.
3. Na utworzonej nowej stronie wybierz pozycję **+ Dodaj**, a następnie wybierz pozycję **Konfiguracja żądanego stanu programu PowerShell**.
4. Kliknij **pozycję Utwórz** u dołu strony z informacjami o rozszerzeniu.

Portal zbiera następujące dane wejściowe:

- **Moduły konfiguracyjne lub skrypt:** To pole jest obowiązkowe (formularz nie został zaktualizowany dla [domyślnego skryptu konfiguracji](#default-configuration-script)). Moduły konfiguracji i skrypty wymagają pliku .ps1, który ma skrypt konfiguracyjny lub plik .zip ze skryptem konfiguracyjnym .ps1 w katalogu głównym. Jeśli używasz pliku zip, wszystkie zasoby zależne muszą być zawarte w folderach modułu w pliku zip. Plik zip można utworzyć przy użyciu polecenia cmdlet **Publish-AzureMDscConfiguration -OutputArchivePath,** które jest zawarte w zestawie SDK programu Azure PowerShell. Plik zip jest przekazyany do magazynu obiektów blob użytkownika i zabezpieczony tokenem sygnatury dostępu Współdzielonego.

- **Kwalifikowana przez moduł Nazwa konfiguracji:** W pliku .ps1 można dołączyć wiele funkcji konfiguracyjnych. Wprowadź nazwę skryptu konfiguracji .ps1, po której następuje \\ nazwa funkcji konfiguracji. Jeśli na przykład skrypt .ps1 ma nazwę configuration.ps1, a konfiguracją jest **IisInstall**, wprowadź **configuration.ps1\IisInstall**.

- **Argumenty konfiguracji:** Jeśli funkcja konfiguracji przyjmuje argumenty, wprowadź je tutaj w formacie **argumentName1=value1,argumentName2=value2**. Ten format jest innym formatem, w którym argumenty konfiguracji są akceptowane w poleceniach cmdlet programu PowerShell lub szablonach Menedżera zasobów.

- **Plik PSD1 danych konfiguracyjnych:** To pole jest opcjonalne. Jeśli konfiguracja wymaga pliku danych konfiguracji w psd1, użyj tego pola, aby wybrać pole danych i przekazać je do magazynu obiektów blob użytkownika. Plik danych konfiguracji jest zabezpieczony tokenem sygnatury dostępu Współdzielonego w magazynie obiektów blob.

- **Wersja WMF**: Określa wersję programu Windows Management Framework (WMF), która powinna być zainstalowana na maszynie wirtualnej. Ustawienie tej właściwości na najnowszą instaluje najnowszą wersję WMF. Obecnie jedynymi możliwymi wartościami dla tej właściwości są 4.0, 5.0, 5.1 i najnowsze. Te możliwe wartości podlegają aktualizacjom. Wartość domyślna jest **najnowsza**.

- **Zbieranie danych**: Określa, czy rozszerzenie będzie zbierać dane telemetryczne. Aby uzyskać więcej informacji, zobacz [Zbieranie danych rozszerzenia usługi Azure DSC](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Wersja**: Określa wersję rozszerzenia DSC do zainstalowania. Aby uzyskać informacje o wersjach, zobacz [historia wersji rozszerzenia DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Automatyczne uaktualnianie wersji pomocniczej:** To pole jest mapowane na przełącznik **AutoUpdate** w poleceniach cmdlet i umożliwia automatyczne aktualizowanie rozszerzenia do najnowszej wersji podczas instalacji. **Tak** poinstruuje program obsługi rozszerzeń, aby użyć najnowszej dostępnej wersji i **nie** wymusi **wersję określoną** do zainstalowania. Wybranie opcji **Tak** ani **Nie** jest takie samo, jak wybranie **opcji Nie**.

## <a name="logs"></a>Dzienniki

Dzienniki rozszerzenia są przechowywane w następującej lokalizacji:`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do centrum dokumentacji programu [PowerShell](/powershell/scripting/dsc/overview/overview).
- Sprawdź [szablon Menedżera zasobów dla rozszerzenia DSC](dsc-template.md).
- Aby uzyskać więcej funkcji, którymi można zarządzać za pomocą programu PowerShell DSC, a aby uzyskać więcej zasobów DSC, przejrzyj [galerię programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Aby uzyskać szczegółowe informacje na temat przekazywania poufnych parametrów do konfiguracji, zobacz [Bezpieczne zarządzanie poświadczeniami za pomocą programu obsługi rozszerzeń DSC](dsc-credentials.md).
