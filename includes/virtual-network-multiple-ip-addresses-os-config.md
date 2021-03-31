---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 93caf39216ef0479ec2799267a9ba8181f37f802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84194225"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Adresy IP do systemu operacyjnego maszyny wirtualnej

Łączenie się z maszyną wirtualną utworzoną za pomocą wielu prywatnych adresów IP i logowanie się do niej. Wszystkie prywatne adresy IP (w tym podstawowy) dodane do maszyny wirtualnej muszą zostać wprowadzone ręcznie. Wykonaj kroki opisane poniżej dla systemu operacyjnego maszyny wirtualnej.

### <a name="windows-server"></a>Windows Server
<details>
  <summary>Rozwiń</summary>

1. W wierszu polecenia wpisz *ipconfig /all*.  Wyświetlany jest tylko *podstawowy* prywatny adres IP (przypisywany za pośrednictwem protokołu DHCP).
2. Wpisz polecenie *ncpa.cpl* w wierszu polecenia, aby otworzyć okno **Połączenia sieciowe**.
3. Otwórz właściwości odpowiedniej karty sieciowej: **Ethernet**.
4. Kliknij dwukrotnie Protokół internetowy w wersji 4 (IPv4).
5. Wybierz pozycję **Użyj następującego adresu IP** i wprowadź następujące wartości:

    * **Adres IP**: wprowadź *podstawowy* prywatny adres IP.
    * **Maska podsieci**: ustaw na podstawie swojej sieci. Na przykład jeśli podsieć jest podsiecią typu /24, wprowadź maską podsieci 255.255.255.0.
    * **Brama domyślna**: pierwszy adres IP w podsieci. Jeśli podsieć jest typu 10.0.0.0/24, adresem IP bramy będzie 10.0.0.1.
    * Wybierz opcję **Użyj następujących adresów serwerów DNS** i wprowadź następujące wartości:
        * **Preferowany serwer DNS**: jeśli nie używasz własnego serwera DNS, wprowadź adres 168.63.129.16.  Jeśli używasz własnego serwera DNS, wprowadź jego adres IP.  (Dla alternatywnego serwera DNS można wybrać dowolny bezpłatny publiczny adres serwera DNS).
    * Wybierz przycisk **Zaawansowane** i Dodaj dodatkowe adresy IP. Dodaj wszystkie pomocnicze prywatne adresy IP, które zostały dodane do interfejsu sieciowego platformy Azure w poprzednim kroku, do interfejsu sieciowego systemu Windows, do którego przypisany jest podstawowy adres IP przypisany do interfejsu sieciowego platformy Azure.

        Nigdy nie należy ręcznie przypisywać publicznego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej. W przypadku ręcznego ustawiania adresu IP w ramach systemu operacyjnego upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do [interfejsu sieciowego](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)platformy Azure, lub utracisz łączność z maszyną wirtualną. Dowiedz się więcej o ustawieniach [prywatnych adresów IP](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) . Nie należy nigdy przypisywać publicznego adresu IP platformy Azure w ramach systemu operacyjnego.

    * Kliknij przycisk **OK**, aby zamknąć ustawienia TCP/IP, a następnie kliknij ponownie przycisk **OK**, aby zamknąć ustawienia karty. Połączenie RDP zostanie ponownie nawiązane.

6. W wierszu polecenia wpisz *ipconfig /all*. Sprawdź, czy zostały wyświetlone wszystkie dodane adresy IP, a protokół DHCP jest wyłączony.
7. Skonfiguruj system Windows do korzystania z prywatnego adresu IP podstawowej konfiguracji adresu IP na platformie Azure jako podstawowego adresu IP dla systemu Windows. Aby uzyskać szczegółowe informacje [, zobacz Brak dostępu do Internetu z maszyny wirtualnej platformy Azure z systemem Windows z wieloma adresami IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) . 

### <a name="validation-windows-server"></a>Walidacja (system Windows Server)

Aby upewnić się, że można nawiązać połączenie z Internetem przy użyciu konfiguracji pomocniczego adresu IP za pomocą skojarzonego z nim publicznego adresu IP, po poprawnym dodaniu go do powyższego kroku Użyj następującego polecenia (zastępując 10.0.0.7 z pomocniczym prywatnym adresem IP):

