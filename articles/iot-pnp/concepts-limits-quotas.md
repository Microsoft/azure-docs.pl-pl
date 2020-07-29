---
title: Limity i przydziały IoT Plug and Play w wersji zapoznawczej | Microsoft Docs
description: Zapoznaj się z limitami, przydziałami i ograniczeniami dotyczącymi korzystania z programu IoT Plug and Play w wersji zapoznawczej.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337402"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limity, przydziały i ograniczenia dotyczące Plug and Play IoT

W tym artykule wyjaśniono limity, przydziały i ograniczenia dotyczące Plug and Play IoT, które są stosowane w publicznej wersji zapoznawczej. Istnieją także [przydziały IoT Hub i ograniczanie przepustowości](../iot-hub/iot-hub-devguide-quotas-throttling.md) .

## <a name="iot-hub"></a>Usługa IoT Hub

W publicznej wersji zapoznawczej do centrum IoT są stosowane następujące limity i przydziały:

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
