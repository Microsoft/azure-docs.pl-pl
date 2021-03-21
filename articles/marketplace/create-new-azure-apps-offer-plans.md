---
title: Jak utworzyć plany dla oferty aplikacji platformy Azure
description: Dowiedz się, jak utworzyć plany dla oferty aplikacji platformy Azure w centrum partnerskim.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370266"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Jak utworzyć plany dla oferty aplikacji platformy Azure

Oferty sprzedawane za pomocą komercyjnej witryny Marketplace firmy Microsoft muszą mieć co najmniej jeden plan, aby można było wystawić ofertę na rynku komercyjnym. Możesz tworzyć różne plany z różnymi opcjami w ramach tej samej oferty. Plany te (czasami określane jako jednostki SKU) mogą różnić się w zależności od typu planu (_szablonu rozwiązania_ lub _aplikacji zarządzanej_), zysków lub odbiorców. Ogólne wskazówki dotyczące planów można znaleźć w temacie [plany i cenniki dla ofert komercyjnych w portalu Marketplace](plans-pricing.md).

## <a name="create-a-plan"></a>Tworzenie planu

1. W górnej części karty **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**.
1. W oknie dialogowym, które się pojawi, w polu **identyfikator planu** wprowadź unikatowy identyfikator planu. Ten identyfikator będzie widoczny dla klientów w adresie URL produktu. Użyj maksymalnie 50 małych znaków alfanumerycznych, łączników lub podkreśleń. Nie można zmodyfikować identyfikatora planu po wybraniu opcji **Utwórz**.
1. W polu **Nazwa planu** wprowadź unikatową nazwę dla tego planu. Klienci będą widzieli tę nazwę podczas wybierania planu do wyboru w ramach oferty. Użyj maksymalnie 50 znaków.
1. Wybierz przycisk **Utwórz**.

## <a name="define-the-plan-setup"></a>Definiowanie konfiguracji planu

Karta **ustawienia planu** pozwala ustawić typ planu, czy ma on replikować konfigurację techniczną z innego planu i w jakich regionach platformy Azure ma być dostępny plan. Odpowiedzi na tej karcie wpłyną na to, które pola są wyświetlane na innych kartach dla tego planu.

### <a name="select-the-plan-type"></a>Wybierz typ planu

- Z listy **Typ planu** wybierz opcję **szablon rozwiązania** lub **aplikacja zarządzana**.

Plan **szablonu rozwiązania** jest zarządzany całkowicie przez klienta. Plan **aplikacji zarządzanej** umożliwia wydawcom zarządzanie aplikacją w imieniu klienta. Aby uzyskać szczegółowe informacje dotyczące tych dwóch typów planu, zobacz [typy planów](plan-azure-application-offer.md#types-of-plans).

### <a name="re-use-technical-configuration-optional"></a>Ponowne używanie konfiguracji technicznej (opcjonalnie)

Jeśli utworzono więcej niż jeden plan tego samego typu w ramach tej oferty, a konfiguracja techniczna jest taka sama, można użyć konfiguracji technicznej z innego planu. Tego ustawienia nie można zmienić po opublikowaniu tego planu.

#### <a name="to-re-use-technical-configuration"></a>Aby wykorzystać konfigurację techniczną

1. Zaznacz pole wyboru **ten plan ponownie używa konfiguracji technicznej z innego planu tego samego typu** .
1. Na wyświetlonej liście wybierz odpowiedni plan bazowy.

> [!NOTE]
> Po ponownym użyciu pakietów z innego planu, cała karta **konfiguracja techniczna** znika z tego planu. Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje wprowadzone w przyszłości, są również używane dla tego planu.

### <a name="select-azure-regions-clouds"></a>Wybierz regiony platformy Azure (chmury)

Plan musi być dostępny w co najmniej jednym regionie świadczenia usługi Azure. Po opublikowaniu planu i udostępnieniu go w określonym regionie świadczenia usługi Azure nie można usunąć tego regionu z oferty.

#### <a name="azure-global-region"></a>Region globalny platformy Azure

Globalne pole wyboru **platformy Azure** jest domyślnie zaznaczone. Dzięki temu plan jest dostępny dla klientów we wszystkich regionach globalnych platformy Azure, które mają komercyjną integrację z portalu Marketplace. W przypadku planów aplikacji zarządzanych można wybrać rynki, dla których ma być dostępny plan.

Aby usunąć ofertę z tego regionu, wyczyść pole wyboru **globalne platformy Azure** .

#### <a name="azure-government-region"></a>Region Azure Government

Ten region zapewnia klientom kontrolowany dostęp do jednostek federalnych, stanowych, lokalnych i plemienne, a także partnerów uprawnionych do ich świadczenia. Ponieważ Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania. Azure Government używa fizycznie wyizolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych).

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS. Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków, które je opisują. Mogą to być albo linki do swojej aukcji bezpośrednio w programie, jak i linki do opisów zgodności z ich własnymi witrynami sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

##### <a name="to-select-the-azure-government-region"></a>Aby wybrać region Azure Government

1. Zaznacz pole wyboru **Azure Government** .
1. W obszarze **certyfikaty Azure Government** wybierz pozycję **+ Dodaj certyfikat (maks. 100)**.
1. W wyświetlonych polach Podaj nazwę i link do certyfikatu.
1. Aby dodać kolejne certyfikaty, powtórz kroki 2 i 3.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: Zaplanuj listę.

## <a name="define-the-plan-listing"></a>Zdefiniuj listę planów

Na karcie **lista planów** można skonfigurować szczegółowe informacje o planie. Na tej karcie są wyświetlane określone informacje, które pokazują różnicę między planami w tej samej ofercie. Możesz zdefiniować nazwę planu, podsumowanie i opis, które mają być wyświetlane na komercyjnym rynku.

1. W polu **Nazwa planu** zostanie wyświetlona nazwa podana wcześniej dla tego planu. W każdej chwili można ją zmienić. Ta nazwa zostanie wyświetlona w komercyjnej witrynie Marketplace jako tytuł planu oprogramowania oferty i jest ograniczona do 100 znaków.
1. W polu **Podsumowanie planu** Podaj krótkie podsumowanie planu (nie oferty). To podsumowanie jest ograniczone do 100 znaków.
1. W polu **Opis planu** Wyjaśnij, co sprawia, że ten plan oprogramowania jest unikatowy i wszelkie różnice między innymi planami w ramach oferty. Nie opisz oferty, tylko plan. Ten opis może zawierać maksymalnie 2 000 znaków.
1. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="next-steps"></a>Następne kroki

Wykonaj jedną z następujących czynności:

- W przypadku konfigurowania planu szablonu rozwiązania przejdź do pozycji [Konfigurowanie planu szablonu rozwiązania](create-new-azure-apps-offer-solution.md).
- W przypadku konfigurowania planu aplikacji zarządzanej przejdź do pozycji [Konfigurowanie zarządzanego planu aplikacji](create-new-azure-apps-offer-managed.md).
