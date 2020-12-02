---
title: Wdrażaj maszyny wirtualne na urządzeniu Azure Stack EDGE Pro za pośrednictwem szablonów
description: Opisuje sposób tworzenia maszyn wirtualnych i zarządzania nimi na urządzeniu z systemem Azure Stack Edge przy użyciu szablonów.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/16/2020
ms.author: alkohli
ms.openlocfilehash: 93df80cd6fcd6f5553ea509a4778a155299bb057
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449060"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Wdrażaj maszyny wirtualne na urządzeniu z systemem Azure Stack Edge na komputerze GPU przy użyciu szablonów

W tym samouczku opisano sposób tworzenia maszyny wirtualnej na urządzeniu z systemem Azure Stack Edge przy użyciu szablonów i zarządzania nią. Te szablony są plikami JavaScript Object Notation (JSON), które definiują infrastrukturę i konfigurację maszyny wirtualnej. W tych szablonach należy określić zasoby do wdrożenia oraz właściwości tych zasobów.

Szablony są elastyczne w różnych środowiskach, ponieważ mogą one przyjmować parametry jako dane wejściowe w czasie wykonywania z pliku. Standardowa struktura nazewnictwa jest `TemplateName.json` dla szablonu i `TemplateName.parameters.json` pliku parametrów. Aby uzyskać więcej informacji na temat szablonów ARM, przejdź do [co to są szablony Azure Resource Manager?](../azure-resource-manager/templates/overview.md).

W tym samouczku użyjemy wstępnie zapisanych przykładowych szablonów do tworzenia zasobów. Nie trzeba edytować pliku szablonu i można modyfikować tylko te `.parameters.json` pliki, aby dostosować wdrożenie do maszyny. 

## <a name="vm-deployment-workflow"></a>Przepływ pracy wdrożenia maszyny wirtualnej

Aby wdrożyć maszyny wirtualne z systemem Azure Stack Edge na wielu urządzeniach, możesz użyć jednego wirtualnego dysku twardego Sysprep dla całej floty, tego samego szablonu do wdrożenia i po prostu wprowadzić drobne zmiany parametrów do tego szablonu dla każdej lokalizacji wdrożenia (te zmiany mogą być wykonywane w tym miejscu lub w sposób programowy). 

Podsumowanie wysokiego poziomu przepływu pracy wdrożenia przy użyciu szablonów jest następujące:

1. **Konfigurowanie wymagań wstępnych** — istnieją 3 typy wymagań wstępnych; urządzenie, klient i dla maszyny wirtualnej.

    1. **Wymagania wstępne dotyczące urządzeń**

        1. Połącz się z Azure Resource Manager na urządzeniu.
        2. Włącz obliczenia za pośrednictwem lokalnego interfejsu użytkownika.

    2. **Wymagania wstępne klienta**

        1. Pobierz szablony maszyn wirtualnych i skojarzone pliki na komputerze klienckim.
        1. Opcjonalnie Skonfiguruj protokół TLS 1,2 na kliencie.
        1. [Pobierz i zainstaluj Eksplorator usługi Microsoft Azure Storage](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) na kliencie.

    3. **Wymagania wstępne maszyny wirtualnej**

        1. Utwórz grupę zasobów w lokalizacji urządzenia, która będzie zawierać wszystkie zasoby maszyn wirtualnych.
        1. Utwórz konto magazynu, aby przekazać dysk VHD użyty do utworzenia obrazu maszyny wirtualnej.
        1. Dodaj identyfikator URI konta magazynu lokalnego do serwera DNS lub pliku Hosts na kliencie, który uzyskuje dostęp do urządzenia.
        1. Zainstaluj na urządzeniu certyfikat magazynu obiektów blob, a także lokalny klient uzyskujący dostęp do urządzenia. Opcjonalnie Zainstaluj certyfikat magazynu obiektów BLOB na Eksplorator usługi Storage.
        1. Utwórz i przekaż dysk VHD do utworzonego wcześniej konta magazynu.

2. **Tworzenie maszyny wirtualnej na podstawie szablonów**

    1. Utwórz obraz maszyny wirtualnej i sieć wirtualną przy użyciu `CreateImageAndVnet.parameters.json` pliku parametrów i `CreateImageAndVnet.json` szablonu wdrożenia.
    1. Utwórz maszynę wirtualną z utworzonymi wcześniej zasobami przy użyciu `CreateVM.parameters.json` pliku parametrów i  `CreateVM.json` szablonu wdrożenia.

## <a name="device-prerequisites"></a>Wymagania wstępne dotyczące urządzeń

