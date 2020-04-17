---
title: 'Szybki start: tworzenie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
ms.openlocfilehash: cbe5276f1b302d62e84c8ba448ad594694a8290c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458730"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki start pokazuje, jak używać interfejsu wiersza polecenia platformy Azure (CLI) do wdrażania maszyny wirtualnej systemu Linux (VM) na platformie Azure. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów.

W ramach tego samouczka zostanie zainstalowany system Ubuntu 16.04 LTS. Aby zobaczyć działanie maszyny wirtualnej, należy nawiązać z nią połączenie przy użyciu protokołu SSH i zainstalować serwer internetowy NGINX.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć usługę Cloud Shell w [https://shell.azure.com/bash](https://shell.azure.com/bash)osobnej karcie przeglądarki, przechodząc do . Wybierz **pozycję Kopiuj,** aby skopiować bloki kodu, wkleić go do powłoki chmury, a następnie wybierz pozycję **Enter,** aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm).

W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* i dodane konto użytkownika o nazwie *azureuser*. Parametr `--generate-ssh-keys` jest używany, aby automatycznie wygenerować klucz SSH i umieścić go w domyślnej lokalizacji klucza (*~/.ssh*). Aby zamiast niego użyć określonego zestawu kluczy, skorzystaj z opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
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
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość elementu `publicIpAddress` z danych wyjściowych maszyny wirtualnej. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w następnych krokach.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Domyślnie podczas tworzenia maszyny wirtualnej z systemem Linux na platformie Azure otwarte są tylko połączenia SSH. Otwórz port TCP 80 do używania z serwerem internetowym NGINX za pomocą polecenia [az vm open-port](/cli/azure/vm):

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Połącz się przez protokół SSH z maszyną wirtualną w zwykły sposób. Zastąp ciąg **publicIpAddress** publicznym adresem IP maszyny wirtualnej, jak wspomniano we wcześniejszych danych wyjściowych z maszyny wirtualnej:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy NGINX. Zaktualizuj zasoby pakietu, a następnie zainstaluj najnowszą wersję pakietu NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Gdy skończysz, wpisz polecenie `exit`, aby opuścić sesję SSH.

## <a name="view-the-web-server-in-action"></a>Oglądanie działającego serwera internetowego

Użyj wybranej przeglądarki internetowej, aby wyświetlić domyślną strona powitalną serwera NGINX. Użyj publicznego adresu IP maszyny wirtualnej jako adresu internetowego. W poniższym przykładzie przedstawiono domyślną witrynę internetową serwera NGINX:

![Wyświetlanie strony powitalnej serwera NGINX](./media/quick-create-cli/view-the-nginx-welcome-page.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć polecenia [delete grupy az,](/cli/azure/group) aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wdrażania maszyny wirtualnej, otwierania portu sieciowego na ruch internetowy oraz instalowania podstawowego serwera sieci Web. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Linux.


> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Linux](./tutorial-manage-vm.md)
