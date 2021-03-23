---
title: Konfigurowanie rozwiązania Azure Defender for IoT opartego na agentach
description: Dowiedz się, jak skonfigurować zbieranie danych w usłudze Azure Defender dla rozwiązania IoT na podstawie agenta
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 9a21b336299438b89fae8d5a837130762a7f36e8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784394"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Konfigurowanie rozwiązania Azure Defender for IoT opartego na agentach  

W tym artykule opisano sposób konfigurowania zbierania danych w usłudze Azure Defender dla rozwiązania IoT opartego na agentach.

## <a name="configure-data-collection"></a>Konfigurowanie zbierania danych

Aby skonfigurować zbieranie danych w usłudze Azure Defender dla rozwiązania IoT na podstawie agenta: 

1. Przejdź do Azure Portal i wybierz IoT Hub, do których jest dołączona usługa Defender for IoT 

1. W menu  **zabezpieczenia**   Wybierz pozycję **Ustawienia**. 

1. Wybierz pozycję **zbieranie danych**. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Wybierz pozycję zbieranie danych z ustawień menu zabezpieczenia.":::

## <a name="geolocation-and-ip-address-handling"></a>Obsługa geolokalizacji i adresów IP 

Aby można było zabezpieczyć rozwiązanie IoT, adresy IP przychodzących i wychodzących połączeń dla urządzeń IoT, IoT Edge i IoT Hub są zbierane i przechowywane domyślnie. Te informacje są niezbędne i służą do wykrywania nietypowej łączności z podejrzanych źródeł adresów IP. Na przykład podczas próby nawiązania połączenia ze źródła adresu IP znanego botnet lub ze źródła adresu IP poza lokalizacją geolokalizację. Usługa Defender for IoT oferuje elastyczność umożliwiającą w dowolnym momencie włączenie i wyłączenie zbierania danych adresów IP. 

Aby włączyć lub wyłączyć zbieranie danych adresów IP: 

1. Otwórz IoT Hub a następnie wybierz pozycję **Ustawienia**   z menu **zabezpieczenia**   . 

1. Wybierz ekran **zbieranie danych**   i zmodyfikuj ustawienia geolokalizacji i obsługi adresów IP zgodnie z potrzebami. 

## <a name="log-analytics-creation"></a>Tworzenie Log Analytics 

Usługa Defender for IoT umożliwia przechowywanie alertów zabezpieczeń, zaleceń i danych pierwotnych zabezpieczeń w obszarze roboczym Log Analytics. Log Analytics pozyskiwanie w IoT Hub jest domyślnie **wyłączone** w ramach rozwiązania Defender for IoT. Istnieje możliwość dołączenia usługi Defender for IoT do obszaru roboczego usługi log Analytics, a także do przechowywania danych zabezpieczeń. 

Istnieją dwa typy informacji przechowywanych domyślnie w obszarze roboczym Log Analytics przez usługę Defender dla IoT:
 
- Alerty zabezpieczeń.

- Mając. 

Możesz wybrać opcję dodania magazynu dla dodatkowego typu informacji `raw events` . 

> [!Note] 
> Przechowywanie  `raw events`   w log Analytics obejmuje dodatkowe koszty magazynowania. 

Aby umożliwić Log Analytics pracy z programem Micro Agent: 

1. Przejdź do kolekcji danych **konfiguracji obszaru roboczego**  >  i Przełącz przełącznik na wartość  **włączone**. 

1. Utwórz nowy obszar roboczy Log Analytics lub Dołącz istniejący. 

1. Sprawdź, czy wybrano opcję **dostęp do danych pierwotnych zabezpieczeń**   .  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Upewnij się, że wybrano dostęp do danych pierwotnych zabezpieczeń.":::

1. Wybierz pozycję **Zapisz**.

Co miesiąc, pierwsze 5 gigabajtów danych odbieranych przez klienta w usłudze Log Analytics platformy Azure jest bezpłatnych. Każde gigabajty danych pozyskanych w obszarze roboczym usługi Azure Log Analytics jest zachowywane bezpłatnie przez pierwsze 31 dni. Aby uzyskać więcej informacji na temat cen, zobacz [log Analytics Cennik](https://azure.microsoft.com/pricing/details/monitor/). 

Aby zmienić konfigurację obszaru roboczego Log Analytics: 

1. W IoT Hub w menu **zabezpieczenia** wybierz pozycję  **Ustawienia**. 

1. Wybierz ekran **zbieranie danych**   i zmodyfikuj konfigurację obszaru roboczego ustawień log Analytics, aby odpowiadały Twoim potrzebom. 

Aby uzyskiwać dostęp do alertów w obszarze roboczym Log Analytics po zakończeniu konfiguracji:

1. Wybierz Alert w usłudze Defender for IoT.

1. Wybierz pozycję **Zbadaj alerty w obszarze roboczym log Analytics**.

Aby uzyskiwać dostęp do alertów w obszarze roboczym Log Analytics po zakończeniu konfiguracji:

1. Wybierz rekomendację w usłudze Defender for IoT.

1. Wybierz pozycję **Zbadaj zalecenia w obszarze roboczym log Analytics**. 
 
Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących danych z Log Analytics, zobacz [Rozpoczynanie pracy z zapytaniami w log Analytics](../azure-monitor/logs/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>Wyłączanie usługi Defender dla IoT 

Aby włączyć lub wyłączyć usługę Defender for IoT na określonym IoT Hub: 

1. W IoT Hub w menu **zabezpieczenia** wybierz pozycję  **Ustawienia**.

1. Wybierz ekran **zbieranie danych**   i zmodyfikuj konfigurację obszaru roboczego ustawień log Analytics, aby odpowiadały Twoim potrzebom.

## <a name="next-steps"></a>Następne kroki 

Przejdź do następnego artykułu, aby [skonfigurować rozwiązanie](quickstart-configure-your-solution.md).