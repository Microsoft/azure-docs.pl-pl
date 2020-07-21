---
title: Używanie kluczy SSH w systemie Windows dla maszyn wirtualnych z systemem Linux
description: Informacje na temat generowania i używania kluczy SSH na komputerze z systemem Windows w celu nawiązania połączenia z maszyną wirtualną z systemem Linux na platformie Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 4fc411d3833fcd47477e7e0fe11c6da9e2114143
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502031"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak używać kluczy SSH w systemie Windows na platformie Azure

W tym artykule opisano sposób generowania i używania kluczy *Secure Shell* (SSH) na komputerze z systemem Windows w celu utworzenia maszyny wirtualnej z systemem Linux na platformie Azure i nawiązania z nią połączenia. Aby używać kluczy SSH z poziomu klienta z systemem Linux lub macOS, zobacz [szybkie](mac-create-ssh-keys.md) lub [szczegółowe](create-ssh-keys-detailed.md) wskazówki.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pakiety systemu Windows i klienci SSH
Za pomocą *klienta SSH*można nawiązać połączenie z maszynami wirtualnymi z systemem Linux i zarządzać nimi na platformie Azure. Komputery z systemem Linux lub macOS zazwyczaj mają pakiet poleceń SSH do generowania kluczy SSH i zarządzania nimi oraz do nawiązywania połączeń SSH. 

