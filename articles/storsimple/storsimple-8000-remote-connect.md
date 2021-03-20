---
title: Zdalne nawiązywanie połączenia z urządzeniem StorSimple
description: Wyjaśniono, jak skonfigurować urządzenie pod kątem zdalnego zarządzania i jak nawiązać połączenie z program Windows PowerShell dla usługi StorSimple za pośrednictwem protokołu HTTP lub HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da688dfb00b26ca6b561d5aa0fb548c221381c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514573"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Zdalne nawiązywanie połączenia z urządzeniem z serii StorSimple 8000

## <a name="overview"></a>Omówienie

Możesz zdalnie nawiązać połączenie z urządzeniem za pomocą programu Windows PowerShell. Po nawiązaniu połączenia w ten sposób nie widzisz menu. (Menu jest widoczne tylko wtedy, gdy do nawiązania połączenia jest używana konsola szeregowa na urządzeniu). Komunikacja zdalna programu Windows PowerShell polega na połączeniu z określonym obszarem działania. Możesz również określić język wyświetlania.

Aby uzyskać więcej informacji o korzystaniu z funkcji komunikacji zdalnej programu Windows PowerShell do zarządzania urządzeniem, przejdź do obszaru [użycie usługi program Windows PowerShell dla usługi StorSimple w celu administrowania urządzeniem StorSimple](storsimple-8000-windows-powershell-administration.md).

W tym samouczku wyjaśniono, jak skonfigurować urządzenie do zdalnego zarządzania, a następnie jak nawiązać połączenie z usługą program Windows PowerShell dla usługi StorSimple. Za pomocą protokołu HTTP lub HTTPS można zdalnie łączyć się za pośrednictwem programu Windows PowerShell. Jednak podczas podejmowania decyzji o sposobie nawiązywania połączenia z usługą program Windows PowerShell dla usługi StorSimple należy wziąć pod uwagę następujące informacje:

* Bezpośrednie łączenie z konsolą szeregową urządzenia jest bezpieczne, ale połączenie z konsolą szeregową przez przełączniki sieciowe nie jest obsługiwane. Należy zachować ostrożność w przypadku nawiązywania połączenia z konsolą szeregową urządzenia przez przełączniki sieciowe.
* Połączenie za pośrednictwem sesji HTTP może oferować lepsze zabezpieczenia niż łączenie za pośrednictwem konsoli szeregowej za pośrednictwem sieci. Chociaż nie jest to najbezpieczniejsza Metoda, jest ona akceptowalna dla zaufanych sieci.
* Nawiązywanie połączenia za pośrednictwem sesji HTTPS z certyfikatem z podpisem własnym jest najbezpieczniejszym i zalecanym rozwiązaniem.

Można połączyć się zdalnie z interfejsem programu Windows PowerShell. Dostęp zdalny do urządzenia StorSimple za pośrednictwem interfejsu programu Windows PowerShell nie jest jednak domyślnie włączony. Najpierw należy włączyć zdalne zarządzanie na urządzeniu, a następnie na kliencie używanym do uzyskiwania dostępu do urządzenia.

Kroki opisane w tym artykule zostały wykonane w systemie hosta z systemem Windows Server 2012 R2.

## <a name="connect-through-http"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTP

Połączenie z program Windows PowerShell dla usługi StorSimple za pośrednictwem sesji HTTP zapewnia większe bezpieczeństwo niż łączenie za pośrednictwem konsoli szeregowej urządzenia StorSimple. Chociaż nie jest to najbezpieczniejsza Metoda, jest ona akceptowalna dla zaufanych sieci.

Aby skonfigurować zdalne zarządzanie, można użyć Azure Portal lub konsoli szeregowej. Wybierz jedną z następujących procedur:

