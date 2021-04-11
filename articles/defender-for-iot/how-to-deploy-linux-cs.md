---
title: Zainstaluj & Wdróż agenta C# w systemie Linux
description: Dowiedz się, jak zainstalować i wdrożyć agenta zabezpieczeń opartego na programie Defender for IoT C# w systemie Linux
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: c84a70928be13212b56636ad1fbb9baaadd0e7d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784207"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Wdrażanie programu Defender for IoT C# Agent zabezpieczeń dla systemu Linux

W tym przewodniku wyjaśniono, jak zainstalować i wdrożyć agenta zabezpieczeń opartego na programie Defender for IoT C# w systemie Linux.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Instalowanie
- Weryfikowanie wdrożenia
- Odinstalowywanie agenta
- Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku innych platform i rodzajów agentów zobacz [Wybieranie odpowiedniego agenta zabezpieczeń](how-to-deploy-agent.md).

1. W celu wdrożenia agenta zabezpieczeń wymagane są uprawnienia administratora lokalnego na komputerze, na którym ma zostać zainstalowana.

1. [Utwórz usługę Defender-IoT-Micro-Agent](quickstart-create-security-twin.md) dla urządzenia.

## <a name="installation"></a>Instalacja

Aby wdrożyć agenta zabezpieczeń, wykonaj następujące czynności:

1. Pobierz najnowszą wersję na swoją maszynę z usługi [GitHub](https://aka.ms/iot-security-github-cs).

1. Wyodrębnij zawartość pakietu i przejdź do folderu _/Install_ .

1. Dodaj uruchomione uprawnienia do **skryptu InstallSecurityAgent** , uruchamiając `chmod +x InstallSecurityAgent.sh`

1. Następnie uruchom następujące polecenie z **uprawnieniami głównymi**:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   Aby uzyskać więcej informacji na temat parametrów uwierzytelniania, zobacz [jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md).

Ten skrypt wykonuje następujące czynności:

- Instaluje wymagania wstępne.

- Dodaje użytkownika usługi (z wyłączonym logowaniem interakcyjnym).

- Instaluje agenta jako **demon** — zakłada, że urządzenie używa **systemu** w ramach klasycznego modelu wdrażania.

- Konfiguruje **sudo** , aby umożliwić agentowi wykonywanie określonych zadań jako głównych.

- Konfiguruje agenta przy użyciu podanych parametrów uwierzytelniania.

Aby uzyskać dodatkową pomoc, uruchom skrypt za pomocą parametru – help: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Odinstalowywanie agenta

Aby odinstalować agenta, uruchom skrypt za pomocą parametru – u: `./InstallSecurityAgent.sh -u` .

> [!NOTE]
> Dezinstalacja nie usuwa żadnych brakujących wymagań wstępnych, które zostały zainstalowane podczas instalacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

1. Sprawdź stan wdrożenia, uruchamiając następujące:

    `systemctl status ASCIoTAgent.service`

1. Włącz rejestrowanie.
   Jeśli uruchomienie agenta nie powiedzie się, Włącz rejestrowanie, aby uzyskać więcej informacji.

   Włącz rejestrowanie, wykonując następujące instrukcje:

   1. Otwórz plik konfiguracji do edycji w dowolnym edytorze systemu Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Edytuj następujące wartości:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       Wartość **LogFilePath** można skonfigurować.

       > [!NOTE]
       > Zalecamy **wyłączenie wylogowywania po zakończeniu** rozwiązywania problemów. Pozostawienie **logowania powoduje zwiększenie** rozmiaru pliku dziennika i użycie danych.

   1. Uruchom ponownie agenta, uruchamiając:

       `systemctl restart ASCIoTAgent.service`

   1. Przejrzyj plik dziennika, aby uzyskać więcej informacji o błędzie.

       Lokalizacja pliku dziennika: `/var/ASCIoTAgent/IotAgentLog.log`

       Zmień ścieżkę lokalizacji pliku zgodnie z nazwą wybraną dla **LogFilePath** w kroku 2.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem](overview.md) usługi Defender for IoT
- Dowiedz się więcej o [architekturze](architecture.md) usługi Defender for IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często zadawane pytania](resources-frequently-asked-questions.md)
- Informacje o [alertach](concept-security-alerts.md)
