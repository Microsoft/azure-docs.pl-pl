---
title: Architektura agenta zabezpieczeń
description: Informacje o architekturze agentów zabezpieczeń agentów używanych w usłudze Azure Defender dla usługi IoT.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 64d90b4d9dc3efbe877230bbc20780b1c4f2d213
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939751"
---
# <a name="security-agent-reference-architecture"></a>Architektura referencyjna agenta zabezpieczeń

Usługa Azure Defender for IoT oferuje architekturę referencyjną dla agentów zabezpieczeń, które rejestrują, przetwarzają, agregują i wysyłają dane zabezpieczeń za pomocą IoT Hub.

Agenci zabezpieczeń są zaprojektowani do pracy w ograniczonym środowisku IoT i są wysoce dostosowywane pod względem wartości, które zapewniają w porównaniu z zasobami, których używają.

Agenci zabezpieczeń obsługują następujące funkcje:

- Zbieraj surowe zdarzenia zabezpieczeń z bazowego systemu operacyjnego (Linux, Windows). Aby dowiedzieć się więcej na temat dostępnych modułów zbierających dane zabezpieczeń, zobacz [Konfiguracja agenta usługi Defender for IoT](how-to-agent-configuration.md).

- Agregowanie nieprzetworzonych zdarzeń zabezpieczeń do komunikatów wysyłanych za poorednictwem IoT Hub.

- Uwierzytelnianie przy użyciu istniejącej tożsamości urządzenia lub dedykowanej tożsamości modułu. Zobacz [metody uwierzytelniania agentów zabezpieczeń](concept-security-agent-authentication-methods.md) , aby dowiedzieć się więcej.

- Skonfiguruj zdalnie za pomocą sznurka modułu **azureiotsecurity** . Aby dowiedzieć się więcej, zobacz [Konfigurowanie usługi Defender for IoT Agent](how-to-agent-configuration.md).

Usługi Defender Security Agents są opracowywane jako projekty open source i dostępne w serwisie GitHub:

- [Usługa Defender for IoT C-based Agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Usługa Defender for IoT — agent oparty na języku C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Platformy obsługiwane przez agenta

Usługa Defender for IoT oferuje różnych agentów Instalatora dla systemu Windows w wersji 32-bitowej i 64-bitowych, a w przypadku wersji 32-bitowej i 64-bitowej. Upewnij się, że dysponujesz odpowiednim instalatorem agenta dla każdego urządzenia, zgodnie z poniższą tabelą:

| Architektura | Linux | Windows |    Szczegóły|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| bitowe  | C  | C#  ||
| -  | C# lub C           | C#      | Zalecamy używanie agenta C dla urządzeń z bardziej ograniczonymi lub minimalnymi zasobami urządzeń.|
|

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o architekturze usługi Defender Security Agent i dostępnych instalatorach.

Aby kontynuować wprowadzenie do usługi Defender for IoT, użyj następujących artykułów:

- Informacje na temat [metod uwierzytelniania agentów zabezpieczeń](concept-security-agent-authentication-methods.md)
- Wybierz i Wdróż [agenta zabezpieczeń](how-to-deploy-agent.md)
- Zapoznaj się z [wymaganiami wstępnymi](service-prerequisites.md) dotyczącymi usługi Defender for IoT
- Dowiedz się, jak [włączyć usługę Defender for IoT w IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z usługi [Defender for IoT — często zadawane pytania](resources-frequently-asked-questions.md)
