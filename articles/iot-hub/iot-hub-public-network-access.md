---
title: Zarządzanie dostępem do sieci publicznej dla usługi Azure IoT Hub
description: Dokumentacja dotycząca sposobu wyłączania i włączania dostępu do sieci publicznej dla usługi IoT Hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: b225ad7e12a95b4dcbb8656f54f8a9e9562b2ee8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604699"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Zarządzanie dostępem do sieci publicznej dla Centrum IoT Hub

Aby ograniczyć dostęp tylko do [prywatnego punktu końcowego dla Centrum IoT w sieci wirtualnej](virtual-network-support.md), wyłącz dostęp do sieci publicznej. Aby to zrobić, użyj Azure Portal lub `publicNetworkAccess` interfejsu API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Wyłącz dostęp do sieci publicznej przy użyciu Azure Portal

1. Odwiedź witrynę [Azure Portal](https://portal.azure.com)
2. Przejdź do centrum IoT Hub.
3. Wybierz pozycję **Sieć** z menu po lewej stronie.
4. W obszarze "Zezwalaj na dostęp do sieci publicznej do" Wybierz pozycję **wyłączone**
5. Wybierz pozycję **Zapisz**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Obraz przedstawiający Azure Portal gdzie wyłączyć dostęp do sieci publicznej" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Aby włączyć dostęp do sieci publicznej, wybierz pozycję **wszystkie sieci**, a następnie pozycję **Zapisz**.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>Uzyskiwanie dostępu do IoT Hub po wyłączeniu dostępu do sieci publicznej

Po wyłączeniu dostępu do sieci publicznej IoT Hub jest dostępny tylko za pośrednictwem [prywatnego punktu końcowego wirtualnej platformy Azure](virtual-network-support.md).

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>IoT Hub punktu końcowego, adresu IP i portów po wyłączeniu dostępu do sieci publicznej

IoT Hub to wielodostępna platforma jako usługa (PaaS), więc różni klienci korzystają z tej samej puli zasobów obliczeniowych, sieciowych i magazynowych. Nazwy hostów IoT Hub są mapowane na publiczny punkt końcowy z publicznie rutowanym adresem IP przez Internet. Różni klienci korzystają z tego IoT Hub publicznego punktu końcowego, a urządzenia IoT w sieci rozległej i sieci lokalne mogą uzyskać do niego dostęp. 

Wyłączenie dostępu do sieci publicznej jest wymuszane w określonym zasobie Centrum IoT, zapewniając izolację. Aby zachować aktywną usługę dla innych zasobów klienta przy użyciu ścieżki publicznej, jego publiczny punkt końcowy pozostaje rozpoznawalny, adresy IP są wykrywalne i porty pozostają otwarte. Nie jest to powód dla obaw, ponieważ firma Microsoft integruje wiele warstw zabezpieczeń w celu zapewnienia pełnej izolacji między dzierżawcami. Aby dowiedzieć się więcej, zobacz [izolacja w chmurze publicznej platformy Azure](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>Filtr adresów IP 

Jeśli dostęp do sieci publicznej jest wyłączony, wszystkie reguły [filtrowania adresów IP](iot-hub-ip-filtering.md) są ignorowane. Wynika to z faktu, że wszystkie adresy IP z publicznej sieci Internet są zablokowane. Aby użyć filtru IP, użyj opcji **wybrane zakresy adresów IP** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Poprawka błędu z wbudowanym punktem końcowym zgodnym z centrum zdarzeń

Wystąpił błąd dotyczący IoT Hub, w którym [wbudowany punkt końcowy zgodny z centrum zdarzeń](iot-hub-devguide-messages-read-builtin.md) nadal jest dostępny za pośrednictwem publicznej sieci Internet, gdy publiczny dostęp do sieci IoT Hub jest wyłączony. Aby dowiedzieć się więcej i skontaktować się z nami na temat tego błędu, zobacz [wyłączanie dostępu do sieci publicznej dla IoT Hub uniemożliwia dostęp do wbudowanego punktu końcowego centrum zdarzeń](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).
