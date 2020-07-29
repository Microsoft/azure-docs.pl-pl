---
title: Zainstaluj & Wdróż agenta systemu Linux C
description: Dowiedz się, jak zainstalować Azure Security Center dla agenta IoT zarówno na 32-bitowym, jak i 64-bitowym systemie Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311185"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Wdrażanie agenta zabezpieczeń usługi Azure Security Center dla IoT opartego na języku C dla systemu Linux

W tym przewodniku wyjaśniono, jak zainstalować i wdrożyć Azure Security Center dla agenta zabezpieczeń usługi IoT C na komputerze z systemem Linux.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie
> * Weryfikowanie wdrożenia
> * Odinstalowywanie agenta
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku innych platform i rodzajów agentów zobacz [Wybieranie odpowiedniego agenta zabezpieczeń](how-to-deploy-agent.md).

1. W celu wdrożenia agenta zabezpieczeń wymagane są uprawnienia administratora lokalnego na komputerze, na którym ma zostać zainstalowana (sudo).

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla urządzenia.

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

- Przeczytaj Azure Security Center dla usługi IoT [— Omówienie](overview.md)
- Dowiedz się więcej o [architekturze](architecture.md) Azure Security Center dla usługi IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często zadawane pytania](resources-frequently-asked-questions.md)
- Informacje o [alertach zabezpieczeń](concept-security-alerts.md)
