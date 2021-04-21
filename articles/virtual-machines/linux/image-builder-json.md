---
title: Tworzenie szablonu usługi Azure Image Builder (wersja zapoznawcza)
description: Dowiedz się, jak utworzyć szablon do użycia z usługą Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: 77460d1675b806e04c72e5f46da0ec4274d99d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762537"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Wersja zapoznawcza: tworzenie szablonu usługi Azure Image Builder 

Usługa Azure Image Builder używa pliku json do przekazania informacji do Image Builder usługi. W tym artykule przejmiemy sekcje pliku JSON, aby można było skompilować własny plik. Przykłady pełnych plików JSON można znaleźć w witrynie [Azure Image Builder GitHub.](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts)

Jest to podstawowy format szablonu:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ i wersja interfejsu API

To `type` typ zasobu, który musi mieć grupę `"Microsoft.VirtualMachineImages/imageTemplates"` . Funkcja `apiVersion` zmienia się wraz ze zmianami interfejsu API, ale powinna być w wersji `"2020-02-14"` zapoznawczej.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Lokalizacja

Lokalizacja to region, w którym zostanie utworzony obraz niestandardowy. W przypadku wersji Image Builder zapoznawczej obsługiwane są następujące regiony:

- East US
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- West Europe


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>plik vmProfile
Domyślnie Image Builder będzie używać maszyny wirtualnej kompilacji "Standard_D1_v2", można ją zastąpić, na przykład jeśli chcesz dostosować obraz dla maszyny wirtualnej z procesorem GPU, potrzebujesz rozmiaru maszyny wirtualnej procesora GPU. Jest to opcjonalne.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Domyślnie Image Builder nie zmieni rozmiaru obrazu, będzie on używać rozmiaru z obrazu źródłowego. Możesz **tylko zwiększyć** rozmiar dysku systemu operacyjnego (Win i Linux), jest to opcjonalne, a wartość 0 oznacza pozostawienie tego samego rozmiaru co obraz źródłowy. Nie można zmniejszyć rozmiaru dysku systemu operacyjnego do rozmiaru mniejszego niż rozmiar z obrazu źródłowego.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Jeśli nie określisz żadnych właściwości sieci wirtualnej, Image Builder utworzy własną sieć wirtualną, publiczny adres IP i sieciowej grupy sieci. Publiczny adres IP jest używany przez usługę do komunikowania się z maszyną wirtualną kompilacji, jeśli jednak nie chcesz, aby publiczny adres IP lub usługa Image Builder miał dostęp do istniejących zasobów sieci wirtualnej, takich jak serwery konfiguracji (DSC, Chef, Puppet, Ansible), udziały plików itp., możesz określić sieć wirtualną. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją sieci](image-builder-networking.md). Jest to opcjonalne.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Tagi

Są to pary klucz/wartość, które można określić dla wygenerowanego obrazu.

## <a name="depends-on-optional"></a>Zależy od (opcjonalnie)

Tej opcjonalnej sekcji można użyć, aby upewnić się, że zależności zostały ukończone przed podjęciem pracy. 

```json
    "dependsOn": [],
```

Aby uzyskać więcej informacji, [zobacz Define resource dependencies (Definiowanie zależności zasobów).](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)

## <a name="identity"></a>Tożsamość

Wymagane — aby Image Builder mieć uprawnienia do odczytu/zapisu obrazów, odczytu w skryptach z usługi Azure Storage, musisz utworzyć tożsamość usługi Azure User-Assigned Identity z uprawnieniami do poszczególnych zasobów. Aby uzyskać szczegółowe informacje na temat Image Builder i odpowiednich kroków, zapoznaj się z [dokumentacją](image-builder-user-assigned-identity.md).


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Image Builder obsługę tożsamości User-Assigned:
* Obsługuje tylko jedną tożsamość
* Nie obsługuje niestandardowych nazw domen

Aby dowiedzieć się więcej, [zobacz Co to są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md).
Aby uzyskać więcej informacji na temat wdrażania tej funkcji, zobacz Configure managed identities for Azure resources on an Azure VM using Azure CLI (Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy [Azure przy użyciu interfejsu wiersza polecenia platformy Azure).](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)

## <a name="properties-source"></a>Właściwości: źródło

