---
title: Rozwiązywanie problemów z połączeniem RDP maszyny wirtualnej platformy Azure według identyfikatora zdarzenia | Microsoft Docs
description: Identyfikatory zdarzeń służą do rozwiązywania różnych problemów, które uniemożliwiają połączenie protokołu Pulpit zdalny (RDP) z maszyną wirtualną platformy Azure.
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
ms.openlocfilehash: 507cd6cfe9f251dbc304b579d634ff986b001264
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088601"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Rozwiązywanie problemów z połączeniem RDP maszyny wirtualnej platformy Azure według identyfikatora zdarzenia 

W tym artykule wyjaśniono, jak używać identyfikatorów zdarzeń do rozwiązywania problemów, które uniemożliwiają połączenie z protokołem Pulpit zdalny (RDP) z maszyną wirtualną platformy Azure.

## <a name="symptoms"></a>Objawy

Podjęto próbę użycia sesji protokołu Pulpit zdalny (RDP) do nawiązania połączenia z maszyną wirtualną platformy Azure. Po wprowadzeniu poświadczeń połączenie nie powiedzie się i zostanie wyświetlony następujący komunikat o błędzie:

**Ten komputer nie może nawiązać połączenia z komputerem zdalnym. Spróbuj ponownie nawiązać połączenie, jeśli problem będzie się powtarzał, skontaktuj się z właścicielem komputera zdalnego lub administratorem sieci.**

Aby rozwiązać ten problem, Przejrzyj dzienniki zdarzeń na maszynie wirtualnej, a następnie zapoznaj się z następującymi scenariuszami.

## <a name="before-you-troubleshoot"></a>Przed rozpoczęciem rozwiązywania problemów

### <a name="create-a-backup-snapshot"></a>Utwórz migawkę kopii zapasowej

