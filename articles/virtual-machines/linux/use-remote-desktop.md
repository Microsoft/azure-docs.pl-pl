---
title: Używanie pulpitu zdalnego na maszynie wirtualnej z systemem Linux na platformie Azure
description: Dowiedz się, jak zainstalować i skonfigurować pulpit zdalny (xrdp) do łączenia się z maszyną wirtualną z systemem Linux na platformie Azure przy użyciu narzędzi graficznych
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/12/2019
ms.author: cynthn
ms.openlocfilehash: 2e97442d4104f52c1a76ba8cd1d81c99508bb242
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605183"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalowanie i konfigurowanie pulpitu zdalnego w celu łączenia się z maszyną wirtualną z systemem Linux na platformie Azure
Maszyny wirtualne systemu Linux (VM) na platformie Azure są zwykle zarządzane z wiersza polecenia przy użyciu połączenia bezpiecznej powłoki (SSH). Gdy nowy linux lub dla szybkich scenariuszy rozwiązywania problemów, korzystanie z pulpitu zdalnego może być łatwiejsze. W tym artykule opisano sposób instalowania i konfigurowania środowiska pulpitu[(xfce)](https://www.xfce.org)i pulpitu zdalnego[(xrdp)](http://xrdp.org)dla maszyny Wirtualnej systemu Linux przy użyciu modelu wdrażania Menedżera zasobów.


## <a name="prerequisites"></a>Wymagania wstępne
Ten artykuł wymaga istniejącej maszyny Wirtualnej Ubuntu 18.04 LTS na platformie Azure. Jeśli chcesz utworzyć maszynę wirtualną, użyj jednej z następujących metod:

- Narzędzie [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
- [Portal platformy Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalowanie środowiska graficznego na maszynie wirtualnej z systemem Linux
Większość maszyn wirtualnych z systemem Linux na platformie Azure nie ma środowiska pulpitu zainstalowanego domyślnie. Maszyny wirtualne z systemem Linux są powszechnie zarządzane przy użyciu połączeń SSH, a nie środowiska graficznego. Istnieją różne środowiska graficzne w Systemie Linux, które można wybrać. W zależności od wybranego środowiska graficznego może zużywać od jednego do 2 GB miejsca na dysku i zająć od 5 do 10 minut, aby zainstalować i skonfigurować wszystkie wymagane pakiety.

Poniższy przykład instaluje lekkie środowisko pulpitu [xfce4](https://www.xfce.org/) na maszynie wirtualnej Ubuntu 18.04 LTS. Polecenia dla innych dystrybucji różnią `yum` się nieznacznie (służy do instalowania na Red Hat Enterprise Linux i konfigurowania odpowiednich `selinux` reguł lub używać `zypper` do instalowania na SUSE, na przykład).

Najpierw SSH do maszyny Wirtualnej. Poniższy przykład łączy się z maszyną wirtualną o nazwie *myvm.westus.cloudapp.azure.com* z nazwą użytkownika *azureuser*. Użyj własnych wartości:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli korzystasz z systemu Windows i potrzebujesz więcej informacji na temat korzystania z funkcji SSH, zobacz [Jak używać kluczy SSH w systemie Windows](ssh-from-windows.md).

Następnie zainstaluj xfce `apt` w następujący sposób:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalowanie i konfigurowanie serwera pulpitu zdalnego
Teraz, gdy masz zainstalowane środowisko pulpitu, skonfiguruj usługę pulpitu zdalnego, aby nasłuchiwać połączeń przychodzących. [xrdp](http://xrdp.org) jest serwerem RDP (Open Source Remote Desktop Protocol), który jest dostępny w większości dystrybucji Linuksa i działa dobrze z xfce. Zainstaluj xrdp na maszynie wirtualnej Ubuntu w następujący sposób:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Powiedz xrdp, jakiego środowiska graficznego użyć po rozpoczęciu sesji. Skonfiguruj xrdp tak, aby używał xfce jako środowiska graficznego w następujący sposób:

```bash
echo xfce4-session >~/.xsession
```

Uruchom ponownie usługę xrdp, aby zmiany zostały wprowadzone w następujący sposób:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ustawianie hasła do konta użytkownika lokalnego
Jeśli podczas tworzenia maszyny Wirtualnej utworzono hasło do konta użytkownika, pomiń ten krok. Jeśli używasz tylko uwierzytelniania za pomocą klucza SSH i nie masz ustawionego hasła do konta lokalnego, przed zalogowaniem się do maszyny Wirtualnej należy określić hasło przed użyciem xrdp. xrdp nie może akceptować kluczy SSH do uwierzytelniania. Poniższy przykład określa hasło dla *użytkownika*konta użytkownika:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Określenie hasła nie powoduje aktualizacji konfiguracji dysku SSHD, aby zezwolić na logowanie hasła, jeśli obecnie nie. Z punktu widzenia zabezpieczeń można połączyć się z maszyną wirtualną za pomocą tunelu SSH przy użyciu uwierzytelniania opartego na kluczach, a następnie połączyć się z xrdp. Jeśli tak, pomiń następujący krok podczas tworzenia reguły sieciowej grupy zabezpieczeń, aby zezwolić na ruch pulpitu zdalnego.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla ruchu pulpitu zdalnego
Aby umożliwić ruchowi pulpitu zdalnego dotarcie do maszyny Wirtualnej systemu Linux, należy utworzyć regułę sieciowej grupy zabezpieczeń, która umożliwia TCP na porcie 3389 dotarcie do maszyny Wirtualnej. Aby uzyskać więcej informacji na temat reguł sieciowej grupy zabezpieczeń, zobacz [Co to jest siećowa grupa zabezpieczeń?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Za pomocą portalu Azure można również [utworzyć regułę sieciowej grupy zabezpieczeń](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Poniższy przykład tworzy regułę sieciowej grupy zabezpieczeń z [otwartym portem az vm](/cli/azure/vm#az-vm-open-port) na porcie *3389*. Z interfejsu wiersza polecenia platformy Azure, a nie sesji SSH do maszyny Wirtualnej, otwórz następującą regułę sieciowej grupy zabezpieczeń:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Łączenie maszyny wirtualnej z systemem Linux za pomocą klienta pulpitu zdalnego
Otwórz lokalnego klienta pulpitu zdalnego i połącz się z adresem IP lub nazwą DNS maszyny Wirtualnej systemu Linux. Wprowadź nazwę użytkownika i hasło do konta użytkownika na maszynie Wirtualnej w następujący sposób:

![Łączenie się z xrdp za pomocą klienta pulpitu zdalnego](./media/use-remote-desktop/remote-desktop-client.png)

Po uwierzytelnieniu środowisko pulpitu xfce zostanie załadowane i będzie wyglądać podobnie do następującego przykładu:

![xfce środowisko graficzne przez xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Jeśli lokalny klient RDP używa uwierzytelniania na poziomie sieci (NLA), może być konieczne wyłączenie tego ustawienia połączenia. XRDP obecnie nie obsługuje NLA. Można również przyjrzeć się alternatywnym rozwiązaniom RDP, które obsługują NLA, takim jak [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli nie można połączyć się z maszyną `netstat` wirtualną z systemem Linux przy użyciu klienta pulpitu zdalnego, użyj na maszynie Wirtualnej systemu Linux, aby sprawdzić, czy maszyna wirtualna nasłuchuje połączeń RDP w następujący sposób:

```bash
sudo netstat -plnt | grep rdp
```

W poniższym przykładzie przedstawiono nasłuchiwanie maszyny Wirtualnej na porcie TCP 3389 zgodnie z oczekiwaniami:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Jeśli usługa *xrdp-sesman* nie nasłuchuje, na maszynie wirtualnej Ubuntu uruchom ponownie usługę w następujący sposób:

```bash
sudo service xrdp restart
```

Przejrzyj dzienniki */var/log* na maszynie wirtualnej Ubuntu, aby dowiadują się, dlaczego usługa może nie odpowiadać. Można również monitorować syslog podczas próby połączenia pulpitu zdalnego, aby wyświetlić wszelkie błędy:

```bash
tail -f /var/log/syslog
```

Inne dystrybucje Linuksa, takie jak Red Hat Enterprise Linux i SUSE, mogą mieć różne sposoby ponownego uruchamiania usług i alternatywnych lokalizacji plików dziennika do sprawdzenia.

Jeśli nie otrzymasz żadnej odpowiedzi na kliencie pulpitu zdalnego i nie zobaczysz żadnych zdarzeń w dzienniku systemu, to zachowanie wskazuje, że ruch pulpitu zdalnego nie może dotrzeć do maszyny Wirtualnej. Przejrzyj reguły sieciowej grupy zabezpieczeń, aby upewnić się, że masz regułę zezwalania na protokół TCP na porcie 3389. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością aplikacji](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat tworzenia i używania kluczy SSH z maszynami wirtualnymi z systemem Linux, zobacz [Tworzenie kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](mac-create-ssh-keys.md).

Aby uzyskać informacje na temat korzystania z funkcji SSH z systemu Windows, zobacz [Jak używać kluczy SSH w systemie Windows](ssh-from-windows.md).

