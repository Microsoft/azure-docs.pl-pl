---
title: Wdrażanie maszyn wirtualnych na urządzeniu z systemem Azure Stack Edge za pośrednictwem Azure PowerShell
description: Opisuje sposób tworzenia maszyn wirtualnych i zarządzania nimi na Azure Stack urządzeniu brzegowym przy użyciu Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 6e776b6dfc233ffb12d3597a0e6bc203f1674abd
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147069"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell-script"></a>Wdrażanie maszyn wirtualnych na urządzeniu Azure Stack Edge za pomocą skryptu Azure PowerShell

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym samouczku opisano, jak utworzyć maszynę wirtualną na urządzeniu Azure Stack Edge przy użyciu skryptu Azure PowerShell i zarządzać nią.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tworzenia i zarządzania maszyną wirtualną na urządzeniu Azure Stack Edge przy użyciu tego skryptu musisz upewnić się, że zostały spełnione wymagania wstępne wymienione w następujących krokach:

### <a name="for-azure-stack-edge-device-via-the-local-web-ui"></a>Dla Azure Stack urządzenia brzegowego za pośrednictwem lokalnego interfejsu użytkownika sieci Web

1. Ustawienia sieciowe na urządzeniu Azure Stack Edge zostały wykonane zgodnie z opisem w [sekcji Krok 1: konfigurowanie Azure Stack urządzenia brzegowego](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-device).

2. Włączono interfejs sieciowy dla obliczeń. Ten interfejs sieciowy IP służy do tworzenia przełącznika wirtualnego dla wdrożenia maszyny wirtualnej. Poniższe kroki przeprowadzą Cię przez proces:

    1. Przejdź do **ustawień obliczenia**. Wybierz interfejs sieciowy, który zostanie użyty do utworzenia przełącznika wirtualnego.

        > [!IMPORTANT] 
        > Dla obliczeń można skonfigurować tylko jeden port.

    2. Włącz obliczenia w interfejsie sieciowym. Azure Stack Edge tworzy przełącznik wirtualny i zarządza nim, odpowiadający interfejsowi sieciowemu.

3. Utworzono i zainstalowano wszystkie certyfikaty na urządzeniu brzegowym Azure Stack i w zaufanym magazynie głównym klienta. Postępuj zgodnie z procedurą opisaną w [kroku 2: Tworzenie i instalowanie certyfikatów](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

### <a name="for-your-windows-client"></a>Dla klienta systemu Windows

1. Na stronie **sieci** w lokalnym interfejsie użytkownika sieci Web urządzenia został zdefiniowany wirtualny protokół internetowy (VIP) platformy Azure. Musisz dodać ten adres VIP do:

    - Plik hosta na kliencie lub,
    - Konfiguracja serwera DNS
    
    > [!IMPORTANT]
    > Zalecamy modyfikację konfiguracji serwera DNS na potrzeby rozpoznawania nazw punktów końcowych.

    1. Uruchom **Notatnik** jako administrator (do zapisania pliku wymagane są uprawnienia administratora), a następnie otwórz plik **hosts** znajdujący się w lokalizacji `C:\Windows\System32\Drivers\etc` .
    
        ![Eksplorator Windows — plik hosts](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Dodaj następujące wpisy do pliku **hosts** zastępujące odpowiednie wartości dla urządzenia:
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        W przypadku konta magazynu można podać nazwę, która ma być używana przez skrypt później w celu utworzenia nowego konta magazynu. Skrypt nie sprawdza, czy to konto magazynu jest już istniejące.

    3. Aby uzyskać informacje, użyj poniższego obrazu. Zapisz plik **hosts**.

        ![plik hosts w Notatniku](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Pobierz skrypt programu PowerShell](https://aka.ms/ase-vm-powershell) użyty w tej procedurze.

3. Upewnij się, że klient systemu Windows korzysta z programu PowerShell 5,0 lub nowszego.

4. Upewnij się, że `Azure.Storage Module version 4.5.0` jest zainstalowany w systemie. Ten moduł można pobrać z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0). Aby zainstalować ten moduł, wpisz:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Aby sprawdzić wersję zainstalowanego modułu, wpisz:

    `Get-InstalledModule -name Azure.Storage`

    Aby odinstalować wszystkie inne moduły wersji, wpisz:

    `Uninstall-Module -Name Azure.Storage`

5. [Pobierz AzCopy 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#download-azcopy) do klienta systemu Windows. Zanotuj tę lokalizację, ponieważ zostanie ona przekazana jako parametr podczas uruchamiania skryptu.

6. Upewnij się, że klient systemu Windows korzysta z protokołu TLS 1,2 lub nowszego.


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Uruchom program PowerShell jako administrator.
2. Przejdź do folderu, w którym został pobrany skrypt na kliencie.  
3. Użyj następującego polecenia, aby uruchomić skrypt:
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    Poniżej przedstawiono przykłady wykonywania skryptu w celu utworzenia maszyny wirtualnej z systemem Windows i maszyny wirtualnej z systemem Linux.

    **Dla maszyny wirtualnej z systemem Windows:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **Dla maszyny wirtualnej z systemem Linux:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. Aby wyczyścić zasoby utworzone przez skrypt, użyj następujących poleceń:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>Następne kroki

[Wdrażanie maszyn wirtualnych przy użyciu Azure PowerShell poleceń cmdlet](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)
