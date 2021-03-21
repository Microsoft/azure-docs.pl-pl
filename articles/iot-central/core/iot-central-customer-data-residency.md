---
title: Dane klienta na miejscu zamieszkania na platformie Azure IoT Central | Microsoft Docs
description: W tym artykule opisano sposób zamieszkania danych klienta w aplikacjach IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280705"
---
# <a name="azure-iot-central-customer-data-residency"></a>Dane klienta platformy Azure IoT Central w miejscu zamieszkania

IoT Central nie przechowuje danych klienta poza określoną geograficzną klienta, z wyjątkiem następujących scenariuszy:

- Gdy nowy użytkownik zostanie dodany do istniejącej aplikacji IoT Central, identyfikator poczty e-mail użytkownika może być przechowywany poza obszarem geograficznym do momentu, gdy Zaproszony użytkownik uzyskuje dostęp do aplikacji po raz pierwszy.

- IoT Central kafelków mapy pulpitu nawigacyjnego używają [Azure Maps](../../azure-maps/about-azure-maps.md). Po dodaniu kafelka mapy do istniejącej aplikacji IoT Central dane lokalizacji mogą być przetwarzane lub przechowywane zgodnie z zasadami geolokalizacji usługi Azure Maps.
