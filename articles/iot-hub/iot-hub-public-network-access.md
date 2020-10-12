---
title: Zarządzanie dostępem do sieci publicznej dla usługi Azure IoT Hub
description: Dokumentacja dotycząca sposobu wyłączania i włączania dostępu do sieci publicznej dla usługi IoT Hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937564"
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

Aby włączyć dostęp do sieci publicznej, wybierz opcję **włączone**, a następnie **Zapisz**.

## <a name="ip-filter"></a>Filtr adresów IP 

Jeśli dostęp do sieci publicznej jest wyłączony, wszystkie reguły [filtrowania adresów IP](iot-hub-ip-filtering.md) są ignorowane. Wynika to z faktu, że wszystkie adresy IP z publicznej sieci Internet są zablokowane. Aby użyć filtru IP, użyj opcji **wybrane zakresy adresów IP** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Poprawka błędu z wbudowanym punktem końcowym zgodnym z centrum zdarzeń

Wystąpił błąd dotyczący IoT Hub, w którym [wbudowany punkt końcowy zgodny z centrum zdarzeń](iot-hub-devguide-messages-read-builtin.md) nadal jest dostępny za pośrednictwem publicznej sieci Internet, gdy publiczny dostęp do sieci IoT Hub jest wyłączony. Aby dowiedzieć się więcej i skontaktować się z nami na temat tego błędu, zobacz [wyłączanie dostępu do sieci publicznej dla IoT Hub uniemożliwia dostęp do wbudowanego punktu końcowego centrum zdarzeń](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).