---
title: Moduł zabezpieczeń dla usługi Azure RTO — Omówienie
description: Dowiedz się więcej na temat modułu zabezpieczeń na potrzeby obsługi i implementacji usługi Azure RTO w ramach usługi Azure Defender dla IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2021
ms.author: shhazam
ms.openlocfilehash: 47b2b49a21a82be13c987b95c7c775f5014a532e
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809086"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Przegląd: usługa Defender for IoT Security module dla usługi Azure RTO (wersja zapoznawcza)

Usługa Azure Defender for IoT Micro oferuje kompleksowe rozwiązanie w zakresie zabezpieczeń dla urządzeń korzystających z usługi Azure RTO. Zapewnia to pokrycie typowych zagrożeń i potencjalnie złośliwych działań na urządzeniach z systemem operacyjnym (RTO) w czasie rzeczywistym. Usługa Azure RTO jest teraz dostarczana z modułem zabezpieczeń usługi Azure IoT wbudowanym w systemie.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Wizualizacja usługi Defender for IoT Azure RTO.":::


Micro module dla usługi Azure RTO oferuje następujące funkcje:

- Wykrycie złośliwego działania sieci
- Niestandardowe zachowanie urządzenia oparte na alertach określania poziomu odniesienia
- Ulepszona higiena zabezpieczeń urządzeń

## <a name="detect-malicious-network-activities"></a>Wykrywaj złośliwe działania sieci

Monitorowane są aktywność sieci przychodzących i wychodzących poszczególnych urządzeń. Obsługiwane protokoły to TCP, UDP i ICMP na protokołach IPv4 i IPv6. Usługa Defender for IoT sprawdza wszystkie te działania w sieci w odniesieniu do źródła danych analizy zagrożeń firmy Microsoft. Kanał informacyjny zostanie zaktualizowany w czasie rzeczywistym przy użyciu milionów unikatowych wskaźników zagrożeń zebranych na całym świecie.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Zachowanie urządzenia określania poziomu odniesienia na podstawie alertów niestandardowych

Określania poziomu odniesienia umożliwia klastrowanie urządzeń w grupach zabezpieczeń i Definiowanie oczekiwanych zachowań poszczególnych grup. Ponieważ urządzenia IoT są zwykle zaprojektowane do działania w dobrze zdefiniowanych i ograniczonych scenariuszach, można łatwo utworzyć linię bazową, która definiuje ich oczekiwane zachowanie przy użyciu zestawu parametrów. Wszelkie odchylenia od linii bazowej wyzwala alert.

## <a name="improve-your-device-security-hygiene"></a>Ulepszanie higieny bezpieczeństwa urządzeń

Korzystając z zalecanej usługi Defender for IoT, można uzyskać wiedzę i szczegółowe informacje o problemach w Twoim środowisku, które wpływają i uszkadzają stan zabezpieczeń urządzeń. Słabe stan zabezpieczeń urządzenia IoT mogą pozwolić na pomyślne ataki, jeśli pozostanie bez zmian. Zabezpieczenia są zawsze mierzone najsłabszym łączem w każdej organizacji.

## <a name="get-started-protecting-azure-rtos-devices"></a>Wprowadzenie do ochrony urządzeń z usługą Azure RTO

Moduł zabezpieczeń dla usługi Azure RTO jest udostępniany bezpłatnie do pobrania dla urządzeń. Usługa Defender for IoT w chmurze jest dostępna w ramach 30-dniowej wersji próbnej dla każdej subskrypcji platformy Azure. Aby rozpocząć, Pobierz [moduł zabezpieczeń dla usługi Azure RTO](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o module zabezpieczeń usługi Azure RTO. Aby dowiedzieć się więcej na temat modułu zabezpieczeń i rozpocząć pracę, zobacz następujące artykuły:

- [Pojęcia dotyczące modułu zabezpieczeń usługi Azure RTO IoT](concept-rtos-security-module.md)
- [Szybki Start: Azure RTO IoT Security module](quickstart-azure-rtos-security-module.md)
