---
title: Konfiguracja żądanego stanu na platformie Azure — omówienie
description: Dowiedz się, jak używać programu obsługi rozszerzeń Microsoft Azure dla konfiguracji żądanego stanu (DSC) programu PowerShell. Artykuł zawiera wymagania wstępne, architekturę i polecenia cmdlet.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: evansma
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 07/13/2020
ms.author: magoedte
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 900273ec48c71e6f88d28bccff6f1e2abd412c1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079578"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Wprowadzenie do procedury obsługi rozszerzenia Azure Desired State Configuration

Agent maszyny wirtualnej platformy Azure i powiązane rozszerzenia są częścią usług infrastruktury Microsoft Azure. Rozszerzenia maszyn wirtualnych to składniki oprogramowania, które rozszerzają funkcjonalność maszyny wirtualnej i upraszczają różne operacje zarządzania MASZYNami wirtualnymi.

Podstawowym przypadkiem użycia dla rozszerzenia konfiguracji żądanego stanu (DSC) platformy Azure jest uruchomienie maszyny wirtualnej do [usługi konfiguracji stanu Azure Automation (DSC)](../../automation/automation-dsc-overview.md).
Usługa oferuje [korzyści](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) , które obejmują bieżące zarządzanie konfiguracją maszyny wirtualnej i integrację z innymi narzędziami operacyjnymi, takimi jak monitorowanie platformy Azure.
Korzystanie z rozszerzenia do rejestrowania maszyn wirtualnych w usłudze zapewnia elastyczne rozwiązanie, które nawet działa w ramach subskrypcji platformy Azure.

Można użyć rozszerzenia DSC niezależnie od usługi Automation DSC.
Jednak spowoduje to wypchnięcie konfiguracji tylko do maszyny wirtualnej.
Żadne bieżące raporty nie są dostępne, inne niż lokalnie na maszynie wirtualnej.

Ten artykuł zawiera informacje o obu scenariuszach: przy użyciu rozszerzenia DSC na potrzeby dołączania do usługi Automation oraz przy użyciu rozszerzenia DSC jako narzędzia do przypisywania konfiguracji do maszyn wirtualnych przy użyciu zestawu Azure SDK.

## <a name="prerequisites"></a>Wymagania wstępne

- **Komputer lokalny**: aby móc korzystać z rozszerzenia maszyny wirtualnej platformy Azure, musisz użyć Azure Portal lub zestawu Azure PowerShell SDK.
- **Agent gościa**: maszyna wirtualna platformy Azure, która jest skonfigurowana za pomocą konfiguracji DSC, musi być systemem operacyjnym, który obsługuje system Windows Management Framework (WMF) 4,0 lub nowszy. Aby zapoznać się z pełną listą obsługiwanych wersji systemu operacyjnego, zobacz [historię wersji rozszerzenia DSC](../../automation/automation-dsc-extension-history.md).

## <a name="terms-and-concepts"></a>Warunki i pojęcia

W tym przewodniku założono znajomość następujących pojęć:

- **Konfiguracja**: dokument konfiguracji DSC.
- **Węzeł**: obiekt docelowy konfiguracji DSC. W tym dokumencie *węzeł* zawsze odwołuje się do maszyny wirtualnej platformy Azure.
- **Dane konfiguracji**: plik. psd1, który zawiera dane dotyczące środowiska dla konfiguracji.

## <a name="architecture"></a>Architektura

