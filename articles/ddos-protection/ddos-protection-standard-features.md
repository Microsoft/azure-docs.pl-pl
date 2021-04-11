---
title: Funkcje Azure DDoS Protection
description: Informacje o Azure DDoS Protection funkcjach
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 490a65c7d20194956350166d23e2d3450b94b12e
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103100"
---
# <a name="azure-ddos-protection-standard-features"></a>Funkcje usługi Azure DDoS Protection w warstwie Standardowa

W poniższych sekcjach przedstawiono najważniejsze funkcje usługi Azure DDoS Protection Standard.

## <a name="always-on-traffic-monitoring"></a>Zawsze włączone monitorowanie ruchu

DDoS Protection monitoruje rzeczywiste wykorzystanie ruchu i stale porównuje go z progami zdefiniowanymi w zasadach DDoS. Po przekroczeniu progu ruchu środki zaradcze DDoS są inicjowane automatycznie. Gdy ruch wraca poniżej progów, środki zaradcze zostaną zatrzymane.

![Azure DDoS Protection ograniczenia standardowego](./media/ddos-protection-overview/mitigation.png)

Podczas rozwiązywania problemów ruch wysyłany do chronionego zasobu jest przekierowywany przez usługę ochrony DDoS i wykonywane jest kilka operacji sprawdzania, takich jak:

- Upewnij się, że pakiety są zgodne ze specyfikacjami internetowymi i nie są źle sformułowane.
- Współdziałanie z klientem w celu ustalenia, czy ruch jest potencjalnie sfałszowanym pakietem (np. SYN auth lub SYN cookie lub przez porzucanie pakietu dla źródła w celu ponownego przesłania go).
- Oceń liczbę pakietów, jeśli nie można wykonać żadnej innej metody wymuszania.

Ochrona DDoS odrzuca ruch związany z atakami i przekazuje ruch pozostały do zamierzonego miejsca docelowego. W ciągu kilku minut od wykrycia ataku otrzymasz powiadomienie przy użyciu metryk usługi Azure Monitor. Konfigurując funkcję rejestrowania na DDoS Protection standardową telemetrię, możesz napisać dzienniki, aby wyświetlić dostępne opcje dla przyszłej analizy. Dane metryk w Azure Monitor dla DDoS Protection standard są przechowywane przez 30 dni.

## <a name="adaptive-real-time-tuning"></a>Adaptacyjne dostosowanie czasu rzeczywistego

Usługa Azure DDoS Protection Basic pomaga chronić klientów i zapobiega wpływowi innych klientów. Jeśli na przykład usługa została zainicjowana w przypadku typowej ilości legalnego ruchu przychodzącego, który jest mniejszy niż *częstotliwość wyzwalania* zasad DDoS Protection całej infrastruktury, atak DDoS na zasoby tego klienta może być niezauważalny. Ogólnie rzecz biorąc, złożoność ostatnich ataków (na przykład wielowektorowe DDoS) i zachowań specyficznych dla aplikacji dla poszczególnych klientów, dopasowane zasady ochrony. Usługa jest realizowana przy użyciu dwóch szczegółowych informacji:

- Automatyczna nauka wzorców ruchu dla każdego klienta (na publiczny adres IP) dla warstwy 3 i 4.

- Minimalizacja fałszywych wartości dodatnich, biorąc pod uwagę, że skalowanie systemu Azure pozwala na pochłanianie znacznego natężenia ruchu.

