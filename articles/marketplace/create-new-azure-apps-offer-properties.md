---
title: Jak skonfigurować właściwości oferty aplikacji platformy Azure
description: Dowiedz się, jak skonfigurować właściwości oferty aplikacji platformy Azure w centrum partnerskim.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 6f92c138a02c9dbdc1d22ca04c733cfbee69dcd0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488523"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>Jak skonfigurować właściwości oferty aplikacji platformy Azure

W tym artykule opisano sposób konfigurowania właściwości oferty aplikacji platformy Azure w portalu komercyjnym.

Na stronie **Właściwości** zdefiniujesz kategorie mające zastosowanie do oferty oraz kontrakty prawne. Upewnij się, że podajesz pełne i dokładne szczegółowe informacje o ofercie na tej stronie, aby była ona wyświetlana odpowiednio i oferowana przez właściwy zestaw klientów.

## <a name="select-a-category-for-your-offer"></a>Wybierz kategorię oferty

W obszarze **Kategorie** wybierz łącze **Kategorie** , a następnie wybierz co najmniej jedną i maksymalnie dwie kategorie grupowania oferty w odpowiednie obszary wyszukiwania komercyjnego portalu Marketplace. Wybierz maksymalnie dwie podkategorie dla każdej kategorii podstawowej i pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

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

Dostępne są dwa rodzaje zmian: _uniwersalne_ i _niestandardowe_.

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
1. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="use-your-own-terms-and-conditions"></a>Korzystanie z własnych warunków i postanowień

Możesz określić własne warunki i postanowienia zamiast standardowej umowy. Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

1. W obszarze **prawne** upewnij się, że pole wyboru **Użyj kontraktu standardowego dla komercyjnego portalu firmy Microsoft** jest wyczyszczone.
1. W polu **warunki i** postanowienia wprowadź do 10 000 znaków tekstu.
1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: Lista oferty.

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować szczegóły listy oferty aplikacji platformy Azure](create-new-azure-apps-offer-listing.md)
