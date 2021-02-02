---
title: Rozszerzenie niestandardowego skryptu platformy Azure dla systemu Windows
description: Automatyzowanie zadań konfiguracyjnych maszyn wirtualnych z systemem Windows przy użyciu rozszerzenia niestandardowego skryptu
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/31/2020
ms.author: robreed
ms.openlocfilehash: d4cfb8d6a48ac41b4deb8913d4277f07cbb43208
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258708"
---
# <a name="custom-script-extension-for-windows"></a>Rozszerzenie niestandardowego skryptu dla systemu Windows

Rozszerzenie skryptu niestandardowego pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań związanych z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Rozszerzenie niestandardowego skryptu integruje się z szablonami Azure Resource Manager i można je uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, Azure Portal lub API REST maszyny wirtualnej platformy Azure.

Ten dokument zawiera szczegółowe informacje dotyczące używania niestandardowego rozszerzenia skryptu przy użyciu modułu Azure PowerShell, szablonów Azure Resource Manager i szczegółów dotyczących rozwiązywania problemów w systemach Windows.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]  
> Nie należy używać rozszerzenia skryptu niestandardowego do uruchamiania Update-AzVM z tą samą maszyną wirtualną jako parametrem, ponieważ będzie ona oczekiwać na siebie.  

### <a name="operating-system"></a>System operacyjny

Rozszerzenie niestandardowego skryptu dla systemu Windows zostanie uruchomione na obsługiwanym rozszerzeniu OSs.
### <a name="windows"></a>Windows

* Windows Server 2008 z dodatkiem R2
* Windows Server 2012
* Windows Server 2012 z dodatkiem R2
* Windows 10
* Windows Server 2016
* System Windows Server 2016 Core
* Windows Server 2019
* System Windows Server 2019 Core

### <a name="script-location"></a>Lokalizacja skryptu

Rozszerzenie można skonfigurować tak, aby korzystało z poświadczeń usługi Azure Blob Storage w celu uzyskania dostępu do usługi Azure Blob Storage. Lokalizacja skryptu może być w dowolnym miejscu, pod warunkiem, że maszyna wirtualna może kierować do tego punktu końcowego, takiego jak GitHub lub wewnętrzny serwer plików.

### <a name="internet-connectivity"></a>Łączność z Internetem

