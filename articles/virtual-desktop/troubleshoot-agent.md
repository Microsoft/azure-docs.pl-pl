---
title: Rozwiązywanie problemów z agentem pulpitu wirtualnego systemu Windows — Azure
description: Jak rozwiązać typowe problemy z agentem i łącznością.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: b47a205cac1717dfc66594f856fd9370a01a9ae3
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168216"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>Rozwiązywanie typowych problemów z agentem pulpitu wirtualnego systemu Windows

Agent pulpitu wirtualnego systemu Windows może powodować problemy z połączeniem ze względu na wiele czynników:
   - Błąd brokera, który sprawia, że Agent zatrzymuje usługę.
   - Problemy z aktualizacjami.
   - Problemy związane z instalowaniem programu podczas instalacji agenta, które zakłócają połączenie z hostem sesji.

Ten artykuł przeprowadzi Cię przez rozwiązania do tych typowych scenariuszy i sposobów rozwiązywania problemów z połączeniami.

>[!NOTE]
>W przypadku rozwiązywania problemów związanych z łącznością sesji oraz z agentem usług pulpitu wirtualnego systemu Windows zalecamy przejrzenie dzienników zdarzeń w temacie **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Znajdź zdarzenia, które mają jedno z następujących źródeł do zidentyfikowania problemu:
>
>- WVD-Agent
>- WVD-Agent-Aktualizator
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>Błąd: moduł ładujący RDAgentBootLoader i/lub Pulpit zdalny Agent przestał działać

Jeśli widzisz którykolwiek z następujących problemów, oznacza to, że moduł ładujący rozruchu, który ładuje agenta, nie mógł prawidłowo zainstalować agenta i usługa agenta nie jest uruchomiona:
- **RDAgentBootLoader** jest zatrzymana lub nie jest uruchomiona.
- Nie ma stanu **modułu ładującego agenta pulpit zdalny**.

Aby rozwiązać ten problem, uruchom moduł ładujący rozruchu programu RDAgent:

1. W oknie usługi kliknij prawym przyciskiem myszy pozycję **moduł ładujący agenta pulpit zdalny**.
2. Wybierz pozycję **Uruchom**. Jeśli ta opcja jest wyszarzona, nie masz uprawnień administratora i trzeba będzie ją uruchomić.
3. Zaczekaj 10 sekund, a następnie kliknij prawym przyciskiem myszy **pulpit zdalny program ładujący agenta**.
4. Wybierz pozycję **Odśwież**.
5. Jeśli usługa zostanie zatrzymana po rozpoczęciu i odświeżeniu, może wystąpić błąd rejestracji. Aby uzyskać więcej informacji, zobacz [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token).

## <a name="error-invalid_registration_token"></a>Błąd: INVALID_REGISTRATION_TOKEN

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3277, który brzmi **INVALID_REGISTRATION_TOKEN** w opisie, token rejestracji, który nie został rozpoznany jako prawidłowy.

Aby rozwiązać ten problem, Utwórz prawidłowy token rejestracji:

1. Aby utworzyć nowy token rejestracji, wykonaj kroki opisane w sekcji [generowanie nowego klucza rejestracji dla maszyny wirtualnej](#step-3-generate-a-new-registration-key-for-the-vm) .
2. Otwórz Edytor rejestru. 
3. Przejdź do **HKEY_LOCAL_MACHINE**  >  **oprogramowania**  >  **Microsoft**  >  **RDInfraAgent**.
4. Wybierz pozycję **IsRegistered**. 
5. W polu **dane wartości:** wprowadź **0** , a następnie wybierz **przycisk OK**. 
6. Wybierz pozycję **RegistrationToken**. 
7. W polu **dane wartości:** wprowadź token rejestracji z kroku 1. 

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu IsRegistered 0](media/isregistered-token.png)

