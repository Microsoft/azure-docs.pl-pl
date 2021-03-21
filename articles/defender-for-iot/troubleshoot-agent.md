---
title: Rozwiązywanie problemów z uruchamianiem agenta zabezpieczeń (Linux)
description: Rozwiązywanie problemów z pracą z usługą Azure Defender for IoT Security Agents dla systemu Linux.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 7be6cf1df15d7afd7cb9447be68ff70ff7b14d03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449224"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Przewodnik rozwiązywania problemów z agentem zabezpieczeń (Linux)

W tym artykule wyjaśniono, jak rozwiązać potencjalne problemy w procesie uruchamiania agenta zabezpieczeń.

Usługa Azure Defender dla programu IoT Agent jest samoczynnie uruchamiana natychmiast po zakończeniu instalacji. Proces uruchamiania agenta obejmuje odczytywanie konfiguracji lokalnej, nawiązywanie połączenia z usługą Azure IoT Hub i pobieranie konfiguracji zdalnej przędzy. Niepowodzenie w jednym z tych kroków może spowodować niepowodzenie działania agenta zabezpieczeń.

W tym przewodniku rozwiązywania problemów dowiesz się, jak:

- Sprawdź, czy Agent zabezpieczeń jest uruchomiony
- Pobieranie błędów agenta zabezpieczeń
- Zrozumienie i korygowanie błędów agentów zabezpieczeń

## <a name="validate-if-the-security-agent-is-running"></a>Sprawdź, czy Agent zabezpieczeń jest uruchomiony

1. Aby sprawdzić, czy Agent zabezpieczeń jest uruchomiony, zaczekaj kilka minut po zainstalowaniu agenta i uruchom następujące polecenie.
     <br>

    **Agent języka C**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **Agent C#**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Jeśli polecenie zwróci pusty wiersz, Agent zabezpieczeń nie został uruchomiony pomyślnie.

## <a name="force-stop-the-security-agent"></a>Wymuś zatrzymanie agenta zabezpieczeń

W przypadkach, gdy nie można uruchomić agenta zabezpieczeń, Zatrzymaj agenta za pomocą poniższego polecenia, a następnie przejdź do tabeli błędów poniżej:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Pobieranie błędów agenta zabezpieczeń

1. Pobierz błędy agenta zabezpieczeń, uruchamiając następujące polecenie:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. Polecenie Pobierz błąd agenta zabezpieczeń pobiera wszystkie dzienniki utworzone przez usługę Defender for IoT Agent. Skorzystaj z poniższej tabeli, aby poznać błędy i wykonać odpowiednie czynności naprawcze.

> [!Note]
> Dzienniki błędów są wyświetlane w kolejności chronologicznej. Pamiętaj, aby zanotować sygnaturę czasową każdego błędu, aby ułatwić korygowanie.

## <a name="restart-the-agent"></a>Ponowne uruchamianie agenta

1. Po znalezieniu i naprawieniu błędu agenta zabezpieczeń spróbuj ponownie uruchomić agenta, uruchamiając następujące polecenie.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Powtórz poprzedni proces, aby pobrać zatrzymanie i pobrać błędy, jeśli Agent nadal kończy pracę.

## <a name="understand-security-agent-errors"></a>Omówienie błędów agenta zabezpieczeń

