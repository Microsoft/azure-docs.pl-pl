---
title: Rozwiązywanie problemów z błędami uwierzytelniania podczas łączenia się z maszyną wirtualną platformy Azure przy użyciu protokołu RDP | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z błędami uwierzytelniania występującymi w przypadku używania Remote Desktop Protocol (RDP) do nawiązywania połączenia z maszyną wirtualną platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: cc1ad3104596cc7ad4bb48f88e4c8312bc833371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088567"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Rozwiązywanie problemów z uwierzytelnianiem podczas używania protokołu RDP w celu połączenia z maszyną wirtualną platformy Azure

Ten artykuł może pomóc w rozwiązywaniu problemów z błędami uwierzytelniania występującymi w przypadku korzystania z połączenia Remote Desktop Protocol (RDP) w celu nawiązania połączenia z maszyną wirtualną platformy Azure.

## <a name="symptoms"></a>Objawy

Przechwyć zrzut ekranu maszyny wirtualnej platformy Azure, który pokazuje ekran powitalny i wskazuje, że system operacyjny jest uruchomiony. Jednak podczas próby nawiązania połączenia z maszyną wirtualną przy użyciu Podłączanie pulpitu zdalnego zostanie wyświetlony jeden z następujących komunikatów o błędach.

### <a name="error-message-1"></a>Komunikat o błędzie 1

**Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędem zabezpieczeń lokalnych.**

### <a name="error-message-2"></a>Komunikat o błędzie 2

**Komputer zdalny, z którym próbujesz nawiązać połączenie, wymaga Uwierzytelnianie na poziomie sieci (NLA), ale nie można skontaktować się z kontrolerem domeny systemu Windows w celu przeprowadzenia NLA. Jeśli jesteś administratorem na komputerze zdalnym, możesz wyłączyć uwierzytelnianie NLA przy użyciu opcji na karcie zdalne okna dialogowego Właściwości systemu.**

### <a name="error-message-3-generic-connection-error"></a>Komunikat o błędzie 3 (ogólny błąd połączenia)

**Ten komputer nie może nawiązać połączenia z komputerem zdalnym. Spróbuj ponownie nawiązać połączenie, jeśli problem będzie się powtarzał, skontaktuj się z właścicielem komputera zdalnego lub administratorem sieci.**

## <a name="cause"></a>Przyczyna

Istnieje wiele powodów, dla których może blokować dostęp RDP do maszyny wirtualnej.

### <a name="cause-1"></a>Przyczyna 1

Maszyna wirtualna nie może komunikować się z kontrolerem domeny (DC). Ten problem może uniemożliwić sesji protokołu RDP uzyskanie dostępu do maszyny wirtualnej przy użyciu poświadczeń domeny. Jednak nadal będzie można zalogować się przy użyciu poświadczeń administratora lokalnego. Ten problem może wystąpić w następujących sytuacjach:

1. Active Directory kanał zabezpieczeń między tą maszyną wirtualną a kontrolerem domeny zostanie przerwany.

2. Maszyna wirtualna ma starą kopię hasła konta, a kontroler domeny ma nowszą kopię.

3. Kontroler domeny, z którym jest nawiązywane połączenie z tą maszyną wirtualną, jest w złej kondycji.

### <a name="cause-2"></a>Przyczyna 2

Poziom szyfrowania maszyny wirtualnej jest wyższy niż ten, który jest używany przez komputer kliencki.

### <a name="cause-3"></a>Przyczyna 3

Protokoły TLS 1,0, 1,1 lub 1,2 (serwery) są wyłączone na maszynie wirtualnej.

### <a name="cause-4"></a>Przyczyna 4

Maszyna wirtualna została skonfigurowana w taki sposób, aby wyłączyć logowanie przy użyciu poświadczeń domeny, a Urząd zabezpieczeń lokalnych (LSA) jest niepoprawnie skonfigurowany.

### <a name="cause-5"></a>Przyczyna 5

Maszyna wirtualna została skonfigurowana w taki sposób, aby akceptowała tylko połączenia zgodne z algorytmem FIPS (Federal Information Processing Standard). Jest to zazwyczaj realizowane przy użyciu zasad Active Directory. Jest to rzadki sposób konfiguracji, ale dla Pulpit zdalny połączeń można wymusić stosowanie trybu FIPS.