Sekcja `source` zawiera informacje o obrazie źródłowym, który będzie używany przez Image Builder. Image Builder obecnie tylko natywnie obsługuje tworzenie obrazów generacji Hyper-V (Gen1) 1 do usługi Azure Shared Image Gallery (SIG) lub obrazu zarządzanego. Jeśli chcesz utworzyć obrazy gen2, musisz użyć obrazu źródłowego Gen2 i rozpowszechnić go do dysku VHD. Następnie należy utworzyć obraz zarządzany z wirtualnego dysku twardego i wstrzyknąć go do pliku SIG jako obraz gen2.

Interfejs API wymaga typu "SourceType" definiującego źródło kompilacji obrazu. Obecnie istnieją trzy typy:
- PlatformImage — wskazuje, że obraz źródłowy jest obrazem z witryny Marketplace.
- ManagedImage — użyj tej funkcji podczas rozpoczynania od zwykłego obrazu zarządzanego.
- SharedImageVersion — jest to używane w przypadku używania wersji obrazu w Shared Image Gallery jako źródła.


> [!NOTE]
> W przypadku korzystania z istniejących obrazów niestandardowych systemu Windows można uruchomić polecenie Sysprep maksymalnie 8 razy na pojedynczym obrazie systemu Windows. Aby uzyskać więcej informacji, zobacz dokumentację [narzędzia sysprep.](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)