Jeśli konieczne jest pobranie skryptu z zewnątrz, takiego jak z witryny GitHub lub Azure Storage, należy otworzyć dodatkową zaporę i porty sieciowej grupy zabezpieczeń. Jeśli na przykład skrypt znajduje się w usłudze Azure Storage, możesz zezwolić na dostęp za pomocą tagów usługi Azure sieciowej grupy zabezpieczeń dla [magazynu](../../virtual-network/network-security-groups-overview.md#service-tags).

Należy zauważyć, że rozszerzenie CustomScript nie ma żadnego sposobu pomijania sprawdzania poprawności certyfikatu. Dlatego jeśli pobierasz z zabezpieczonej lokalizacji z przykładem. certyfikat z podpisem własnym może zakończyć się błędami, takimi jak *"certyfikat zdalny jest nieprawidłowy zgodnie z procedurą walidacji"*. Upewnij się, że certyfikat został poprawnie zainstalowany w magazynie *"Zaufane główne urzędy certyfikacji"* na maszynie wirtualnej.

Jeśli skrypt znajduje się na serwerze lokalnym, może być konieczne otwarcie dodatkowych portów zapory i sieciowych grup zabezpieczeń.

### <a name="tips-and-tricks"></a>Porady i wskazówki

* Najwyższy współczynnik błędów dla tego rozszerzenia wynika z błędów składni w skrypcie, testowanie uruchamiania skryptu bez błędów oraz umieszczanie dodatkowego rejestrowania w skrypcie, aby ułatwić znalezienie tego, gdzie nie powiodło się.
* Pisz skrypty idempotentne. Pozwala to zagwarantować, że jeśli zostaną uruchomione ponownie przypadkowo, nie spowoduje to zmian w systemie.
* Upewnij się, że skrypty nie wymagają udziału użytkownika w trakcie działania.
* Dozwolony czas uruchamiania skryptu wynosi 90 minut — każdy dłuższy czas spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie umieszczaj operacji ponownego uruchamiania wewnątrz skryptu; ta akcja spowoduje problemy z innymi instalowanymi rozszerzeniami. Po ponownym uruchomieniu rozszerzenie nie będzie kontynuować pracy, gdy zostanie uruchomione jeszcze raz.
* Jeśli masz skrypt, który spowoduje ponowne uruchomienie komputera, zainstaluj aplikacje i Uruchom skrypty, możesz zaplanować ponowne uruchomienie przy użyciu zaplanowanego zadania systemu Windows lub użyć narzędzi takich jak DSC, Chef lub Puppet.
* Nie zaleca się uruchamiania skryptu, który spowoduje zatrzymanie lub aktualizację agenta maszyny wirtualnej. Może to opuścić rozszerzenie w stanie przejścia, co prowadzi do przekroczenia limitu czasu.
* Rozszerzenie uruchomi skrypt tylko raz. Jeśli chcesz uruchomić skrypt przy każdym rozruchu, musisz użyć rozszerzenia w celu utworzenia zaplanowanego zadania systemu Windows.
* Jeśli chcesz zaplanować czas uruchomienia skryptu, użyj rozszerzenia w celu utworzenia zaplanowanego zadania systemu Windows.
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Jeśli potrzebujesz częstszych aktualizacji stanu działającego skryptu, musisz utworzyć własne rozwiązanie.
* Niestandardowe rozszerzenie skryptu nie obsługuje natywnie serwerów proxy, jednak można użyć narzędzia transferu plików, które obsługuje serwery proxy w skrypcie, na przykład *zwinięcie*
* Pamiętaj, że skrypt lub polecenia mogą polegać na lokalizacjach katalogów innych niż domyślne. Przygotuj logikę obsługującą taką sytuację.
* Rozszerzenie niestandardowego skryptu zostanie uruchomione na koncie LocalSystem
* Jeśli planujesz używać właściwości *storageAccountName* i *storageAccountKey* , te właściwości muszą być umieszczone w *protectedSettings*.

## <a name="extension-schema"></a>Schemat rozszerzenia

Konfiguracja rozszerzenia niestandardowego skryptu określa elementy, takie jak lokalizacja skryptu i polecenie do uruchomienia. Tę konfigurację można zapisać w plikach konfiguracji, określić ją w wierszu polecenia lub określić w szablonie Azure Resource Manager.

Poufne dane można przechowywać w chronionej konfiguracji, która jest szyfrowana i odszyfrowywana tylko wewnątrz maszyny wirtualnej. Konfiguracja chroniona jest przydatna, gdy polecenie wykonywania zawiera wpisy tajne, takie jak hasło.

Te elementy powinny być traktowane jako dane poufne i określone w konfiguracji ustawień chronionych przez rozszerzenia. Dane ustawienia chronionego rozszerzenia maszyny wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> Właściwość managedIdentity **nie może** być używana w połączeniu z właściwościami StorageAccountName lub storageAccountKey

> [!NOTE]
> Tylko jedna wersja rozszerzenia może być zainstalowana na maszynie wirtualnej w danym momencie, co oznacza, że niestandardowy skrypt dwa razy w tym samym szablonie Menedżer zasobów dla tej samej maszyny wirtualnej zakończy się niepowodzeniem.

> [!NOTE]
> Można użyć tego schematu wewnątrz zasobu VirtualMachine lub jako zasób autonomiczny. Nazwa zasobu musi mieć format "virtualMachineName/ExtensionName", jeśli to rozszerzenie jest używane jako zasób autonomiczny w szablonie ARM.

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publisher | Microsoft.Compute | ciąg |
| typ | CustomScriptExtension | ciąg |
| typeHandlerVersion | 1.10 | int |
| fileUris (np.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| Sygnatura czasowa (np.) | 123456789 | 32-bitowa liczba całkowita |
| Sekcji commandtoexecute (np.) | PowerShell — ExecutionPolicy nieograniczony-plik configure-music-app.ps1 | ciąg |
| storageAccountName (np.) | examplestorageacct | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | ciąg |
| managedIdentity (np.) | {} lub {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} lub {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | Obiekt JSON |

>[!NOTE]
>W tych nazwach właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazw, jak pokazano poniżej.

#### <a name="property-value-details"></a>Szczegóły wartości właściwości

* `commandToExecute`: (**wymagane**, ciąg) skrypt punktu wejścia do wykonania. Użyj tego pola zamiast tego, jeśli polecenie zawiera wpisy tajne, takie jak hasła, lub fileUris są poufne.
* `fileUris`: (opcjonalne, tablica ciągów) adresy URL dla plików do pobrania.
* `timestamp` (opcjonalnie, 32-bitową liczbę całkowitą) Użyj tego pola tylko do wyzwalania ponownego uruchomienia skryptu przez zmianę wartości tego pola.  Dopuszczalna jest dowolna wartość całkowita; musi on być inny niż Poprzednia wartość.
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu. W przypadku określenia poświadczeń magazynu wszystkie `fileUris` muszą być adresami URL dla obiektów blob platformy Azure.
* `storageAccountKey`: (opcjonalnie, String) klucz dostępu konta magazynu
* `managedIdentity`: (opcjonalnie obiekt JSON) [zarządzana tożsamość](../../active-directory/managed-identities-azure-resources/overview.md) do pobierania plików
  * `clientId`: (opcjonalnie, String) identyfikator klienta zarządzanej tożsamości
  * `objectId`: (opcjonalnie, String) identyfikator obiektu tożsamości zarządzanej

Następujące wartości można ustawić w ustawieniach publicznych lub chronionych, rozszerzenie odrzuci każdą konfigurację, w której poniższe wartości są ustawione zarówno w ustawieniach publicznych, jak i chronionych.

* `commandToExecute`

Korzystanie z ustawień publicznych może być przydatne w przypadku debugowania, ale zaleca się używanie ustawień chronionych.

Ustawienia publiczne są wysyłane w postaci zwykłego tekstu do maszyny wirtualnej, na której skrypt zostanie wykonany.  Ustawienia chronione są szyfrowane przy użyciu klucza znanego tylko na platformie Azure i maszynie wirtualnej. Ustawienia są zapisywane na maszynie wirtualnej w miarę ich wysyłania, czyli jeśli ustawienia zostały zaszyfrowane, są one zaszyfrowane na maszynie wirtualnej. Certyfikat używany do odszyfrowywania zaszyfrowanych wartości jest przechowywany na maszynie wirtualnej i używany do odszyfrowywania ustawień (w razie potrzeby) w czasie wykonywania.

####  <a name="property-managedidentity"></a>Właściwość: managedIdentity
> [!NOTE]
> Ta właściwość **musi** być określona tylko w ustawieniach chronionych.

CustomScript (wersja 1,10 lub nowszy) obsługuje [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md) do pobierania plików z adresów URL określonych w ustawieniu "fileUris". Umożliwia CustomScript dostęp do prywatnych obiektów blob lub kontenerów usługi Azure Storage bez konieczności przekazywania wpisów tajnych, takich jak tokeny SAS lub klucze kont magazynu.

Aby można było użyć tej funkcji, użytkownik musi dodać tożsamość przypisaną przez [system](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) lub [przypisanej do użytkownika](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) do maszyny wirtualnej lub VMSS, gdzie oczekiwano CustomScript, i [przyznać zarządzanej tożsamości dostęp do kontenera lub obiektu BLOB usługi Azure Storage](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Aby użyć tożsamości przypisanej do systemu na docelowej maszynie wirtualnej/VMSS, ustaw wartość pola "managedidentity" na pusty obiekt JSON. 

> Przykład:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Aby użyć tożsamości przypisanej przez użytkownika na docelowej maszynie wirtualnej/VMSS, należy skonfigurować pole "managedidentity" z IDENTYFIKATORem klienta lub IDENTYFIKATORem obiektu tożsamości zarządzanej.

> Przykłady:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> Właściwość managedIdentity **nie może** być używana w połączeniu z właściwościami StorageAccountName lub storageAccountKey

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Schemat JSON, który jest szczegółowo opisany w poprzedniej sekcji, można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie niestandardowego skryptu podczas wdrażania. W poniższych przykładach pokazano, jak używać niestandardowego rozszerzenia skryptu:

* [Samouczek: wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Wdrażanie aplikacji dwuwarstwowej w systemie Windows i usłudze Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell

`Set-AzVMCustomScriptExtension`Polecenie może służyć do dodawania niestandardowego rozszerzenia skryptu do istniejącej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Więcej przykładów

### <a name="using-multiple-scripts"></a>Korzystanie z wielu skryptów

W tym przykładzie masz trzy skrypty, które są używane do tworzenia serwera. **Sekcji commandtoexecute** wywołuje pierwszy skrypt, a następnie dysponuje się opcjami dotyczącymi wywoływania innych metod. Na przykład można mieć skrypt główny, który kontroluje wykonywanie, z odpowiednią obsługą błędów, rejestrowaniem i zarządzaniem stanem. Skrypty są pobierane na komputer lokalny na potrzeby uruchamiania programu. Na przykład w `1_Add_Tools.ps1` przypadku wywołania `2_Add_Features.ps1` przez dodanie  `.\2_Add_Features.ps1` do skryptu i powtórz ten proces dla innych skryptów zdefiniowanych w programie `$settings` .

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings `
    -ProtectedSettings $protectedSettings;
```

### <a name="running-scripts-from-a-local-share"></a>Uruchamianie skryptów z udziału lokalnego

W tym przykładzie możesz chcieć użyć lokalnego serwera SMB dla lokalizacji skryptu. Dzięki temu nie trzeba podawać żadnych innych ustawień, z wyjątkiem **sekcji commandtoexecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Jak uruchomić skrypt niestandardowy więcej niż raz przy użyciu interfejsu wiersza polecenia

Jeśli chcesz uruchomić rozszerzenie skryptu niestandardowego więcej niż raz, tę akcję można wykonać tylko w następujących warunkach:

* **Nazwa** rozszerzenia jest taka sama jak w poprzednim wdrożeniu rozszerzenia.
* Zaktualizuj konfigurację w przeciwnym razie polecenie nie zostanie wykonane jeszcze raz. Można dodać w właściwości dynamicznej do polecenia, takich jak sygnatura czasowa.

Alternatywnie można ustawić **wartość true** dla właściwości [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) .

### <a name="using-invoke-webrequest"></a>Używanie Invoke-WebRequest

Jeśli używasz polecenia [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) w skrypcie, musisz określić parametr lub w przeciwnym razie `-UseBasicParsing` zostanie wyświetlony następujący komunikat o błędzie podczas sprawdzania szczegółowego stanu:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Aby wdrożyć rozszerzenie niestandardowego skryptu na zestawie skalowania, zobacz [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)

## <a name="classic-vms"></a>Klasyczne maszyny wirtualne

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Aby wdrożyć rozszerzenie niestandardowego skryptu na klasycznych maszynach wirtualnych, można użyć Azure Portal lub klasycznych poleceń cmdlet Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

Przejdź do klasycznego zasobu maszyny wirtualnej. W obszarze **Ustawienia** wybierz pozycję **rozszerzenia** .

Kliknij pozycję **+ Dodaj** i na liście zasobów wybierz pozycję **niestandardowe rozszerzenie skryptu**.

Na stronie **rozszerzenie instalacji** wybierz lokalny plik programu PowerShell i Wypełnij wszystkie argumenty i kliknij przycisk **OK**.

### <a name="powershell"></a>PowerShell

Użyj polecenia cmdlet [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) , aby dodać rozszerzenie niestandardowego skryptu do istniejącej maszyny wirtualnej.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu modułu Azure PowerShell. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Dane wyjściowe rozszerzenia są rejestrowane w plikach znalezionych w następującym folderze na docelowej maszynie wirtualnej.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Określone pliki zostaną pobrane do następującego folderu na docelowej maszynie wirtualnej.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

gdzie `<n>` jest dziesiętną liczbą całkowitą, która może ulec zmianie między wykonaniami rozszerzenia.  `1.*`Wartość jest zgodna z rzeczywistą, bieżącą `typeHandlerVersion` wartością rozszerzenia.  Na przykład faktyczny katalog może być `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

Po wykonaniu `commandToExecute` polecenia rozszerzenie ustawia ten katalog (na przykład `...\Downloads\2` ) jako bieżący katalog roboczy. Ten proces umożliwia lokalizowanie plików pobranych za pośrednictwem właściwości przy użyciu ścieżek względnych `fileURIs` . Przykłady można znaleźć w poniższej tabeli.

Ze względu na to, że absolutna ścieżka pobierania może się różnić w miarę upływu czasu, lepiej jest wybrać względne ścieżki skryptów/plików w `commandToExecute` ciągu, jeśli jest to możliwe. Na przykład:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informacje o ścieżce po pierwszym segmencie identyfikatora URI są przechowywane dla plików pobranych za pośrednictwem `fileUris` listy właściwości.  Jak pokazano w poniższej tabeli, pobrane pliki są mapowane do podkatalogów pobierania, aby odzwierciedlały strukturę `fileUris` wartości.  

#### <a name="examples-of-downloaded-files"></a>Przykłady pobranych plików

| Identyfikator URI w fileUris | Pobrana lokalizacja względna | Lokalizacja pobrana bezwzględnie <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> bezwzględne ścieżki katalogów zmieniają się w okresie istnienia maszyny wirtualnej, ale nie w ramach pojedynczego wykonywania rozszerzenia CustomScript.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz również zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
