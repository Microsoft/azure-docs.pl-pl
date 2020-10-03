---
title: Komercyjne często zadawane pytania dotyczące analizy portalu Marketplace & terminologia — centrum partnerskie
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące komercyjnej analizy portalu Marketplace w centrum partnerskim. Ten artykuł zawiera słownik danych dla terminologii dotyczącej analizy.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: aff26d3b491896aa9a1b1fee93d39a5ec93d730c
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665145"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Terminologia komercyjna dotycząca analizy Marketplace i często zadawane pytania

Ten artykuł dotyczy często zadawanych pytań dotyczących komunikatów analitycznych w centrum partnerskim, a także zawiera słownik terminologii dotyczącej analizy.

## <a name="common-questions"></a>Często zadawane pytania

**Nie mogę wyświetlić danych analitycznych w centrum partnerskim. Gdy uzyskuję dostęp do tych stron, zobaczysz poniższy komunikat. Dlaczego to jest?**

![Nie ma jeszcze danych dla ofert](./media/analytics-faq-no-data.png)

Dlaczego może być wyświetlany następujący komunikat:

- Obecnie nie istnieją żadne pozyskiwania dla opublikowanych ofert w portalu Marketplace. Może to oznaczać, że oferty znajdują się na żywo w witrynie Marketplace i uzyskują widoki od klientów na stronach wyświetlania produktu, ale klienci nie zostali jeszcze zakupili i wdrażali.
- Publikowanie oferty może być w toku i nie jest jeszcze na żywo. Klienci mogą uzyskać tylko oferty na żywo. Aby sprawdzić stan ofert, zobacz Omówienie na [pulpicie nawigacyjnym analizy](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Aby uzyskać więcej informacji, zobacz [pulpit nawigacyjny podsumowania w portalu komercyjnej analizy Marketplace](./summary-dashboard.md).
- Oferty mogą być wyświetlane jako **kontakt ze mną**, które są ofertami tylko do wyświetlania i nie mogą zostać zakupione przez klientów w portalu Marketplace. Chociaż te oferty generują potencjalnych klientów i są Ci udostępniane, nie są tworzone zamówienia dla tych ofert, ponieważ nie można ich zakupić. Aby sprawdzić typ aukcji oferty, przejdź do strony konfiguracja.

**Wiem, że mam dane analityczne, ale pojawia się następujący komunikat:**

![Brak danych dla podanego zakresu dat](./media/analytics-faq-data-range.png)

Jeśli otrzymujesz tę wiadomość, oznacza to, że masz dane analityczne, ale nie ma danych dla wybranego zakresu dat. Wybierz inny zakres dat lub niestandardowy zakres dat, aby wyświetlić dowolne dane od 2010. Aby uzyskać więcej informacji, zobacz sekcję zakres dat [podsumowania pulpitu nawigacyjnego w portalu Marketplace](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Słownik terminów dotyczących danych

| Nazwa atrybutu | Raporty | Definicja|
|---|---|---|
| Typ licencji platformy Azure | Klient, zamówienie | Typ umowy licencyjnej używanej przez klientów do kupowania platformy Azure. Znany również jako kanał |
| Typ licencji platformy Azure: dostawca rozwiązań w chmurze | Klient, zamówienie | Klient końcowy nabywa platformę Azure i ofertę portalu Marketplace za pomocą swojego dostawcy rozwiązań w chmurze, który działa jako odsprzedawca.|
| Typ licencji platformy Azure: Enterprise | Klient, zamówienie | Klient końcowy nabywa platformę Azure i ofertę w portalu Marketplace za pomocą Umowa Enterprise podpisanej bezpośrednio w firmie Microsoft.|
| Typ licencji platformy Azure: przedsiębiorstwo przez odsprzedawcę  | Klient, zamówienie | Klient końcowy nabywa platformę Azure i ofertę portalu Marketplace przez odsprzedawcę, który ułatwia ich Umowa Enterprise firmie Microsoft.|
| Typ licencji platformy Azure: płatność zgodnie z rzeczywistym użyciem| Klient, zamówienie | Klient końcowy nabywa platformę Azure i ofertę Marketplace w ramach umowy "płatność zgodnie z rzeczywistym użyciem" podpisanej bezpośrednio z firmą Microsoft.|
| Identyfikator konta rozliczeniowego| Klient, zamówienie, użycie| Identyfikator konta, na którym jest generowane rozliczanie. Mapuj **Identyfikator konta rozliczeniowego** na wartość **CustomerID** , aby połączyć Raport transakcji wypłaty z raportami klientów, zamówień i użycia.|
| Nazwa wystąpienia chmury| Zamówienie| Microsoft Cloud, w którym wystąpiło wdrożenie maszyny wirtualnej.|
| Nazwa wystąpienia chmury: globalna Azure| Zamówienie| Publiczna globalna chmura firmy Microsoft.|
| Nazwa wystąpienia chmury: Azure Government | Zamówienie| Specyficzne dla instytucji rządowe chmury firmy Microsoft dla jednego z następujących rządów: Chiny, Niemcy lub Stany Zjednoczone Ameryki.| |
| Miasto klienta| Klient| Nazwa miasta podana przez klienta. Miasto może być inne niż miasto w subskrypcji platformy Azure klienta.|
| Język komunikacji klienta  | Klient| Język preferowany przez klienta do komunikacji.|
| Nazwa firmy klienta | Klient, zamówienie | Nazwa firmy podana przez klienta. Nazwa może różnić się od miasta w subskrypcji platformy Azure klienta.|
| Kraj/region klienta | Klient, zamówienie | Nazwa kraju/regionu podana przez klienta. Kraj/region może być inny niż kraj/region w subskrypcji platformy Azure klienta.|
| Adres E-mail klienta| Klient| Adres e-mail dostarczony przez klienta końcowego. Adres e-mail może różnić się od adresu e-mail w subskrypcji platformy Azure klienta.|
| Imię klienta| Klient| Nazwa podana przez klienta. Nazwa może różnić się od nazwy podanej w subskrypcji platformy Azure klienta.|
| Customer ID | Klient, zamówienie | Unikatowy identyfikator przypisany do klienta. Klient może mieć zero lub więcej subskrypcji portalu Azure Marketplace.|
| Kod pocztowy klienta  | Klient| Kod pocztowy dostarczony przez klienta. Kod może być inny niż kod pocztowy podany w subskrypcji platformy Azure klienta.|
| Stan klienta| Klient| Stan (adres) dostarczony przez klienta. Stan może być inny niż stan podany w subskrypcji platformy Azure klienta.|
| Data pozyskania| Klient| Pierwszy dzień zakupionej oferty przez klienta.|
| Data utraty| Klient| Ostatnia data anulowania ostatniego zakupionej oferty przez klienta.|
| Jest nowym klientem  | Zamówienie| Wartość będzie identyfikować nowego klienta przejmującego jedną lub większą liczbę ofert po raz pierwszy (lub nie). Wartość będzie równa "yes", jeśli w ramach tego samego miesiąca kalendarzowego dla "Data pozyskania". Wartość będzie równa "No", jeśli klient kupił dowolne oferty przed zgłoszonym miesiącem kalendarzowym. |
| Jest jednostką SKU wersji zapoznawczej| Zamówienie| Wartość będzie informować o tym, czy jest oznaczona jako wersja zapoznawcza. Wartość będzie równa "yes", jeśli jednostka SKU została odpowiednio oznaczona, a tylko subskrypcje platformy Azure autoryzowane przez użytkownika, można wdrożyć i korzystać z tego obrazu. Wartość będzie równa "No", jeśli jednostka SKU nie została zidentyfikowana jako "wersja zapoznawcza". |
| Czy kontakt promocyjny jest wybierany| Klient| Ta wartość informuje o tym, czy klient aktywnie zabrał w celu skontaktowania się z wydawcami. W tej chwili nie prezentujemy opcji klientom, dlatego w całej tablicy podano wartość "No". Po wdrożeniu tej funkcji rozpocznie się aktualizowanie.|
| Typ licencji Marketplace| Zamówienie| Metoda rozliczeń oferty portalu Marketplace.|
| Typ licencji Marketplace: rozliczane za pomocą platformy Azure| Zamówienie| Firma Microsoft jest Twoim agentem dla tej oferty w witrynie Marketplace i wekslom klientów w Twoim imieniu. (Karta kredytowa PAYG lub faktura w przedsiębiorstwie)|
| Typ licencji Marketplace: korzystanie z własnej licencji | Zamówienie| Maszyna wirtualna wymaga klucza licencji dostarczonego przez klienta do wdrożenia. Firma Microsoft nie rozliczy klientów za korzystanie z oferty w ten sposób w portalu Marketplace.|
| Typ licencji Marketplace: bezpłatnie| Zamówienie| Oferta jest konfigurowana bezpłatnie dla wszystkich użytkowników. Firma Microsoft nie rozliczy klientów za użycie tej oferty.|
| Typ licencji Marketplace: Firma Microsoft jako odsprzedawca  | Zamówienie| Firma Microsoft jest odsprzedawcą dla tej oferty portalu Marketplace.|
| Identyfikator subskrypcji witryny Marketplace | Klient, zamówienie | Unikatowy identyfikator skojarzony z subskrypcją platformy Azure używany przez klienta do zakupu oferty portalu Marketplace. Identyfikator był dawniej identyfikatorem GUID subskrypcji platformy Azure.|
| Offer Name  | Zamówienie| Nazwa oferty portalu Marketplace.|
| Typ oferty  | Zamówienie| Typ oferty Microsoft Marketplace.|
| Typ oferty: aplikacja zarządzana  | Zamówienie | Użyj usługi Azure App: Managed App — typ oferty, jeśli są wymagane następujące warunki: wdrażasz rozwiązanie oparte na subskrypcji dla klienta przy użyciu maszyny wirtualnej lub całego rozwiązania opartego na IaaS. Ty lub Twój klient wymaga, aby rozwiązanie było zarządzane przez partnera. |
| Typ oferty: aplikacja platformy Azure| Zamówienie | Użyj typu oferty szablonu rozwiązania aplikacji platformy Azure, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza prostą maszyną wirtualną.|
| Typ oferty: usługa konsultingowa| Zamówienie| Usługi doradcze w witrynie Azure Marketplace umożliwiają łączenie klientów z usługami w celu wspierania i zwiększania użycia platformy Azure.|
| Typ oferty: kontener | Zamówienie| Użyj typu oferty kontenera, gdy rozwiązanie jest obrazem kontenera Docker, który został zainicjowany jako usługa kontenera platformy Azure oparta na Kubernetes.|
| Typ oferty: Dynamics 365 Business Central| Zamówienie| Użyj tego typu oferty, jeśli Twoje rozwiązanie jest zintegrowane z usługą Dynamics 365 dla finansów i operacji|
| Typ oferty: Dynamics 365 dla zaangażowania klienta | Zamówienie| Użyj tego typu oferty, jeśli Twoje rozwiązanie jest zintegrowane z usługą Dynamics 365 na potrzeby zaangażowania klientów.|
| Typ oferty: moduł IoT Edge | Zamówienie| Moduły Azure IoT Edge są najmniejszymi jednostkami obliczeniowymi zarządzanymi przez IoT Edge i mogą zawierać usługi firmy Microsoft (takie jak Azure Stream Analytics), usługi innych firm lub własny kod specyficzny dla rozwiązania. |
| Typ oferty: aplikacja Power BI | Zamówienie| Użyj typu oferty aplikacji Power BI podczas wdrażania aplikacji zintegrowanej z Power BI.|
| Typ oferty: aplikacja SaaS| Zamówienie| Użyj typu oferty aplikacji SaaS, aby umożliwić klientowi kupowanie rozwiązań technicznych opartych na SaaS, w ramach subskrypcji.||
| Typ oferty: maszyna wirtualna | Zamówienie | Podczas wdrażania urządzenia wirtualnego w ramach subskrypcji skojarzonej z klientem Użyj typu oferty maszyny wirtualnej.|
| Typ oferty: rozszerzenie Visual Studio Marketplace  | Zamówienie| Typ oferty dostępny wcześniej dla deweloperów rozszerzenia usługi Azure DevOps. Przechodzenie do przodu deweloperów rozszerzeń platformy Azure DevOps może sprzedawać swoje rozszerzenia bezpośrednio klientom. Oferty rozszerzeń można skonfigurować jako płatne lub w tym okres próbny. |
| Data anulowania zamówienia| Zamówienie| Data anulowania zamówienia witryny Marketplace.|
| Identyfikator zamówienia| Zamówienie| Unikatowy identyfikator zamówienia klienta usługi portalu Marketplace. Oferty oparte na użyciu maszyny wirtualnej nie są skojarzone z kolejnością.|
| Zamówienie daty zakupu| Zamówienie| Data utworzenia zamówienia witryny Marketplace.|
| Stan zamówienia| Zamówienie| Stan zamówienia witryny Marketplace w momencie ostatniego odświeżenia danych.|
| Stan zamówienia: aktywny  | Zamówienie| Klient kupił zamówienie i nie anulował jego zamówienia.|
| Stan zamówienia: anulowane | Zamówienie| Klient kupił wcześniej zamówienie, a następnie anulował jego zamówienie.|
| Adres E-mail dostawcy| Klient| Adres e-mail dostawcy, który jest powiązany z relacją między firmą Microsoft a klientem końcowym. Jeśli klient jest przedsiębiorstwem do odsprzedawcy, będzie to odsprzedawca. Jeśli jest używany dostawca rozwiązań w chmurze (CSP), będzie to dostawca usług kryptograficznych.|
| Nazwa dostawcy| Klient| Nazwa dostawcy występującego w relacji między firmą Microsoft a klientem końcowym. Jeśli klient jest przedsiębiorstwem do odsprzedawcy, będzie to odsprzedawca. Jeśli jest używany dostawca rozwiązań w chmurze (CSP), będzie to dostawca usług kryptograficznych.|
| Jednostka SKU| Zamówienie| Nazwa jednostki SKU (jest nazywana planem) zgodnie z definicją podczas publikowania. Oferta może mieć wiele jednostek SKU (planów), ale każdy z nich może być skojarzony tylko z jedną ofertą.|
| Data zakończenia okresu próbnego| Zamówienie| Data zakończenia okresu próbnego dla tego zamówienia lub zostanie ona zakończona.|
|||

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analitycznych dostępnych na rynku komercyjnym centrum partnerskiego, zobacz [Analiza portalu komercyjnego w centrum partnerskim](./analytics.md).
- W przypadku grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla oferty, zobacz [pulpit nawigacyjny podsumowania w portalu komercyjnej analizy Marketplace](./summary-dashboard.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [Zamówienie pulpitu nawigacyjnego w portalu komercyjnej analizy Marketplace](./orders-dashboard.md).
- W przypadku maszyn wirtualnych (VM) są dostępne metryki dotyczące użycia i taryfowego rozliczania, zobacz [pulpit nawigacyjny użycia w portalu komercyjnej analizy Marketplace](./usage-dashboard.md).
- Aby uzyskać szczegółowe informacje na temat klientów, w tym trendów wzrostu, zobacz [pulpit nawigacyjny klienta w komercyjnej analizie portalu Marketplace](./customer-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i AppSource, zobacz [pulpit nawigacyjny oceny i przeglądów w portalu komercyjnej analizy Marketplace](./ratings-reviews.md).
