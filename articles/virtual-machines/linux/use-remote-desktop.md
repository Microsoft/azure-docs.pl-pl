---
title: Używanie xrdp z systemem Linux
description: Dowiedz się, jak zainstalować i skonfigurować Pulpit zdalny (xrdp) w celu nawiązania połączenia z maszyną wirtualną z systemem Linux na platformie Azure przy użyciu narzędzi graficznych
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 309b106d2141c8257c5163efe7ff45a7bae5d5c3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759655"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Instalowanie i konfigurowanie programu xrdp w celu używania Pulpit zdalny z systemem Ubuntu

Maszyny wirtualne z systemem Linux na platformie Azure są zwykle zarządzane z wiersza polecenia przy użyciu połączenia Secure Shell (SSH). W przypadku nowych wersji systemu Linux lub szybkich scenariuszy rozwiązywania problemów korzystanie z pulpitu zdalnego może być łatwiejsze. W tym artykule szczegółowo opisano sposób instalowania i konfigurowania środowiska pulpitu[(xfce)](https://www.xfce.org)i pulpitu zdalnego[(xrdp)](http://xrdp.org)dla maszyny wirtualnej z systemem Linux z systemem Ubuntu.

Artykuł został napisany i przetestowany przy użyciu maszyny wirtualnej z systemem Ubuntu 18.04. 

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł wymaga istniejącej maszyny wirtualnej z systemem Ubuntu 18.04 LTS na platformie Azure. Jeśli musisz utworzyć maszynę wirtualną, użyj jednej z następujących metod:

- Interfejs [wiersza polecenia platformy Azure](quick-create-cli.md)
- Azure Portal [](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalowanie środowiska pulpitu na maszynie wirtualnej z systemem Linux

Większość maszyn wirtualnych z systemem Linux na platformie Azure nie ma domyślnie zainstalowanego środowiska pulpitu. Maszyny wirtualne z systemem Linux są często zarządzane przy użyciu połączeń SSH, a nie środowiska pulpitu. W systemie Linux można wybrać różne środowiska pulpitu. W zależności od wybranego środowiska pulpitu może ono zużywać od jednego do 2 GB miejsca na dysku, a instalowanie i konfigurowanie wszystkich wymaganych pakietów może potrwać od 5 do 10 minut.

Poniższy przykład instaluje lekkie środowisko pulpitu [xfce4](https://www.xfce.org/) na maszynie wirtualnej z systemem Ubuntu 18.04 LTS. Polecenia dla innych dystrybucji różnią się nieco (na przykład do instalowania na Red Hat Enterprise Linux i konfigurowania odpowiednich reguł lub używania do instalowania w `yum` `selinux` `zypper` programie SUSE).

Najpierw na stronie SSH na maszynie wirtualnej. Poniższy przykład łączy się z maszyną wirtualną *o myvm.westus.cloudapp.azure.com* przy użyciu nazwy użytkownika *azureuser.* Użyj własnych wartości:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli używasz systemu Windows i potrzebujesz więcej informacji na temat korzystania z SSH, zobacz Jak używać [kluczy SSH w systemie Windows.](ssh-from-windows.md)

Następnie zainstaluj program xfce przy `apt` użyciu następującego narzędzia:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalowanie i konfigurowanie serwera pulpitu zdalnego
Teraz, gdy masz zainstalowane środowisko pulpitu, skonfiguruj usługę pulpitu zdalnego do nasłuchiwać połączeń przychodzących. [xrdp](http://xrdp.org) to serwer open source Remote Desktop Protocol (RDP), który jest dostępny w większości dystrybucji systemu Linux i działa dobrze z xfce. Zainstaluj pakiet xrdp na maszynie wirtualnej z systemem Ubuntu w następujący sposób:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Poinformuj xrdp, jakiego środowiska pulpitu użyć po uruchomieniu sesji. Skonfiguruj xrdp do używania xfce jako środowiska pulpitu w następujący sposób:

```bash
echo xfce4-session >~/.xsession
```

Uruchom ponownie usługę xrdp, aby zmiany weszły w życie w następujący sposób:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ustawianie hasła konta użytkownika lokalnego
Jeśli podczas tworzenia maszyny wirtualnej utworzono hasło dla konta użytkownika, pomiń ten krok. Jeśli używasz tylko uwierzytelniania za pomocą klucza SSH i nie masz ustawionego hasła konta lokalnego, określ hasło przed użyciem xrdp do logowania się do maszyny wirtualnej. Xrdp nie może akceptować kluczy SSH do uwierzytelniania. W poniższym przykładzie określono hasło dla konta użytkownika *azureuser:*

```bash
sudo passwd azureuser
```

> [!NOTE]
> Określenie hasła nie powoduje zaktualizowania konfiguracji SSHD w celu zezwolenia na logowanie przy użyciu hasła, jeśli obecnie nie jest to możliwe. Z punktu widzenia zabezpieczeń możesz nawiązać połączenie z maszyną wirtualną za pomocą tunelu SSH przy użyciu uwierzytelniania opartego na kluczach, a następnie połączyć się z usługą xrdp. Jeśli tak, pomiń poniższy krok tworzenia reguły sieciowej grupy zabezpieczeń, aby zezwolić na ruch pulpitu zdalnego.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla ruchu Pulpit zdalny sieciowego
Aby zezwolić Pulpit zdalny do maszyny wirtualnej z systemem Linux, należy utworzyć regułę sieciowej grupy zabezpieczeń, która umożliwia dostęp protokołu TCP na porcie 3389 do maszyny wirtualnej. Aby uzyskać więcej informacji na temat reguł sieciowej grupy zabezpieczeń, zobacz [Co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/network-security-groups-overview.md) Reguły grupy zabezpieczeń Azure Portal użyć do utworzenia [reguły sieciowej grupy zabezpieczeń.](../windows/nsg-quickstart-portal.md)

Poniższy przykład tworzy regułę sieciowej grupy zabezpieczeń za pomocą [az vm open-port](/cli/azure/vm#az_vm_open_port) na *porcie 3389.* W interfejsie wiersza polecenia platformy Azure, a nie w sesji SSH z maszyną wirtualną, otwórz następującą regułę sieciowej grupy zabezpieczeń:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Łączenie maszyny wirtualnej z systemem Linux przy użyciu Pulpit zdalny wirtualnej

Otwórz lokalnego klienta pulpitu zdalnego i połącz się z adresem IP lub nazwą DNS maszyny wirtualnej z systemem Linux. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Zrzut ekranu przedstawiający klienta pulpitu zdalnego.":::

Wprowadź nazwę użytkownika i hasło dla konta użytkownika na maszynie wirtualnej w następujący sposób:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Zrzut ekranu przedstawiający ekran logowania xrdp.":::

Po uwierzytelnieniu środowisko pulpitu xfce zostanie załadowane i będzie wyglądać podobnie do następującego przykładu:

![Środowisko pulpitu xfce za pośrednictwem xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Jeśli lokalny klient RDP korzysta z uwierzytelniania na poziomie sieci (NLA), może być konieczne wyłączenie tego ustawienia połączenia. XRDP nie obsługuje obecnie NLA. Możesz również przyjrzeć się alternatywnym rozwiązaniom RDP, które obsługują nla, takim [jak FreeRDP.](https://www.freerdp.com)


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli nie możesz nawiązać połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta Pulpit zdalny, użyj narzędzia na maszynie wirtualnej z systemem Linux, aby sprawdzić, czy maszyna wirtualna nasłuchuje połączeń `netstat` RDP w następujący sposób:

```bash
sudo netstat -plnt | grep rdp
```

W poniższym przykładzie pokazano, jak maszyna wirtualna nasłuchuje na porcie TCP 3389 zgodnie z oczekiwaniami:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Jeśli usługa *xrdp-sesman* nie nasłuchuje, na maszynie wirtualnej z systemem Ubuntu uruchom ponownie usługę w następujący sposób:

```bash
sudo service xrdp restart
```

Przejrzyj dzienniki w *pliku /var/log* na maszynie wirtualnej z systemem Ubuntu, aby sprawdzić, dlaczego usługa może nie odpowiadać. Można również monitorować syslog podczas próby połączenia pulpitu zdalnego, aby wyświetlić błędy:

```bash
tail -f /var/log/syslog
```

Inne dystrybucje systemu Linux, takie jak Red Hat Enterprise Linux i SUSE, mogą mieć różne sposoby ponownego uruchamiania usług i alternatywnych lokalizacji plików dziennika do przejrzenia.

Jeśli nie otrzymasz żadnej odpowiedzi w kliencie pulpitu zdalnego i nie widzisz żadnych zdarzeń w dzienniku systemu, to zachowanie wskazuje, że ruch pulpitu zdalnego nie może uzyskać dostępu do maszyny wirtualnej. Przejrzyj reguły sieciowej grupy zabezpieczeń, aby upewnić się, że masz regułę zezwalania na ruch TCP na porcie 3389. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością aplikacji.](/troubleshoot/azure/virtual-machines/troubleshoot-app-connection)


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat tworzenia i używania kluczy SSH na maszynach wirtualnych z systemem Linux, zobacz [Create SSH keys for Linux VMs in Azure](mac-create-ssh-keys.md)(Tworzenie kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure).

Aby uzyskać informacje na temat używania SSH z systemu Windows, zobacz How to use SSH keys with Windows (Jak [używać kluczy SSH w systemie Windows).](ssh-from-windows.md)