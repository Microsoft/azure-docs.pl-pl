---
title: Pomiary rzeczywistego użytkownika na platformie Azure Traffic Manager
description: W tym wprowadzeniu dowiesz się, jak usługa Azure Traffic Manager Pomiary rzeczywistego użytkownika działa.
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
ms.openlocfilehash: 618f8fff532da0f6ae315ad9e4cda35a289949d1
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183714"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Omówienie pomiarów rzeczywistego użytkownika usługi Traffic Manager

Po skonfigurowaniu profilu Traffic Manager do korzystania z metody routingu wydajności usługa sprawdza, czy żądania zapytań DNS pochodzą z i podejmuje decyzje dotyczące routingu, aby kierować tych podmiotów żądających do regionu platformy Azure, który zapewnia im najmniejsze opóźnienia. Jest to realizowane przy użyciu analizy opóźnienia sieci, która Traffic Manager utrzymywana dla różnych sieci użytkowników końcowych.

Pomiary rzeczywistego użytkownika pozwala mierzyć pomiary opóźnienia sieci w regionach platformy Azure, od aplikacji klienckich używanych przez użytkowników końcowych, a także mieć Traffic Manager uwzględniania tych informacji podczas podejmowania decyzji dotyczących routingu. Korzystając z Pomiary rzeczywistego użytkownika, można zwiększyć dokładność routingu dla żądań pochodzących z tych sieci, w których znajdują się użytkownicy końcowi. 

## <a name="how-real-user-measurements-work"></a>Jak działa Pomiary rzeczywistego użytkownika

Pomiary rzeczywistego użytkownika pracy, ponieważ aplikacje klienckie mierzą opóźnienia w regionach platformy Azure, ponieważ są widoczne z sieci użytkowników końcowych, w których są używane. Jeśli na przykład masz stronę sieci Web, do której użytkownicy uzyskują dostęp w różnych lokalizacjach (na przykład w regionach Ameryki Północnej), możesz użyć Pomiary rzeczywistego użytkownika z metodą routingu wydajności, aby uzyskać dostęp do najlepszego regionu platformy Azure, w którym jest hostowana aplikacja serwera.

Zaczyna od osadzania języka JavaScript dostarczonego przez platformę Azure (z unikatowym kluczem) na stronach sieci Web. Gdy to zrobisz, za każdym razem, gdy użytkownik odwiedzi tę stronę sieci Web, kwerendy języka JavaScript Traffic Manager, aby uzyskać informacje na temat regionów świadczenia usługi Azure, które powinny być mierzone. Usługa zwraca zestaw punktów końcowych do skryptu, który następnie mierzy te regiony po kolei przez pobranie obrazu pojedynczego piksela hostowanego w tych regionach świadczenia usługi Azure i zanotowanie opóźnienia między czasem wysłania żądania a czasem odebrania pierwszego bajtu. Te pomiary są następnie raportowane z powrotem do usługi Traffic Manager.

W miarę upływu czasu zdarza się to wiele razy i między wieloma sieciami, co prowadzi do Traffic Manager uzyskiwania dokładniejszych informacji na temat właściwości opóźnienia sieci, w których znajdują się użytkownicy końcowi. Te informacje zaczynają być uwzględniane w decyzjach dotyczących routingu podejmowanych przez Traffic Manager. W efekcie prowadzi to do zwiększonej dokładności tych decyzji na podstawie wysłanych Pomiary rzeczywistego użytkownika.

Gdy używasz Pomiary rzeczywistego użytkownika, opłaty są naliczane na podstawie liczby pomiarów wysłanych do Traffic Manager. Aby uzyskać więcej informacji na temat cen, odwiedź [stronę z cennikiem Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Często zadawane pytania

* [Jakie korzyści wynikają z używania Pomiary rzeczywistego użytkownika?](./traffic-manager-faqs.md#what-are-the-benefits-of-using-real-user-measurements)

* [Czy mogę używać Pomiary rzeczywistego użytkownika z regionami nienależącymi do platformy Azure?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-non-azure-regions)

* [Która metoda routingu przynosi korzyści z Pomiary rzeczywistego użytkownika?](./traffic-manager-faqs.md#which-routing-method-benefits-from-real-user-measurements)

* [Czy muszę włączyć Pomiary rzeczywistego użytkownika każdy profil osobno?](./traffic-manager-faqs.md#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Jak mogę wyłączyć Pomiary rzeczywistego użytkownika dla mojej subskrypcji?](./traffic-manager-faqs.md#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Czy mogę używać Pomiary rzeczywistego użytkownika z aplikacjami klienckimi innymi niż strony sieci Web?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Ile pomiarów jest wykonywanych za każdym razem, gdy jest renderowana Strona sieci Web z włączonymi Pomiary rzeczywistego użytkownikaami?](./traffic-manager-faqs.md#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Czy istnieje opóźnienie przed uruchomieniem skryptu Pomiary rzeczywistego użytkownika na mojej stronie sieci Web?](./traffic-manager-faqs.md#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Czy mogę używać Pomiary rzeczywistego użytkownika tylko z regionami platformy Azure, które mają być mierzone?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Czy mogę ograniczyć liczbę pomiarów wprowadzonych do określonego numeru?](./traffic-manager-faqs.md#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Czy mogę zobaczyć pomiary wykonywane przez aplikację kliencką w ramach Pomiary rzeczywistego użytkownika?](./traffic-manager-faqs.md#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Czy mogę zmodyfikować skrypt pomiarowy dostarczony przez Traffic Manager?](./traffic-manager-faqs.md#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Czy będzie możliwe, aby inni mogli zobaczyć klucz, z którego korzystam z Pomiary rzeczywistego użytkownika?](./traffic-manager-faqs.md#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Czy inne osoby mogą używać klucza rumu?](./traffic-manager-faqs.md#can-others-abuse-my-rum-key)

* [Czy muszę umieścić miarę JavaScript na wszystkich moich stronach sieci Web?](./traffic-manager-faqs.md#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Czy informacje o użytkownikach końcowych są identyfikowane przez Traffic Manager, jeśli używam Pomiary rzeczywistego użytkownika?](./traffic-manager-faqs.md#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Czy funkcja mierzenia Pomiary rzeczywistego użytkownika musi używać Traffic Manager do routingu?](./traffic-manager-faqs.md#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Czy muszę hostować każdą usługę w regionach platformy Azure, która ma być używana z Pomiary rzeczywistego użytkownika?](./traffic-manager-faqs.md#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Czy moja przepustowość platformy Azure zwiększa się, gdy używam Pomiary rzeczywistego użytkownika?](./traffic-manager-faqs.md#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak używać [pomiary rzeczywistego użytkownika ze stronami sieci Web](traffic-manager-create-rum-web-pages.md)
- Dowiedz się, [jak działa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o usłudze [Mobile Center](/mobile-center/)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez Traffic Manager
- Dowiedz się, jak [utworzyć profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)