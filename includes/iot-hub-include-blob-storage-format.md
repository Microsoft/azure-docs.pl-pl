---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89042879"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Dane można zapisywać do magazynu obiektów BLOB w formacie [Apache Avro](https://avro.apache.org/) , który jest wartością domyślną lub JSON (wersja zapoznawcza). 
>    
> Możliwość kodowania formatu JSON jest w wersji zapoznawczej we wszystkich regionach, w których IoT Hub jest dostępna, z wyjątkiem Wschodnie stany USA, zachodnie stany USA i Europa Zachodnia. Format kodowania można ustawić tylko w czasie, gdy jest skonfigurowany punkt końcowy magazynu obiektów BLOB. Nie można zmienić formatu dla punktu końcowego, który został już skonfigurowany. W przypadku korzystania z kodowania JSON należy ustawić wartość ContentType na JSON i contentEncoding na UTF-8 we właściwościach systemu komunikatów. 
>
> Aby uzyskać bardziej szczegółowe informacje na temat korzystania z punktu końcowego magazynu obiektów blob, zobacz [wskazówki dotyczące routingu do magazynu](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).
>