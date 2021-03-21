---
title: 'Szybki Start: Omówienie agentów zabezpieczeń'
description: W tym przewodniku szybki start dowiesz się, jak zrozumieć architekturę agentów zabezpieczeń dla agentów używanych w usłudze Azure Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: de8c52372b2ef92dcf2abe357a40ef8947439a8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493970"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Szybki Start: architektura referencyjna agenta zabezpieczeń

Usługa Azure Defender for IoT oferuje architekturę referencyjną dla agentów zabezpieczeń, które rejestrują, przetwarzają, agregują i wysyłają dane zabezpieczeń za pomocą IoT Hub.

Agenci zabezpieczeń są zaprojektowani do pracy w ograniczonym środowisku IoT i są wysoce dostosowywane pod względem wartości, które zapewniają w porównaniu z zasobami, których używają.

Agenci zabezpieczeń obsługują następujące funkcje:

- Uwierzytelnianie przy użyciu istniejącej tożsamości urządzenia lub dedykowanej tożsamości modułu. Aby dowiedzieć się więcej, zobacz [metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md).

- Zbieraj surowe zdarzenia zabezpieczeń z bazowego systemu operacyjnego (Linux, Windows). Aby dowiedzieć się więcej na temat dostępnych modułów zbierających dane zabezpieczeń, zobacz [Konfiguracja agenta usługi Defender for IoT](how-to-agent-configuration.md).

- Agregowanie nieprzetworzonych zdarzeń zabezpieczeń do komunikatów wysyłanych za poorednictwem IoT Hub.

- Skonfiguruj zdalnie za pomocą sznurka modułu **azureiotsecurity** . Aby dowiedzieć się więcej, zobacz [Konfigurowanie usługi Defender for IoT Agent](how-to-agent-configuration.md).

Usługi Defender Security Agents są opracowywane jako projekty open source i dostępne w serwisie GitHub:

- [Usługa Defender for IoT C-based Agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Usługa Defender for IoT — agent oparty na języku C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="prerequisites"></a>Wymagania wstępne

Brak

## <a name="agent-supported-platforms"></a>Platformy obsługiwane przez agenta

Usługa Defender for IoT oferuje różnych agentów Instalatora dla 32 bitowych i 64-bitowych systemu Windows, które są takie same dla 32 bit i 64-bit Linux. Upewnij się, że dysponujesz odpowiednim instalatorem agenta dla każdego urządzenia, zgodnie z poniższą tabelą:

| Architektura | Linux | Windows | Szczegóły |
|--|--|--|--|
| 32 bit | C | C# |  |
| 64-bitowa | C# lub C | C# | Zalecamy używanie agenta C dla urządzeń z bardziej ograniczonymi lub minimalnymi zasobami urządzeń. |


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano ogólne omówienie architektury Defender for IoT Defender-IoT-Micro-Agent i dostępnych instalatorów.

Aby kontynuować wprowadzenie do usługi Defender for IoT, użyj następujących artykułów:

> [!div class="nextstepaction"]
> [metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)