Większość błędów agentów zabezpieczeń jest wyświetlanych w następującym formacie:

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Kod błędu | Kod podrzędny błędu | Szczegóły błędu | Koryguj C | Koryguj C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Konfiguracja lokalna | Brakująca konfiguracja | Brak konfiguracji w lokalnym pliku konfiguracyjnym. Komunikat o błędzie powinien określać, który klucz nie istnieje. | Dodaj brakujący klucz do LocalConfiguration.js/var/w pliku, aby uzyskać szczegółowe informacje, zobacz [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) .| Dodaj brakujący klucz do pliku General.config, aby uzyskać szczegółowe informacje, zobacz [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Konfiguracja lokalna | Niemożliwa analiza konfiguracji | Nie można przeanalizować wartości konfiguracji. Komunikat o błędzie powinien określać, który klucz nie może zostać przeanalizowany. Nie można przeanalizować wartości konfiguracji, ponieważ wartość nie znajduje się w oczekiwanym typie lub wartość znajduje się poza zakresem. | Popraw wartość klucza w/var/LocalConfiguration.jsw pliku, aby był zgodny ze schematem LocalConfiguration, zobacz [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) , aby uzyskać szczegółowe informacje. |  Popraw wartość klucza w pliku General.config, aby był zgodny ze schematem, zobacz [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) , aby uzyskać szczegółowe informacje.|
| Konfiguracja lokalna | Format pliku | Nie można przeanalizować pliku konfiguracji. | Plik konfiguracji jest uszkodzony, pobierz agenta i zainstaluj go ponownie. | |
| Konfiguracja zdalna | Limit czasu | Agent nie może pobrać sznurka modułu azureiotsecurity w określonym limicie czasu. | Upewnij się, że konfiguracja uwierzytelniania jest poprawna, i spróbuj ponownie. | Agent nie może pobrać sznurka modułu azureiotsecurity w ramach limitu czasu. | Upewnij się, że konfiguracja uwierzytelniania jest poprawna, i spróbuj ponownie. |
| Authentication | Plik nie istnieje | Plik w danej ścieżce nie istnieje. | Upewnij się, że plik istnieje w danej ścieżce lub przejdź do **LocalConfiguration.jsw** pliku i Zmień konfigurację **ścieżki** . | Upewnij się, że plik istnieje w danej ścieżce lub przejdź do pliku **Authentication.config** i Zmień konfigurację **ścieżki** .|
| Authentication | Uprawnienie do pliku | Agent nie ma wystarczających uprawnień, aby otworzyć plik. | Nadaj użytkownikowi **asciotagent** uprawnienia do odczytu pliku w danej ścieżce. | Upewnij się, że plik jest dostępny. |
| Authentication | Format pliku | Dany plik ma nieprawidłowy format. | Upewnij się, że plik jest w poprawnym formacie. Obsługiwane typy plików to PFX i PEM. | Upewnij się, że plik jest prawidłowym plikiem certyfikatu. |
| Authentication | Brak autoryzacji | Agent nie może uwierzytelnić się w odniesieniu do IoT Hub z podanym poświadczeniami. | Sprawdź poprawność konfiguracji uwierzytelniania w pliku LocalConfiguration, przejdź przez konfigurację uwierzytelniania i upewnij się, że wszystkie szczegóły są poprawne, i sprawdź, czy wpis tajny w pliku jest zgodny z uwierzytelnioną tożsamością. | Sprawdź poprawność konfiguracji uwierzytelniania w Authentication.config, przejdź przez konfigurację uwierzytelniania i upewnij się, że wszystkie szczegóły są poprawne, a następnie sprawdź, czy wpis tajny w pliku jest zgodny z uwierzytelnioną tożsamością.
| Authentication | Nie znaleziono | Znaleziono urządzenie/moduł. | Sprawdź poprawność konfiguracji uwierzytelniania — upewnij się, że nazwa hosta jest poprawna, urządzenie istnieje w IoT Hub i ma moduł azureiotsecurity bliźniaczy. |  Sprawdź poprawność konfiguracji uwierzytelniania — upewnij się, że nazwa hosta jest poprawna, urządzenie istnieje w IoT Hub i ma moduł azureiotsecurity bliźniaczy. |
| Authentication | Brakująca konfiguracja | Brak konfiguracji w pliku *Authentication.config* . Komunikat o błędzie powinien określać, który klucz nie istnieje. | Dodaj brakujący klucz do *LocalConfiguration.js* pliku.| Dodaj brakujący klucz do pliku *Authentication.config* , aby uzyskać szczegółowe informacje, zobacz [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Authentication | Niemożliwa analiza konfiguracji | Nie można przeanalizować wartości konfiguracji. Komunikat o błędzie powinien określać, który klucz nie może zostać przeanalizowany. Nie można przeanalizować wartości konfiguracji, ponieważ wartość nie jest oczekiwanego typu lub wartość znajduje się poza zakresem. |Popraw wartość klucza w **LocalConfiguration.js** pliku. |Popraw wartość klucza w pliku **Authentication.config** , aby dopasować schemat, zobacz [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) , aby uzyskać szczegółowe informacje.|
|

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem](overview.md) usługi Defender for IoT
- Dowiedz się więcej o [architekturze](architecture.md) usługi Defender for IoT
- Włączanie [usługi](quickstart-onboard-iot-hub.md) Defender for IoT
- Przeczytaj [często zadawane pytania](resources-frequently-asked-questions.md) dotyczące usługi Defender for IoT
- Dowiedz się, jak uzyskać dostęp do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- Omówienie [zaleceń](concept-recommendations.md)
- Informacje o [alertach](concept-security-alerts.md) zabezpieczeń
