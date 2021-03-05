---
title: Szczegółowe instrukcje dotyczące tworzenia pary kluczy SSH
description: Zapoznaj się ze szczegółowymi instrukcjami tworzenia pary kluczy publicznych i prywatnych SSH dla maszyn wirtualnych z systemem Linux na platformie Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 39db1aec06c16a2d44c93bea7785931f8de8ca9e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182472"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Szczegółowe kroki: tworzenie kluczy SSH i zarządzanie nimi na potrzeby uwierzytelniania na maszynie wirtualnej z systemem Linux na platformie Azure

Za pomocą pary kluczy Secure Shell (SSH) można utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania. W tym artykule pokazano, jak utworzyć i używać pary plików prywatnego publicznego klucza SSH RSA dla połączeń klienta SSH.

Jeśli chcesz użyć szybkich poleceń, zobacz [jak utworzyć parę kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](mac-create-ssh-keys.md).

Aby utworzyć klucze SSH i używać ich do łączenia się z komputerem z **systemem Windows** , zobacz [jak używać kluczy SSH w systemie Windows na platformie Azure](ssh-from-windows.md). Za pomocą [Azure Portal](../ssh-keys-portal.md) można także tworzyć klucze SSH do tworzenia maszyn wirtualnych w portalu i zarządzać nimi.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Korzystanie z kluczy SSH i ich zalety

Podczas tworzenia maszyny wirtualnej platformy Azure przez określenie klucza publicznego platforma Azure kopiuje klucz publiczny (w `.pub` formacie) do `~/.ssh/authorized_keys` folderu na maszynie wirtualnej. Klucze SSH w programie służą `~/.ssh/authorized_keys` do wyzwania klienta w celu dopasowania go do odpowiedniego klucza prywatnego połączenia SSH. Na maszynie wirtualnej platformy Azure z systemem Linux, która używa kluczy SSH do uwierzytelniania, platforma Azure konfiguruje serwer SSHD w taki sposób, aby nie zezwalał na logowanie przy użyciu hasła, tylko kluczy SSH. Tworząc maszynę wirtualną z systemem Linux na platformie Azure przy użyciu kluczy SSH, możesz zabezpieczyć wdrożenie maszyny wirtualnej i zapisać w nim typowy krok konfiguracji po wdrożeniu, aby wyłączyć hasła w `sshd_config` pliku.

Jeśli nie chcesz używać kluczy SSH, możesz skonfigurować maszynę wirtualną z systemem Linux do korzystania z uwierzytelniania przy użyciu hasła. Jeśli maszyna wirtualna nie jest dostępna w Internecie, korzystanie z haseł może być wystarczające. Jednak nadal trzeba zarządzać hasłami dla każdej maszyny wirtualnej z systemem Linux oraz zachować prawidłowe zasady i praktyki haseł, takie jak minimalna długość hasła i regularne aktualizacje. 

## <a name="generate-keys-with-ssh-keygen"></a>Generuj klucze przy użyciu protokołu SSH-Keygen

Aby utworzyć klucze, preferowane polecenie jest `ssh-keygen` dostępne w przypadku narzędzi OpenSSH w Azure Cloud Shell, na hoście macOS lub Linux oraz w systemie Windows 10. `ssh-keygen` pyta o szereg pytań, a następnie zapisuje klucz prywatny i pasujący klucz publiczny. 

Klucze SSH są domyślnie przechowywane w katalogu `~/.ssh`.  Jeśli nie masz katalogu `~/.ssh`, polecenie `ssh-keygen` tworzy ten katalog z odpowiednimi uprawnieniami.

### <a name="basic-example"></a>Przykład podstawowy

Następujące `ssh-keygen` polecenie generuje pliki o 4096-bitowym publicznym i prywatnym kluczu SSH, domyślnie w `~/.ssh` katalogu. Jeśli para kluczy SSH istnieje w bieżącej lokalizacji, te pliki są zastępowane.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Szczegółowy przykład
Poniższy przykład przedstawia dodatkowe opcje polecenia, aby utworzyć parę kluczy SSH RSA. Jeśli para kluczy SSH istnieje w bieżącej lokalizacji, te pliki są zastępowane. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Opis polecenia**

`ssh-keygen` — program używany do tworzenia kluczy

`-m PEM` = Sformatuj klucz jako PEM

`-t rsa` = Typ klucza do utworzenia, w tym przypadku w formacie RSA

`-b 4096` = Liczba bitów w kluczu, w tym przypadku 4096

