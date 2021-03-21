---
title: Łączenie z maszynami wirtualnymi z systemem Linux przy użyciu kluczy SSH
description: Dowiedz się, jak generować klucze SSH i używać ich z komputera z systemem Windows, aby nawiązać połączenie z maszyną wirtualną z systemem Linux na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: aeecdcddaeba385c6dc0773356bcd0e36231e1e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607470"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak używać kluczy SSH w systemie Windows na platformie Azure

Ten artykuł dotyczy użytkowników systemu Windows, którzy chcą [tworzyć](#create-an-ssh-key-pair) i używać kluczy *Secure Shell* (SSH) do [łączenia](#connect-to-your-vm) się z maszynami wirtualnymi z systemem Linux na platformie Azure. Możesz również [generować i przechowywać klucze SSH w Azure Portal,](../ssh-keys-portal.md) które będą używane podczas tworzenia maszyn wirtualnych w portalu.


Aby używać kluczy SSH z poziomu klienta z systemem Linux lub macOS, zobacz [szybkie kroki](mac-create-ssh-keys.md). Aby zapoznać się z bardziej szczegółowym omówieniem protokołu SSH, zobacz [szczegółowe instrukcje: tworzenie kluczy SSH i zarządzanie nimi na potrzeby uwierzytelniania na maszynie wirtualnej z systemem Linux na platformie Azure](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Omówienie protokołów SSH i kluczy

[SSH](https://www.ssh.com/ssh/) to protokół połączenia szyfrowanego, który umożliwia bezpieczne logowanie za pośrednictwem niezabezpieczonych połączeń. SSH to domyślny protokół połączeń dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure. Mimo że protokół SSH zapewnia szyfrowane połączenie, korzystanie z haseł przy użyciu protokołu SSH nadal pozostawia maszynę wirtualną zagrożoną atakami na rachunek. Zalecamy łączenie się z maszyną wirtualną za pośrednictwem protokołu SSH przy użyciu pary kluczy publiczny-prywatny, znanych także jako *klucze SSH*. 

Para kluczy publiczny-prywatny jest taka sama jak blokada na wierzchu drzwi. Blokada jest udostępniona **publicznie**, każda osoba mająca właściwy klucz może otworzyć drzwi. Klucz jest **prywatny** i ma tylko osoby, którym ufasz, ponieważ może służyć do odblokowania drzwi. 

- *Klucz publiczny* jest umieszczany na maszynie wirtualnej z systemem Linux podczas tworzenia maszyny wirtualnej. 

- *Klucz prywatny* pozostaje w systemie lokalnym. Klucz prywatny należy chronić. Nie należy go udostępniać.

Po nawiązaniu połączenia z maszyną wirtualną z systemem Linux maszyna wirtualna testuje klienta SSH, aby upewnić się, że ma prawidłowy klucz prywatny. Jeśli klient ma klucz prywatny, zostanie mu udzielony dostęp do maszyny wirtualnej. 

W zależności od zasad zabezpieczeń organizacji można użyć pojedynczej pary kluczy, aby uzyskać dostęp do wielu maszyn wirtualnych i usług platformy Azure. Dla każdej maszyny wirtualnej nie jest wymagana osobna para kluczy. 

Klucz publiczny może być współużytkowany z każdą osobą, ale tylko ty (lub Twoja lokalna infrastruktura zabezpieczeń) powinna mieć dostęp do Twojego klucza prywatnego.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>Klienci SSH

Najnowsze wersje systemu Windows 10 obejmują [polecenia klienta OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) do tworzenia i używania kluczy SSH oraz nawiązywania połączeń SSH przy użyciu programu PowerShell lub wiersza polecenia. Jest to najprostszy sposób tworzenia połączenia SSH z maszyną wirtualną z systemem Linux przy użyciu komputera systemu Windows. 

Możesz również użyć bash w [Azure Cloud Shell](../../cloud-shell/overview.md) , aby nawiązać połączenie z maszyną wirtualną. Możesz użyć Cloud Shell w [przeglądarce internetowej](https://shell.azure.com/bash), z [Azure Portal](https://portal.azure.com)lub jako terminalu w Visual Studio Code przy użyciu [rozszerzenia konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

Możesz również zainstalować [podsystem Windows dla systemu Linux](/windows/wsl/about) , aby nawiązać połączenie z maszyną wirtualną za pośrednictwem protokołu SSH, i użyć innych natywnych narzędzi systemu Linux w ramach powłoki bash.

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Utwórz parę kluczy SSH przy użyciu `ssh-keygen` polecenia. Wprowadź nazwę pliku lub Użyj domyślnej podanej w nawiasie (na przykład `C:\Users\username/.ssh/id_rsa` ).  Wprowadź hasło do pliku lub pozostaw puste hasło, jeśli nie chcesz używać hasła. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Tworzenie maszyny wirtualnej przy użyciu klucza

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, Podaj klucz publiczny SSH podczas tworzenia maszyny wirtualnej.

Przy użyciu interfejsu wiersza polecenia platformy Azure należy określić ścieżkę i nazwę pliku klucza publicznego przy użyciu `az vm create` i `--ssh-key-value` parametru.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Przy użyciu programu PowerShell należy użyć polecenia `New-AzVM` i dodać klucz SSH do konfiguracji maszyny wirtualnej za pomocą polecenia ". Aby zapoznać się z przykładem, zobacz [Szybki Start: Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu programu PowerShell](quick-create-powershell.md).

W przypadku wielu wdrożeń przy użyciu portalu możesz chcieć przekazać swój klucz publiczny do platformy Azure, który można łatwo wybrać podczas tworzenia maszyny wirtualnej z poziomu portalu. Aby uzyskać więcej informacji, zobacz [przekazywanie klucza SSH](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Z kluczem publicznym wdrożonym na maszynie wirtualnej platformy Azure i kluczem prywatnym w systemie lokalnym, SSH z maszyną wirtualną przy użyciu adresu IP lub nazwy DNS maszyny wirtualnej. Zastąp wartość *azureuser* i *10.111.12.123* w poniższym poleceniu nazwą użytkownika administratora, adresem IP (lub w pełni kwalifikowaną nazwą domeny) i ścieżką do klucza prywatnego:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Jeśli hasło zostało skonfigurowane podczas tworzenia pary kluczy, wprowadź hasło po wyświetleniu monitu.

Jeśli maszyna wirtualna korzysta z zasad dostępu just in Time, przed nawiązaniem połączenia z maszyną wirtualną należy zażądać dostępu. Aby uzyskać więcej informacji na temat zasad just in Time, zobacz [Zarządzanie dostępem do maszyn wirtualnych przy użyciu zasad just in Time](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o kluczach SSH w Azure Portal, zobacz [generowanie i przechowywanie kluczy SSH w Azure Portal](../ssh-keys-portal.md) do użycia podczas tworzenia maszyn wirtualnych w portalu.

- Aby uzyskać szczegółowe instrukcje, opcje i zaawansowane Przykłady pracy z kluczami SSH, zobacz [szczegółowe instrukcje dotyczące tworzenia par kluczy SSH](create-ssh-keys-detailed.md).

- Możesz również użyć programu PowerShell w Azure Cloud Shell, aby generować klucze SSH i nawiązać połączenia SSH z maszynami wirtualnymi z systemem Linux. Zobacz [Przewodnik Szybki Start dla programu PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

- Jeśli masz problemy z używaniem protokołu SSH do łączenia się z maszynami wirtualnymi z systemem Linux, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)