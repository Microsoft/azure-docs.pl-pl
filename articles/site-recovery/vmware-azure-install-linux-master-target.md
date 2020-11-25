---
title: Zainstaluj główny serwer docelowy na potrzeby powrotu po awarii maszyny wirtualnej z systemem Linux przy użyciu Azure Site Recovery
description: Dowiedz się, jak skonfigurować główny serwer docelowy z systemem Linux na potrzeby powrotu po awarii do lokacji lokalnej podczas odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: mayg
ms.openlocfilehash: 9e1008f7acbfe0685b7a171176c7dc54592d1491
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019246"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalowanie głównego serwera docelowego z systemem Linux na potrzeby powrotu po awarii
Po przełączeniu maszyn wirtualnych w tryb failover na platformę Azure można wrócić do trybu failover maszyn wirtualnych w lokacji lokalnej. Aby powrócić po awarii, należy ponownie włączyć ochronę maszyny wirtualnej z platformy Azure do lokacji lokalnej. W przypadku tego procesu wymagany jest lokalny główny serwer docelowy do odbierania ruchu. 

Jeśli chroniona maszyna wirtualna jest maszyną wirtualną z systemem Windows, potrzebny jest główny serwer docelowy systemu Windows. W przypadku maszyny wirtualnej z systemem Linux wymagany jest główny serwer docelowy systemu Linux. Przeczytaj poniższe kroki, aby dowiedzieć się, jak utworzyć i zainstalować główny obiekt docelowy systemu Linux.

> [!IMPORTANT]
> Począwszy od wersji głównego serwera docelowego 9.10.0, najnowszy główny serwer docelowy można zainstalować tylko na serwerze Ubuntu 16,04. Nowe instalacje nie są dozwolone na serwerach CentOS 16,8. Można jednak kontynuować uaktualnianie starych głównych serwerów docelowych przy użyciu wersji 9.10.0.
> Główny serwer docelowy w systemie LVM nie jest obsługiwany.

## <a name="overview"></a>Omówienie
Ten artykuł zawiera instrukcje dotyczące instalowania głównego elementu docelowego systemu Linux.

