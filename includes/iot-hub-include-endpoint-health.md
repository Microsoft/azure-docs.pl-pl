---
title: dołączanie pliku
description: dołączanie pliku
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 36c05badb3b2292a29b8227c7f03b841474c97ad
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548201"
---
Aby uzyskać stan kondycji punktów końcowych, można użyć interfejsu API REST [Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) . Zalecamy używanie [metryk routingu IoT Hub](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) związanych z opóźnieniem komunikatów routingu w celu identyfikowania i debugowania błędów, gdy kondycja punktu końcowego jest martwa lub zła, ponieważ oczekuje się, że opóźnienie będzie większe, gdy punkt końcowy jest w jednym z tych stanów. Aby dowiedzieć się więcej o korzystaniu z metryk IoT Hub, zobacz [IoT Hub monitorowania](../articles/iot-hub/monitor-iot-hub.md).

|Stan kondycji|Opis|
|---|---|
|dobrej kondycji|Punkt końcowy akceptuje komunikaty zgodnie z oczekiwaniami.|
|złej kondycji|Punkt końcowy nie akceptuje komunikatów, a IoT Hub ponawia próbę wysłania komunikatów do tego punktu końcowego.|
|unknown|IoT Hub nie próbował dostarczyć komunikatów do tego punktu końcowego.|
|pogorszenie|Punkt końcowy akceptuje komunikaty wolniej niż oczekiwano lub trwa odzyskiwanie z stanu złej kondycji.|
|martwy|IoT Hub nie dostarcza już komunikatów do tego punktu końcowego. Ponowna próba wysłania komunikatów do tego punktu końcowego nie powiodła się.|
