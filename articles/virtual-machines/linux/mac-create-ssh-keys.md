---
title: Tworzenie i używanie pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Jak utworzyć parę kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure i używać jej w celu zwiększenia bezpieczeństwa procesu uwierzytelniania.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c5e683e1f5af42a69fac45c20f52169834967649
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788137"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Szybkie kroki: tworzenie i używanie pary kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure

Para kluczy Secure Shell (SSH) umożliwia tworzenie maszyn wirtualnych na platformie Azure, które używają kluczy SSH do uwierzytelniania. W tym artykule przedstawiono sposób szybkiego generowania i używania pary plików SSH z kluczem publicznym i prywatnym dla maszyn wirtualnych z systemem Linux. Te kroki można wykonać za pomocą Azure Cloud Shell, hosta z systemem macOS lub Linux. 

> [!NOTE]
> Maszyny wirtualne utworzone przy użyciu kluczy SSH są domyślnie konfigurowane z wyłączonymi hasłami, co znacznie zwiększa trudność w przypadku ataków siłowych. 

Aby uzyskać więcej informacji i przykładów, zobacz [Szczegółowe kroki tworzenia par kluczy SSH.](create-ssh-keys-detailed.md)

Aby uzyskać dodatkowe sposoby generowania i używania kluczy SSH na komputerze z systemem Windows, zobacz Jak używać kluczy SSH w [systemie Windows na platformie Azure.](ssh-from-windows.md)

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Użyj polecenia `ssh-keygen` , aby wygenerować pliki kluczy publicznych i prywatnych SSH. Domyślnie te pliki są tworzone w katalogu ~/.ssh. Możesz określić inną lokalizację i opcjonalne hasło *(hasło),* aby uzyskać dostęp do pliku klucza prywatnego. Jeśli w danej lokalizacji istnieje para kluczy SSH o tej samej nazwie, te pliki zostaną zastąpione.

Następujące polecenie tworzy parę kluczy SSH przy użyciu szyfrowania RSA i długości bitowej 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Jeśli tworzysz maszynę wirtualną za pomocą interfejsu wiersza polecenia platformy [Azure](/cli/azure) za pomocą polecenia [az vm create,](/cli/azure/vm#az_vm_create) możesz opcjonalnie wygenerować pliki kluczy publicznych i prywatnych SSH przy użyciu `--generate-ssh-keys` opcji . Pliki kluczy są przechowywane w katalogu ~/.ssh, chyba że określono inaczej z `--ssh-dest-key-path` opcją . Jeśli para kluczy SSH już istnieje i jest używana opcja, nowa para kluczy nie zostanie wygenerowana, ale zamiast tego zostanie użyta  `--generate-ssh-keys` istniejąca para kluczy. W poniższym poleceniu zastąp *wartości VMname* i *RGname* własnymi wartościami:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH podczas wdrażania maszyny wirtualnej

Aby utworzyć maszynę wirtualną z systemem Linux używającą kluczy SSH do uwierzytelniania, określ klucz publiczny SSH podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, szablonów Azure Resource Manager lub innych metod:

* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](quick-create-portal.md)
* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
* [Tworzenie maszyny wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md)

Jeśli nie znasz formatu klucza publicznego SSH, możesz wyświetlić klucz publiczny za pomocą następującego polecenia, zastępując element ścieżką i nazwą pliku własnego klucza publicznego w razie `cat` `~/.ssh/id_rsa.pub` potrzeby:

```bash
cat ~/.ssh/id_rsa.pub
```

Typowa wartość klucza publicznego wygląda następująco:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

W przypadku kopiowania i wklejania zawartości pliku klucza publicznego do użycia w szablonie Azure Portal lub szablonie Resource Manager upewnij się, że nie kopiowane są żadne białe znaki końcowe. Aby skopiować klucz publiczny w systemie macOS, możesz potokować plik klucza publicznego do pliku `pbcopy` . Podobnie w systemie Linux plik klucza publicznego można potokować do programów, takich jak `xclip` .

Klucz publiczny umieszczany na maszynie wirtualnej z systemem Linux na platformie Azure jest domyślnie przechowywany w pliku ~/.ssh/id_rsa.pub, chyba że podczas tworzenia pary kluczy określono inną lokalizację. Aby użyć interfejsu wiersza polecenia platformy [Azure 2.0](/cli/azure) do utworzenia maszyny wirtualnej z istniejącym kluczem publicznym, określ wartość i opcjonalnie lokalizację tego klucza publicznego przy użyciu polecenia [az vm create](/cli/azure/vm#az_vm_create) z `--ssh-key-values` opcją . W poniższym poleceniu zastąp wartości *myVM,* *myResourceGroup,* *UbuntuLTS,* *azureuser* i *mysshkey.pub* własnymi wartościami:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Jeśli chcesz używać wielu kluczy SSH z maszyną wirtualną, możesz wprowadzić je na liście rozdzielonych spacjami, na przykład w ten `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` sposób.


## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH

Klucz publiczny wdrożony na maszynie wirtualnej platformy Azure i klucz prywatny w systemie lokalnym, protokół SSH z maszyną wirtualną przy użyciu adresu IP lub nazwy DNS maszyny wirtualnej. W poniższym poleceniu zastąp wartości *azureuser* *i myvm.westus.cloudapp.azure.com* nazwą użytkownika administratora i w pełni kwalifikowaną nazwą domeny (lub adresem IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy określono hasło, wprowadź to hasło po wyświetleniu monitu podczas procesu logowania. Maszyna wirtualna zostanie dodana do pliku ~/.ssh/known_hosts i nie zostanie ponownie poproszony o połączenie, dopóki nie zmieni się klucz publiczny na maszynie wirtualnej platformy Azure lub nazwa serwera nie zostanie usunięta z pliku ~/.ssh/known_hosts.

Jeśli maszyna wirtualna korzysta z zasad dostępu just in time, musisz zażądać dostępu, zanim będzie można nawiązać połączenie z maszyną wirtualną. Aby uzyskać więcej informacji na temat zasad just in time, zobacz Manage virtual machine access using the just in time policy (Zarządzanie dostępem do maszyny wirtualnej przy użyciu zasad [just in time).](../../security-center/security-center-just-in-time.md)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z parami kluczy SSH, zobacz Szczegółowe kroki tworzenia par kluczy [SSH i zarządzania nimi.](create-ssh-keys-detailed.md)

* Jeśli masz problemy z połączeniami SSH z maszynami wirtualnmi platformy Azure, zobacz Troubleshoot SSH connections to an Azure Linux VM (Rozwiązywanie problemów z połączeniami SSH z maszyną [wirtualną platformy Azure z systemem Linux).](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection)