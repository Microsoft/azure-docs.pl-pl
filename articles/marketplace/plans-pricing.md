---
title: Plany i Cennik dla ofert komercyjnych Marketplace
description: Dowiedz się więcej na temat planów dla ofert portalu komercyjnego firmy Microsoft w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: e97171e61814b832e78f7bd815d8d6f341a90840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91858176"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>Plany i Cennik dla ofert komercyjnych Marketplace

Plan definiuje zakres i limity oferty oraz powiązane ceny, jeśli ma to zastosowanie. Na przykład, w zależności od typu oferty, można wybrać rynki regionalne i zdecydować, czy plan jest widoczny dla publiczności, czy tylko dla odbiorców prywatnych. Niektóre typy ofert obsługują subskrypcje cykliczne, niektóre obsługują Cennik oparty na użyciu i niektórzy umożliwiają zakupienie oferty przez klienta w ramach licencji zakupionej bezpośrednio od wydawcy. Zapewnia to elastyczność, zapewniając klientom różne opcje techniczne i cenowe.

Można utworzyć do 100 planów dla każdej oferty, która obsługuje plany, i do 45 tych planów może być [prywatna](#plan-visibility). Możesz tworzyć płatne plany tylko dla ofert maszyn wirtualnych platformy Azure, oferty aplikacji platformy Azure (aplikacje zarządzane) i oferty oprogramowanie jako usługa (SaaS). W przypadku wybrania opcji zakupu dowolnej z tych ofert za poorednictwem firmy Microsoft (oferty transakcyjne) wymagane jest utworzenie co najmniej jednego planu. Można utworzyć plany dla niektórych innych typów ofert, ale plany dla tych typów ofert nie obejmują opcji cenowych.

> [!TIP]
> Oferta, w której firma Microsoft ułatwia wymianę pieniędzy na licencję na oprogramowanie w imieniu wydawcy.

## <a name="plans-by-offer-type"></a>Plany według typu oferty

W poniższej tabeli przedstawiono opcje planu dla każdego typu oferty. Poniższe sekcje zawierają więcej informacji na temat tych opcji. 

| Typ oferty | Plany z opcjami cenowymi | Plany bez opcji cenowych | Opcja odbiorców prywatnych |
| ------------ | ------------- | ------------- | ------------- |
| Aplikacja zarządzana platformy Azure | &#10004; | | &#10004; |
| Szablon rozwiązania platformy Azure | | &#10004; | &#10004; |
| Kontener platformy Azure | | &#10004; (BYOL) | |
| Moduł IoT Edge |  | &#10004; |  |
| Usługa zarządzana |  | &#10004; (BYOL) | &#10004; |
| Oprogramowanie jako usługa | &#10004; |  | &#10004; |
| Maszyna wirtualna platformy Azure | &#10004; |  | &#10004; |
|||||

Plany nie są obsługiwane w przypadku następujących typów ofert:

- Usługa doradcza
- Dynamics 365 Business Central
- Dynamics 365 Customer Engagement & PowerApps
- Dynamics 365 for Operations
- Aplikacja Power BI

## <a name="plan-information"></a>Informacje o planie

Każdy typ oferty wymaga innych informacji podczas tworzenia nowego planu. Linki do artykułów specyficznych dla oferty można znaleźć w [przewodniku publikowania według typu oferty](publisher-guide-by-offer-type.md). Po utworzeniu nowego planu na stronie **Przegląd planu** zostaną wyświetlone różne karty, takie jak **Plan aukcji** lub **Cennik i dostępność** , aby skonfigurować różne szczegóły dla Twojego planu. Na każdej karcie będzie wyświetlany stan nieukończony lub ukończony podczas pracy nad tymi polami.

![Przedstawia stronę aukcji planu w centrum partnerskim. Pola Nazwa planu, podsumowanie planu i opis planu są wyróżnione.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Istnieje kilka typowych szczegółów, które należy wykonać w celu uzyskania nowego planu:

- **Identyfikator planu**: Utwórz unikatowy identyfikator dla każdego planu w tej ofercie. Używaj maksymalnie 50 znaków: tylko małe litery, znaki alfanumeryczne, łączniki i podkreślenia. Ten identyfikator będzie widoczny dla klientów w adresie URL produktu i szablonach Azure Resource Manager (jeśli dotyczy). Tego identyfikatora nie można zmienić po opublikowaniu oferty.
- **Nazwa planu**: (Objaśnienie 1 na powyższym obrazie). Utwórz unikatową nazwę dla każdego planu w tej ofercie. Użyj maksymalnie 50 znaków. Nazwa planu służy do rozróżniania planów oprogramowania, które mogą być częścią tej samej oferty (na przykład nazwa oferty: plan standardowy i plan przedsiębiorstwa). Klienci będą widzieli tę nazwę podczas wybierania planu do wyboru w ramach oferty.
- **Podsumowanie planu**: (Objaśnienie 2 na powyższym obrazie). To podsumowanie jest wyświetlane w wynikach wyszukiwania portalu Azure Marketplace i może zawierać maksymalnie 100 znaków.
   > [!NOTE]
   > To pole nie dotyczy ofert SaaS.
- **Opis planu**: (Objaśnienie 3 na powyższym obrazie). Dodaj opis planu, który wyjaśnia, co sprawia, że ten plan jest unikatowy z innych planów oferty. Użyj maksymalnie 500 znaków. Ta zawartość będzie widoczna dla klientów na stronach z listą ofert podczas przeglądania i wybierania planu dla oferty.

Nazwa i opis planu są wyświetlane na stronie z listą ofert w komercyjnych sklepach online w portalu Marketplace. Poniższy zrzut ekranu przedstawia trzy plany dotyczące SaaS oferty w witrynie Azure Marketplace.

![Przedstawia stronę aukcji oferty w centrum partnerskim. Przedstawiono trzy plany.](./media/commercial-marketplace-plans/offer-listing-page.png)

Po utworzeniu planów na stronie **Przegląd planu** zostanie wyświetlona lista Twoich planów, identyfikator, inne szczegóły, bieżący stan publikowania i wszystkie dostępne akcje. Dostępne akcje zależą od stanu planu i mogą obejmować:

- Jeśli plan ma stan **wersja robocza** — Usuń wersję roboczą.
- Jeśli plan ma stan **Live** – Zatrzymaj plan sprzedaży lub zsynchronizuj odbiorców prywatnych.

Poniższy zrzut ekranu przedstawia dwie oferty robocze.

![Przedstawia stronę przegląd planu w centrum partnerskim. Przedstawiono dwa plany.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Cennik i dostępność

Komercyjny Portal rynku działa na modelu agencji, zgodnie z którym wydawcy ustawili ceny, klienci z firmą Microsoft Bills, a firma Microsoft płaci przychody dla wydawców w trakcie wstrzymania opłaty za Agencję. Możesz zdefiniować rynki, widoczność i Cennik oferty (jeśli ma to zastosowanie) na karcie **Cennik i dostępność** lub **dostępność** .

- **Rynki**: każdy plan musi być dostępny na co najmniej jednym rynku.  Można wybrać tylko kraje, w których firma Microsoft przekazuje sprzedaż i korzystać z opodatkowania w Twoim imieniu.
- **Cennik**: modele cenowe dotyczą tylko planów dla usług Azure Managed Application, SaaS i Azure Virtual Machine. Wszystkie plany dla tej samej oferty muszą korzystać z tego samego modelu cen.  
- **Widoczność planu**: w zależności od typu oferty możesz zdefiniować odbiorców prywatnych lub ukryć ofertę lub plan w witrynie Azure Marketplace. Ta informacja została omówiona bardziej szczegółowo w temacie [Planowanie widoczności](#plan-visibility) w dalszej części tego artykułu.

> [!TIP]
> Zalecamy utworzenie planów, które najlepiej pasują do wzorców użytkowania docelowej bazy klientów. Pozwala to ograniczyć użytkownikom częste przełączanie planów na podstawie ich zmian w użyciu. Przykład oferty SaaS z trzema naliczanymi planami rozliczeń można znaleźć w temacie [Przykładowa oferta](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Modele cenowe

Model cenowy należy skojarzyć z każdym planem dla następujących typów ofert. Każdy z tych typów ofert ma różne dostępne modele cen:

- **Aplikacja zarządzana platformy Azure**: stawka ryczałtowa (miesięczna) i cena oparta na użyciu (Wymiary usługi pomiarowej).
- **Oprogramowanie jako usługa**: stawka ryczałtowa (miesięczna lub roczna), za użytkownika i Cennik oparty na użyciu (Wymiary usługi pomiarowej). 
- **Maszyna wirtualna platformy Azure**: Bring Your Own License (BYOL) i Cennik oparty na użyciu. W przypadku modelu cen opartego na użyciu można naliczać opłaty za rdzeń, rozmiar na rdzeń lub na rynek i rozmiar podstawowy. Model licencji BYOL nie zezwala na dodatkowe opłaty oparte na użyciu.   (BYOL oferty maszyn wirtualnych nie wymagają modelu cen).

Wszystkie plany dla tej samej oferty muszą korzystać z tego samego modelu cen. Na przykład oferta SaaS nie może mieć jednego planu, który ma stałą stawkę i inny plan, który jest na użytkownika. Szczegółowe informacje można znaleźć w dokumentacji oferty.

Jeśli już ustawisz ceny dla planu w Stany Zjednoczone dolarów (USD) i dodasz kolejną lokalizację na rynku, cena nowego rynku zostanie obliczona zgodnie z bieżącymi stawkami za wymianę. Po zapisaniu zmian zobaczysz link z **cenami eksportowymi (xlsx)** , za pomocą którego można przejrzeć i zmienić cenę poszczególnych rynków przed opublikowaniem.

> [!IMPORTANT]
> Po opublikowaniu oferty wybór modelu cen nie może zostać zmieniony.

Proste SaaS oferty i aplikacja zarządzana oferują pomoc techniczną do regulowania rozliczeń przy użyciu usługi pomiaru Marketplace. Jest to model rozliczania oparty na użyciu, który umożliwia definiowanie jednostek niestandardowych, takich jak przepustowość lub wiadomości e-mail, dzięki czemu klienci będą obciążani opłatami za użycie. Zapoznaj się z połączoną dokumentacją, aby dowiedzieć się więcej o naliczaniu opłat za [aplikacje zarządzane](./partner-center-portal/azure-app-metered-billing.md) i [aplikacje SaaS](./partner-center-portal/saas-metered-billing.md).

## <a name="custom-prices"></a>Ceny niestandardowe

Aby ustawić ceny niestandardowe na indywidualnym rynku, należy wyeksportować, zmodyfikować, a następnie zaimportować arkusz cen. Użytkownik jest odpowiedzialny za sprawdzanie poprawności cen i posiadanie tych ustawień.

Uważnie Przejrzyj ceny przed opublikowaniem, ponieważ istnieją pewne ograniczenia dotyczące tego, co można zmienić po opublikowaniu planu:

- Po opublikowaniu planu nie można zmienić modelu cen.
- Po opublikowaniu okresu rozliczeniowego dla planu nie można go później usunąć.
- Po opublikowaniu ceny na rynku planu nie można jej później zmienić.

Ceny ustawione w Stany Zjednoczone dolarach (USD) są konwertowane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących kursów wymiany przy zapisywaniu. Sprawdź te ceny przed opublikowaniem przez wyeksportowanie arkusza cen i zapoznaj się z ceną w każdym wybranym rynku.

1. Najpierw musisz zapisać zmiany cen, aby włączyć eksportowanie danych cen. W dolnej części karty **Cennik i dostępność** wybierz pozycję **Zapisz wersję roboczą**.
1. W obszarze **Cennik** wybierz łącze **Eksportuj dane cennika** .
1. Otwórz plik exportedPrice.xlsx w programie Microsoft Excel.
1. W arkuszu kalkulacyjnym wprowadź aktualizacje, aby uzyskać informacje o cenach, a następnie Zapisz. Plik CSV.
    > [!NOTE]
    > Aby można było zaktualizować plik, może być konieczne włączenie edycji w programie Excel.
1. Na karcie **Cennik i dostępność** w obszarze **Cennik** wybierz łącze **Importuj dane cennika** .
1. W wyświetlonym oknie dialogowym wybierz pozycję **tak**.
1. Wybierz zaktualizowany plik exportedPrice.xlsx a następnie wybierz pozycję **Otwórz**.

## <a name="plan-visibility"></a>Widoczność planu

Plany dla niektórych typów ofert są publicznie dostępne lub dostępne tylko dla określonych odbiorców (prywatnych). Oferty z planami prywatnymi zostaną opublikowane na Azure Portal. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](private-offers.md).

Użytkownik definiuje swoich prywatnych odbiorców przy użyciu identyfikatorów dzierżawy platformy Azure lub identyfikatorów subskrypcji platformy Azure, w zależności od typu oferty. Możesz wprowadzić do 10 identyfikatorów ręcznie lub zaimportować do 10, 00 identyfikatorów subskrypcji lub identyfikatorów dzierżaw 20 000 (zgodnie z wymaganiami) przy użyciu pliku CSV. Możesz również zdefiniować prywatnych odbiorców dla usług doradczych i ofert systemu Dynamics 365, które nie mają planów.

Po opublikowaniu oferty z planem prywatnym możesz zaktualizować odbiorców lub wybrać opcję udostępnienia planu wszystkim użytkownikom. Gdy plan zostanie opublikowany jako widoczny dla wszystkich użytkowników, musi pozostawać widoczny dla wszystkich i nie może zostać skonfigurowany jako plan prywatny ponownie.

> [!NOTE]
> Prywatni odbiorcy różnią się od odbiorców w wersji zapoznawczej. Na stronie **dostępność** niektórych typów ofert możesz zdefiniować odbiorców, którzy będą mogli wyświetlić podgląd oferty przed opublikowaniem oferty na żywo w komercyjnej witrynie Marketplace. Gdy oznaczenie odbiorców prywatnych odnosi się tylko do określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlać wszystkie plany (prywatne lub nie), ale tylko w okresie ograniczonej wersji zapoznawczej, podczas gdy plan jest testowany i sprawdzany.

Możesz również wybrać opcję ukrycia planu całkowicie z portalu Azure Marketplace, jeśli oferta zostanie wdrożona w ramach szablonu aplikacji zarządzanej lub rozwiązania.

## <a name="free-trials"></a>Bezpłatne wersje próbne

Możesz włączyć bezpłatną wersję próbną dla planów dla usługi Azure Virtual Machines i SaaS.

> [!NOTE]
> W tej sekcji opisano bezpłatne wersje próbne planów płatnych w ramach ofert, które są sprzedawane w firmie Microsoft. Różnią się one od bezpłatnych wersji próbnych od partnerów, którzy chcą niezależnie przetwarzać transakcje. Możesz utworzyć bezpłatną listę próbną (nieskojarzoną z planem) dla następujących typów ofert:
> - Maszyna wirtualna platformy Azure 
> - SaaS 
> - Dynamics 365 Business Central
> - Dynamics 365 dla zaangażowania klienta & PowerApps
> - Dynamics 365 for Operations
>
> Aby uzyskać więcej informacji na temat opcji wyświetlania listy, zobacz [Określanie opcji publikowania](determine-your-listing-type.md).

Bezpłatne wersje próbne są obsługiwane dla wszystkich modeli rozliczeń z wyjątkiem planów taryfowych. Plany SaaS umożliwiają korzystanie z 1-miesięcznych bezpłatnych wersji próbnych. Plany maszyn wirtualnych platformy Azure umożliwiają korzystanie z 1, 3 lub 6-miesięcznych bezpłatnych wersji próbnych.

Po wybraniu przez klienta bezpłatnej wersji próbnej zbierzemy informacje o rozliczeniach, ale nie rozliczmy klienta, dopóki wersja próbna nie zostanie przekonwertowana na płatną subskrypcję. Bezpłatne wersje próbne są automatycznie konwertowane na płatną subskrypcję na koniec okresu próbnego, chyba że klient anuluje subskrypcję przed zakończeniem okresów próbnych.

W okresie próbnym klienci mogą oszacować dowolne z obsługiwanych planów w ramach tej samej oferty, w ramach której jest włączona bezpłatna wersja próbna. Jeśli przełączysz się do innej wersji próbnej w ramach tej samej oferty, okres próbny nie zostanie uruchomiony ponownie. Na przykład jeśli klient korzysta z bezpłatnej wersji próbnej przez 15 dni, a następnie przejdzie do innej bezpłatnej wersji próbnej dla tej samej oferty, nowy okres próbny będzie uwzględniać 15 dni. Bezpłatna wersja próbna, którą klient ocenia po zakończeniu okresu próbnego, jest automatycznie konwertowana na płatną subskrypcję.

Po wybraniu przez klienta bezpłatnej wersji próbnej dla planu nie można dokonać konwersji na płatną subskrypcję dla tego planu, dopóki nie zakończy się okres próbny. Jeśli klient zdecyduje się na przekonwertowanie na inny plan w ramach oferty, która nie ma bezpłatnej wersji próbnej, zostanie wykonana konwersja, ale bezpłatna wersja próbna zakończy się natychmiast, a wszystkie dane zostaną utracone.

> [!NOTE]
> Po rozpoczęciu płacenia przez klienta planu nie można ponownie uzyskać bezpłatnej wersji próbnej tego samego oferty, nawet jeśli przełączysz się do innego planu, który obsługuje bezpłatne wersje próbne.

Aby uzyskać informacje o subskrypcjach klientów, które aktualnie uczestniczą w bezpłatnej wersji próbnej, użyj nowej właściwości interfejsu API `isFreeTrial` , która zostanie oznaczona jako true lub false. Aby uzyskać więcej informacji, zobacz [SaaS Get Subscription API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak dodać lub zaktualizować plany w ramach istniejącej oferty, zobacz temat [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](./partner-center-portal/update-existing-offer.md).
- Aby dowiedzieć się więcej o opcjach transakcji i skojarzonych modelach cen, zobacz [komercyjne funkcje Transact Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).
