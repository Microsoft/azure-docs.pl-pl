---
title: Architektura wieloosiowa OPC — platforma Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie architektury wieloosiowej OPC. Opisano w nim sposób odnajdywania, aktywacji, przeglądania i monitorowania serwera.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2504ba6262ba281d4049d89b03d2b3bc60061669
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281800"
---
# <a name="opc-twin-architecture"></a>Architektura OPC bliźniaczy

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Na poniższych diagramach przedstawiono architekturę OPC z przędzą.

## <a name="discover-and-activate"></a>Odnajdywanie i aktywowanie

1. Operator włącza funkcję skanowania sieci w module lub wykonuje jednorazowe odnajdywanie przy użyciu adresu URL odnajdowania. Wykryte punkty końcowe i informacje o aplikacji są wysyłane przez telemetrię do agenta dołączania w celu przetworzenia.  Agent dołączania urządzenia OPC UA przetwarza zdarzenia odnajdowania serwera OPC UA wysyłane przez IoT Edgee w ramach modułu w trybie odnajdywania lub skanowania. Zdarzenia odnajdywania powodują rejestrowanie i aktualizowanie aplikacji w rejestrze urządzeń OPC UA.

   ![Jak działa OPCa](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operator sprawdza certyfikat odnalezionego punktu końcowego i aktywuje zarejestrowany sznury punkt końcowego w celu uzyskania dostępu. 

   ![Jak działa OPCa](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Przeglądaj i monitoruj

1. Po aktywowaniu operator może użyć interfejsu API REST usługi Bliźniaczyej, aby przejrzeć lub sprawdzić model informacji o serwerze, zmienne obiektów odczytu/zapisu i metody wywołań.  Użytkownik używa uproszczonego interfejsu API OPC UA wyrażonego w pełni w protokołach HTTP i JSON.

   ![Jak działa OPCa](media/overview-opc-twin-architecture/opc-twin3.png)

1. Interfejs REST usługi bliźniaczyej może również służyć do tworzenia monitorowanych elementów i subskrypcji w wydawcy OPC. Wydawca OPC umożliwia wysyłanie danych telemetrycznych z systemów OPC UA do IoT Hub. Aby uzyskać więcej informacji na temat wydawcy OPC, zobacz [co to jest OPC Publisher](overview-opc-publisher.md).

   ![Jak działa OPCa](media/overview-opc-twin-architecture/opc-twin4.png)
