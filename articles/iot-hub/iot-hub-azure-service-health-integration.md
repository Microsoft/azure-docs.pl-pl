---
title: Sprawdź usługę Azure IoT Hub i kondycję zasobów | Microsoft Docs
description: Użyj Azure Service Health i Azure Resource Health do monitorowania IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548520"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Sprawdzanie kondycji usługi IoT Hub i zasobów

Platforma Azure IoT Hub integruje się z [usługą Azure Service Health](../service-health/overview.md) , aby zapewnić możliwość monitorowania kondycji usługi IoT Hub i poszczególnych centrów IoT. Możesz również skonfigurować alerty do powiadamiania o zmianie stanu usługi IoT Hub lub Centrum IoT. Usługa Azure Service Health jest kombinacją trzech mniejszych usług: Azure Resource Health, Azure Service Health i strony stanu platformy Azure. W sekcjach w tym artykule opisano poszczególne usługi i relacje, aby IoT Hub bardziej szczegółowo.

Usługa Azure Service Health ułatwia monitorowanie zdarzeń na poziomie usług, takich jak awaria i uaktualnienia, które mogą mieć wpływ na dostępność usługi IoT Hub i poszczególnych centrów IoT. IoT Hub integruje się również z usługą Azure Monitor w celu zapewnienia IoT Hub metryk platformy i IoT Hub dzienników zasobów, których można użyć do monitorowania błędów operacyjnych i warunków występujących w konkretnym Centrum IoT Hub. Aby dowiedzieć się więcej, zobacz [Monitor IoT Hub](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Sprawdzanie kondycji Centrum IoT Hub za pomocą Azure Resource Health

Azure Resource Health jest częścią usługi Azure Service Health, która śledzi prawidłowość poszczególnych zasobów. Stan kondycji Centrum IoT Hub można sprawdzić bezpośrednio w portalu.

Aby wyświetlić historię stanu i stanu Centrum IoT Hub przy użyciu portalu, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT hub w Azure Portal.

1. W okienku po lewej stronie w obszarze **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **Resource Health** .

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Strona kondycji zasobów dla Centrum IoT":::

Aby dowiedzieć się więcej na temat Azure Resource Health i sposobu interpretacji danych dotyczących kondycji, zobacz [Resource Health Omówienie](../service-health/resource-health-overview.md) w dokumentacji Azure Service Health.

Możesz również wybrać pozycję **Dodaj alert kondycji zasobu** , aby skonfigurować alerty wyzwalane po zmianie stanu kondycji Centrum IoT. Aby dowiedzieć się więcej, zobacz [Konfigurowanie alertów dotyczących zdarzeń usługi Service Health](../service-health/alerts-activity-log-service-notifications-portal.md) i powiązanych tematów w dokumentacji Azure Service Health.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Sprawdź kondycję centrów IoT w subskrypcji za pomocą Azure Service Health

Za pomocą Azure Service Health można sprawdzić stan kondycji wszystkich centrów IoT w ramach subskrypcji.

Aby sprawdzić kondycję centrów IoT, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do **Service Health**  >  **kondycji zasobów** .

3. Z listy rozwijanej wybierz subskrypcję, a następnie wybierz **IoT Hub** jako typ zasobu.

Aby dowiedzieć się więcej na temat Azure Service Health i sposobu interpretacji danych dotyczących kondycji, zobacz [Service Health Omówienie](../service-health/service-health-overview.md) w dokumentacji Azure Service Health.

Aby dowiedzieć się, jak skonfigurować alerty za pomocą Azure Service Health, zobacz [Konfigurowanie alertów dotyczących zdarzeń usługi Service Health](../service-health/alerts-activity-log-service-notifications-portal.md) i powiązanych tematów w dokumentacji Azure Service Health.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Sprawdzanie kondycji usługi IoT Hub według regionów na stronie stanu platformy Azure

Aby sprawdzić stan IoT Hub i innych usług według regionów na całym świecie, możesz użyć [strony stanu platformy Azure](https://status.azure.com/status). Aby uzyskać więcej informacji na stronie stanu platformy Azure, zobacz [Omówienie stanu platformy Azure](../service-health/azure-status-overview.md) w dokumentacji Azure Service Health.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Azure Service Health usługi](../service-health/overview.md) , aby uzyskać szczegółowe informacje na temat Azure Service Health, Azure Resource Health i strony stanu platformy Azure.
* Aby uzyskać opis monitorowania IoT Hub platformy Azure, zobacz artykuł [monitorowanie usługi azure IoT Hub](monitor-iot-hub.md) .
