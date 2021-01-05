---
title: Pracuj z alertami na czujniku
description: Pracuj z alertami, aby zwiększyć bezpieczeństwo i działanie sieci.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 00207ffb8480ae99c2f1aad74183fca9ea45ee17
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842847"
---
# <a name="work-with-alerts-on-your-sensor"></a>Pracuj z alertami na czujniku

Pracuj z alertami, aby zwiększyć bezpieczeństwo i działanie sieci. Alerty zawierają informacje o:

- Odchylenia od dozwolonej aktywności sieciowej

- Anomalie protokołu i działania

- Podejrzany ruch złośliwego oprogramowania

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Wykrywanie skanowania adresów.":::

Opcje zarządzania alertami umożliwiają użytkownikom:

- Poinstruuj czujniki, aby poznać działanie wykryte jako autoryzowany ruch.

- Potwierdź przeglądanie alertu.

- Poinstruuj czujniki, aby wyciszyć zdarzenia wykryte z identycznymi urządzeniami i porównywalnym ruchem.

Dostępne są dodatkowe narzędzia, które ułatwiają zwiększenie i przyspieszenie badania alertu. Na przykład:

  - Dodawanie komentarzy do instrukcji dla recenzentów alertów.

  - Utwórz grupy alertów do wyświetlania w rozwiązaniach SOC. 

  - Wyszukaj określone alerty; Przejrzyj powiązane pliki PCAP; Wyświetlanie wykrytych urządzeń i innych podłączonych urządzeń w mapie urządzeń lub wysyłanie szczegółów alertu do systemów partnerskich.

  - Przekazuj alerty dostawcom partnerskim: systemy SIEM, systemy MSSP i inne.

## <a name="alerts-and-engines"></a>Alerty i aparaty

Alerty są wyzwalane, gdy aparaty czujników wykrywają zmiany w ruchu sieciowym i zachowaniu, które wymagają Twojej uwagi. W tym artykule opisano rodzaj alertów, które są wyzwalane przez każdy aparat.

| Typ alertu | Opis |
|-|-|
| Alerty naruszenia zasad | Wyzwalane, gdy aparat naruszenia zasad wykrywa odchylenia od wcześniej podanego ruchu. Na przykład: <br /> -Zostanie wykryte nowe urządzenie.  <br /> -Na urządzeniu Wykryto nową konfigurację. <br /> -Urządzenie nie zostało zdefiniowane jako urządzenie programistyczne przeprowadzi zmianę programistyczną. <br /> -Zmieniono wersję oprogramowania układowego. |
| Alerty naruszenia protokołu | Wyzwalane, gdy aparat naruszenia protokołu wykryje struktury pakietów lub wartości pól, które nie są zgodne ze specyfikacją protokołu. | 
| Alerty operacyjne | Wyzwalane, gdy aparat operacyjny wykrywa zdarzenia operacyjne sieci lub działa nieprawidłowo. Na przykład urządzenie sieciowe zostało zatrzymane za pomocą polecenia zatrzymania PLC lub interfejs na czujniku przestał ruch monitorowania. |
| Alerty złośliwego oprogramowania | Wyzwalane, gdy aparat złośliwego oprogramowania wykryje złośliwe działanie sieciowe. Na przykład aparat wykrywa znany atak, taki jak robak. |
| Alerty anomalii | Wyzwalane, gdy aparat anomalii wykryje odchylenia. Na przykład urządzenie wykonuje skanowanie sieci, ale nie jest zdefiniowane jako urządzenie skanujące. |

Dostępne są narzędzia do włączania i wyłączania aparatów czujników. Alerty nie są wyzwalane z wyłączonych aparatów. Sprawdź, [jaki ruch jest monitorowany](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Alerty i raportowanie czujników

Działanie odzwierciedlone w alertach można obliczyć podczas generowania wyszukiwania danych, oceny ryzyka i raportów wektorów ataków. Gdy zarządzasz tymi zdarzeniami, czujnik odpowiednio aktualizuje raporty.

Na przykład:

  - Nieautoryzowana łączność między urządzeniem w zdefiniowanej podsieci a urządzeniami znajdującymi się poza podsiecią (publiczną) zostanie wyświetlona w raporcie dotyczącym wyszukiwania danych w Internecie i w sekcji *połączenia internetowe* *dotyczące* oceny ryzyka. Gdy te urządzenia zostaną autoryzowane (wyznani), są one obliczane podczas generowania tych raportów.

  - Zdarzenia złośliwego oprogramowania wykryte na urządzeniach sieciowych są zgłaszane w raportach oceny ryzyka. Gdy alerty dotyczące zdarzeń złośliwego oprogramowania są *wyciszone*, zmodyfikowane urządzenia nie zostaną obliczone w raporcie oceny ryzyka.

## <a name="see-also"></a>Zobacz także

- [Nauka i inteligentne tryby nauki IT](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [Wyświetlanie informacji podanych w alertach](how-to-view-information-provided-in-alerts.md)
- [Zarządzanie wydarzeniem alertu](how-to-manage-the-alert-event.md)
- [Przyspiesz przepływy pracy alertów](how-to-accelerate-alert-incident-response.md)