Rozszerzenie Azure DSC korzysta z platformy agenta maszyny wirtualnej platformy Azure w celu dostarczania, przyjęcia i raportowania konfiguracji DSC działających na maszynach wirtualnych platformy Azure. Rozszerzenie DSC akceptuje dokument konfiguracji i zestaw parametrów. Jeśli nie podano pliku, [domyślny skrypt konfiguracji](#default-configuration-script) jest osadzony z rozszerzeniem. Domyślny skrypt konfiguracji służy tylko do ustawiania metadanych w [lokalnej Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Gdy rozszerzenie jest wywoływane po raz pierwszy, instaluje wersję programu WMF przy użyciu następującej logiki:

- Jeśli system operacyjny maszyny wirtualnej platformy Azure to Windows Server 2016, nie jest podejmowana żadna akcja. System Windows Server 2016 ma już zainstalowaną najnowszą wersję programu PowerShell.
- Jeśli zostanie określona właściwość **wmfVersion** , jest zainstalowana ta wersja programu WMF, chyba że ta wersja jest niezgodna z systemem operacyjnym maszyny wirtualnej.
- Jeśli właściwość **wmfVersion** nie zostanie określona, zostanie zainstalowana najnowsza odpowiednia wersja programu WMF.

Instalowanie pakietu WMF wymaga ponownego uruchomienia. Po ponownym uruchomieniu rozszerzenie pobiera plik zip, który jest określony we właściwości **modulesUrl** , jeśli został podany. Jeśli ta lokalizacja znajduje się w usłudze Azure Blob Storage, możesz określić token SAS we właściwości **sasToken** , aby uzyskać dostęp do pliku. Po pobraniu i rozpakowaniu pliku zip funkcja konfiguracji zdefiniowana w **configurationFunction** uruchamia się w celu wygenerowania plik mof ([Managed Object Format](/windows/win32/wmisdk/managed-object-format--mof-)). Rozszerzenie zostanie uruchomione przy `Start-DscConfiguration -Force` użyciu wygenerowanego pliku MOF. Rozszerzenie przechwytuje dane wyjściowe i zapisuje je w kanale stanu platformy Azure.

### <a name="default-configuration-script"></a>Domyślny skrypt konfiguracji

Rozszerzenie Azure DSC zawiera domyślny skrypt konfiguracji, który jest przeznaczony do użycia podczas dołączania maszyny wirtualnej do usługi Azure Automation DSC. Parametry skryptu są wyrównane z konfigurowalnymi właściwościami [Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaConfig). Aby poznać parametry skryptu, zobacz [domyślny skrypt konfiguracji](dsc-template.md#default-configuration-script) w [rozszerzeniu konfiguracji żądanego stanu z szablonami Azure Resource Manager](dsc-template.md). Aby zapoznać się z pełnym skryptem, zobacz [szablon szybkiego startu platformy Azure w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informacje dotyczące rejestrowania w usłudze Azure Automation State Configuration (DSC)

W przypadku korzystania z rozszerzenia DSC w celu zarejestrowania węzła z usługą konfiguracji stanu należy podać trzy wartości.

- RegistrationUrl — adres https konta Azure Automation
- RegistrationKey — wspólny klucz tajny używany do rejestrowania węzłów w usłudze
- NodeConfigurationName — nazwa konfiguracji węzła (MOF) do ściągnięcia z usługi w celu skonfigurowania roli serwera

Te informacje mogą być widoczne w Azure Portal lub można użyć programu PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

W polu Nazwa konfiguracji węzła upewnij się, że konfiguracja węzła istnieje w obszarze Konfiguracja stanu platformy Azure.  Jeśli tak nie jest, wdrożenie rozszerzenia zwróci błąd.  Upewnij się również, że używasz nazwy *konfiguracji węzła* , a nie konfiguracji.
Konfiguracja jest definiowana w skrypcie, który jest używany [do kompilowania konfiguracji węzła (plik MOF)](../../automation/automation-dsc-compile.md).
Nazwa będzie zawsze konfiguracją, po której następuje okres `.` i albo `localhost` określona nazwa komputera.

## <a name="dsc-extension-in-resource-manager-templates"></a>Rozszerzenie DSC w szablonach Menedżer zasobów

W większości scenariuszy Menedżer zasobów szablony wdrażania są oczekiwanym sposobem pracy z rozszerzeniem DSC. Aby uzyskać więcej informacji i zapoznać się z przykładami dotyczącymi sposobu uwzględniania rozszerzenia DSC w szablonach wdrażania Menedżer zasobów, zobacz [rozszerzenie konfiguracji żądanego stanu z szablonami Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell dla rozszerzenia DSC

Polecenia cmdlet programu PowerShell, które są używane do zarządzania rozszerzeniem DSC, najlepiej używać w scenariuszach interaktywnego rozwiązywania problemów i zbierania informacji. Za pomocą poleceń cmdlet można spakować, opublikować i monitorować wdrożenia rozszerzenia DSC. Polecenia cmdlet dla rozszerzenia DSC nie zostały jeszcze zaktualizowane do pracy z [domyślnym skryptem konfiguracji](#default-configuration-script).

Polecenie cmdlet **Publish-AzVMDscConfiguration** pobiera plik konfiguracji, skanuje go pod kątem zależnych zasobów DSC, a następnie tworzy plik. zip. Plik. zip zawiera konfigurację i zasoby DSC, które są konieczne do przekonfigurowania. Polecenie cmdlet może również utworzyć pakiet lokalnie za pomocą parametru *-OutputArchivePath* . W przeciwnym razie polecenie cmdlet opublikuje plik zip w usłudze BLOB Storage, a następnie zabezpieczy go za pomocą tokenu SAS.

Skrypt konfiguracji. ps1 tworzony przez polecenie cmdlet znajduje się w pliku zip w folderze głównym folderu archiwum. Folder modułu zostanie umieszczony w folderze Archiwum w obszarze zasoby.

Polecenie cmdlet **Set-AzVMDscExtension** wprowadza ustawienia wymagane przez rozszerzenie DSC programu PowerShell do obiektu konfiguracji maszyny wirtualnej.

Polecenie cmdlet **Get-AzVMDscExtension** Pobiera stan rozszerzenia DSC dla określonej maszyny wirtualnej.

Polecenie cmdlet **Get-AzVMDscExtensionStatus** Pobiera stan konfiguracji DSC, który został wdrożony przez procedurę obsługi rozszerzenia DSC. Tę akcję można wykonać na jednej maszynie wirtualnej lub w grupie maszyn wirtualnych.

Polecenie cmdlet **Remove-AzVMDscExtension** usuwa procedurę obsługi rozszerzenia z określonej maszyny wirtualnej. To polecenie cmdlet *nie powoduje usunięcia konfiguracji* , odinstalowania programu WMF lub zmiany ustawień zastosowanych na maszynie wirtualnej. Powoduje tylko usunięcie procedury obsługi rozszerzenia.

Ważne informacje dotyczące Menedżer zasobów poleceń cmdlet rozszerzenia DSC:

- Polecenia cmdlet Azure Resource Manager są synchroniczne.
- Parametry *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version*i *Location* są wymagane.
- *ArchiveResourceGroupName* jest opcjonalnym parametrem. Ten parametr można określić, jeśli konto magazynu należy do innej grupy zasobów niż ta, w której utworzono maszynę wirtualną.
- Użyj przełącznika **autoaktualizacje** , aby automatycznie zaktualizować procedurę obsługi rozszerzenia do najnowszej wersji, gdy jest ona dostępna. Ten parametr może spowodować ponowne uruchomienie maszyny wirtualnej po wydaniu nowej wersji programu WMF.

### <a name="get-started-with-cmdlets"></a>Wprowadzenie do poleceń cmdlet

Rozszerzenie DSC platformy Azure może używać dokumentów konfiguracji DSC do bezpośredniego konfigurowania maszyn wirtualnych platformy Azure podczas wdrażania. Ten krok nie rejestruje węzła do automatyzacji. Węzeł *nie* jest zarządzany centralnie.

Poniższy przykład pokazuje prosty przykład konfiguracji. Zapisz konfigurację lokalnie jako iisInstall.ps1.

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

Poniższe polecenia umieszczają skrypt iisInstall.ps1 na określonej maszynie wirtualnej. Polecenia również wykonują konfigurację, a następnie raportują stan ponownie.

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

Przy użyciu interfejsu wiersza polecenia platformy Azure można wdrożyć rozszerzenie DSC na istniejącej maszynie wirtualnej.

W przypadku maszyny wirtualnej z systemem Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

W przypadku maszyny wirtualnej z systemem Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Funkcja Azure Portal

Aby skonfigurować DSC w portalu:

1. Przejdź do maszyny wirtualnej.
2. W obszarze **Ustawienia** wybierz pozycję **Rozszerzenia**.
3. Na utworzonej nowej stronie wybierz pozycję **+ Dodaj**, a następnie wybierz pozycję **Konfiguracja żądanego stanu programu PowerShell**.
4. Kliknij pozycję **Utwórz** w dolnej części strony informacje o rozszerzeniu.

Portal zbiera następujące dane wejściowe:

- **Moduły lub skrypty konfiguracyjne**: to pole jest obowiązkowe (formularz nie został zaktualizowany dla [domyślnego skryptu konfiguracji](#default-configuration-script)). Moduły konfiguracji i skrypty wymagają pliku. ps1, który ma skrypt konfiguracji lub plik zip z skryptem konfiguracyjnym. ps1 w katalogu głównym. Jeśli używasz pliku. zip, wszystkie zasoby zależne muszą być dołączone do folderów modułów w pliku zip. Plik zip można utworzyć za pomocą polecenia cmdlet **Publish-AzureVMDscConfiguration-OutputArchivePath** , które jest zawarte w zestawie SDK Azure PowerShell. Plik zip jest przekazywany do magazynu obiektów BLOB użytkownika i zabezpieczony za pomocą tokenu SAS.

- **Kwalifikowana nazwa modułu konfiguracji**: w pliku ps1 można uwzględnić wiele funkcji konfiguracyjnych. Wprowadź nazwę skryptu Configuration. ps1, \\ a następnie nazwę funkcji konfiguracji. Na przykład jeśli skrypt. ps1 ma nazwę configuration.ps1 a konfiguracja to **IisInstall**, wprowadź **configuration.ps1 \iisinstall**.

- **Argumenty konfiguracji**: Jeśli funkcja konfiguracji przyjmuje argumenty, wprowadź je w tym miejscu w formacie **argumentName1 = wartość1, argumentName2 = wartość2**. Ten format jest innym formatem, w którym argumenty konfiguracji są akceptowane w poleceniach cmdlet programu PowerShell lub szablonach Menedżer zasobów.

- **Plik PSD1 danych konfiguracji**: Jeśli konfiguracja wymaga pliku danych konfiguracji w programie `.psd1` , należy użyć tego pola, aby wybrać plik danych i przekazać go do magazynu obiektów BLOB użytkownika. Plik danych konfiguracji jest zabezpieczony przez token sygnatury dostępu współdzielonego w magazynie obiektów BLOB.

- **Wersja WMF**: określa wersję programu Windows Management Framework (WMF), która ma zostać zainstalowana na maszynie wirtualnej. Ustawienie tej właściwości na Najnowsza spowoduje zainstalowanie najnowszej wersji programu WMF. Obecnie jedyne możliwe wartości tej właściwości to 4,0, 5,0, 5,1 i najnowsze. Te możliwe wartości podlegają aktualizacjom. Wartość domyślna to **Najnowsza**.

- **Zbieranie danych**: określa, czy rozszerzenie będzie zbierać dane telemetryczne. Aby uzyskać więcej informacji, zobacz [zbieranie danych rozszerzenia DSC platformy Azure](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).

- **Wersja**: określa wersję rozszerzenia DSC do zainstalowania. Aby uzyskać informacje na temat wersji, zobacz [historia wersji rozszerzenia DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Automatycznie uaktualniaj wersję pomocniczą**: to pole jest mapowane **na przełącznik** autouzupełniania w poleceniach cmdlet i włącza rozszerzenie do automatycznej aktualizacji do najnowszej wersji podczas instalacji. **Tak** nakazuje programowi obsługi rozszerzenia użycie najnowszej dostępnej wersji, a wartość **nie** spowoduje wymuszenie instalacji określonej **wersji** . Wybranie opcji **tak** nie **jest** tak samo, jak w przypadku wybrania opcji **nie**.

## <a name="logs"></a>Dzienniki

Dzienniki rozszerzenia są przechowywane w następującej lokalizacji: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do [centrum dokumentacji programu PowerShell](/powershell/scripting/dsc/overview/overview).
- Zapoznaj się z [szablonem Menedżer zasobów dla rozszerzenia DSC](dsc-template.md).
- Aby uzyskać więcej funkcji, którymi można zarządzać za pomocą programu PowerShell DSC i więcej zasobów DSC, przejrzyj [galerię programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Aby uzyskać szczegółowe informacje o przekazywaniu poufnych parametrów do konfiguracji, zobacz [Zarządzanie poświadczeniami bezpiecznie przy użyciu programu obsługi rozszerzeń DSC](dsc-credentials.md).
