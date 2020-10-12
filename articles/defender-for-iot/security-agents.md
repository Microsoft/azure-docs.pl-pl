---
title: Omówienie agenta zabezpieczeń
description: Rozpocznij pracę z zrozumieniem, konfigurowaniem, wdrażaniem i używaniem agentów usługi Azure Defender for IoT Security Service na urządzeniach IoT.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 888fdbb2352c4ac972eac8b24c29d22ce973e04d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939746"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Wprowadzenie do usługi Azure Defender dla agentów zabezpieczeń urządzeń IoT

Usługa Defender Security Agents oferuje ulepszone funkcje zabezpieczeń, takie jak monitorowanie połączeń zdalnych, aktywne aplikacje, zdarzenia logowania i najlepsze rozwiązania w zakresie konfiguracji systemu operacyjnego. Przejmij kontrolę nad ochroną pola urządzenia przed zagrożeniami i stan zabezpieczeń w ramach jednej usługi.

Dostępna jest architektura referencyjna dla agentów zabezpieczeń systemu Linux i Windows, zarówno w języku C#, jak i C.

Agenci zabezpieczeń usługi Defender for IoT obsługują nieprzetworzoną kolekcję zdarzeń z systemu operacyjnego urządzenia, agregację zdarzeń w celu zmniejszenia kosztów i konfiguracji za pośrednictwem sznurka modułu urządzenia. Komunikaty zabezpieczeń są wysyłane przez IoT Hub do usługi Defender dla usług IoT Analytics.

Użyj następującego przepływu pracy do wdrożenia i przetestowania usługi Defender dla agentów zabezpieczeń IoT:

1. [Włącz usługę Defender for IoT na IoT Hub](quickstart-onboard-iot-hub.md)
1. Jeśli IoT Hub nie ma zarejestrowanych urządzeń, [Zarejestruj nowe urządzenie](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Utwórz moduł zabezpieczeń azureiotsecurity](quickstart-create-security-twin.md) dla urządzeń.
1. Aby zainstalować agenta na symulowanym urządzeniu platformy Azure zamiast instalować go na rzeczywistym urządzeniu, uruchom [nową maszynę wirtualną platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) w dostępnej strefie.
1. [Wdróż usługę Defender for IoT Security Agent](how-to-deploy-linux-cs.md) na urządzeniu IoT lub nową maszynę wirtualną.
1. Postępuj zgodnie z instrukcjami [trigger_events](https://aka.ms/iot-security-github-trigger-events) , aby uruchomić niegroźną symulację ataku.
1. Sprawdź alerty usługi Defender for IoT w odpowiedzi na atak symulowany w poprzednim kroku. Rozpocznij weryfikację po pięciu minutach od uruchomienia skryptu.
1. Poznaj [alerty](concept-security-alerts.md), [zalecenia](concept-recommendations.md)i [głębokie szczegółowe przy użyciu log Analytics](how-to-security-data-access.md) przy użyciu IoT Hub.

## <a name="next-steps"></a>Następne kroki

- Skonfiguruj [rozwiązanie](quickstart-configure-your-solution.md)
- [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)
- Konfigurowanie [alertów niestandardowych](quickstart-create-custom-alerts.md)
- [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
