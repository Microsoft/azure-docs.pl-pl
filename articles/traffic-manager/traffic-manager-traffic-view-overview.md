---
title: Widok ruchu na platformie Azure Traffic Manager
description: W tym instruktażu dowiesz się, jak działa widok ruchu w usłudze Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: f1a3cc5ccb326d6a198895f9f459e661f7805b1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401541"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Widok ruchu

Traffic Manager zapewnia Routing na poziomie systemu DNS, dzięki czemu użytkownicy końcowi są kierowani do zdrowych punktów końcowych na podstawie metody routingu określonej podczas tworzenia profilu. Widok ruchu zapewnia Traffic Manager z widokiem baz użytkowników (na poziomie stopnia szczegółowości systemu DNS) i ich wzorca ruchu. Po włączeniu Widok ruchu te informacje są przetwarzane w celu zapewnienia szczegółowych informacji z możliwością działania. 

Za pomocą Widok ruchu można:
- Informacje o tym, gdzie znajdują się bazy użytkowników (do poziomu szczegółowości lokalnego programu rozpoznawania nazw DNS).
- wyświetlanie ilości ruchu (zaobserwowane jako zapytania DNS obsługiwane przez usługę Azure Traffic Manager) pochodzące z tych regionów.
- uzyskaj wgląd w to, co jest opóźnieniem reprezentatywnym dla tych użytkowników.
- głębokie szczegółowe do określonych wzorców ruchu z poszczególnych baz użytkowników w regionach platformy Azure, w których znajdują się punkty końcowe. 

Na przykład można użyć Widok ruchu, aby zrozumieć, które regiony mają dużą liczbę ruchu, ale odczuwają większe opóźnienia. Następnie możesz użyć tych informacji do zaplanowania rozwinięcia zasięgu w nowych regionach świadczenia usługi Azure, dzięki czemu Ci użytkownicy będą mogli korzystać z mniejszego opóźnienia.

## <a name="how-traffic-view-works"></a>Jak działa Widok ruchu

Widok ruchu działa przez zaTraffic Manager zapoznaj się z zapytaniami przychodzącymi odebranymi w ciągu ostatnich siedmiu dni względem profilu, który ma włączoną tę funkcję. Na podstawie przychodzących informacji o zapytaniach Widok ruchu wyodrębnia źródłowy adres IP programu rozpoznawania nazw DNS, który jest używany jako reprezentacja lokalizacji użytkowników. Są one następnie pogrupowane na poziomie programu rozpoznawania nazw DNS, aby utworzyć regiony podstawowe użytkownika przy użyciu informacji geograficznych adresów IP obsługiwanych przez Traffic Manager. Traffic Manager następnie szuka regionów świadczenia usługi Azure, do których zapytanie zostało rozesłane i konstruuje mapę przepływu ruchu dla użytkowników z tych regionów.  
W następnym kroku Traffic Manager skorelowany region podstawowy użytkownika z mapowaniem regionów platformy Azure z tabelami opóźnień analizy sieci, które są przechowywane dla różnych sieci użytkowników końcowych, aby zrozumieć średnie opóźnienia napotykane przez użytkowników z tych regionów podczas łączenia się z regionami platformy Azure. Wszystkie te obliczenia są następnie łączone na poziomie lokalnego protokołu IP rozpoznawania nazw DNS przed jego wyświetleniem. Informacje można wykorzystać na różne sposoby.

Częstotliwość aktualizacji danych widoku ruchu zależy od wielu wewnętrznych zmiennych usługi. Jednak dane są zwykle aktualizowane co 24 godziny.

>[!NOTE]
>Opóźnienie opisane w Widok ruchu jest reprezentatywnym opóźnieniem między użytkownikiem końcowym i regionami świadczenia usługi Azure, z którymi nawiązano połączenie, i nie jest opóźnieniem wyszukiwania DNS. Widok ruchu zapewnia optymalny szacunek czasu oczekiwania między lokalnym programem rozpoznawania nazw DNS a regionem platformy Azure, do którego zostało skierowane zapytanie, jeśli jest za mało dostępnych danych, zwracane opóźnienie będzie równe null. 

## <a name="visual-overview"></a>Przegląd wizualizacji

