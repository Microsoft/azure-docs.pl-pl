---
title: Jak skonfigurować właściwości oferty SaaS w centrum partnerskim firmy Microsoft
description: Dowiedz się, jak skonfigurować właściwości komercyjnej oferty firmy Microsoft w witrynie Marketplace w centrum partnerskim.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 7d90c9b9b2c9aa97083e17d5ab7d20fc6b471658
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94492127"
---
# <a name="how-to-configure-your-saas-offer-properties"></a>Jak skonfigurować właściwości oferty SaaS

W tym artykule opisano sposób konfigurowania właściwości oferty oprogramowanie jako usługa (SaaS) w portalu komercyjnym firmy Microsoft.

Na karcie **Właściwości** zdefiniujesz kategorie i branże, które mają zastosowanie do oferty, wersji aplikacji i umów prawnych. Upewnij się, że podajesz pełne i dokładne szczegółowe informacje o ofercie na tej stronie, aby była ona wyświetlana odpowiednio i oferowana przez właściwy zestaw klientów.

## <a name="select-a-category-for-your-offer"></a>Wybierz kategorię oferty

W obszarze **Kategoria** wybierz co najmniej jedną i maksymalnie dwie kategorie na potrzeby grupowania oferty w odpowiednie obszary wyszukiwania w portalu Marketplace. W oparciu o wybrane kategorie określisz, w jakich sklepach online ma zostać wystawiona oferta: w witrynie Azure Marketplace, Microsoft AppSource lub obu.

## <a name="select-industries-optional"></a>Wybierz branże (opcjonalnie)

W obszarze **branże** możesz wybrać maksymalnie dwie branże i maksymalnie dwie branże podrzędne (nazywane również pionowymi) dla każdej branży. Te branże są używane do wyświetlania oferty w przypadku, gdy klienci filtrują wyszukiwanie w branżach i gałęziach podrzędnych w sklepie online.

> [!NOTE]
> Jeśli Twoja oferta nie jest zależna od branży, pozostaw tę sekcję pustą.

1. W obszarze **branże** wybierz łącze **+ Branża** .
1. Wybierz branżę z listy **branżowej** .
1. Wybierz co najmniej jeden i maksymalnie dwa pionowe wartości z listy **branż podrzędnych** . Użyj klawisza CTRL, aby wybrać wiele branż podrzędnych.
1. Aby dodać kolejną branżę i pionowo, wybierz pozycję **+ branże** , a następnie powtórz kroki od 1 do 3.

## <a name="specify-an-app-version-optional"></a>Określ wersję aplikacji (opcjonalnie)

 W polu **wersja aplikacji** wprowadź numer wersji. Wersja aplikacji jest używana w witrynie AppSource Marketplace do identyfikowania numeru wersji oferty.

## <a name="provide-terms-and-conditions"></a>Podaj warunki i postanowienia

W obszarze **prawne** Podaj warunki i postanowienia dotyczące oferty. Dostępne są dwie opcje:

