---
title: Moduł zabezpieczeń dla IoT Edge
description: Zapoznaj się z architekturą i możliwościami Azure Security Center dla modułu zabezpieczeń IoT dla IoT Edge.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0eb68f517c4c5b04dd4f92dd1a804dcf5ad24362
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310625"
---
# <a name="azure-iot-edge-security-module"></a>Moduł zabezpieczeń Azure IoT Edge

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) zapewnia zaawansowane możliwości zarządzania i wykonywania służbowych przepływów pracy na krawędzi.
Kluczowa część, która IoT Edge odgrywa w środowiskach IoT, sprawia, że jest szczególnie atrakcyjna dla złośliwych aktorów.

Azure Security Center dla usługi IoT Security to kompleksowe rozwiązanie zabezpieczeń dla IoT Edge urządzeń.
Azure Security Center dla modułu IoT zbiera, agreguje i analizuje surowe dane zabezpieczeń z systemu operacyjnego i systemu kontenerów w celu podejmowania działań i alertów dotyczących zabezpieczeń.

Podobnie jak w przypadku Azure Security Center dla agentów zabezpieczeń IoT dla urządzeń IoT, Azure Security Center dla modułu IoT Edge jest wysoce dostosowywany za pomocą sznurka modułu.
Aby dowiedzieć się więcej, zobacz [Configure the Agent](how-to-agent-configuration.md) .

Azure Security Center dla usługi IoT Security module dla IoT Edge oferuje następujące funkcje:

- Zbiera nieprzetworzone zdarzenia zabezpieczeń z bazowego systemu operacyjnego (Linux) i IoT Edge systemów kontenerów.

  Aby dowiedzieć się więcej na temat dostępnych modułów zbierających dane zabezpieczeń, zobacz [Azure Security Center dla konfiguracji agenta IoT](how-to-agent-configuration.md) .

- Analiza manifestów wdrażania IoT Edge.

- Agreguje pierwotne zdarzenia zabezpieczeń do komunikatów wysyłanych za poorednictwem [centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Usuń konfigurację za pomocą sznurka modułu zabezpieczeń.

  Aby dowiedzieć się więcej, zobacz [konfigurowanie Azure Security Center dla agenta IoT](how-to-agent-configuration.md) .

Azure Security Center dla modułu zabezpieczeń IoT dla IoT Edge jest uruchamiany w trybie uprzywilejowanym w obszarze IoT Edge.
Tryb uprzywilejowany jest wymagany, aby umożliwić modułowi monitorowanie systemu operacyjnego i innych modułów IoT Edge.

## <a name="module-supported-platforms"></a>Platformy obsługiwane przez moduły

Azure Security Center dla usługi IoT Security module dla IoT Edge jest obecnie dostępny tylko dla systemu Linux.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o architekturze i możliwościach Azure Security Center modułu zabezpieczeń IoT dla IoT Edge.

Aby kontynuować wprowadzenie do usługi Azure Security Center na potrzeby wdrażania IoT, użyj następujących artykułów:

- Wdróż [moduł zabezpieczeń dla IoT Edge](how-to-deploy-edge.md)
- Dowiedz się, jak [skonfigurować moduł zabezpieczeń](how-to-agent-configuration.md)
- Przejrzyj Azure Security Center [wymagań wstępnych usługi](service-prerequisites.md) IoT
- Dowiedz się, jak [włączyć usługę Azure Security Center dla usługi IoT w IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [Azure Security Center na potrzeby usługi IoT — często zadawane pytania](resources-frequently-asked-questions.md)
