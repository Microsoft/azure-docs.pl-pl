---
title: Agenci zabezpieczeń
description: Zacznij od ustalenia, konfigurowania, wdrażania i używania usługi Azure Defender dla agentów usługi zabezpieczeń IoT na urządzeniach IoT.
ms.topic: conceptual
ms.date: 1/24/2021
ms.openlocfilehash: a1d74de9824b9e825f87754da21070d4e7f1ee33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783493"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Rozpoczynanie pracy z usługą Azure Defender dla urządzeń IoT Micro Agents

Usługa Defender Security Agents oferuje ulepszone funkcje zabezpieczeń, takie jak monitorowanie najlepszych rozwiązań w zakresie konfiguracji systemu operacyjnego. Przejmij kontrolę nad ochroną pola urządzenia przed zagrożeniami i stan zabezpieczeń w ramach jednej usługi.

Obrony przed agentami zabezpieczeń IoT obsługują nieprzetworzoną kolekcję zdarzeń z systemu operacyjnego urządzenia, agregacji zdarzeń w celu zmniejszenia kosztów i konfiguracji za pośrednictwem sznurka modułowego urządzenia. Komunikaty zabezpieczeń są wysyłane przez IoT Hub do usługi Defender dla usług IoT Analytics.

Użyj następującego przepływu pracy do wdrożenia i przetestowania usługi Defender dla agentów zabezpieczeń IoT:

1. [Włącz usługę Defender for IoT na IoT Hub](quickstart-onboard-iot-hub.md).

1. Jeśli IoT Hub nie ma zarejestrowanych urządzeń, [Zarejestruj nowe urządzenie](../iot-accelerators/iot-accelerators-device-simulation-overview.md).

1. [Utwórz sznurek modułu DefenderIotMicroAgent](quickstart-create-micro-agent-module-twin.md) dla urządzeń.

1. Aby zainstalować agenta na symulowanym urządzeniu platformy Azure zamiast instalować go na rzeczywistym urządzeniu, uruchom [nową maszynę wirtualną platformy Azure](../virtual-machines/linux/quick-create-portal.md) w dostępnej strefie.

1. [Wdróż usługę Defender for IoT Security Agent](how-to-deploy-linux-cs.md) na urządzeniu IoT lub nową maszynę wirtualną.

1. Postępuj zgodnie z instrukcjami [trigger_events](https://aka.ms/iot-security-github-trigger-events) , aby uruchomić wydarzenie bazowe systemu operacyjnego.

1. Sprawdź zalecenia usługi Defender for IoT w odpowiedzi na niepowodzenie sprawdzania symulowanej linii bazowej systemu operacyjnego w poprzednim kroku. Rozpocznij weryfikację po 30 minutach od uruchomienia skryptu.

## <a name="next-steps"></a>Następne kroki

- Skonfiguruj [rozwiązanie](quickstart-configure-your-solution.md)
- [Tworzenie Defender-IoT-Micro-Agents](quickstart-create-security-twin.md)
- Konfigurowanie [alertów niestandardowych](quickstart-create-custom-alerts.md)
- [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)