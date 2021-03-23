---
title: Zainstaluj & Wdróż agenta systemu Linux C
description: Dowiedz się, jak zainstalować i wdrożyć usługę Defender dla agenta zabezpieczeń opartego na usłudze IoT C w systemie Linux
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6f59db7ff24412c66a6a4898b14272ea9540fdd2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778818"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Wdrażanie usługi Defender dla agenta zabezpieczeń opartego na usłudze IoT C dla systemu Linux

W tym przewodniku wyjaśniono, jak zainstalować i wdrożyć agenta zabezpieczeń opartego na usłudze Defender for IoT C w systemie Linux.

- Instalowanie
- Weryfikowanie wdrożenia
- Odinstalowywanie agenta
- Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku innych platform i rodzajów agentów zobacz [Wybieranie odpowiedniego agenta zabezpieczeń](how-to-deploy-agent.md).

1. W celu wdrożenia agenta zabezpieczeń wymagane są uprawnienia administratora lokalnego na komputerze, na którym ma zostać zainstalowana (sudo).

1. [Utwórz usługę Defender-IoT-Micro-Agent](quickstart-create-security-twin.md) dla urządzenia.

## <a name="installation"></a>Instalacja

Aby zainstalować i wdrożyć agenta zabezpieczeń, użyj następującego przepływu pracy:

1. Pobierz najnowszą wersję na swoją maszynę z usługi [GitHub](https://aka.ms/iot-security-github-c).

1. Wyodrębnij zawartość pakietu i przejdź do folderu _/src/Installation_ .

1. Dodaj uruchomione uprawnienia do **skryptu InstallSecurityAgent** , uruchamiając następujące polecenie:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Następnie uruchom polecenie:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Zobacz [jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md) , aby uzyskać więcej informacji na temat parametrów uwierzytelniania.

Ten skrypt wykonuje następującą funkcję:

1. Instaluje wymagania wstępne.

1. Dodaje użytkownika usługi (z wyłączonym logowaniem interakcyjnym).

1. Instaluje agenta jako **demon** — zakłada, że urządzenie używa **systemu** do zarządzania usługami.

1. Konfiguruje agenta przy użyciu podanych parametrów uwierzytelniania.

Aby uzyskać dodatkową pomoc, uruchom skrypt za pomocą parametru – help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Odinstalowywanie agenta

Aby odinstalować agenta, uruchom skrypt za pomocą parametru –-Uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Sprawdź stan wdrożenia, uruchamiając następujące:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem](overview.md) usługi Defender for IoT
- Dowiedz się więcej o [architekturze](architecture.md) usługi Defender for IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często zadawane pytania](resources-frequently-asked-questions.md)
- Informacje o [alertach zabezpieczeń](concept-security-alerts.md)