Zamieszczaj komentarze lub pytania na końcu tego artykułu lub na [stronie pytań firmy Microsoft na&pytania dotyczące usługi Azure Recovery Services](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby wybrać hosta, na którym ma zostać wdrożony główny element docelowy, ustal, czy powrót po awarii ma nastąpić do istniejącej lokalnej maszyny wirtualnej lub nowej maszyny wirtualnej. 
    * Dla istniejącej maszyny wirtualnej Host głównego elementu docelowego powinien mieć dostęp do magazynów danych maszyny wirtualnej.
    * Jeśli lokalna maszyna wirtualna nie istnieje (w przypadku odzyskiwania alternatywnej lokalizacji), maszyna wirtualna powrotu po awarii jest tworzona na tym samym hoście co główny serwer docelowy. Możesz wybrać dowolnego hosta ESXi, aby zainstalować główny cel.
* Główny element docelowy powinien znajdować się w sieci, która może komunikować się z serwerem przetwarzania i serwerem konfiguracji.
* Wersja głównego elementu docelowego musi być równa lub wcześniejsza niż wersja serwera przetwarzania i serwera konfiguracji. Na przykład jeśli wersja serwera konfiguracji to 9,4, wersja głównego celu może być 9,4 lub 9,3, ale nie 9,5.
* Główny element docelowy może być tylko maszyną wirtualną VMware, a nie serwerem fizycznym.

> [!NOTE]
> Upewnij się, że nie włączasz vMotion magazynu na żadnym składniku zarządzania, takim jak główny element docelowy. Jeśli główny element docelowy zostanie przesunięty po pomyślnym ponownej ochronie, dyski maszyny wirtualnej (VMDK) nie mogą zostać odłączone. W takim przypadku powrót po awarii kończy się niepowodzeniem.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Wskazówki dotyczące ustalania wielkości na potrzeby tworzenia głównego serwera docelowego

Utwórz główny obiekt docelowy zgodnie z następującymi wskazówkami dotyczącymi ustalania wielkości:
- **Pamięć RAM**: 6 GB lub więcej
- **Rozmiar dysku systemu operacyjnego**: 100 GB lub więcej (aby zainstalować system operacyjny)
- **Dodatkowy rozmiar dysku dla dysku przechowywania**: 1 TB
- **Rdzenie procesora CPU**: 4 rdzenie lub więcej
- **Jądro**: 4,16. *

## <a name="deploy-the-master-target-server"></a>Wdróż główny serwer docelowy

### <a name="install-ubuntu-16042-minimal"></a>Zainstaluj Ubuntu 16.04.2 minimum

Wykonaj następujące kroki, aby zainstalować system operacyjny Ubuntu 16.04.2 64-bitowy.

1.   Przejdź do [linku pobierania](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), wybierz najbliższy duplikat i Pobierz Ubuntu 16.04.2 minimalny 64-bitowy ISO.
Zachowaj Ubuntu 16.04.2 minimalny 64-bitowy ISO w stacji dysków DVD i uruchom system.

1.  Wybierz opcję **angielski** jako preferowany język, a następnie wybierz **klawisz ENTER**.
    
    ![Wybierz język](./media/vmware-azure-install-linux-master-target/image1.png)
1. Wybierz pozycję **Zainstaluj serwer Ubuntu**, a następnie wybierz klawisz **Enter**.

    ![Wybierz pozycję Zainstaluj serwer Ubuntu](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Wybierz opcję **angielski** jako preferowany język, a następnie wybierz **klawisz ENTER**.

    ![Wybierz angielski jako preferowany język](./media/vmware-azure-install-linux-master-target/image3.png)

1. Wybierz odpowiednią opcję z listy opcje **strefy czasowej** , a następnie wybierz **klawisz ENTER**.

    ![Wybierz prawidłową strefę czasową](./media/vmware-azure-install-linux-master-target/image4.png)

1. Wybierz pozycję **nie** (opcja domyślna), a następnie wybierz klawisz **Enter**.

     ![Konfigurowanie klawiatury](./media/vmware-azure-install-linux-master-target/image5.png)
1. Wybierz opcję **angielski (Stany Zjednoczone)** jako kraj/region pochodzenia dla klawiatury, a następnie wybierz **klawisz ENTER**.

1. Wybierz opcję **angielski (US)** jako układ klawiatury, a następnie wybierz klawisz **Enter**.

1. Wprowadź nazwę hosta serwera w polu **Nazwa hosta** , a następnie wybierz pozycję **Kontynuuj**.

1. Aby utworzyć konto użytkownika, wprowadź nazwę użytkownika, a następnie wybierz pozycję **Kontynuuj**.

      ![Tworzenie konta użytkownika](./media/vmware-azure-install-linux-master-target/image9.png)

1. Wprowadź hasło dla nowego konta użytkownika, a następnie wybierz pozycję **Kontynuuj**.

1.  Potwierdź hasło dla nowego użytkownika, a następnie wybierz pozycję **Kontynuuj**.

    ![Potwierdź hasła](./media/vmware-azure-install-linux-master-target/image11.png)

1.  W następnym zaznaczeniu w celu zaszyfrowania katalogu macierzystego wybierz pozycję **nie** (opcja domyślna), a następnie wybierz **klawisz ENTER**.

1. Jeśli wyświetlana strefa czasowa jest poprawna, wybierz opcję **tak** (opcja domyślna), a następnie wybierz **klawisz ENTER**. Aby zmienić konfigurację strefy czasowej, wybierz pozycję **nie**.

1. Na stronie opcje partycjonowania wybierz opcję z **przewodnikiem — Użyj całego dysku**, a następnie wybierz **klawisz ENTER**.

     ![Wybierz opcję partycjonowania metody](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Wybierz odpowiedni dysk z opcji **Wybierz dysk do partycjonowania** , a następnie wybierz klawisz **Enter**.

    ![Wybierz dysk](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Wybierz pozycję **tak** , aby zapisać zmiany na dysku, a następnie wybierz **klawisz ENTER**.

    ![Wybierz opcję domyślną](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  W obszarze Konfiguruj wybór serwera proxy wybierz opcję domyślną, wybierz pozycję **Kontynuuj**, a następnie wybierz klawisz **Enter**.
     
     ![Zrzut ekranu pokazujący, gdzie wybrać pozycję Kontynuuj, a następnie wybierz klawisz ENTER.](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Wybierz opcję **Brak opcji aktualizacje automatyczne** w wyborze do zarządzania uaktualnieniami w systemie, a następnie wybierz klawisz **Enter**.

     ![Wybierz sposób zarządzania uaktualnieniami](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Ponieważ główny serwer docelowy Azure Site Recovery wymaga bardzo konkretnej wersji Ubuntu, należy upewnić się, że uaktualnienia jądra są wyłączone dla maszyny wirtualnej. Jeśli są włączone, wszystkie regularne uaktualnienia powodują, że główny serwer docelowy będzie działać nieprawidłowo. Upewnij się, że wybrano opcję **bez aktualizacji automatycznych** .

1.  Wybierz opcje domyślne. Jeśli chcesz, aby program openSSH dla połączenia SSH, wybierz opcję **serwer OpenSSH** , a następnie wybierz pozycję **Kontynuuj**.

    ![Wybieranie oprogramowania](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. W obszarze wyboru do instalacji modułu ładującego GRUB Boot wybierz pozycję **tak**, a następnie wybierz **klawisz ENTER**.
     
    ![Instalator rozruchu GRUB](./media/vmware-azure-install-linux-master-target/image20.png)


1. Wybierz odpowiednie urządzenie do instalacji modułu ładującego rozruchu (najlepiej **/dev/SDA**), a następnie wybierz **klawisz ENTER**.
     
    ![Wybierz odpowiednie urządzenie](./media/vmware-azure-install-linux-master-target/image21.png)

1. Wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **Enter** , aby zakończyć instalację.

    ![Zakończ instalację](./media/vmware-azure-install-linux-master-target/image22.png)

1. Po zakończeniu instalacji zaloguj się do maszyny wirtualnej przy użyciu nowych poświadczeń użytkownika. (Aby uzyskać więcej informacji, zobacz **krok 10** ).

1. Wykonaj kroki opisane na poniższym zrzucie ekranu, aby ustawić hasło użytkownika ROOT. Następnie zaloguj się jako użytkownik ROOT.

    ![Ustawianie hasła użytkownika ROOT](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Skonfiguruj maszynę jako główny serwer docelowy

Aby uzyskać identyfikator dla każdego dysku twardego SCSI na maszynie wirtualnej z systemem Linux, **dysk. Należy włączyć parametr EnableUUID = TRUE** . Aby włączyć ten parametr, wykonaj następujące czynności:

1. Zamknij maszynę wirtualną.

2. W lewym okienku kliknij prawym przyciskiem myszy wpis maszyny wirtualnej, a następnie wybierz pozycję **Edytuj ustawienia**.

3. Wybierz kartę **Opcje** .

4. W okienku po lewej stronie wybierz pozycję **Zaawansowane**  >  **Ogólne**, a następnie wybierz przycisk **Parametry konfiguracji** w prawej dolnej części ekranu.

    ![Otwórz parametr konfiguracji](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Opcja **Parametry konfiguracji** jest niedostępna, gdy maszyna jest uruchomiona. Aby uaktywnić tę kartę, Zamknij maszynę wirtualną.

5. Sprawdź, czy wiersz z **dyskiem. EnableUUID** już istnieje.

   - Jeśli wartość istnieje i jest ustawiona na **false**, Zmień wartość na **true**. (W wartościach nie jest rozróżniana wielkość liter).

   - Jeśli wartość istnieje i jest ustawiona na **wartość true**, wybierz pozycję **Anuluj**.

   - Jeśli wartość nie istnieje, wybierz pozycję **Dodaj wiersz**.

   - W kolumnie Nazwa Dodaj **dysk. EnableUUID**, a następnie ustaw wartość na **true**.

     ![Sprawdzanie, czy dysk jest. EnableUUID już istnieje](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Wyłącz uaktualnienia jądra

Główny serwer docelowy Azure Site Recovery wymaga określonej wersji Ubuntu, upewnij się, że uaktualnienia jądra są wyłączone dla maszyny wirtualnej. Jeśli uaktualnienia jądra są włączone, może to spowodować nieprawidłowe działanie głównego serwera docelowego.

#### <a name="download-and-install-additional-packages"></a>Pobierz i zainstaluj dodatkowe pakiety

> [!NOTE]
> Upewnij się, że masz połączenie z Internetem, aby pobrać i zainstalować dodatkowe pakiety. Jeśli nie masz łączności z Internetem, musisz ręcznie znaleźć te pakiety DEB i zainstalować je.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Pobierz instalatora na potrzeby instalacji

Jeśli główny serwer docelowy ma łączność z Internetem, możesz wykonać poniższe kroki, aby pobrać Instalatora. W przeciwnym razie można skopiować Instalatora z serwera przetwarzania, a następnie zainstalować go.

#### <a name="download-the-master-target-installation-packages"></a>Pobierz pakiety instalacji głównego elementu docelowego

[Pobierz najnowsze bity instalacji głównego elementu docelowego systemu Linux](https://aka.ms/latestlinuxmobsvc).

Aby pobrać go przy użyciu systemu Linux, wpisz:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Pamiętaj, aby pobrać i rozpakować Instalatora w katalogu macierzystym. Po rozpakować do **/usr/local**, instalacja nie powiedzie się.


#### <a name="access-the-installer-from-the-process-server"></a>Uzyskiwanie dostępu do Instalatora z serwera przetwarzania

1. Na serwerze przetwarzania przejdź do **lokalizacji C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Skopiuj wymagany plik Instalatora z serwera przetwarzania i Zapisz go jako **latestlinuxmobsvc. tar. gz** w katalogu macierzystym.


### <a name="apply-custom-configuration-changes"></a>Zastosuj niestandardowe zmiany konfiguracji

Aby zastosować niestandardowe zmiany konfiguracji, wykonaj następujące czynności jako użytkownik główny:

1. Uruchom następujące polecenie, aby untar dane binarne.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Zrzut ekranu przedstawiający polecenie do uruchomienia](./media/vmware-azure-install-linux-master-target/image16.png)

2. Uruchom następujące polecenie, aby udzielić uprawnienia.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Uruchom poniższe polecenie, aby uruchomić skrypt.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Uruchom skrypt tylko raz na serwerze. Następnie zamknij serwer. Po dodaniu dysku należy ponownie uruchomić serwer, zgodnie z opisem w następnej sekcji.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Dodawanie dysku przechowywania do głównej docelowej maszyny wirtualnej systemu Linux

Wykonaj następujące kroki, aby utworzyć dysk przechowywania:

1. Dołącz nowy dysk o pojemności 1 TB do głównej maszyny wirtualnej z systemem Linux, a następnie uruchom maszynę.

2. Użyj **wielościeżkowego** polecenia, aby poznać identyfikator wielościeżkowy dysku przechowywania: **wielościeżkowy — wszystkie**

    ![Identyfikator wielu ścieżek](./media/vmware-azure-install-linux-master-target/image27.png)

3. Sformatuj dysk, a następnie utwórz system plików na nowym dysku: **mkfs. ext4/dev/mapper/ \<Retention disk's multipath id>**.
    
    ![System plików](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Po utworzeniu systemu plików Zainstaluj dysk przechowywania.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Utwórz wpis **fstab** , aby zainstalować dysk przechowywania za każdym razem, gdy system zostanie uruchomiony.
    
    `vi /etc/fstab`
    
    Wybierz pozycję **Wstaw** , aby rozpocząć edycję pliku. Utwórz nowy wiersz, a następnie Wstaw poniższy tekst. Edytuj identyfikator wielościeżkowy dysku na podstawie wyróżnionego identyfikatora wielościeżkowego z poprzedniego polecenia.

    **/dev/mapper/ \<Retention disks multipath id> /mnt/Retention ext4 rw 0 0**

    Wybierz **klawisz ESC**, a następnie wpisz **: WQ** (Zapisz i Wyjdź), aby zamknąć okno edytora.

### <a name="install-the-master-target"></a>Zainstaluj główny element docelowy

> [!IMPORTANT]
> Wersja głównego serwera docelowego musi być równa lub wcześniejsza niż wersja serwera przetwarzania i serwera konfiguracji. Jeśli ten warunek nie zostanie spełniony, ponowne włączenie ochrony zakończy się pomyślnie, ale replikacja nie powiedzie się.


> [!NOTE]
> Przed zainstalowaniem głównego serwera docelowego Sprawdź, czy plik **/etc/hosts** na maszynie wirtualnej zawiera wpisy, które mapują lokalną nazwę hosta na adresy IP skojarzone ze wszystkimi kartami sieciowymi.

1. Uruchom następujące polecenie, aby zainstalować główny serwer docelowy.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    ```

2. Skopiuj hasło z **witryny C:\ProgramData\Microsoft Azure Site Recovery\private\connection.Passphrase** na serwerze konfiguracji. Następnie zapisz go jako **passphrase.txt** w tym samym katalogu lokalnym, uruchamiając następujące polecenie:

    `echo <passphrase> >passphrase.txt`

    Przykład: 

    `echo itUx70I47uxDuUVY >passphrase.txt`
    

3. Zanotuj adres IP serwera konfiguracji. Uruchom następujące polecenie, aby zarejestrować serwer na serwerze konfiguracji.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Przykład: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Poczekaj na zakończenie działania skryptu. Jeśli główny serwer docelowy rejestruje się prawidłowo, główny element docelowy zostanie wyświetlony na stronie **infrastruktura Site Recovery** w portalu.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalowanie głównego elementu docelowego przy użyciu instalacji interaktywnej

1. Uruchom następujące polecenie, aby zainstalować główny serwer docelowy. W polu Rola agenta wybierz pozycję **główny element docelowy**.

    ```
    ./install
    ```

2. Wybierz domyślną lokalizację instalacji, a następnie wybierz **klawisz ENTER** , aby kontynuować.

    ![Wybieranie domyślnej lokalizacji instalacji głównego elementu docelowego](./media/vmware-azure-install-linux-master-target/image17.png)

Po zakończeniu instalacji zarejestruj serwer konfiguracji przy użyciu wiersza polecenia.

1. Zanotuj adres IP serwera konfiguracji. Będzie ona potrzebna w następnym kroku.

2. Uruchom następujące polecenie, aby zarejestrować serwer na serwerze konfiguracji.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh
    ```

     Poczekaj na zakończenie działania skryptu. Jeśli główny element docelowy został pomyślnie zarejestrowany, główny element docelowy zostanie wyświetlony na stronie **infrastruktura Site Recovery** w portalu.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Zainstaluj narzędzia VMware Tools/Open-VM-Tools na głównym serwerze docelowym

Aby można było odnaleźć magazyny danych, należy zainstalować narzędzia VMware lub Otwórz maszynę wirtualną na głównym serwerze docelowym. Jeśli narzędzia nie są zainstalowane, ekran Ponowne włączanie ochrony nie znajduje się na liście w obszarze magazyny danych. Po zakończeniu instalacji narzędzi VMware należy ponownie uruchomić program.

### <a name="upgrade-the-master-target-server"></a>Uaktualnianie głównego serwera docelowego

Uruchom instalatora. Automatycznie wykryje, że Agent jest zainstalowany na głównym serwerze docelowym. W celu uaktualnienia wybierz pozycję **Y**.  Po zakończeniu instalacji sprawdź, czy wersja głównego elementu docelowego została zainstalowana przy użyciu następującego polecenia:

`cat /usr/local/.vx_version`


Zobaczysz, że w polu **wersja** nadano numer wersji głównego elementu docelowego.

## <a name="common-issues"></a>Typowe problemy

* Upewnij się, że nie włączasz vMotion magazynu na żadnym składniku zarządzania, takim jak główny element docelowy. Jeśli główny element docelowy zostanie przesunięty po pomyślnym ponownej ochronie, dyski maszyny wirtualnej (VMDK) nie mogą zostać odłączone. W takim przypadku powrót po awarii kończy się niepowodzeniem.

* Główny element docelowy nie powinien mieć żadnych migawek na maszynie wirtualnej. Jeśli istnieją migawki, powrót po awarii kończy się niepowodzeniem.

* Ze względu na niestandardową konfigurację karty sieciowej interfejs sieciowy jest wyłączony podczas uruchamiania, a główny agent docelowy nie może zostać zainicjowany. Upewnij się, że następujące właściwości zostały prawidłowo ustawione. Sprawdź te właściwości w/etc/network/interfaces. pliku karty Ethernet
    * eth0
    * iface eth0 inet DHCP <br>

    Uruchom ponownie usługę sieciową przy użyciu następującego polecenia: <br>

`sudo systemctl restart networking`


## <a name="next-steps"></a>Następne kroki
Po zakończeniu instalacji i rejestracji głównego elementu docelowego można zobaczyć, że główny element docelowy pojawia się w **głównej sekcji docelowej** w **infrastrukturze Site Recovery**, w obszarze przegląd serwera konfiguracji.

Teraz możesz kontynuować ponowną [ochronę](vmware-azure-reprotect.md), a następnie powrócić po awarii.

