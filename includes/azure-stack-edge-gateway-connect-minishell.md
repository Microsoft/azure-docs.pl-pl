---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081151"
---
W zależności od systemu operacyjnego klienta procedury zdalnego łączenia się z urządzeniem są różne.

### <a name="remotely-connect-from-a-windows-client"></a>Nawiązywanie połączenia zdalnego z klienta systemu Windows


#### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Klient systemu Windows korzysta z programu Windows PowerShell 5,0 lub nowszego.
- Klient systemu Windows ma łańcuch podpisywania (certyfikat główny) odpowiadający certyfikatowi węzła zainstalowanego na urządzeniu. Aby uzyskać szczegółowe instrukcje, zobacz [Instalowanie certyfikatu na kliencie systemu Windows](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
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

    Jeśli nie używasz certyfikatów (zalecamy korzystanie z certyfikatów!), możesz pominąć ten test, korzystając z opcji sesji: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` .

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > W przypadku korzystania z tej `-UseSSL` opcji użytkownik jest zdalny przy użyciu programu PowerShell za pośrednictwem *protokołu HTTPS*. Zalecane jest, aby zawsze używać *protokołu HTTPS* do zdalnego łączenia za pośrednictwem programu PowerShell. 

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

> [!IMPORTANT]
> W bieżącej wersji można nawiązać połączenie z interfejsem programu PowerShell urządzenia tylko za pośrednictwem klienta systemu Windows. `-UseSSL`Opcja nie działa z klientami systemu Linux.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->