![Diagram przedstawiający sposób działania standardu DDoS Protection, z zakreślonym wygenerowaniem zasad](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection dane telemetryczne, monitorowanie i alerty

DDoS Protection Standard uwidacznia rozbudowane dane telemetryczne za pośrednictwem [Azure monitor](../azure-monitor/overview.md). Można skonfigurować alerty dla dowolnych metryk Azure Monitor używanych przez DDoS Protection. Możesz zintegrować rejestrowanie z usługą Splunk (Azure Event Hubs), dziennikami Azure Monitor i usługą Azure Storage, aby uzyskać zaawansowaną analizę za pośrednictwem interfejsu diagnostyki Azure Monitor.

### <a name="ddos-mitigation-policies"></a>Zasady ograniczania DDoS

W Azure Portal wybierz pozycję **Monitoruj**  >  **metryki**. W okienku **metryk** wybierz grupę zasobów, wybierz typ zasobu **publiczny adres IP**, a następnie wybierz publiczny adres IP platformy Azure. Metryki DDoS są widoczne w okienku **Dostępne metryki** .

DDoS Protection standard stosuje trzy zasady ograniczania ryzyka (TCP SYN, TCP i UDP) dla każdego publicznego adresu IP chronionego zasobu w sieci wirtualnej z włączoną funkcją DDoS. Progi zasad można wyświetlić, wybierając pakiety przychodzące metryczne, **Aby wyzwolić DDoS ograniczenia**.

![Wykres dostępnych metryk i metryk](./media/ddos-best-practices/image-7.png)

Progi zasad są automatycznie konfigurowane za pośrednictwem profilowania sieci opartych na uczeniu maszynowym. W przypadku przekroczenia progu zasad środki zaradcze DDoS występują dla adresu IP w obszarze atak.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metryka adresu IP w obszarze atak DDoS

Jeśli publiczny adres IP jest w trakcie ataku, wartość metryki **w obszarze atak DDoS lub nie** zostanie zmieniona na 1, ponieważ DDoS Protection wykonuje środki zaradcze dla ruchu ataków.

!["W przypadku ataku DDoS lub nie" metryki i wykresu](./media/ddos-best-practices/image-8.png)

Zalecamy skonfigurowanie alertu dla tej metryki. Następnie otrzymasz powiadomienie, gdy na publicznym adresie IP zostanie wykonane aktywne ograniczenie DDoS.

Aby uzyskać więcej informacji, zobacz [zarządzanie Azure DDoS Protection standard przy użyciu Azure Portal](manage-ddos-protection.md).

## <a name="web-application-firewall-for-resource-attacks"></a>Zapora aplikacji sieci Web dla ataków zasobów

Specyficzne dla ataków zasobów w warstwie aplikacji należy skonfigurować zaporę aplikacji sieci Web (WAF), aby ułatwić Zabezpieczanie aplikacji sieci Web. WAF sprawdza przychodzący ruch internetowy, aby blokować iniekcje SQL, skrypty między lokacjami, DDoS i inne ataki warstwy 7. Platforma Azure udostępnia [WAF jako funkcję Application Gateway](../web-application-firewall/ag/ag-overview.md) w celu scentralizowanej ochrony aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. Dostępne są inne oferty WAF od partnerów platformy Azure, które mogą być bardziej odpowiednie dla Twoich potrzeb za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Nawet zapory aplikacji sieci Web są podatne na ataki na mocy i wyczerpania stanu. Zdecydowanie zalecamy włączenie standardu DDoS Protection w sieci wirtualnej WAF, aby ułatwić ochronę przed atakami typu "i". Aby uzyskać więcej informacji, zobacz sekcję [architektury referencyjne DDoS Protection](ddos-protection-reference-architectures.md) .

## <a name="protection-planning"></a>Planowanie ochrony

Planowanie i przygotowywanie ma kluczowe znaczenie dla zrozumienia, jak system będzie wykonywany podczas ataku DDoS. Projektowanie planu odpowiedzi zarządzania zdarzeniami jest częścią tego nakładu pracy.

Jeśli masz DDoS Protection Standard, upewnij się, że jest on włączony w sieci wirtualnej punktów końcowych dostępnych z Internetu. Konfigurowanie alertów DDoS pomaga stale oglądać potencjalne ataki na infrastrukturę. 

Monitoruj aplikacje niezależnie od siebie. Zapoznaj się z normalnym zachowaniem aplikacji. Przygotuj się do działania, jeśli aplikacja nie zachowuje się zgodnie z oczekiwaniami podczas ataku DDoS. 

Dowiedz się, w jaki sposób usługi będą reagować na ataki przez [przetestowanie przez symulacje](test-through-simulations.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć plan ochrony DDoS](manage-ddos-protection.md).
