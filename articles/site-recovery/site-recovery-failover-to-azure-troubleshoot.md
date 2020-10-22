---
title: Rozwiązywanie problemów z trybem failover na platformie Azure — błędy | Microsoft Docs
description: W tym artykule opisano sposoby rozwiązywania typowych błędów w przypadku przełączenia w tryb failover na platformie Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: d2a0444483c382da7c54accf7dca49d097671771
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371991"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Rozwiązywanie problemów z błędami w przypadku przejścia w tryb failover maszyny wirtualnej VMware lub komputera fizycznego na platformie Azure

Podczas pracy w trybie failover maszyny wirtualnej na platformie Azure może zostać wyświetlony jeden z następujących błędów. Aby rozwiązać problem, należy wykonać opisane kroki dla każdego warunku błędu.

## <a name="failover-failed-with-error-id-28031"></a>Nie można przełączyć do trybu failover z powodu błędu o IDENTYFIKATORze 28031

Site Recovery nie mógł utworzyć maszyny wirtualnej przełączonej w tryb failover na platformie Azure. Może się to zdarzyć z jednego z następujących powodów:

* Nie ma wystarczającego limitu przydziału, aby utworzyć maszynę wirtualną: można sprawdzić dostępny limit przydziału, przechodząc do opcji subskrypcja-> użycie i przydziały. Aby zwiększyć limit przydziału, możesz otworzyć [nowe żądanie pomocy technicznej](https://aka.ms/getazuresupport) .

* Podjęto próbę przejścia do trybu failover maszyn wirtualnych z różnymi rodzinami rozmiarów w tym samym zestawie dostępności. Upewnij się, że wybrano rodzinę rozmiarów dla wszystkich maszyn wirtualnych w tym samym zestawie dostępności. Zmień rozmiar, przechodząc do ustawień obliczenia i sieć maszyny wirtualnej, a następnie ponów próbę przełączenia w tryb failover.

* Istnieją zasady dotyczące subskrypcji, która uniemożliwia tworzenie maszyny wirtualnej. Zmień zasady, aby umożliwić tworzenie maszyny wirtualnej, a następnie ponów próbę przejścia w tryb failover.

## <a name="failover-failed-with-error-id-28092"></a>Nie można przełączyć do trybu failover z powodu błędu o IDENTYFIKATORze 28092

Site Recovery nie mógł utworzyć interfejsu sieciowego dla maszyny wirtualnej przełączonej w tryb failover. Upewnij się, że masz wystarczające przydziały dostępne do utworzenia interfejsów sieciowych w ramach subskrypcji. Dostępny limit przydziału można sprawdzić, przechodząc do opcji subskrypcja-> użycie i przydziały. Aby zwiększyć limit przydziału, możesz otworzyć [nowe żądanie pomocy technicznej](https://aka.ms/getazuresupport) . Jeśli masz wystarczający limit przydziału, może to być sporadyczny problem, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać nawet po ponownych próbach, pozostaw komentarz na końcu tego dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Nie można przełączyć do trybu failover z powodu błędu o IDENTYFIKATORze 70038

Site Recovery nie mógł utworzyć klasycznej maszyny wirtualnej w trybie failover na platformie Azure. Może się tak zdarzyć, ponieważ:

* Jeden z zasobów, takich jak sieć wirtualna, która jest wymagana do utworzenia maszyny wirtualnej, nie istnieje. Utwórz sieć wirtualną zgodnie z ustawieniami w obszarze Ustawienia obliczeń i sieci maszyny wirtualnej albo Zmodyfikuj ustawienie do sieci wirtualnej, która już istnieje, a następnie ponów próbę przełączenia w tryb failover.

## <a name="failover-failed-with-error-id-170010"></a>Nie można przełączyć do trybu failover z powodu błędu o IDENTYFIKATORze 170010

Site Recovery nie mógł utworzyć maszyny wirtualnej przełączonej w tryb failover na platformie Azure. Przyczyną może być fakt, że wewnętrzne działanie funkcji uzupełniania nie powiodło się dla lokalnej maszyny wirtualnej.

Aby można było wyświetlić wszystkie maszyny na platformie Azure, środowisko platformy Azure wymaga, aby niektóre sterowniki były w stanie uruchamiania rozruchu i usług takich jak DHCP, aby były w stanie autostartu. W ten sposób, podczas pracy w trybie failover, program konwertuje typ uruchomienia **sterowników ATAPI, Intelide, storflt, VMBus i storvsc** na uruchamianie rozruchu. Konwertuje również typ uruchomienia kilku usług, takich jak DHCP, na Autostart. To działanie może zakończyć się niepowodzeniem z powodu problemów specyficznych dla środowiska. 

Aby ręcznie zmienić typ uruchamiania sterowników dla **systemu operacyjnego gościa Windows**, wykonaj następujące czynności:

1. [Pobierz](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) skrypt No-odwodnieni i uruchom go w następujący sposób. Ten skrypt sprawdza, czy maszyna wirtualna wymaga odwodnienia.

    `.\Script-no-hydration.ps1`

    Daje następujący wynik, jeśli jest wymagane odwodnienie:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    This system doesn't meet no-hydration requirement.
    ```

    Jeśli maszyna wirtualna spełnia wymagania dotyczące braku odwodnienia, skrypt zwróci wynik "ten system spełnia wymagania dotyczące braku odwodnienia". W takim przypadku wszystkie sterowniki i usługi są w stanie, w jakim jest to wymagane przez platformę Azure, a odwodnienie na maszynie wirtualnej nie są wymagane.

2. Uruchom w następujący sposób skrypt No-odwodnieni-Set, jeśli maszyna wirtualna nie spełnia wymagań uzupełniania.

    `.\Script-no-hydration.ps1 -set`
    
    Spowoduje to przekonwertowanie typu uruchomienia sterowników i podaje wynik podobny do poniższego:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0

    This system is now no-hydration compatible.
    ```

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nie można nawiązać połączenia/RDP/SSH z maszyną wirtualną w trybie failover ze względu na szary przycisk Połącz na maszynie wirtualnej

Szczegółowe instrukcje dotyczące rozwiązywania problemów z protokołem RDP można znaleźć w [naszej dokumentacji.](../virtual-machines/troubleshooting/troubleshoot-rdp-connection.md)

Szczegółowe instrukcje dotyczące rozwiązywania problemów z protokołem SSH można znaleźć w [naszej dokumentacji.](../virtual-machines/troubleshooting/troubleshoot-ssh-connection.md)

Jeśli przycisk **Połącz** na maszynie wirtualnej w trybie failover na platformie Azure jest wyszarzony i nie masz połączenia z platformą Azure za pośrednictwem usługi Express Route lub sieci VPN typu lokacja-lokacja, a następnie

1. Przejdź do **sieci maszyn wirtualnych**  >  **Networking**, kliknij nazwę wymaganego interfejsu sieciowego.  ![Zrzut ekranu przedstawia stronę sieci maszyny wirtualnej z wybraną nazwą interfejsu sieciowego.](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Przejdź do opcji **konfiguracje IP**, a następnie kliknij pole Nazwa wymaganej konfiguracji protokołu IP. ![Zrzut ekranu przedstawia stronę konfiguracyjną I P dla interfejsu sieciowego z wybraną nazwą I P.](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Aby włączyć publiczny adres IP, kliknij pozycję **Włącz**. ![Włącz adres IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kliknij pozycję **Konfiguruj wymagane ustawienia**  >  **Utwórz nowy**. ![Utwórz nowy](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Wprowadź nazwę publicznego adresu, wybierz opcje domyślne dla **jednostki SKU** i **przypisania**, a następnie kliknij przycisk **OK**.
6. Teraz, aby zapisać wprowadzone zmiany, kliknij przycisk **Zapisz**.
7. Zamknij panele i przejdź do sekcji **Omówienie** maszyny wirtualnej, aby nawiązać połączenie/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nie można nawiązać połączenia/RDP/SSH — przycisk połączenia z maszyną wirtualną

Jeśli przycisk **Połącz** na maszynie wirtualnej w trybie failover na platformie Azure jest dostępny (nie jest wyszarzony), sprawdź **diagnostykę rozruchu** na maszynie wirtualnej i Sprawdź błędy wymienione w [tym artykule](../virtual-machines/troubleshooting/boot-diagnostics.md).

1. Jeśli maszyna wirtualna nie została uruchomiona, spróbuj przejść w tryb failover do starszego punktu odzyskiwania.
2. Jeśli aplikacja wewnątrz maszyny wirtualnej nie działa, spróbuj przejść w tryb failover do punktu odzyskiwania spójnego na poziomie aplikacji.
3. Jeśli maszyna wirtualna jest przyłączona do domeny, upewnij się, że kontroler domeny działa prawidłowo. Można to zrobić, wykonując następujące czynności:

    a. Utwórz nową maszynę wirtualną w tej samej sieci.

    b.  Upewnij się, że można przyłączyć się do tej samej domeny, w której oczekiwana jest maszyna wirtualna w trybie failover.

    c. Jeśli kontroler domeny **nie** działa prawidłowo, spróbuj zalogować się do maszyny wirtualnej przełączonej w tryb failover przy użyciu konta administratora lokalnego.
4. W przypadku korzystania z niestandardowego serwera DNS upewnij się, że jest on dostępny. Można to zrobić, wykonując następujące czynności:

    a. Utwórz nową maszynę wirtualną w tej samej sieci i

    b. Sprawdź, czy maszyna wirtualna może przeprowadzić rozpoznawanie nazw przy użyciu niestandardowego serwera DNS

>[!Note]
>Włączenie dowolnego ustawienia innego niż diagnostyka rozruchu wymaga zainstalowania agenta maszyny wirtualnej platformy Azure na maszynie wirtualnej przed przejściem w tryb failover

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Nie można otworzyć konsoli szeregowej po przejściu maszyny z interfejsem UEFI na platformę Azure do trybu failover

Jeśli można nawiązać połączenie z maszyną przy użyciu protokołu RDP, ale nie można otworzyć konsoli szeregowej, wykonaj następujące czynności:

* Jeśli system operacyjny komputera to Red Hat lub Oracle Linux 7. */8.0, uruchom następujące polecenie na maszynie wirtualnej platformy Azure w trybie failover z uprawnieniami głównymi. Uruchom ponownie maszynę wirtualną po poleceniu.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
  ```

* Jeśli system operacyjny maszyny to CentOS 7. *, uruchom następujące polecenie na maszynie wirtualnej platformy Azure w trybie failover z uprawnieniami głównymi. Uruchom ponownie maszynę wirtualną po poleceniu.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
  ```

## <a name="unexpected-shutdown-message-event-id-6008"></a>Nieoczekiwany komunikat zamknięcia (Identyfikator zdarzenia 6008)

W przypadku uruchamiania maszyny wirtualnej z systemem Windows po przejściu w tryb failover po odebraniu nieoczekiwanego komunikatu zamknięcia na odzyskiwanej maszynie wirtualnej oznacza to, że stan zamykania maszyny wirtualnej nie został przechwycony w punkcie odzyskiwania używanym do pracy w trybie failover. Dzieje się tak w przypadku odzyskiwania do punktu, gdy maszyna wirtualna nie została w pełni wyłączona.

Zwykle nie jest to przyczyną problemu i zazwyczaj można je zignorować dla nieplanowanych przełączeń w tryb failover. Jeśli zaplanowano przejście w tryb failover, należy upewnić się, że maszyna wirtualna została prawidłowo ZAMKNIĘTA przed przełączeniem w tryb failover i zapewni wystarczającą ilość czasu na wysłanie lokalnie oczekujących danych replikacji na platformę Azure. Następnie użyj **najnowszej** opcji na [ekranie trybu failover](site-recovery-failover.md#run-a-failover) , aby wszystkie oczekujące dane na platformie Azure były przetwarzane do punktu odzyskiwania, który jest następnie używany do pracy w trybie failover maszyny wirtualnej.

## <a name="unable-to-select-the-datastore"></a>Nie można wybrać magazynu danych

Ten problem jest wskazywany, gdy nie można wyświetlić magazynu danych w usłudze Azure Portal podczas próby ponownego włączenia ochrony maszyny wirtualnej, która wystąpiła w trybie failover. Wynika to z faktu, że główny element docelowy nie został rozpoznany jako maszyna wirtualna w obszarze vCenters dodanej do Azure Site Recovery.

Aby uzyskać więcej informacji na temat ponownej ochrony maszyny wirtualnej, zobacz Ponowne [Włączanie ochrony i przywracanie maszyn w lokacji lokalnej po przejściu do trybu failover na platformie Azure](vmware-azure-reprotect.md).

W celu rozwiązania tego problemu:

Ręcznie Utwórz główny element docelowy w programie vCenter, który zarządza maszyną źródłową. Magazyn danych będzie dostępny po następnej operacji odnajdywania vCenter i odświeżania sieci szkieletowej.

> [!Note]
> 
> Operacje odnajdywania i odświeżania sieci szkieletowej mogą potrwać do 30 minut. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>Rejestracja głównego elementu docelowego systemu Linux z usługą CS kończy się niepowodzeniem z powodu błędu TLS 35 

Rejestracja głównego elementu docelowego Azure Site Recovery z serwerem konfiguracji kończy się niepowodzeniem z powodu włączenia uwierzytelnionego serwera proxy w głównym elemencie docelowym. 
 
Ten błąd jest wskazywany przez następujące ciągi w dzienniku instalacji: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

W celu rozwiązania tego problemu:
 
1. Na maszynie wirtualnej serwera konfiguracji Otwórz wiersz polecenia i sprawdź ustawienia serwera proxy przy użyciu następujących poleceń:

    /etc/environment $http echo _proxy echo $https _proxy 

2. Jeśli dane wyjściowe poprzednich poleceń pokazują, że zdefiniowano http_proxy lub https_proxy ustawienia, użyj jednej z następujących metod, aby odblokować główną komunikację docelową z serwerem konfiguracji:
   
   - Pobierz [Narzędzie PsExec](/sysinternals/downloads/psexec).
   - Użyj narzędzia, aby uzyskać dostęp do kontekstu użytkownika systemu i określić, czy adres serwera proxy jest skonfigurowany. 
   - Jeśli skonfigurowano serwer proxy, Otwórz program IE w kontekście użytkownika systemu przy użyciu narzędzia PsExec.
  
     **PsExec-s-i "użycie ścieżki%ProgramFiles%\Internet Explorer\iexplore.exe"**

   - Aby upewnić się, że główny serwer docelowy może komunikować się z serwerem konfiguracji:
  
     - Zmodyfikuj ustawienia serwera proxy w programie Internet Explorer, aby pominąć docelowy adres IP serwera za pomocą serwera proxy.   
     Lub
     - Wyłącz serwer proxy na głównym serwerze docelowym. 


## <a name="next-steps"></a>Następne kroki
- Rozwiązywanie problemów z [połączeniem RDP z maszyną wirtualną z systemem Windows](../virtual-machines/troubleshooting/troubleshoot-rdp-connection.md)
- Rozwiązywanie problemów z [połączeniem SSH z maszyną wirtualną z systemem Linux](../virtual-machines/troubleshooting/detailed-troubleshoot-ssh-connection.md)

Jeśli potrzebujesz więcej pomocy, Opublikuj zapytanie na [stronie pytania firmy&Microsoft w celu Site Recovery](/answers/topics/azure-site-recovery.html) lub pozostaw komentarz na końcu tego dokumentu. Mamy aktywną społeczność, która powinna być w stanie pomóc.