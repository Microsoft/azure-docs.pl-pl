---
title: Rozszerzenie niestandardowego skryptu platformy Azure dla systemu Windows
description: Automatyzowanie zadań konfiguracji maszyny wirtualnej z systemem Windows przy użyciu rozszerzenia niestandardowego skryptu
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 08/31/2020
ms.openlocfilehash: 6341e3abbf591d0e6e0395e17ccf15ec73a3ac43
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835456"
---
# <a name="custom-script-extension-for-windows"></a>Rozszerzenie niestandardowego skryptu dla systemu Windows

Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań konfiguracji lub zarządzania. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia. Rozszerzenie niestandardowego skryptu integruje się z Azure Resource Manager szablonów i można je uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell Azure Portal lub interfejsu API REST maszyny wirtualnej platformy Azure.

W tym dokumencie szczegółowo opisano sposób używania rozszerzenia niestandardowego skryptu przy użyciu modułu Azure PowerShell, szablonów Azure Resource Manager oraz szczegółowo opisano kroki rozwiązywania problemów w systemach Windows.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]  
> Nie używaj rozszerzenia niestandardowego skryptu do uruchamiania Update-AzVM z tej samej maszyny wirtualnej co jej parametr, ponieważ będzie czekać na siebie.  

### <a name="operating-system"></a>System operacyjny

Rozszerzenie niestandardowego skryptu dla systemu Windows zostanie uruchomione w obsługiwanych systemach operacyjnych rozszerzeń;

### <a name="windows"></a>Windows

* Windows Server 2008 z dodatkiem R2
* Windows Server 2012
* Windows Server 2012 z dodatkiem R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Lokalizacja skryptu

Możesz skonfigurować rozszerzenie do używania poświadczeń usługi Azure Blob Storage w celu uzyskania dostępu do usługi Azure Blob Storage. Lokalizacja skryptu może być w dowolnym miejscu, o ile maszyna wirtualna może być przekierowyna do tego punktu końcowego, na przykład do usługi GitHub lub do wewnętrznego serwera plików.

### <a name="internet-connectivity"></a>Łączność z Internetem

