---
title: 'Szybki Start: Włączanie usługi'
description: Dowiedz się, jak dołączyć i włączyć usługę Defender for IoT Security na platformie Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e3768ef233c60f1687bc804778c3dabf32666e1d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835163"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Szybki Start: dołączanie usługi Azure Defender for IoT w IoT Hub

Ten artykuł zawiera wyjaśnienie, jak włączyć usługę Defender for IoT na istniejącym IoT Hub. Jeśli obecnie nie masz IoT Hub, zobacz [tworzenie IoT Hub przy użyciu Azure Portal](../iot-hub/iot-hub-create-through-portal.md) , aby rozpocząć pracę.

> [!NOTE]
> Usługa Defender for IoT obecnie obsługuje tylko centra usługi IoT w warstwie Standardowa.

## <a name="prerequisites-for-enabling-the-service"></a>Wymagania wstępne dotyczące włączania usługi

- Obszar roboczy usługi Log Analytics
  - Dwa typy informacji są domyślnie przechowywane w obszarze roboczym Log Analytics przez usługę Defender for IoT; **alerty** i **zalecenia dotyczące** zabezpieczeń.
  - Możesz dodać magazyn o dodatkowym typie informacji, **zdarzenia pierwotne**. Należy pamiętać, że przechowywanie **nieprzetworzonych zdarzeń** w log Analytics obejmuje dodatkowe koszty magazynowania.
- IoT Hub (warstwa standardowa)
- Spełnione są wszystkie [wymagania wstępne systemu](quickstart-system-prerequisites.md).

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>Włącz usługę Defender for IoT na IoT Hub

Aby włączyć zabezpieczenia na IoT Hub:

1. Otwórz **IoT Hub** w Azure Portal.
1. W menu **zabezpieczenia** kliknij pozycję **Zabezpiecz swoje rozwiązanie IoT**.

Gratulacje! Zakończono Włączanie usługi Defender for IoT na IoT Hub.

### <a name="geolocation-and-ip-address-handling"></a>Obsługa geolokalizacji i adresów IP

Aby zabezpieczyć rozwiązanie IoT, adresy IP przychodzących i wychodzących połączeń z i z urządzeń IoT, IoT Edge i IoT Hub są zbierane i przechowywane domyślnie. Te informacje są niezbędne do wykrywania nietypowej łączności z podejrzanych źródeł adresów IP. Na przykład podczas próby nawiązania połączenia ze źródła IP znanego botnet lub ze źródła IP poza lokalizacją geolokalizację. Usługa Defender for IoT oferuje elastyczność umożliwiającą w dowolnym momencie włączenie i wyłączenie zbierania danych adresów IP.

Aby włączyć lub wyłączyć zbieranie danych adresów IP:

1. Otwórz IoT Hub a następnie wybierz pozycję **Ustawienia** z menu **zabezpieczenia** .
1. Wybierz ekran **zbieranie danych** i zmodyfikuj ustawienia geolokalizacji i/lub obsługa adresów IP.

### <a name="log-analytics-creation"></a>Tworzenie Log Analytics

Gdy usługa Defender for IoT jest włączona, zostanie utworzony domyślny obszar roboczy platformy Azure Log Analytics, w którym będą przechowywane surowe zdarzenia zabezpieczeń, alerty i zalecenia dotyczące urządzeń IoT, IoT Edge i IoT Hub. Co miesiąc, pierwsze pięć (5) GB danych pozyskanych na klientach do usługi Log Analytics platformy Azure jest bezpłatnych. Co GB Dane pozyskiwane w obszarze roboczym usługi Azure Log Analytics są przechowywane bezpłatnie przez pierwsze 31 dni. Dowiedz się więcej o cenach [log Analytics](https://azure.microsoft.com/pricing/details/monitor/) .

Aby zmienić konfigurację obszaru roboczego Log Analytics:

1. Otwórz IoT Hub a następnie wybierz pozycję **Ustawienia** z menu **zabezpieczenia** .
1. Wybierz ekran **zbieranie danych** i zmodyfikuj konfigurację obszaru roboczego ustawień log Analytics.

### <a name="customize-your-iot-security-solution"></a>Dostosowywanie rozwiązania do zabezpieczeń IoT

Domyślnie włączenie rozwiązania Defender for IoT automatycznie zabezpiecza wszystkie centra IoT w ramach subskrypcji platformy Azure.

Aby włączyć lub wyłączyć usługę Defender for IoT na określonym IoT Hub:

1. Otwórz IoT Hub a następnie wybierz pozycję **Ustawienia** z menu **zabezpieczenia** .
1. Wybierz ekran **zbieranie danych** i zmodyfikuj ustawienia zabezpieczeń dowolnego Centrum IoT Hub w ramach subskrypcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby skonfigurować Twoje rozwiązanie...

> [!div class="nextstepaction"]
> [Konfigurowanie rozwiązania](quickstart-configure-your-solution.md)