- [Użyj standardowej umowy z opcjonalnymi zmianami](#use-the-standard-contract)
- [Korzystanie z własnych warunków i postanowień](#use-your-own-terms-and-conditions)

Aby dowiedzieć się więcej o standardowej umowie i opcjonalnych zmianach, zobacz artykuł [Standardowy dla portalu komercyjnego firmy Microsoft](standard-contract.md). Możesz pobrać plik PDF [kontraktu standardowego](https://go.microsoft.com/fwlink/?linkid=2041178) (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

### <a name="use-the-standard-contract"></a>Korzystanie z kontraktu standardowego

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardowy kontrakt, którego możesz użyć dla ofert w portalu komercyjnym. W przypadku oferowania oprogramowania zgodnie z umową standardowa klienci muszą tylko raz odczytywać i akceptować ją i nie muszą tworzyć niestandardowych warunków i postanowień.

1. Zaznacz pole wyboru **Użyj standardowego kontraktu dla portalu komercyjnego firmy Microsoft** .

   ![Ilustruje pole wyboru Użyj standardowego kontraktu dla komercyjnej witryny Marketplace firmy Microsoft.](partner-center-portal/media/use-standard-contract.png)
1. W oknie dialogowym **potwierdzenia** wybierz pozycję **Zaakceptuj**. Może być konieczne przewinięcie w górę, aby je zobaczyć.
1. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

   > [!NOTE]
   > Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace nie można użyć własnych niestandardowych warunków i postanowień. Oferuj swoje rozwiązanie w ramach standardowej umowy z opcjonalnymi zmianami lub w ramach własnych warunków i postanowień.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Dodaj zmiany do standardowej umowy (opcjonalnie)

Dostępne są dwa rodzaje zmian: *uniwersalne* i *niestandardowe*.

#### <a name="add-universal-amendment-terms"></a>Dodaj postanowienia dotyczące uniwersalnej zmiany

W obszarze **postanowienia dotyczące zmiany uniwersalnej do standardowej umowy Marketplace firmy Microsoft** wprowadź swoje uniwersalne postanowienia dotyczące zmiany. W tym polu można wprowadzić nieograniczoną liczbę znaków. Te warunki są wyświetlane klientom w AppSource, w witrynie Azure Marketplace i/lub Azure Portal podczas odnajdywania i przepływu zakupów.

#### <a name="add-one-or-more-custom-amendments"></a>Dodawanie co najmniej jednej zmiany niestandardowej

1. W obszarze **niestandardowe zmiany warunki do standardowej umowy dotyczącej komercyjnej witryny Marketplace firmy Microsoft** wybierz łącze **Dodaj termin poprawki niestandardowej (maks. 10)** .
1. W polu **niestandardowe warunki zmiany** wprowadź warunki umowy licencyjnej.
1. W polu **Identyfikator dzierżawy** wprowadź identyfikator dzierżawy. Tylko klienci powiązani z identyfikatorami dzierżawy określonymi dla tych terminów niestandardowych będą widzieć je w przepływie zakupu oferty w Azure Portal.
   > [!TIP]
   > Identyfikator dzierżawy identyfikuje Twojego klienta na platformie Azure. Możesz polecić klienta dla tego identyfikatora i znaleźć go, przechodząc do [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Właściwości**. Wartość identyfikatora katalogu jest identyfikator dzierżawy (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ). Możesz również wyszukać identyfikator dzierżawy organizacji klienta przy użyciu adresu URL nazwy domeny, co [to jest mój Microsoft Azure i identyfikator dzierżawy pakietu Office 365?](https://www.whatismytenantid.com/).
1. W polu **Opis** opcjonalnie wprowadź przyjazny opis identyfikatora dzierżawy. Ten opis pomaga zidentyfikować klienta, którego celem jest zmiana.
1. Aby dodać inny identyfikator dzierżawy, wybierz łącze **Dodaj identyfikator dzierżawy klienta** i powtórz kroki 3 i 4. Możesz dodać maksymalnie 20 identyfikatorów dzierżawców.
1. Aby dodać kolejny termin zmiany, powtórz kroki od 1 do 5. Możesz dostarczyć do dziesięciu postanowień w ramach oferty. 
2. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="use-your-own-terms-and-conditions"></a>Korzystanie z własnych warunków i postanowień

Możesz określić własne warunki i postanowienia zamiast standardowej umowy. Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

1. W obszarze **prawne** upewnij się, że pole wyboru **Użyj kontraktu standardowego dla komercyjnego portalu firmy Microsoft** jest wyczyszczone.
1. W polu **warunki i** postanowienia wprowadź do 10 000 znaków tekstu.
1. Wybierz opcję **Zapisz wersję roboczą** przed przejściem do następnej karty, na **liście oferty**.

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować szczegóły aukcji oferty SaaS](create-new-saas-offer-listing.md)
