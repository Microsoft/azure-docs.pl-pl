---
title: Jak włączyć wirtualizację zagnieżdżoną w maszynach wirtualnych platformy Azure
description: Jak włączyć wirtualizację zagnieżdżoną w maszynach wirtualnych platformy Azure
author: cynthn
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: how-to
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.openlocfilehash: 2c66f88cc49028fae50d89a9a7c24233d5a926b4
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865714"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Jak włączyć wirtualizację zagnieżdżoną na maszynie Wirtualnej platformy Azure

Wirtualizacja zagnieżdżona jest obsługiwana w kilku rodzinach maszyn wirtualnych platformy Azure. Ta funkcja zapewnia dużą elastyczność we wspieraniu scenariuszy, takich jak rozwój, testowanie, szkolenia i środowiska demonstracyjne.   

W tym artykule krok po włączeniu funkcji Hyper-V na maszynie wirtualnej platformy Azure i konfigurowaniu połączenia z Internetem na tej maszynie wirtualnej gościa.

## <a name="create-a-nesting-capable-azure-vm"></a>Tworzenie maszyny Wirtualnej platformy Azure z obsługą zagnieżdżenia

Utwórz nową maszynę wirtualną platformy Azure w systemie Windows Server 2016. Aby uzyskać pełną listę rozmiarów maszyn wirtualnych obsługujących zagnieżdżanie, zapoznaj się z [artykułem jednostki obliczeniowej platformy Azure](acu.md).

Pamiętaj, aby wybrać rozmiar maszyny Wirtualnej wystarczająco duży, aby obsługiwać wymagania maszyny wirtualnej gościa. W tym przykładzie używamy D3_v3 rozmiar maszyny Wirtualnej platformy Azure. 

