---
title: Limity i przydziały Plug and Play IoT | Microsoft Docs
description: Zapoznaj się z limitami, przydziałami i ograniczeniami, które są stosowane podczas korzystania z Plug and Play IoT.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577989"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>Limity Plug and Play IoT, przydziały i ograniczenia

W tym artykule wyjaśniono limity, przydziały i ograniczenia dotyczące Plug and Play IoT. Istnieją także [przydziały IoT Hub i ograniczanie przepustowości](../iot-hub/iot-hub-devguide-quotas-throttling.md) .

## <a name="iot-hub"></a>Usługa IoT Hub

Do centrum IoT są stosowane następujące limity i przydziały:

| Limity, ograniczenia i ograniczenia | Wartość | Uwagi |
|-----|-----|-----|
| Liczba interfejsów, które mogą być zarejestrowane na Hub | 1500 ||
| Maksymalny rozmiar nazwy składnika | 1-64 znaków | Dozwolone znaki: a-z, A-Z, 0-9 (nie jako pierwszy znak) i podkreślenia (nie jako pierwszy lub ostatni znak). |
| Maksymalny rozmiar nazwy właściwości | 1-64 znaków | Dozwolone znaki: a-z, A-Z, 0-9 (nie jako pierwszy znak) i podkreślenia (nie jako pierwszy lub ostatni znak). |
| Maksymalny rozmiar wartości właściwości | Analogicznie jak [Właściwość](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) Digital bliźniaczych reprezentacji Definition Language | 5 poziomów i nie może być tablicą ani złożonym schematem zawierającym tablicę |
| Maksymalny rozmiar nazwy polecenia | 1-64 znaków | Dozwolone znaki: a-z, A-Z, 0-9 (nie jako pierwszy znak) i podkreślenia (nie jako pierwszy lub ostatni znak).|
| Rozmiar przędzy urządzenia | Analogicznie jak [limity IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Biblioteka analizatora

Biblioteka analizatora jest zgodna z limitami stosowanymi w [języku definicji Digital bliźniaczych reprezentacji](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="next-steps"></a>Następne kroki

Sugerowanym następnym krokiem jest zapoznanie się z [architekturą Plug and Play IoT](concepts-architecture.md).
