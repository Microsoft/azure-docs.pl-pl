---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95564163"
---
Aby uzyskać stan kondycji punktów końcowych, można użyć interfejsu API REST [Uzyskaj kondycję punktu końcowego](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) . Zalecamy używanie [metryk routingu IoT Hub](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) związanych z opóźnieniem komunikatów routingu w celu identyfikowania i debugowania błędów, gdy kondycja punktu końcowego jest martwa lub zła, ponieważ oczekuje się, że opóźnienie będzie większe, gdy punkt końcowy jest w jednym z tych stanów. Aby dowiedzieć się więcej o korzystaniu z metryk IoT Hub, zobacz [IoT Hub monitorowania](../articles/iot-hub/monitor-iot-hub.md).

|Stan kondycji|Opis|
|---|---|
|dobrej kondycji|Punkt końcowy akceptuje komunikaty zgodnie z oczekiwaniami.|
|złej kondycji|Punkt końcowy nie akceptuje komunikatów, a IoT Hub ponawia próbę wysłania komunikatów do tego punktu końcowego.|
|unknown|IoT Hub nie próbował dostarczyć komunikatów do tego punktu końcowego.|
|pogorszenie|Punkt końcowy akceptuje komunikaty wolniej niż oczekiwano lub trwa odzyskiwanie z stanu złej kondycji.|
|martwy|IoT Hub nie dostarcza już komunikatów do tego punktu końcowego. Ponowna próba wysłania komunikatów do tego punktu końcowego nie powiodła się.|