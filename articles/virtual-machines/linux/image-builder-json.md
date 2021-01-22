---
title: Tworzenie szablonu programu Azure Image Builder (wersja zapoznawcza)
description: Dowiedz się, jak utworzyć szablon do użycia z programem Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 08/13/2020
ms.topic: reference
ms.service: virtual-machines
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: 9ae477dd04237e285915157615dcb6a6b841ca99
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678259"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Wersja zapoznawcza: Tworzenie szablonu usługi Azure Image Builder 

Usługa Azure Image Builder używa pliku JSON do przekazywania informacji do usługi Image Builder. W tym artykule przejdziemy do sekcji pliku JSON, aby można było utworzyć własne. Aby zapoznać się z przykładami pełnych plików JSON, zobacz [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

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

`type`Jest typem zasobu, który musi być `"Microsoft.VirtualMachineImages/imageTemplates"` . Zmieni się w miarę upływu `apiVersion` czasu, gdy interfejs API ulegnie zmianie, ale powinien być `"2020-02-14"` w wersji zapoznawczej.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Lokalizacja

Lokalizacja to region, w którym zostanie utworzony obraz niestandardowy. W przypadku wersji zapoznawczej programu Image Builder obsługiwane są następujące regiony:

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
## <a name="vmprofile"></a>vmProfile
Domyślnie program Image Builder użyje maszyny wirtualnej kompilacji "Standard_D1_v2". można to zastąpić, na przykład jeśli chcesz dostosować obraz dla maszyny wirtualnej procesora GPU, potrzebujesz rozmiaru maszyny wirtualnej procesora GPU. Jest to opcjonalne.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Domyślnie Konstruktor obrazów nie zmieni rozmiaru obrazu, będzie używał rozmiaru z obrazu źródłowego. Można zwiększyć **tylko** rozmiar dysku systemu operacyjnego (win i Linux), to jest opcjonalne, a wartość 0 oznacza pozostawienie tego samego rozmiaru co obraz źródłowy. Nie można zmniejszyć rozmiaru dysku systemu operacyjnego do mniejszego niż rozmiar z obrazu źródłowego.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Jeśli nie określisz żadnych właściwości sieci wirtualnej, Konstruktor obrazów utworzy własną sieć wirtualną, publiczny adres IP i sieciowej grupy zabezpieczeń. Publiczny adres IP jest używany przez usługę do komunikacji z maszyną wirtualną kompilacji, ale jeśli nie chcesz, aby publiczny adres IP lub Konstruktor obrazów miał dostęp do istniejących zasobów sieci wirtualnej, takich jak serwery konfiguracji (DSC, Chef, Puppet, rozwiązania ansible), udziały plików itp., możesz określić sieć wirtualną. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją sieciową](image-builder-networking.md), która jest opcjonalna.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Tagi

Są to pary klucz/wartość, które można określić dla generowanego obrazu.

## <a name="depends-on-optional"></a>Zależy od (opcjonalnie)

Tej opcjonalnej sekcji można użyć, aby upewnić się, że zależności zostały ukończone przed kontynuowaniem. 

```json
    "dependsOn": [],
```

Aby uzyskać więcej informacji, zobacz [Definiowanie zależności zasobów](../../azure-resource-manager/templates/define-resource-dependency.md#dependson).

## <a name="identity"></a>Tożsamość

Wymagane — aby program Image Builder miał uprawnienia do odczytu/zapisu obrazów, przeczytaj skrypty z usługi Azure Storage, musisz utworzyć tożsamość usługi Azure User-Assigned, która ma uprawnienia do poszczególnych zasobów. Aby uzyskać szczegółowe informacje na temat sposobu działania uprawnień konstruktora obrazów i odpowiednich czynności, zapoznaj się z [dokumentacją](image-builder-user-assigned-identity.md).


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Obsługa programu Image Builder dla tożsamości User-Assigned:
* Obsługuje tylko jedną tożsamość
* Nie obsługuje niestandardowych nazw domen

Aby dowiedzieć się więcej, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md).
Aby uzyskać więcej informacji na temat wdrażania tej funkcji, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Właściwości: Źródło