Aby utworzyć migawkę kopii zapasowej, wykonaj kroki opisane w sekcji [migawka dysku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Zdalne nawiązywanie połączenia z maszyną wirtualną

Aby zdalnie nawiązać połączenie z maszyną wirtualną, użyj jednej z metod w temacie [jak używać narzędzi zdalnych do rozwiązywania problemów z maszynami wirtualnymi platformy Azure](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scenariusz 1

### <a name="event-logs"></a>Dzienniki zdarzeń

W wystąpieniu programu CMD Uruchom następujące polecenia, aby sprawdzić, czy zdarzenie 1058 lub zdarzenie 1057 są rejestrowane w dzienniku systemu w ciągu ostatnich 24 godzin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      Systemami <br />
**Źródło:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:**      1058 <br />
**Kategoria zadania:** Dawaj <br />
**Poziom:**         Porn <br />
**Słowa kluczowe:**      Motyw <br />
**Użytkownik:**          NIE DOTYCZY <br />
**Komputer:**      *komputer* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie może zastąpić wygasłego certyfikatu z podpisem własnym używanym do uwierzytelniania serwera hosta sesji usług pulpitu zdalnego w połączeniach TLS. Odmowa dostępu do odpowiedniego kodu stanu.

**Nazwa dziennika:**      Systemami <br />
**Źródło:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:**      1058 <br />
**Kategoria zadania:** Dawaj <br />
**Poziom:**         Porn <br />
**Słowa kluczowe:**      Motyw <br />
**Użytkownik:**          NIE DOTYCZY <br />
**Komputer:**      *komputer* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie może utworzyć nowego certyfikatu z podpisem własnym, który ma być używany na potrzeby uwierzytelniania serwera hosta sesji usług pulpitu zdalnego dla połączeń TLS, odpowiedni kod stanu to obiekt już istnieje.

**Nazwa dziennika:**      Systemami <br />
**Źródło:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:**      1057 <br />
**Kategoria zadania:** Dawaj <br />
**Poziom:**         Porn <br />
**Słowa kluczowe:**      Motyw <br />
**Użytkownik:**          NIE DOTYCZY <br />
**Komputer:**      *komputer* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie może utworzyć nowego certyfikatu z podpisem własnym, który ma być używany do uwierzytelniania serwera hosta sesji usług pulpitu zdalnego dla połączeń TLS. Odpowiedni kod stanu to zestaw kluczy nie istnieje

Możesz również sprawdzić zdarzenia błędów SCHANNEL 36872 i 36870, uruchamiając następujące polecenia:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      Systemami <br />
**Źródło:**        Schannel <br />
**Data:**          — <br />
**Identyfikator zdarzenia:**      36870 <br />
**Kategoria zadania:** Dawaj <br />
**Poziom:**         Porn <br />
**Służąc**       <br />
**Użytkownik:**          SYSTEMAMI <br />
**Komputer:**      *komputer* <br />
**Opis:** Wystąpił błąd krytyczny podczas próby dostępu do klucza prywatnego poświadczeń serwera TLS. Kod błędu zwrócony z modułu kryptograficznego to 0x8009030D.  <br />
Wewnętrzny stan błędu to 10001.

### <a name="cause"></a>Przyczyna
Ten problem występuje, ponieważ nie można uzyskać dostępu do lokalnych kluczy szyfrowania RSA w folderze MachineKeys na maszynie wirtualnej. Ten problem może wystąpić z jednego z następujących powodów:

1. Nieprawidłowa konfiguracja uprawnień w folderze MachineKeys lub w plikach RSA.

2. Uszkodzony lub brakujący klucz RSA.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy skonfigurować odpowiednie uprawnienia do certyfikatu RDP, wykonując następujące kroki.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Udziel uprawnienia do folderu MachineKeys

1. Utwórz skrypt za pomocą następującej zawartości:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Uruchom ten skrypt, aby zresetować uprawnienia folderu MachineKey i zresetować pliki RSA do wartości domyślnych.

3.  Spróbuj ponownie uzyskać dostęp do maszyny wirtualnej.

Po uruchomieniu skryptu można sprawdzić następujące pliki, na których występują problemy z uprawnieniami:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Odnów certyfikat z podpisem własnym RDP

Jeśli problem będzie się powtarzać, uruchom następujący skrypt, aby upewnić się, że certyfikat z podpisem własnym RDP zostanie odnowiony:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Jeśli nie możesz odnowić certyfikatu, wykonaj następujące kroki, aby podjąć próbę usunięcia certyfikatu:

1. Na innej maszynie wirtualnej w tej samej sieci wirtualnej Otwórz pole **Uruchom** , wpisz **MMC**, a następnie naciśnij przycisk **OK**. 

2. W menu **plik** wybierz polecenie **Dodaj/Usuń przystawkę**.

3. Z listy **Dostępne przystawki** wybierz pozycję **Certyfikaty**, a następnie wybierz pozycję **Dodaj**.

4. Wybierz pozycję **konto komputera**, a następnie wybierz przycisk **dalej**.

5. Wybierz **inny komputer**, a następnie Dodaj adres IP maszyny wirtualnej, która ma problemy.
   >[!Note]
   >Spróbuj użyć sieci wewnętrznej, aby uniknąć używania wirtualnego adresu IP.

6. Wybierz pozycję **Zakończ**, a następnie wybierz pozycję **OK**.

   ![Wybierz komputer](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Rozwiń węzeł Certyfikaty, przejdź do folderu Remote Desktop\Certificates, kliknij prawym przyciskiem myszy certyfikat, a następnie wybierz polecenie **Usuń**.

8. Uruchom ponownie usługę konfiguracji Pulpit zdalny:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >W tym momencie w przypadku odświeżania magazynu z programu MMC certyfikat zostanie wyświetlony ponownie. 

Spróbuj ponownie uzyskać dostęp do maszyny wirtualnej przy użyciu protokołu RDP.

#### <a name="update-tlsssl-certificate"></a>Aktualizowanie certyfikatu TLS/SSL

W przypadku skonfigurowania maszyny wirtualnej do korzystania z certyfikatu TLS/SSL Uruchom następujące polecenie, aby uzyskać odcisk palca. Następnie sprawdź, czy jest to ten sam identyfikator odcisku palca certyfikatu:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Jeśli tak nie jest, Zmień odcisk palca:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Możesz również spróbować usunąć klucz, aby protokół RDP używał certyfikatu z podpisem własnym dla protokołu RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenariusz 2

### <a name="event-log"></a>Dziennik zdarzeń

W wystąpieniu programu CMD Uruchom następujące polecenia, aby sprawdzić, czy w dzienniku systemu w ciągu ostatnich 24 godzin zarejestrowano zdarzenie błędu SCHANNEL 36871:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      Systemami <br />
**Źródło:**        Schannel <br />
**Data:**          — <br />
**Identyfikator zdarzenia:**      36871 <br />
**Kategoria zadania:** Dawaj <br />
**Poziom:**         Porn <br />
**Służąc**       <br />
**Użytkownik:**          SYSTEMAMI <br />
**Komputer:**      *komputer* <br />
**Opis:** Wystąpił błąd krytyczny podczas tworzenia poświadczenia serwera TLS. Wewnętrzny stan błędu to 10013.
 
### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez zasady zabezpieczeń. Gdy starsze wersje protokołu TLS (na przykład 1,0) są wyłączone, dostęp RDP kończy się niepowodzeniem.

### <a name="resolution"></a>Rozwiązanie

Protokół RDP domyślnie używa protokołu TLS 1,0. Jednak protokół może zostać zmieniony na TLS 1,1, który jest nowym standardem.

Aby rozwiązać ten problem, zobacz [Rozwiązywanie problemów z uwierzytelnianiem w przypadku używania protokołu RDP do nawiązywania połączenia z maszyną wirtualną platformy Azure](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Scenariusz 3

Jeśli na maszynie wirtualnej została zainstalowana rola **brokera Podłączanie pulpitu zdalnego** , sprawdź, czy w ciągu ostatnich 24 godzin wystąpił zdarzenie 2056 lub zdarzenie 1296. W wystąpieniu programu CMD Uruchom następujące polecenia: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      Microsoft-Windows-TerminalServices-SessionBroker/operacyjny <br />
**Źródło:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:**      2056 <br />
**Kategoria zadania:** (109) <br />
**Poziom:**         Porn <br />
**Służąc**       <br />
**Użytkownik:**          USŁUGA SIECIOWA <br />
**Komputer:**      *nazwa FQDN komputera* <br />
**Opis:** Nie można znaleźć opisu zdarzenia o IDENTYFIKATORze 2056 z źródła Microsoft-Windows-TerminalServices-SessionBroker. Składnik, który wywołuje to zdarzenie, nie jest zainstalowany na komputerze lokalnym lub instalacja jest uszkodzona. Można zainstalować lub naprawić składnik na komputerze lokalnym. <br />
Jeśli zdarzenie pochodzi z innego komputera, informacje o wyświetlaniu musiały zostać zapisane w zdarzeniu. <br />
Następujące informacje zostały uwzględnione w zdarzeniu: <br />
NULL <br />
NULL <br />
Logowanie do bazy danych nie powiodło się.

**Nazwa dziennika:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Źródło:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:**      1296 <br />
**Kategoria zadania:** (104) <br />
**Poziom:**         Porn <br />
**Służąc**       <br />
**Użytkownik:**          USŁUGA SIECIOWA <br />
**Komputer:**      *nazwa FQDN komputera* <br />
**Opis:** Nie można znaleźć opisu zdarzenia o IDENTYFIKATORze 1296 ze źródła Microsoft-Windows-TerminalServices-SessionBroker-Client. Składnik, który wywołuje to zdarzenie, nie jest zainstalowany na komputerze lokalnym lub instalacja jest uszkodzona. Można zainstalować lub naprawić składnik na komputerze lokalnym.
Jeśli zdarzenie pochodzi z innego komputera, informacje o wyświetlaniu musiały zostać zapisane w zdarzeniu.
Następujące informacje zostały uwzględnione w zdarzeniu:  <br />
*Opis* <br />
*Opis* <br />
Broker Podłączanie pulpitu zdalnego nie jest gotowy do komunikacji z usługą RPC.

### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ nazwa hosta serwera Podłączanie pulpitu zdalnego brokera została zmieniona, co nie jest obsługiwaną zmianą. 

Nazwa hosta zawiera wpisy i zależności w wewnętrznej bazie danych systemu Windows, które są wymagane przez Pulpit zdalny farmie usług, aby mogły działać. Zmiana nazwy hosta po utworzeniu farmy powoduje wystąpienie wielu błędów i może spowodować, że serwer brokera przestanie działać.

### <a name="resolution"></a>Rozwiązanie 

Aby rozwiązać ten problem, należy zainstalować ponownie rolę Podłączanie pulpitu zdalnego brokera i wewnętrzną bazę danych systemu Windows.

## <a name="next-steps"></a>Następne kroki

[Zdarzenia Schannel](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786445(v=ws.11))

[Omówienie techniczne dostawcy obsługi zabezpieczeń Schannel](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786429(v=ws.11))

[Protokół RDP kończy się niepowodzeniem z IDENTYFIKATORem zdarzenia 1058 & zdarzenie 36870 z certyfikatem hosta sesji Pulpit zdalny & komunikacją protokołu SSL](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)

[Schannel 36872 lub Schannel 36870 na kontrolerze domeny](/archive/blogs/instan/schannel-36872-or-schannel-36870-on-a-domain-controller)

[Identyfikator zdarzenia 1058 — Usługi pulpitu zdalnego uwierzytelniania i szyfrowania](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee890862(v=ws.10))
