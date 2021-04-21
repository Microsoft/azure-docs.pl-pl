---
title: Nawiązywanie połączenia z maszynami wirtualnych z systemem Linux przy użyciu kluczy SSH
description: Dowiedz się, jak generować klucze SSH z komputera z systemem Windows i używać ich do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 0199a47b2306d7d461ba61057c7ab1015015df08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835564"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak używać kluczy SSH w systemie Windows na platformie Azure

Ten artykuł dotyczy użytkowników systemu [](#create-an-ssh-key-pair) Windows, którzy chcą tworzyć [](#connect-to-your-vm) klucze *Secure Shell* (SSH) i używać ich do łączenia się z maszynami wirtualnymi z systemem Linux na platformie Azure. Klucze [SSH można również generować](../ssh-keys-portal.md) i przechowywać w Azure Portal do użycia podczas tworzenia maszyn wirtualnych w portalu.


Aby użyć kluczy SSH z klienta systemu Linux lub macOS, zobacz [szybkie kroki](mac-create-ssh-keys.md). Aby uzyskać bardziej szczegółowe omówienie protokołu SSH, zobacz Szczegółowe kroki: Tworzenie kluczy SSH i zarządzanie nimi w celu uwierzytelniania na maszynie wirtualnej [z systemem Linux na platformie Azure.](create-ssh-keys-detailed.md)

## <a name="overview-of-ssh-and-keys"></a>Omówienie SSH i kluczy

[SSH](https://www.ssh.com/ssh/) to szyfrowany protokół połączenia, który umożliwia bezpieczne logowanie za pośrednictwem niezabezpieczonych połączeń. SSH to domyślny protokół połączenia dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure. Mimo że sam SSH zapewnia szyfrowane połączenie, używanie haseł z połączeniem SSH nadal pozostawia maszynę wirtualną na ataki siłowe. Zalecamy nawiązywanie połączenia z maszyną wirtualną za pośrednictwem połączenia SSH przy użyciu pary kluczy publiczny-prywatny, nazywanej również *kluczami SSH.* 

Para kluczy publiczny-prywatny przypomina blokadę w drzwiach wejściowych. Blokada jest ujawniona **publicznie ,** każda osoba mająca właściwy klucz może otworzyć drzwi. Klucz jest prywatny **i** jest nadany tylko zaufanym osobom, ponieważ może służyć do odblokowania drzwi. 

- Klucz *publiczny jest umieszczany* na maszynie wirtualnej z systemem Linux podczas jej tworzenia. 

- Klucz *prywatny pozostaje* w systemie lokalnym. Klucz prywatny należy chronić. Nie należy go udostępniać.

Podczas nawiązywania połączenia z maszyną wirtualną z systemem Linux maszyna wirtualna testuje klienta SSH, aby upewnić się, że ma prawidłowy klucz prywatny. Jeśli klient ma klucz prywatny, uzyskuje dostęp do maszyny wirtualnej. 

W zależności od zasad zabezpieczeń organizacji można ponownie użyć jednej pary kluczy, aby uzyskać dostęp do wielu maszyn wirtualnych i usług platformy Azure. Nie potrzebujesz oddzielnej pary kluczy dla każdej maszyny wirtualnej. 

Klucz publiczny może być udostępniony wszystkim osobom, ale tylko Ty (lub lokalna infrastruktura zabezpieczeń) powinien mieć dostęp do Twojego klucza prywatnego.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>Klienci SSH

Najnowsze wersje programu Windows 10 zawierają polecenia klienta [OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) służące do tworzenia i używania kluczy SSH oraz tworzenia połączeń SSH z programu PowerShell lub wiersza polecenia. Jest to najprostszy sposób tworzenia połączenia SSH z maszyną wirtualną z systemem Linux z komputera z systemem Windows. 

Możesz również użyć powłoki Bash w [Azure Cloud Shell,](../../cloud-shell/overview.md) aby nawiązać połączenie z maszyną wirtualną. Możesz użyć Cloud Shell w przeglądarce [internetowej](https://shell.azure.com/bash), z witryny [Azure Portal](https://portal.azure.com)lub jako terminalu w usłudze Visual Studio Code przy użyciu rozszerzenia konta [platformy Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

Możesz również zainstalować [](/windows/wsl/about) pakiet Podsystem Windows dla systemu Linux nawiązać połączenie z maszyną wirtualną za pośrednictwem połączenia SSH i użyć innych natywnych narzędzi systemu Linux w powłoce Bash.

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Utwórz parę kluczy SSH za pomocą `ssh-keygen` polecenia . Wprowadź nazwę pliku lub użyj wartości domyślnej wyświetlanej w nawiasach (na przykład `C:\Users\username/.ssh/id_rsa` ).  Wprowadź hasło dla pliku lub pozostaw to hasło puste, jeśli nie chcesz używać hasła. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Tworzenie maszyny wirtualnej przy użyciu klucza

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, podaj klucz publiczny SSH podczas tworzenia maszyny wirtualnej.

Za pomocą interfejsu wiersza polecenia platformy Azure można określić ścieżkę i nazwę pliku klucza publicznego przy użyciu parametrów `az vm create` `--ssh-key-value` i .

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

W programie PowerShell użyj polecenia `New-AzVM` i dodaj klucz SSH do konfiguracji maszyny wirtualnej przy użyciu polecenia ". Aby uzyskać przykład, zobacz Szybki start: tworzenie maszyny wirtualnej z systemem [Linux na platformie Azure przy użyciu programu PowerShell.](quick-create-powershell.md)

Jeśli korzystasz z portalu do wielu wdrożeń, możesz przekazać klucz publiczny na platformę Azure, gdzie można go łatwo wybrać podczas tworzenia maszyny wirtualnej z portalu. Aby uzyskać więcej informacji, [zobacz Przekazywanie klucza SSH.](../ssh-keys-portal.md#upload-an-ssh-key)


## <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Klucz publiczny wdrożony na maszynie wirtualnej platformy Azure i klucz prywatny w systemie lokalnym, protokół SSH z maszyną wirtualną przy użyciu adresu IP lub nazwy DNS maszyny wirtualnej. Zastąp elementy *azureuser* i *10.111.12.123* następującym poleceniem nazwą użytkownika administratora, adresem IP (lub w pełni kwalifikowaną nazwą domeny) i ścieżką do klucza prywatnego:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Jeśli hasło skonfigurowano podczas tworzenia pary kluczy, po wyświetleniu monitu wprowadź hasło.

Jeśli maszyna wirtualna korzysta z zasad dostępu just in time, musisz zażądać dostępu, zanim będzie można nawiązać połączenie z maszyną wirtualną. Aby uzyskać więcej informacji na temat zasad just in time, zobacz Manage virtual machine access using the just in time policy (Zarządzanie dostępem do maszyny wirtualnej przy użyciu zasad [just in time).](../../security-center/security-center-just-in-time.md)


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o kluczach SSH w Azure Portal, zobacz Generowanie i przechowywanie kluczy [SSH](../ssh-keys-portal.md) w Azure Portal do użycia podczas tworzenia maszyn wirtualnych w portalu.

- Aby uzyskać szczegółowe instrukcje, opcje i zaawansowane przykłady pracy z kluczami SSH, zobacz Szczegółowe kroki tworzenia par [kluczy SSH.](create-ssh-keys-detailed.md)

- Program PowerShell w programie Azure Cloud Shell umożliwia również generowanie kluczy SSH i połączenia SSH z maszynami wirtualnych z systemem Linux. Zobacz Przewodnik [Szybki start programu PowerShell.](../../cloud-shell/quickstart-powershell.md#ssh)

- Jeśli masz trudności z nawiązywaniem połączenia za pomocą połączenia SSH z maszynami wirtualnmi z systemem Linux, zobacz Troubleshoot SSH connections to an Azure Linux VM (Rozwiązywanie problemów z połączeniami SSH z maszyną [wirtualną platformy Azure z systemem Linux).](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