```bash
ping -S 10.0.0.7 outlook.com
```
>[!NOTE]
>W przypadku konfiguracji pomocniczych adresów IP można wysyłać polecenia ping do Internetu tylko wtedy, gdy z konfiguracją jest skojarzony publiczny adres IP. W przypadku podstawowych konfiguracji adresu IP publiczny adres IP nie jest wymagany do wysyłania poleceń ping do Internetu.
</details>

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)
<details>
  <summary>Rozwiń</summary>
Zalecamy zapoznanie się z najnowszą dokumentacją dystrybucji systemu Linux. 

1. Otwórz okno terminalu.
2. Upewnij się, że jesteś zalogowany jako użytkownik root. Jeśli nie, wprowadź następujące polecenie:

   ```bash
   sudo -i
   ```

3. Zaktualizuj plik konfiguracji interfejsu sieciowego (przyjęto, że jest to „eth0”).

   * Zachowaj istniejący element wiersza dla protokołu dhcp. Podstawowy adres IP pozostanie skonfigurowany tak jak poprzednio.
   * Dodaj konfigurację dla dodatkowego statycznego adresu IP za pomocą następujących poleceń:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     Powinien zostać wyświetlony plik cfg.
4. Otwórz ten plik. Na końcu tego pliku powinny znajdować się następujące wiersze:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Po wierszach, które istnieją w tym pliku, dodaj następujące wiersze:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Zapisz plik za pomocą następującego polecenia:

   ```bash
   :wq
   ```

7. Zresetuj interfejs sieciowy przy użyciu następującego polecenia:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Jeśli korzystasz z połączenia zdalnego, uruchom w jednym wierszu zarówno polecenie ifdown, jak i ifup.
   >

8. Za pomocą następującego polecenia sprawdź, czy adres IP został dodany do interfejsu sieciowego:

   ```bash
   ip addr list eth0
   ```

   Dodany adres IP powinien być widoczny na liście.

### <a name="validation-ubuntu-1416"></a>Walidacja (Ubuntu 14/16)

Aby upewnić się, że będziesz mieć możliwość nawiązania połączenia z Internetem przy użyciu konfiguracji pomocniczego adresu IP za pośrednictwem skojarzonego z nią publicznego adresu IP, użyj następującego polecenia:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>W przypadku konfiguracji pomocniczych adresów IP można wysyłać polecenia ping do Internetu tylko wtedy, gdy z konfiguracją jest skojarzony publiczny adres IP. W przypadku podstawowych konfiguracji adresu IP publiczny adres IP nie jest wymagany do wysyłania poleceń ping do Internetu.

W przypadku maszyn wirtualnych z systemem Linux podczas próby walidacji łączności wychodzącej z pomocniczej karty sieciowej może być konieczne dodanie odpowiednich tras. Istnieje wiele sposobów, aby to zrobić. Zapoznaj się z odpowiednią dokumentacją dla swojej dystrybucji systemu. Oto jedna z metod wykonania tej czynności:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Pamiętaj, aby zamienić:
    - wartość **10.0.0.5** na prywatny adres IP, który ma skojarzony ze sobą publiczny adres IP;
    - wartość **10.0.0.1** na bramę domyślną;
    - **eth2** na nazwę pomocniczej karty sieciowej </details>

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04 +)
<details>
  <summary>Rozwiń</summary>
Ubuntu 18,04 i nowsze zostały zmienione na `netplan` dla zarządzania siecią systemu operacyjnego. Zalecamy zapoznanie się z najnowszą dokumentacją dystrybucji systemu Linux. 

1. Otwórz okno terminalu.
2. Upewnij się, że jesteś zalogowany jako użytkownik root. Jeśli nie, wprowadź następujące polecenie:

    ```bash
    sudo -i
    ```

3. Utwórz plik dla drugiego interfejsu i otwórz go w edytorze tekstu:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Dodaj następujące wiersze do pliku, zastępując `10.0.0.6/24` je adresem IP/maską:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Zapisz plik za pomocą następującego polecenia:

    ```bash
    :wq
    ```

6. Przetestuj zmiany przy użyciu [planu](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) usługi, aby potwierdzić składnię:

    ```bash
    netplan try
    ```

> [!NOTE]
> `netplan try` Program zastosuje zmiany tymczasowo i przewróci zmiany ponownie po 120 sekundach. W przypadku utraty łączności należy zaczekać 120 sekund, a następnie ponownie nawiązać połączenie. W tym momencie zmiany zostaną wycofane.

7. Przy założeniu, że nie występują żadne problemy z programem `netplan try` , Zastosuj zmiany w konfiguracji:

    ```bash
    netplan apply
    ```

