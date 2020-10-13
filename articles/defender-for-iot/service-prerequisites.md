---
title: Składniki & wymagania wstępne
description: Szczegóły dotyczące wszystkiego, czego potrzebujesz, aby rozpocząć pracę z usługą Azure Defender pod kątem wymagań wstępnych usługi IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937836"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Wymagania wstępne dotyczące usługi Azure Defender dla IoT

W tym artykule opisano różne składniki usługi Defender for IoT, co należy zacząć i wyjaśniono podstawowe pojęcia ułatwiające zrozumienie usługi.

## <a name="minimum-requirements"></a>Minimalne wymagania

- Warstwa standardowa IoT Hub
  - Uprawnienia na poziomie **właściciela** roli platformy Azure
- [Obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (zalecane)
  - Korzystanie z Azure Security Center jest zaleceniem, a nie wymaganiem. Bez Azure Security Center nie będzie można wyświetlać innych zasobów platformy Azure w ramach usługi IoT Hub.

## <a name="working-with-defender-for-iot-service"></a>Praca z usługą Defender for IoT

Usługa Defender for IoT Insights i raporty są dostępne za pomocą usługi Azure IoT Hub i Azure Security Center. Aby włączyć usługę Defender for IoT na platformie Azure IoT Hub, wymagane jest konto z uprawnieniami na poziomie **właściciela** . Po włączeniu funkcji ASC dla IoT w IoT Hub usługa Defender for IoT Insights zostanie wyświetlona jako funkcja **zabezpieczeń** w usłudze Azure IoT Hub i jako  **IoT** w Azure Security Center.

## <a name="supported-service-regions"></a>Obsługiwane regiony usługi

Usługa Defender for IoT jest obecnie obsługiwana w przypadku centrów IoT w następujących regionach platformy Azure:

- Central US
- East US
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Południowo-środkowe stany USA
- Północno-środkowe stany USA
- Kanada Środkowa
- Kanada Wschodnia
- Europa Północna
- Brazil South
- Francja Środkowa
- Zachodnie Zjednoczone Królestwo
- Południowe Zjednoczone Królestwo
- West Europe
- Europa północna
- Japonia Zachodnia
- Japan East
- Australia Południowo-Wschodnia
- Australia Wschodnia
- Azja Wschodnia
- Southeast Asia
- Korea Środkowa
- Korea Południowa
- Indie Środkowe
- Indie Południowe

Usługa Defender for IoT kieruje cały ruch ze wszystkich regionów europejskich do regionalnego centrum danych Europa Zachodnia i wszystkie pozostałe regiony do centralnych, regionalnych centrów danych USA.

## <a name="wheres-my-iot-hub"></a>Gdzie mam IoT Hub?

Sprawdź lokalizację IoT Hub, aby zweryfikować dostępność usługi przed rozpoczęciem.

1. Otwórz IoT Hub.
1. Kliknij pozycję **Przegląd**.
1. Sprawdź, czy podana lokalizacja odpowiada jednemu z [obsługiwanych regionów usługi](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Obsługiwane platformy dla agentów

Usługa Defender dla agentów IoT obsługuje rosnącą listę urządzeń i platform. Zapoznaj się z [listą obsługiwanych platform](how-to-deploy-agent.md) , aby sprawdzić istniejącą lub zaplanowaną bibliotekę urządzeń.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [Omówienie](overview.md) zabezpieczeń usługi Azure IoT
- Dowiedz się [, jak włączyć usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często zadawane pytania dotyczące usługi Defender for IoT](resources-frequently-asked-questions.md)
- Zapoznaj się z tematem jak [zrozumieć alerty usługi Defender for IoT](concept-security-alerts.md)