`source`Sekcja zawiera informacje o obrazie źródłowym, który będzie używany przez Konstruktor obrazów. Konstruktor obrazów obecnie obsługuje tylko natywnie tworzenie obrazów generacji funkcji Hyper-V (Gen1) 1 w galerii obrazów udostępnionych (SIG) systemu Azure lub w obrazie zarządzanym. Jeśli chcesz utworzyć obrazy Gen2, musisz użyć źródłowego obrazu Gen2 i rozesłać do dysku VHD. Następnie należy utworzyć obraz zarządzany na podstawie wirtualnego dysku twardego i wprowadzić go do SIG jako obraz Gen2.

Interfejs API wymaga elementu "SourceType", który definiuje Źródło dla kompilacji obrazu, obecnie istnieją trzy typy:
- PlatformImage — wskazuje, że obraz źródłowy jest obrazem portalu Marketplace.
- ManagedImage — Użyj tego w przypadku uruchamiania z regularnego zarządzanego obrazu.
- SharedImageVersion — ta wartość jest używana w przypadku używania wersji obrazu w galerii obrazów udostępnionych jako źródło.


> [!NOTE]
> W przypadku korzystania z istniejących obrazów niestandardowych systemu Windows można uruchomić polecenie Sysprep do 8 razy w pojedynczym obrazie systemu Windows, aby uzyskać więcej informacji, zobacz dokumentację programu [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) .

