---
title: Wdrażanie maszyn wirtualnych na urządzeniu z systemem Azure Stack Edge przy użyciu procesora GPU za pomocą interfejsu wiersza polecenia platformy Azure i języka Python
description: Zawiera opis sposobu tworzenia maszyn wirtualnych i zarządzania nimi na urządzeniu z systemem Azure Stack Edge przy użyciu interfejsu wiersza polecenia platformy Azure i języka Python.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: 54a4a938be18d39993652cecb87b3604e268fcef
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678957"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Wdrażanie maszyn wirtualnych na urządzeniu z systemem Azure Stack Edge na komputerze GPU przy użyciu interfejsu wiersza polecenia platformy Azure i języka Python

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

W tym samouczku opisano, jak utworzyć maszynę wirtualną i zarządzać nią na urządzeniu Azure Stack brzeg Pro przy użyciu interfejsu wiersza polecenia platformy Azure (CLI) i języka Python.

## <a name="vm-deployment-workflow"></a>Przepływ pracy wdrożenia maszyny wirtualnej

Przepływ pracy wdrażania przedstawiono na poniższym diagramie.

![Przepływ pracy wdrożenia maszyny wirtualnej](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

Podsumowanie wysokiego poziomu przepływu pracy wdrożenia jest następujące:

1. Połącz z Azure Resource Manager
2. Tworzenie grupy zasobów
3. Tworzenie konta magazynu
4. Dodaj identyfikator URI obiektu BLOB do pliku hosts
5. Instalowanie certyfikatów
6. Przekazywanie wirtualnego dysku twardego
7. Tworzenie dysków zarządzanych na podstawie wirtualnego dysku twardego
8. Tworzenie obrazu maszyny wirtualnej na podstawie dysku zarządzanego obrazu
9. Tworzenie maszyny wirtualnej z wcześniej utworzonymi zasobami
10. Tworzenie sieci wirtualnej
11. Tworzenie wirtualnej karty sieciowej przy użyciu identyfikatora podsieci sieci wirtualnej

Aby uzyskać szczegółowy opis diagramu przepływu pracy, zobacz [wdrażanie maszyn wirtualnych na urządzeniu Azure Stack EDGE Pro przy użyciu Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). Aby uzyskać informacje na temat nawiązywania połączenia z usługą Azure Resource Manager, zobacz [nawiązywanie połączenia z Azure Resource Manager przy użyciu Azure PowerShell](azure-stack-edge-j-series-connect-resource-manager.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tworzenia i zarządzania maszyną wirtualną na urządzeniu Azure Stack EDGE Pro przy użyciu interfejsu wiersza polecenia platformy Azure i języka Python należy upewnić się, że zostały spełnione wymagania wstępne wymienione w następujących krokach:

1. Ustawienia sieciowe zostały wykonane na urządzeniu z systemem Azure Stack brzeg Pro zgodnie z opisem w [sekcji Krok 1. konfigurowanie Azure Stack EDGE Pro Device](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. Włączono interfejs sieciowy dla obliczeń. Ten interfejs sieciowy IP służy do tworzenia przełącznika wirtualnego dla wdrożenia maszyny wirtualnej. Poniższe kroki przeprowadzą Cię przez proces:

    1. Przejdź do pozycji **obliczenia**. Wybierz interfejs sieciowy, który zostanie użyty do utworzenia przełącznika wirtualnego.

        > [!IMPORTANT] 
        > Dla obliczeń można skonfigurować tylko jeden port.

    2. Włącz obliczenia w interfejsie sieciowym. Azure Stack EDGE Pro tworzy przełącznik wirtualny i zarządza nim, odpowiadający interfejsowi sieciowemu.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. Utworzono i zainstalowano wszystkie certyfikaty na urządzeniu Azure Stack EDGE Pro i w zaufanym magazynie Twojego klienta. Postępuj zgodnie z procedurą opisaną w [kroku 2: Tworzenie i instalowanie certyfikatów](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

4. Utworzono certyfikat *CER* z kodowaniem Base-64 (format PEM) dla urządzenia Azure Stack EDGE Pro. Ten element został już przekazany jako łańcuch podpisywania na urządzeniu i zainstalowany w zaufanym magazynie głównym na komputerze klienckim. Ten certyfikat jest również wymagany w formacie *PEM* , aby można było korzystać z tego klienta w języku Python.

    Przekonwertuj ten certyfikat na format PEM przy użyciu `certutil` polecenia. Należy uruchomić to polecenie w katalogu, który zawiera certyfikat.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    Poniżej przedstawiono przykładowe użycie polecenia:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    Ten PEM zostanie również dodany do sklepu w języku Python później.

5. Adres IP urządzenia został przypisany do strony **sieci** w lokalnym interfejsie użytkownika sieci Web urządzenia. Musisz dodać ten adres IP do:

    - Plik hosta na kliencie lub,
    - Konfiguracja serwera DNS
    
    > [!IMPORTANT]
    > Zalecamy modyfikację konfiguracji serwera DNS na potrzeby rozpoznawania nazw punktów końcowych.

    1. Uruchom **Notatnik** jako administrator (do zapisania pliku wymagane są uprawnienia administratora), a następnie otwórz plik **hosts** znajdujący się w lokalizacji `C:\Windows\System32\Drivers\etc` .
    
        ![Eksplorator Windows — plik hosts](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Dodaj następujące wpisy do pliku **hosts** zastępujące odpowiednie wartości dla urządzenia:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Aby uzyskać informacje, użyj poniższego obrazu. Zapisz plik **hosts**.

        ![plik hosts w Notatniku](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [Pobierz skrypt języka Python](https://aka.ms/ase-vm-python) używany w tej procedurze.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>Krok 1. Konfigurowanie interfejsu wiersza polecenia platformy Azure/Python na kliencie

### <a name="verify-profile-and-install-azure-cli"></a>Weryfikowanie profilu i Instalowanie interfejsu wiersza polecenia platformy Azure

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Zainstaluj interfejs wiersza polecenia platformy Azure na kliencie. W tym przykładzie zainstalowano interfejs wiersza polecenia platformy Azure 2.0.80. Aby sprawdzić wersję interfejsu wiersza polecenia platformy Azure, uruchom `az --version` polecenie.

    Poniżej przedstawiono przykładowe dane wyjściowe powyższego polecenia:

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Jeśli nie masz interfejsu wiersza polecenia platformy Azure, Pobierz i [Zainstaluj interfejs wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows). Interfejs wiersza polecenia platformy Azure można uruchomić przy użyciu wiersza poleceń systemu Windows lub środowiska Windows PowerShell.

2. Zanotuj lokalizację języka Python interfejsu wiersza polecenia. Należy to zrobić, aby określić lokalizację zaufanego głównego magazynu certyfikatów dla interfejsu wiersza polecenia platformy Azure.

3. Do uruchomienia przykładowego skryptu używanego w tym artykule potrzebne są następujące wersje biblioteki języka Python:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    Aby zainstalować wersje, uruchom następujące polecenie:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    Następujące przykładowe dane wyjściowe pokazują instalację Haikunator:

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    Następujące przykładowe dane wyjściowe pokazują instalację PIP dla `msrestazure` : 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Ufaj certyfikatowi głównemu urzędu certyfikacji Azure Stack EDGE Pro

1. Znajdź lokalizację certyfikatu na swojej maszynie. Lokalizacja może się różnić w zależności od tego, gdzie zainstalowano `az cli` . Uruchom program Windows PowerShell jako administrator. Przejdź do ścieżki, w której `az cli` zainstalowano Język Python: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe` .

    Aby pobrać lokalizację certyfikatu, wpisz następujące polecenie:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    Polecenie cmdlet zwraca lokalizację certyfikatu, jak pokazano poniżej:  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Zanotuj tę lokalizację, ponieważ będziesz jej używać później — `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Ufaj certyfikatowi głównemu urzędu certyfikacji programu Azure Stack Edge, dołączając go do istniejącego certyfikatu języka Python. Podaj ścieżkę do lokalizacji, w której zapisano certyfikat PEM wcześniej.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    Przykładową ścieżką będzie "C:\VM-scripts\rootteam3device.pem"
    
    Następnie wpisz następującą serię poleceń w programie Windows PowerShell:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Nawiązywanie połączenia z Azure Stack EDGE Pro

1. Zarejestruj środowisko Azure Stack Edge, uruchamiając `az cloud register` polecenie.

    W niektórych scenariuszach bezpośrednia wychodząca łączność z Internetem jest kierowana za pośrednictwem serwera proxy lub zapory, co wymusza przechwycenie protokołu SSL. W takich przypadkach polecenie AZ Cloud Register może zakończyć się niepowodzeniem z powodu błędu, takiego jak \" nie można uzyskać punktów końcowych z chmury. \" Aby obejść ten błąd, ustaw następujące zmienne środowiskowe w programie Windows PowerShell:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Ustaw zmienne środowiskowe dla skryptu dla Azure Resource Manager punkt końcowy, lokalizację, w której są tworzone zasoby, oraz ścieżkę do lokalizacji źródłowego wirtualnego dysku twardego. Lokalizacja zasobów jest ustalana na wszystkich urządzeniach z Azure Stack EDGE Pro i jest ustawiona na `dbelocal` . Należy również określić prefiksy adresów i prywatny adres IP. Wszystkie następujące zmienne środowiskowe są wartościami na podstawie wartości z wyjątkiem `AZURE_RESOURCE_LOCATION` , które powinny być stałe `"dbelocal"` .

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Zarejestruj swoje środowisko. Użyj następujących parametrów podczas uruchamiania AZ Cloud Register:

    | Wartość | Opis | Przykład |
    | --- | --- | --- |
    | Nazwa środowiska | Nazwa środowiska, z którym próbujesz nawiązać połączenie | Podaj nazwę, na przykład `aze-environ` |
    | Menedżer zasobów punkt końcowy | Ten adres URL to `https://Management.<appliancename><dnsdomain>` . <br> Aby uzyskać ten adres URL, przejdź do strony **urządzenia** w lokalnym interfejsie użytkownika sieci Web urządzenia. |Na przykład `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    Poniżej przedstawiono przykładowe użycie powyższego polecenia:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Ustaw aktywne środowisko przy użyciu następujących poleceń:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    Poniżej przedstawiono przykładowe użycie powyższego polecenia:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Zaloguj się do swojego środowiska usługi Azure Stack EDGE Pro przy użyciu `az login` polecenia. Możesz zalogować się do środowiska Azure Stack EDGE Pro jako użytkownik lub jako nazwa [główna usługi](../active-directory/develop/app-objects-and-service-principals.md).

   Wykonaj następujące kroki, aby zalogować się jako *użytkownik*:

   Możesz określić nazwę użytkownika i hasło bezpośrednio w `az login` poleceniu lub uwierzytelnić się za pomocą przeglądarki. Należy to zrobić, jeśli konto ma włączone uwierzytelnianie wieloskładnikowe.

   Poniżej przedstawiono przykładowe użycie `az login` :
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   Po użyciu polecenia Zaloguj zostanie wyświetlony monit o podanie hasła. Podaj Azure Resource Manager hasło.

   Poniżej przedstawiono przykładowe dane wyjściowe dla pomyślnego logowania po podaniu hasła:  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   Zanotuj `id` wartości i, `tenantId` tak jak odpowiadają one identyfikatorowi subskrypcji Azure Resource Manager i Azure Resource Manager identyfikator dzierżawy i zostaną użyte w późniejszym kroku.
       
   Następujące zmienne środowiskowe muszą zostać ustawione tak, aby działały jako nazwa *główna usługi*:

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   Identyfikator klienta Azure Resource Manager jest zakodowany na stałe. Identyfikator dzierżawy Azure Resource Manager i Identyfikator subskrypcji Azure Resource Manager są obecne w danych wyjściowych `az login` polecenia wykonanego wcześniej. Wpis tajny klienta Azure Resource Manager jest ustawionym hasłem Azure Resource Manager.

   Aby uzyskać więcej informacji, zobacz [Azure Resource Manager hasło](azure-stack-edge-j-series-set-azure-resource-manager-password.md).

5. Zmień profil do wersji 2019-03-01-hybrydowej. Aby zmienić wersję profilu, uruchom następujące polecenie:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    Poniżej przedstawiono przykładowe użycie `az cloud update` :

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>Krok 2. Tworzenie maszyny wirtualnej

Skrypt języka Python jest dostarczany w celu utworzenia maszyny wirtualnej. W zależności od tego, czy użytkownik jest zalogowany jako użytkownik, czy jako nazwa główna usługi, skrypt pobiera dane wejściowe odpowiednio i tworzy maszynę wirtualną.

1. Uruchom skrypt języka Python z tego samego katalogu, w którym zainstalowano Język Python.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. Po uruchomieniu skryptu przekazanie dysku VHD trwa 20-30 minut. Aby wyświetlić postęp operacji przekazywania, można użyć Eksplorator usługi Azure Storage lub AzCopy.

    Oto przykładowe dane wyjściowe pomyślnego uruchomienia skryptu. Skrypt tworzy wszystkie zasoby w grupie zasobów, używa tych zasobów do tworzenia maszyny wirtualnej, a następnie usuwa grupę zasobów, w tym wszystkie utworzone przez siebie zasoby.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Następne kroki

[Typowe polecenia AZ CLI dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/cli-manage.md)