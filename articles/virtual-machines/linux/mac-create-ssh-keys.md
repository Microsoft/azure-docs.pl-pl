---
title: Tworzenie i używanie pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Jak utworzyć i używać pary kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure w celu zwiększenia bezpieczeństwa procesu uwierzytelniania.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c77375782ba23114be1953d9f8ad7de31ab06f1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582191"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Szybkie kroki: Tworzenie pary kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure

Za pomocą pary kluczy Secure Shell (SSH) można tworzyć maszyny wirtualne na platformie Azure, które używają kluczy SSH do uwierzytelniania. W tym artykule pokazano, jak szybko generować i używać pary plików prywatnego publicznego klucza SSH dla maszyn wirtualnych z systemem Linux. Te kroki można wykonać przy użyciu Azure Cloud Shell, hosta macOS lub Linux. 

> [!NOTE]
> Maszyny wirtualne utworzone przy użyciu kluczy SSH są domyślnie skonfigurowane z wyłączonymi hasłami, co znacznie zwiększa trudność ataków typu "rozpuszczenie". 

Aby uzyskać więcej informacji o tle i przykładach, zobacz [szczegółowe instrukcje dotyczące tworzenia par kluczy SSH](create-ssh-keys-detailed.md).

Aby uzyskać dodatkowe sposoby generowania i używania kluczy SSH na komputerze z systemem Windows, zobacz [jak używać kluczy SSH w systemie Windows na platformie Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Użyj `ssh-keygen` polecenia, aby wygenerować pliki publicznego i prywatnego klucza SSH. Domyślnie te pliki są tworzone w katalogu ~/.SSH. Aby uzyskać dostęp do pliku klucza prywatnego, można określić inną lokalizację i opcjonalne hasło (*hasło*). Jeśli para kluczy SSH o tej samej nazwie istnieje w danej lokalizacji, te pliki są zastępowane.

Następujące polecenie tworzy parę kluczy SSH przy użyciu szyfrowania RSA i długość bitową 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Jeśli używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure) , aby utworzyć maszynę wirtualną z poleceniem [AZ VM Create](/cli/azure/vm#az-vm-create) , możesz opcjonalnie wygenerować pliki publicznego i prywatnego klucza SSH przy użyciu `--generate-ssh-keys` opcji. Pliki kluczy są przechowywane w katalogu ~/.SSH, chyba że określono inaczej z `--ssh-dest-key-path` opcją. Jeśli para kluczy SSH już istnieje i  `--generate-ssh-keys` jest używana opcja, Nowa para kluczy nie zostanie wygenerowana, ale zamiast tego zostanie użyta istniejąca para kluczy. W poniższym poleceniu Zastąp wartości *VMName* i *RGname* własnymi wartościami:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH podczas wdrażania maszyny wirtualnej

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, określ klucz publiczny SSH podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, szablonów Azure Resource Manager lub innych metod:

* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](quick-create-portal.md)
* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
* [Tworzenie maszyny wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md)

Jeśli nie znasz formatu klucza publicznego SSH, możesz wyświetlić swój klucz publiczny za pomocą następującego `cat` polecenia, zastępując `~/.ssh/id_rsa.pub` ścieżkę i nazwę pliku klucza publicznego, jeśli jest to konieczne:

```bash
cat ~/.ssh/id_rsa.pub
```

Typowa wartość klucza publicznego wygląda podobnie do tego przykładu:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Jeśli skopiujesz i wkleisz zawartość pliku klucza publicznego do użycia w Azure Portal lub szablon Menedżer zasobów, pamiętaj, aby nie kopiować żadnych białych znaków. Aby skopiować klucz publiczny w macOS, można przekazać plik klucza publicznego do programu `pbcopy` . Podobnie w systemie Linux można potokować plik klucza publicznego do programów takich jak `xclip` .

Klucz publiczny umieszczany na maszynie wirtualnej z systemem Linux na platformie Azure jest domyślnie przechowywany w folderze ~/.ssh/id_rsa. pub, chyba że podczas tworzenia pary kluczy określono inną lokalizację. Aby użyć [interfejsu wiersza polecenia platformy Azure 2,0](/cli/azure) do utworzenia maszyny wirtualnej z istniejącym kluczem publicznym, określ wartość i opcjonalnie lokalizację tego klucza publicznego przy użyciu polecenia [AZ VM Create](/cli/azure/vm#az-vm-create) z `--ssh-key-values` opcją. W poniższym poleceniu Zastąp *myVM*, *webresources*, *UbuntuLTS*, *azureuser* i *mysshkey. pub* własnymi wartościami:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Jeśli chcesz użyć wielu kluczy SSH z maszyną wirtualną, możesz wprowadzić je w postaci listy rozdzielonej spacjami, np `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` ..


## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH

Z kluczem publicznym wdrożonym na maszynie wirtualnej platformy Azure i kluczem prywatnym w systemie lokalnym Użyj protokołu SSH do maszyny wirtualnej przy użyciu adresu IP lub nazwy DNS maszyny wirtualnej. W poniższym poleceniu Zastąp *azureuser* i *MyVM.westus.cloudapp.Azure.com* nazwą użytkownika administratora oraz w pełni kwalifikowaną nazwą domeny (lub adresem IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy określono hasło, wprowadź je po wyświetleniu monitu podczas procesu logowania. Maszyna wirtualna jest dodawana do pliku ~/.ssh/known_hosts i nie zostanie wyświetlony monit o ponowne nawiązanie połączenia do momentu zmiany klucza publicznego na maszynie wirtualnej platformy Azure lub usunięcia nazwy serwera z ~/.ssh/known_hosts.

Jeśli maszyna wirtualna korzysta z zasad dostępu just in Time, przed nawiązaniem połączenia z maszyną wirtualną należy zażądać dostępu. Aby uzyskać więcej informacji na temat zasad just in Time, zobacz [Zarządzanie dostępem do maszyn wirtualnych przy użyciu zasad just in Time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z parami kluczy SSH, zobacz [szczegółowe instrukcje dotyczące tworzenia par kluczy SSH i zarządzania nimi](create-ssh-keys-detailed.md).

* Jeśli masz problemy z połączeniami SSH z maszynami wirtualnymi platformy Azure, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure Linux](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).