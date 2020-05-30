---
title: Składniki & wymagania wstępne
description: Szczegóły dotyczące wszystkiego, czego potrzebujesz, aby rozpocząć pracę z Azure Security Centerami dotyczącymi wymagań wstępnych usługi IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19e4ea37aa10c90d15a2b7dcdf962c131c8e473d
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193212"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center wymagań wstępnych usługi IoT

W tym artykule opisano różne składniki Azure Security Center usługi IoT, co należy zacząć i wyjaśniono podstawowe pojęcia ułatwiające zrozumienie usługi.

## <a name="minimum-requirements"></a>Minimalne wymagania

- Warstwa standardowa IoT Hub
  - Uprawnienia na poziomie **właściciela** roli RBAC
- [Log Analytics obszar roboczy](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (zalecane)
  - Korzystanie z Azure Security Center jest zaleceniem, a nie wymaganiem. Bez Azure Security Center nie będzie można wyświetlać innych zasobów platformy Azure w ramach usługi IoT Hub.

## <a name="working-with-azure-security-center-for-iot-service"></a>Praca z Azure Security Center dla usługi IoT

Azure Security Center usługi IoT Insights i raportowania są dostępne za pomocą usług Azure IoT Hub i Azure Security Center. Aby włączyć Azure Security Center IoT na platformie Azure IoT Hub, wymagane jest konto z uprawnieniami na poziomie **właściciela** . Po włączeniu funkcji ASC dla IoT w IoT Hub Azure Security Center usługi IoT Insights będą wyświetlane jako funkcja **zabezpieczeń** w usłudze Azure IoT Hub oraz jako **IoT** w Azure Security Center.

## <a name="supported-service-regions"></a>Obsługiwane regiony usługi

Azure Security Center usługi IoT jest obecnie obsługiwana w przypadku centrów IoT w następujących regionach platformy Azure:

- Środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Południowo-środkowe stany USA
- Północno-środkowe stany USA
- Kanada Środkowa
- Kanada Wschodnia
- Europa Północna
- Brazylia Południowa
- Francja Środkowa
- Zachodnie Zjednoczone Królestwo
- Południowe Zjednoczone Królestwo
- Europa Zachodnia
- Europa północna
- Japonia Zachodnia
- Japonia Wschodnia
- Australia Południowo-Wschodnia
- Australia Wschodnia
- Azja Wschodnia
- Azja Południowo-Wschodnia
- Korea Środkowa
- Korea Południowa
- Indie Środkowe
- Indie Południowe

Azure Security Center usługi IoT kieruje cały ruch ze wszystkich regionów europejskich do regionalnego centrum danych Europa Zachodnia i wszystkie pozostałe regiony do centralnych, regionalnych centrów danych.

## <a name="wheres-my-iot-hub"></a>Gdzie mam IoT Hub?

Sprawdź lokalizację IoT Hub, aby zweryfikować dostępność usługi przed rozpoczęciem.

1. Otwórz IoT Hub.
1. Kliknij pozycję **Przegląd**.
1. Sprawdź, czy podana lokalizacja odpowiada jednemu z [obsługiwanych regionów usługi](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Obsługiwane platformy dla agentów

Azure Security Center dla agentów IoT obsługuje rosnącą listę urządzeń i platform. Zapoznaj się z [listą obsługiwanych platform](how-to-deploy-agent.md) , aby sprawdzić istniejącą lub zaplanowaną bibliotekę urządzeń.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [Omówienie](overview.md) zabezpieczeń usługi Azure IoT
- Dowiedz się [, jak włączyć usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [Azure Security Center dla usługi IoT — często zadawane pytania](resources-frequently-asked-questions.md)
- Dowiedz się, jak [zrozumieć Azure Security Center alertów IoT](concept-security-alerts.md)
