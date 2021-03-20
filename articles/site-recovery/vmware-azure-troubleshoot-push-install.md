---
title: Rozwiązywanie problemów z instalacją wypychaną usługi mobilności za pomocą Azure Site Recovery
description: Rozwiązywanie problemów z instalacją usług mobilności podczas włączania replikacji na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98127876"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Rozwiązywanie problemów z instalacją wypychaną usługi mobilności

Instalacja usługi mobilności jest kluczowym krokiem umożliwiającym replikację. Powodzenie tego kroku zależy od wymagań wstępnych dotyczących spotkań i pracy z obsługiwanymi konfiguracjami. Najczęstsze błędy, które można napotkać podczas instalacji usługi mobilności, są następujące:

* [Błędy poświadczeń/uprawnień](#credentials-check-errorid-95107--95108)
* [Błędy logowania](#login-failures-errorid-95519-95520-95521-95522)
* [Błędy łączności](#connectivity-failure-errorid-95117--97118)
* [Błędy udostępniania plików i drukarek](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Błędy usługi Instrumentacja zarządzania Windows (WMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nieobsługiwane systemy operacyjne](#unsupported-operating-systems)
* [Nieobsługiwane konfiguracje rozruchowe](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Błędy instalacji usługi kopiowania woluminów w tle (VSS)](#vss-installation-failures)
* [Nazwa urządzenia w konfiguracji GRUB zamiast identyfikatora UUID urządzenia](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Wolumin Menedżera woluminów logicznych (LVM)](#lvm-support-from-920-version)
* [Ostrzeżenia dotyczące ponownego uruchamiania](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Po włączeniu replikacji Azure Site Recovery próbuje zainstalować agenta usługi mobilności na maszynie wirtualnej (VM). W ramach tego procesu serwer konfiguracji próbuje nawiązać połączenie z maszyną wirtualną i skopiować agenta. Aby włączyć pomyślną instalację, postępuj zgodnie ze wskazówkami dotyczącymi rozwiązywania problemów krok po kroku.

## <a name="credentials-check-errorid-95107--95108"></a>Sprawdzanie poświadczeń (ErrorID: 95107 & 95108)

Sprawdź, czy konto użytkownika wybrane podczas włączania replikacji jest prawidłowe i dokładne. Azure Site Recovery wymaga konta **głównego** lub konta użytkownika z **uprawnieniami administratora** do przeprowadzenia instalacji wypychanej. W przeciwnym razie instalacja wypychana zostanie zablokowana na maszynie źródłowej.

W przypadku systemu Windows (**błąd 95107**) Upewnij się, że konto użytkownika ma dostęp administracyjny na komputerze źródłowym, przy użyciu konta lokalnego lub konta domeny. Jeśli nie korzystasz z konta domeny, musisz wyłączyć kontrolę dostępu użytkowników zdalnych na komputerze lokalnym.

* Aby ręcznie dodać klucz rejestru, który wyłącza kontrolę dostępu użytkowników zdalnych:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Dodaj nową `DWORD` : `LocalAccountTokenFilterPolicy`
  * Ustaw wartość na `1`

* Aby dodać klucz rejestru, w wierszu polecenia Uruchom następujące polecenie:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

W przypadku systemu Linux (**błąd 95108**) należy wybrać konto **Główne** dla pomyślnej instalacji agenta usługi mobilności. Ponadto należy uruchomić usługi SSH protokół transferu plików (SFTP). Aby włączyć podsystem SFTP i uwierzytelnianie hasłem w pliku _sshd_config_ :

1. Zaloguj się jako użytkownik **root**.
1. Przejdź do _pliku/etc/ssh/sshd_config_, Znajdź wiersz zaczynający się od `PasswordAuthentication` .
1. Usuń komentarz z wiersza i zmień wartość na `yes` .
1. Znajdź wiersz zaczynający się od `Subsystem` i Usuń komentarz z wiersza.
1. Uruchom ponownie `sshd` usługę.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Błąd niewystarczających uprawnień (ErrorID: 95517)

Gdy użytkownik wybrany do zainstalowania agenta mobilności nie ma uprawnień administratora, serwer konfiguracji/skalowalny w poziomie serwer przetwarzania nie będzie mógł skopiować oprogramowania agenta mobilności na maszynę źródłową. Ten błąd jest wynikiem błędu odmowy dostępu. Upewnij się, że konto użytkownika ma uprawnienia administratora.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Błąd niewystarczających uprawnień (ErrorID: 95518)

Gdy ustanowienie relacji zaufania domeny między domeną podstawową a stacją roboczą nie powiedzie się podczas próby zalogowania się na maszynie źródłowej, Instalacja agenta mobilności kończy się niepowodzeniem z powodu błędu o IDENTYFIKATORze 95518. Upewnij się, że konto użytkownika używane do instalowania agenta mobilności ma uprawnienia administracyjne do logowania się za pomocą domeny podstawowej maszyny źródłowej.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Niepowodzenia logowania (ErrorID: 95519, 95520, 95521, 95522)

W tej części opisano poświadczenia i komunikaty o błędach logowania.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Poświadczenia konta użytkownika zostały wyłączone (ErrorID: 95519)

Konto użytkownika wybrane podczas włączania replikacji zostało wyłączone. Aby włączyć konto użytkownika, zapoznaj się z [tym artykułem](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) lub uruchom następujące polecenie, zastępując tekst _username_ nazwą rzeczywistą.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Poświadczenia zostały zablokowane z powodu wielu nieudanych prób zalogowania (ErrorID: 95520)

Wiele nieudanych prób uzyskania dostępu do maszyny spowoduje zablokowanie konta użytkownika. Przyczyną błędu może być:

* Poświadczenia podane podczas instalacji konfiguracji są nieprawidłowe.
* Konto użytkownika wybrane podczas włączania replikacji jest nieprawidłowe.

Zmodyfikuj poświadczenia wybrane przez następujące [instrukcje](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) , a następnie spróbuj ponownie wykonać operację.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Serwery logowania nie są dostępne na maszynie źródłowej (ErrorID: 95521)

Ten błąd występuje, gdy serwery logowania nie są dostępne na maszynie źródłowej. Jeśli serwery logowania nie są dostępne, żądania logowania będą kończyć się niepowodzeniem i nie będzie można zainstalować agenta mobilności. W celu pomyślnego zalogowania się upewnij się, że serwery logowania są dostępne na maszynie źródłowej i uruchom usługę Netlogon. Aby uzyskać więcej informacji, zobacz [scenariusze logowania systemu Windows](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Usługa logowania nie jest uruchomiona na maszynie źródłowej (ErrorID: 95522)

Usługa logowania nie jest uruchomiona na maszynie źródłowej i spowodowała błąd żądania logowania. Nie można zainstalować agenta mobilności. Aby rozwiązać ten problem, użyj jednej z następujących metod, aby uruchomić `Netlogon` usługę na maszynie źródłowej:

* Aby uruchomić `Netlogon` usługę z wiersza polecenia, uruchom polecenie `net start Netlogon` .
* W Menedżerze zadań Uruchom `Netlogon` usługę.

## <a name="connectivity-failure-errorid-95117--97118"></a>Niepowodzenie łączności (ErrorID: 95117 & 97118)

Serwer konfiguracji/serwer przetwarzania skalowalnego w poziomie próbuje nawiązać połączenie ze źródłową maszyną wirtualną w celu zainstalowania agenta mobilności. Ten błąd występuje, gdy maszyna źródłowa jest nieosiągalna z powodu problemów z łącznością sieciową.

Aby rozwiązać ten problem:

* Upewnij się, że konto użytkownika ma dostęp administracyjny na komputerze źródłowym, przy użyciu konta lokalnego lub konta domeny. Jeśli nie korzystasz z konta domeny, musisz wyłączyć kontrolę dostępu użytkowników zdalnych na komputerze lokalnym.
  * Aby ręcznie dodać klucz rejestru, który wyłącza kontrolę dostępu użytkowników zdalnych:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * Dodaj nową `DWORD` : `LocalAccountTokenFilterPolicy`
    * Ustaw wartość na `1`
  * Aby dodać klucz rejestru, w wierszu polecenia Uruchom następujące polecenie:

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* Upewnij się, że można wysłać polecenie ping do maszyny źródłowej z serwera konfiguracji. Jeśli podczas włączania replikacji wybrano serwer przetwarzania skalowalny w poziomie, upewnij się, że można wysłać polecenie ping do maszyny źródłowej z serwera przetwarzania.

* Upewnij się, że na maszynie wirtualnej jest włączona usługa udostępniania plików i drukarek. Sprawdź kroki opisane [tutaj](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106).

* Upewnij się, że na maszynie wirtualnej jest włączona usługa WMI. Sprawdź kroki opisane [tutaj](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103).

* Upewnij się, że sieciowe foldery udostępnione na maszynie wirtualnej są dostępne z serwera przetwarzania. Sprawdź kroki opisane [tutaj](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523).

* W wierszu polecenia komputera źródłowego należy użyć usługi `Telnet` do pingowania serwera konfiguracji lub skalowalnego w poziomie serwera przetwarzania na porcie HTTPS 135, jak pokazano w poniższym poleceniu. To polecenie sprawdza, czy występują problemy z łącznością sieciową lub problemy z blokowaniem portów zapory.

  `telnet <CS/ scale-out PS IP address> <135>`

* Ponadto w przypadku maszyny wirtualnej z systemem Linux:
  * Sprawdź, czy są zainstalowane najnowsze pakiety OpenSSH, OpenSSH Server i OpenSSL.
  * Sprawdź i upewnij się, że Secure Shell (SSH) jest włączony i jest uruchomiony na porcie 22.
  * Usługa SFTP powinna być uruchomiona. Aby włączyć podsystem SFTP i uwierzytelnianie hasłem w pliku _sshd_config_ :

    1. Zaloguj się jako użytkownik **root**.
    1. Przejdź do pliku _/etc/ssh/sshd_config_ , Znajdź wiersz zaczynający się od `PasswordAuthentication` .
    1. Usuń komentarz z wiersza i zmień wartość na `yes` .
    1. Znajdź wiersz zaczynający się od `Subsystem` i usuń znaczniki komentarza z wiersza
    1. Uruchom ponownie `sshd` usługę.

* Próba nawiązania połączenia nie powiodła się w przypadku braku odpowiednich odpowiedzi po upływie określonego czasu lub nawiązane połączenie nie powiodło się z powodu niepowodzenia odpowiedzi podłączonego hosta.
* Może to być problem związany z łącznością/siecią/domeną. Może to być spowodowane tym, że rozpoznawanie nazw DNS lub problem z wyczerpaniem portów TCP. Sprawdź, czy w Twojej domenie występują jakieś znane problemy.

## <a name="connectivity-failure-errorid-95523"></a>Niepowodzenie łączności (ErrorID: 95523)

Ten błąd występuje, gdy sieć, w której znajduje się maszyna źródłowa nie została znaleziona, mogła zostać usunięta lub nie jest już dostępna. Jedynym sposobem na rozwiązanie tego błędu jest upewnienie się, że sieć istnieje.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>Sprawdź dostęp do udostępnionych folderów sieciowych na maszynie źródłowej (ErrorID: 95105, 95523)

Sprawdź, czy sieciowe foldery udostępnione na maszynie wirtualnej są dostępne zdalnie z serwera przetwarzania (PS) przy użyciu określonych poświadczeń. Aby potwierdzić dostęp: 

1. Zaloguj się do maszyny serwera przetwarzania.
2. Otwórz Eksploratora plików. Na pasku adresu wpisz `\\<SOURCE-MACHINE-IP>\C$` i kliknij klawisz ENTER.

    ![Otwórz folder w PS](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. Eksplorator plików wyświetli monit o podanie poświadczeń. Wprowadź nazwę użytkownika i hasło, a następnie kliknij przycisk OK. <br><br/>

    ![Podaj poświadczenia](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > Jeśli maszyna źródłowa jest przyłączona do domeny, podaj nazwę domeny oraz nazwę użytkownika jako `<domainName>\<username>` . Jeśli maszyna źródłowa znajduje się w grupie roboczej, podaj tylko nazwę użytkownika.

4. W przypadku pomyślnego nawiązania połączenia foldery maszyny źródłowej będą widoczne zdalnie z serwera przetwarzania.

    ![Widoczne foldery z maszyny źródłowej](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

Jeśli połączenie nie powiedzie się, sprawdź, czy zostały spełnione wszystkie wymagania wstępne.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Sprawdzenie usług udostępniania plików i drukarek (ErrorID: 95105 & 95106)

Po sprawdzeniu łączności Sprawdź, czy na maszynie wirtualnej jest włączona usługa udostępniania plików i drukarek. Te ustawienia są wymagane do skopiowania agenta mobilności na maszynę źródłową.

W przypadku **systemu Windows 2008 R2 i wcześniejszych wersji**:

* Aby włączyć udostępnianie plików i drukarek za poorednictwem zapory systemu Windows,
  1. Otwórz **Panel sterowania**  >  **system i zabezpieczenia**  >  **zapory systemu Windows**. W okienku po lewej stronie wybierz pozycję **Ustawienia zaawansowane**  >  **reguły ruchu przychodzącego** w drzewie konsoli.
  1. Zlokalizuj reguły udostępniania plików i drukarek (NB — sesja-in) i udostępnianie plików i drukarek (ruch przychodzący SMB).
  1. Dla każdej reguły kliknij prawym przyciskiem myszy regułę, a następnie kliknij polecenie **Włącz regułę**.

* Aby włączyć udostępnianie plików zasady grupy:
  1. Przejdź do **menu Start**, wpisz `gpmc.msc` i Wyszukaj.
  1. W okienku nawigacji otwórz następujące foldery: Konfiguracja użytkownika **zasady komputera lokalnego**  >    >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **udostępnianie sieci**.
  1. W okienku szczegółów kliknij dwukrotnie pozycję **uniemożliwiaj użytkownikom udostępnianie plików w ramach ich profilu**.

     Aby wyłączyć ustawienie zasady grupy i umożliwić użytkownikowi udostępnianie plików, wybierz pozycję **wyłączone**.

  1. Wybierz przycisk **OK**, aby zapisać zmiany.

  Aby dowiedzieć się więcej, zobacz [Włączanie lub wyłączanie udostępniania plików przy użyciu zasady grupy](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Aby włączyć udostępnianie plików i drukarek w nowszych wersjach systemu Windows lub Linux, postępuj zgodnie z instrukcjami w temacie [Instalowanie usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Sprawdzanie konfiguracji Instrumentacja zarządzania Windows (WMI) (kod błędu: 95103)

Po sprawdzeniu usług plików i drukarek należy włączyć usługę WMI dla profilów prywatnych, publicznych i domeny za pomocą zapory. Te ustawienia są wymagane do ukończenia zdalnego wykonywania na maszynie źródłowej.

Aby włączyć usługę WMI:

1. Przejdź do pozycji **Panel sterowania**  >  **zabezpieczenia** i wybierz opcję **Zapora systemu Windows**.
1. Wybierz pozycję **Zmień ustawienia** , a następnie wybierz kartę **wyjątki** .
1. W oknie **wyjątki** zaznacz pole wyboru dla INSTRUMENTACJA zarządzania Windows (WMI), aby włączyć ruch usługi WMI przez zaporę.

Ruch WMI można również włączyć za pośrednictwem zapory z poziomu wiersza polecenia przy użyciu następującego polecenia:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Inne artykuły dotyczące rozwiązywania problemów z usługą WMI można znaleźć w następujących artykułach.

* [Podstawowe testowanie WMI](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [Rozwiązywanie problemów z usługą WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Rozwiązywanie problemów przy użyciu skryptów WMI i usług WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Nieobsługiwane systemy operacyjne

Inną częstą przyczyną niepowodzenia może być nieobsługiwany system operacyjny. Użyj obsługiwanego systemu operacyjnego i wersji jądra w celu pomyślnej instalacji usługi mobilności. Unikaj używania prywatnych poprawek.

Aby wyświetlić listę systemów operacyjnych i wersje jądra obsługiwane przez Azure Site Recovery, zobacz [dokument macierzy obsługi](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nieobsługiwane konfiguracje dysków rozruchowych (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Woluminy rozruchowe i partycje systemowe nie są tym samym dyskiem (ErrorID: 95309)

Przed wersją 9,20, partycje rozruchowe i systemowe/woluminy na różnych dyskach były nieobsługiwaną konfiguracją. Począwszy od [wersji 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), ta konfiguracja jest obsługiwana.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Dysk rozruchowy jest niedostępny (ErrorID: 95310)

Nie można chronić maszyny wirtualnej bez dysku rozruchowego. Dysk rozruchowy zapewnia płynne odzyskiwanie maszyny wirtualnej podczas pracy w trybie failover. Brak dysku rozruchowego powoduje awarię rozruchu komputera po przejściu do trybu failover. Upewnij się, że maszyna wirtualna zawiera dysk rozruchowy, a następnie spróbuj ponownie wykonać operację. Ponadto wiele dysków rozruchowych na tym samym komputerze nie jest obsługiwanych.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Na maszynie źródłowej znajduje się wiele dysków rozruchowych (ErrorID: 95311)

Maszyna wirtualna z wieloma dyskami rozruchowymi nie jest [obsługiwaną konfiguracją](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partycja systemowa na wielu dyskach (ErrorID: 95313)

Przed wersją 9,20 konfiguracja partycji głównej lub woluminu na wielu dyskach była nieobsługiwana. Począwszy od [wersji 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), ta konfiguracja jest obsługiwana.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Włączenie ochrony nie powiodło się, ponieważ nazwa urządzenia wymieniona w konfiguracji GRUB zamiast identyfikatora UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Możliwa przyczyna

Pliki konfiguracyjne Grand Unified inicjujący (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/grub2/grub.cfg_ lub _/etc/default/grub_) mogą zawierać wartość parametrów **root** i **Resume** jako rzeczywiste nazwy urządzeń zamiast uniwersalnego unikatowego identyfikatora (UUID). Site Recovery zezwala na podejście UUID, ponieważ nazwy urządzeń mogą ulec zmianie po ponownym uruchomieniu maszyny wirtualnej. Na przykład maszyna wirtualna może nie przełączyć się w tryb online o tej samej nazwie w trybie failover, co spowoduje problemy.

Na przykład:

- Następujący wiersz pochodzi z GRUB pliku _/Boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Następujący wiersz pochodzi z GRUB pliku _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> Linie GRUB zawierają rzeczywiste nazwy urządzeń dla **katalogu głównego** i **wznowienia** , a nie identyfikatora UUID.

### <a name="how-to-fix"></a>Jak naprawić

Nazwy urządzeń należy zamienić na odpowiednie identyfikatory UUID.

1. Znajdź identyfikator UUID urządzenia, wykonując polecenie `blkid \<device name>` .

   Na przykład:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Teraz Zastąp nazwę urządzenia identyfikatorem UUID w formacie takim jak `root=UUID=\<UUID>` . Na przykład jeśli zamienimy nazwy urządzeń z identyfikatorem UUID dla certyfikatu głównego i wznowienia wymienionego w plikach _/Boot/grub2/grub.cfg_, _/Boot/grub2/grub.cfg_ lub _/etc/default/grub_ , wówczas wiersze w plikach wyglądają jak w następującym wierszu:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Uruchom ponownie ochronę.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalacja usługi mobilności została ukończona z ostrzeżeniem o ponownym uruchomieniu (ErrorID: 95265 & 95266)

Usługa mobilności Site Recovery ma wiele składników, z których jeden jest nazywany sterownikiem filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko podczas ponownego uruchamiania systemu. Poprawki sterownika filtru można wykonać tylko w przypadku załadowania nowego sterownika filtru w momencie ponownego uruchomienia systemu.

> [!IMPORTANT]
> Jest to ostrzeżenie, a istniejąca replikacja będzie działała nawet po nowej aktualizacji agenta. Możesz wybrać opcję ponownego uruchomienia w dowolnym momencie, aby uzyskać korzyści płynące z nowego sterownika filtru, ale jeśli nie uruchomisz ponownie komputera, stary sterownik filtru będzie nadal działać. W związku z tym po aktualizacji bez ponownego uruchomienia, z wyjątkiem sterownika filtru, **korzyści z innych ulepszeń i poprawek w usłudze mobilności zostały zrealizowane**. Mimo że nie jest wymagane ponowne uruchomienie komputera po każdym uaktualnieniu. Aby uzyskać informacje o tym, kiedy jest wymagany ponowny rozruch, należy wybrać sekcję [ponowne uruchomienie po uaktualnieniu usługi mobilności](service-updates-how-to.md#reboot-after-mobility-service-upgrade) w artykule aktualizacje usługi w Azure Site Recovery.

> [!TIP]
>Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi planowania uaktualnień w oknie obsługi, zapoznaj się z tematem [Obsługa najnowszej wersji systemu operacyjnego/jądra](service-updates-how-to.md#support-for-latest-operating-systemskernels) w artykule aktualizacje usługi w Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Obsługa LVM w wersji 9,20

Przed wersją 9,20, Menedżer woluminów logicznych (LVM) był obsługiwany tylko w przypadku dysków z danymi. `/boot`Partycja powinna znajdować się na partycji dysku, a nie woluminie LVM.

Począwszy od [wersji 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), jest obsługiwany [dysk systemu operacyjnego w LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) .

## <a name="insufficient-space-errorid-95524"></a>Niewystarczająca ilość miejsca (ErrorID: 95524)

Po skopiowaniu agenta mobilności na maszynę źródłową wymagane jest co najmniej 100 MB wolnego miejsca. Upewnij się, że maszyna źródłowa ma wymaganą ilość wolnego miejsca, a następnie spróbuj ponownie wykonać operację.

## <a name="low-system-resources"></a>Niskie zasoby systemowe

Możliwe identyfikatory błędów, które są widoczne dla tego problemu, to 95572 i 95573. Ten problem występuje, gdy system ma małą ilość dostępnej pamięci i nie może przydzielić pamięci na potrzeby instalacji usługi mobilności. Upewnij się, że wystarczająca ilość pamięci została zwolniona w celu pomyślnego zakończenia instalacji.

## <a name="vss-installation-failures"></a>Błędy instalacji usługi VSS

Instalacja usługi kopiowania woluminów w tle (VSS) jest częścią instalacji agenta mobilności. Ta usługa jest używana w procesie do generowania punktów odzyskiwania spójnych przez aplikację. Błędy podczas instalacji usługi VSS mogą wystąpić z wielu powodów. Aby zidentyfikować dokładne błędy, zapoznaj się z _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Niektóre typowe błędy i kroki rozwiązywania zostały wyróżnione w poniższej sekcji.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Błąd VSS-2147023170 [0x800706BE]-Exit Code 511

Ten problem najczęściej występuje, gdy oprogramowanie antywirusowe blokuje operacje Azure Site Recovery usług.

Aby rozwiązać ten problem:

1. Przejrzyj listę [wykluczeń folderów z programu antywirusowego](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Postępuj zgodnie z wytycznymi opublikowanymi przez dostawcę oprogramowania antywirusowego w celu odblokowania rejestracji biblioteki DLL w systemie Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Błąd usługi VSS 7 [0x7] — kod zakończenia 511

Ten błąd jest spowodowany błędem środowiska uruchomieniowego, ponieważ jest za mało pamięci, aby zainstalować usługę VSS. Zwiększ ilość miejsca na dysku na pomyślne ukończenie tej operacji.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Błąd VSS-2147023824 [0x80070430]-Exit Code 517

Ten błąd występuje, gdy Azure Site Recovery usługi VSS Provider została [oznaczona do usunięcia](/previous-versions/ms838153(v=msdn.10)). Spróbuj ręcznie zainstalować usługę VSS na maszynie źródłowej, uruchamiając następujące polecenie:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Błąd VSS-2147023841 [0x8007041F]-Exit Code 512

Ten błąd występuje, gdy Azure Site Recovery baza danych usługi VSS Provider jest [zablokowana](/previous-versions/ms833798(v=msdn.10)). Spróbuj ręcznie zainstalować usługę VSS na maszynie źródłowej, uruchamiając następujące polecenie w wierszu polecenia:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Gdy wystąpi błąd, sprawdź, czy w stanie **uruchomienia** jakiegokolwiek programu antywirusowego lub innych usług jest zablokowany. Proces w stanie **początkowym** może zachować blokadę w usługach bazy danych. Wystąpił błąd podczas instalowania dostawcy usługi VSS. Upewnij się, że żadna usługa nie jest w stanie **początkowym** , a następnie ponów próbę wykonania tej operacji.

### <a name="vss-exit-code-806"></a>Kod zakończenia usługi VSS 806

Ten błąd występuje, gdy konto użytkownika używane do instalacji nie ma uprawnień do wykonania `CSScript` polecenia. Podaj odpowiednie uprawnienia do konta użytkownika, aby wykonać skrypt, a następnie spróbuj ponownie wykonać operację.

### <a name="other-vss-errors"></a>Inne błędy usługi VSS

Spróbuj ręcznie zainstalować usługę dostawcy VSS na maszynie źródłowej, uruchamiając następujące polecenie w wierszu polecenia:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Błąd VSS — 0x8004E00F

Ten błąd występuje zazwyczaj podczas instalacji agenta mobilności z powodu problemów w programie `DCOM` i `DCOM` jest w stanie krytycznym.

Aby określić przyczynę błędu, należy wykonać poniższą procedurę.

### <a name="examine-the-installation-logs"></a>Sprawdzanie dzienników instalacji

1. Otwórz dziennik instalacji znajdujący się pod adresem _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. Obecność następującego błędu wskazuje na ten problem:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

W celu rozwiązania tego problemu:

Skontaktuj się z [zespołem platformy Microsoft Windows](https://aka.ms/Windows_Support) , aby uzyskać pomoc w rozwiązywaniu problemu z modelem DCOM.

Po rozwiązaniu problemu z modelem DCOM ponownie zainstaluj Azure Site Recovery dostawcę usługi VSS przy użyciu następującego polecenia w wierszu polecenia:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Jeśli spójność aplikacji nie jest krytyczna dla wymagań związanych z odzyskiwaniem po awarii, można ominąć instalację dostawcy usługi VSS.

Aby obejść Azure Site Recovery instalację dostawcy usługi VSS i ręcznie zainstalować dostawcę usług VSS Azure Site Recovery po instalacji:

1. Zainstaluj usługę mobilności. Instalacja nie powiedzie się w kroku: **Konfiguracja po instalacji**.
1. Aby pominąć instalację usługi VSS:
   1. Otwórz katalog instalacji usługi mobilności Azure Site Recovery znajdujący się w lokalizacji:

      _C:\Program Files (x86) \Microsoft Azure Site Recovery\agent_

   1. Zmodyfikuj Azure Site Recovery skrypty instalacji dostawcy usługi VSS _InMageVSSProvider_Install_ i _InMageVSSProvider_Uninstall. cmd_ , aby zawsze kończyły się powodzeniem, dodając następujące wiersze:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Wykonaj ręczną instalację agenta mobilności.
1. Po pomyślnym zakończeniu instalacji i przeniesieniu do następnego kroku **Skonfiguruj**, Usuń wiersze, które zostały dodane.
1. Aby zainstalować dostawcę usługi VSS, Otwórz wiersz polecenia jako administrator i uruchom następujące polecenie:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Sprawdź, czy Azure Site Recovery Dostawca usługi VSS jest zainstalowany jako usługa w usługach systemu Windows. Otwórz konsolę MMC usługi składnika, aby upewnić się, że Dostawca usługi VSS znajduje się na liście.
1. Jeśli instalacja dostawcy usługi VSS nie powiedzie się, należy skontaktować się z pomocą techniczną w celu rozwiązania błędów uprawnień w interfejsie programowania aplikacji kryptograficznych (CAPI2).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalacja dostawcy VSS nie powiodła się, ponieważ usługa klastrowania jest włączona na komputerze niebędącym klastrem

Ten problem powoduje niepowodzenie instalacji agenta mobilności Azure Site Recovery podczas instalacji dostawcy Azure Site Recovery VSS. Przyczyną tego błędu jest problem `COM+` uniemożliwiający zainstalowanie dostawcy usługi VSS.

### <a name="to-identify-the-issue"></a>Aby zidentyfikować problem

W dzienniku znajdującym się na serwerze konfiguracji w _C:\ProgramData\ASRSetupLogs\UploadedLogs \<date-time> UA_InstallLogFile. log_ znajdziesz następujący wyjątek:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

W celu rozwiązania tego problemu:

1. Sprawdź, czy maszyna nie jest maszyną klastra i czy składniki klastra nie są używane.
1. Jeśli składniki nie są używane, Usuń składniki klastra z komputera.

## <a name="drivers-are-missing-on-the-source-server"></a>Na serwerze źródłowym brakuje sterowników

Jeśli instalacja agenta mobilności nie powiedzie się, Sprawdź dzienniki w obszarze _C:\ProgramData\ASRSetupLogs_ , aby ustalić, czy w niektórych zestawach kontroli brakuje niektórych wymaganych sterowników.

W celu rozwiązania tego problemu:

1. Korzystając z edytora rejestru, takiego jak `regedit.msc` , Otwórz Rejestr.
1. Otwórz `HKEY_LOCAL_MACHINE\SYSTEM` węzeł.
1. W `SYSTEM` węźle Znajdź zestawy kontrolek.
1. Otwórz każdy zestaw kontrolek i sprawdź, czy są obecne następujące sterowniki systemu Windows:

   * Napęd
   * VMBus
   * Storflt
   * Storvsc
   * Intelide

1. Zainstaluj ponownie wszystkie brakujące sterowniki.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](vmware-azure-tutorial.md) na temat sposobu konfigurowania odzyskiwania po awarii dla maszyn wirtualnych VMware.