Na komputerach z systemem Windows nie zawsze są zainstalowane porównywalne polecenia SSH. Najnowsze wersje systemu Windows 10 zapewniają [polecenia klienta OpenSSH](https://devblogs.microsoft.com/commandline/windows10v1803/) do tworzenia kluczy SSH i zarządzania nimi oraz nawiązywania połączeń SSH z wiersza polecenia. Najnowsze wersje systemu Windows 10 obejmują również [podsystem Windows dla systemu Linux](/windows/wsl/about) do uruchamiania i uzyskiwania dostępu do narzędzi, takich jak klient SSH natywnie w ramach powłoki bash. 

Inne typowe klienckie aplikacje SSH systemu Windows, które można zainstalować lokalnie, znajdują się w następujących pakietach:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git dla systemu Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

W [Azure Cloud Shell](../../cloud-shell/overview.md)można również użyć narzędzi SSH dostępnych w bash. 

* Dostęp do Cloud Shell w przeglądarce internetowej znajduje się w [https://shell.azure.com](https://shell.azure.com) [Azure Portal](https://portal.azure.com). 
* Dostęp do Cloud Shell jako terminalu z poziomu Visual Studio Code przez zainstalowanie [rozszerzenia konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH
W poniższych sekcjach opisano dwie opcje tworzenia pary kluczy SSH w systemie Windows. Można użyć polecenia powłoki ( `ssh-keygen` ) lub graficznego interfejsu użytkownika (PuTTYgen). Należy również pamiętać, że podczas tworzenia klucza przy użyciu programu PowerShell należy przekazać klucz publiczny jako format SSH. com (SECSH). Podczas korzystania z interfejsu wiersza polecenia Konwertuj klucz na format OpenSSH przed przekazaniem. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Tworzenie kluczy SSH przy użyciu protokołu SSH-Keygen

Jeśli uruchamiasz powłokę poleceń w systemie Windows, która obsługuje narzędzia klienta SSH (lub używasz Azure Cloud Shell), Utwórz parę kluczy SSH za pomocą `ssh-keygen` polecenia. Wpisz następujące polecenie i Odpowiedz na nie. Jeśli para kluczy SSH istnieje w wybranej lokalizacji, te pliki są zastępowane. 

```bash
ssh-keygen -t rsa -b 2048
```

Aby uzyskać więcej informacji, zobacz sekcję [szybkie](mac-create-ssh-keys.md) lub [szczegółowe](create-ssh-keys-detailed.md) instrukcje tworzenia kluczy SSH przy użyciu programu `ssh-keygen` .

### <a name="create-ssh-keys-with-puttygen"></a>Utwórz klucze SSH za pomocą PuTTYgen

Jeśli wolisz używać narzędzia opartego na graficznym interfejsie użytkownika do tworzenia kluczy SSH, możesz użyć generatora kluczy PuTTYgen dołączonego do [pobranego pakietu](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Aby utworzyć parę kluczy SSH RSA z PuTTYgen:

1. Uruchom PuTTYgen.

2. Kliknij pozycję **Generate** (Generuj). Domyślnie PuTTYgen generuje klucz RSA o 2048-bitowym SSH-2.

3. Przesuń wskaźnik myszy w pusty obszar, aby zapewnić losowość klucza.

4. Po wygenerowaniu klucza publicznego opcjonalnie wprowadź i Potwierdź hasło. Po uwierzytelnieniu na maszynie wirtualnej przy użyciu prywatnego klucza SSH zostanie wyświetlony monit o podanie hasła. Bez hasła, jeśli ktoś uzyska klucz prywatny, może zalogować się do dowolnej maszyny wirtualnej lub usługi, która korzysta z tego klucza. Zalecamy utworzenie hasła. Jeśli zapomnisz hasła, jego odzyskanie nie będzie możliwe.

5. Klucz publiczny jest wyświetlany w górnej części okna. Możesz skopiować ten cały klucz publiczny, a następnie wkleić go do Azure Portal lub do szablonu Azure Resource Manager podczas tworzenia maszyny wirtualnej z systemem Linux. Możesz również wybrać opcję **Zapisz klucz publiczny** , aby zapisać kopię na komputerze. Należy pamiętać, że podczas zapisywania do pliku, program poformatuje konwersję klucza publicznego do innego formatu, [RFC4716](https://tools.ietf.org/html/rfc4716). Format RFC4716 może być niezgodny ze wszystkimi interfejsami API. Dlatego w celu użycia w Azure Portal zaleca się skopiowanie klucza publicznego, który jest wyświetlany w oknie.

    ![Zapisz plik z kluczem publicznym](./media/ssh-from-windows/save-public-key.png)

6. Opcjonalnie, aby zapisać klucz prywatny w formacie formatu klucza prywatnego (plik. PPK), wybierz pozycję **Zapisz klucz prywatny**. Plik. ppk będzie potrzebny później, aby można było użyć wyrażenia w celu nawiązania połączenia SSH z maszyną wirtualną.

    ![Zapisz plik z kluczem prywatnym](./media/ssh-from-windows/save-ppk-file.png)

    Jeśli chcesz zapisać klucz prywatny w formacie OpenSSH, format klucza prywatnego używany przez wielu klientów SSH, wybierz pozycję **konwersje**  >  **Eksportuj klucz OpenSSH**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH podczas wdrażania maszyny wirtualnej

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, Podaj klucz publiczny SSH podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal lub innych metod.

Poniższy przykład pokazuje, jak skopiować i wkleić ten klucz publiczny do Azure Portal podczas tworzenia maszyny wirtualnej z systemem Linux. Klucz publiczny zazwyczaj jest przechowywany w katalogu ~/.SSH/authorized_key na nowej maszynie wirtualnej.

   ![Użyj klucza publicznego podczas tworzenia maszyny wirtualnej w Azure Portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Jednym ze sposobów nawiązania połączenia SSH z maszyną wirtualną z systemem Linux z poziomu systemu Windows jest użycie klienta SSH. Jest to preferowana metoda, jeśli w systemie Windows jest zainstalowany klient SSH lub jeśli używasz narzędzi SSH w bash w Azure Cloud Shell. Jeśli wolisz narzędzia opartego na graficznym interfejsie użytkownika, możesz nawiązać połączenie za pomocą programu.  

### <a name="use-an-ssh-client"></a>Korzystanie z klienta SSH
Z kluczem publicznym wdrożonym na maszynie wirtualnej platformy Azure i kluczem prywatnym w systemie lokalnym, SSH z maszyną wirtualną przy użyciu adresu IP lub nazwy DNS maszyny wirtualnej. W poniższym poleceniu Zastąp *azureuser* i *MyVM.westus.cloudapp.Azure.com* nazwą użytkownika administratora oraz w pełni kwalifikowaną nazwą domeny (lub adresem IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli hasło zostało skonfigurowane podczas tworzenia pary kluczy, należy wprowadzić hasło po wyświetleniu monitu podczas procesu logowania.

Jeśli maszyna wirtualna korzysta z zasad dostępu just in Time, przed nawiązaniem połączenia z maszyną wirtualną należy zażądać dostępu. Aby uzyskać więcej informacji na temat zasad just in Time, zobacz [Zarządzanie dostępem do maszyn wirtualnych przy użyciu zasad just in Time](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Połącz za pomocą

Jeśli zainstalowano [pakiet pobierania pobrany](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) i wcześniej wygenerował plik w postaci pliku klucza prywatnego (. PPK), można nawiązać połączenie z maszyną wirtualną z systemem Linux przy użyciu systemu.

1. Rozpocznij.

2. Wprowadź nazwę hosta lub adres IP maszyny wirtualnej z Azure Portal:

    ![Otwórz nowe połączenie.](./media/ssh-from-windows/putty-new-connection.png)

3. Wybierz kategorię **połączenie**  >  **SSH**połączenia  >  **Auth** . Wyszukaj i wybierz swój klucz prywatny (plik. PPK):

    ![Wybierz swój klucz prywatny do uwierzytelnienia](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kliknij przycisk **Otwórz** , aby nawiązać połączenie z maszyną wirtualną.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe instrukcje, opcje i zaawansowane Przykłady pracy z kluczami SSH, zobacz [szczegółowe instrukcje dotyczące tworzenia par kluczy SSH](create-ssh-keys-detailed.md).

* Możesz również użyć programu PowerShell w Azure Cloud Shell, aby generować klucze SSH i nawiązać połączenia SSH z maszynami wirtualnymi z systemem Linux. Zobacz [Przewodnik Szybki Start dla programu PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Jeśli masz problemy z używaniem protokołu SSH do łączenia się z maszynami wirtualnymi z systemem Linux, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)
