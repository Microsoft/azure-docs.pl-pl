---
title: Azure Cloud Shell szybki start — bash
description: Dowiedz się, jak używać wiersza polecenia bash w przeglądarce przy użyciu Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 91b7c58890518559c046023bd78c9248e9840f9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89468753"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Przewodnik Szybki Start dla usługi Bash w Azure Cloud Shell

Ten dokument zawiera szczegółowe informacje dotyczące używania bash w Azure Cloud Shell w [Azure Portal](https://ms.portal.azure.com/).

> [!NOTE]
> Dostępny jest również program [PowerShell w Azure Cloud Shell](quickstart-powershell.md) Szybki Start.

## <a name="start-cloud-shell"></a>Uruchamianie usługi Cloud Shell
1. Uruchom **Cloud Shell** od górnego nawigowania Azure Portal. <br>
![Zrzut ekranu przedstawiający sposób uruchamiania Azure Cloud Shell w Azure Portal.](media/quickstart/shell-icon.png)

2. Wybierz subskrypcję, aby utworzyć konto magazynu i udział plików Microsoft Azure.
3. Wybierz pozycję "Utwórz magazyn"

> [!TIP]
> Użytkownik jest automatycznie uwierzytelniany dla interfejsu wiersza polecenia platformy Azure w każdej sesji.

### <a name="select-the-bash-environment"></a>Wybierz środowisko bash
Upewnij się, że lista rozwijana środowiska z lewej strony okna powłoki ma wartość `Bash` . <br>
![Zrzut ekranu przedstawiający sposób wybierania środowiska bash dla Azure Cloud Shell.](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Ustawianie subskrypcji
1. Lista subskrypcji, do których masz dostęp.
   ```azurecli-interactive
   az account list
   ```

2. Ustaw preferowaną subskrypcję:

   ```azurecli-interactive
   az account set --subscription 'my-subscription-name'
   ```

> [!TIP]
> Twoja subskrypcja zostanie zapamiętana na potrzeby przyszłych sesji przy użyciu usługi `/home/<user>/.azure/azureProfile.json` .

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz nową grupę zasobów w zachodnim regionie o nazwie "Mojagz".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem Linux
Utwórz maszynę wirtualną Ubuntu w nowej grupie zasobów. Interfejs wiersza polecenia platformy Azure utworzy klucze SSH i skonfiguruje dla nich maszynę wirtualną. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Użycie `--generate-ssh-keys` powoduje, że interfejs wiersza polecenia platformy Azure tworzy i konfiguruje klucze publiczne i prywatne w maszynie wirtualnej i `$Home` katalogu. Klucze domyślne są umieszczane w Cloud Shell w `/home/<user>/.ssh/id_rsa` i `/home/<user>/.ssh/id_rsa.pub` . `.ssh`Folder jest utrwalany w dołączonym obrazie 5 GB udziału plików, który będzie używany do utrwalania `$Home` .

Nazwa użytkownika na tej maszynie wirtualnej będzie używana w Cloud Shell ($ User@Azure: ).

### <a name="ssh-into-your-linux-vm"></a>Użyj protokołu SSH na maszynie wirtualnej z systemem Linux
1. Wyszukaj nazwę maszyny wirtualnej na pasku wyszukiwania Azure Portal.
2. Kliknij przycisk "Połącz", aby uzyskać nazwę i publiczny adres IP maszyny wirtualnej. <br>
   ![Zrzut ekranu przedstawiający sposób nawiązywania połączenia z systemem Linux V M przy użyciu S S H.](media/quickstart/sshcmd-copy.png)

3. Za pomocą polecenia SSH do maszyny wirtualnej `ssh` .
   ```
   ssh username@ipaddress
   ```

Po ustanowieniu połączenia SSH powinien zostać wyświetlony monit Ubuntu powitalny. <br>
![Zrzut ekranu przedstawiający inicjalizację Ubuntu i monit powitalny po nawiązaniu połączenia S S H.](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Czyszczenie 
1. Zakończ sesję SSH.
   ```
   exit
   ```

2. Usuń grupę zasobów i wszystkie znajdujące się w niej zasoby.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o utrwalaniu plików dla bash w Cloud Shell](persisting-shell-storage.md) <br>
[Informacje o interfejsie wiersza polecenia platformy Azure](/cli/azure/) <br>
[Dowiedz się więcej o usłudze Azure Files Storage](../storage/files/storage-files-introduction.md) <br>