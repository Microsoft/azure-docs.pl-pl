---
title: Model dołączania usługi komunikacji równorzędnej platformy Azure
description: Dowiedz się więcej na temat dołączania usługi Azure peering
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84872078"
---
# <a name="onboarding-peering-service-model"></a>Model usługi komunikacji równorzędnej dołączania

Proces dołączania usługi komunikacji równorzędnej składa się z dwóch modeli wymienionych poniżej:

 - Nawiązywanie połączenia z usługą komunikacji równorzędnej

 - Dodawanie telemetrii połączenia usługi Komunikacja równorzędna

Plany akcji dla powyższych modeli wymienionych poniżej opisano poniżej:

| **Krok** | **Akcja**| **Co otrzymujemy**|
|-----------|---------|---------|---------|
|1|Klient do aprowizacji łączności od partnera łączności (bez interakcji z firmą Microsoft). |Usługodawca internetowy, który jest dobrze połączony z firmą Microsoft i spełnia wymagania techniczne dotyczące wykonywania i niezawodnej łączności z firmą Microsoft.  |
|2 (opcjonalnie)|Klient rejestruje lokalizacje w Azure Portal. Lokalizacja jest definiowana przez: Nazwa usługodawcy internetowego/IXP, fizyczna lokalizacja witryny klienta (poziom stanu), prefiks IP przyznany lokalizacji przez dostawcę usług lub przedsiębiorstwo.  |Telemetrię: monitorowanie trasy internetowej, priorytetyzacja ruchu od firmy Microsoft do najbliższej lokalizacji punktu POP krawędzi użytkownika. |



## <a name="onboarding-peering-service-connection"></a>Nawiązywanie połączenia z usługą komunikacji równorzędnej

Aby dołączyć połączenie z usługą komunikacji równorzędnej, wykonaj następujące czynności:

Współpracuj z partnerem usługodawcy internetowego (ISP) lub Internet Exchange (IX), aby uzyskać usługę komunikacji równorzędnej w celu połączenia sieci z siecią firmy Microsoft.

Upewnij się, że [dostawcy łączności](location-partners.md) są współpracujące z usługą komunikacji równorzędnej firmy Microsoft. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Dodawanie telemetrii połączenia usługi Komunikacja równorzędna

Klienci mogą wybrać swoje dane telemetryczne, takie jak routing tras protokołu BGP, aby monitorować opóźnienia i wydajność sieci podczas uzyskiwania dostępu do sieci firmy Microsoft. Można to osiągnąć przez zarejestrowanie połączenia w Azure Portal.

Aby dołączać dane telemetryczne połączenia usługi komunikacji równorzędnej, klient musi zarejestrować połączenie usługi komunikacji równorzędnej w Azure Portal. Zapoznaj się z procedurą [rejestracji komunikacji równorzędnej — Azure Portal](azure-portal.md) .

Poniżej można zmierzyć dane telemetryczne, odwołując się [tutaj](measure-connection-telemetry.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat sposobu rejestrowania połączenia z usługą komunikacji równorzędnej, zobacz temat [Rejestrowanie usługi komunikacji równorzędnej — Azure Portal](azure-portal.md).

Aby dowiedzieć się więcej o telemetrii połączenia miary, zobacz [mierzenie danych telemetrycznych połączeń](measure-connection-telemetry.md).
