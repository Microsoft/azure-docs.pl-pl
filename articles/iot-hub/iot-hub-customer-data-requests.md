---
title: Żądania danych klienta dotyczące urządzeń IoT Hub platformy Azure
description: Większość urządzeń zarządzanych w usłudze Azure IoT Hub nie jest osobista, ale niektóre z nich. W tym artykule omówiono administratorów, którzy mogą eksportować lub usuwać dane osobowe z urządzenia.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 1fdff6e1c29b8484aaea3bb647e2f1738d1889c6
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142232"
---
# <a name="customer-data-request-features-for-azure-iot-hub-devices"></a>Funkcje żądania danych klienta dla urządzeń IoT Hub platformy Azure

IoT Hub platformy Azure to usługa w chmurze oparta na interfejsie API REST przeznaczona dla klientów korporacyjnych, która zapewnia bezpieczną, dwukierunkową komunikację między milionami urządzeń a usługą platformy Azure podzielonej na partycje.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Do poszczególnych urządzeń przypisywany jest identyfikator urządzenia (identyfikator urządzenia) przez administratora dzierżawy. Dane urządzenia są oparte na przypisanym IDENTYFIKATORze urządzenia. Firma Microsoft nie przechowuje żadnych informacji i nie ma dostępu do danych, które mogłyby umożliwić korelację identyfikatora urządzenia z użytkownikiem.

Wiele urządzeń zarządzanych w usłudze Azure IoT Hub nie są urządzeniami osobistymi, na przykład z termostatem lub robotem fabryki biurowej. Klienci mogą jednak rozważyć, że niektóre urządzenia mają być identyfikowalne i według ich uznania mogą zachować własne metody śledzenia zasobów lub spisu, które wiążą urządzenia z osobami. Usługa Azure IoT Hub zarządza i przechowuje wszystkie dane skojarzone z urządzeniami, tak jakby były danymi osobistymi.

Administratorzy dzierżawy mogą używać Azure Portal lub interfejsów API REST usługi do realizacji żądań informacji przez eksportowanie lub usuwanie danych skojarzonych z IDENTYFIKATORem urządzenia.

W przypadku korzystania z funkcji routingu usługi Azure IoT Hub do przesyłania komunikatów na urządzeniach do innych usług, żądania danych muszą być wykonywane przez administratora dzierżawy dla każdego punktu końcowego routingu, aby wykonać pełne żądanie dla danego urządzenia. Aby uzyskać więcej informacji, zobacz dokumentację referencyjną dla każdego punktu końcowego. Aby uzyskać więcej informacji o obsługiwanych punktach końcowych, zobacz [punkty końcowe IoT Hub referencyjnych](iot-hub-devguide-endpoints.md).

Jeśli używasz funkcji integracji Azure Event Grid w usłudze Azure IoT Hub, żądania danych muszą być wykonywane przez administratora dzierżawy dla każdego subskrybenta tych zdarzeń. Aby uzyskać więcej informacji, zobacz temat [reagowanie na zdarzenia IoT Hub przy użyciu Event Grid](iot-hub-event-grid.md).

W przypadku tworzenia dzienników diagnostycznych za pomocą funkcji integracji Azure Monitor w usłudze Azure IoT Hub, żądania danych muszą być wykonywane przez administratora dzierżawy względem przechowywanych dzienników. Aby uzyskać więcej informacji, zobacz [monitorowanie kondycji IoT Hub platformy Azure](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administratorzy dzierżawy mogą użyć bloku urządzenia IoT rozszerzenia IoT Hub platformy Azure w Azure Portal, aby usunąć urządzenie, co spowoduje usunięcie danych skojarzonych z tym urządzeniem.

Możliwe jest również wykonywanie operacji usuwania dla urządzeń korzystających z interfejsów API REST. Aby uzyskać więcej informacji, zobacz temat [Usługa-Usuwanie urządzenia](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Administratorzy dzierżawy mogą używać kopiowania i wklejania w okienku urządzenia IoT rozszerzenia IoT Hub platformy Azure w Azure Portal do eksportowania danych skojarzonych z urządzeniem.

Możliwe jest również wykonywanie operacji eksportowania dla urządzeń przy użyciu interfejsów API REST. Aby uzyskać więcej informacji, zobacz [Usługa-Get Device](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice).

> [!NOTE]
> Gdy korzystasz z usług firmy Microsoft dla przedsiębiorstw, firma Microsoft generuje pewne informacje, znane jako dzienniki generowane przez system. Niektóre dzienniki generowane przez system IoT Hub platformy Azure nie są dostępne ani eksportowane przez administratorów dzierżawy. Te dzienniki składają się na faktyczne działania wykonywane w ramach usługi i danych diagnostycznych związanych z poszczególnymi urządzeniami.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Pełna Dokumentacja interfejsów API usługi Azure IoT Hub Service znajduje się w [interfejsie API usługi IoT Hub Service](/rest/api/iothub/service/configuration).