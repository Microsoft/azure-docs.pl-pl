---
title: 'Szybki Start: Włączanie usługi'
description: Dowiedz się, jak dołączać i włączać Azure Security Center usługi zabezpieczeń IoT w usłudze Azure IoT Hub.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0d3e4ad63baf6883aa8fadaca5b1f2d28fa14881
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310750"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Szybki Start: dołączanie Azure Security Center dla usługi IoT w programie IoT Hub

Ten artykuł zawiera informacje na temat sposobu włączania usługi IoT Azure Security Center dla istniejących IoT Hub. Jeśli obecnie nie masz IoT Hub, zobacz [tworzenie IoT Hub przy użyciu Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) , aby rozpocząć pracę.

> [!NOTE]
> Azure Security Center IoT obecnie obsługuje tylko centra IoT w warstwie Standardowa.

## <a name="prerequisites-for-enabling-the-service"></a>Wymagania wstępne dotyczące włączania usługi

- Obszar roboczy usługi Log Analytics
  - Dwa typy informacji są domyślnie przechowywane w obszarze roboczym Log Analytics przez Azure Security Center dla IoT; **alerty** i **zalecenia dotyczące**zabezpieczeń.
  - Możesz dodać magazyn o dodatkowym typie informacji, **zdarzenia pierwotne**. Należy pamiętać, że przechowywanie **nieprzetworzonych zdarzeń** w log Analytics obejmuje dodatkowe koszty magazynowania.
- IoT Hub (warstwa standardowa)
- Spełnia wszystkie [wymagania wstępne dotyczące usługi](service-prerequisites.md)

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Włącz Azure Security Center IoT na IoT Hub

Aby włączyć zabezpieczenia na IoT Hub:

1. Otwórz **IoT Hub** w Azure Portal.
1. W menu **zabezpieczenia** kliknij pozycję **Zabezpiecz swoje rozwiązanie IoT**.

Gratulacje! Ukończono Włączanie Azure Security Center IoT na IoT Hub.

### <a name="geolocation-and-ip-address-handling"></a>Obsługa geolokalizacji i adresów IP

Aby zabezpieczyć rozwiązanie IoT, adresy IP przychodzących i wychodzących połączeń z i z urządzeń IoT, IoT Edge i IoT Hub są zbierane i przechowywane domyślnie. Te informacje są niezbędne do wykrywania nietypowej łączności z podejrzanych źródeł adresów IP. Na przykład podczas próby nawiązania połączenia ze źródła IP znanego botnet lub ze źródła IP poza lokalizacją geolokalizację. Azure Security Center usługi IoT oferuje elastyczność umożliwiającą w dowolnym momencie włączenie i wyłączenie zbierania danych adresów IP.

Aby włączyć lub wyłączyć zbieranie danych adresów IP:

1. Otwórz IoT Hub a następnie wybierz pozycję **Przegląd** z menu **zabezpieczenia** .
1. Wybierz ekran **Ustawienia** , a następnie zmodyfikuj ustawienia geolokalizacji i/lub obsługa adresów IP.

### <a name="log-analytics-creation"></a>Tworzenie Log Analytics

Gdy Azure Security Center IoT jest włączona, zostanie utworzony domyślny obszar roboczy platformy Azure Log Analytics w celu przechowywania nieprzetworzonych zdarzeń zabezpieczeń, alertów i zaleceń dotyczących urządzeń IoT, IoT Edge i IoT Hub. Co miesiąc, pierwsze pięć (5) GB danych pozyskanych na klientach do usługi Log Analytics platformy Azure jest bezpłatnych. Co GB Dane pozyskiwane w obszarze roboczym usługi Azure Log Analytics są przechowywane bezpłatnie przez pierwsze 31 dni. Dowiedz się więcej o cenach [log Analytics](https://azure.microsoft.com/pricing/details/monitor/) .

Aby zmienić konfigurację obszaru roboczego Log Analytics:

1. Otwórz IoT Hub a następnie wybierz pozycję **Przegląd** z menu **zabezpieczenia** .
1. Wybierz ekran **Ustawienia** i zmodyfikuj konfigurację obszaru roboczego ustawień log Analytics.

### <a name="customize-your-iot-security-solution"></a>Dostosowywanie rozwiązania do zabezpieczeń IoT

Domyślnie włączenie Azure Security Center na potrzeby rozwiązania IoT automatycznie zabezpiecza wszystkie centra IoT w ramach subskrypcji platformy Azure.

Aby włączyć lub wyłączyć Azure Security Center usługi IoT na określonym IoT Hub:

1. Otwórz IoT Hub a następnie wybierz pozycję **Przegląd** z menu **zabezpieczenia** .
1. Wybierz ekran **Ustawienia** i zmodyfikuj ustawienia zabezpieczeń dowolnego Centrum IoT Hub w ramach subskrypcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby skonfigurować Twoje rozwiązanie...

> [!div class="nextstepaction"]
> [Konfigurowanie rozwiązania](quickstart-configure-your-solution.md)
