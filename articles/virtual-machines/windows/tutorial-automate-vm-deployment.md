---
title: Samouczek — Instalowanie aplikacji na maszynie wirtualnej z systemem Windows na platformie Azure
description: Z tego samouczka dowiesz się, jak za pomocą rozszerzenia niestandardowego skryptu uruchamiać skrypty i wdrażać aplikacje na maszynach wirtualnych z systemem Windows na platformie Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 14d0190a97c22a805065ceaf41dcd655b9e8182b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065289"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Samouczek — wdrażanie aplikacji na maszynie wirtualnej z systemem Windows na platformie Azure za pomocą rozszerzenia niestandardowego skryptu

Aby skonfigurować maszyny wirtualne w szybki i spójny sposób, można użyć [rozszerzenia niestandardowego skryptu dla systemu Windows](../extensions/custom-script-windows.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie usług IIS za pomocą rozszerzenia niestandardowego skryptu
> * Tworzenie maszyny wirtualnej korzystającej z rozszerzenia niestandardowego skryptu
> * Wyświetlanie działającej witryny usług IIS po zastosowaniu rozszerzenia

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="custom-script-extension-overview"></a>Rozszerzenie niestandardowego skryptu — omówienie
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia.

Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager, a także uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, witryny Azure Portal lub interfejsu API REST maszyny wirtualnej platformy Azure.

Rozszerzenia niestandardowego skryptu można używać na maszynach wirtualnych z systemem Windows lub Linux.


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* w lokalizacji *EastUS*. Grupa zasobów *myResourceGroupAutomate* i pomocnicze zasoby sieciowe zostaną utworzone, jeśli jeszcze nie istnieją. To polecenie cmdlet otwiera również port *80* w celu obsługi ruchu internetowego.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Utworzenie maszyny wirtualnej i zasobów może potrwać kilka minut.


## <a name="automate-iis-install"></a>Automatyzowanie instalacji usług IIS
Zainstaluj rozszerzenie niestandardowego skryptu przy użyciu polecenia [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension). To rozszerzenie uruchamia polecenie `powershell Add-WindowsFeature Web-Server`, aby zainstalować serwer internetowy usług IIS, a następnie aktualizuje stronę *Default.htm* w celu wyświetlenia nazwy hosta maszyny wirtualnej:

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testowanie witryny internetowej
Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIPAddress*:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona witryna internetowa z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Działająca witryna internetowa usług IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem zautomatyzowano instalację usług IIS na maszynie wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Instalowanie usług IIS za pomocą rozszerzenia niestandardowego skryptu
> * Tworzenie maszyny wirtualnej korzystającej z rozszerzenia niestandardowego skryptu
> * Wyświetlanie działającej witryny usług IIS po zastosowaniu rozszerzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak tworzyć niestandardowe obrazy maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych obrazów maszyn wirtualnych](./tutorial-custom-images.md)
