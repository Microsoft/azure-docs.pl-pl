---
title: Architektura agenta zabezpieczeń
description: Informacje o architekturze agentów zabezpieczeń agentów używanych w Azure Security Center dla usługi IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310682"
---
# <a name="security-agent-reference-architecture"></a>Architektura referencyjna agenta zabezpieczeń

Azure Security Center dla usługi IoT oferuje architekturę referencyjną dla agentów zabezpieczeń, które rejestrują, przetwarzają, agregują i wysyłają dane zabezpieczeń za IoT Hub.

Agenci zabezpieczeń są zaprojektowani do pracy w ograniczonym środowisku IoT i są wysoce dostosowywane pod względem wartości, które zapewniają w porównaniu z zasobami, których używają.

Agenci zabezpieczeń obsługują następujące funkcje:

- Zbieraj surowe zdarzenia zabezpieczeń z bazowego systemu operacyjnego (Linux, Windows). Aby dowiedzieć się więcej na temat dostępnych modułów zbierających dane zabezpieczeń, zobacz [Azure Security Center for IoT — Konfiguracja agenta](how-to-agent-configuration.md).

- Agregowanie nieprzetworzonych zdarzeń zabezpieczeń do komunikatów wysyłanych za poorednictwem IoT Hub.

- Uwierzytelnianie przy użyciu istniejącej tożsamości urządzenia lub dedykowanej tożsamości modułu. Zobacz [metody uwierzytelniania agentów zabezpieczeń](concept-security-agent-authentication-methods.md) , aby dowiedzieć się więcej.

- Skonfiguruj zdalnie za pomocą sznurka modułu **azureiotsecurity** . Aby dowiedzieć się więcej, zobacz [konfigurowanie Azure Security Center dla agenta IoT](how-to-agent-configuration.md).

Azure Security Center dla agentów zabezpieczeń IoT są tworzone jako projekty open source i dostępne w serwisie GitHub:

- [Azure Security Center dla agenta usługi IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Azure Security Center dla agenta opartego na programie IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Platformy obsługiwane przez agenta

Azure Security Center dla IoT oferuje różnych agentów Instalatora dla systemu Windows w wersji 32 i 64-bitowych, a w przypadku wersji 32-bitowej i 64-bitowej Linux. Upewnij się, że dysponujesz odpowiednim instalatorem agenta dla każdego urządzenia, zgodnie z poniższą tabelą:

| Architektura | Linux | Windows |    Szczegóły|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| bitowe  | C  | C#  ||
| -  | C# lub C           | C#      | Zalecamy używanie agenta C dla urządzeń z bardziej ograniczonymi lub minimalnymi zasobami urządzeń.|
|

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono Azure Security Center informacje dotyczące architektury agentów zabezpieczeń IoT i dostępnych instalatorów.

Aby kontynuować wprowadzenie do usługi Azure Security Center na potrzeby wdrażania IoT, użyj następujących artykułów:

- Informacje na temat [metod uwierzytelniania agentów zabezpieczeń](concept-security-agent-authentication-methods.md)
- Wybierz i Wdróż [agenta zabezpieczeń](how-to-deploy-agent.md)
- Przejrzyj Azure Security Center [wymagań wstępnych usługi](service-prerequisites.md) IoT
- Dowiedz się, jak [włączyć usługę Azure Security Center dla usługi IoT w IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [Azure Security Center na potrzeby usługi IoT — często zadawane pytania](resources-frequently-asked-questions.md)
