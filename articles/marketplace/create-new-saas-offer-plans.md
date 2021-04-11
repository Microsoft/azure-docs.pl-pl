---
title: Jak utworzyć plany dla oferty usługi SaaS w centrum partnerskim firmy Microsoft
description: Jak utworzyć plany dla nowej oferty oprogramowania jako usługi (SaaS) przy użyciu portalu komercyjnej witryny Microsoft Marketplace w centrum partnerskim.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: fb1514c146dcd352451597e6182c6d99ed8836d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044723"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Jak utworzyć plany dla oferty SaaS

Oferty sprzedawane za pomocą komercyjnego portalu Microsoft Marketplace muszą mieć co najmniej jeden plan. Możesz tworzyć różne plany z różnymi opcjami w ramach tej samej oferty. Plany te (czasami określane jako jednostki SKU) mogą różnić się w odniesieniu do wersji, zysków lub warstw usługi. Aby uzyskać szczegółowe wskazówki dotyczące planów, zobacz [plany i cenniki dla ofert komercyjnych w portalu Marketplace](plans-pricing.md).

> [!NOTE]
> Jeśli zdecydujesz się na przetwarzanie transakcji niezależnie, ta opcja nie zostanie wyświetlona. Zamiast tego przejdź do [sposobu sprzedaży oferty SaaS](create-new-saas-offer-marketing.md).

## <a name="create-a-plan"></a>Tworzenie planu

1. W górnej części karty **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**.

1. W oknie dialogowym, które się pojawi, w polu **identyfikator planu** wprowadź unikatowy identyfikator planu. Użyj maksymalnie 50 małych znaków alfanumerycznych, łączników lub podkreśleń. Nie można zmodyfikować identyfikatora planu po wybraniu opcji **Utwórz**.

1. W polu **Nazwa planu** wprowadź unikatową nazwę dla tego planu. Użyj maksymalnie 50 znaków.

1. Wybierz przycisk **Utwórz**.

## <a name="define-the-plan-listing"></a>Zdefiniuj listę planów

Na karcie **lista planów** można zdefiniować nazwę i opis planu, które mają być wyświetlane na komercyjnym rynku.

1. W polu **Nazwa planu** zostanie wyświetlona nazwa podana wcześniej dla tego planu. W każdej chwili można ją zmienić. Ta nazwa będzie wyświetlana na komercyjnej witrynie Marketplace jako tytuł planu oprogramowania Twojej oferty.

1. W polu **Opis planu** Wyjaśnij, co sprawia, że ten plan oprogramowania jest unikatowy i wszelkie różnice między innymi planami w ramach oferty. Ten opis może zawierać maksymalnie 500 znaków.
1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty: **Cennik i dostępność**.

## <a name="define-markets-pricing-and-availability"></a>Definiowanie rynków, cen i dostępności

Każdy plan musi być dostępny na co najmniej jednym rynku. Na karcie **Cennik i dostępność** można skonfigurować rynki, w których ten plan będzie dostępny w programie, żądany model zysków, cenę i warunki rozliczeń. Ponadto możesz wskazać, czy plan ma być widoczny dla wszystkich, czy tylko dla określonych klientów (nazywanych również planem prywatnym).

1. Na stronie **rynki** wybierz łącze **Edytuj rynki** .
1. W wyświetlonym oknie dialogowym Wybierz lokalizacje rynkowe, w których chcesz udostępnić plan. Należy wybrać co najmniej jeden i maksymalnie 141 rynków.

   > [!NOTE]
   > To okno dialogowe zawiera pole wyszukiwania i opcję filtrowania tylko w krajach, w których firma Microsoft przekazuje sprzedaż i korzysta z opodatkowania w Twoim imieniu.

1. Wybierz pozycję **Zapisz**, aby zamknąć okno dialogowe.

## <a name="define-a-pricing-model"></a>Definiowanie modelu cen