8. Otwórz wiersz polecenia jako administrator.
9. Wprowadź **polecenie net stop RDAgentBootLoader**. 
10. Wprowadź **net start RDAgentBootLoader**. 
11. Otwórz Edytor rejestru.
12. Przejdź do **HKEY_LOCAL_MACHINE**  >  **oprogramowania**  >  **Microsoft**  >  **RDInfraAgent**.
13. Sprawdź, czy wartość **IsRegistered** została ustawiona na 1, a w kolumnie dane dla **RegistrationToken**. 

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający IsRegistered 1](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>Błąd: Agent nie może połączyć się z brokerem za pomocą INVALID_FORM

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3277, które wskazuje "INVALID_FORM" w opisie, wystąpił problem z komunikacją między agentem i brokerem. Agent nie może nawiązać połączenia z brokerem lub dotrzeć do określonego adresu URL z powodu niektórych ustawień zapory lub DNS.

Aby rozwiązać ten problem, sprawdź, czy można nawiązać połączenie z BrokerURI i BrokerURIGlobal:
1. Otwórz Edytor rejestru. 
2. Przejdź do **HKEY_LOCAL_MACHINE**  >  **oprogramowania**  >  **Microsoft**  >  **RDInfraAgent**. 
3. Zanotuj wartości **BrokerURI** i **BrokerURIGlobal**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu globalnego identyfikatora URI brokera i identyfikatora URI brokera](media/broker-uri.png)

 
4. Otwórz przeglądarkę i przejdź do pozycji *\<BrokerURI\> interfejs API/kondycja*. 
   - Upewnij się, że używasz wartości z kroku 3 w **BrokerURI**. W tej sekcji Przykładowa będzie <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> .
5. Otwórz kolejną kartę w przeglądarce i przejdź do pozycji *\<BrokerURIGlobal\> interfejs API/kondycja*. 
   - Upewnij się, że używasz wartości z kroku 3 w łączu **BrokerURIGlobal** . W tej sekcji Przykładowa będzie <https://rdbroker.wvd.microsoft.com/api/health> .
6. Jeśli sieć nie blokuje połączenia z brokerem, obie strony zostaną pomyślnie załadowane i zostanie wyświetlony komunikat informujący o tym, że **"Broker usług pulpitu zdalnego jest w dobrej kondycji"** , jak pokazano na poniższych zrzutach ekranu. 

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający pomyślnie załadowany dostęp do identyfikatora URI brokera](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający pomyślnie załadowany dostęp globalnego identyfikatora URI brokera](media/broker-global.png)
 

7. Jeśli sieć blokuje połączenie z brokerem, strony nie zostaną załadowane, jak pokazano na poniższym zrzucie ekranu. 

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający niepowodzenie ładowania dostępu do brokera](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający niepowodzenie załadowanego dostępu globalnego brokera](media/unsuccessful-broker-global.png)

