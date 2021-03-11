---
title: Tworzenie i przekazywanie obrazu OpenBSD
description: Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy (VHD) zawierający system operacyjny OpenBSD, aby utworzyć maszynę wirtualną platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 3437fcd2d1d521aad237ecf6d3f7937b4835d376
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554602"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Tworzenie i przekazywanie obrazu dysku OpenBSD na platformę Azure
W tym artykule przedstawiono sposób tworzenia i przekazywania wirtualnego dysku twardego (VHD) zawierającego system operacyjny OpenBSD. Po przekazaniu można użyć go jako własnego obrazu do utworzenia maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz następujące elementy:

* **Subskrypcja platformy Azure** — Jeśli nie masz konta, możesz ją utworzyć w zaledwie kilka minut. Jeśli masz subskrypcję MSDN, zobacz [comiesięczne środki na korzystanie z platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). W przeciwnym razie Dowiedz się, jak [utworzyć bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).  
* **Interfejs wiersza polecenia platformy Azure** — upewnij się, że zainstalowano najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zalogowano się na koncie platformy Azure z usługą [AZ login](/cli/azure/reference-index).
* **OpenBSD system operacyjny zainstalowany w pliku VHD** — na wirtualnym dysku twardym musi być zainstalowany obsługiwany system operacyjny OpenBSD ([6,6 w wersji amd64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)). Istnieje wiele narzędzi do tworzenia plików VHD. Na przykład można użyć rozwiązania wirtualizacji, takiego jak funkcja Hyper-V, aby utworzyć plik VHD i zainstalować system operacyjny. Aby uzyskać instrukcje dotyczące sposobu instalowania i używania funkcji Hyper-V, zobacz [Instalowanie funkcji Hyper-v i Tworzenie maszyny wirtualnej](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).


## <a name="prepare-openbsd-image-for-azure"></a>Przygotuj obraz OpenBSD dla platformy Azure
Na maszynie wirtualnej, na której zainstalowano system operacyjny OpenBSD 6,1, który dodano obsługę funkcji Hyper-V, wykonaj następujące procedury:

1. Jeśli usługa DHCP nie jest włączona podczas instalacji, należy ją włączyć w następujący sposób:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Skonfiguruj konsolę seryjną w następujący sposób:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Skonfiguruj instalację pakietu w następujący sposób:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Domyślnie `root` użytkownik jest wyłączony na maszynach wirtualnych na platformie Azure. Użytkownicy mogą uruchamiać polecenia z podwyższonym poziomem uprawnień przy użyciu `doas` polecenia na maszynie wirtualnej OpenBSD. DOAs jest domyślnie włączona. Aby uzyskać więcej informacji, zobacz [DOAs. conf](https://man.openbsd.org/doas.conf.5). 

5. Zainstaluj i skonfiguruj wymagania wstępne dla agenta platformy Azure w następujący sposób:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Najnowszą wersję usługi Azure Agent można znaleźć w witrynie [GitHub](https://github.com/Azure/WALinuxAgent/releases). Zainstaluj agenta w następujący sposób:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Po zainstalowaniu usługi Azure Agent warto sprawdzić, czy działa ona w następujący sposób:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Cofaj obsługę administracyjną systemu, aby oczyścić go i uczynić go odpowiednim do ponownego aprowizacji. Następujące polecenie usuwa także ostatnio zainicjowane konto użytkownika i powiązane dane:

    ```sh
    waagent -deprovision+user -force
    ```

Teraz możesz zamknąć maszynę wirtualną.


## <a name="prepare-the-vhd"></a>Przygotowywanie wirtualnego dysku twardego
Format VHDX nie jest obsługiwany na platformie Azure, tylko **stałego dysku VHD**. Dysk można przekonwertować na stały format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet [convert-VHD](/powershell/module/hyper-v/convert-vhd) programu PowerShell. Przykład jest następujący.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Tworzenie zasobów magazynu i przekazywanie
Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Aby przekazać dysk VHD, Utwórz konto magazynu za pomocą [AZ Storage account Create](/cli/azure/storage/account). Nazwy kont magazynu muszą być unikatowe, więc podaj własną nazwę. Poniższy przykład tworzy konto magazynu o nazwie *mojekontomagazynu*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Aby kontrolować dostęp do konta magazynu, należy uzyskać klucz magazynu za pomocą polecenie [AZ Storage account Keys list](/cli/azure/storage/account/keys) w następujący sposób:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Aby logicznie oddzielić przesyłane wirtualne dyski twarde, Utwórz kontener na koncie magazynu za pomocą polecenie [AZ Storage Container Create](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Na koniec Przekaż dysk VHD za pomocą [AZ Storage BLOB upload](/cli/azure/storage/blob) w następujący sposób:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Tworzenie maszyny wirtualnej na podstawie wirtualnego dysku twardego
Można utworzyć maszynę wirtualną z [przykładowym skryptem](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-vhd) lub bezpośrednio za pomocą [AZ VM Create](/cli/azure/vm). Aby określić OpenBSD wirtualny dysk twardy, użyj `--image` parametru w następujący sposób:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Uzyskaj adres IP dla maszyny wirtualnej OpenBSD za pomocą [AZ VM list-IP-addresss](/cli/azure/vm) w następujący sposób:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Teraz można obsłużyć protokół SSH na maszynie wirtualnej OpenBSD jako normalny:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Następne kroki
Jeśli chcesz dowiedzieć się więcej o obsłudze funkcji Hyper-V w systemie OpenBSD 6.1, przeczytaj artykuł [OpenBSD 6,1](https://www.openbsd.org/61.html) i [HyperV. 4](https://man.openbsd.org/hyperv.4).

Jeśli chcesz utworzyć maszynę wirtualną z dysku zarządzanego, Przeczytaj [AZ Disk](/cli/azure/disk).