Jeśli musisz pobrać skrypt zewnętrznie, na przykład z usługi GitHub lub Azure Storage, musisz otworzyć dodatkowe porty zapory i sieciowej grupy zabezpieczeń. Jeśli na przykład skrypt znajduje się w usłudze Azure Storage, możesz zezwolić na dostęp przy użyciu tagów usługi sieciowej platformy Azure dla [magazynu](../../virtual-network/network-security-groups-overview.md#service-tags).

Pamiętaj, że rozszerzenie CustomScript nie ma żadnego sposobu obejścia weryfikacji certyfikatu. Jeśli więc pobierasz z zabezpieczonej lokalizacji za pomocą np. certyfikat z podpisem własnym, może wystąpić błąd, taki jak "Certyfikat zdalny jest *nieprawidłowy zgodnie z procedurą weryfikacji".* Upewnij się, że certyfikat jest poprawnie zainstalowany w magazynie *"Zaufane główne urzędy certyfikacji"* na maszynie wirtualnej.

Jeśli skrypt znajduje się na serwerze lokalnym, nadal może być konieczne otwarcie dodatkowych portów zapory i sieciowej grupy zabezpieczeń.

### <a name="tips-and-tricks"></a>Porady i wskazówki

* Najwyższy odsetek błędów dla tego rozszerzenia wynika z błędów składniowych w skrypcie, przetestuj, czy skrypt działa bez błędu, a także umieść dodatkowe rejestrowanie w skrypcie, aby ułatwić znalezienie miejsca, w którym wystąpił błąd.
* Pisanie skryptów idempotentnych. Dzięki temu, jeśli zostaną one uruchomione ponownie przypadkowo, zmiany systemu nie zostaną wprowadzone.
* Upewnij się, że skrypty nie wymagają udziału użytkownika w trakcie działania.
* Dozwolony czas uruchamiania skryptu wynosi 90 minut — każdy dłuższy czas spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie umieszczaj operacji ponownego uruchamiania wewnątrz skryptu; ta akcja spowoduje problemy z innymi instalowanymi rozszerzeniami. Po ponownym uruchomieniu rozszerzenie nie będzie kontynuować pracy, gdy zostanie uruchomione jeszcze raz.
* Jeśli masz skrypt, który spowoduje ponowne uruchomienie, a następnie zainstaluje aplikacje i uruchomi skrypty, możesz zaplanować ponowne uruchomienie przy użyciu zaplanowanego zadania systemu Windows lub użyć narzędzi, takich jak rozszerzenia DSC, Chef lub Puppet.
* Nie zaleca się uruchamiania skryptu, który spowoduje zatrzymanie lub aktualizację agenta maszyny wirtualnej. Może to pozostawić rozszerzenie w stanie Transitioning (Przejście), co prowadzi do przeoczania limitu czasu.
* Rozszerzenie uruchomi skrypt tylko raz. Jeśli chcesz uruchomić skrypt przy każdym rozruchu, musisz użyć rozszerzenia w celu utworzenia zaplanowanego zadania systemu Windows.
* Jeśli chcesz zaplanować czas uruchomienia skryptu, użyj rozszerzenia w celu utworzenia zaplanowanego zadania systemu Windows.
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Jeśli potrzebujesz częstszych aktualizacji stanu działającego skryptu, musisz utworzyć własne rozwiązanie.
* Rozszerzenie niestandardowego skryptu nie obsługuje natywnie serwerów proxy, ale można użyć narzędzia do transferu plików, które obsługuje serwery proxy w ramach skryptu, takiego jak *Invoke-WebRequest*
* Pamiętaj, że skrypt lub polecenia mogą polegać na lokalizacjach katalogów innych niż domyślne. Przygotuj logikę obsługującą taką sytuację.
* Rozszerzenie niestandardowego skryptu zostanie uruchomione na koncie LocalSystem
* Jeśli planujesz używać właściwości *storageAccountName* i *storageAccountKey,* te właściwości muszą być koloczone w *parametrze protectedSettings.*

## <a name="extension-schema"></a>Schemat rozszerzenia

Konfiguracja rozszerzenia niestandardowego skryptu określa lokalizację skryptu i polecenie do uruchomienia. Tę konfigurację można przechowywać w plikach konfiguracji, określać ją w wierszu polecenia lub określać w Azure Resource Manager szablonu.

Poufne dane można przechowywać w chronionej konfiguracji, która jest szyfrowana i odszyfrowywać tylko wewnątrz maszyny wirtualnej. Konfiguracja chroniona jest przydatna, gdy polecenie wykonywania zawiera wpisy tajne, takie jak hasło lub odwołanie do pliku sygnatury dostępu współdzielonego (SAS), które powinny być chronione.

Te elementy powinny być traktowane jako poufne dane i określone w konfiguracji ustawień chronionych rozszerzeniami. Chronione dane ustawień rozszerzenia maszyny wirtualnej platformy Azure są szyfrowane i odszyfrowywać tylko na docelowej maszynie wirtualnej.

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
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {},
            "fileUris": [
                "script location"
            ]
        }
    }
}
```

> [!NOTE]
> Właściwości managedIdentity **nie można** używać w połączeniu z właściwościami storageAccountName ani storageAccountKey

> [!NOTE]
> W pewnym momencie na maszynie wirtualnej można zainstalować tylko jedną wersję rozszerzenia, a dwukrotne określenie niestandardowego skryptu w tym samym szablonie Resource Manager dla tej samej maszyny wirtualnej nie powiedzie się.

> [!NOTE]
> Tego schematu można użyć wewnątrz zasobu VirtualMachine lub jako zasobu autonomicznego. Nazwa zasobu musi być w tym formacie "virtualMachineName/extensionName", jeśli to rozszerzenie jest używane jako zasób autonomiczny w szablonie usługi ARM.

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość /przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publisher | Microsoft.Compute | ciąg |
| typ | CustomScriptExtension | ciąg |
| typeHandlerVersion | 1.10 | int |
| fileUris (np.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| sygnatura czasowa (np.) | 123456789 | 32-bitowa liczba całkowita |
| commandToExecute (np.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | ciąg |
| storageAccountName (np.) | examplestorageacct | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | ciąg |
| managedIdentity (np.) | { } lub { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } lub { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | Obiekt JSON |

>[!NOTE]
>W nazwach tych właściwości jest wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazw, jak pokazano poniżej.

#### <a name="property-value-details"></a>Szczegóły wartości właściwości

* `commandToExecute`:**(wymagany**, ciąg) skrypt punktu wejścia do wykonania. Użyj tego pola zamiast tego, jeśli polecenie zawiera wpisy tajne, takie jak hasła, lub twoje fileUris są poufne.
* `fileUris`: (opcjonalna tablica ciągów) adresy URL plików do pobrania. Jeśli adresy URL są poufne (na przykład adresy URL zawierające klucze), to pole należy określić w obszarze protectedSettings
* `timestamp` (opcjonalna 32-bitowa liczba całkowita) użyj tego pola tylko do wyzwolenia ponownego uruchomienia skryptu przez zmianę wartości tego pola.  Dowolna wartość całkowita jest akceptowalna; Musi ona być inna niż poprzednia wartość.
* `storageAccountName`: (opcjonalnie ciąg) nazwa konta magazynu. Jeśli określisz poświadczenia magazynu, wszystkie `fileUris` muszą być adresami URL dla obiektów blob platformy Azure.
* `storageAccountKey`: (opcjonalnie ciąg) klucz dostępu konta magazynu
* `managedIdentity`: (opcjonalny obiekt JSON) tożsamość [zarządzana](../../active-directory/managed-identities-azure-resources/overview.md) do pobierania plików
  * `clientId`: (opcjonalnie ciąg) identyfikator klienta tożsamości zarządzanej
  * `objectId`: (opcjonalnie ciąg) identyfikator obiektu tożsamości zarządzanej

Następujące wartości można ustawić w ustawieniach publicznych lub chronionych. Rozszerzenie odrzuci dowolną konfigurację, w której poniższe wartości są ustawione zarówno w ustawieniach publicznych, jak i chronionych.

* `commandToExecute`
* `fileUris`

Korzystanie z ustawień publicznych może być przydatne do debugowania, ale zaleca się korzystanie z ustawień chronionych.

Ustawienia publiczne są wysyłane w czytelnym tekście do maszyny wirtualnej, na której zostanie wykonany skrypt.  Ustawienia chronione są szyfrowane przy użyciu klucza znanego tylko platformie Azure i maszynie wirtualnej. Ustawienia są zapisywane na maszynie wirtualnej podczas ich wysłania, czyli jeśli ustawienia zostały zaszyfrowane, są one zapisywane w postaci zaszyfrowanej na maszynie wirtualnej. Certyfikat używany do odszyfrowywania zaszyfrowanych wartości jest przechowywany na maszynie wirtualnej i używany do odszyfrowywania ustawień (w razie potrzeby) w czasie wykonywania.

####  <a name="property-managedidentity"></a>Właściwość: managedIdentity
> [!NOTE]
> Ta właściwość **musi być** określona tylko w ustawieniach chronionych.

Język CustomScript (wersja 1.10 [](../../active-directory/managed-identities-azure-resources/overview.md) lub nowsza) obsługuje tożsamość zarządzaną do pobierania plików z adresów URL podanych w ustawieniu "fileUris". Umożliwia ona językowi CustomScript dostęp do prywatnych obiektów blob lub kontenerów usługi Azure Storage bez konieczności przekazania przez użytkownika wpisów tajnych, takich jak tokeny SAS lub klucze konta magazynu.

Aby użyć tej funkcji, użytkownik musi dodać [](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) tożsamość przypisaną przez [system](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) lub przypisaną przez użytkownika do maszyny wirtualnej lub zestawu vmSS, na której ma zostać uruchomiony skrypt CustomScript, oraz udzielić tożsamości zarządzanej dostępu do kontenera lub obiektu blob usługi [Azure Storage.](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access)

Aby użyć tożsamości przypisanej przez system na docelowej maszynie wirtualnej/zestawie usług VMSS, ustaw pole "managedidentity" na pusty obiekt JSON. 

> Przykład:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Aby użyć tożsamości przypisanej przez użytkownika na docelowej maszynie wirtualnej/w usługach VMSS, skonfiguruj pole "managedidentity" z identyfikatorem klienta lub identyfikatorem obiektu tożsamości zarządzanej.

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
> Właściwości managedIdentity **nie można** używać w połączeniu z właściwościami storageAccountName ani storageAccountKey

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać za pomocą Azure Resource Manager szablonów. Schemat JSON, który został szczegółowo szczegółowo opisane w poprzedniej sekcji, może być używany w szablonie Azure Resource Manager do uruchamiania rozszerzenia niestandardowego skryptu podczas wdrażania. Następujące przykłady pokazują, jak używać rozszerzenia niestandardowego skryptu:

* [Samouczek: wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Wdrażanie aplikacji dwuwarstwowej w systemie Windows i Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell

Polecenie `Set-AzVMCustomScriptExtension` może służyć do dodawania rozszerzenia niestandardowego skryptu do istniejącej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Więcej przykładów

### <a name="using-multiple-scripts"></a>Używanie wielu skryptów

W tym przykładzie masz trzy skrypty używane do kompilowania serwera. Polecenie **CommandToExecute** wywołuje pierwszy skrypt, a następnie dostępne są opcje dotyczące sposobu wywoływania innych. Na przykład możesz mieć skrypt główny, który kontroluje wykonywanie, z odpowiednią obsługą błędów, rejestrowaniem i zarządzaniem stanem. Skrypty są pobierane na komputer lokalny w celu uruchomienia. Na przykład w programie można wywołać , dodając do skryptu , i powtórzyć ten proces dla `1_Add_Tools.ps1` `2_Add_Features.ps1` innych `.\2_Add_Features.ps1` skryptów, które zdefiniowano w . `$settings`

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

W tym przykładzie można użyć lokalnego serwera SMB dla lokalizacji skryptu. Dzięki temu nie trzeba po prostu po prostu podać żadnych innych ustawień, z wyjątkiem **polecenia commandToExecute.**

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

Jeśli chcesz uruchomić rozszerzenie niestandardowego skryptu więcej niż raz, możesz wykonać tę akcję tylko w tych warunkach:

* Nazwa **rozszerzenia** parametr jest taka sama jak poprzednie wdrożenie rozszerzenia.
* Zaktualizuj konfigurację. W przeciwnym razie polecenie nie zostanie ponownie wykonane. Do polecenia można dodać właściwość dynamiczną, taką jak sygnatura czasowa.

Alternatywnie można ustawić właściwość [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) na **wartość true**.

### <a name="using-invoke-webrequest"></a>Korzystanie z Invoke-WebRequest

Jeśli używasz polecenia [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) w skrypcie, musisz określić parametr . W innym przypadku podczas sprawdzania stanu szczegółowego zostanie wyświetlony `-UseBasicParsing` następujący błąd:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Aby wdrożyć rozszerzenie niestandardowego skryptu w zestawie skalowania, zobacz [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)

## <a name="classic-vms"></a>Klasyczne maszyny wirtualne

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Aby wdrożyć rozszerzenie niestandardowego skryptu na klasycznych maszyn wirtualnych, można użyć Azure Portal lub klasycznych Azure PowerShell cmdlet.

### <a name="azure-portal"></a>Azure Portal

Przejdź do zasobu klasycznej maszyny wirtualnej. Wybierz **pozycję Rozszerzenia w** obszarze **Ustawienia.**

Kliknij **pozycję + Dodaj** i na liście zasobów wybierz pozycję Rozszerzenie **niestandardowego skryptu.**

Na stronie **Instalowanie rozszerzenia** wybierz lokalny plik programu PowerShell, wypełnij wszelkie argumenty i kliknij przycisk **OK.**

### <a name="powershell"></a>PowerShell

Polecenie cmdlet [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) może służyć do dodawania rozszerzenia niestandardowego skryptu do istniejącej maszyny wirtualnej.

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

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu modułu Azure PowerShell rozszerzenia. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny wirtualnej, uruchom następujące polecenie:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Dane wyjściowe rozszerzenia są rejestrowane w plikach znalezionych w następującym folderze na docelowej maszynie wirtualnej.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Określone pliki są pobierane do następującego folderu na docelowej maszynie wirtualnej.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

gdzie `<n>` jest liczbą całkowitą dziesiętną, która może zmieniać się między wykonaniami rozszerzenia.  Wartość `1.*` odpowiada rzeczywistej, `typeHandlerVersion` bieżącej wartości rozszerzenia.  Na przykład rzeczywistym katalogiem może być `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

