---
title: Szybki start — tworzenie maszyny Wirtualnej systemu Windows przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć maszynę wirtualną systemu Windows za pomocą interfejsu wiersza polecenia platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 37f86f480e0d2a46bb54ad9b041fb1892a9ebbc4
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458168"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Windows za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Z tego przewodnika Szybki start dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure wdrożyć na platformie Azure maszynę wirtualną z systemem Windows Server 2016. Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu RDP i zainstalujesz serwer internetowy usług IIS.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/bash](https://shell.azure.com/bash)osobnej karcie przeglądarki, przechodząc do . Wybierz **pozycję Kopiuj,** aby skopiować bloki kodu, wkleić go do powłoki chmury, a następnie naciśnij klawisz **Enter,** aby go uruchomić.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*. W tym przykładzie użyto *azureuser* dla nazwy użytkownika administracyjnego. 

Należy podać hasło spełniające [wymagania dotyczące haseł dla maszyn wirtualnych platformy Azure.](/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm
) Korzystając z poniższego przykładu, zostanie wyświetlony monit o wprowadzenie hasła w wierszu polecenia. Można również dodać `--admin-password` parametr z wartością hasła. Nazwa użytkownika i hasło będą używane później, po nawiązaniu połączenia z maszyną wirtualną.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser 
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja utworzenia maszyny wirtualnej zakończyła się pomyślnie.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość elementu `publicIpAddress` z danych wyjściowych maszyny wirtualnej. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w następnych krokach.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Domyślnie podczas tworzenia maszyny wirtualnej z systemem Windows na platformie Azure otwarte są tylko połączenia RDP. Otwórz port TCP 80 do używania z serwerem internetowym usług IIS za pomocą polecenia [az vm open-port](/cli/azure/vm):

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z komputera lokalnego. Zastąp adres IP publicznym adresem IP swojej maszyny wirtualnej. Gdy zostanie wyświetlony monit, wprowadź poświadczenia użyte podczas tworzenia maszyny wirtualnej:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy usług IIS. Na maszynie wirtualnej otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po zakończeniu zamknij połączenie RDP z maszyną wirtualną.

## <a name="view-the-web-server-in-action"></a>Oglądanie działającego serwera internetowego

Po zainstalowaniu usług IIS i otwarciu portu 80 na maszynie wirtualnej dla ruchu z Internetu wyświetl domyślną stronę powitalną serwera usług IIS przy użyciu wybranej przeglądarki internetowej. Użyj publicznego adresu IP maszyny wirtualnej uzyskanego w poprzednim kroku. W poniższym przykładzie przedstawiono domyślną witrynę internetową usług IIS:

![Domyślna witryna usług IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group):

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wdrażania maszyny wirtualnej, otwierania portu sieciowego na ruch internetowy oraz instalowania podstawowego serwera sieci Web. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](./tutorial-manage-vm.md)
