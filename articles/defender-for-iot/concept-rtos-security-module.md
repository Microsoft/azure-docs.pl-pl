---
title: Ogólne wyjaśnienie podstaw usługi Defender-IoT-Micro-Agent for Azure RTO
description: Poznaj podstawowe informacje o usłudze Defender-IoT-Micro-Agent dla koncepcji i przepływu pracy usługi Azure RTO.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 83557930aeeccbb557382583e4d6666a000ce52c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779277"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-Micro-Agent for Azure RTO (wersja zapoznawcza)

Skorzystaj z tego artykułu, aby lepiej zrozumieć usługę Defender-IoT-Micro-Agent dla usługi Azure RTO, w tym funkcje i korzyści, a także linki do odpowiednich konfiguracji i zasobów referencyjnych. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTO IoT Defender-IoT-Micro-Agent

Usługa Defender-IoT-Micro-Agent for Azure RTO oferuje kompleksowe rozwiązanie zabezpieczeń dla urządzeń RTO platformy Azure w ramach oferty NetX Duo. W ramach oferty NetX Duo usługa Azure RTO jest dostarczana z wbudowaną usługą Azure IoT Defender-IoT-Micro-Agent i zapewnia ochronę typowych zagrożeń na urządzeniach z systemem operacyjnym w czasie rzeczywistym po aktywowaniu. 

Usługa Defender-IoT-Micro-Agent RTO jest uruchamiana w tle i zapewnia bezproblemowe środowisko użytkownika podczas wysyłania komunikatów zabezpieczeń przy użyciu unikatowych połączeń każdego klienta z ich IoT Hub. Program Defender-IoT-Micro-Agent dla usługi Azure RTO jest domyślnie włączony.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX Duo to zaawansowany stos oparty na protokole TCP/IP klasy przemysłowej zaprojektowany specjalnie z myślą o głęboko osadzonych aplikacjach w czasie rzeczywistym i IoT. Azure RTO NetX Duo to dwuwarstwowy stos sieciowy IPv4 i IPv6, który oferuje bogaty zestaw protokołów, w tym zabezpieczenia i chmurę. Dowiedz się więcej o rozwiązaniach [Azure RTO NetX Duo](/azure/rtos/netx-duo/) .

Moduł oferuje następujące funkcje:

- **Wykrywaj złośliwe działania sieci**
- **Linie bazowe zachowania urządzenia na podstawie alertów niestandardowych**
- **Poprawa higieny zabezpieczeń urządzeń**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-Micro-Agent for Azure RTO Architecture

Program Defender-IoT-Micro-Agent for Azure RTO jest inicjowany przez platformę oprogramowania pośredniczącego usługi Azure IoT i używa klientów IoT Hub do wysyłania telemetrii zabezpieczeń do centrum.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender-IoT-Micro-Diagram stanu i przepływ informacji":::

Usługa Defender-IoT-Micro-Agent dla platformy Azure RTO monitoruje następujące działania i informacje o urządzeniu przy użyciu trzech modułów zbierających:
- Aktywność sieciowa urządzenia **TCP**, **UDP** i **ICM**
- Informacje o systemie jako wersje **ThreadX** i **NetX Duo**
- Zdarzenia pulsu

Każdy moduł zbierający jest połączony z grupą priorytetów, a każda grupa priorytetów ma własny interwał z możliwymi wartościami **niski**, **Średni** i **wysoki**. Interwały mają wpływ na przedział czasu, w którym dane są zbierane i wysyłane.

Każdy interwał jest konfigurowalny, a łączniki IoT można włączać i wyłączać, aby dodatkowo [dostosować rozwiązanie](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Obsługiwane alerty i zalecenia dotyczące zabezpieczeń

Defender-IoT-Micro-Agent for Azure RTO obsługuje określone alerty zabezpieczeń i zalecenia. Po zakończeniu konfiguracji początkowej należy [przejrzeć i dostosować odpowiednie wartości alertu i rekomendacji](concept-rtos-security-alerts-recommendations.md) dla usługi.

## <a name="ready-to-begin"></a>Chcesz zacząć?

Usługa Defender-IoT-Micro-Agent for Azure RTO jest oferowana bezpłatnie do pobrania dla urządzeń IoT. Usługa Defender for IoT w chmurze jest dostępna w ramach 30-dniowej wersji próbnej dla każdej subskrypcji platformy Azure. [Pobierz usługę Defender-IoT-Micro-Agent teraz](https://github.com/azure-rtos/azure-iot-preview/releases) i zacznijmy. 

## <a name="next-steps"></a>Następne kroki

- Rozpocznij pracę z usługą Defender-IoT-Micro-Agent for Azure RTO — [wymagania wstępne i Instalatora](quickstart-azure-rtos-security-module.md).
- Dowiedz się więcej na temat usługi Defender-IoT-Micro-Agent na potrzeby [alertów zabezpieczeń RTO i rekomendacji](concept-rtos-security-alerts-recommendations.md). 
- Użyj usługi Defender-IoT-Micro-Agent dla [interfejsu API RTO Reference](azure-rtos-security-module-api.md).