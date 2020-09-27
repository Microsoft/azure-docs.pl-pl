---
title: Omówienie tworzenia aplikacji Power BI — Microsoft AppSource
description: W tym artykule opisano ogólne kroki publikowania aplikacji Power BI w programie Microsoft AppSource. Dostępne są również wymagania techniczne i biznesowe, które aplikacja Power BI musi spełnić, aby można było je opublikować w portalu komercyjnym.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 04/02/2020
ms.openlocfilehash: 2f34723bec6dc2212f8897eca849e6535d737fc8
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397423"
---
# <a name="power-bi-app-creation-overview"></a>Omówienie tworzenia aplikacji Power BI

W tym artykule opisano sposób publikowania aplikacji Power BI w programie Microsoft [AppSource](https://appsource.microsoft.com/). Power BI dostosowywalne pakiety aplikacji, w tym zestawy danych, raporty i pulpity nawigacyjne. Następnie możesz użyć aplikacji z innymi Power BI platformami za pomocą AppSource, wykonać korekty i dostosowania dozwolone przez dewelopera, a następnie połączyć je ze swoimi danymi.

## <a name="publishing-benefits"></a>Korzyści z publikowania

Zalety publikowania na komercyjnej witrynie Marketplace:

- Promuj swoją firmę przy użyciu marki Microsoft.
- Może dotrzeć do ponad 100 000 000 Microsoft 365 i użytkowników usługi Dynamics 365 w AppSource i więcej niż 200 000 organizacje w witrynie Azure Marketplace.
- Odbieraj wysokiej jakości potencjalni klienci z tych rynków.
- Twoje usługi są promowane przez zespoły pól i telesprzedaży firmy Microsoft.

## <a name="overview"></a>Omówienie

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Przegląd czynności związanych z publikowaniem aplikacji Power BI" border="false":::

Oto podstawowe kroki publikowania:

1. Utwórz aplikację w Power BI. Otrzymasz link instalacji pakietu, który jest głównym zasobem technicznym oferty. Wyślij pakiet testowy do środowiska przedprodukcyjnego przed utworzeniem oferty w centrum partnerskim. Aby uzyskać szczegółowe informacje, zobacz [co to są Power BI Apps?](https://docs.microsoft.com/power-bi/service-template-apps-overview).
2. Dodaj materiały marketingowe, takie jak Oficjalna nazwa, opis i logo.
3. Dołącz do oferty dokumenty prawne i pomoc techniczna, takie jak warunki użytkowania, zasady ochrony prywatności, zasady pomocy technicznej i pomoc użytkownika.
4. Utwórz ofertę — Użyj Centrum partnerskiego, aby edytować szczegóły, w tym opis oferty, materiały marketingowe, informacje prawne, informacje o pomocy technicznej i specyfikacje zasobów.
5. Prześlij ją do publikacji.
6. Monitoruj proces w centrum partnerskim, w którym zespół AppSource dołączania, sprawdza poprawność i poświadcza swoją aplikację.
7. Po uzyskaniu certyfikatu zapoznaj się z aplikacją w środowisku testowym i zwolnij ją. Spowoduje to wyświetlenie listy w witrynie AppSource ("trafia na żywo").
8. W Power BI Wyślij pakiet do środowiska produkcyjnego. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie wydaniem aplikacji Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przejrzyj poniższe linki, które zawierają szablony, porady i przykłady.

- [Tworzenie aplikacji Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Porady dotyczące tworzenia aplikacji Power BI](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Przykłady](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Wymagania

Aby można było opublikować ją w komercyjnej witrynie Marketplace, oferta Power BI aplikacji musi spełniać następujące wymagania techniczne i biznesowe.

### <a name="technical-requirements"></a>Wymagania techniczne

Głównym zasobem technicznym, którego potrzebujesz, jest [aplikacja Power BI](https://go.microsoft.com/fwlink/?linkid=2028636). To jest kolekcja podstawowych zestawów danych, raportów lub pulpitów nawigacyjnych. Obejmuje to również opcjonalne połączone usługi i osadzone zestawy danych, znane wcześniej jako [pakiet zawartości](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction). Aby uzyskać więcej informacji na temat opracowywania tego typu aplikacji, zobacz artykuł [co to są Power BI Apps?](https://go.microsoft.com/fwlink/?linkid=2028636).

#### <a name="get-an-installation-web-address"></a>Pobierz adres internetowy instalacji

Aplikację Power BI można utworzyć tylko w środowisku [Power BI](https://powerbi.microsoft.com/) .

1. Zaloguj się przy użyciu [licencji Power BI Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Utwórz i przetestuj swoją aplikację w Power BI.
3. Po otrzymaniu adresu internetowego instalacji aplikacji należy dodać go do strony **konfiguracji technicznej** w centrum partnerskim.

Po utworzeniu i przetestowaniu aplikacji w Power BI Zapisz adres internetowy instalacji aplikacji, ponieważ będzie on potrzebny do [utworzenia oferty aplikacji Power BI](create-power-bi-app-offer.md).

### <a name="business-requirements"></a>Wymagania biznesowe

Wymagania biznesowe obejmują proceduralne, umowne i prawne zobowiązania. Należy:

- Być zarejestrowanym komercyjnym wydawcą portalu Marketplace. Jeśli nie masz rejestracji, postępuj zgodnie z instrukcjami w temacie [Tworzenie komercyjnego konta witryny Marketplace w centrum partnerskim](create-account.md).
- Podaj zawartość, która spełnia kryteria oferty do wystawienia w witrynie AppSource. Aby uzyskać więcej informacji, zobacz temat [czy aplikacja ma być wyświetlana w witrynie AppSource? Oto jak to zrobić](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Zaakceptuj [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement)i postępuj zgodnie z tymi zasadami.

## <a name="next-step"></a>Następny krok

- [Tworzenie oferty aplikacji Power BI w centrum partnerskim](create-power-bi-app-offer.md)
