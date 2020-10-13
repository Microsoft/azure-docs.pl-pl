---
title: plik dołączany
description: plik dołączany
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792097"
---
Aby uzyskać stan kondycji punktów końcowych, można użyć interfejsu API REST [Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) . Zalecamy używanie [metryk IoT Hub](../articles/iot-hub/iot-hub-metrics.md) związanych z opóźnieniem komunikatów routingu w celu identyfikowania i debugowania błędów, gdy kondycja punktu końcowego jest martwa lub zła, ponieważ oczekuje się, że opóźnienie ma być wyższe, gdy punkt końcowy jest w jednym z tych stanów.


|Stan kondycji|Opis|
|---|---|
|dobrej kondycji|Punkt końcowy akceptuje komunikaty zgodnie z oczekiwaniami.|
|złej kondycji|Punkt końcowy nie akceptuje komunikatów, a IoT Hub ponawia próbę wysłania komunikatów do tego punktu końcowego.|
|unknown|IoT Hub nie próbował dostarczyć komunikatów do tego punktu końcowego.|
|pogorszenie|Punkt końcowy akceptuje komunikaty wolniej niż oczekiwano lub trwa odzyskiwanie z stanu złej kondycji.|
|martwy|IoT Hub nie dostarcza już komunikatów do tego punktu końcowego. Ponowna próba wysłania komunikatów do tego punktu końcowego nie powiodła się.|
