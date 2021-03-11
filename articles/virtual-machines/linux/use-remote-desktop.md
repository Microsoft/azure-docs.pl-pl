---
title: Korzystanie z xrdp z systemem Linux
description: Informacje o instalowaniu i konfigurowaniu Pulpit zdalny (xrdp) w celu nawiązania połączenia z maszyną wirtualną z systemem Linux na platformie Azure przy użyciu narzędzi graficznych
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 1f7eb3b38b4ae04e81839fce2b14c1a84f3f0204
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564598"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Instalowanie i Konfigurowanie xrdp w celu używania Pulpit zdalny z Ubuntu

Maszyny wirtualne z systemem Linux na platformie Azure są zwykle zarządzane z wiersza polecenia przy użyciu połączenia Secure Shell (SSH). W przypadku nowych do systemu Linux lub w celu szybkiego rozwiązywania problemów korzystanie z pulpitu zdalnego może być prostsze. W tym artykule szczegółowo opisano sposób instalowania i konfigurowania środowiska pulpitu ([pulpit Xfce](https://www.xfce.org)) i pulpitu zdalnego ([xrdp](http://xrdp.org)) dla maszyny wirtualnej z systemem Linux i Ubuntu.

Artykuł został WRITEN i przetestowany przy użyciu maszyny wirtualnej Ubuntu 18,04. 

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł wymaga istniejącej maszyny wirtualnej Ubuntu 18,04 LTS na platformie Azure. Jeśli musisz utworzyć maszynę wirtualną, użyj jednej z następujących metod:

- [Interfejs wiersza polecenia platformy Azure](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalowanie środowiska pulpitu na maszynie wirtualnej z systemem Linux

Większość maszyn wirtualnych z systemem Linux na platformie Azure nie ma domyślnie zainstalowanego środowiska pulpitu. Maszyny wirtualne z systemem Linux są często zarządzane przy użyciu połączeń SSH, a nie środowiska pulpitu. Dostępne są różne środowiska pulpitu w systemie Linux, które można wybrać. W zależności od wybranego środowiska komputerowego może zużywać jeden do 2 GB miejsca na dysku i przejmować od 5 do 10 minut na zainstalowanie i skonfigurowanie wszystkich wymaganych pakietów.

Poniższy przykład instaluje środowisko pulpitu lekkiego [Xfce4](https://www.xfce.org/) na maszynie wirtualnej z systemem Ubuntu 18,04 LTS. Polecenia dla innych dystrybucji różnią się nieznacznie (Użyj, `yum` Aby zainstalować program na Red Hat Enterprise Linux i skonfigurować odpowiednie `selinux` reguły, lub użyć `zypper` programu do instalacji w systemie SUSE, na przykład).

Najpierw SSH z maszyną wirtualną. Poniższy przykład nawiązuje połączenie z maszyną wirtualną o nazwie *MyVM.westus.cloudapp.Azure.com* z nazwą użytkownika *azureuser*. Użyj własnych wartości:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli używasz systemu Windows i potrzebujesz więcej informacji na temat korzystania z protokołu SSH, zobacz [jak używać kluczy SSH w systemie Windows](ssh-from-windows.md).

Następnie zainstaluj pulpit Xfce przy użyciu `apt` następujących metod:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalowanie i Konfigurowanie serwera usług pulpitu zdalnego
Teraz, gdy masz zainstalowane środowisko pulpitu, skonfiguruj usługę pulpitu zdalnego do nasłuchiwania połączeń przychodzących. [xrdp](http://xrdp.org) to serwer open source Remote Desktop Protocol (RDP), który jest dostępny w większości dystrybucji systemu Linux i dobrze współdziała z pulpit Xfce. Zainstaluj program xrdp na maszynie wirtualnej Ubuntu w następujący sposób:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Poinformuj xrdp o tym, jakie środowisko pulpitu ma być używane podczas uruchamiania sesji. Skonfiguruj xrdp do używania pulpit Xfce jako środowiska pulpitu w następujący sposób:

```bash
echo xfce4-session >~/.xsession
```

Uruchom ponownie usługę xrdp, aby zmiany zaczęły obowiązywać w następujący sposób:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ustawianie hasła konta użytkownika lokalnego
Jeśli utworzono hasło dla konta użytkownika podczas tworzenia maszyny wirtualnej, Pomiń ten krok. Jeśli używasz tylko uwierzytelniania przy użyciu klucza SSH i nie masz skonfigurowanego hasła konta lokalnego, określ hasło przed użyciem xrdp do logowania się do maszyny wirtualnej. xrdp nie może akceptować kluczy SSH na potrzeby uwierzytelniania. W poniższym przykładzie określono hasło dla konta użytkownika *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Określenie hasła nie aktualizuje konfiguracji SSHD w celu zezwolenia na logowanie za pomocą hasła, jeśli obecnie nie jest to możliwe. Z punktu widzenia zabezpieczeń można nawiązać połączenie z maszyną wirtualną za pomocą tunelu SSH przy użyciu uwierzytelniania opartego na kluczach, a następnie nawiązać połączenie z usługą xrdp. Jeśli tak, Pomiń Poniższy krok podczas tworzenia reguły sieciowej grupy zabezpieczeń, aby zezwolić na ruch pulpitu zdalnego.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Utwórz regułę sieciowej grupy zabezpieczeń dla ruchu Pulpit zdalny
Aby zezwolić na ruch Pulpit zdalny do maszyny wirtualnej z systemem Linux, należy utworzyć regułę sieciowej grupy zabezpieczeń, która zezwala na ruch TCP na porcie 3389 w celu uzyskania dostępu do maszyny wirtualnej. Aby uzyskać więcej informacji o regułach sieciowej grupy zabezpieczeń, zobacz [co to jest sieciowa Grupa zabezpieczeń?](../../virtual-network/network-security-groups-overview.md) Możesz również [użyć Azure Portal, aby utworzyć regułę sieciowej grupy zabezpieczeń](../windows/nsg-quickstart-portal.md).

Poniższy przykład tworzy regułę sieciowej grupy zabezpieczeń przy użyciu [AZ VM Open-Port](/cli/azure/vm#az-vm-open-port) na porcie *3389*. Z poziomu wiersza polecenia platformy Azure, a nie sesji SSH z maszyną wirtualną, Otwórz następującą regułę sieciowej grupy zabezpieczeń:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux za pomocą klienta Pulpit zdalny

Otwórz lokalny klient pulpitu zdalnego i nawiąż połączenie z adresem IP lub nazwą DNS maszyny wirtualnej z systemem Linux. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Zrzut ekranu klienta pulpitu zdalnego.":::

Wprowadź nazwę użytkownika i hasło dla konta użytkownika na maszynie wirtualnej w następujący sposób:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Zrzut ekranu przedstawiający ekran logowania xrdp.":::

Po uwierzytelnieniu środowisko pulpitu pulpit Xfce zostanie załadowane i będzie wyglądać podobnie do poniższego przykładu:

![Środowisko pulpitu pulpit Xfce przez xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Jeśli lokalny klient RDP korzysta z uwierzytelniania na poziomie sieci, może być konieczne wyłączenie tego ustawienia połączenia. XRDP obecnie nie obsługuje NLA. Możesz również zapoznać się z alternatywnymi rozwiązaniami RDP, które obsługują uwierzytelnianie NLA, takie jak [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli nie można nawiązać połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta Pulpit zdalny, użyj `netstat` maszyny wirtualnej z systemem Linux, aby sprawdzić, czy maszyna wirtualna nasłuchuje połączeń RDP w następujący sposób:

```bash
sudo netstat -plnt | grep rdp
```

Poniższy przykład pokazuje, że maszyna wirtualna nasłuchuje na porcie TCP 3389 zgodnie z oczekiwaniami:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Jeśli usługa *xrdp-sesman* nie nasłuchuje, na maszynie wirtualnej Ubuntu Uruchom ponownie usługę w następujący sposób:

```bash
sudo service xrdp restart
```

Przejrzyj dzienniki w */var/log* na maszynie wirtualnej Ubuntu w celu wskazania, dlaczego usługa może nie odpowiadać. Dziennik systemowy można także monitorować podczas próby połączenia pulpitu zdalnego, aby wyświetlić wszystkie błędy:

```bash
tail -f /var/log/syslog
```

Inne dystrybucje systemu Linux, takie jak Red Hat Enterprise Linux i SUSE, mogą mieć różne sposoby ponownego uruchamiania usług i alternatywnych lokalizacji plików dziennika do przejrzenia.

Jeśli nie otrzymasz żadnej odpowiedzi z klienta pulpitu zdalnego i nie widzisz żadnych zdarzeń w dzienniku systemu, to zachowanie wskazuje, że ruch pulpitu zdalnego nie może nawiązać połączenia z maszyną wirtualną. Przejrzyj reguły sieciowej grupy zabezpieczeń, aby upewnić się, że masz regułę zezwalającą na ruch TCP na porcie 3389. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością aplikacji](../troubleshooting/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat tworzenia i używania kluczy SSH z maszynami wirtualnymi z systemem Linux, zobacz [Tworzenie kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](mac-create-ssh-keys.md).

Aby uzyskać informacje na temat korzystania z protokołu SSH w systemie Windows, zobacz [jak używać kluczy SSH w systemie Windows](ssh-from-windows.md).
