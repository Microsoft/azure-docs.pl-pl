---
title: Jak włączyć wirtualizację zagnieżdżoną na platformie Azure Virtual Machines
description: Jak włączyć wirtualizację zagnieżdżoną na platformie Azure Virtual Machines
author: cynthn
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: how-to
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.openlocfilehash: e85ac58c80e1fd695938bf09b6435dba1f4ee083
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091350"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Jak włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej platformy Azure

Wirtualizacja zagnieżdżona jest obsługiwana w kilku rodzinach maszyn wirtualnych platformy Azure. Ta funkcja zapewnia dużą elastyczność w zakresie obsługi scenariuszy, takich jak programowanie, testowanie, uczenie i środowiska demonstracyjne.   

W tym artykule opisano Włączanie funkcji Hyper-V na maszynie wirtualnej platformy Azure i Konfigurowanie łączności internetowej z tą maszyną wirtualną gościa.

## <a name="create-a-nesting-capable-azure-vm"></a>Tworzenie maszyny wirtualnej platformy Azure z możliwością zagnieżdżania

Utwórz nową maszynę wirtualną platformy Azure z systemem Windows Server 2016. Aby uzyskać pełną listę rozmiarów maszyn wirtualnych obsługujących zagnieżdżanie, zapoznaj się z [artykułem dotyczącym jednostek obliczeniowych platformy Azure](../acu.md).

Należy pamiętać, aby wybrać rozmiar maszyny wirtualnej wystarczająco duży, aby obsługiwał wymagania maszyny wirtualnej gościa. W tym przykładzie używamy maszyny wirtualnej platformy Azure o rozmiarze D4_v3. 

W [tym miejscu](https://azure.microsoft.com/regions/services/)możesz zobaczyć dostępność maszyn wirtualnych z serii Dv3 lub EV3.

>[!NOTE]
>
>Aby uzyskać szczegółowe instrukcje dotyczące tworzenia nowej maszyny wirtualnej, zobacz [Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą modułu Azure PowerShell](./tutorial-manage-vm.md)
    
## <a name="connect-to-your-azure-vm"></a>Nawiązywanie połączenia z maszyną wirtualną platformy Azure

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną.

1. Kliknij przycisk **Połącz** we właściwościach maszyny wirtualnej. Zostanie utworzony i pobrany plik Remote Desktop Protocol (rdp).

2. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) ze sklepu Mac App Store.

3. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie kliknij przycisk **OK**.

4. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Włączanie funkcji Hyper-V na maszynie wirtualnej platformy Azure
Te ustawienia można skonfigurować ręcznie lub podano skrypt programu PowerShell służący do automatyzowania konfiguracji.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opcja 1: Użyj skryptu programu PowerShell, aby skonfigurować wirtualizację zagnieżdżoną
Skrypt programu PowerShell umożliwiający włączenie wirtualizacji zagnieżdżonej na hoście z systemem Windows Server 2016 jest dostępny w witrynie [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skrypt sprawdza wymagania wstępne, a następnie konfiguruje wirtualizację zagnieżdżoną na maszynie wirtualnej platformy Azure. Aby ukończyć konfigurację, należy ponownie uruchomić maszynę wirtualną platformy Azure. Ten skrypt może współpracować w innych środowiskach, ale nie jest gwarantowany. Zapoznaj się z wpisem w blogu platformy Azure, korzystając z pokazu wideo na żywo w przypadku zwirtualizowanej wirtualizacji działającej na platformie Azure https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opcja 2: Konfigurowanie wirtualizacji zagnieżdżonej ręcznie

1. Na maszynie wirtualnej platformy Azure Otwórz program PowerShell jako administrator. 

2. Włącz funkcję Hyper-V i narzędzia do zarządzania.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >To polecenie uruchamia ponownie maszynę wirtualną platformy Azure. Połączenie RDP zostanie utracone podczas procesu ponownego uruchamiania.
    
3. Po ponownym uruchomieniu maszyny wirtualnej platformy Azure ponownie nawiąż połączenie z maszyną wirtualną przy użyciu protokołu RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Konfigurowanie łączności z Internetem dla maszyny wirtualnej gościa
Utwórz nową wirtualną kartę sieciową dla maszyny wirtualnej gościa i skonfiguruj bramę translatora adresów sieciowych w celu umożliwienia łączności z Internetem.

### <a name="create-a-nat-virtual-network-switch"></a>Tworzenie przełącznika sieci wirtualnej NAT

1. Na maszynie wirtualnej platformy Azure Otwórz program PowerShell jako administrator.
   
2. Utwórz przełącznik wewnętrzny.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Wyświetl właściwości przełącznika i zanotuj numer IfIndex dla nowej karty.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Zanotuj "numer IfIndex" dla właśnie utworzonego przełącznika wirtualnego.
    
4. Utwórz adres IP dla bramy translatora adresów sieciowych.
    
Aby skonfigurować bramę, potrzebne są pewne informacje o sieci:    
  * IPAddress — adres IP bramy translatora adresów sieciowych określa protokół IPv4 lub IPv6, który ma być używany jako domyślny adres bramy podsieci sieci wirtualnej. Formularz generyczny to. b. c. 1 (na przykład "192.168.0.1"). Gdy końcowa pozycja nie musi być literałem, zazwyczaj jest (na podstawie długości prefiksu). Zwykle należy używać przestrzeni adresów sieci prywatnej RFC 1918. 
  * PrefixLength — długość prefiksu podsieci definiuje rozmiar podsieci lokalnej (Maska podsieci). Długość prefiksu podsieci będzie wartością całkowitą z zakresu od 0 do 32. wartość 0 spowoduje zamapowanie całego Internetu, 32 zezwala tylko na jeden zamapowany adres IP. Wspólne wartości mieszczą się w zakresie od 24 do 12 w zależności od tego, ile adresów IP należy dołączyć do translatora adresów sieciowych. Częstą PrefixLengthą jest 24 — jest to maska podsieci 255.255.255.0.
  * InterfaceIndex- **numer IfIndex** to indeks interfejsu przełącznika wirtualnego utworzonego w poprzednim kroku. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Tworzenie sieci NAT

Aby skonfigurować bramę, należy podać informacje o bramie sieci i translatora adresów sieciowych:
  * Nazwa — jest to nazwa sieci NAT. 
  * InternalIPInterfaceAddressPrefix — prefiks podsieci NAT zawiera opis prefiksu adresu IP bramy translatora adresów sieciowych, a także długość prefiksu podsieci NAT z powyżej. Formularz generyczny będzie miał długość prefiksu podsieci b. c. 0/translatora adresów sieciowych. 

W programie PowerShell utwórz nową sieć NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Tworzenie maszyny wirtualnej gościa

>[!IMPORTANT] 
>
>Agent gościa platformy Azure nie jest obsługiwany na zagnieżdżonych maszynach wirtualnych i może powodować problemy zarówno na hoście, jak i na zagnieżdżonych maszynach wirtualnych. Nie instaluj agenta platformy Azure na zagnieżdżonych maszynach wirtualnych i nie używaj obrazu do tworzenia zagnieżdżonych maszyn wirtualnych, na których jest już zainstalowany agent gościa platformy Azure.

1. Otwórz Menedżera funkcji Hyper-V i Utwórz nową maszynę wirtualną. Skonfiguruj maszynę wirtualną tak, aby korzystała z utworzonej nowej sieci wewnętrznej.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Zainstaluj system operacyjny na maszynie wirtualnej gościa.
    
    >[!NOTE] 
    >
    >Do zainstalowania na maszynie wirtualnej potrzebny jest nośnik instalacyjny systemu operacyjnego. W tym przypadku korzystamy z systemu Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Przypisywanie adresu IP do maszyny wirtualnej gościa

Adres IP można przypisać do maszyny wirtualnej gościa ręcznie przez ręczne ustawienie statycznego adresu IP na maszynie wirtualnej gościa lub skonfigurowanie protokołu DHCP na maszynie wirtualnej platformy Azure w celu dynamicznego przypisywania adresu IP.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opcja 1: Konfigurowanie protokołu DHCP do dynamicznego przypisywania adresu IP do maszyny wirtualnej gościa
Wykonaj poniższe kroki, aby skonfigurować protokół DHCP na maszynie wirtualnej hosta na potrzeby dynamicznego przypisywania adresów.

#### <a name="install-dhcp-server-on-the-azure-vm"></a>Instalowanie serwera DHCP na maszynie wirtualnej platformy Azure

1. Otwórz Menedżera serwera. Na pulpicie nawigacyjnym kliknij pozycję **Dodaj role i funkcje**. Zostanie otwarty Kreator dodawania ról i funkcji.
  
2. W Kreatorze kliknij przycisk **dalej** , aż do strony role serwera.
  
3. Kliknij, aby zaznaczyć pole wyboru **serwer DHCP** , kliknij przycisk **Dodaj funkcje**, a następnie kliknij przycisk **dalej** do momentu ukończenia pracy kreatora.
  
4. Kliknij przycisk **Zainstaluj**.

#### <a name="configure-a-new-dhcp-scope"></a>Skonfiguruj nowy zakres DHCP

1. Otwórz Menedżera DHCP.
  
2. W okienku nawigacji rozwiń nazwę serwera, kliknij prawym przyciskiem myszy pozycję **IPv4**, a następnie kliknij pozycję **Nowy zakres**. Zostanie wyświetlony Kreator nowego zakresu, a następnie kliknij przycisk **dalej**.
  
3. Wprowadź nazwę i opis zakresu, a następnie kliknij przycisk **dalej**.
  
4. Zdefiniuj zakres adresów IP dla serwera DHCP (na przykład 192.168.0.100 do 192.168.0.200).
  
5. Klikaj przycisk **dalej** , aż do strony bramy domyślnej. Wprowadź wcześniej utworzony adres IP (na przykład 192.168.0.1) jako bramę domyślną, a następnie kliknij przycisk **Dodaj**.
  
6. Klikaj przycisk **dalej** , aż kreator zakończy pracę, pozostawiając wszystkie wartości domyślne, a następnie kliknij przycisk **Zakończ**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opcja 2: ręczne ustawianie statycznego adresu IP na maszynie wirtualnej gościa
Jeśli nie skonfigurowano protokołu DHCP do dynamicznego przypisywania adresu IP do maszyny wirtualnej gościa, wykonaj następujące kroki, aby ustawić statyczny adres IP.

1. Na maszynie wirtualnej platformy Azure Otwórz program PowerShell jako administrator.

2. Kliknij prawym przyciskiem myszy maszynę wirtualną gościa, a następnie kliknij pozycję Połącz.

3. Zaloguj się do maszyny wirtualnej gościa.

4. Na maszynie wirtualnej gościa Otwórz Centrum sieci i udostępniania.

5. Skonfiguruj kartę sieciową dla adresu w zakresie sieci NAT utworzonej w poprzedniej sekcji.

W tym przykładzie zostanie użyty adres w zakresie 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testowanie łączności w maszynie wirtualnej gościa

Na maszynie wirtualnej gościa Otwórz przeglądarkę i przejdź do strony sieci Web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

Aby uzyskać instrukcje dotyczące włączania przezroczystych połączeń między maszynami wirtualnymi gościa i maszynami wirtualnymi platformy Azure, zapoznaj się z [dokumentem](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)
