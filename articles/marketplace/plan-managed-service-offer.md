---
title: Planowanie oferty usługi zarządzanej dla portalu komercyjnego firmy Microsoft
description: Jak zaplanować nową ofertę usługi zarządzanej dla portalu Azure Marketplace przy użyciu komercyjnego programu w witrynie Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371944"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Planowanie oferty usługi zarządzanej dla portalu komercyjnego firmy Microsoft

W tym artykule wprowadzono wymagania dotyczące publikowania oferty usługi zarządzanej w portalu komercyjnym firmy Microsoft za pomocą Centrum partnerskiego.

Usługi zarządzane to oferty w portalu Azure Marketplace, które umożliwiają zarządzanie wieloma dzierżawcami i wielodostępną usługą Azure Lighthouse. Aby dowiedzieć się więcej, zobacz [co to jest usługa Azure Lighthouse?](../lighthouse/overview.md) Gdy klient kupuje ofertę usługi zarządzanej, może delegować co najmniej jedną subskrypcję lub grupę zasobów. Następnie możesz korzystać z tych zasobów, korzystając z funkcji [zarządzania zasobami delegowanymi przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md) Lighthouse.

## <a name="eligibility-requirements"></a>Wymagania dotyczące kwalifikowania się

Aby opublikować ofertę usługi zarządzanej, należy uzyskać kompetencje firmy Microsoft dotyczące złota lub srebra na platformie chmurowej. Ta kompetencja pokazuje Twoją wiedzę dla klientów. Aby uzyskać więcej informacji, zobacz [Microsoft Partner Network kompetencji](https://partner.microsoft.com/membership/competencies).

Oferty muszą spełniać wszystkie odpowiednie [komercyjne zasady certyfikacji portalu Marketplace](/legal/marketplace/certification-policies) , które mają zostać opublikowane w portalu Azure Marketplace.

## <a name="customer-leads"></a>Potencjalni klienci

Aby zbierać informacje o klientach, należy połączyć swoją ofertę z systemem zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnienie swoich informacji. Te szczegóły klienta, wraz z nazwą oferty, IDENTYFIKATORem i sklepem online, gdzie znalazły ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Komercyjna witryna Marketplace obsługuje różne rodzaje systemów CRM, a także opcję używania tabeli platformy Azure lub konfigurowania punktu końcowego HTTPS przy użyciu automatyzacji.

Połączenie programu CRM można dodać lub zmodyfikować w dowolnym momencie podczas tworzenia oferty lub po niej. Aby uzyskać szczegółowe wskazówki, zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Umowy prawne

Na stronie właściwości Centrum partnerskiego zostanie wyświetlony monit o podanie **warunków i** postanowień związanych z korzystaniem z oferty. Możesz wprowadzić swoje warunki bezpośrednio w centrum partnerskim lub podać adres URL, pod którym można je znaleźć. Przed zakupem oferty klienci będą musieli zaakceptować te warunki i postanowienia.

## <a name="offer-listing-details"></a>Szczegóły listy ofert

Po utworzeniu oferty usługi zarządzanej w centrum partnerskim będziesz wprowadzać tekst, obrazy, dokumenty i inne szczegóły oferty. Oto, co klienci zobaczą, gdy odkryją ofertę w witrynie Azure Marketplace. Zobacz poniższy przykład:

:::image type="content" source="media/example-managed-service.png" alt-text="Ilustruje, w jaki sposób oferta usługi zarządzanej jest wyświetlana w witrynie Azure Marketplace.":::

**Opisy połączeń**

1. Logo
1. Nazwa
1. Krótki opis
1. Kategorie
1. Umowy prawne i zasady zachowania poufności informacji
1. Opis
1. Zrzuty ekranu/wideo
1. Przydatne łącza

Oto przykład sposobu wyświetlania listy ofert w Azure Portal:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Azure Portal.":::

**Opisy połączeń**

1. Nazwa
2. Opis
3. Przydatne łącza
4. Zrzuty ekranu/wideo

> [!NOTE]
> Jeśli oferta jest w innym języku niż angielski, Lista ofert może znajdować się w tym języku, ale opis musi zaczynać lub kończyć się frazą w języku angielskim "Ta usługa jest dostępna w &lt; języku zawartości oferty>". Możesz również dostarczyć dokumenty pomocnicze w języku innym niż ten, który jest używany w szczegółach listy oferty.

Aby ułatwić szybkie tworzenie oferty, przygotuj niektóre z tych elementów przed czasem. Poniższe elementy są wymagane, o ile nie zaznaczono inaczej.

**Nazwa**: zostanie ona wyświetlona jako tytuł oferty na stronie komercyjnej Marketplace. Nazwa może być znakiem towarowym. Nie może zawierać znaków emoji (chyba że są to symbole towarowe i praw autorskich) i muszą być ograniczone do 50 znaków.

**Podsumowanie wyników wyszukiwania**: opisz cel lub cel oferty w 100 lub mniejszej liczbie znaków. To podsumowanie służy do wyświetlania wyników wyszukiwania w komercyjnym portalu Marketplace. Nie powinna być identyczna z tytułem. Rozważ uwzględnienie najpopularniejszych słów kluczowych aparatu wyszukiwania.

**Krótki opis**: Podaj krótki opis oferty (do 256 znaków). Zostanie ona wyświetlona na liście ofert w Azure Portal.

**Opis**: opisz swoją ofertę w 3 000 znakach lub mniej. Ten opis zostanie wyświetlony na liście komercyjne witryny Marketplace. Rozważ uwzględnienie propozycji wartości, najważniejszych korzyści, kategorii lub branżowych, a także wszelkich niezbędnych informacji.

Oto kilka porad dotyczących pisania opisu:

* Jasno opisz wartość oferty w pierwszych kilku zdaniach, w tym:
    * Typ użytkownika, który przynosi korzyści z oferty.
    * Czego potrzebują klienci lub wystawiają adresy oferty.
* Należy pamiętać, że pierwsze niektóre zdania mogą być wyświetlane w wynikach wyszukiwania.
* Używaj słownictwa specyficznego dla branży.

Możesz użyć tagów HTML do formatowania opisu. Aby uzyskać informacje na temat formatowania HTML, zobacz [tagi HTML obsługiwane w opisach oferty komercyjnej witryny Marketplace](./supported-html-tags.md).

**Link zasad ochrony prywatności**: Podaj adres URL zasad ochrony prywatności, hostowanych w Twojej witrynie. Użytkownik jest odpowiedzialny za zapewnienie zgodności oferty z przepisami i przepisami dotyczącymi ochrony prywatności oraz podawanie prawidłowych zasad zachowania poufności informacji.

**Przydatne linki** (opcjonalnie): Przekaż uzupełniające dokumenty online o swojej ofercie.

**Informacje kontaktowe**: Podaj imię i nazwisko, adres e-mail i numer telefonu dwóch osób w firmie (Możesz być jednym z nich): kontakt z pomocą techniczną i kontakt inżynieryjny. Będziemy używać tych informacji do komunikowania się z Twoją ofertą. Te informacje nie są widoczne dla klientów, ale mogą być udostępniane partnerom dostawcy rozwiązań w chmurze (CSP)

**Adresy URL pomocy technicznej** (opcjonalnie): Jeśli są obsługiwane witryny sieci Web dla klientów globalnych platformy Azure i/lub Azure Government klientów, podaj te adresy URL.

**Multimedia dla portalu Marketplace — logo**: podaj plik PNG dla logo o dużej wielkości oferty. Centrum partnerskie będzie używać go do tworzenia średnich i małych logo. Opcjonalnie można zastąpić te logo innym obrazem później.

* Duże logo (od 216 x 216 do 350 x 350 px) pojawia się na liście ofert w witrynie Azure Marketplace.
* Średnie logo (90 x 90 pikseli) jest wyświetlana podczas tworzenia nowego zasobu.
* Małe logo (48 x 48 pikseli) jest używane w wynikach wyszukiwania w portalu Azure Marketplace.

Postępuj zgodnie z poniższymi wskazówkami dotyczącymi logo:

* Upewnij się, że obraz nie jest rozciągany.
* Projekt platformy Azure ma prostą paletę kolorów. Ogranicz liczbę podstawowych i pomocniczych kolorów w logo.
* Kolory Azure Portal są białe i czarne. Nie używaj ich jako tła w logo. Zalecamy proste kolory podstawowe, które sprawiają, że logo zostanie uwidocznione.
* Jeśli używasz przezroczystego tła, upewnij się, że logo i tekst nie są białe, czarne ani niebieskie.
* Wygląd i działanie logo powinny być płaskie. Unikaj gradientów. Nie umieszczaj tekstu w logo, nawet nazwy firmy ani marki.

**Multimedia w portalu Marketplace — zrzuty ekranu** (opcjonalnie): Dodaj do pięciu obrazów, które pokazują, jak działa Twoja oferta. Wszystkie obrazy muszą mieć 1280 x 720 pikseli w rozmiarze i w. Format PNG.

**Multimedia dla portalu Marketplace — wideo** (opcjonalnie): Przekaż do pięciu filmów wideo, które przedstawiają Twoją ofertę. Filmy wideo muszą być hostowane w serwisie YouTube lub Vimeo i mieć miniaturę (plik PNG 1280 x 720).

## <a name="preview-audience"></a>Podgląd odbiorców

Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty przed jej opublikowaniem w witrynie Azure Marketplace w celu jej przetestowania. Na stronie dla **odbiorców wersji zapoznawczej** Centrum partnerskiego można zdefiniować ograniczonych odbiorców w wersji zapoznawczej.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od planu prywatnego. Plan prywatny jest dostępny tylko dla określonych odbiorców. Pozwala to na negocjowanie planu niestandardowego z konkretnymi klientami.

Możesz wysyłać zaproszenia do adresów e-mail konta Microsoft (MSA) lub Azure Active Directory (Azure AD). Ręcznie Dodaj maksymalnie 10 adresów e-mail lub zaimportuj do 20 za pomocą pliku CSV. Jeśli Twoja oferta już istnieje, możesz zdefiniować odbiorcę w wersji zapoznawczej do testowania wszelkich zmian lub aktualizacji oferty.

## <a name="plans-and-pricing"></a>Plany i cennik

Oferty usług zarządzanych wymagają co najmniej jednego planu. Plan definiuje zakres, limity i powiązane ceny rozwiązania, jeśli ma zastosowanie. Możesz utworzyć wiele planów dla oferty, aby dać klientom różne opcje techniczne i cenowe. Ogólne wskazówki dotyczące planów, w tym planów prywatnych, można znaleźć w temacie [plany i Cennik dla ofert komercyjnych w portalu Marketplace](plans-pricing.md).

Usługi zarządzane obsługują tylko jeden model cen: **Bring Your Own License (BYOL)**. Oznacza to, że klienci będą rozliczani bezpośrednio, a firma Microsoft nie będzie obciążana opłatami.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie oferty usługi zarządzanej](./create-managed-service-offer.md)
* [Najlepsze rozwiązania dotyczące ofert](./gtm-offer-listing-best-practices.md)