`-C "azureuser@myserver"` — komentarz dodany na końcu pliku klucza publicznego, aby umożliwić jego łatwą identyfikację. Zwykle jako komentarz używany jest adres e-mail, ale użycie dowolnego z nich najlepiej sprawdza się w przypadku Twojej infrastruktury.

`-f ~/.ssh/mykeys/myprivatekey` = Nazwa pliku klucza prywatnego, jeśli nie zostanie użyta nazwa domyślna. Odpowiedni plik klucza publicznego dołączony do programu `.pub` jest generowany w tym samym katalogu. Ten katalog musi istnieć.

`-N mypassphrase` = dodatkowe hasło używane do uzyskiwania dostępu do pliku klucza prywatnego. 

### <a name="example-of-ssh-keygen"></a>Przykład polecenia ssh-keygen

```bash
ssh-keygen -t rsa -m PEM -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Zapisane pliki kluczy

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Nazwa pary kluczy dla potrzeb tego artykułu. Istnienie pary kluczy o nazwie `id_rsa` jest ustawieniem domyślnym; niektóre narzędzia mogą oczekiwać `id_rsa` nazwy pliku klucza prywatnego, dzięki czemu jest to dobry pomysł. Katalog `~/.ssh/` jest domyślną lokalizacją par kluczy SSH oraz pliku konfiguracyjnego SSH. Jeśli nie określono pełnej ścieżki, polecenie `ssh-keygen` spowoduje utworzenie kluczy w bieżącym katalogu roboczym, a nie domyślnym katalogu `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista `~/.ssh` katalogów

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Hasło klucza

`Enter passphrase (empty for no passphrase):`

*Zdecydowanie* zaleca się dodanie hasła do klucza prywatnego. Bez hasła do ochrony pliku klucza każda osoba mająca plik może użyć go do zalogowania się na dowolnym serwerze, który ma odpowiadający mu klucz publiczny. Dodanie hasła oferuje większą ochronę w przypadku, gdy ktoś jest w stanie uzyskać dostęp do pliku klucza prywatnego, co pozwoli na przekazanie czasu na zmianę kluczy.

## <a name="generate-keys-automatically-during-deployment"></a>Generuj klucze automatycznie podczas wdrażania

Jeśli używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure) do utworzenia maszyny wirtualnej, możesz opcjonalnie wygenerować pliki publicznego i prywatnego klucza SSH, uruchamiając polecenie [AZ VM Create](/cli/azure/vm) z `--generate-ssh-keys` opcją. Klucze są przechowywane w katalogu ~/.SSH. Należy pamiętać, że ta opcja polecenia nie zastępuje kluczy, jeśli już istnieją w tej lokalizacji.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH podczas wdrażania maszyny wirtualnej

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, Podaj klucz publiczny SSH podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal, interfejsu wiersza polecenia, szablonów Menedżer zasobów lub innych metod. W przypadku korzystania z portalu należy wprowadzić sam klucz publiczny. Jeśli używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure) do utworzenia maszyny wirtualnej z istniejącym kluczem publicznym, określ wartość lub lokalizację tego klucza publicznego, uruchamiając polecenie [AZ VM Create](/cli/azure/vm) z `--ssh-key-value` opcją. 

Jeśli nie masz doświadczenia w formacie klucza publicznego SSH, możesz wyświetlić swój klucz publiczny, uruchamiając `cat` w następujący sposób `~/.ssh/id_rsa.pub` :

```bash
cat ~/.ssh/id_rsa.pub
```

Dane wyjściowe są podobne do następujących (tutaj redagowane):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Jeśli skopiujesz i wkleisz zawartość pliku klucza publicznego do Azure Portal lub szablonu Menedżer zasobów, upewnij się, że nie kopiujesz żadnych dodatkowych spacji ani nie wprowadzaj dodatkowych podziałów wierszy. Na przykład jeśli używasz macOS, możesz popotokować plik klucza publicznego (domyślnie `~/.ssh/id_rsa.pub` ) do **pbcopy** , aby skopiować zawartość (istnieją inne programy systemu Linux, takie jak `xclip` ).

Jeśli wolisz używać klucza publicznego, który jest w formacie wielowierszowym, można wygenerować RFC4716 sformatowany klucz w kontenerze PEM z utworzonego wcześniej klucza publicznego.