Skonfiguruj te wymagania wstępne na urządzeniu z systemem Azure Stack brzeg Pro.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Wymagania wstępne klienta

Skonfiguruj te wymagania wstępne na kliencie, które będą używane w celu uzyskania dostępu do urządzenia z systemem Azure Stack Edge.

1. [Pobierz Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) , jeśli używasz go do przekazywania dysku VHD. Alternatywnie możesz pobrać AzCopy, aby przekazać dysk VHD. Jeśli używasz starszych wersji programu AzCopy, może być konieczne skonfigurowanie protokołu TLS 1,2 na komputerze klienckim. 
1. [Pobierz szablony maszyn wirtualnych i pliki parametrów](https://aka.ms/ase-vm-templates) na komputer kliencki. Rozpakuj go do katalogu, który będzie używany jako katalog roboczy.


## <a name="vm-prerequisites"></a>Wymagania wstępne maszyny wirtualnej

Skonfiguruj te wymagania wstępne, aby utworzyć zasoby, które będą wymagane do utworzenia maszyny wirtualnej. 

    
### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure, takie jak konto magazynu, dysk zarządzany i zarządzany.

> [!IMPORTANT]
> Wszystkie zasoby są tworzone w tej samej lokalizacji, w której znajduje się urządzenie, a lokalizacja jest ustawiona na **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Poniżej pokazano przykładowe dane wyjściowe.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz nowe konto magazynu przy użyciu grupy zasobów utworzonej w poprzednim kroku. Jest to **konto magazynu lokalnego** , które zostanie użyte do przekazania obrazu dysku wirtualnego dla maszyny wirtualnej.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Tylko lokalne konta magazynu, takie jak Magazyn lokalnie nadmiarowy (Standard_LRS lub Premium_LRS), można tworzyć za pośrednictwem Azure Resource Manager. Aby utworzyć konta magazynu warstwowego, zapoznaj się z instrukcjami w temacie [Dodawanie i nawiązywanie połączenia z kontami magazynu w witrynie Azure Stack EDGE Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Poniżej pokazano przykładowe dane wyjściowe.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Aby uzyskać klucz konta magazynu, uruchom `Get-AzureRmStorageAccountKey` polecenie. Przykładowe dane wyjściowe tego polecenia przedstawiono poniżej.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Dodaj identyfikator URI obiektu BLOB do pliku hosts

Upewnij się, że został już dodany identyfikator URI obiektu BLOB w pliku hosts dla klienta, który jest używany do łączenia się z usługą BLOB Storage. **Uruchom program Notepad jako administrator** i Dodaj następujący wpis dla identyfikatora URI obiektu BLOB w obiekcie `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

W typowym środowisku skonfigurowano system DNS tak, aby wszystkie konta magazynu wskazywały na urządzenie Azure Stack EDGE Pro z `*.blob.devicename.domainname.com` wpisem.

### <a name="optional-install-certificates"></a>Obowiązkowe Instalowanie certyfikatów

Pomiń ten krok, jeśli zostanie nawiązane połączenie za pośrednictwem Eksplorator usługi Storage przy użyciu *protokołu HTTP*. W przypadku korzystania z *protokołu HTTPS* należy zainstalować odpowiednie certyfikaty w Eksplorator usługi Storage. W takim przypadku należy zainstalować certyfikat punktu końcowego obiektu BLOB. Aby uzyskać więcej informacji, zobacz jak tworzyć i przekazywać certyfikaty w artykule [Zarządzanie certyfikatami](azure-stack-edge-j-series-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Tworzenie i przekazywanie wirtualnego dysku twardego

Upewnij się, że masz obraz dysku wirtualnego, którego można użyć do przekazania w późniejszym kroku. Wykonaj kroki opisane w temacie [Tworzenie obrazu maszyny wirtualnej](azure-stack-edge-j-series-create-virtual-machine-image.md). 

Skopiuj wszystkie obrazy dysków, które mają być używane do stronicowych obiektów BLOB na lokalnym koncie magazynu utworzonym w poprzednich krokach. Możesz użyć narzędzia, takiego jak [Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) lub [AzCopy, aby przekazać dysk VHD do konta magazynu](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) utworzonego w poprzednich krokach. 

### <a name="use-storage-explorer-for-upload"></a>Użyj Eksplorator usługi Storage do przekazania

1. Otwórz Eksploratora usługi Storage. Przejdź do obszaru **Edycja** i upewnij się, że aplikacja ma ustawioną wartość **docelową Azure Stack interfejsów API**.

    ![Ustaw element docelowy na Azure Stack interfejsów API](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Zainstaluj certyfikat klienta w formacie PEM. Przejdź do pozycji **edytuj > certyfikaty SSL > zaimportować certyfikaty**. Wskaż certyfikat klienta.

    ![Importuj certyfikat punktu końcowego magazynu obiektów BLOB](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - W przypadku korzystania z certyfikatów generowanych przez urządzenia Pobierz i Przekonwertuj certyfikat punktu końcowego magazynu obiektów BLOB `.cer` na `.pem` Format. Uruchom następujące polecenie. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Jeśli używasz własnego certyfikatu, Użyj certyfikatu głównego łańcucha podpisywania w `.pem` formacie.

3. Po zaimportowaniu certyfikatu ponownie uruchom Eksplorator usługi Storage, aby zmiany zaczęły obowiązywać.

    ![Uruchom ponownie Eksplorator usługi Storage](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. W lewym okienku kliknij prawym przyciskiem myszy pozycję **konta magazynu** i wybierz pozycję **Połącz z usługą Azure Storage**. 

    ![Łączenie z usługą Azure Storage 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Wybierz pozycję **Użyj klucza i nazwy konta magazynu**. Wybierz opcję **Dalej**.

    ![Nawiązywanie połączenia z usługą Azure Storage 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. W oknie **łączenie z nazwą i kluczem** Podaj **nazwę wyświetlaną**, **nazwę konta magazynu** i **klucz konta** usługi Azure Storage. Wybierz **inną** domenę magazynu, a następnie podaj `<device name>.<DNS domain>` Parametry połączenia. Jeśli certyfikat nie został zainstalowany w Eksplorator usługi Storage, zaznacz opcję **Użyj protokołu HTTP** . Wybierz opcję **Dalej**.

    ![Nawiązywanie połączenia przy użyciu nazwy i klucza](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Przejrzyj **Podsumowanie połączenia** i wybierz pozycję **Połącz**.

8. Konto magazynu zostanie wyświetlone w okienku po lewej stronie. Wybierz i rozwiń konto magazynu. Wybierz **kontenery obiektów BLOB**, kliknij prawym przyciskiem myszy i wybierz pozycję **Utwórz kontener obiektów BLOB**. Podaj nazwę kontenera obiektów BLOB.

9. Wybierz kontener, który został właśnie utworzony, a następnie w okienku po prawej stronie wybierz pozycję **przekaż > Przekaż pliki**. 

    ![Przekaż plik VHD 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Przeglądaj i wskaż wirtualny dysk twardy, który chcesz przekazać w **wybranych plikach**. Wybierz **Typ obiektu BLOB** jako **obiekt BLOB strony** i wybierz pozycję **Przekaż**.

    ![Przekaż plik VHD 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Po załadowaniu wirtualnego dysku twardego do kontenera obiektów BLOB wybierz dysk VHD, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Właściwości**. 

    ![Przekaż plik VHD 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Skopiuj i Zapisz **Identyfikator URI** , ponieważ będzie on używany w dalszych krokach.

    ![Kopiuj identyfikator URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>Tworzenie obrazu i sieci wirtualnej dla maszyny wirtualnej

Aby utworzyć obraz i sieć wirtualną dla maszyny wirtualnej, należy edytować `CreateImageAndVnet.parameters.json` plik parametrów, a następnie wdrożyć szablon `CreateImageAndVnet.json` , który używa tego pliku parametrów.


### <a name="edit-parameters-file"></a>Edytuj plik parametrów

Plik `CreateImageAndVnet.parameters.json` przyjmuje następujące parametry: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

Edytuj plik, `CreateImageAndVnet.parameters.json` Aby uwzględnić następujące elementy na urządzeniu Azure Stack EDGE Pro:

1. Podaj typ systemu operacyjnego odpowiadający dyskowi VHD, który zostanie przekazany. Typem systemu operacyjnego może być Windows lub Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Zmień identyfikator URI obrazu na identyfikator URI obrazu przekazanego we wcześniejszym kroku:

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Jeśli używasz *protokołu HTTP* z Eksplorator usługi Storage, zmień go na identyfikator URI *https* .

3. Zmień wartość `addressPrefix` i `subnetPrefix` . W lokalnym interfejsie użytkownika urządzenia przejdź do strony **Sieć** . Znajdź port włączony do obliczania. Pobierz adres IP sieci podstawowej i Dodaj maskę podsieci, aby utworzyć notację CIDR. Jeśli masz standardową podsieć 255.255.255.0, zrób to, zastępując ostatnią liczbę adresów IP wartością 0 i dodając/24 do końca. Dlatego 10.126.68.0 z maską podsieci 255.255.255.0 zmieni się na 10.126.68.0/24. 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. Podaj unikatową nazwę obrazu, nazwę sieci wirtualnej i nazwę podsieci dla parametrów.

    Oto przykładowy kod JSON, który jest używany w tym artykule.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. Zapisz plik parametrów.


### <a name="deploy-template"></a>Wdrażanie szablonu 

Wdróż szablon `CreateImageAndVnet.json` . Ten szablon wdraża zasoby sieci wirtualnej i obrazów, które będą używane do tworzenia maszyn wirtualnych w późniejszym kroku.

> [!NOTE]
> Po wdrożeniu szablonu w przypadku uzyskania błędu uwierzytelniania Twoje poświadczenia platformy Azure dla tej sesji mogły wygasnąć. Ponownie uruchom `login-AzureRM` polecenie, aby połączyć się z Azure Resource Manager na urządzeniu Azure Stack EDGE Pro.

1. Uruchom następujące polecenie: 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. Sprawdź, czy pomyślnie zainicjowano Inicjowanie obsługi obrazu i zasobów sieci wirtualnej. Oto przykładowe dane wyjściowe pomyślnie utworzonego obrazu i sieci wirtualnej.
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>Tworzenie maszyny wirtualnej

### <a name="edit-parameters-file-to-create-vm"></a>Edytuj plik parametrów, aby utworzyć maszynę wirtualną
 
Aby utworzyć maszynę wirtualną, użyj pliku parametrów `CreateVM.parameters.json`. Przyjmuje poniższe parametry.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Przypisz odpowiednie parametry do `CreateVM.parameters.json` urządzenia z Azure Stack Edge.

1. Podaj unikatową nazwę, nazwę interfejsu sieciowego i nazwę ipconfig. 
1. Wprowadź nazwę użytkownika, hasło i obsługiwane rozmiary maszyn wirtualnych.
1. Nadaj tej samej nazwie **VnetName**, **Subnetname** i **ImageName** , zgodnie z parametrami dla `CreateImageAndVnet.parameters.json` . Na przykład, jeśli określono VnetName, subnetname i ImageName jako **vnet1**, **subnet1** i **image1**, należy pozostawić te wartości te same dla parametrów w tym szablonie.
1. Teraz musisz mieć statyczny adres IP, aby przypisać do maszyny wirtualnej, która znajduje się w sieci podsieci zdefiniowanej powyżej. Zastąp **PrivateIPAddress** tym adresem w pliku parametrów. Aby maszyna wirtualna mogła uzyskać adres IP z lokalnego serwera protokół DHCP;, pozostaw `privateIPAddress` wartość pustą.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Zapisz plik parametrów.

    Oto przykładowy kod JSON, który jest używany w tym artykule.
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Wdróż szablon w celu utworzenia maszyny wirtualnej

Wdróż szablon tworzenia maszyny wirtualnej `CreateVM.json` . Ten szablon tworzy interfejs sieciowy z istniejącej sieci wirtualnej i tworzy maszynę wirtualną na podstawie wdrożonego obrazu.

1. Uruchom następujące polecenie: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    Tworzenie maszyny wirtualnej zajmie 15-20 minut. Oto przykładowe dane wyjściowe pomyślnie utworzonej maszyny wirtualnej.
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   

    Możesz również uruchomić `New-AzureRmResourceGroupDeployment` polecenie asynchronicznie za pomocą `–AsJob` parametru. Oto przykładowe dane wyjściowe, gdy polecenie cmdlet zostanie uruchomione w tle. Następnie można wykonać zapytanie o stan zadania tworzonego przy użyciu `Get-Job` polecenia cmdlet.

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Sprawdź, czy maszyna wirtualna została pomyślnie zainicjowana. Uruchom następujące polecenie:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Łączenie z maszyną wirtualną

W zależności od tego, czy utworzono maszynę wirtualną z systemem Windows, czy z systemem Linux, kroki do połączenia mogą być różne.

### <a name="connect-to-windows-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows

Wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux

Wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge Pro device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>Nieobsługiwane operacje maszyn wirtualnych i polecenia cmdlet

Rozszerzenia, zestawy skalowania, zestawy dostępności, migawki nie są obsługiwane.

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge Pro device.

On the client used to access your Azure Stack Edge Pro device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>Następne kroki

[Polecenia cmdlet Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)