8. Jeśli sieć blokuje te adresy URL, konieczne będzie odblokowanie wymaganych adresów URL. Aby uzyskać więcej informacji, zobacz [Lista wymaganych adresów URL](safe-url-list.md).
9. Jeśli to nie rozwiąże problemu, upewnij się, że nie masz żadnych zasad grupy z szyframi, które blokują połączenie z brokerem. Pulpit wirtualny systemu Windows używa tych samych szyfrów TLS 1,2 co [drzwi platformy Azure](../frontdoor/front-door-faq.yml#what-are-the-current-cipher-suites-supported-by-azure-front-door-). Aby uzyskać więcej informacji, zobacz [zabezpieczenia połączeń](network-connectivity.md#connection-security).

## <a name="error-3703"></a>Błąd: 3703

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3703, "adres URL bramy usług pulpitu zdalnego: nie jest dostępne" w opisie, Agent nie może nawiązać połączenia z adresami URL bramy. Aby pomyślnie nawiązać połączenie z hostem sesji i zezwolić na ruch sieciowy do tych punktów końcowych w celu obejścia ograniczeń, należy odblokować adresy URL z [listy wymaganych adresów URL](safe-url-list.md). Upewnij się również, że ustawienia zapory lub serwera proxy nie blokują tych adresów URL. Odblokowanie tych adresów URL jest wymagane do korzystania z pulpitu wirtualnego systemu Windows.

Aby rozwiązać ten problem, sprawdź, czy w ustawieniach zapory i/lub DNS nie są blokowane te adresy URL:
1. [Używanie zapory platformy Azure do ochrony wdrożeń pulpitów wirtualnych systemu Windows.](../firewall/protect-windows-virtual-desktop.md)
2. Skonfiguruj [Ustawienia usługi DNS zapory platformy Azure](../firewall/dns-settings.md).

## <a name="error-3019"></a>Błąd: 3019

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3019, oznacza to, że Agent nie może uzyskać dostępu do adresów URL transportu gniazda internetowego. Aby pomyślnie nawiązać połączenie z hostem sesji i zezwolić na ruch sieciowy, aby obejść te ograniczenia, należy odblokować adresy URL wymienione na [liście wymaganych adresów URL](safe-url-list.md). Skontaktuj się z zespołem obsługi sieci platformy Azure, aby upewnić się, że te adresy URL nie są blokowane przez zaporę, serwer proxy i ustawienia DNS. Możesz również sprawdzić dzienniki śledzenia sieci, aby określić, gdzie jest blokowana usługa pulpitu wirtualnego systemu Windows. Jeśli otworzysz żądanie pomocy technicznej dla tego konkretnego problemu, pamiętaj o dołączeniu dzienników śledzenia sieci do żądania.

## <a name="error-installationhealthcheckfailedexception"></a>Błąd: InstallationHealthCheckFailedException

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3277, które wskazuje "InstallationHealthCheckFailedException" w opisie, oznacza to, że odbiornik stosu nie działa, ponieważ serwer terminali przełączył klucz rejestru dla odbiornika stosu.

Aby rozwiązać ten problem:
1. Sprawdź, czy [odbiornik stosu działa](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
2. Jeśli odbiornik stosu nie działa, [ręcznie Odinstaluj i ponownie zainstaluj składnik stosu](#error-vms-are-stuck-in-unavailable-or-upgrading-state).

## <a name="error-endpoint_not_found"></a>Błąd: ENDPOINT_NOT_FOUND

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3277, które wskazuje "ENDPOINT_NOT_FOUND" w opisie, co oznacza, że Broker nie może znaleźć punktu końcowego do nawiązania połączenia. Problem z połączeniem może mieć jedną z następujących przyczyn:

- Brak maszyn wirtualnych w puli hostów
- Maszyny wirtualne w puli hostów nie są aktywne
- Wszystkie maszyny wirtualne w puli hostów przekroczyły limit maksymalnej liczby sesji
- Żadna z maszyn wirtualnych w puli hostów nie ma uruchomionej usługi agenta

Aby rozwiązać ten problem:

1. Upewnij się, że maszyna wirtualna jest włączona i nie została usunięta z puli hostów.
2. Upewnij się, że maszyna wirtualna nie przekroczyła maksymalnego limitu sesji.
3. Upewnij się, że [Usługa agenta jest uruchomiona](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running) i że [odbiornik stosu](#error-stack-listener-isnt-working-on-windows-10-2004-vm)działa.
4. Upewnij się [, że Agent może połączyć się z brokerem](#error-agent-cannot-connect-to-broker-with-invalid_form).
5. Upewnij się [, że maszyna wirtualna ma prawidłowy token rejestracji](#error-invalid_registration_token).
6. Upewnij się [, że token rejestracji maszyny wirtualnej nie wygasł](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues). 

## <a name="error-installmsiexception"></a>Błąd: InstallMsiException

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3277, to oznacza, że **InstallMsiException** w opisie, Instalator jest już uruchomiony dla innej aplikacji podczas próby zainstalowania agenta lub zasada blokuje uruchamianie programu msiexec.exe.

Aby rozwiązać ten problem, wyłącz następujące zasady:
   - Wyłącz Instalator Windows  
      - Ścieżka kategorii: Konfiguracja komputera\Szablony administracyjne \ składniki systemu Windows\zdalne Instalator
   
>[!NOTE]
>Nie jest to kompletna lista zasad, tylko te, z których obecnie jesteśmy świadomi.

Aby wyłączyć zasady:
1. Otwórz wiersz polecenia jako administrator.
2. Wprowadź i uruchom **przystawkę RSoP. msc**.
3. W oknie **wynikowy zestaw zasad** , który jest wyłączany, przejdź do ścieżki kategorii.
4. Wybierz zasady.
5. Wybierz opcję **Wyłączone**.
6. Wybierz przycisk **Zastosuj**.   

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu zasad Instalator Windows w wynikowym zestawie zasad](media/gpo-policy.png)

## <a name="error-win32exception"></a>Błąd: Win32exception

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3277, które informuje o **InstallMsiException** w opisie, zasada blokuje uruchamianie cmd.exe. Zablokowanie tego programu uniemożliwia uruchomienie okna konsoli, co jest potrzebne do ponownego uruchomienia usługi przy każdej aktualizacji agenta.

Aby rozwiązać ten problem, wyłącz następujące zasady:
   - Zapobiegaj dostępowi do wiersza polecenia   
      - Ścieżka kategorii: Konfiguracja komputera\Szablony Administracyjne\system użytkownika
    
>[!NOTE]
>Nie jest to kompletna lista zasad, tylko te, z których obecnie jesteśmy świadomi.

Aby wyłączyć zasady:
1. Otwórz wiersz polecenia jako administrator.
2. Wprowadź i uruchom **przystawkę RSoP. msc**.
3. W oknie **wynikowy zestaw zasad** , który jest wyłączany, przejdź do ścieżki kategorii.
4. Wybierz zasady.
5. Wybierz opcję **Wyłączone**.
6. Wybierz przycisk **Zastosuj**.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>Błąd: odbiornik stosu nie działa na maszynie wirtualnej z systemem Windows 10 2004

Uruchom **qwinsta** w wierszu polecenia i zanotuj numer wersji, który pojawia się obok **protokołu RDP-SxS**. Jeśli nie widzisz składników **RDP-TCP** i **RDP-SxS** , **nasłuchuje** obok nich lub nie są wyświetlane w ogóle po uruchomieniu **qwinsta**, oznacza to, że występuje problem z stosem. Aktualizacje stosu są instalowane razem z aktualizacjami agenta, a gdy ta instalacja przejdzie awry, odbiornik pulpitu wirtualnego systemu Windows nie będzie działał.

Aby rozwiązać ten problem:
1. Otwórz Edytor rejestru.
2. Przejdź do **HKEY_LOCAL_MACHINE**  >  **system**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
3. W obszarze **WinStations** może być widocznych kilka folderów z różnymi wersjami stosu, wybierz folder, który jest zgodny z informacjami o wersji wykrytymi podczas uruchamiania **qwinsta** w wierszu polecenia.
4. Znajdź **fReverseConnectMode** i upewnij się, że jej wartość jest równa **1**. Upewnij się również, że **fEnableWinStation** jest ustawiony na **1**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający fReverseConnectMode](media/fenable-2.png)

5. Jeśli **fReverseConnectMode** nie jest ustawiona na **1**, wybierz pozycję **fReverseConnectMode** i wprowadź wartość **1** w polu wartość. 
6. Jeśli **fEnableWinStation** nie jest ustawiona na **1**, wybierz pozycję **fEnableWinStation** i wprowadź wartość **1** w polu wartość.
7. Uruchom ponownie maszynę wirtualną. 

>[!NOTE]
>Aby zmienić tryb **fReverseConnectMode** lub **FEnableWinStation** dla wielu maszyn wirtualnych jednocześnie, można wykonać jedną z następujących czynności:
>
>- Wyeksportuj klucz rejestru z komputera, który już pracujesz, i zaimportuj go na wszystkie inne maszyny, które wymagają tej zmiany.
>- Utwórz obiekt zasad grupy (GPO), który ustawia wartość klucza rejestru dla maszyn, które wymagają zmiany.

7. Przejdź do **HKEY_LOCAL_MACHINE**  >  **system**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **ClusterSettings**.
8. W obszarze **ClusterSettings** Znajdź pozycję **SessionDirectoryListener** i upewnij się, że jej wartość danych to **RDP-SXS...**.
9. Jeśli **SessionDirectoryListener** nie jest ustawiona na **RDP-SXS...**, należy wykonać kroki opisane w sekcji [Odinstalowywanie agenta i modułu ładującego rozruchu](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) , aby najpierw odinstalować agenta, moduł ładujący rozruchu i składniki stosu, a następnie [ponownie zainstalować agenta i moduł ładujący rozruchu](#step-4-reinstall-the-agent-and-boot-loader). Spowoduje to ponowne zainstalowanie sterty równoległej.

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>Błąd: problem pulsu, w którym użytkownicy zachowują rozłączenie z hostów sesji

Jeśli serwer nie pobiera pulsu z usługi pulpitu wirtualnego systemu Windows, należy zmienić próg pulsu. Postępuj zgodnie z instrukcjami w tej sekcji, jeśli są spełnione co najmniej jeden z następujących scenariuszy:

- Otrzymujesz błąd **CheckSessionHostDomainIsReachableAsync**
- Otrzymujesz błąd **ConnectionBrokenMissedHeartbeatThresholdExceeded**
- Otrzymujesz komunikat o błędzie **ConnectionEstablished: UnexpectedNetworkDisconnect**
- Klienci korzystający z rozłączenia
- Użytkownicy nie rozłączają się z hostami sesji

Aby zmienić próg pulsu:
1. Otwórz wiersz polecenia jako administrator.
2. Wprowadź polecenie **qwinsta** i uruchom je.
3. Powinny być wyświetlane dwa składniki stosu: **RDP-TCP** i **RDP-SxS**. 
   - W zależności od używanej wersji systemu operacyjnego, do **protokołu RDP-SxS** może następować numer kompilacji. Jeśli tak jest, pamiętaj o zapisaniu tego numeru w przyszłości.
4. Otwórz Edytor rejestru.
5. Przejdź do **HKEY_LOCAL_MACHINE**  >  **system**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
6. W obszarze **WinStations** może być widocznych kilka folderów dla różnych wersji stosu. Wybierz folder pasujący do numeru wersji z kroku 3.
7. Utwórz nowy rejestr DWORD, klikając prawym przyciskiem myszy Edytor rejestru, a następnie wybierając **nową**  >  **wartość DWORD (32-bitową)**. Podczas tworzenia DWORD wprowadź następujące wartości:
   - HeartbeatInterval: 10000
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. Uruchom ponownie maszynę wirtualną.

>[!NOTE]
>Jeśli zmiana progu pulsu nie rozwiąże problemu, może wystąpić problem z siecią, którego potrzebujesz, aby skontaktować się z zespołem sieci platformy Azure.

## <a name="error-downloadmsiexception"></a>Błąd: DownloadMsiException

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3277, **DownloadMsiException** w opisie, na dysku nie ma wystarczającej ilości miejsca dla RDAgent.

Aby rozwiązać ten problem, zwolnij miejsce na dysku przez:
   - Usuwanie plików, które nie znajdują się już w użytkowniku
   - Zwiększenie pojemności magazynu maszyny wirtualnej

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>Błąd: nie można zaktualizować agenta za pomocą MissingMethodException

Przejdź do **Podgląd zdarzeń**  >  **Windows Logs**  >  **aplikacji**. Jeśli zobaczysz zdarzenie o IDENTYFIKATORze 3389, czyli "MissingMethodException: nie znaleziono metody" w opisie, oznacza to, że Agent pulpitu wirtualnego systemu Windows nie został pomyślnie zaktualizowany i przywrócono do wcześniejszej wersji. Może to być spowodowane tym, że numer wersji programu .NET Framework aktualnie zainstalowanej na maszynach wirtualnych jest mniejszy niż 4.7.2. Aby rozwiązać ten problem, należy uaktualnić platformę .NET do wersji 4.7.2 lub nowszej, postępując zgodnie z instrukcjami instalacji zawartymi w [dokumentacji .NET Framework](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>Błąd: maszyny wirtualne są zablokowane lub nie są w stanie uaktualnić

Otwórz okno programu PowerShell jako administrator i uruchom następujące polecenie cmdlet:

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

Jeśli stan wymieniony dla hosta sesji lub hostów w puli hostów zawsze mówi "niedostępne" lub "uaktualnienie", agent lub stos nie został pomyślnie zainstalowany.

Aby rozwiązać ten problem, zainstaluj ponownie stos równoległy:
1. Otwórz wiersz polecenia jako administrator.
2. Wprowadź **polecenie net stop RDAgentBootLoader**. 
3. Przejdź do **Panelu sterowania**  >  **programy**  >  **programy i funkcje**.
4. Odinstaluj najnowszą wersję **stosu sieci usługi pulpitu zdalnego SxS** lub wersję wymienioną w **HKEY_LOCAL_MACHINE**  >  **system**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** w **ReverseConnectListener**.
5. Otwórz okno konsoli jako administrator i przejdź do **programu Files**  >  **Microsoft RDInfra**.
6. Wybierz składnik **SxSStack** lub uruchom polecenie **msiexec/i SxSStack- <version> . msi** , aby zainstalować plik msi.
8. Uruchom ponownie maszynę wirtualną.
9. Wróć do wiersza polecenia i uruchom polecenie **qwinsta** .
10. Sprawdź, czy składnik stosu zainstalowany w kroku 6 mówi, że **nasłuchuje** obok niego.
   - W takim przypadku wprowadź **net start RDAgentBootLoader** w wierszu polecenia i uruchom ponownie maszynę wirtualną.
   - W przeciwnym razie należy [ponownie zarejestrować maszynę wirtualną i ponownie zainstalować składnik agenta](#your-issue-isnt-listed-here-or-wasnt-resolved) .

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>Błąd: nie znaleziono połączenia: RDAgent nie ma aktywnego połączenia z brokerem

Twoje maszyny wirtualne mogą mieć swój limit połączeń, więc maszyna wirtualna nie może akceptować nowych połączeń.

Aby rozwiązać ten problem:
   - Zmniejsz limit liczby sesji. Zapewnia to bardziej równomierne rozłożenie zasobów między hostami sesji i uniemożliwi wyczerpanie zasobów.
   - Zwiększ pojemność zasobów maszyn wirtualnych.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>Błąd: działanie maszyny wirtualnej Pro lub innego nieobsługiwanego systemu operacyjnego

Stos równoległy jest obsługiwany tylko przez jednostki SKU systemu Windows Enterprise lub Windows Server, co oznacza, że systemy operacyjne, takie jak Pro VM, nie są. Jeśli nie masz jednostki SKU przedsiębiorstwa lub serwera, stos zostanie zainstalowany na maszynie wirtualnej, ale nie zostanie aktywowany, więc nie zobaczysz go, gdy uruchomisz **qwinsta** w wierszu polecenia.

Aby rozwiązać ten problem, należy utworzyć maszynę wirtualną z systemem Windows Enterprise lub Windows Server.
1. Przejdź do [szczegółów maszyny wirtualnej](create-host-pools-azure-marketplace.md#virtual-machine-details) i wykonaj kroki 1-12, aby skonfigurować jeden z następujących zalecanych obrazów:
   - Wiele sesji systemu Windows 10 Enterprise, wersja 1909
   - Wiele sesji systemu Windows 10 Enterprise, wersja 1909 + Microsoft 365 aplikacje
   - Windows Server 2019 Datacenter
   - Wiele sesji systemu Windows 10 Enterprise, wersja 2004
   - Wiele sesji systemu Windows 10 Enterprise, wersja 2004 + Microsoft 365 aplikacje
2. Wybierz pozycję **Przejrzyj i Utwórz**.

## <a name="error-name_already_registered"></a>Błąd: NAME_ALREADY_REGISTERED

Nazwa maszyny wirtualnej została już zarejestrowana i prawdopodobnie jest duplikatem.

Aby rozwiązać ten problem:
1. Wykonaj kroki opisane w sekcji [Usuwanie hosta sesji z puli hostów](#step-2-remove-the-session-host-from-the-host-pool) .
2. [Utwórz kolejną maszynę wirtualną](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). Upewnij się, że wybrano unikatową nazwę dla tej maszyny wirtualnej.
3. Przejdź do [Azure Portal](https://portal.azure.com) i Otwórz stronę **Przegląd** puli hostów, w której znajduje się maszyna wirtualna. 
4. Otwórz kartę **hosty sesji** i sprawdź, czy wszystkie hosty sesji znajdują się w tej puli hostów.
5. Poczekaj 5-10 minut, aż stan hosta sesji będzie **dostępny**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający hosta sesji](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>Twój problem nie jest tutaj wymieniony lub nie został rozpoznany

Jeśli nie możesz znaleźć problemu w tym artykule lub instrukcje nie były pomocne, zalecamy odinstalowanie, ponowne zainstalowanie i ponowne zarejestrowanie agenta usług pulpitu wirtualnego systemu Windows. Instrukcje przedstawione w tej sekcji pokazują, jak ponownie zarejestrować MASZYNę wirtualną w usłudze pulpitu wirtualnego systemu Windows, odinstalując wszystkie składniki agenta, modułu ładującego rozruchu i stosu, usuwając hosta sesji z puli hostów, generując nowy klucz rejestracji dla maszyny wirtualnej, a następnie instalując ponownie agenta i moduł ładujący rozruchu. W przypadku zastosowania co najmniej jednego z następujących scenariuszy wykonaj następujące instrukcje:
- Maszyna wirtualna jest zablokowana **lub** jest **niedostępna**
- Odbiornik stosu nie działa i jest uruchomiony w systemie Windows 10 1809, 1903 lub 1904
- Otrzymujesz błąd **EXPIRED_REGISTRATION_TOKEN**
- Twoje maszyny wirtualne nie są widoczne na liście hosty sesji
- Nie widzisz **modułu ładującego pulpit zdalny Agent** w oknie usług
- Nie widzisz składnika **RdAgentBootLoader** w Menedżerze zadań
- Otrzymujesz **brokera połączeń nie można zweryfikować błędu ustawień** na niestandardowych maszynach wirtualnych obrazu
- Instrukcje zawarte w tym artykule nie rozwiązują problemu

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>Krok 1: Odinstalowywanie wszystkich agentów, modułu ładującego rozruchu i programów składników stosu

Przed ponownym zainstalowaniem agenta, modułu ładującego rozruchu i stosu należy odinstalować wszystkie istniejące programy składowe z maszyny wirtualnej. Aby odinstalować wszystkie programy Agent, ładujący rozruch i składniki stosu:
1. Zaloguj się do maszyny wirtualnej jako administrator. 
2. Przejdź do **Panelu sterowania**  >  **programy**  >  **programy i funkcje**.
3. Usuń następujące programy:
   - Moduł ładujący rozruchu agenta Pulpit zdalny
   - Agent infrastruktury Usługi pulpitu zdalnego
   - Agent zagenewa infrastruktury Usługi pulpitu zdalnego
   - Usługi pulpitu zdalnego stos sieciowy SxS
   
>[!NOTE]
>Może zostać wyświetlona wiele wystąpień tych programów. Pamiętaj, aby usunąć wszystkie z nich.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający odinstalowywanie programów](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>Krok 2. Usuwanie hosta sesji z puli hostów

Po usunięciu hosta sesji z puli hostów Host sesji nie jest już zarejestrowany w tej puli hostów. Działa to jako Reset do rejestracji hosta sesji. Aby usunąć hosta sesji z puli hostów:
1. Przejdź do strony **Przegląd** puli hostów, w której znajduje się maszyna wirtualna, w [Azure Portal](https://portal.azure.com). 
2. Przejdź do karty **hosty sesji** , aby wyświetlić listę wszystkich hostów sesji w tej puli hostów.
3. Zapoznaj się z listą hostów sesji i wybierz maszynę wirtualną, którą chcesz usunąć.
4. Wybierz pozycję **Usuń**.  

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający usuwanie maszyny wirtualnej z puli hostów](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>Krok 3. Generowanie nowego klucza rejestracji dla maszyny wirtualnej

Musisz wygenerować nowy klucz rejestracji, który służy do ponownego rejestrowania maszyny wirtualnej w puli hostów i w usłudze. Aby wygenerować nowy klucz rejestracji dla maszyny wirtualnej:
1. Otwórz [Azure Portal](https://portal.azure.com) i przejdź do strony **Przegląd** puli hostów maszyny wirtualnej, którą chcesz edytować.
2. Wybierz pozycję **klucz rejestracji**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu klucza rejestracji w portalu](media/reg-key.png)

3. Otwórz kartę **klucz rejestracji** i wybierz pozycję **Generuj nowy klucz**.
4. Wprowadź datę wygaśnięcia, a następnie wybierz przycisk **OK**.  

>[!NOTE]
>Data wygaśnięcia nie może być krótsza niż godzina i nie dłuższa niż 27 dni od daty i godziny jego wygenerowania. Zdecydowanie zalecamy ustawienie daty wygaśnięcia na 27 dni.

5. Skopiuj nowo wygenerowany klucz do Schowka. Ten klucz będzie potrzebny później.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>Krok 4. ponowne zainstalowanie agenta i modułu ładującego rozruchu

Po ponownym zainstalowaniu najnowszej zaktualizowanej wersji agenta i modułu ładującego rozruchu, Agent równoległy stosujący i program do monitorowania Genewa są automatycznie instalowane. Aby ponownie zainstalować agenta i moduł ładujący rozruchu:
1. Zaloguj się do maszyny wirtualnej jako administrator i użyj odpowiedniej wersji Instalatora agenta dla danego wdrożenia w zależności od wersji systemu Windows, która jest uruchomiona na maszynie wirtualnej. Jeśli masz maszynę wirtualną z systemem Windows 10, postępuj zgodnie z instrukcjami w temacie [Rejestrowanie maszyn wirtualnych](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) , aby pobrać **agenta usług pulpitu wirtualnego systemu Windows** i programu **inicjującego agenta wirtualnego systemu Windows**. Jeśli masz maszynę wirtualną z systemem Windows 7, wykonaj kroki 13-14 w temacie [Rejestrowanie maszyn wirtualnych](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine) , aby pobrać **agenta usług pulpitu wirtualnego systemu Windows** i **Menedżera agentów usług pulpitu wirtualnego systemu Windows**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu strony pobierania agenta i programu inicjującego](media/download-agent.png)

2. Kliknij prawym przyciskiem myszy pobranego agenta i instalatorów modułu ładującego rozruchu.
3. Wybierz pozycję **Właściwości**.
4. Wybierz opcję **Odblokuj**.
5. Wybierz przycisk **OK**.
6. Uruchom instalatora agenta.
7. Gdy Instalator wyświetli monit o token rejestracji, Wklej klucz rejestracji ze schowka. 

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający wklejony token rejestracji](media/pasted-agent-token.png)

8. Uruchom Instalatora modułu ładującego rozruchu.
9. Uruchom ponownie maszynę wirtualną. 
10. Przejdź do [Azure Portal](https://portal.azure.com) i Otwórz stronę **Przegląd** puli hostów, do której należy maszyna wirtualna.
11. Przejdź do karty **hosty sesji** , aby wyświetlić listę wszystkich hostów sesji w tej puli hostów.
12. Teraz powinien zostać wyświetlony Host sesji zarejestrowany w puli hostów ze stanem **dostępne**. 

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający hosta sesji](media/hostpool-portal.png)

## <a name="next-steps"></a>Następne kroki

Jeśli problem będzie się powtarzał, utwórz przypadek pomocy technicznej i podaj szczegółowe informacje o występującym problemie oraz o podjętych działaniach w celu rozwiązania tego problemu. Poniższa lista zawiera inne zasoby, których można użyć do rozwiązywania problemów z wdrożeniem pulpitu wirtualnego systemu Windows.

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli środowiska i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z klientami Pulpit zdalny, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