Tutaj można wyświetlić regionalną dostępność [maszyn](https://azure.microsoft.com/regions/services/)wirtualnych z serii Dv3 lub Ev3.

>[!NOTE]
>
>Aby uzyskać szczegółowe instrukcje dotyczące tworzenia nowej maszyny wirtualnej, zobacz [Tworzenie maszyn wirtualnych systemu Windows i zarządzanie nimi za pomocą modułu programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Łączenie się z maszyną wirtualną platformy Azure

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną.

1. Kliknij przycisk **Połącz** we właściwościach maszyny wirtualnej. Zostanie utworzony i pobrany plik Remote Desktop Protocol (rdp).

2. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) ze sklepu Mac App Store.

3. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie kliknij przycisk **OK**.

4. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Włączanie funkcji funkcji funkcji Hyper-V na maszynie Wirtualnej platformy Azure
Ustawienia te można skonfigurować ręcznie lub udostępniliśmy skrypt programu PowerShell w celu zautomatyzowania konfiguracji.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opcja 1: Konfigurowanie wirtualizacji zagnieżdżonej zagnieżdżonej za pomocą skryptu programu PowerShell
Skrypt programu PowerShell umożliwiający wirtualizację zagnieżdżoną na hoście systemu Windows Server 2016 jest dostępny w [usłudze GitHub.](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested) Skrypt sprawdza wymagania wstępne, a następnie konfiguruje zagnieżdżoną wirtualizację na maszynie Wirtualnej platformy Azure. Ponowne uruchomienie maszyny Wirtualnej platformy Azure jest niezbędne do ukończenia konfiguracji. Ten skrypt może działać w innych środowiskach, ale nie jest gwarantowana. Zapoznaj się z wpisem w blogu platformy Azure z prezentacją wideo na żywo na temat wirtualizacji zagnieżdżonej uruchomionej na platformie Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opcja 2: Ręczne konfigurowanie zagnieżdżonej wirtualizacji

1. Na maszynie Wirtualnej platformy Azure otwórz program PowerShell jako administrator. 

2. Włącz funkcję funkcji Hyper-V i narzędzia do zarządzania.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >To polecenie powoduje ponowne uruchomienie maszyny Wirtualnej platformy Azure. Połączenie RDP zostanie utracine podczas procesu ponownego uruchamiania.
    
3. Po ponownym uruchomieniu maszyny Wirtualnej platformy Azure ponownie połącz się z maszyną wirtualną przy użyciu protokołu RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Konfigurowanie połączenia z Internetem dla maszyny wirtualnej gościa
Utwórz nową wirtualną kartę sieciową dla maszyny wirtualnej gościa i skonfiguruj bramę NAT, aby włączyć łączność z Internetem.

### <a name="create-a-nat-virtual-network-switch"></a>Tworzenie przełącznika sieci wirtualnej NAT

1. Na maszynie Wirtualnej platformy Azure otwórz program PowerShell jako administrator.
   
2. Utwórz przełącznik wewnętrzny.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Wyświetl właściwości przełącznika i zanotuj ifIndex dla nowej karty.

    ```powershell
    Get-NetAdapter
    ```

    ![Wadapter netadapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Zwróć uwagę na "ifIndex" dla właśnie utworzonego wirtualnego przełącznika.
    
4. Utwórz adres IP bramy NAT.
    
Aby skonfigurować bramę, potrzebne są pewne informacje o sieci:    
  * Adres IP — adres IP bramy NAT określa adres IPv4 lub IPv6, który ma być używany jako domyślny adres bramy dla podsieci sieci wirtualnej. Formularz ogólny to a.b.c.1 (na przykład "192.168.0.1"). Podczas gdy ostateczna pozycja nie musi być .1, zwykle jest (na podstawie długości prefiksu). Zazwyczaj należy użyć przestrzeni adresowej sieci prywatnej RFC 1918. 
  * PrefixLength - Długość prefiksu podsieci definiuje rozmiar podsieci lokalnej (maska podsieci). Długość prefiksu podsieci będzie wartością całkowitą z 0 do 32. 0 mapowałby cały Internet, 32 pozwalałby tylko na jedno zamapowane IP. Typowe wartości wahają się od 24 do 12 w zależności od tego, ile usług IP muszą być dołączone do translatora. Wspólny PrefiksLength to 24 -- jest to maska podsieci 255.255.255.0.
  * InterfaceIndex - **ifIndex** jest indeksem interfejsu przełącznika wirtualnego utworzonego w poprzednim kroku. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Tworzenie sieci NAT

Aby skonfigurować bramę, należy podać informacje o sieci i bramie NAT:
  * Nazwa — jest to nazwa sieci NAT. 
  * InternalIPInterfaceAddressPrefix - Prefiks podsieci NAT opisuje zarówno prefiks IP bramy NAT z góry, jak i długość prefiksu podsieci NAT z góry. Formularz ogólny będzie a.b.c.0/NAT Długość prefiksu podsieci. 

W programie PowerShell utwórz nową sieć NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Tworzenie maszyny wirtualnej gościa

>[!IMPORTANT] 
>
>Agent gościa platformy Azure nie jest obsługiwany na zagnieżdżonych maszynach wirtualnych i może powodować problemy zarówno na hoście, jak i zagnieżdżonych maszynach wirtualnych. Nie instaluj agenta platformy Azure na zagnieżdżonych maszynach wirtualnych i nie używaj obrazu do tworzenia zagnieżdżonych maszyn wirtualnych, które mają już zainstalowanego agenta gościa platformy Azure.

1. Otwórz Menedżera funkcji Hyper-V i utwórz nową maszynę wirtualną. Skonfiguruj maszynę wirtualną do używania nowej sieci wewnętrznej, która została utworzona.
    
    ![SiećConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Zainstaluj system operacyjny na maszynie wirtualnej gościa.
    
    >[!NOTE] 
    >
    >Potrzebny jest nośnik instalacyjny dla systemu operacyjnego do zainstalowania na maszynie wirtualnej. W tym przypadku używamy Systemu Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Przypisywanie adresu IP do maszyny wirtualnej gościa

Adres IP można przypisać do maszyny wirtualnej gościa, ręcznie ustawiając statyczny adres IP na maszynie wirtualnej gościa lub konfigurując usługę DHCP na maszynie Wirtualnej platformy Azure w celu dynamicznego przypisywania adresu IP.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opcja 1: Konfigurowanie usługi DHCP do dynamicznego przypisywania adresu IP do maszyny wirtualnej gościa
Wykonaj poniższe czynności, aby skonfigurować protokół DHCP na maszynie wirtualnej hosta do dynamicznego przypisywania adresów.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instalowanie serwera DCHP na maszynie Wirtualnej platformy Azure

1. Otwórz Menedżera serwera. Na pulpicie nawigacyjnym kliknij pozycję **Dodaj role i funkcje**. Zostanie otwarty Kreator dodawania ról i funkcji.
  
2. W kreatorze kliknij przycisk **Dalej,** aż strona Role serwera.
  
3. Kliknij, aby zaznaczyć pole wyboru **Serwer DHCP,** kliknij pozycję **Dodaj funkcje**, a następnie kliknij przycisk **Dalej,** aż do ukończenia pracy kreatora.
  
4. Kliknij **pozycję Zainstaluj**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurowanie nowego zakresu DHCP

1. Otwórz Menedżera DHCP.
  
2. W okienku nawigacji rozwiń nazwę serwera, kliknij prawym przyciskiem myszy **IPv4**i kliknij polecenie **Nowy zakres**. Zostanie wyświetlony Kreator nowego zakresu, kliknij przycisk **Dalej**.
  
3. Wprowadź nazwę i opis zakresu i kliknij przycisk **Dalej**.
  
4. Zdefiniuj zakres adresów IP dla serwera DCHP (na przykład 192.168.0.100 do 192.168.0.200).
  
5. Kliknij **przycisk Dalej,** aż strona Brama domyślna. Wprowadź adres IP utworzony wcześniej (na przykład 192.168.0.1) jako bramę domyślną, a następnie kliknij przycisk **Dodaj**.
  
6. Kliknij **przycisk Dalej,** aż kreator zakończy się, pozostawiając wszystkie wartości domyślne, a następnie kliknij przycisk **Zakończ**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opcja 2: Ręczne ustawianie statycznego adresu IP na maszynie wirtualnej gościa
Jeśli usługa DHCP nie została skonfigurowana do dynamicznego przypisywania adresu IP do maszyny wirtualnej gościa, wykonaj następujące kroki, aby ustawić statyczny adres IP.

1. Na maszynie Wirtualnej platformy Azure otwórz program PowerShell jako administrator.

2. Kliknij prawym przyciskiem myszy maszynę wirtualną gościa i kliknij polecenie Połącz.

3. Zaloguj się do maszyny wirtualnej gościa.

4. Na maszynie wirtualnej gościa otwórz Centrum sieci i udostępniania.

5. Skonfiguruj kartę sieciową dla adresu w zakresie sieci NAT utworzonej w poprzedniej sekcji.

W tym przykładzie użyjesz adresu w zakresie 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testowanie łączności na maszynie wirtualnej gościa

Na maszynie wirtualnej gościa otwórz przeglądarkę i przejdź do strony internetowej.
    ![GuestVM (GośćVM)](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Konfigurowanie łączności intranetowej dla maszyny wirtualnej gościa

Aby uzyskać instrukcje dotyczące włączania przezroczystej łączności między maszynami wirtualnymi gości a maszynami wirtualnymi platformy Azure, zapoznaj się z [tym dokumentem.](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network)
