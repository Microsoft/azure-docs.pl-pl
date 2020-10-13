---
title: Zainstaluj agenta C# na urządzeniu z systemem Windows
description: Dowiedz się więcej na temat sposobu instalowania usługi Defender for IoT Agent na 32-bitowych lub 64-bitowych urządzeniach z systemem Windows.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 04b33c7e63efbd6ffabf978708e1b8ed81f1fc42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940034"
---
# <a name="deploy-an-defender-for-iot-c-based-security-agent-for-windows"></a>Wdrażanie agenta zabezpieczeń opartego na programie Defender for IoT C# dla systemu Windows

W tym przewodniku wyjaśniono, jak zainstalować agenta zabezpieczeń oparty na programie Defender for IoT C# w systemie Windows.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zainstaluj
> * Weryfikowanie wdrożenia
> * Odinstalowywanie agenta
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku innych platform i rodzajów agentów zobacz [Wybieranie odpowiedniego agenta zabezpieczeń](how-to-deploy-agent.md).

1. Prawa administratora lokalnego na komputerze, na którym chcesz się zainstalować.

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla urządzenia.

## <a name="installation"></a>Instalacja

Aby zainstalować agenta zabezpieczeń, użyj następującego przepływu pracy:

1. Zainstaluj agenta usługi Defender dla systemu Windows C# na urządzeniu. Pobierz najnowszą wersję na swoją maszynę z repozytorium usługi Defender for IoT [GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Wyodrębnij zawartość pakietu i przejdź do folderu/install.

1. Otwórz program Windows PowerShell jako administrator.
1. Dodaj uruchomione uprawnienia do skryptu InstallSecurityAgent, uruchamiając:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    następnie uruchom polecenie:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Na przykład:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Aby uzyskać więcej informacji na temat parametrów uwierzytelniania, zobacz [jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md).

Ten skrypt wykonuje następujące czynności:

* Instaluje wymagania wstępne.
* Dodaje użytkownika usługi (z wyłączonym logowaniem interakcyjnym).
* Instaluje agenta jako **usługę systemową**.
* Konfiguruje agenta przy użyciu podanych parametrów uwierzytelniania.

Aby uzyskać dodatkową pomoc, użyj polecenia Get-Help w programie PowerShell.

Get-Help przykład:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Sprawdź stan wdrożenia

Sprawdź stan wdrożenia agenta, uruchamiając następujące:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Odinstalowywanie agenta

Aby odinstalować agenta:

1. Uruchom następujący skrypt programu PowerShell z parametrem **-mode** ustawionym do **odinstalowania**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchomienie agenta nie powiedzie się, Włącz rejestrowanie (domyślnie jest ono *wyłączone* ), aby uzyskać więcej informacji.

Aby włączyć rejestrowanie:

1. Otwórz plik konfiguracji (General.config) do edycji przy użyciu standardowego edytora plików.

1. Edytuj następujące wartości:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Zalecamy **wyłączenie wylogowywania po zakończeniu** rozwiązywania problemów. Pozostawienie **logowania powoduje zwiększenie** rozmiaru pliku dziennika i użycie danych.

1. Uruchom ponownie agenta, uruchamiając następujące polecenie programu PowerShell lub wiersza polecenia:

    **Narzędzia**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   lub

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Przejrzyj plik dziennika, aby uzyskać więcej informacji o błędzie. Plik dziennika powinien znajdować się w katalogu roboczym, w którym jest uruchamiany skrypt. 

   Lokalizacja pliku dziennika: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [omówieniem](overview.md) usługi Defender for IoT
* Dowiedz się więcej o [architekturze](architecture.md) usługi Defender for IoT
* Włącz [usługę](quickstart-onboard-iot-hub.md)
* Przeczytaj [często zadawane pytania](resources-frequently-asked-questions.md)
* Informacje o [alertach](concept-security-alerts.md)