Po przejściu do sekcji **Widok ruchu** na stronie Traffic Manager zostanie wyświetlona mapa geograficzna z nakładką widok ruchu szczegółowych informacji. Mapa zawiera informacje o podstawowym i punktach końcowych użytkownika dla profilu Traffic Manager.

![Traffic Manager Widok ruchu widoku geograficznego][1]

### <a name="user-base-information"></a>Informacje podstawowe użytkownika

Dla tych lokalnych funkcji rozpoznawania nazw DNS, dla których informacje o lokalizacji są dostępne, są wyświetlane na mapie. Kolor programu rozpoznawania nazw DNS oznacza średni czas oczekiwania napotykany przez użytkowników końcowych korzystających z tego mechanizmu rozpoznawania nazw DNS dla Traffic Manager zapytań.

Po umieszczeniu wskaźnika myszy na lokalizacji programu rozpoznawania nazw DNS na mapie zostanie wyświetlona wartość:
- adres IP programu rozpoznawania nazw DNS
- ilość ruchu zapytań DNS widzianych przez Traffic Manager z niego
- punkty końcowe, do których kierowany jest ruch z programu rozpoznawania nazw DNS, jako linia między punktem końcowym a programem rozpoznawania nazw DNS 
- Średnie opóźnienie z tej lokalizacji do punktu końcowego reprezentowane jako kolor linii łączącej je

### <a name="endpoint-information"></a>Informacje o punkcie końcowym

Regiony platformy Azure, w których znajdują się punkty końcowe, są wyświetlane jako niebieskie kropki na mapie. Jeśli punkt końcowy jest zewnętrzny i nie ma zamapowanego regionu platformy Azure, zostanie wyświetlony na górze mapy. Kliknij dowolny punkt końcowy, aby zobaczyć różne lokalizacje (na podstawie używanego programu rozpoznawania nazw DNS), w którym ruch został skierowany do tego punktu końcowego. Połączenia są wyświetlane jako linia między punktem końcowym i lokalizacją programu rozpoznawania nazw DNS i są kolorowe według reprezentatywnego opóźnienia między tą parą. Ponadto można wyświetlić nazwę punktu końcowego, region platformy Azure, w którym działa, oraz łączną liczbę żądań, które zostały skierowane do niego przez ten profil Traffic Manager.


## <a name="tabular-listing-and-raw-data-download"></a>Wyświetlanie listy tabelarycznej i nieprzetworzonych danych

Widok ruchu dane można wyświetlić w formacie tabelarycznym w Azure Portal. Istnieje wpis dla każdej pary IP/punktu końcowego programu rozpoznawania nazw DNS, który pokazuje adres IP programu rozpoznawania nazw DNS, nazwę i lokalizację geograficzną regionu świadczenia usługi Azure, w którym znajduje się punkt końcowy (jeśli jest dostępny), liczbę żądań skojarzonych z tym programem rozpoznawania nazw DNS do tego punktu końcowego oraz reprezentatywne opóźnienie skojarzone z użytkownikami końcowymi przy użyciu tego systemu DNS (jeśli jest dostępny). Możesz również pobrać Widok ruchu dane jako plik CSV, który może być używany jako część wybranego przepływu pracy analizy.

## <a name="billing"></a>Rozliczenia

Gdy używasz Widok ruchu, opłaty są naliczane na podstawie liczby punktów danych używanych do tworzenia szczegółowych informacji. Obecnie jedynym używanym typem punktu danych są zapytania odebrane względem profilu Traffic Manager. Aby uzyskać więcej informacji na temat cen, odwiedź [stronę z cennikiem Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Często zadawane pytania

* [Co robią Widok ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Jak mogę skorzystać z Widok ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Jak różnią się Widok ruchu od metryk Traffic Manager dostępnych za pośrednictwem usługi Azure monitor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Czy Widok ruchu używać informacji o podsieci klienta mechanizmów EDNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Ile dni danych używa Widok ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Jak Widok ruchu obsługiwać zewnętrzne punkty końcowe?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Czy muszę włączyć Widok ruchu dla każdego profilu w mojej subskrypcji?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Jak mogę wyłączyć Widok ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Jak działa rozliczanie Widok ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak działa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez Traffic Manager
- Dowiedz się, jak [utworzyć profil Traffic Manager](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png