### <a name="platformimage-source"></a>Źródło PlatformImage 
Usługa Azure Image Builder obsługuje obrazy systemu Windows Server i klienta, a w systemie Linux Azure Marketplace — zobacz [tutaj](../image-builder-overview.md#os-support) , aby zapoznać się z pełną listą. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Właściwości w tym miejscu są takie same, jak w przypadku tworzenia maszyn wirtualnych za pomocą polecenia AZ CLI, uruchom poniższe polecenie, aby uzyskać właściwości: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

W wersji można użyć "najnowszej" wersji, a wersja jest oceniana, gdy kompilacja obrazu jest wykonywana, a nie podczas przesyłania szablonu. W przypadku korzystania z tej funkcji z miejscem docelowym galerii obrazów udostępnionych można uniknąć ponownego przesyłania szablonu i ponownie uruchamiać kompilację obrazu w odstępach czasu, dzięki czemu obrazy są odtwarzane z najnowszych obrazów.

#### <a name="support-for-market-place-plan-information"></a>Pomoc techniczna dotycząca planu miejsca na rynku
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

Ustawia obraz źródłowy jako istniejący obraz zarządzany uogólnionego wirtualnego dysku twardego lub maszyny wirtualnej.

> [!NOTE]
> Źródłowy obraz zarządzany musi być w obsługiwanym systemie operacyjnym, a obraz musi być w tym samym regionie co szablon programu Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`Powinien być identyfikator ResourceID obrazu zarządzanego. Użyj `az image list` , aby wyświetlić listę dostępnych obrazów.


### <a name="sharedimageversion-source"></a>Źródło SharedImageVersion
Ustawia obraz źródłowy jako istniejącą wersję obrazu w galerii obrazów udostępnionych.

> [!NOTE]
> Obraz zarządzany przez źródło musi być obsługiwanym systemem operacyjnym, a obraz musi być w tym samym regionie co szablon usługi Azure Image Builder, a jeśli nie, należy replikować wersję obrazu do regionu szablonu programu Image Builder.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`Powinien być identyfikator ResourceID wersji obrazu. Użyj [AZ SIG Image-Version list](/cli/azure/sig/image-version#az-sig-image-version-list) , aby wyświetlić listę wersji obrazu.


## <a name="properties-buildtimeoutinminutes"></a>Właściwości: buildTimeoutInMinutes

Domyślnie Konstruktor obrazów będzie uruchamiany przez 240 minut. Po tym czasie zostanie przekroczony limit czasu i zostanie zatrzymany, niezależnie od tego, czy kompilacja obrazu została ukończona. Jeśli zostanie osiągnięty limit czasu, zobaczysz błąd podobny do tego:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Jeśli nie określisz wartości buildTimeoutInMinutes lub ustawisz ją na 0, spowoduje to użycie wartości domyślnej. Można zwiększyć lub zmniejszyć wartość, maksymalnie 960mins (16hrs). W przypadku systemu Windows nie zalecamy ustawienia tej opcji poniżej 60 minut. Jeśli okaże się, że upłynie limit czasu, przejrzyj [dzienniki](image-builder-troubleshoot.md#customization-log), aby sprawdzić, czy krok dostosowania oczekuje na dane wejściowe użytkownika. 

Jeśli okaże się, że potrzebujesz więcej czasu na ukończenie dostosowanych dostosowań, ustaw na to, czego potrzebujesz, przy niewielkim obciążeniu. Ale nie ustawiaj go zbyt wysokie, ponieważ może być konieczne poczekanie na przekroczenie limitu czasu przed wyświetleniem błędu. 


## <a name="properties-customize"></a>Właściwości: Dostosowywanie

Konstruktor obrazów obsługuje wiele "konfiguratorów". Dostosowania są funkcjami używanymi do dostosowywania obrazu, takich jak uruchamianie skryptów lub ponowne uruchamianie serwerów. 

W przypadku korzystania z `customize` : 
- Można użyć wielu dostosowań, ale muszą one mieć unikatowy `name` .
- Dostosowania są wykonywane w kolejności określonej w szablonie.
- Jeśli jeden z ustawień nie powiedzie się, cały składnik dostosowywania zakończy się niepowodzeniem, a raport zostanie zwrócony z powrotem.
- Zdecydowanie zaleca się dokładne przetestowanie skryptu przed użyciem go w szablonie. Debugowanie skryptu na własnej maszynie wirtualnej będzie łatwiejsze.
- Nie należy umieszczać poufnych danych w skryptach. 
- Lokalizacje skryptów muszą być publicznie dostępne, chyba że używany jest plik [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

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

 
Sekcja Dostosowywanie jest tablicą. Konstruktor obrazów platformy Azure będzie uruchamiany przez konfiguratorów w kolejności sekwencyjnej. Dowolna awaria w dowolnym obszarze dostosowywania zakończy się niepowodzeniem procesu kompilacji. 

> [!NOTE]
> Polecenia wbudowane można wyświetlać w definicji szablonu obrazu i według pomoc techniczna firmy Microsoft, w przypadku pomocy technicznej. Informacje poufne należy przenieść do skryptów w usłudze Azure Storage, gdzie dostęp wymaga uwierzytelnienia.
 
### <a name="shell-customizer"></a>Konfigurator powłoki

Konfigurator powłoki obsługuje uruchamianie skryptów powłoki, muszą być publicznie dostępne dla IB w celu uzyskania dostępu do nich.

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
 
Dostosuj właściwości:

- **Typ** — Shell 
- **Nazwa — nazwa** do śledzenia dostosowania 
- **scriptUri** -URI do lokalizacji pliku 
- **wbudowana** tablica poleceń powłoki, rozdzielonych przecinkami.
- **sha256Checksum** — wartość sumy kontrolnej SHA256 pliku, wygenerowana lokalnie, a następnie Konstruktor obrazów będzie obliczać sumę kontrolną i sprawdzać poprawność.
    * Aby wygenerować sha256Checksum, użyj terminalu w systemie Mac/Linux: `sha256sum <fileName>`


Aby polecenia były uruchamiane z uprawnieniami administratora, muszą być poprzedzone prefiksem `sudo` .

> [!NOTE]
> Polecenia wbudowane są przechowywane jako część definicji szablonu obrazu, ale można je wyświetlić w przypadku zrzutu definicji obrazu. są one również widoczne do pomoc techniczna firmy Microsoft w przypadku rozwiązywania problemów z pomocą techniczną. Jeśli masz poufne polecenia lub wartości, zdecydowanie zalecamy, aby były one przenoszone do skryptów, a uwierzytelnianie w usłudze Azure Storage odbywa się przy użyciu tożsamości użytkownika.

### <a name="windows-restart-customizer"></a>Dostosowywanie ponownego uruchomienia systemu Windows 
Dostosowywanie ponownego uruchomienia umożliwia ponowne uruchomienie maszyny wirtualnej z systemem Windows i poczekanie, aż powróci do trybu online. pozwala to na zainstalowanie oprogramowania wymagającego ponownego uruchomienia.  

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
 
Dostosuj właściwości:
- **Typ**: WindowsRestart
- **restartCommand** — polecenie, aby wykonać ponowne uruchomienie (opcjonalnie). Wartość domyślna to `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** — polecenie, aby sprawdzić, czy ponowne uruchomienie zakończyło się pomyślnie (opcjonalnie). 
- **restartTimeout** — limit czasu ponownego uruchomienia określony jako ciąg wielkości i jednostki. Na przykład `5m` (5 minut) lub `2h` (2 godziny). Wartość domyślna to: "5 m"

### <a name="linux-restart"></a>Ponowne uruchamianie systemu Linux  
Jeśli instalujesz sterowniki lub składniki wymagające ponownego uruchomienia, można je zainstalować i wywoływać ponowne uruchomienie przy użyciu konfiguratora powłoki, a na maszynie wirtualnej kompilacji jest przekroczenie limitu czasu 20min SSH.

### <a name="powershell-customizer"></a>Dostosowywanie programu PowerShell 
Konfigurator powłoki obsługuje uruchamianie skryptów programu PowerShell i polecenia wbudowanego. skrypty muszą być dostępne publicznie, aby można było uzyskać do nich dostęp.

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

Dostosuj właściwości:

- **Type** — PowerShell.
- **scriptUri** -URI do lokalizacji pliku skryptu programu PowerShell. 
- **wbudowane** — polecenia śródwierszowe do uruchomienia, oddzielone przecinkami.
- **validExitCodes** — opcjonalne, prawidłowe kody, które mogą być zwracane z skryptu/polecenia wbudowanego, pozwoli to uniknąć zgłaszanego błędu skryptu/polecenia wbudowanego.
- **runElevated** — opcjonalne, wartość logiczna, obsługa uruchamiania poleceń i skryptów z podniesionymi uprawnieniami.
- **sha256Checksum** — wartość sumy kontrolnej SHA256 pliku, wygenerowana lokalnie, a następnie Konstruktor obrazów będzie obliczać sumę kontrolną i sprawdzać poprawność.
    * Aby wygenerować sha256Checksum przy użyciu programu PowerShell w systemie Windows [Get-hash](/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Konfigurator plików

Program do dostosowywania plików umożliwia programowi Image Builder pobranie pliku z witryny GitHub lub Azure Storage. Jeśli masz potok kompilacji obrazu, który opiera się na artefaktach kompilacji, możesz ustawić konfiguratora plików na pobieranie z udziału kompilacji i przenieść artefakty do obrazu.  

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

Właściwości dostosowywania pliku:

- **SourceUri** — dostępny punkt końcowy magazynu, może to być usługa GitHub lub Azure Storage. Można pobrać tylko jeden plik, a nie cały katalog. Jeśli konieczne jest pobranie katalogu, należy użyć skompresowanego pliku, a następnie zdekompresować go przy użyciu powłoki lub konfiguratorów programu PowerShell. 
- **Destination** — jest to pełna ścieżka docelowa i nazwa pliku. Wszystkie ścieżki i podkatalogi, do których się odwołuje, muszą istnieć, użyj programu Shell lub konfiguratorzy programu PowerShell, aby wcześniej je skonfigurować. Możesz użyć niestandardowych konfiguratorów skryptów, aby utworzyć ścieżkę. 

Jest to obsługiwane przez katalogi systemu Windows i ścieżki Linux, ale istnieją pewne różnice: 
- System operacyjny Linux — jedyną ścieżką konstruktora obrazu można napisać w/tmp.
- Windows — brak ograniczenia ścieżki, ale ścieżka musi istnieć.
 
 
Jeśli wystąpi błąd podczas próby pobrania pliku lub umieszczenia go w określonym katalogu, krok dostosowywania zakończy się niepowodzeniem i będzie on znajdować się w pliku customization. log.

> [!NOTE]
> Program do dostosowywania plików jest odpowiedni dla małych plików do pobrania, < baza. W przypadku większych pobrań plików Użyj skryptu lub polecenia wbudowanego, a następnie użyj kodu do pobrania plików, takich jak Linux `wget` lub `curl` Windows, `Invoke-WebRequest` .

Pliki w obszarze dostosowywania plików można pobrać z usługi Azure Storage przy użyciu pliku [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Windows Update konfiguratora
Ten element dostosowujący jest oparty na [społeczności Windows Update aprowizacji](https://packer.io/docs/provisioners/community-supported.html) dla programu Packer, czyli projektu Open Source obsługiwanego przez społeczność programu Packer. Firma Microsoft testuje i sprawdza poprawność aprowizacji za pomocą usługi Image Builder i będzie obsługiwać problemy związane z badaniem oraz pozwala na rozwiązywanie problemów, ale projekt open source nie jest oficjalnie obsługiwany przez firmę Microsoft. Aby zapoznać się ze szczegółową dokumentacją i uzyskać pomoc dotyczącą Windows Update aprowizacji, zobacz repozytorium projektu.

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

Dostosuj właściwości:
- **Typ**  — windowsupdate.
- **oczekiwano elementu SearchCriteria** — opcjonalne, określa, który typ aktualizacji jest instalowany (zalecane, ważne itp.), BrowseOnly = 0 i IsInstalled = 0 (zalecane) jest wartością domyślną.
- **filtry** — opcjonalne, umożliwia określenie filtru, aby uwzględnić lub wykluczyć aktualizacje.
- **updateLimit** — opcjonalne, definiuje liczbę aktualizacji, które mogą być instalowane, domyślnie 1000.
 
> [!NOTE]
> Windows Update konfigurator może zakończyć się niepowodzeniem, jeśli istnieją jakiekolwiek zaległe ponowne uruchomienia systemu Windows lub instalacje aplikacji nadal działają, zazwyczaj ten błąd może pojawić się w temacie Customization. log, `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . Zdecydowanie odradzamy Dodawanie w systemie Windows ponownego uruchamiania i/lub zezwalanie aplikacjom na ukończenie instalacji przy użyciu funkcji [uśpienia] lub oczekiwanie poleceń ( https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/start-sleep?view=powershell-7) w poleceniach wbudowanych lub w skryptach przed uruchomieniem Windows Update.

### <a name="generalize"></a>Generalize 
Domyślnie program Azure Image Builder uruchomi również kod "anulowania aprowizacji" na końcu każdej fazy dostosowywania obrazu do "generalize" obrazu. Uogólnianie to proces, w którym obraz jest skonfigurowany tak, aby można go było ponownie wykorzystać do tworzenia wielu maszyn wirtualnych. W przypadku maszyn wirtualnych z systemem Windows usługa Azure Image Builder używa programu Sysprep. W przypadku systemu Linux usługa Azure Image Builder uruchamia polecenie "waagent-dezastrzeganie". 

Polecenia konstruktora obrazu, które użytkownicy mogą uogólniać, nie są odpowiednie dla każdej sytuacji, więc usługa Azure Image Builder umożliwi dostosowanie tego polecenia w razie potrzeby. 

W przypadku migrowania istniejącego dostosowania i używania innych poleceń Sysprep/waagent można użyć poleceń ogólnych konstruktora obrazu, a jeśli Tworzenie maszyny wirtualnej nie powiedzie się, użyj własnych poleceń Sysprep lub waagent.

Jeśli usługa Azure Image Builder pomyślnie utworzy obraz niestandardowy systemu Windows, a następnie utworzysz dla niego maszynę wirtualną, sprawdź, czy Tworzenie maszyny wirtualnej nie powiedzie się lub nie zakończy się pomyślnie, należy zapoznać się z dokumentacją programu Sysprep dla systemu Windows Server lub zgłosić żądanie pomocy technicznej z zespołem pomocy technicznej dla systemu Windows Server, który może rozwiązywać problemy i polecić poprawne użycie programu Sysprep.


#### <a name="default-sysprep-command"></a>Domyślne polecenie programu Sysprep
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
#### <a name="default-linux-deprovision-command"></a>Domyślne polecenie anulowania aprowizacji systemu Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Zastępowanie poleceń
Aby zastąpić polecenia, należy użyć programu PowerShell lub obsługi skryptów powłoki w celu utworzenia plików poleceń z dokładną nazwą pliku i umieścić je w prawidłowych katalogach:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

Konstruktor obrazów odczyta te polecenia, są one zapisywane do dzienników AIB "Customization. log". Zobacz temat [Rozwiązywanie problemów](image-builder-troubleshoot.md#customization-log) dotyczących zbierania dzienników.
 
## <a name="properties-distribute"></a>Właściwości: Dystrybuuj

Konstruktor obrazów platformy Azure obsługuje trzy cele dystrybucji: 

- obraz zarządzany przez **managedImage** .
- **sharedImage** — Galeria obrazów udostępnionych.
- **Wirtualny dysk twardy** (VHD) na koncie magazynu.

Można dystrybuować obraz do obu typów docelowych w tej samej konfiguracji.

> [!NOTE]
> Domyślne polecenie narzędzia Sysprep AIB nie zawiera "/Mode: VM", jednak może być wymagane w przypadku tworzenia obrazów, które mają zainstalowaną rolę funkcji Hyper-v. Jeśli musisz dodać ten argument polecenia, musisz zastąpić polecenie Sysprep.

Ponieważ możesz mieć więcej niż jeden element docelowy do dystrybucji, Konstruktor obrazów zachowuje stan dla każdego elementu docelowego dystrybucji, do którego można uzyskać dostęp za pomocą zapytania `runOutputName` .  `runOutputName`Jest obiektem, który można wysłać zapytanie o dystrybucję w celu uzyskania informacji o tej dystrybucji. Można na przykład zbadać lokalizację dysku VHD lub regiony, w których została zreplikowana wersja obrazu, lub utworzyć wersję obrazu SIG. Jest to właściwość każdego celu dystrybucji. `runOutputName`Musi być unikatowa dla każdego celu dystrybucji. Oto przykład, który wykonuje zapytanie do dystrybucji galerii obrazów udostępnionych:

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

Wyjście obrazu będzie zasobem obrazu zarządzanego.

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
- **Typ** — managedImage 
- **imageId** — identyfikator zasobu obrazu docelowego, oczekiwany format:/subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.COMPUTE/images/\<imageName>
- **Lokalizacja** lokalizacji zarządzanego obrazu.  
- **runOutputName** — unikatowa nazwa identyfikująca dystrybucję.  
- **artifactTags** — opcjonalne Tagi par wartości klucza określonego przez użytkownika.
 
 
> [!NOTE]
> Docelowa Grupa zasobów musi istnieć.
> Jeśli chcesz, aby obraz został rozdystrybuowany do innego regionu, wydłuża to czas wdrażania. 

### <a name="distribute-sharedimage"></a>Dystrybuuj: sharedImage 
Galeria udostępnionych obrazów systemu Azure to nowa usługa zarządzania obrazami, która umożliwia zarządzanie replikacją regionów obrazu, przechowywanie wersji i udostępnianie obrazów niestandardowych. Konstruktor obrazów platformy Azure obsługuje dystrybucję z tą usługą, dzięki czemu można dystrybuować obrazy do regionów obsługiwanych przez udostępnione Galerie obrazów. 
 
Udostępniona Galeria obrazów składa się z: 
 
- Galeria — kontener dla wielu obrazów udostępnionych. Galeria jest wdrażana w jednym regionie.
- Definicje obrazów — grupowanie koncepcji dla obrazów. 
- Wersje obrazu — ten typ obrazu służy do wdrażania maszyny wirtualnej lub zestawu skalowania. Wersje obrazu mogą być replikowane do innych regionów, w których należy wdrożyć maszyny wirtualne.
 
Przed rozpoczęciem dystrybucji do galerii obrazów należy utworzyć galerię i definicję obrazu, zobacz [obrazy udostępnione](../shared-images-cli.md). 

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

Dystrybuuj właściwości dla udostępnionych galerii obrazów:

- **Typ** — sharedImage  
- **galleryImageId** — identyfikator galerii obrazów udostępnionych, który można określić w dwóch formatach:
    * Automatyczne przechowywanie wersji — Konstruktor obrazów wygeneruje dla Ciebie numer wersji monotoniczny. jest to przydatne w przypadku, gdy chcesz przechowywać ponownie obrazy z tego samego szablonu: format jest następujący: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Jawne przechowywanie wersji — można przekazać numer wersji, który ma być używany przez program Image Builder. Format to: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** — unikatowa nazwa identyfikująca dystrybucję.  
- **artifactTags** — opcjonalne Tagi par wartości klucza określonego przez użytkownika.
- **replicationRegions** — tablica regionów do replikacji. Jednym z regionów musi być region, w którym została wdrożona Galeria. Dodanie regionów oznacza zwiększenie czasu kompilacji, ponieważ kompilacja nie zostanie ukończona, dopóki replikacja nie zostanie zakończona.
- **excludeFromLatest** (opcjonalnie) dzięki temu można oznaczyć, że utworzona wersja obrazu nie będzie używana jako Najnowsza wersja w definicji SIG, wartość domyślna to "false".
- **storageAccountType** (opcjonalnie) AIB obsługuje określanie tych typów magazynu dla wersji obrazu, która ma zostać utworzona:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Jeśli szablon obrazu i odwołanie `image definition` nie znajdują się w tej samej lokalizacji, zobaczysz dodatkowy czas na utworzenie obrazów. Konstruktor obrazów aktualnie nie ma `location` parametru zasobu wersji obrazu, dlatego pochodzi on z jego elementu nadrzędnego `image definition` . Na przykład, jeśli definicja obrazu ma wartość Zachodnie i chcesz, aby wersja obrazu była replikowana na wschód, obiekt BLOB jest kopiowany do zachodniego, z tego względu tworzony jest zasób wersji obrazu w zachodniej tabeli, a następnie replikacja do wschodniego. Aby uniknąć dodatkowego czasu replikacji, upewnij się, że `image definition` szablon obrazu znajduje się w tej samej lokalizacji.


### <a name="distribute-vhd"></a>Dystrybuuj: wirtualny dysk twardy  
Można wyprowadzać dane wyjściowe do dysku VHD. Następnie możesz skopiować dysk VHD i użyć go do opublikowania w portalu Azure MarketPlace lub użyć z Azure Stack.  

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

- **Typ** — wirtualny dysk twardy.
- **runOutputName** — unikatowa nazwa identyfikująca dystrybucję.  
- **Tagi** — opcjonalne Tagi par wartości klucza określonego przez użytkownika.
 
Usługa Azure Image Builder nie zezwala użytkownikowi na określenie lokalizacji konta magazynu, ale można wysłać zapytanie o stan w `runOutputs` celu pobrania lokalizacji.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Po utworzeniu dysku VHD skopiuj go do innej lokalizacji tak szybko, jak to możliwe. Wirtualny dysk twardy jest przechowywany na koncie magazynu w tymczasowej grupie zasobów utworzonej podczas przesyłania szablonu obrazu do usługi Azure Image Builder. Usunięcie szablonu obrazu spowoduje utratę wirtualnego dysku twardego. 

## <a name="image-template-operations"></a>Operacje szablonu obrazu

### <a name="starting-an-image-build"></a>Uruchamianie kompilacji obrazu
Aby rozpocząć kompilację, należy wywołać polecenie "Run" w zasobie szablonu obrazu, przykłady `run` poleceń:

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
Jeśli używasz kompilacji obrazu, że uważasz, że jest nieprawidłowa, oczekiwanie na dane wejściowe użytkownika lub użytkownik nie zostanie pomyślnie zakończony, możesz anulować kompilację.

Kompilację można anulować w dowolnym momencie. Jeśli faza dystrybucji została uruchomiona, można nadal anulować, ale konieczne będzie wyczyszczenie wszelkich obrazów, które mogą nie zostać ukończone. Polecenie Cancel nie czeka na zakończenie anulowania, Monitoruj, `lastrunstatus.runstate` Aby anulować postęp, korzystając z tych [poleceń](image-builder-troubleshoot.md#customization-log)stanu.


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

Istnieją przykładowe pliki JSON dla różnych scenariuszy w witrynie [GitHub usługi Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder).