---
title: Używanie kluczy SSH w systemie Windows dla maszyn wirtualnych z systemem Linux
description: Dowiedz się, jak generować i używać kluczy SSH na komputerze z systemem Windows, aby połączyć się z maszyną wirtualną systemu Linux na platformie Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: cdf901ca56c150cfed6ba3d462ce493d40bd2488
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757989"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak używać kluczy SSH z systemem Windows na platformie Azure

W tym artykule opisano sposoby generowania i używania kluczy *bezpiecznej powłoki* (SSH) na komputerze z systemem Windows w celu utworzenia maszyny wirtualnej systemu Linux (VM) na platformie Azure i nawiązania połączenia z nią. Aby użyć kluczy SSH z klienta systemu Linux lub macOS, zapoznaj się z [szybkimi](mac-create-ssh-keys.md) lub [szczegółowymi](create-ssh-keys-detailed.md) wskazówkami.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pakiety windows i klienci SSH
Łączysz się z maszynami wirtualnymi z systemem Linux i zarządzasz nimi na platformie Azure przy użyciu *klienta SSH*. Komputery z systemem Linux lub macOS zwykle mają zestaw poleceń SSH do generowania i zarządzania kluczami SSH i do nawiązywać połączenia SSH. 

Komputery z systemem Windows nie zawsze mają zainstalowane porównywalne polecenia SSH. Najnowsze wersje systemu Windows 10 zapewniają [polecenia klienta OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) do tworzenia kluczy SSH i zarządzania nimi oraz nawiązywać połączenia SSH z wiersza polecenia. Najnowsze wersje systemu Windows 10 zawierają również [podsystem windows dla systemu Linux](https://docs.microsoft.com/windows/wsl/about) do uruchamiania i uzyskiwania dostępu do narzędzi, takich jak klient SSH natywnie w powłoce Bash. 

Inne typowe klientów SSH systemu Windows, które można zainstalować lokalnie, są zawarte w następujących pakietach:

* [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git dla systemu Windows](https://git-for-windows.github.io/)
* [MobaXterm (MobaXterm)](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Można również użyć narzędzi SSH dostępnych w bash w [usłudze Azure Cloud Shell.](../../cloud-shell/overview.md) 

* Uzyskaj dostęp do usługi [https://shell.azure.com](https://shell.azure.com) Cloud Shell w przeglądarce internetowej w [witrynie Azure lub](https://portal.azure.com)w portalu Azure. 
* Uzyskaj dostęp do usługi Cloud Shell jako terminal z poziomu kodu programu Visual Studio, instalując [rozszerzenie konta platformy Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH
W poniższych sekcjach opisano dwie opcje tworzenia pary kluczy SSH w systemie Windows. Można użyć polecenia powłoki (`ssh-keygen`) lub narzędzia GUI (PuTTYgen). Należy również pamiętać, że podczas tworzenia klucza za pomocą programu Powershell należy przekazać klucz publiczny w formacie ssh.com(SECSH). W przypadku korzystania z interfejsu wiersza polecenia przekonwertuj klucz na format OpenSSH przed przesłaniem. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Tworzenie klawiszy SSH za pomocą ssh-keygen

Jeśli uruchomisz powłokę poleceń w systemie Windows, która obsługuje narzędzia klienckie SSH (lub używasz usługi Azure Cloud Shell), utwórz parę kluczy SSH za pomocą `ssh-keygen` polecenia. Wpisz następujące polecenie i odpowiedz na monity. Jeśli para kluczy SSH istnieje w wybranej lokalizacji, pliki te są zastępowane. 

```bash
ssh-keygen -t rsa -b 2048
```

Aby uzyskać więcej informacji na temat tła i informacji, zobacz [szybkie](mac-create-ssh-keys.md) lub [szczegółowe](create-ssh-keys-detailed.md) kroki tworzenia klawiszy SSH przy użyciu `ssh-keygen`programu .

### <a name="create-ssh-keys-with-puttygen"></a>Tworzenie klawiszy SSH za pomocą PuTTYgen

Jeśli wolisz używać narzędzia opartego na gui do tworzenia kluczy SSH, możesz użyć generatora kluczy PuTTYgen, dołączonego do [pakietu pobierania PuTTY.](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 

Aby utworzyć parę kluczy SSH RSA z puttygen:

1. Uruchom puttygen.

2. Kliknij pozycję **Generate** (Generuj). Domyślnie PuTTYgen generuje 2048-bitowy klucz RSA SSH-2.

4. Poruszaj myszą w pustym obszarze, aby zapewnić losowość klawisza.

5. Po wygenerowaniu klucza publicznego opcjonalnie wprowadź i potwierdź hasło. Podczas uwierzytelniania na maszynie wirtualnej za pomocą prywatnego klucza SSH zostanie wyświetlony monit o podanie hasła. Bez hasła, jeśli ktoś uzyskuje klucz prywatny, mogą zalogować się do dowolnej maszyny Wirtualnej lub usługi, która używa tego klucza. Zalecamy utworzenie hasła. Jeśli zapomnisz hasła, jego odzyskanie nie będzie możliwe.

6. Klucz publiczny jest wyświetlany w górnej części okna. Możesz skopiować ten cały klucz publiczny, a następnie wkleić go do witryny Azure portal lub szablonu usługi Azure Resource Manager podczas tworzenia maszyny Wirtualnej z systemem Linux. Można również wybrać **pozycję Zapisz klucz publiczny,** aby zapisać kopię na komputerze:

    ![Zapisz plik klucza publicznego PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Opcjonalnie, aby zapisać klucz prywatny w formacie klucza prywatnego PuTTy (plik ppk), wybierz **zapisz klucz prywatny**. Plik .ppk będzie potrzebny później, aby użyć PuTTY do nawiązywać połączenie SSH z maszyną wirtualną.

    ![Zapisz plik klucza prywatnego PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Jeśli chcesz zapisać klucz prywatny w formacie OpenSSH, formacie klucza prywatnego używanym przez wielu klientów SSH, wybierz opcję **Konwersje** > **Eksportuj klucz OpenSSH**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH podczas wdrażania maszyny Wirtualnej

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, podaj klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu witryny Azure portal lub innych metod.

W poniższym przykładzie pokazano, jak można skopiować i wkleić ten klucz publiczny do witryny Azure portal podczas tworzenia maszyny Wirtualnej systemu Linux. Klucz publiczny jest zazwyczaj następnie przechowywany w katalogu ~/.ssh/authorized_key na nowej maszynie wirtualnej.

   ![Używanie klucza publicznego podczas tworzenia maszyny Wirtualnej w witrynie Azure portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Jednym ze sposobów nawiązywać połączenie SSH z maszyną wirtualną systemu Linux z systemu Windows jest użycie klienta SSH. Jest to preferowana metoda, jeśli masz klienta SSH zainstalowany w systemie Windows lub jeśli używasz narzędzi SSH w Bash w usłudze Azure Cloud Shell. Jeśli wolisz narzędzie oparte na graficznym interfejsie graficznym, możesz połączyć się z PuTTY.  

### <a name="use-an-ssh-client"></a>Korzystanie z klienta SSH
Po wdrożeniu klucza publicznego na maszynie wirtualnej platformy Azure i klucza prywatnego w systemie lokalnym ssh do maszyny wirtualnej przy użyciu adresu IP lub nazwy DNS maszyny Wirtualnej. Zastąp *użytkownika platformy Azure* i *myvm.westus.cloudapp.azure.com* w następującym poleceniu nazwą użytkownika administratora i w pełni kwalifikowaną nazwą domeny (lub adresem IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy skonfigurowano hasło, wprowadź hasło po wyświetleniu monitu podczas procesu logowania.

Jeśli maszyna wirtualna używa zasad dostępu just-in-time, należy zażądać dostępu, zanim będzie można połączyć się z maszyną wirtualną. Aby uzyskać więcej informacji na temat zasad just-in-time, zobacz [Zarządzanie dostępem do maszyny wirtualnej przy użyciu zasad just in time](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Połącz się z putty

Jeśli zainstalowano [pakiet pobierania PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) i wcześniej wygenerowano plik klucza prywatnego PuTTY (.ppk), możesz połączyć się z maszyną wirtualną z systemem Linux z PuTTY.

1. Uruchom PuTTy.

2. Wypełnij nazwę hosta lub adres IP maszyny Wirtualnej z witryny Azure portal:

    ![Otwórz nowe połączenie PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Wybierz kategorię**SSH** > **Auth** **połączenia.** >  Przejdź do swojego klucza prywatnego PuTTY (plik ppk):

    ![Wybierz klucz prywatny PuTTY do uwierzytelniania](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kliknij **przycisk Otwórz,** aby połączyć się z maszyną wirtualną.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe kroki, opcje i zaawansowane przykłady pracy z kluczami SSH, zobacz [Szczegółowe kroki tworzenia par kluczy SSH](create-ssh-keys-detailed.md).

* Za pomocą programu PowerShell w usłudze Azure Cloud Shell można również generować klucze SSH i nawiązywać połączenia SSH z maszynami wirtualnymi z systemem Linux. Zobacz [przewodnik szybki start programu PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Jeśli masz trudności z używaniem SSH do łączenia się z maszynami wirtualnymi z systemem Linux, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną systemu Azure Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