Podczas wykonywania polecenia rozszerzenie ustawia ten katalog (na przykład `commandToExecute` ) jako bieżący katalog `...\Downloads\2` roboczy. Ten proces umożliwia użycie ścieżek względnych do lokalizowania plików pobranych za pośrednictwem `fileURIs` właściwości . Przykłady można znaleźć w poniższej tabeli.

Ponieważ bezwzględna ścieżka pobierania może się zmieniać w czasie, lepiej wybrać względne ścieżki skryptów/plików w ciągu, jeśli `commandToExecute` jest to możliwe. Na przykład:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informacje o ścieżce po pierwszym segmencie URI są przechowywane dla plików pobranych za `fileUris` pośrednictwem listy właściwości.  Jak pokazano w poniższej tabeli, pobrane pliki są mapowane na podkatalogi pobierania w celu odzwierciedlenia struktury `fileUris` wartości.  

#### <a name="examples-of-downloaded-files"></a>Przykłady pobranych plików

| URI w fileUris | Względna lokalizacja pobrana | Bezwzględna lokalizacja pobrana <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Ścieżki katalogów bezwzględnych zmieniają się w okresie istnienia maszyny wirtualnej, ale nie w ramach pojedynczego wykonania rozszerzenia CustomScript.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie w tym artykule, możesz skontaktować się z ekspertami w dziedzinie platformy Azure na platformie Azure w witrynie [MSDN i Stack Overflow forach.](https://azure.microsoft.com/support/forums/) Możesz również utworzyć zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny pomocy [technicznej platformy Azure i](https://azure.microsoft.com/support/options/) wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomoc techniczna platformy Azure, przeczytaj [często zadawane pytania dotyczące Microsoft Azure pomocy technicznej.](https://azure.microsoft.com/support/faq/)
