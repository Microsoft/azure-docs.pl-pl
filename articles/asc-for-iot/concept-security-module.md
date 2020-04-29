---
title: Moduł zabezpieczeń i bliźniaczych reprezentacji urządzeń
description: Dowiedz się więcej o koncepcji bliźniaczych reprezentacji modułów zabezpieczeń i sposobach ich użycia w Azure Security Center na potrzeby IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311444"
---
# <a name="security-module"></a>Moduł zabezpieczeń

W tym artykule wyjaśniono, jak Azure Security Center usługi IoT korzystają z bliźniaczych reprezentacji i modułów urządzeń.

## <a name="device-twins"></a>Bliźniaczych reprezentacji urządzenia

W przypadku rozwiązań IoT utworzonych na platformie Azure bliźniaczych reprezentacji urządzeń odgrywa kluczową rolę w zarządzaniu urządzeniami i automatyzacją procesów.

Azure Security Center dla IoT oferuje pełną integrację z istniejącą platformą zarządzania urządzeniami IoT, umożliwiając zarządzanie stanem zabezpieczeń urządzeń oraz korzystanie z istniejących możliwości sterowania urządzeniami. Integrację uzyskuje się, korzystając z mechanizmu IoT Hubej przędzy.

Dowiedz się więcej o koncepcji [bliźniaczych reprezentacji urządzeń](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) na platformie Azure IoT Hub.

## <a name="security-module-twins"></a>Bliźniaczych reprezentacji modułu zabezpieczeń

Azure Security Center dla IoT utrzymuje dwuosiowy moduł zabezpieczeń dla każdego urządzenia w usłudze.
Sznury modułowe zabezpieczeń zawiera wszystkie informacje dotyczące zabezpieczeń urządzeń dla każdego konkretnego urządzenia w rozwiązaniu.
Właściwości zabezpieczeń urządzeń są utrzymywane w dedykowanym module zabezpieczeń, w celu zapewnienia bezpieczniejszej komunikacji oraz do włączania aktualizacji i konserwacji, które wymagają mniejszej ilości zasobów.

Zobacz [Tworzenie sznurka modułu zabezpieczeń](quickstart-create-security-twin.md) i [Konfigurowanie agentów zabezpieczeń](how-to-agent-configuration.md) , aby dowiedzieć się, jak tworzyć, dostosowywać i konfigurować sznurek. Zobacz [Opis modułu bliźniaczych reprezentacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) , aby dowiedzieć się więcej o koncepcji modułu bliźniaczych reprezentacji w IoT Hub.

## <a name="see-also"></a>Zobacz także

- [Omówienie Azure Security Center IoT](overview.md)
- [Wdrażanie agentów zabezpieczeń](how-to-deploy-agent.md)
- [Metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)
