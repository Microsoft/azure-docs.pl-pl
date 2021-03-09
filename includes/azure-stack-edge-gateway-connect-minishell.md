---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 5c1ac3f79ab5db2622dafd3229b39bbe19bce41e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473765"
---
W zależności od systemu operacyjnego klienta procedury zdalnego łączenia się z urządzeniem są różne.

### <a name="remotely-connect-from-a-windows-client"></a>Nawiązywanie połączenia zdalnego z klienta systemu Windows


#### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Klient systemu Windows korzysta z programu Windows PowerShell 5,0 lub nowszego.
- Klient systemu Windows ma łańcuch podpisywania (certyfikat główny) odpowiadający certyfikatowi węzła zainstalowanego na urządzeniu. Aby uzyskać szczegółowe instrukcje, zobacz [Instalowanie certyfikatu na kliencie systemu Windows](../articles/databox-online/azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- `hosts`Plik znajdujący się pod adresem `C:\Windows\System32\drivers\etc` dla klienta systemu Windows zawiera wpis odpowiadający certyfikatowi węzła w następującym formacie:

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Oto przykład wpisu dla `hosts` pliku:
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Szczegółowe procedury

Wykonaj następujące kroki, aby zdalnie nawiązać połączenie z klientem systemu Windows.

1. Uruchom sesję programu Windows PowerShell jako administrator.
2. Upewnij się, że na kliencie jest uruchomiona usługa Windows Remote Management. W wierszu polecenia wpisz polecenie:

    `winrm quickconfig`

    Aby uzyskać więcej informacji, zobacz [Instalowanie i Konfigurowanie programu Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Przypisz zmienną do adresu IP urządzenia.

    $ip = "<device_ip>"

    Zastąp `<device_ip>` adres IP urządzenia.

4. Aby dodać adres IP urządzenia do listy zaufanych hostów klienta, wpisz następujące polecenie:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Uruchom sesję programu Windows PowerShell na urządzeniu:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Jeśli zostanie wyświetlony komunikat o błędzie związanym z relacją zaufania, sprawdź, czy łańcuch podpisywania certyfikatu węzła przekazany do urządzenia został również zainstalowany na kliencie, który uzyskuje dostęp do urządzenia.

    > [!NOTE] 
    > W przypadku korzystania z tej `-UseSSL` opcji użytkownik jest zdalny przy użyciu programu PowerShell za pośrednictwem *protokołu HTTPS*. Zalecane jest, aby zawsze używać *protokołu HTTPS* do zdalnego łączenia za pośrednictwem programu PowerShell. Mimo że sesja *http* nie jest najbezpieczniejsza, jest akceptowalna dla zaufanych sieci.

6. Podaj hasło po wyświetleniu monitu. Użyj tego samego hasła, które jest używane do logowania się do lokalnego interfejsu użytkownika sieci Web. Domyślne hasło lokalnego interfejsu użytkownika sieci Web to *Password1*. Po pomyślnym nawiązaniu połączenia z urządzeniem za pomocą zdalnego programu PowerShell zobaczysz następujące przykładowe dane wyjściowe:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Nawiązywanie połączenia zdalnego z klienta systemu Linux

Na kliencie z systemem Linux, który będzie używany do nawiązywania połączeń:

- [Zainstaluj najnowszą wersję programu PowerShell Core dla systemu Linux](/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6&preserve-view=true) z usługi GitHub, aby uzyskać funkcję komunikacji zdalnej SSH. 
- [Zainstaluj tylko `gss-ntlmssp` pakiet z modułu NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). W przypadku klientów Ubuntu Użyj następującego polecenia:
    - `sudo apt-get install gss-ntlmssp`

Aby uzyskać więcej informacji, przejdź do [komunikacji zdalnej programu PowerShell za pośrednictwem protokołu SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6&preserve-view=true).

Wykonaj następujące kroki, aby zdalnie nawiązać połączenie z klientem NFS.

1. Aby otworzyć sesję programu PowerShell, wpisz:

    `sudo pwsh`
 
2. Aby nawiązać połączenie przy użyciu klienta zdalnego, wpisz:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser -UseSSL`

    Po wyświetleniu monitu podaj hasło używane do logowania się do urządzenia.
 
> [!NOTE]
> Ta procedura nie działa na Mac OS.