* Użyj Azure Portal, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP
* [Używanie konsoli szeregowej do włączania zdalnego zarządzania za pośrednictwem protokołu HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Po włączeniu zarządzania zdalnego wykonaj poniższą procedurę, aby przygotować klienta do połączenia zdalnego.

* [Przygotowywanie klienta do zdalnego połączenia](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Użyj Azure Portal, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP

Aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP, wykonaj następujące czynności w Azure Portal.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Aby włączyć zdalne zarządzanie za pomocą Azure Portal

1. Przejdź do usługi Menedżer urządzeń StorSimple. Wybierz pozycję **urządzenia** , a następnie wybierz i kliknij urządzenie, które chcesz skonfigurować do zdalnego zarządzania. Przejdź do pozycji **Ustawienia urządzenia > zabezpieczenia**.
2. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **zdalne zarządzanie**.
3. W bloku **Zarządzanie zdalne** ustaw opcję **Włącz zdalne zarządzanie** na **wartość tak**.
4. Teraz możesz wybrać opcję połączenia przy użyciu protokołu HTTP. (Wartość domyślna to połączenie za pośrednictwem protokołu HTTPS). Upewnij się, że wybrano protokół HTTP.
   
   > [!NOTE]
   > Łączenie za pośrednictwem protokołu HTTP jest dopuszczalne tylko w sieciach zaufanych.
   
5. Kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Używanie konsoli szeregowej do włączania zdalnego zarządzania za pośrednictwem protokołu HTTP
Aby włączyć zdalne zarządzanie, wykonaj następujące czynności w konsoli szeregowej urządzenia.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aby włączyć zdalne zarządzanie za pomocą konsoli szeregowej urządzenia
1. W menu konsoli szeregowej wybierz opcję 1. Aby uzyskać więcej informacji o korzystaniu z konsoli szeregowej na urządzeniu, przejdź do pozycji [Połącz z program Windows PowerShell dla usługi StorSimple za pomocą konsoli szeregowej urządzenia](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. W wierszu polecenia wpisz: `Enable-HcsRemoteManagement –AllowHttp`
3. Zostanie wyświetlony monit o luki w zabezpieczeniach dotyczące korzystania z protokołu HTTP w celu nawiązania połączenia z urządzeniem. Po wyświetleniu monitu potwierdź, wpisując **Y**.
4. Sprawdź, czy protokół HTTP jest włączony, wpisując: `Get-HcsSystem`
5. Sprawdź, czy pole **RemoteManagementMode** zawiera **HttpsAndHttpEnabled**. Na poniższej ilustracji przedstawiono te ustawienia w temacie.
   
     ![Porty HTTPS i HTTP włączone](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Przygotowywanie klienta do zdalnego połączenia
Aby włączyć zdalne zarządzanie, wykonaj następujące czynności na kliencie.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Aby przygotować klienta do połączenia zdalnego
1. Uruchom sesję programu Windows PowerShell jako administrator. W przypadku korzystania z klienta systemu Windows 10 Domyślnie usługa Windows Remote Management jest ustawiona na ręczny. Może być konieczne uruchomienie usługi przez wpisanie:

    `Start-Service WinRM`
    
2. Wpisz następujące polecenie, aby dodać adres IP urządzenia StorSimple do listy zaufanych hostów klienta:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Zastąp <*device_ip*> adresem IP urządzenia; na przykład: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Wpisz następujące polecenie, aby zapisać poświadczenia urządzenia w zmiennej: 
   
    ```
    $cred = Get-Credential
    ```
    
4. W wyświetlonym oknie dialogowym:
   
   1. Wpisz nazwę użytkownika w tym formacie: *device_ip \ssadmin*.
   2. Wpisz hasło administratora urządzenia, które zostało ustawione podczas konfigurowania urządzenia przy użyciu Kreatora instalacji. Domyślne hasło to *Password1*.
5. Uruchom sesję programu Windows PowerShell na urządzeniu, wpisując następujące polecenie:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Aby utworzyć sesję programu Windows PowerShell do użycia z urządzeniem wirtualnym StorSimple, Dołącz `–Port` parametr i określ port publiczny skonfigurowany w ramach komunikacji zdalnej dla urządzenia wirtualnego StorSimple.
   
   
W tym momencie powinna istnieć aktywna zdalna sesja środowiska Windows PowerShell z urządzeniem.
   
![Komunikacja zdalna programu PowerShell przy użyciu protokołu HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTPS

Nawiązywanie połączenia z program Windows PowerShell dla usługi StorSimple za pośrednictwem sesji HTTPS to najbezpieczniejsza i zalecana metoda zdalnego łączenia się z urządzeniem Microsoft Azure StorSimple. Poniższe procedury wyjaśniają, jak skonfigurować konsolę szeregową i komputery klienckie, aby można było używać protokołu HTTPS do łączenia się z program Windows PowerShell dla usługi StorSimple.

Aby skonfigurować zdalne zarządzanie, można użyć Azure Portal lub konsoli szeregowej. Wybierz jedną z następujących procedur:

* Użyj Azure Portal, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTPS
* [Używanie konsoli szeregowej do włączania zdalnego zarządzania za pośrednictwem protokołu HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Po włączeniu zarządzania zdalnego wykonaj następujące procedury, aby przygotować hosta do zdalnego zarządzania i nawiązać połączenie z urządzeniem z hosta zdalnego.

* [Przygotowywanie hosta do zarządzania zdalnego](#prepare-the-host-for-remote-management)
* [Nawiązywanie połączenia z urządzeniem z hosta zdalnego](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Użyj Azure Portal, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTPS

Aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTPS, wykonaj następujące czynności w Azure Portal.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTPS z poziomu Azure Portal

1. Przejdź do usługi Menedżer urządzeń StorSimple. Wybierz pozycję **urządzenia** , a następnie wybierz i kliknij urządzenie, które chcesz skonfigurować do zdalnego zarządzania. Przejdź do pozycji **Ustawienia urządzenia > zabezpieczenia**.
2. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **zdalne zarządzanie**.
3. Ustaw opcję **Włącz zdalne zarządzanie** na **Tak**.
4. Teraz możesz nawiązać połączenie przy użyciu protokołu HTTPS. (Wartość domyślna to połączenie za pośrednictwem protokołu HTTPS). Upewnij się, że wybrano protokół HTTPS.
5. Kliknij przycisk... a następnie kliknij pozycję **Pobierz certyfikat zdalnego zarządzania**. Określ lokalizację, w której ma zostać zapisany ten plik. Ten certyfikat należy zainstalować na komputerze klienckim lub hoście, który będzie używany do nawiązywania połączenia z urządzeniem.
6. Kliknij przycisk **Zapisz** , a następnie kliknij przycisk **tak** po wyświetleniu monitu o potwierdzenie.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Używanie konsoli szeregowej do włączania zdalnego zarządzania za pośrednictwem protokołu HTTPS

Aby włączyć zdalne zarządzanie, wykonaj następujące czynności w konsoli szeregowej urządzenia.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aby włączyć zdalne zarządzanie za pomocą konsoli szeregowej urządzenia
1. W menu konsoli szeregowej wybierz opcję 1. Aby uzyskać więcej informacji o korzystaniu z konsoli szeregowej na urządzeniu, przejdź do pozycji [Połącz z program Windows PowerShell dla usługi StorSimple za pomocą konsoli szeregowej urządzenia](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. W wierszu polecenia wpisz:
   
     `Enable-HcsRemoteManagement`
   
    Powinno to umożliwić korzystanie z protokołu HTTPS na urządzeniu.
3. Sprawdź, czy protokół HTTPS został włączony, wpisując: 
   
     `Get-HcsSystem`
   
    Upewnij się, że pole **RemoteManagementMode** zawiera **HttpsEnabled**. Na poniższej ilustracji przedstawiono te ustawienia w temacie.
   
     ![Port Serial HTTPS włączony](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Z danych wyjściowych `Get-HcsSystem` Skopiuj numer seryjny urządzenia i Zapisz go do późniejszego użycia.
   
   > [!NOTE]
   > Numer seryjny jest mapowany na nazwę POSPOLITą w certyfikacie.
   
5. Uzyskaj certyfikat zarządzania zdalnego, wpisując: 
   
     `Get-HcsRemoteManagementCert`
   
    Zostanie wyświetlony certyfikat podobny do poniższego.
   
    ![Pobieranie certyfikatu zarządzania zdalnego](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Skopiuj informacje w certyfikacie z **-----Rozpocznij certyfikat-----** , aby **----------certyfikatów** do edytora tekstu, takiego jak Notatnik, i Zapisz go jako plik. cer. (Skopiujesz ten plik na hosta zdalnego podczas przygotowywania hosta).
   
   > [!NOTE]
   > Aby wygenerować nowy certyfikat, użyj `Set-HcsRemoteManagementCert` polecenia cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>Przygotowywanie hosta do zarządzania zdalnego

Aby przygotować komputer hosta do połączenia zdalnego, które używa sesji HTTPS, wykonaj następujące procedury:

* [Zaimportuj plik CER do magazynu głównego klienta lub hosta zdalnego](#to-import-the-certificate-on-the-remote-host).
* [Dodaj numery seryjne urządzeń do pliku Hosts na hoście zdalnym](#to-add-device-serial-numbers-to-the-remote-host).

Poniżej opisano każdą z tych procedur.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Aby zaimportować certyfikat na hoście zdalnym
1. Kliknij prawym przyciskiem myszy plik cer, a następnie wybierz pozycję **Zainstaluj certyfikat**. Spowoduje to uruchomienie Kreatora importu certyfikatów.
   
    ![Kreator importu certyfikatów 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. W polu **Lokalizacja magazynu** wybierz pozycję **Maszyna lokalna**, a następnie kliknij przycisk **Dalej**.
3. Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj**. Przejdź do głównego magazynu hosta zdalnego, a następnie kliknij przycisk **Dalej**.
   
    ![Kreator importu certyfikatów 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Kliknij przycisk **Finish** (Zakończ). Pojawi się komunikat informujący, że importowanie zakończyło się pomyślnie.
   
    ![Kreator importu certyfikatów 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Aby dodać numery seryjne urządzeń do hosta zdalnego
1. Uruchom Notatnik jako administrator, a następnie otwórz plik hosts znajdujący się w lokalizacji \Windows\System32\Drivers\etc.
2. Dodaj następujące trzy wpisy do pliku hosts: **adres IP danych 0**, **stały adres IP kontrolera 0** i **stały adres IP kontrolera 1**.
3. Wprowadź wcześniej zapisany numer seryjny urządzenia. Mapuj na adres IP, jak pokazano na poniższej ilustracji. W przypadku kontrolera 0 i 1 Dodaj **Controller0** i **Controller1** na końcu numeru SERYJNego (nazwa pospolita).
   
    ![Dodawanie nazwy POSPOLITej do pliku hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Zapisz plik hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Nawiązywanie połączenia z urządzeniem z hosta zdalnego

Za pomocą programu Windows PowerShell i protokołu TLS wprowadź na urządzeniu sesję SSAdmin z hosta zdalnego lub klienta. Sesja SSAdmin jest mapowana do opcji 1 w menu [konsoli szeregowej](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) urządzenia.

Wykonaj poniższą procedurę na komputerze, z którego ma zostać utworzone zdalne połączenie programu Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Aby wprowadzić sesję SSAdmin na urządzeniu przy użyciu programu Windows PowerShell i protokołu TLS
1. Uruchom sesję programu Windows PowerShell jako administrator. W przypadku korzystania z klienta systemu Windows 10 Domyślnie usługa Windows Remote Management jest ustawiona na ręczny. Może być konieczne uruchomienie usługi przez wpisanie:

    `Start-Service WinRM`

2. Dodaj adres IP urządzenia do zaufanych hostów klienta, wpisując:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Gdzie <*device_ip*> jest adresem IP urządzenia; na przykład: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Aby utworzyć nowe poświadczenie, wpisz:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Gdzie <*IP urządzenia docelowego*> jest adresem IP danych 0 dla urządzenia; na przykład **10.126.173.90** , jak pokazano na powyższym obrazie pliku Hosts. Podaj również hasło administratora dla urządzenia.
4. Utwórz sesję, wpisując:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Dla parametru-ComputerName w poleceniu cmdlet Podaj <*numer seryjny> urządzenia docelowego* . Ten numer seryjny został zmapowany na adres IP danych 0 w pliku hosts hosta zdalnego. na przykład **SHX0991003G44MT** , jak pokazano na poniższej ilustracji.
5. Wpisz:
   
     `Enter-PSSession $session`
6. Musisz poczekać kilka minut, a następnie nawiązać połączenie z urządzeniem za pośrednictwem protokołu HTTPS przez protokół TLS. Zostanie wyświetlony komunikat z informacją, że masz połączenie z urządzeniem.
   
    ![Komunikacja zdalna programu PowerShell przy użyciu protokołów HTTPS i TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [używaniu programu Windows PowerShell do administrowania urządzeniem StorSimple](storsimple-8000-windows-powershell-administration.md).
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

