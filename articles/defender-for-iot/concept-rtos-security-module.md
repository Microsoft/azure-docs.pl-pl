---
title: Koncepcyjne wyjaśnienie podstaw usługi Defender-IoT-micro-agent dla Azure RTOS
description: Poznaj podstawowe informacje na temat usługi Defender-IoT-micro-agent, aby poznać Azure RTOS i przepływ pracy.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 04b86d401bcb9fc919c36b28cf4f80ea3bfd7030
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750486"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-micro-agent dla Azure RTOS (wersja zapoznawcza)

Skorzystaj z tego artykułu, aby lepiej zrozumieć usługę Defender-IoT-micro-agent dla usługi Azure RTOS, w tym funkcje i korzyści, a także linki do odpowiedniej konfiguracji i zasobów referencyjnych. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-micro-agent

Mikrousługa Defender-IoT-Azure RTOS zapewnia kompleksowe rozwiązanie zabezpieczeń dla urządzeń Azure RTOS w ramach oferty NetX Duo. W ramach oferty NetX Duo usługa Azure RTOS jest dostarczana z wbudowaną usługą Azure IoT Defender-IoT-micro-agent i zapewnia pokrycie typowych zagrożeń na urządzeniach z systemem operacyjnym w czasie rzeczywistym po aktywowaniu.

Mikrousługa Defender-IoT-agent dla usługi Azure RTOS działa w tle i zapewnia bezproblemowe środowisko użytkownika podczas wysyłania komunikatów zabezpieczeń przy użyciu unikatowych połączeń poszczególnych klientów z IoT Hub. Mikrousługa Defender-IoT-Azure RTOS jest domyślnie włączona.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo to zaawansowany stos sieciowy klasy przemysłowej TCP/IP zaprojektowany specjalnie z myślą o głęboko osadzonych aplikacjach czasu rzeczywistego i IoT. Azure RTOS NetX Duo to podwójny stos sieciowy IPv4 i IPv6, który udostępnia bogaty zestaw protokołów, w tym zabezpieczeń i chmury. Dowiedz się więcej [o Azure RTOS NetX Duo.](/azure/rtos/netx-duo/)

Moduł oferuje następujące funkcje:

- **Wykrywanie złośliwych działań sieciowych**
- **Linie bazowe zachowania urządzenia oparte na alertach niestandardowych**
- **Ulepszanie higieni zabezpieczeń urządzeń**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-micro-agent na Azure RTOS architektury

Mikrousługa Defender-IoT-micro-agent dla usługi Azure RTOS jest inicjowana przez platformę oprogramowania pośredniczącego Azure IoT i używa klientów usługi IoT Hub do wysyłania telemetrii zabezpieczeń do centrum.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Diagram stanu mikrousługi i przepływ informacji usługi Azure IoT Defender-IoT-micro-agent":::

Mikrousługa Defender-IoT-Azure RTOS monitoruje następujące działania i informacje dotyczące urządzeń przy użyciu trzech modułów zbierających:
- Aktywność sieci urządzenia **TCP,** **UDP** i **ICM**
- Informacje o systemie jako **wersje Threadx** i **NetX Duo**
- Zdarzenia pulsu

Każdy moduł zbierający jest połączony z grupą priorytetów, a każda grupa priorytetów ma własny interwał z możliwymi wartościami **Niski,** **Średni** i **Wysoki.** Interwały mają wpływ na przedział czasu, w którym dane są zbierane i wysyłane.

Każdy interwał czasu można konfigurować, a łączniki IoT można włączać i wyłączać w celu dalszego [dostosowywania rozwiązania.](how-to-azure-rtos-security-module.md) 

## <a name="supported-security-alerts-and-recommendations"></a>Obsługiwane alerty zabezpieczeń i zalecenia

Mikrousługa Defender-IoT-Azure RTOS obsługuje określone alerty zabezpieczeń i zalecenia. Pamiętaj, aby [przejrzeć i dostosować odpowiednie wartości alertów i zaleceń](concept-rtos-security-alerts-recommendations.md) dla usługi po zakończeniu konfiguracji początkowej.

## <a name="ready-to-begin"></a>Chcesz rozpocząć?

Mikrousługa Defender-IoT-Azure RTOS jest do pobrania bezpłatnie dla urządzeń IoT. Usługa Defender dla IoT w chmurze jest dostępna w ramach 30-dniowej wersji próbnej na subskrypcję platformy Azure. [Pobierz teraz mikrousługę Defender-IoT-micro-agent](https://github.com/azure-rtos/azure-iot-preview/releases) i zacznijmy. 

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do usługi Defender-IoT-micro-agent w celu Azure RTOS [wymagań wstępnych i konfiguracji.](quickstart-azure-rtos-security-module.md)
- Dowiedz się więcej na temat usługi Defender-IoT-micro-agent na Azure RTOS [alertów zabezpieczeń i obsługi rekomendacji.](concept-rtos-security-alerts-recommendations.md) 
- Użyj usługi Defender-IoT-micro-agent na Azure RTOS [interfejsu API .](azure-rtos-security-module-api.md)