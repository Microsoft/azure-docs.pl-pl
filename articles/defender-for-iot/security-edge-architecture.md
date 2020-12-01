---
title: Usługa Defender for IoT Security module dla IoT Edge
description: Zapoznaj się z architekturą i możliwościami usługi Azure Defender for IoT Security module dla IoT Edge.
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
ms.openlocfilehash: 4f6d9f670a1b85e55ccc8f6cb18645b92927221a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351643"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Moduł zabezpieczeń usługi Azure Defender dla IoT Edge

[Azure IoT Edge](../iot-edge/index.yml) zapewnia zaawansowane możliwości zarządzania i wykonywania służbowych przepływów pracy na krawędzi.
Kluczowa część, która IoT Edge odgrywa w środowiskach IoT, sprawia, że jest szczególnie atrakcyjna dla złośliwych aktorów.

Usługa Defender for IoT Security module oferuje kompleksowe rozwiązanie zabezpieczeń dla urządzeń IoT Edge.
Moduł Defender for IoT zbiera, agreguje i analizuje pierwotne dane zabezpieczeń z systemu operacyjnego i systemu kontenerów w celu podejmowania działań i alertów dotyczących zabezpieczeń.

Podobnie jak usługa Defender dla agentów zabezpieczeń IoT dla urządzeń IoT, moduł Defender for IoT Edge jest wysoce dostosowywalny za pomocą sznurka modułu.
Aby dowiedzieć się więcej, zobacz [Configure the Agent](how-to-agent-configuration.md) .

Usługa Defender for IoT Security module dla IoT Edge oferuje następujące funkcje:

- Zbiera nieprzetworzone zdarzenia zabezpieczeń z bazowego systemu operacyjnego (Linux) i IoT Edge systemów kontenerów.

  Aby dowiedzieć się więcej na temat dostępnych modułów zbierających dane zabezpieczeń, zobacz [Konfiguracja agenta usługi Defender for IoT](how-to-agent-configuration.md) .

- Analiza manifestów wdrażania IoT Edge.

- Agreguje pierwotne zdarzenia zabezpieczeń do komunikatów wysyłanych za poorednictwem [centrum IoT Edge](../iot-edge/iot-edge-runtime.md#iot-edge-hub).

- Usuń konfigurację za pomocą sznurka modułu zabezpieczeń.

  Aby dowiedzieć się więcej, zobacz [Konfigurowanie agenta usługi Defender for IoT](how-to-agent-configuration.md) .

Usługa Defender for IoT Security module dla IoT Edge jest uruchamiana w trybie uprzywilejowanym w obszarze IoT Edge.
Tryb uprzywilejowany jest wymagany, aby umożliwić modułowi monitorowanie systemu operacyjnego i innych modułów IoT Edge.

## <a name="module-supported-platforms"></a>Platformy obsługiwane przez moduły

Usługa Defender for IoT Security module dla IoT Edge jest obecnie dostępna tylko dla systemu Linux.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o architekturze i możliwościach modułu usługi Defender for IoT dla IoT Edge.

Aby kontynuować wprowadzenie do usługi Defender for IoT, użyj następujących artykułów:

- Wdróż [moduł zabezpieczeń dla IoT Edge](how-to-deploy-edge.md)
- Dowiedz się, jak [skonfigurować moduł zabezpieczeń](how-to-agent-configuration.md)
- Zapoznaj się z [wymaganiami wstępnymi](service-prerequisites.md) dotyczącymi usługi Defender for IoT
- Dowiedz się, jak [włączyć usługę Defender for IoT w IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z usługi [Defender for IoT — często zadawane pytania](resources-frequently-asked-questions.md)