Model cenowy należy skojarzyć z każdym planem: _stawka ryczałtowa_ lub _na użytkownika_. Wszystkie plany w tej samej ofercie muszą korzystać z tego samego modelu cen. Na przykład oferta nie może mieć jednego planu, który ma stałą stawkę i inny plan, który jest na użytkownika. Aby uzyskać więcej informacji, zobacz [modele cen SaaS](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> Po opublikowaniu oferty nie można zmienić modelu cen. Ponadto wszystkie plany dla tej samej oferty muszą mieć ten sam model cen.

### <a name="configure-flat-rate-pricing"></a>Konfigurowanie cen stawki zryczałtowanej

1. Na karcie **Cennik i dostępność** w obszarze **Cennik** wybierz pozycję stała **stawka**.
1. Zaznacz pole wyboru **miesięczny** lub **roczny** , a następnie wprowadź cenę.

### <a name="add-a-custom-meter-dimension"></a>Dodaj niestandardowy wymiar miernika

Ta opcja jest dostępna tylko w przypadku wybrania cennika stawki zryczałtowanej. Aby uzyskać więcej informacji, zobacz [naliczanie opłat za SaaS przy użyciu komercyjnej usługi pomiarów w portalu Marketplace](./partner-center-portal/saas-metered-billing.md).

1. W obszarze **Wymiary usługi pomiaru Marketplace** wybierz łącze **Dodaj niestandardowy wymiar miernika (max 30)** .
1. W polu **Identyfikator** wprowadź niezmienne odwołanie do identyfikatora podczas emitowania zdarzeń użycia.
1. W polu **Nazwa wyświetlana** wprowadź nazwę wyświetlaną skojarzoną z wymiarem. Na przykład "wysłane wiadomości SMS".
1. W polu **Jednostka miary** wprowadź opis jednostki rozliczeniowej. Na przykład "za wiadomość SMS" lub "na 100 wiadomości e-mail".
1. W polu **Cena jednostkowa w USD** wprowadź cenę jednej jednostki wymiaru.
1. W polu **Liczba miesięczna uwzględnionych w programie** wprowadź ilość (jako liczbę całkowitą) wymiaru, który jest uwzględniany w każdym miesiącu dla klientów, którzy zwracają cykliczną opłatą miesięczną. Aby ustawić nieograniczoną liczbę, zaznacz to pole wyboru.
1. W obszarze **roczna ilość uwzględniona w polu Podstawa** wprowadź ilość wymiaru (jako liczbę całkowitą), która jest uwzględniana w każdym miesiącu dla klientów, którzy zwracają cykliczną opłatą roczną. Aby ustawić nieograniczoną liczbę, zaznacz to pole wyboru.
1. Aby dodać kolejny niestandardowy wymiar miernika, wybierz łącze **Dodaj inny wymiar** , a następnie powtórz kroki od 1 do 7.

### <a name="configure-per-user-pricing"></a>Konfigurowanie cennika dla poszczególnych użytkowników

1. Na karcie **Cennik i dostępność** w obszarze **Cennik** wybierz pozycję **na użytkownika**.
2. Jeśli ma to zastosowanie, w obszarze **limity użytkowników** Określ minimalną i maksymalną liczbę użytkowników dla tego planu.
3. W obszarze **okres rozliczeniowy** Określ cenę miesięczną, cenę roczną lub oba te elementy.

### <a name="validate-custom-prices"></a>Sprawdzanie poprawności cen niestandardowych

Aby ustawić ceny niestandardowe na indywidualnym rynku, należy wyeksportować, zmodyfikować, a następnie zaimportować arkusz cen. Użytkownik jest odpowiedzialny za sprawdzanie poprawności cen i posiadanie tych ustawień. Aby uzyskać szczegółowe informacje, zobacz [ceny niestandardowe](plans-pricing.md#custom-prices).

1. Najpierw musisz zapisać zmiany cen, aby włączyć eksportowanie danych cen. W dolnej części karty **Cennik i dostępność** wybierz pozycję **Zapisz wersję roboczą**.
1. W obszarze **Cennik** wybierz łącze **Eksportuj dane cennika** .
1. Otwórz plik exportedPrice.xlsx w programie Microsoft Excel.
1. W arkuszu kalkulacyjnym wprowadź aktualizacje, aby uzyskać informacje o cenach, a następnie Zapisz. Plik CSV.<br> Aby można było zaktualizować plik, może być konieczne włączenie edycji w programie Excel.
2. Na karcie **Cennik i dostępność** w obszarze **Cennik** wybierz łącze **Importuj dane cennika** .
3. W wyświetlonym oknie dialogowym kliknij przycisk **tak**.
4. Wybierz zaktualizowany plik exportedPrice.xlsx a następnie kliknij przycisk **Otwórz**.

### <a name="enable-a-free-trial"></a>Włącz bezpłatną wersję próbną

Możesz skonfigurować bezpłatną wersję próbną dla każdego planu w ofercie. Zaznacz to pole wyboru, aby zezwolić na miesięczną bezpłatną wersję próbną. To pole wyboru nie jest dostępne w przypadku planów korzystających z usługi pomiaru Marketplace. Aby uzyskać więcej informacji, zobacz [bezpłatne wersje próbne](plans-pricing.md#free-trials).

> [!IMPORTANT]
> Po opublikowaniu oferty transakcyjnej w ramach bezpłatnej wersji próbnej nie można jej wyłączyć dla tego planu. Przed opublikowaniem oferty upewnij się, że to ustawienie jest poprawne, aby uniknąć konieczności ponownego tworzenia planu.

- W obszarze **bezpłatna wersja próbna** zaznacz pole wyboru **Zezwalaj na miesięczną bezpłatną wersję próbną** .

## <a name="choose-who-can-see-your-plan"></a>Wybierz, kto może zobaczyć Twój plan

Każdy plan można skonfigurować tak, aby był widoczny dla wszystkich użytkowników lub tylko do określonych odbiorców. Możesz udzielić dostępu do planu prywatnego przy użyciu identyfikatorów dzierżawców z opcją dołączenia opisu każdego przypisanego identyfikatora dzierżawy. Możesz dodać maksymalnie 10 identyfikatorów dzierżaw ręcznie lub maksymalnie 20 000 identyfikatorów dzierżawy przy użyciu. Plik CSV.

> [!NOTE]
> W przypadku opublikowania planu prywatnego można później zmienić jego widoczność na publiczną. Jednak po opublikowaniu planu publicznego nie można zmienić jego widoczności na prywatną.

### <a name="make-your-plan-public"></a>Ustaw swój plan jako publiczny

1. W obszarze **widoczność planu** wybierz pole **publiczne** .
1. Wybierz pozycję **Zapisz wersję roboczą**, a następnie w lewym górnym rogu karty wybierz pozycję **Przegląd planu** , aby powrócić do karty **Przegląd planu** .
1. Aby utworzyć kolejny plan dla tej oferty, w górnej części karty **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**. Następnie powtórz kroki opisane w sekcji [Tworzenie planu](#create-a-plan) . W przeciwnym razie przejdź do pozycji [Wyświetl plany](#view-your-plans).

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Ręcznie Dodaj identyfikatory dzierżawy dla planu prywatnego 

1. W obszarze **widoczność planu** wybierz **to pole jest planem prywatnym** .
1. W wyświetlonym polu **Identyfikator dzierżawy** wprowadź identyfikator dzierżawy usługi Azure AD dla odbiorców, którym chcesz udzielić dostępu do tego planu prywatnego. Wymagany jest co najmniej jeden identyfikator dzierżawy.
1. Obowiązkowe Wprowadź opis tych odbiorców w polu **Opis** .
1. Aby dodać inny identyfikator dzierżawy, powtórz kroki 2 i 3.
1. Po zakończeniu dodawania identyfikatorów dzierżawy wybierz pozycję **Zapisz wersję roboczą**, a następnie w lewym górnym rogu karty wybierz pozycję **planowanie przegląd** , aby powrócić do karty **Przegląd planu** .
1. Aby utworzyć kolejny plan dla tej oferty, w górnej części karty **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**. Następnie powtórz kroki opisane w sekcji [Tworzenie planu](#create-a-plan) . W przeciwnym razie przejdź do pozycji [Wyświetl plany](#view-your-plans).

### <a name="use-a-csv-file-for-a-private-plan"></a>Użyj. Plik CSV dla planu prywatnego

1. W obszarze **widoczność planu** wybierz **to pole jest planem prywatnym** .
2. Wybierz łącze **Eksportuj odbiorców (CSV)** .
3. Otwórz okno. Plik CSV i Dodaj identyfikatory platformy Azure, dla których chcesz udzielić dostępu do prywatnej oferty do kolumny **ID** .
4. Opcjonalnie wprowadź opis dla każdej grupy odbiorców w kolumnie **Opis** .
5. Dodaj element "TenantID" w kolumnie **Type** dla każdego wiersza z identyfikatorem platformy Azure.
6. Zapisz. Plik CSV.
7. Na karcie **Cennik i dostępność** w obszarze **widoczność planu** wybierz łącze **Importuj odbiorców (CSV)** .
8. W wyświetlonym oknie dialogowym wybierz pozycję **tak**.
9. Wybierz pozycję. Plik CSV, a następnie wybierz pozycję **Otwórz**.
10. Wybierz pozycję **Zapisz wersję roboczą**, a następnie w lewym górnym rogu karty wybierz pozycję **Przegląd planu** , aby powrócić do karty **Przegląd planu** .
11. Aby utworzyć kolejny plan dla tej oferty, w górnej części karty **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**. Następnie powtórz kroki opisane w sekcji [Tworzenie planu](#create-a-plan) . W przeciwnym razie, jeśli skończysz tworzyć plany, przejdź do następnej sekcji: **Wyświetlanie planów**.

## <a name="view-your-plans"></a>Wyświetlanie planów

Po utworzeniu jednego lub kilku planów zobaczysz nazwę planu, identyfikator planu, model cen, dostępność (Public lub private), bieżący stan publikowania oraz wszystkie dostępne akcje na karcie **Przegląd planu** .

Akcje, które są dostępne w kolumnie **Akcja** karty **Przegląd planu** , różnią się w zależności od stanu planu i mogą obejmować następujące elementy:

- Jeśli plan ma stan **wersja robocza**, w kolumnie **Akcja** zostanie wyświetlona wartość **Usuń wersję roboczą**.
- Jeśli plan ma stan **Live**, link w kolumnie **akcji** będzie **zatrzymywać sprzedawanie planu** lub **zsynchronizować odbiorców prywatnych**. Link **Synchronizuj odbiorców prywatnych** będzie publikować tylko zmiany w prywatnych odbiorcach, bez publikowania innych aktualizacji, które mogły zostać wprowadzone do oferty.

## <a name="before-you-publish-your-offer"></a>Przed opublikowaniem oferty

Jeśli jeszcze tego nie zrobiono, Utwórz ofertę tworzenia i testowania (DEV), aby przetestować ofertę przed opublikowaniem oferty produkcyjnej na żywo. Aby dowiedzieć się więcej, zobacz [Tworzenie oferty tworzenia i testowania](create-saas-dev-test-offer.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak sprzedawać ofertę SaaSą](create-new-saas-offer-marketing.md) w ramach **Współsprzedaży z firmą Microsoft** i **odsprzedawać je za pomocą programów CSP** .
- [Testowanie i publikowanie oferty SaaS na komercyjnym rynku](test-publish-saas-offer.md).