### <a name="platformimage-source"></a>Źródło Obraz platformy 
Usługa Azure Image Builder obsługuje system Windows Server i klienta oraz obrazy systemu Linux Azure Marketplace, zobacz [tutaj,](../image-builder-overview.md#os-support) aby uzyskać pełną listę. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Poniższe właściwości są takie same, jak te używane do tworzenia maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Az, uruchom poniższe polecenia, aby pobrać właściwości: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Możesz użyć "najnowszej" w wersji , wersja jest oceniana podczas kompilowania obrazu, a nie podczas przesyłanego szablonu. Jeśli używasz tej funkcji z miejscem docelowym Shared Image Gallery, możesz uniknąć ponownego prześlij szablonu i ponownie uruchomić kompilację obrazu w odstępach czasu, aby obrazy zostały odtworzone na podstawie najnowszych obrazów.

#### <a name="support-for-market-place-plan-information"></a>Obsługa informacji o planie miejsca na rynku
Możesz również określić informacje o planie, na przykład:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>Źródło ManagedImage

Ustawia obraz źródłowy jako istniejący obraz zarządzany uogólniony wirtualnego dysku twardego lub maszyny wirtualnej.

> [!NOTE]
> Źródłowy obraz zarządzany musi mieć obsługiwany system operacyjny, a obraz musi mieć ten sam region co szablon usługi Azure Image Builder operacyjnego. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Powinien `imageId` to być resourceid obrazu zarządzanego. Użyj `az image list` , aby wyświetlić listę dostępnych obrazów.


### <a name="sharedimageversion-source"></a>Źródło SharedImageVersion
Ustawia obraz źródłowy istniejącej wersji obrazu w Shared Image Gallery.

> [!NOTE]
> Źródłowy obraz zarządzany musi mieć obsługiwany system operacyjny, a obraz musi mieć ten sam region co szablon usługi Azure Image Builder. Jeśli nie, zreplikuj wersję obrazu do regionu Image Builder Szablon.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Powinien `imageVersionId` to być resourceid wersji obrazu. Użyj [az sig image-version list,](/cli/azure/sig/image-version#az_sig_image_version_list) aby wyświetlić listę wersji obrazu.


## <a name="properties-buildtimeoutinminutes"></a>Właściwości: buildTimeoutInMinutes

Domyślnie aplikacja Image Builder przez 240 minut. Następnie limit czasu i zatrzymanie, niezależnie od tego, czy kompilacja obrazu jest ukończona. Po przecięeniu limitu czasu zostanie wyświetlony błąd podobny do tego:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Jeśli nie określisz wartości buildTimeoutInMinutes lub ustawisz ją na 0, zostanie ustawiona wartość domyślna. Wartość można zwiększyć lub zmniejszyć do maksymalnie 960 minut (16 godzin). W przypadku systemu Windows nie zalecamy ustawiania tego ustawienia na mniej niż 60 minut. Jeśli zostanie wyświetlony limit czasu, przejrzyj [](image-builder-troubleshoot.md#customization-log)dzienniki , aby sprawdzić, czy krok dostosowywania oczekuje na dane wejściowe użytkownika. 

Jeśli potrzebujesz więcej czasu na ukończenie dostosowań, ustaw je na to, co uważasz za potrzebne, przy niewielkim narzucie pracy. Nie należy jednak ustawiać go zbyt wysoko, ponieważ może być konieczne oczekiwanie na przeoczanie limitu czasu, zanim wystąpi błąd. 


## <a name="properties-customize"></a>Właściwości: dostosowywanie

Image Builder obsługuje wielu "customizers". Customizers to funkcje używane do dostosowywania obrazu, takie jak uruchamianie skryptów lub ponowne uruchamianie serwerów. 

W przypadku korzystania z `customize` programu : 
- Możesz użyć wielu elementów do dostosowywania, ale muszą one mieć unikatowy identyfikator `name` .
- Dostosowywanie jest wykonywane w kolejności określonej w szablonie.
- Jeśli jeden z customizer ulegnie awarii, cały składnik dostosowywania zakończy się niepowodzeniem i zwrócę błąd.
- Zdecydowanie zaleca się dokładne przetestowanie skryptu przed użyciem go w szablonie. Debugowanie skryptu na własnej maszynie wirtualnej będzie łatwiejsze.
- Nie umieszczaj poufnych danych w skryptach. 
- Lokalizacje skryptów muszą być publicznie dostępne, chyba że używasz [msi](./image-builder-user-assigned-identity.md).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
Sekcja customize jest tablicą. Usługa Azure Image Builder będzie uruchamiać dostosowywanie w kolejności sekwencyjnej. Wszelkie błędy w dowolnym skompilowanym programie spowoduje niepowodzenie procesu kompilacji. 

> [!NOTE]
> Polecenia wbudowane można wyświetlać w definicji szablonu obrazu i Pomoc techniczna Microsoft podczas pomocy w przypadku pomocy technicznej. Jeśli masz poufne informacje, powinny one zostać przeniesione do skryptów w usłudze Azure Storage, gdzie dostęp wymaga uwierzytelnienia.
 
### <a name="shell-customizer"></a>Dostosowywanie powłoki

Dostosowywanie powłoki obsługuje uruchamianie skryptów powłoki, które muszą być publicznie dostępne dla użytkownika IB, aby uzyskać do nich dostęp.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Obsługa systemu operacyjnego: Linux 
 
Dostosowywanie właściwości:

- **type** — powłoka 
- **name** — nazwa śledzenia dostosowania 
- **scriptUri** — URI do lokalizacji pliku 
- **inline** — tablica poleceń powłoki rozdzielonych przecinkami.
- **sha256Checksum** — wartość sumy kontrolnej sha256 pliku, należy wygenerować to lokalnie, a następnie Image Builder sumy kontrolnej i weryfikacji.
    * Aby wygenerować sha256Checksum, przy użyciu terminalu w systemie Mac/Linux uruchom: `sha256sum <fileName>`

> [!NOTE]
> Polecenia wbudowane są przechowywane jako część definicji szablonu obrazu. Są one widoczne podczas zrzutu definicji obrazu. Są one również widoczne dla usługi Pomoc techniczna Microsoft w przypadku wystąpienia problemu na potrzeby rozwiązywania problemów. Jeśli masz poufne polecenia lub wartości, zdecydowanie zaleca się, aby zostały one przeniesione do skryptów i używać tożsamości użytkownika do uwierzytelniania w usłudze Azure Storage.

#### <a name="super-user-privileges"></a>Uprawnienia administratora
Aby polecenia były uruchamiane z uprawnieniami administratora, muszą mieć prefiks , można dodać je do skryptów lub użyć poleceń w `sudo` tekście, na przykład:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Przykładowy skrypt korzystający z polecenia sudo, do których można się odwoływać przy użyciu wartości scriptUri:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Dostosowywanie ponownego uruchamiania systemu Windows 
Dostosowywanie Ponowne uruchamianie umożliwia ponowne uruchomienie maszyny wirtualnej z systemem Windows i oczekiwanie na jej powrót do trybu online. Pozwala to zainstalować oprogramowanie wymagające ponownego uruchomienia.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Obsługa systemu operacyjnego: Windows
 
Dostosowywanie właściwości:
- **Typ:** WindowsRestart
- **restartCommand** — polecenie do wykonania ponownego uruchomienia (opcjonalnie). Wartość domyślna to `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** — polecenie, aby sprawdzić, czy ponowne uruchomienie zakończyło się pomyślnie (opcjonalnie). 
- **restartTimeout —** limit czasu ponownego uruchomienia określony jako ciąg wielkości i jednostki. Na przykład `5m` (5 minut) lub `2h` (2 godziny). Wartość domyślna to: "5 m"

### <a name="linux-restart"></a>Ponowne uruchomienie systemu Linux  
Nie ma jednak narzędzia do dostosowywania ponownego uruchamiania systemu Linux, jednak jeśli instalujesz sterowniki lub składniki, które wymagają ponownego uruchomienia, możesz je zainstalować i wywołać ponownie przy użyciu customizera powłoki, istnieje 20-minutowy limit czasu SSH dla maszyny wirtualnej kompilacji.

### <a name="powershell-customizer"></a>Dostosowywanie programu PowerShell 
Dostosowywanie powłoki obsługuje uruchamianie skryptów programu PowerShell i polecenia wbudowanego, a skrypty muszą być publicznie dostępne dla interfejsu IB, aby uzyskać do nich dostęp.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Obsługa systemu operacyjnego: Windows i Linux

Dostosowywanie właściwości:

- **type** — PowerShell.
- **scriptUri** — URI do lokalizacji pliku skryptu programu PowerShell. 
- **inline** — polecenia wbudowane, które mają być uruchamiane, rozdzielone przecinkami.
- **validExitCodes** — opcjonalne prawidłowe kody, które mogą być zwracane z polecenia skryptu/wbudowanego, pozwoli to uniknąć zgłoszonego błędu skryptu/polecenia wbudowanego.
- **runElevated** — opcjonalna, logiczna, obsługa uruchamiania poleceń i skryptów z podwyższonym poziomem uprawnień.
- **sha256Checksum** — wartość sumy kontrolnej sha256 pliku, ta wartość jest generowana lokalnie, a następnie Image Builder sumy kontrolnej i weryfikacji.
    * Aby wygenerować sha256Checksum, przy użyciu programu PowerShell w systemie Windows [Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash)


### <a name="file-customizer"></a>Dostosowywanie pliku

Narzędzie do dostosowywania plików umożliwia konstruktorowi obrazów pobranie pliku z usługi GitHub lub usługi Azure Storage. Jeśli masz potok kompilacji obrazu, który opiera się na artefaktach kompilacji, możesz ustawić dostosowywanie plików do pobrania z udziału kompilacji i przenieść artefakty do obrazu.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Obsługa systemu operacyjnego: Linux i Windows 

Właściwości dostosowywanie pliku:

- **sourceUri** — dostępny punkt końcowy magazynu. Może to być usługa GitHub lub usługa Azure Storage. Możesz pobrać tylko jeden plik, a nie cały katalog. Jeśli musisz pobrać katalog, użyj skompresowanego pliku, a następnie dekompresuj go przy użyciu customizerów powłoki lub programu PowerShell. 

> [!NOTE]
> Jeśli identyfikator sourceUri jest kontem usługi Azure Storage, niezależnie od tego, czy obiekt blob jest oznaczony jako publiczny, należy udzielić tożsamości użytkownika zarządzanego uprawnień dostępu do odczytu obiektu blob. Zobacz ten [przykład,](./image-builder-user-assigned-identity.md#create-a-resource-group) aby ustawić uprawnienia magazynu.

- **destination** — jest to pełna ścieżka docelowa i nazwa pliku. Każda ścieżka i podkatalogi, do których istnieją odwołania, muszą istnieć. Aby je wcześniej skonfigurować, należy użyć customizerów powłoki lub programu PowerShell. Aby utworzyć ścieżkę, możesz użyć skrypatorów. 

Jest to obsługiwane przez katalogi systemu Windows i ścieżki systemu Linux, ale istnieją pewne różnice: 
- System operacyjny Linux — jedyną ścieżką, w jaką konstruktor obrazów może zapisywać, jest /tmp.
- Windows — brak ograniczeń ścieżki, ale ścieżka musi istnieć.
 
 
Jeśli wystąpi błąd podczas próby pobrania pliku lub umieścić go w określonym katalogu, krok dostosowywania nie powiedzie się i będzie się on pojawiał w pliku customization.log.

> [!NOTE]
> Dostosowywanie plików jest odpowiednie tylko w przypadku pobierania małych plików o rozmiarze < 20 MB. W przypadku większych plików do pobrania użyj skryptu lub polecenia wbudowanego, użyj kodu do pobrania plików, takich jak, Linux `wget` `curl` lub , Windows, `Invoke-WebRequest` .

### <a name="windows-update-customizer"></a>Windows Update Customizer
Ten customizer jest zbudowany na społeczności Windows Update Provisioner for [Packer,](https://packer.io/docs/provisioners/community-supported.html) która jest projektem open source przez społeczność usługi Packer. Firma Microsoft testuje i weryfikuje aprowizator za pomocą usługi Image Builder, będzie obsługiwać badanie związanych z nim problemów, a także będzie pracować nad rozwiązaniem problemów, jednak projekt open source nie jest oficjalnie obsługiwany przez firmę Microsoft. Aby uzyskać szczegółową dokumentację dotyczącą usługi Windows Update Provisioner, zobacz repozytorium projektu.

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Dostosowywanie właściwości:
- **type**  — WindowsUpdate.
- **searchCriteria** — opcjonalnie określa typ instalowanych aktualizacji (zalecane, ważne itp.), wartość BrowseOnly=0 i wartość IsInstalled=0 (zalecana) jest wartością domyślną.
- **filters** — opcjonalnie umożliwia określenie filtru w celu dołączania lub wykluczania aktualizacji.
- **updateLimit —** opcjonalnie określa, ile aktualizacji można zainstalować( wartość domyślna to 1000).
 
> [!NOTE]
> Działanie Windows Update może się nie powieść, jeśli istnieją jakieś zaległe ponowne uruchomienia systemu Windows lub instalacje aplikacji nadal działają. Zazwyczaj ten błąd może zostać wyświetlony w pliku `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` customization.log. Zdecydowanie zalecamy rozważenie dodania polecenia w trybie ponownego uruchomienia systemu [](/powershell/module/microsoft.powershell.utility/start-sleep) Windows i/lub umożliwienie aplikacjom wystarczająco dużo czasu na ukończenie instalacji przy użyciu poleceń uśpienia lub oczekiwania w poleceniach lub skryptach w tekście przed uruchomieniem Windows Update.

### <a name="generalize"></a>Generalize 
Domyślnie usługa Azure Image Builder również uruchamia kod "cofanie aprowizację" na końcu każdej fazy dostosowywania obrazu, aby "uogólnić" obraz. Uogólnienie to proces, w którym obraz jest ustawiany, aby można go było ponownie użyć do utworzenia wielu maszyn wirtualnych. W przypadku maszyn wirtualnych z systemem Windows usługa Azure Image Builder sysprep. W przypadku systemu Linux usługa Azure Image Builder uruchamia "waagent -deprovision". 

Polecenia, Image Builder użytkownikom uogólniać, mogą nie być odpowiednie w każdej sytuacji, dlatego usługa Azure Image Builder umożliwi dostosowanie tego polecenia w razie potrzeby. 

Jeśli migrujesz istniejące dostosowania i używasz różnych poleceń Sysprep/waagent, możesz użyć poleceń ogólnych Image Builder, a jeśli tworzenie maszyny wirtualnej zakończy się niepowodzeniem, użyj własnych poleceń Sysprep lub waagent.

Jeśli usługa Azure Image Builder pomyślnie utworzy niestandardowy obraz systemu Windows i utworzysz na jego podstawie maszynę wirtualną, a następnie przekonasz się, że tworzenie maszyny wirtualnej kończy się niepowodzeniem lub nie zakończy się pomyślnie, musisz przejrzeć dokumentację programu Sysprep systemu Windows Server lub poprosić o pomoc techniczną zespół pomocy technicznej sysprep systemu Windows Server, który może rozwiązywać problemy i doradzać prawidłowe użycie narzędzia Sysprep.


#### <a name="default-sysprep-command"></a>Domyślne polecenie sysprep
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Domyślne polecenie deprovision systemu Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Zastępowanie poleceń
Aby zastąpić polecenia, użyj aprowizatorów skryptów programu PowerShell lub powłoki, aby utworzyć pliki poleceń z dokładną nazwą pliku i umieścić je w odpowiednich katalogach:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder odczytywać te polecenia, są one zapisywane w dziennikach AIB "customization.log". Zobacz [rozwiązywanie problemów dotyczących](image-builder-troubleshoot.md#customization-log) zbierania dzienników.
 
## <a name="properties-distribute"></a>Właściwości: dystrybucja

Usługa Azure Image Builder obsługuje trzy cele dystrybucji: 

- **managedImage** — obraz zarządzany.
- **sharedImage** — Shared Image Gallery.
- **Dysk VHD** — wirtualny dysk twardy na koncie magazynu.

Obraz można dystrybuować do obu typów docelowych w tej samej konfiguracji.

> [!NOTE]
> Domyślne polecenie sysprep AIB nie zawiera wartości "/mode:vm", jednak może to być wymagane podczas tworzenia obrazów z zainstalowaną rolą HyperV. Jeśli musisz dodać ten argument polecenia, musisz zastąpić polecenie sysprep.

Ponieważ można dystrybuować do więcej niż jednego obiektu docelowego, program Image Builder zachowuje stan dla każdego docelowego obiektu docelowego dystrybucji, do którego można uzyskać dostęp przez odpytanie obiektu `runOutputName` .  Jest `runOutputName` obiektem, do których można wysłać zapytanie po dystrybucji, aby uzyskać informacje o tej dystrybucji. Można na przykład odpytować lokalizację dysku VHD lub regiony, w których została zreplikowana wersja obrazu, lub utworzyć wersję obrazu SIG. Jest to właściwość każdego obiektu docelowego dystrybucji. Element `runOutputName` musi być unikatowy dla każdego obiektu docelowego dystrybucji. Oto przykład: to jest zapytanie dotyczące Shared Image Gallery dystrybucji:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

Dane wyjściowe:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Dystrybuuj: managedImage

Dane wyjściowe obrazu będą zasobem obrazu zarządzanego.

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Dystrybuuj właściwości:
- **type** — managedImage 
- **imageId** — identyfikator zasobu obrazu docelowego, oczekiwany format: /subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **location** — lokalizacja obrazu zarządzanego.  
- **runOutputName** — unikatowa nazwa identyfikująca dystrybucję.  
- **artifactTags — opcjonalne** tagi pary wartości klucza określone przez użytkownika.
 
 
> [!NOTE]
> Docelowa grupa zasobów musi istnieć.
> Jeśli chcesz, aby obraz był dystrybuowany do innego regionu, spowoduje to zwiększenie czasu wdrożenia . 

### <a name="distribute-sharedimage"></a>Rozdystrybuuj: sharedImage 
Azure Shared Image Gallery to nowa usługa do zarządzania obrazami, która umożliwia zarządzanie replikacją regionów obrazów, wersją i udostępnianiem obrazów niestandardowych. Usługa Azure Image Builder obsługuje dystrybucję za pomocą tej usługi, dzięki czemu można dystrybuować obrazy do regionów obsługiwanych przez galerie obrazów udostępnionych. 
 
Na Shared Image Gallery składa się: 
 
- Galeria — kontener dla wielu udostępnionych obrazów. Galeria jest wdrażana w jednym regionie.
- Definicje obrazów — koncepcyjne grupowanie obrazów. 
- Wersje obrazów — jest to typ obrazu używany do wdrażania maszyny wirtualnej lub zestawu skalowania. Wersje obrazów można replikować do innych regionów, w których należy wdrożyć maszyny wirtualne.
 
Przed rozpoczęciem dystrybucji do galerii obrazów należy utworzyć galerię i definicję obrazu. Zobacz [Obrazy udostępnione.](../shared-images-cli.md) 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Dystrybuuj właściwości galerii obrazów udostępnionych:

- **type** — sharedImage  
- **galleryImageId** — identyfikator galerii obrazów udostępnionych, który można określić w dwóch formatach:
    * Automatyczne wersjonowanie — Image Builder wygeneruje monotoniczny numer wersji. Jest to przydatne w przypadku, gdy chcesz zachować ponowne kompilowanie obrazów z tego samego szablonu: format: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Jawna wersja — można przekazać numer wersji, którego ma używać konstruktor obrazów. Format jest: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** — unikatowa nazwa identyfikująca dystrybucję.  
- **artifactTags — opcjonalne** tagi pary wartości klucza określone przez użytkownika.
- **replicationRegions —** tablica regionów na replikację. Jednym z regionów musi być region, w którym wdrożono galerię. Dodawanie regionów oznacza zwiększenie czasu kompilacji, ponieważ kompilacja nie zostanie ukończona, dopóki replikacja nie zostanie zakończona.
- **excludeFromLatest** (opcjonalnie) Umożliwia to oznaczenie tworzyć wersji obrazu nie być używana jako najnowsza wersja w definicji SIG, wartość domyślna to "false".
- **StorageAccountType** (opcjonalnie) AIB obsługuje określanie następujących typów magazynu dla wersji obrazu, która ma zostać utworzona:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Jeśli szablon obrazu i odwołania nie znajdują się w tej samej lokalizacji, pojawi się dodatkowy czas `image definition` na utworzenie obrazów. Image Builder obecnie nie ma `location` parametru dla zasobu wersji obrazu, należy go uzyskać z jego nadrzędnego elementu `image definition` . Jeśli na przykład definicja obrazu znajduje się w stanie westus i chcesz, aby wersja obrazu była replikowana do regionu eastus, obiekt blob jest kopiowany do regionu westus, w tym przypadku tworzony jest zasób wersji obrazu w stanie westus, a następnie replikowany do regionu eastus. Aby uniknąć dodatkowego czasu replikacji, upewnij się, że szablon obrazu i `image definition` znajdują się w tej samej lokalizacji.


### <a name="distribute-vhd"></a>Rozdystrybuuj: wirtualny dysk twardy  
Dane wyjściowe można uzyskać na wirtualny dysk twardy. Następnie możesz skopiować wirtualny dysk twardy i użyć go do opublikowania w usłudze Azure MarketPlace lub użyć go z Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Obsługa systemu operacyjnego: Windows i Linux

Dystrybuuj parametry wirtualnego dysku twardego:

- **typ** — dysk VHD.
- **runOutputName** — unikatowa nazwa identyfikująca dystrybucję.  
- **tags** — opcjonalne tagi par klucz-wartość określone przez użytkownika.
 
Usługa Azure Image Builder nie zezwala użytkownikowi na określanie lokalizacji konta magazynu, ale można odpytować stan , `runOutputs` aby uzyskać lokalizację.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Po utworzeniu dysku VHD skopiuj go do innej lokalizacji, tak szybko, jak to możliwe. Wirtualny dysk twardy jest przechowywany na koncie magazynu w tymczasowej grupie zasobów utworzonej podczas przesłania szablonu obrazu do usługi Azure Image Builder Service. Usunięcie szablonu obrazu spowoduje utratę dysku VHD. 

## <a name="image-template-operations"></a>Operacje na szablonie obrazu

### <a name="starting-an-image-build"></a>Uruchamianie kompilacji obrazu
Aby rozpocząć kompilację, należy wywołać polecenie "Uruchom" dla zasobu szablon obrazu, przykłady `run` poleceń:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Anulowanie kompilacji obrazu
Jeśli uruchamiasz kompilację obrazu, która uważasz za nieprawidłową, oczekujesz na dane wejściowe użytkownika lub uważasz, że nigdy nie zakończy się pomyślnie, możesz anulować kompilację.

Kompilację można anulować w dowolnym momencie. Jeśli faza dystrybucji została rozpoczęta, nadal można anulować, ale konieczne będzie oczyszczenie wszystkich obrazów, które mogą nie zostać ukończone. Polecenie anulowania nie czeka na ukończenie anulowania. Monitoruj, aby `lastrunstatus.runstate` anulować postęp, używając tych poleceń [stanu.](image-builder-troubleshoot.md#customization-log)


Przykłady `cancel` poleceń:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Następne kroki

W usłudze Azure Image Builder GitHub znajdują się przykładowe pliki JSON dla różnych [scenariuszy.](https://github.com/azure/azvmimagebuilder)