8. Za pomocą następującego polecenia sprawdź, czy adres IP został dodany do interfejsu sieciowego:

    ```bash
    ip addr list eth0
    ```

    Dodany adres IP powinien być widoczny na liście. Przykład:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```
### <a name="validation-ubuntu-1804"></a>Walidacja (Ubuntu 18.04 +)

Aby upewnić się, że będziesz mieć możliwość nawiązania połączenia z Internetem przy użyciu konfiguracji pomocniczego adresu IP za pośrednictwem skojarzonego z nią publicznego adresu IP, użyj następującego polecenia:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>W przypadku konfiguracji pomocniczych adresów IP można wysyłać polecenia ping do Internetu tylko wtedy, gdy z konfiguracją jest skojarzony publiczny adres IP. W przypadku podstawowych konfiguracji adresu IP publiczny adres IP nie jest wymagany do wysyłania poleceń ping do Internetu.

W przypadku maszyn wirtualnych z systemem Linux podczas próby walidacji łączności wychodzącej z pomocniczej karty sieciowej może być konieczne dodanie odpowiednich tras. Istnieje wiele sposobów, aby to zrobić. Zapoznaj się z odpowiednią dokumentacją dla swojej dystrybucji systemu. Oto jedna z metod wykonania tej czynności:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Pamiętaj, aby zamienić:
    - wartość **10.0.0.5** na prywatny adres IP, który ma skojarzony ze sobą publiczny adres IP;
    - wartość **10.0.0.1** na bramę domyślną;
    - **eth2** na nazwę pomocniczej karty sieciowej </details>

### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat, CentOS i inne)
<details>
  <summary>Rozwiń</summary>

1. Otwórz okno terminalu.
2. Upewnij się, że jesteś zalogowany jako użytkownik root. Jeśli nie, wprowadź następujące polecenie:

    ```bash
    sudo -i
    ```

3. Wprowadź hasło i postępuj zgodnie z wyświetlanymi instrukcjami. Jeśli jesteś zalogowany jako użytkownik root, przejdź do folderu za skryptami sieciowymi za pomocą następującego polecenia:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Użyj następującego polecenia, aby wyświetlić listę powiązanych plików ifcfg:

    ```bash
    ls ifcfg-*
    ```

    Jeden z plików powinien mieć nazwę *ifcfg-eth0*.

5. Aby dodać adres IP, utwórz dla niego plik konfiguracji zgodnie z poniższym wzorem. Pamiętaj, że dla każdej konfiguracji IP powinien zostać utworzony jeden plik.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Otwórz plik *ifcfg-eth0:0* za pomocą następującego polecenia:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Dodaj zawartość do pliku (w tym przypadku *eth0:0*) przy użyciu następującego polecenia. Pamiętaj, aby zaktualizować informacje na podstawie swojego adresu IP.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Zapisz plik, korzystając z następującego polecenia:

    ```bash
    :wq
    ```

9. Uruchom ponownie usługi sieciowe i upewnij się, że zmiany zostały pomyślnie wprowadzone, uruchamiając następujące polecenia:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Dodany adres IP (*eth0:0*) powinien być widoczny na zwróconej liście.

### <a name="validation-red-hat-centos-and-others"></a>Walidacja (Red Hat, CentOS i inne)

Aby upewnić się, że będziesz mieć możliwość nawiązania połączenia z Internetem przy użyciu konfiguracji pomocniczego adresu IP za pośrednictwem skojarzonego z nią publicznego adresu IP, użyj następującego polecenia:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>W przypadku konfiguracji pomocniczych adresów IP można wysyłać polecenia ping do Internetu tylko wtedy, gdy z konfiguracją jest skojarzony publiczny adres IP. W przypadku podstawowych konfiguracji adresu IP publiczny adres IP nie jest wymagany do wysyłania poleceń ping do Internetu.

W przypadku maszyn wirtualnych z systemem Linux podczas próby walidacji łączności wychodzącej z pomocniczej karty sieciowej może być konieczne dodanie odpowiednich tras. Istnieje wiele sposobów, aby to zrobić. Zapoznaj się z odpowiednią dokumentacją dla swojej dystrybucji systemu. Oto jedna z metod wykonania tej czynności:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Pamiętaj, aby zamienić:
    - wartość **10.0.0.5** na prywatny adres IP, który ma skojarzony ze sobą publiczny adres IP;
    - wartość **10.0.0.1** na bramę domyślną;
    - **eth2** na nazwę pomocniczej karty sieciowej </details>