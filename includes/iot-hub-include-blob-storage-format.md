---
title: dołączanie pliku
description: dołączanie pliku
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "73612133"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Dane można zapisywać do magazynu obiektów BLOB w formacie [Apache Avro](https://avro.apache.org/) , który jest wartością domyślną lub JSON (wersja zapoznawcza). 
>    
> Możliwość kodowania formatu JSON jest w wersji zapoznawczej we wszystkich regionach, w których IoT Hub jest dostępna, z wyjątkiem Wschodnie stany USA, zachodnie stany USA i Europa Zachodnia. Format kodowania można ustawić tylko w czasie, gdy jest skonfigurowany punkt końcowy magazynu obiektów BLOB. Nie można zmienić formatu dla punktu końcowego, który został już skonfigurowany. W przypadku korzystania z kodowania JSON należy ustawić wartość ContentType na JSON i contentEncoding na UTF-8 we właściwościach systemu komunikatów. 
>
> Aby uzyskać bardziej szczegółowe informacje na temat korzystania z punktu końcowego magazynu obiektów blob, zobacz [wskazówki dotyczące routingu do magazynu](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>