---
title: Defender-IoT-Micro-Agent i bliźniaczych reprezentacji urządzeń
description: Dowiedz się więcej o koncepcji usługi Defender-IoT-Micro-Agent bliźniaczych reprezentacji oraz o sposobie ich użycia w usłudze Defender for IoT.
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: 1ed6faf03d168ed7a2a2f07733cb7e238d234915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779175"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-Micro-Agent

W tym artykule wyjaśniono, jak usługa Defender for IoT używa urządzeń bliźniaczych reprezentacji i modułów.

## <a name="device-twins"></a>Bliźniaczych reprezentacji urządzenia

W przypadku rozwiązań IoT utworzonych na platformie Azure bliźniaczych reprezentacji urządzeń odgrywa kluczową rolę w zarządzaniu urządzeniami i automatyzacją procesów.

Usługa Defender for IoT oferuje pełną integrację z istniejącą platformą zarządzania urządzeniami IoT, umożliwiając zarządzanie stanem zabezpieczeń urządzeń oraz korzystanie z istniejących możliwości sterowania urządzeniami. Integrację uzyskuje się, korzystając z mechanizmu IoT Hubej przędzy.

Dowiedz się więcej o koncepcji [bliźniaczych reprezentacji urządzeń](../iot-hub/iot-hub-devguide-device-twins.md) na platformie Azure IoT Hub.

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-Micro-Agent bliźniaczych reprezentacji

Usługa Defender for IoT utrzymuje sznury usługi Defender-IoT-Micro-Agent dla każdego urządzenia w usłudze.
Sznurek usługi Defender-IoT-Micro-Agent zawiera wszystkie informacje dotyczące zabezpieczeń urządzeń dla każdego konkretnego urządzenia w rozwiązaniu.
Właściwości zabezpieczeń urządzeń są utrzymywane w dedykowanych sznurach usługi Defender-IoT-Micro-Agent do bezpieczniejszej komunikacji oraz do włączania aktualizacji i konserwacji, które wymagają mniejszej ilości zasobów.

Aby dowiedzieć się, jak tworzyć, dostosowywać i konfigurować sznurek, zobacz [Create Defender-IoT-Micro-Agent bliźniaczy](quickstart-create-security-twin.md) i [Konfigurowanie agentów zabezpieczeń](how-to-agent-configuration.md) . Zobacz [Opis modułu bliźniaczych reprezentacji](../iot-hub/iot-hub-devguide-module-twins.md) , aby dowiedzieć się więcej o koncepcji modułu bliźniaczych reprezentacji w IoT Hub.

## <a name="see-also"></a>Zobacz też

- [Omówienie usługi Defender for IoT](overview.md)
- [Wdrażanie agentów zabezpieczeń](how-to-deploy-agent.md)
- [Metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)