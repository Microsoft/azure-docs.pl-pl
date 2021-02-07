---
title: Azure DDoS Protection testowania symulacji
description: Dowiedz się, jak przetestować symulacje
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e95495e48725a68ab1fe3f37d235e5765b2c8015
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806243"
---
# <a name="test-through-simulations"></a>Testowanie przez symulacje

Dobrym sposobem jest przetestowanie założeń dotyczących sposobu reagowania usług na ataki przez przeprowadzenie okresowych symulacji. Podczas testowania Sprawdź, czy usługi lub aplikacje nadal działają zgodnie z oczekiwaniami, i nie przerywaj pracy użytkownika. Zidentyfikuj luki zarówno z technologii, jak i procesu punktu widzenia i Uwzględnij je w strategii odpowiedzi DDoS. Zalecamy przeprowadzenie takich testów w środowiskach przejściowych lub poza godzinami szczytu, aby zminimalizować wpływ na środowisko produkcyjne.

Współpracujemy z [chmurą BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud), generatorem ruchu samoobsługowego, aby utworzyć interfejs, w którym klienci platformy Azure mogą generować ruch dla publicznych punktów końcowych z włączonymi DDoS Protectionami dla symulacji. Możesz użyć symulacji, aby:

- Sprawdź, jak Azure DDoS Protection pomaga chronić zasoby platformy Azure przed atakami DDoS.
- Zoptymalizuj proces reagowania na zdarzenia w trakcie ataku DDoS.
- Zgodność dokumentu DDoS.
- Uczenie zespołów zabezpieczeń sieci.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć [Plan ochrony Standard platformy Azure DDoS](manage-ddos-protection.md) z chronionymi publicznymi adresami IP.
- Najpierw musisz utworzyć konto w [chmurze BreakingPoint](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Konfigurowanie ataku testowego DDoS

1. Wprowadź lub wybierz następujące wartości, a następnie wybierz pozycję **Rozpocznij Test**:

    |Ustawienie        |Wartość                                              |
    |---------      |---------                                          |
    |Docelowy adres IP           | Wprowadź jeden z publicznych adresów IP, które chcesz przetestować.                     |
    |Numer portu   | Wprowadź _443_.                       |
    |Profil DDoS | Możliwe wartości to,,,,,,,,,, `DNS Flood` `NTPv2 Flood` `SSDP Flood` `TCP SYN Flood` `UDP 64B Flood` `UDP 128B Flood` `UDP 256B Flood` `UDP 512B Flood` `UDP 1024B Flood` `UDP 1514B Flood` `UDP Fragmentation` , `UDP Memcached` .|
    |Rozmiar testu       | Możliwe wartości to `100K pps, 50 Mbps and 4 source IPs` , `200K pps, 100 Mbps and 8 source IPs` , `400K pps, 200Mbps and 16 source IPs` , `800K pps, 400 Mbps and 32 source IPs` .                                  |
    |Czas trwania testu | Możliwe wartości to `10 Minutes` , `15 Minutes` , `20 Minutes` , `25 Minutes` , `30 Minutes` .|

Powinien teraz wyglądać następująco:

![Przykład symulacji ataku DDoS: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Monitorowanie i weryfikowanie

1. Zaloguj się do https://portal.azure.com subskrypcji i przejdź do niej.
1. Wybierz publiczny adres IP, na którym przetestowano atak.
1. W obszarze **Monitorowanie** wybierz pozycję **Metryki**.
1. W polu **Metryka** wybierz opcję _w obszarze atak DDoS_.

Gdy zasób jest w trakcie ataku, należy sprawdzić, czy wartość zmienia się z **0** na **1**, tak jak na poniższej ilustracji:

![Przykład symulacji ataku DDoS: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>Skrypt interfejsu API chmury BreakingPoint

Ten [skrypt interfejsu API](https://aka.ms/ddosbreakingpoint) może służyć do automatyzowania testowania DDoS przez uruchomienie jednokrotne lub użycie firmy CRONUS do zaplanowania regularnych testów. Jest to przydatne w celu sprawdzenia, czy rejestrowanie jest prawidłowo skonfigurowane i czy procedury wykrywania i reagowania są skuteczne. Skrypty wymagają systemu operacyjnego Linux (przetestowane z Ubuntu 18,04 LTS) i Python 3. Zainstaluj wymagania wstępne i klienta interfejsu API za pomocą dołączonego skryptu lub korzystając z dokumentacji w witrynie sieci Web [BreakingPoint w chmurze](http://breakingpoint.cloud/) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wyświetlać i konfigurować telemetrię ochrony DDoS](telemetry.md).
- Dowiedz się [, jak wyświetlać i konfigurować rejestrowanie diagnostyczne DDoS](diagnostic-logging.md).
- Dowiedz się, jak [angażować DDoS szybką odpowiedź](ddos-rapid-response.md).
