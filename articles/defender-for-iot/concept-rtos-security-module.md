---
title: Ogólne objaśnienie podstaw modułu zabezpieczeń dla usługi Azure RTO
description: Poznaj podstawowe informacje o module zabezpieczeń dla koncepcji i przepływu pracy usługi Azure RTO.
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
ms.openlocfilehash: 9f816b35fd79d3f158ea12fd2c7e1d811823dc8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939506"
---
# <a name="security-module-for-azure-rtos-preview"></a>Moduł zabezpieczeń dla usługi Azure RTO (wersja zapoznawcza)

Skorzystaj z tego artykułu, aby lepiej zrozumieć moduł zabezpieczeń usługi Azure RTO, w tym funkcje i korzyści, a także linki do odpowiednich konfiguracji i zasobów referencyjnych. 

## <a name="azure-rtos-iot-security-module"></a>Moduł zabezpieczeń usługi Azure RTO IoT

Moduł zabezpieczeń usługi Azure RTO oferuje kompleksowe rozwiązanie zabezpieczeń dla urządzeń RTO platformy Azure w ramach oferty NetX Duo. W ramach oferty NetX Duo usługa Azure RTO jest dostarczana z wbudowanym modułem zabezpieczeń Azure IoT i zapewnia pokrycie typowych zagrożeń na urządzeniach z systemem operacyjnym w czasie rzeczywistym po aktywowaniu. 

Moduł zabezpieczeń usługi Azure RTO działa w tle i zapewnia bezproblemowe środowisko użytkownika podczas wysyłania komunikatów zabezpieczeń przy użyciu unikatowych połączeń każdego klienta z ich IoT Hub. Moduł zabezpieczeń dla usługi Azure RTO jest domyślnie włączony.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX Duo to zaawansowany stos oparty na protokole TCP/IP klasy przemysłowej zaprojektowany specjalnie z myślą o głęboko osadzonych aplikacjach w czasie rzeczywistym i IoT. Azure RTO NetX Duo to dwuwarstwowy stos sieciowy IPv4 i IPv6, który oferuje bogaty zestaw protokołów, w tym zabezpieczenia i chmurę. Dowiedz się więcej o rozwiązaniach [Azure RTO NetX Duo](https://aka.ms/netxduo) .

Moduł oferuje następujące funkcje:

- **Wykrywaj złośliwe działania sieci**
- **Linie bazowe zachowania urządzenia na podstawie alertów niestandardowych**
- **Poprawa higieny zabezpieczeń urządzeń**

## <a name="security-module-for-azure-rtos-architecture"></a>Moduł zabezpieczeń dla architektury Azure RTO

Moduł zabezpieczeń usługi Azure RTO jest inicjowany przez platformę oprogramowania pośredniczącego Azure IoT i używa klientów IoT Hub do wysyłania telemetrii zabezpieczeń do centrum.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Diagram stanu modułu usługi Azure IoT Security i przepływ informacji":::

Moduł zabezpieczeń usługi Azure RTO monitoruje następujące działania i informacje o urządzeniu przy użyciu trzech modułów zbierających:
- Aktywność sieciowa urządzenia **TCP**, **UDP**i **ICM**
- Informacje o systemie jako wersje **ThreadX** i **NetX Duo**
- Zdarzenia pulsu

Każdy moduł zbierający jest połączony z grupą priorytetów, a każda grupa priorytetów ma własny interwał z możliwymi wartościami **niski**, **Średni**i **wysoki**. Interwały mają wpływ na przedział czasu, w którym dane są zbierane i wysyłane.

Każdy interwał jest konfigurowalny, a łączniki IoT można włączać i wyłączać, aby dodatkowo [dostosować rozwiązanie](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Obsługiwane alerty i zalecenia dotyczące zabezpieczeń

Moduł zabezpieczeń usługi Azure RTO obsługuje określone alerty zabezpieczeń i zalecenia. Po zakończeniu konfiguracji początkowej należy [przejrzeć i dostosować odpowiednie wartości alertu i rekomendacji](concept-rtos-security-alerts-recommendations.md) dla usługi.

## <a name="ready-to-begin"></a>Chcesz zacząć?

Moduł zabezpieczeń dla usługi Azure RTO jest dostępny bezpłatnie do pobrania dla urządzeń IoT. Usługa Defender for IoT w chmurze jest dostępna w ramach 30-dniowej wersji próbnej dla każdej subskrypcji platformy Azure. [Pobierz moduł zabezpieczeń teraz](https://github.com/azure-rtos/azure-iot-preview/releases) i zacznijmy. 

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do modułu zabezpieczeń dla usługi Azure RTO — [wymagania wstępne i konfiguracja](quickstart-azure-rtos-security-module.md).
- Dowiedz się więcej na temat modułu zabezpieczeń dla [alertów zabezpieczeń usługi Azure RTO i pomocy technicznej zalecenia](concept-rtos-security-alerts-recommendations.md). 
- Użyj modułu zabezpieczeń dla [interfejsu API](azure-rtos-security-module-api.md)usługi Azure RTO Reference.