## <a name="before-you-troubleshoot"></a>Przed rozpoczęciem rozwiązywania problemów

### <a name="create-a-backup-snapshot"></a>Utwórz migawkę kopii zapasowej

Aby utworzyć migawkę kopii zapasowej, wykonaj kroki opisane w sekcji [migawka dysku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Zdalne nawiązywanie połączenia z maszyną wirtualną

Aby zdalnie nawiązać połączenie z maszyną wirtualną, użyj jednej z metod w temacie [jak używać narzędzi zdalnych do rozwiązywania problemów z maszynami wirtualnymi platformy Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Usługa klienta zasad grupy

Jeśli jest to maszyna wirtualna przyłączona do domeny, najpierw Zatrzymaj usługę klienta zasady grupy, aby zapobiec zastąpieniu zmian przez zasady Active Directory. Aby to zrobić, uruchom następujące polecenie:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Po naprawieniu problemu Przywróć tę maszynę wirtualną, aby skontaktować się z domeną w celu pobrania najnowszego obiektu zasad grupy z domeny. Aby to zrobić, uruchom następujące polecenia:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Jeśli zmiana zostanie wycofana, oznacza to, że Active Directory zasady powodują wystąpienie problemu. 

### <a name="workaround"></a>Obejście

Aby obejść ten problem, uruchom następujące polecenia w oknie polecenia, aby wyłączyć uwierzytelnianie:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Następnie uruchom ponownie maszynę wirtualną.

Aby ponownie włączyć uwierzytelnianie NLA, uruchom następujące polecenie, a następnie uruchom ponownie maszynę wirtualną:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="for-domain-joined-vms"></a>W przypadku maszyn wirtualnych przyłączonych do domeny

Aby rozwiązać ten problem, należy najpierw sprawdzić, czy maszyna wirtualna może połączyć się z kontrolerem domeny, oraz czy kontroler domeny ma stan "dobra" i może obsługiwać żądania z maszyny wirtualnej.

>[!Note] 
>Aby przetestować kondycję kontrolera domeny, można użyć innej maszyny wirtualnej w tej samej sieci wirtualnej i podsieci, która współużytkuje ten sam serwer logowania.

Nawiąż połączenie z maszyną wirtualną, na której występuje problem przy użyciu Konsola szeregowa, zdalnego narzędzia CMD lub zdalnego programu PowerShell, zgodnie z krokami opisanymi w sekcji "łączenie się zdalnie z maszyną wirtualną".

Aby określić, z którym kontrolerem domeny jest nawiązywane połączenie maszyna wirtualna, uruchom następujące polecenie w konsoli programu: 

```cmd
set | find /i "LOGONSERVER"
```

Następnie Sprawdź kondycję bezpiecznego kanału między maszyną wirtualną a kontrolerem domeny. Aby to zrobić, uruchom następujące polecenie w wystąpieniu programu PowerShell z podwyższonym poziomem uprawnień. To polecenie zwraca flagę logiczną, która wskazuje, czy bezpieczny kanał jest aktywny:

```powershell
Test-ComputerSecureChannel -verbose
```

Jeśli kanał jest przerwany, uruchom następujące polecenie, aby je naprawić:

```powershell
Test-ComputerSecureChannel -repair
```

Upewnij się, że hasło konta komputera w Active Directory zostało zaktualizowane na maszynie wirtualnej i na kontrolerze domeny:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Jeśli komunikacja między kontrolerem domeny a maszyną wirtualną jest dobra, ale kontroler domeny nie ma wystarczającej kondycji, aby otworzyć sesję RDP, możesz spróbować ponownie uruchomić kontroler domeny.

Jeśli powyższe polecenia nie rozwiążą problemu z komunikacją z domeną, można ponownie dołączyć tę maszynę wirtualną do domeny. W tym celu wykonaj następujące kroki:

1. Utwórz skrypt o nazwie Unjoin.ps1 przy użyciu następującej zawartości, a następnie wdróż skrypt jako rozszerzenie niestandardowego skryptu na Azure Portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Ten skrypt powoduje wymuszanie maszyny wirtualnej z domeny i ponowne uruchomienie jej ponownie 10 sekund. Następnie należy wyczyścić obiekt komputera po stronie domeny.

2.  Po zakończeniu oczyszczania ponownie Dołącz tę maszynę wirtualną do domeny. W tym celu należy utworzyć skrypt o nazwie JoinDomain.ps1 przy użyciu następującej zawartości, a następnie wdrożyć skrypt jako rozszerzenie niestandardowego skryptu na Azure Portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Spowoduje to dołączenie do maszyny wirtualnej w domenie przy użyciu określonych poświadczeń.

Jeśli kanał Active Directory jest w dobrej kondycji, hasło komputera zostanie zaktualizowane, a kontroler domeny działa zgodnie z oczekiwaniami, spróbuj wykonać poniższe czynności.

Jeśli problem będzie się powtarzał, sprawdź, czy poświadczenie domeny jest wyłączone. W tym celu Otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenie, aby określić, czy maszyna wirtualna została skonfigurowana do wyłączania kont domeny w celu zalogowania się na maszynie wirtualnej:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Jeśli klucz jest ustawiony na **1**, oznacza to, że serwer został skonfigurowany tak, aby nie zezwalał na poświadczenia domeny. Zmień ten klucz na **0**.

### <a name="for-standalone-vms"></a>Dla autonomicznych maszyn wirtualnych

#### <a name="check-minencryptionlevel"></a>Sprawdź MinEncryptionLevel

W wystąpieniu programu CMD Uruchom następujące polecenie, aby wykonać zapytanie dotyczące wartości rejestru **MinEncryptionLevel** :

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Na podstawie wartości rejestru wykonaj następujące kroki:

* 4 (FIPS): Przejdź do [sprawdzania zgodnych algorytmów FIPS](#fips-compliant).

* 3 (szyfrowanie 128-bitowe): Ustaw ważność na **2** , uruchamiając następujące polecenie:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (najwyższy możliwy do zaszyfrowania, zgodnie z podanym przez klienta): możesz spróbować ustawić szyfrowanie na wartość minimalną **1** , uruchamiając następujące polecenie:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Uruchom ponownie maszynę wirtualną, aby zmiany wprowadzone w rejestrze zaczęły obowiązywać.

#### <a name="tls-version"></a>Wersja protokołu TLS

W zależności od systemu protokół RDP używa protokołu TLS 1,0, 1,1 lub 1,2 (serwer). Aby zbadać, jak te protokoły są skonfigurowane na maszynie wirtualnej, Otwórz wystąpienie programu CMD, a następnie uruchom następujące polecenia:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Jeśli zwracane wartości nie są wszystkie **1**, oznacza to, że protokół jest wyłączony. Aby włączyć te protokoły, uruchom następujące polecenia:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

W przypadku innych wersji protokołu można uruchomić następujące polecenia:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Pobierz wersję x. x protokołu SSH/TLS z dzienników systemu operacyjnego gościa na stronie błędy SCHANNEL.

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a> Sprawdzanie połączeń algorytmów zgodnych ze standardem FIPs

Pulpit zdalny można wymusić, aby używać tylko połączeń algorytmów zgodnych ze standardem FIPs. Można to skonfigurować przy użyciu klucza rejestru. W tym celu Otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień, a następnie wykonaj zapytanie o następujące klucze:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Jeśli polecenie zwraca **1**, Zmień wartość rejestru na **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Sprawdź, który jest bieżącym MinEncryptionLevel na maszynie wirtualnej:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Jeśli polecenie zwraca **4**, Zmień wartość rejestru na **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Uruchom ponownie maszynę wirtualną, aby zmiany wprowadzone w rejestrze zaczęły obowiązywać.

## <a name="next-steps"></a>Następne kroki

[Metoda SetEncryptionLevel klasy Win32_TSGeneralSetting](/windows/desktop/termserv/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurowanie uwierzytelniania serwera i poziomów szyfrowania](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc770833(v=ws.11))

[Klasa Win32_TSGeneralSetting](/windows/desktop/termserv/win32-tsgeneralsetting)
