---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f9707ab48d64ede2e796675eb3a0a6e7820c7073
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603279"
---
W zależności od systemu operacyjnego klienta procedury zdalnego łączenia się z urządzeniem są różne.

### <a name="remotely-connect-from-a-windows-client"></a>Nawiązywanie połączenia zdalnego z klienta systemu Windows

Przed rozpoczęciem upewnij się, że na kliencie systemu Windows jest uruchomiony program Windows PowerShell 5,0 lub nowszy.

Wykonaj następujące kroki, aby zdalnie nawiązać połączenie z klientem systemu Windows.

1. Uruchom sesję programu Windows PowerShell jako administrator.
2. Upewnij się, że na kliencie jest uruchomiona usługa Windows Remote Management. W wierszu polecenia wpisz polecenie:

    `winrm quickconfig`

3. Przypisz zmienną do adresu IP urządzenia.

    $ip = "<device_ip>"

    Zastąp `<device_ip>` adres IP urządzenia.

4. Aby dodać adres IP urządzenia do listy zaufanych hostów klienta, wpisz następujące polecenie:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Uruchom sesję programu Windows PowerShell na urządzeniu:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Podaj hasło po wyświetleniu monitu. Użyj tego samego hasła, które jest używane do logowania się do lokalnego interfejsu użytkownika sieci Web. Domyślne hasło lokalnego interfejsu użytkownika sieci Web to *Password1*. Po pomyślnym nawiązaniu połączenia z urządzeniem za pomocą zdalnego programu PowerShell zobaczysz następujące przykładowe dane wyjściowe:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Nawiązywanie połączenia zdalnego z klienta systemu Linux

Na kliencie z systemem Linux, który będzie używany do nawiązywania połączeń:

- [Zainstaluj najnowszą wersję programu PowerShell Core dla systemu Linux](/powershell/scripting/install/installing-powershell-core-on-linux) z usługi GitHub, aby uzyskać funkcję komunikacji zdalnej SSH. 
- [Zainstaluj tylko `gss-ntlmssp` pakiet z modułu NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). W przypadku klientów Ubuntu Użyj następującego polecenia:
    - `sudo apt-get install gss-ntlmssp`

Aby uzyskać więcej informacji, przejdź do [komunikacji zdalnej programu PowerShell za pośrednictwem protokołu SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Wykonaj następujące kroki, aby zdalnie nawiązać połączenie z klientem NFS.

1. Aby otworzyć sesję programu PowerShell, wpisz:

    `pwsh`
 
2. Aby nawiązać połączenie przy użyciu klienta zdalnego, wpisz:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Po wyświetleniu monitu podaj hasło używane do logowania się do urządzenia.
 
> [!NOTE]
> Ta procedura nie działa w przypadku macOS.