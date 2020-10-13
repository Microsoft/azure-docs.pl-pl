---
title: Rozwiązywanie problemów z hostem sesji Windows Virtual Desktop (klasyczny) — Azure
description: Jak rozwiązywać problemy podczas konfigurowania maszyn wirtualnych hosta sesji pulpitu wirtualnego systemu Windows (klasycznego).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 90b4c574a03d8dee50beff60304fb5c1f3b52945
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008631"
---
# <a name="windows-virtual-desktop-classic-session-host-virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej hosta sesji pulpitu wirtualnego systemu Windows (klasycznego)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../troubleshoot-vm-configuration.md).

Ten artykuł służy do rozwiązywania problemów występujących podczas konfigurowania maszyn wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows.

## <a name="provide-feedback"></a>Przekazywanie opinii

Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="vms-are-not-joined-to-the-domain"></a>Maszyny wirtualne nie są przyłączone do domeny

Postępuj zgodnie z tymi instrukcjami, jeśli masz problemy z przyłączaniem maszyn wirtualnych do domeny.

- Ręcznie Dołącz maszynę wirtualną przy użyciu procesu w [przyłączeniu maszyny wirtualnej z systemem Windows Server do domeny zarządzanej](../../active-directory-domain-services/join-windows-vm.md) lub za pomocą [szablonu dołączania do domeny](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Spróbuj wysłać polecenie ping do nazwy domeny z wiersza polecenia na maszynie wirtualnej.
- Przejrzyj listę komunikatów o błędach dołączania do domeny w [temacie Rozwiązywanie problemów z przyłączaniem do domeny](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Błąd: Nieprawidłowe poświadczenia

**Przyczyna:** Wystąpił błąd podczas wprowadzania poświadczeń w ramach poprawek do interfejsu szablonu Azure Resource Manager.

**Poprawka:** Wykonaj jedną z następujących czynności, aby rozwiązać ten problem.

- Ręcznie Dodaj maszyny wirtualne do domeny.
- Wdróż ponownie szablon po potwierdzeniu poświadczeń. Zobacz [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell-2019.md).
- Przyłączanie maszyn wirtualnych do domeny przy użyciu szablonu z [przyłączaniem istniejącej maszyny wirtualnej z systemem Windows do domeny usługi AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Błąd: przekroczono limit czasu oczekiwania na dane wejściowe użytkownika

**Przyczyna:** Konto używane do dołączania do domeny może mieć uwierzytelnianie wieloskładnikowe (MFA).

**Poprawka:** Wykonaj jedną z następujących czynności, aby rozwiązać ten problem.

- Tymczasowe usuwanie usługi MFA dla konta.
- Użyj konta usługi.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Błąd: konto używane podczas aprowizacji nie ma uprawnień do ukończenia operacji

**Przyczyna:** Używane konto nie ma uprawnień do przyłączania maszyn wirtualnych do domeny ze względu na zgodność i regulacje.

**Poprawka:** Wykonaj jedną z następujących czynności, aby rozwiązać ten problem.

- Użyj konta, które jest członkiem grupy Administratorzy.
- Przyznaj odpowiednie uprawnienia do używanego konta.

### <a name="error-domain-name-doesnt-resolve"></a>Błąd: nie rozpoznano nazwy domeny

**Przyczyna 1:** Maszyny wirtualne znajdują się w sieci wirtualnej, która nie jest skojarzona z siecią wirtualną (VNET), w której znajduje się domena.

**Poprawka 1:** Utwórz sieć równorzędną sieci wirtualnej między siecią wirtualną, w której zainicjowano maszyny wirtualne i sieć wirtualną, w której jest uruchomiony kontroler domeny. Zobacz [Tworzenie komunikacji równorzędnej sieci wirtualnej — Menedżer zasobów, różne subskrypcje](../../virtual-network/create-peering-different-subscriptions.md).

**Przyczyna 2:** W przypadku korzystania z Azure Active Directory Domain Services (Azure AD DS) sieć wirtualna nie ma zaktualizowanych ustawień serwera DNS w celu wskazywania zarządzanych kontrolerów domeny.

**Poprawka 2:** Aby zaktualizować ustawienia DNS dla sieci wirtualnej zawierającej AD DS platformy Azure, zobacz temat [Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure](../../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Przyczyna 3:** Ustawienia serwera DNS interfejsu sieciowego nie wskazują odpowiedniego serwera DNS w sieci wirtualnej.

**Poprawka 3:** Wykonaj jedną z następujących czynności, aby rozwiązać ten problem, wykonując czynności opisane w temacie [Change DNS Servers].
- Zmień ustawienia serwera DNS interfejsu sieciowego na **niestandardowe** , wykonując czynności opisane w części [Zmienianie serwerów DNS](../../virtual-network/virtual-network-network-interface.md#change-dns-servers) i określ prywatne adresy IP serwerów DNS w sieci wirtualnej.
- Zmień ustawienia serwera DNS interfejsu sieciowego, aby **dziedziczyć z sieci wirtualnej** z procedurami [zmiany serwerów DNS](../../virtual-network/virtual-network-network-interface.md#change-dns-servers), a następnie zmień ustawienia serwera DNS sieci wirtualnej na kroki z sekcji [zmiana serwerów DNS](../../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Nie zainstalowano programu Windows Virtual Desktop Agent i modułu ładującego rozruchu pulpitu wirtualnego systemu Windows

Zalecanym sposobem aprowizacji maszyn wirtualnych jest użycie szablonu **puli hostów usług pulpitu wirtualnego systemu Windows Azure Resource Manager tworzenie i udostępnianie** . Szablon automatycznie instaluje agenta pulpitu wirtualnego systemu Windows i moduł ładujący rozruchu agenta pulpitu wirtualnego systemu Windows.

Postępuj zgodnie z tymi instrukcjami, aby upewnić się, że składniki są zainstalowane i sprawdź komunikaty o błędach.

1. Upewnij się, że dwa składniki są zainstalowane, zaznaczając **Panel sterowania**  >  **programy**  >  **programy i funkcje**. Jeśli program **Windows Virtual Desktop Agent** i **moduł ładujący rozruchu agenta wirtualnego systemu Windows** nie są widoczne, nie są one zainstalowane na maszynie wirtualnej.
2. Otwórz **Eksploratora plików** i przejdź do **C:\Windows\Temp\ScriptLog.log**. Jeśli brakuje pliku, oznacza to, że nie można uruchomić programu PowerShell DSC, który zainstalował dwa składniki, w podanym kontekście zabezpieczeń.
3. Jeśli plik **C:\Windows\Temp\ScriptLog.log** jest obecny, otwórz go i sprawdź komunikaty o błędach.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Błąd: brakuje agenta usług pulpitu wirtualnego systemu Windows i modułu ładującego rozruchu agenta pulpitu wirtualnego systemu Windows. Brak C:\Windows\Temp\ScriptLog.log również

**Przyczyna 1:** Poświadczenia podane podczas wprowadzania dla szablonu Azure Resource Manager były nieprawidłowe lub niewystarczające uprawnienia.

**Poprawka 1:** Ręcznie Dodaj brakujące składniki do maszyn wirtualnych za pomocą polecenia [Utwórz pulę hostów za pomocą programu PowerShell](create-host-pools-powershell-2019.md).

**Przyczyna 2:** Program PowerShell DSC był w stanie uruchomić i wykonać, ale nie można go zakończyć, ponieważ nie może się zalogować do pulpitu wirtualnego systemu Windows i uzyskać wymaganych informacji.

**Poprawka 2:** Potwierdź elementy z poniższej listy.

- Upewnij się, że konto nie ma usługi MFA.
- Upewnij się, że nazwa dzierżawy jest dokładna, a dzierżawca istnieje na pulpicie wirtualnym systemu Windows.
- Upewnij się, że konto ma co najmniej uprawnienia współautora pulpitu zdalnego.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Błąd: uwierzytelnianie nie powiodło się, błąd w C:\Windows\Temp\ScriptLog.log

**Przyczyna:** Program PowerShell DSC był w stanie wykonać, ale nie mógł nawiązać połączenia z pulpitem wirtualnym systemu Windows.

**Poprawka:** Potwierdź elementy z poniższej listy.

- Ręcznie Zarejestruj maszyny wirtualne za pomocą usługi pulpitu wirtualnego systemu Windows.
- Potwierdź, że konto używane do nawiązywania połączenia z pulpitem wirtualnym systemu Windows ma uprawnienia do tworzenia pul hostów w dzierżawie.
- Potwierdzenie konta nie ma uwierzytelniania MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agent pulpitu wirtualnego systemu Windows nie jest rejestrowany w usłudze pulpitu wirtualnego systemu Windows

Gdy Agent pulpitu wirtualnego systemu Windows jest instalowany po raz pierwszy na maszynach wirtualnych hosta sesji (ręcznie lub za pośrednictwem szablonu Azure Resource Manager i programu PowerShell DSC), udostępnia token rejestracji. W poniższej sekcji omówiono problemy związane z rozwiązywaniem problemów dotyczących agenta usług pulpitu wirtualnego systemu Windows i tokenu.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Błąd: stan zgłoszony w poleceniu cmdlet Get-RdsSessionHost wyświetla stan jako niedostępny

> [!div class="mx-imgBorder"]
> ![Polecenie cmdlet Get-RdsSessionHost wyświetla stan jako niedostępny.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Przyczyna:** Agent nie może zaktualizować się do nowej wersji.

**Poprawka:** Postępuj zgodnie z tymi instrukcjami, aby ręcznie zaktualizować agenta.

1. Pobierz nową wersję agenta na maszynie wirtualnej hosta sesji.
2. Uruchom Menedżera zadań, a następnie na karcie Usługa Zatrzymaj usługę RDAgentBootLoader.
3. Uruchom Instalatora, aby uzyskać nową wersję agenta pulpitu wirtualnego systemu Windows.
4. Po wyświetleniu monitu o token rejestracji Usuń wpis INVALID_TOKEN i naciśnij przycisk Dalej (nowy token nie jest wymagany).
5. Ukończ pracę Kreatora instalacji.
6. Otwórz Menedżera zadań i uruchom usługę RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Błąd: wpis rejestru agenta usług pulpitu wirtualnego systemu Windows zawiera wartość 0

**Przyczyna:** Token rejestracji wygasł lub został wygenerowany z wartością wygaśnięcia wynoszącą 999999.

**Poprawka:** Postępuj zgodnie z tymi instrukcjami, aby naprawić błąd rejestru agenta.

1. Jeśli istnieje już token rejestracji, usuń go z poleceniem Remove-RDSRegistrationInfo.
2. Generuj nowy token przy użyciu programu RDS-NewRegistrationInfo.
3. Upewnij się, że parametr-ExpriationHours jest ustawiony na 72 (wartość maksymalna to 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Błąd: Agent pulpitu wirtualnego systemu Windows nie zgłasza pulsu podczas uruchamiania Get-RdsSessionHost

**Przyczyna 1:** Usługa RDAgentBootLoader została zatrzymana.

**Poprawka 1:** Uruchom Menedżera zadań, a jeśli karta usługi zgłosi stan zatrzymania dla usługi RDAgentBootLoader, uruchom usługę.

**Przyczyna 2:** Port 443 może być zamknięty.

**Poprawka 2:** Postępuj zgodnie z tymi instrukcjami, aby otworzyć port 443.

1. Upewnij się, że port 443 jest otwarty przez pobranie narzędzia PSPing z [narzędzi sysinternal](/sysinternals/downloads/psping/).
2. Zainstaluj program PSPing na maszynie wirtualnej hosta sesji, w której jest uruchomiony Agent programu.
3. Otwórz wiersz polecenia jako administrator i wydaj poniższe polecenie:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Potwierdź, że PSPing otrzymał informacje z RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Rozwiązywanie problemów z funkcją pulpitu wirtualnego systemu Windows — stos równoległy

Stos równoległy pulpitu wirtualnego systemu Windows jest automatycznie instalowany z systemem Windows Server 2019. Użyj Instalatora Microsoft (MSI), aby zainstalować stos równoległy w systemie Microsoft Windows Server 2016 lub Windows Server 2012 R2. W przypadku systemu Microsoft Windows 10 stos równoległy pulpitu wirtualnego systemu Windows jest włączony z **enablesxstackrs.ps1**.

Istnieją trzy główne sposoby, w których stos równoległy jest instalowany lub włączony na maszynach wirtualnych puli hostów sesji:

- Za pomocą Azure Resource Manager **tworzenia i udostępniania nowego szablonu puli hostów wirtualnego systemu Windows**
- Przez dołączenie i włączenie na obrazie głównym
- Ręcznie zainstalowane lub włączone dla każdej maszyny wirtualnej (lub z rozszerzeniami/programem PowerShell)

Jeśli występują problemy z stosem równoległym pulpitu wirtualnego systemu Windows, wpisz polecenie **qwinsta** z wiersza polecenia, aby upewnić się, że stos równoległy jest zainstalowany lub włączony.

Dane wyjściowe **qwinsta** będą wystawiać **protokół RDP-SxS** w danych wyjściowych, jeśli zostanie zainstalowany i włączony stos równoległy.

> [!div class="mx-imgBorder"]
> ![Stos równoległy został zainstalowany lub włączony przy użyciu qwinsta, który jest wymieniony jako RDP-SxS w danych wyjściowych.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Sprawdź wpisy rejestru wymienione poniżej i upewnij się, że ich wartości pasują do siebie. Jeśli brakuje kluczy rejestru lub wartości są niezgodne, postępuj zgodnie z instrukcjami w temacie [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell-2019.md) na temat sposobu ponownej instalacji stosu równoległego.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Błąd: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE kod błędu.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Przyczyna:** Stos równoległy nie jest zainstalowany na maszynie wirtualnej hosta sesji.

**Poprawka:** Postępuj zgodnie z tymi instrukcjami, aby zainstalować stos równoległy na maszynie wirtualnej hosta sesji.

1. Użyj Remote Desktop Protocol (RDP), aby przejść bezpośrednio do maszyny wirtualnej hosta sesji jako administrator lokalny.
2. Pobierz i zaimportuj [moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono, Uruchom to polecenie cmdlet, aby zalogować się do konta:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Zainstaluj stos równoległy przy użyciu polecenia [Utwórz pulę hostów za pomocą programu PowerShell](create-host-pools-powershell-2019.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Jak naprawić stos równoległy pulpitu wirtualnego systemu Windows, który działa nieprawidłowo

Istnieją znane sytuacje, które mogą spowodować nieprawidłowe działanie stosu równoległego:

- Nie zgodnie z prawidłową kolejnością kroków, aby włączyć stos równoległy
- Autoaktualizacja do ulepszonego uniwersalnego dysku systemu Windows 10 (EVD)
- Brak roli hosta sesji Pulpit zdalny
- Uruchamianie enablesxsstackrc.ps1 wiele razy
- Uruchamianie enablesxsstackrc.ps1 na koncie, które nie ma uprawnień administratora lokalnego

Instrukcje przedstawione w tej sekcji mogą pomóc w odinstalowaniu stosu równoległego pulpitu wirtualnego systemu Windows. Po odinstalowaniu stosu równoległego przejdź do pozycji "Zarejestruj maszynę wirtualną w puli hostów systemu Windows Virtual Desktop" w temacie [Tworzenie puli hostów za pomocą programu PowerShell](create-host-pools-powershell-2019.md) , aby ponownie zainstalować stos równoległy.

Maszyna wirtualna używana do uruchamiania korygowania musi znajdować się w tej samej podsieci i domenie co maszyna wirtualna z nieprawidłowo działającym stosem równoległym.

Postępuj zgodnie z tymi instrukcjami, aby przeprowadzić korygowanie z tej samej podsieci i domeny:

1. Połącz się z maszyną wirtualną przy użyciu standardu Remote Desktop Protocol (RDP), z której zostanie zastosowana poprawka.
2. Pobierz PsExec z https://docs.microsoft.com/sysinternals/downloads/psexec .
3. Rozpakuj pobrany plik.
4. Uruchom wiersz polecenia jako administrator lokalny.
5. Przejdź do folderu, w którym PsExec zostało rozpakowane.
6. W wierszu polecenia Użyj następującego polecenia:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname to nazwa maszyny wirtualnej z nieprawidłowym stosem równoległym.

7. Zaakceptuj umowę licencyjną PsExec, klikając pozycję Zgadzam się.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu umowy licencji na oprogramowanie.](../media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >To okno dialogowe będzie wyświetlane tylko po pierwszym uruchomieniu PsExec.

8. Po otwarciu sesji wiersza polecenia na maszynie wirtualnej z nieprawidłowym stosem równoległym Uruchom program qwinsta i upewnij się, że jest dostępny wpis o nazwie RDP-SXS. W przeciwnym razie stos równoległy nie jest obecny na maszynie wirtualnej, więc problem nie jest powiązany z stosem równoległym.

    > [!div class="mx-imgBorder"]
    > ![Wiersz polecenia administratora](../media/AdministratorCommandPrompt.png)

9. Uruchom następujące polecenie, które spowoduje wyświetlenie listy składników firmy Microsoft zainstalowanych na maszynie wirtualnej z nieprawidłowym stosem równoległym.

    ```cmd
        wmic product get name
    ```

10. Uruchom poniższe polecenie z nazwami produktów w powyższym kroku.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Odinstaluj wszystkie produkty, które zaczynają się od "Pulpit zdalny".

12. Po odinstalowaniu wszystkich składników pulpitu wirtualnego systemu Windows postępuj zgodnie z instrukcjami dla danego systemu operacyjnego:

13. Jeśli używany system operacyjny to Windows Server, należy ponownie uruchomić maszynę wirtualną, która ma nieprawidłowo działający stos (z Azure Portal lub z narzędzia PsExec).

Jeśli używany system operacyjny to Microsoft Windows 10, wykonaj poniższe instrukcje:

14. Na maszynie wirtualnej z systemem PsExec Otwórz Eksploratora plików i skopiuj disablesxsstackrc.ps1 na dysk systemowy maszyny wirtualnej z nieprawidłowo działającym stosem równoległym.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname to nazwa maszyny wirtualnej z nieprawidłowym stosem równoległym.

15. Zalecany proces: w narzędziu PsExec Uruchom program PowerShell i przejdź do folderu z poprzedniego kroku i uruchom disablesxsstackrc.ps1. Alternatywnie można uruchomić następujące polecenia cmdlet:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Po zakończeniu działania poleceń cmdlet Uruchom ponownie maszynę wirtualną z nieprawidłowym stosem Side-by-side.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Tryb licencjonowania Pulpit zdalny nie jest skonfigurowany

Jeśli zalogujesz się do wielosesyjnego systemu Windows 10 Enterprise przy użyciu konta administracyjnego, możesz otrzymać powiadomienie informujące o tym, że Pulpit zdalny tryb licencjonowania nie jest skonfigurowany, Usługi pulpitu zdalnego przestanie działać w ciągu X dni. Na serwerze brokera połączeń użyj Menedżer serwera, aby określić tryb licencjonowania Pulpit zdalny ".

W przypadku upływu limitu czasu zostanie wyświetlony komunikat o błędzie "Sesja zdalna została rozłączona, ponieważ dla tego komputera nie ma Pulpit zdalny licencji dostępu klienta".

Jeśli zobaczysz jeden z tych komunikatów, oznacza to, że obraz nie ma zainstalowanych najnowszych aktualizacji systemu Windows lub że tryb licencjonowania Pulpit zdalny jest konfigurowany za pomocą zasad grupy. Postępuj zgodnie z instrukcjami w następnych sekcjach, aby sprawdzić ustawienia zasad grupy, zidentyfikuj wersję wielosesyjną systemu Windows 10 Enterprise i zainstaluj odpowiednią aktualizację.

>[!NOTE]
>Pulpit wirtualny systemu Windows wymaga tylko licencji dostępu klienta RDS (CAL), gdy pula hostów zawiera hosty sesji systemu Windows Server. Aby dowiedzieć się, jak skonfigurować RDS CAL, zobacz [Licencjonowanie wdrożenia usług pulpitu zdalnego z licencjami dostępu klienta](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Wyłącz ustawienie zasad grupy trybu licencjonowania Pulpit zdalny

Sprawdź ustawienie zasad grupy, otwierając Edytor zasady grupy na maszynie wirtualnej i przechodząc do **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >  **pulpit zdalny**  >  **Licencjonowanie**hosta sesji  >  **Ustaw tryb licencjonowania pulpit zdalny**. Jeśli ustawienie zasad grupy jest **włączone**, zmień je na **wyłączone**. Jeśli jest już wyłączona, pozostaw ją jako-is.

>[!NOTE]
>Jeśli zasady grupy są ustawiane za pośrednictwem domeny, należy wyłączyć to ustawienie dla zasad przeznaczonych dla maszyn wirtualnych obejmujących wiele sesji systemu Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Określ, która wersja wielosesyjnej usługi Windows 10 Enterprise jest używana

Aby sprawdzić, która wersja systemu Windows 10 Enterprise ma wiele sesji:

1. Zaloguj się przy użyciu konta administratora.
2. Wprowadź "informacje" na pasku wyszukiwania obok menu Start.
3. Wybierz pozycję **Informacje o**komputerze.
4. Sprawdź liczbę obok pozycji "wersja". Liczba powinna mieć wartość "1809" lub "1903", jak pokazano na poniższej ilustracji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający okno specyfikacji systemu Windows. Numer wersji jest wyróżniony kolorem niebieskim.](../media/windows-specifications.png)

Teraz, gdy znasz numer wersji, przejdź do odpowiedniej sekcji.

### <a name="version-1809"></a>Wersja 1809

Jeśli numer wersji jest taki sam, jak "1809", zainstaluj [aktualizację KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Wersja 1903

Wdróż ponownie system operacyjny hosta z najnowszą wersją obrazu systemu Windows 10 w wersji 1903 z galerii platformy Azure.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Nie można nawiązać połączenia z komputerem zdalnym z powodu błędu zabezpieczeń

Jeśli użytkownicy zobaczą komunikat o błędzie "nie można nawiązać połączenia z komputerem zdalnym z powodu błędu zabezpieczeń. Jeśli ten problem będzie się powtarzał, skontaktuj się z administratorem lub pomocą techniczną, aby uzyskać pomoc, "Sprawdź poprawność wszelkich istniejących zasad, które zmieniają domyślne uprawnienia RDP. Jedną z zasad, które mogą spowodować pojawienie się tego błędu jest "Zezwalaj na logowanie za Usługi pulpitu zdalnego zasad zabezpieczeń".

Aby dowiedzieć się więcej na temat tych zasad, zobacz [Zezwalanie na logowanie przy użyciu usługi pulpitu zdalnego](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview-2019.md).
- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues-2019.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration-2019.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection-2019.md).
- Aby rozwiązać problemy z Pulpit zdalny klientami, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](../troubleshoot-client.md)
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup-2019.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../../azure-resource-manager/templates/deployment-history.md).