Aby utworzyć klucz w formacie RFC4716 przy użyciu istniejącego publicznego klucza SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH z maszyną wirtualną za pomocą klienta SSH
Z kluczem publicznym wdrożonym na maszynie wirtualnej platformy Azure i kluczem prywatnym w systemie lokalnym, SSH z maszyną wirtualną przy użyciu adresu IP lub nazwy DNS maszyny wirtualnej. W poniższym poleceniu Zastąp *azureuser* i *MyVM.westus.cloudapp.Azure.com* nazwą użytkownika administratora oraz w pełni kwalifikowaną nazwą domeny (lub adresem IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy podano hasło, wprowadź je po wyświetleniu monitu podczas procesu logowania. (Serwer zostanie dodany do folderu `~/.ssh/known_hosts` i monit o ponowne połączenie nie zostanie wyświetlony, dopóki klucz publiczny na maszynie wirtualnej nie ulegnie zmianie lub nazwa serwera nie zostanie usunięta z folderu `~/.ssh/known_hosts`).

Jeśli maszyna wirtualna korzysta z zasad dostępu just in Time, przed nawiązaniem połączenia z maszyną wirtualną należy zażądać dostępu. Aby uzyskać więcej informacji na temat zasad just in Time, zobacz [Zarządzanie dostępem do maszyn wirtualnych przy użyciu zasad just in Time](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Przechowywanie hasła klucza prywatnego przy użyciu agenta SSH-Agent

Aby uniknąć wpisywania hasła do pliku klucza prywatnego przy każdym logowaniu SSH, możesz użyć polecenia `ssh-agent` , aby buforować hasło pliku klucza prywatnego. Jeśli używasz komputera Mac, łańcuch kluczy macOS bezpiecznie przechowuje hasło klucza prywatnego po wywołaniu `ssh-agent` .

Sprawdź i Użyj `ssh-agent` i, `ssh-add` aby poinformować system SSH o plikach kluczy, aby nie trzeba było interaktywnie używać hasła.

```bash
eval "$(ssh-agent -s)"
```

Następnie dodaj klucz prywatny do programu `ssh-agent` przy użyciu polecenia `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Hasło klucza prywatnego jest teraz przechowywane w `ssh-agent` .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Użyj protokołu SSH-Copy-ID, aby skopiować klucz do istniejącej maszyny wirtualnej

Jeśli maszyna wirtualna została już utworzona, możesz dodać nowy klucz publiczny SSH do maszyny wirtualnej z systemem Linux przy użyciu polecenia `ssh-copy-id` .

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Tworzenie i konfigurowanie pliku konfiguracyjnego SSH

Można utworzyć i skonfigurować plik konfiguracji SSH ( `~/.ssh/config` ) w celu przyspieszenia logowania i zoptymalizowania zachowania klienta SSH. 

Poniższy przykład pokazuje prostą konfigurację, której można użyć, aby szybko zalogować się jako użytkownik do określonej maszyny wirtualnej przy użyciu domyślnego klucza prywatnego SSH. 

Utwórz plik.

```bash
touch ~/.ssh/config
```

Edytuj plik, aby dodać nową konfigurację protokołu SSH

```bash
vim ~/.ssh/config
```

Dodaj ustawienia konfiguracji odpowiednie dla maszyny wirtualnej hosta. W tym przykładzie nazwa maszyny wirtualnej to *MyVM* , a nazwa konta to *azureuser*.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Możesz dodać konfiguracje dla dodatkowych hostów, aby umożliwić każdemu z nich korzystanie z własnej pary kluczy. Zobacz [plik konfiguracji SSH](https://www.ssh.com/ssh/config/) , aby uzyskać bardziej zaawansowane opcje konfiguracji.

Teraz, gdy masz już parę kluczy SSH i skonfigurowany plik konfiguracji SSH, możesz szybko i bezpiecznie zalogować się do maszyny wirtualnej z systemem Linux. Po uruchomieniu poniższego polecenia SSH lokalizuje i ładuje wszystkie ustawienia z `Host myvm` bloku w pliku konfiguracyjnym SSH.

```bash
ssh myvm
```

Po pierwszym zalogowaniu się do serwera przy użyciu klucza SSH polecenie poprosi o wpisanie hasła dla tego pliku klucza.

## <a name="next-steps"></a>Następne kroki

W dalszej kolejności należy utworzyć maszyny wirtualne systemu Linux platformy Azure przy użyciu nowego klucza publicznego SSH. Maszyny wirtualne platformy Azure tworzone przy użyciu klucza publicznego SSH jako logowania są lepiej zabezpieczone niż maszyny wirtualne utworzone przy użyciu domyślnej metody logowania.

* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](quick-create-portal.md)
* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
* [Tworzenie maszyny wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md)
