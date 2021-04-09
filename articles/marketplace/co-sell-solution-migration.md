---
title: Przeprowadź migrację rozwiązań do współpracy z GTM OCP do Centrum partnerskiego, aby uzyskać Microsoft AppSource
description: Dowiedz się, jak przeprowadzić migrację rozwiązań współsprzedawanych z GTM OCP do Centrum partnerskiego w celu Microsoft AppSource).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/04/2021
ms.openlocfilehash: 7ffb5a3dfd23a1515c6d21784b82ccdbf0674f0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593411"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>Migracja rozwiązań do sprzedaży ze OCP GTM do komercyjnej witryny Marketplace

Firma Microsoft przenosi środowisko publikowania. [Komercyjne witryny Marketplace](overview.md) zapewniają uproszczoną publikację do współsprzedaży przez trzy kanały firmy Microsoft dzięki scentralizowanemu tworzeniu ofert i zarządzaniu w centrum partnerskim, w którym zarządzasz już swoją relacją z firmą Microsoft.

Jako partner firmy Microsoft zarejestrowany w komercyjnej witrynie Marketplace możesz:

- Publikuj swoje oferty w centralnie i we współpracy między bezpośrednimi klientami, partnerami i kanałami sprzedającymi firmy Microsoft.
- Upewnij się, że Twoje oferty znajdują się w odpowiednim sklepie online —[Microsoft AppSource](https://appsource.microsoft.com) lub [witrynie Azure Marketplace](https://azure.microsoft.com), aby dotrzeć do milionów klientów w chmurze, którzy są dodanym do możliwości oferty.
- Zapoznaj się z własnymi procesami publikowania, aby obsprzedawać oferty, które są zgodne z celami biznesowymi.
- Wyrównaj publikowanie oferty w centrum partnerskim, w którym już zarządzasz swoją relacją firmy Microsoft i możliwością współsprzedaży.
- Odblokuj [nagrody z witryny Marketplace](partner-center-portal/marketplace-rewards.md).

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Wymagania wstępne dotyczące kontynuowania współpracy z firmą Microsoft

Upewnij się, że masz aktywne członkostwo w Microsoft Partner Network i są one zarejestrowane w portalu komercyjnym w centrum partnerskim.

- Dołącz do Microsoft Partner Network [bez kosztów](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership). Jako partner będziesz mieć dostęp do wyłącznych zasobów, programów, narzędzi i połączeń, aby rozwijać swoją firmę.
- Jeśli nie masz konta w komercyjnej witrynie Marketplace, [zarejestruj się teraz](partner-center-portal/create-account.md) , aby kontynuować sprzedawanie w firmie Microsoft i uzyskać dostęp do pełnego środowiska publikowania.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Publikowanie aktualizacji w celu osiągnięcia stanu gotowości do sprzedawania

Aby Twoje rozwiązanie było wykrywalne dla sprzedawcy i partnerów firmy Microsoft, musi on spełniać [wymagania dotyczące](marketplace-co-sell.md)współtworzenia. Aby sprzedawca firmy Microsoft mógł zostać zachęcani, Twoje rozwiązanie musi spełniać [wymagania kwalifikujące się do zachęty](marketplace-co-sell.md). Wykonaj te wymagania na karcie współsprzedaż w centrum partnerskim (zobacz [ten obraz](#cosell-tab) w dalszej części tego artykułu).

> [!NOTE]
> W komercyjnej witrynie Marketplace Twoje rozwiązania są nazywane "ofertami" w całym środowisku publikowania.

Po zarejestrowaniu się w komercyjnej witrynie Marketplace Przygotuj się do migracji rozwiązań z OCP GTM.

Przed zaimportowaniem rozwiązań z GTM OCP należy wykonać następujące czynności:

1. Odwiedź [listę wydawców](https://partner.microsoft.com/dashboard/account/v3/publishers/list)firmy. Obejmuje właściciela konta, menedżerów i deweloperów, którzy mają dostęp do publikowania. Dowiedz się więcej na temat [ról użytkowników Centrum partnerskiego](./partner-center-portal/manage-account.md#define-user-roles-and-permissions).
2. Podawaj jeden z wymienionych kontaktów, aby [dodać użytkowników](https://partner.microsoft.com/dashboard/account/usermanagement) do komercyjnej witryny Marketplace jako *menedżerów* lub *deweloperów*, ponieważ tylko te role mogą edytować i publikować rozwiązania.
3. Współpracuj z programistami, aby przenieść swoje rozwiązania z konta OCP GTM do komercyjnej witryny Marketplace.
4. Zdecyduj, które z poniższych czynności chcesz wykonać:
    1. Scal to rozwiązanie z podobną ofertą w komercyjnej witrynie Marketplace.
    1. Przeprowadź migrację tego rozwiązania z OCP GTM do komercyjnej witryny Marketplace.
    1. Odrzuć to rozwiązanie.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>Migrowanie rozwiązań z OCP GTM

1. Rozpocznij migrację [tutaj](https://partner.microsoft.com/solutions/migration#).
2. Wybierz stronę **Przegląd** , a następnie **kliknij tutaj, aby** rozpocząć.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="Strona omówienia Centrum partnerskiego, karta przegląd.":::

3. Aby rozpocząć migrację, wybierz kartę **rozwiązania** , w której zostaną wyświetlone wszystkie rozwiązania skojarzone z identyfikatorami MPN.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="Strona omówienia Centrum partnerskiego, karta rozwiązania.":::

    > [!NOTE]
    > Na tej karcie należy zauważyć, że nie ma żadnych rozwiązań oczekujących na przeprowadzenie migracji do komercyjnej witryny Marketplace. Aby kontynuować sprzedawanie z firmą Microsoft, upewnij się, że zmigrowane rozwiązania zostały opublikowane na komercyjnym rynku.

    Dowiedz się więcej o stanie rozwiązania, przeglądając porady dotyczące narzędzi. Wszystkie rozwiązania oczekujące na działanie są wymienione w obszarze **Akcja**.<a name="beginmigration"></a>

4. Wybierz pozycję **Rozpocznij migrację** (Zobacz obraz powyżej) dla rozwiązania, które chcesz zmigrować, a następnie wybierz jedną z następujących opcji:

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="Trzy opcje migracji.":::

### <a name="merge-solution-with-a-similar-offer"></a>Scal rozwiązanie z podobną ofertą

Wybierz tę opcję, jeśli rozwiązanie jest już opublikowane w komercyjnej witrynie Marketplace, a dwa powinny zostać scalone w ramach jednej oferty. Pozwoli to uniknąć tworzenia zduplikowanych ofert.

1. Zidentyfikuj istniejącą ofertę.
    1. Wybierz opcję **Chcę scalić to rozwiązanie z podobną ofertą w komercyjnej witrynie Marketplace** (zobacz **wymaganego obrazu akcji** [powyżej](#beginmigration)).
    1. Karta **Akcja 1** pokazuje na żywo oferty w witrynie Marketplace, z którymi można SKOJARZYĆ rozwiązanie OCP GTM. Wybierz ofertę na żywo z listy, jeśli ją masz. Jeśli nie ma listy ofert do wybrania, wprowadź adres (URL) klienta w witrynie Microsoft AppSource lub witrynie Azure Marketplace.
        [![Karta akcja 1 procesu scalania.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. Wybierz opcję **Kontynuuj**.
1. Zażądaj scalenia.
    1. Karta **Akcja 2** przedstawia wskazówki, aby zażądać scalenia rozwiązania OCP GTM z określonym przez Ciebie. Aby zażądać scalenia, wybierz pozycję **zapisz & skontaktuj się z pomocą techniczną**, co spowoduje otwarcie strony pomocy technicznej dla partnerów w przeglądarce.
    1. Wybierz pozycję **Podaj szczegóły problemu** i wprowadź następujące polecenie: [ ![ kartę Akcja 2 procesu scalania.](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Wybierz pozycję **Prześlij**. Zespół pomocy technicznej partnerskiej skontaktuje się z Tobą w ciągu dwóch dni roboczych.
    1. Pomoc techniczna dla partnerów będzie współdziałać z ty, aby zapewnić pomyślne scalenie tej oferty, aby była opublikowana jako oferta na żywo.

### <a name="migrate-this-solution-from-ocp-gtm"></a>Migruj to rozwiązanie z OCP GTM

Wybierz tę opcję, jeśli możesz mieć rozwiązanie OCP GTM, które nie ma jeszcze odpowiedniej oferty opublikowanej w portalu komercyjnym. Musisz opublikować to rozwiązanie w komercyjnej witrynie Marketplace, aby kontynuować sprzedawanie z firmą Microsoft i przeprowadzenie migracji tego rozwiązania pozwoli zaoszczędzić czas dzięki zachowaniu informacji i rachunku z GTM OCP. Ta opcja wymaga wybrania typu oferty.

1. Wybierz opcję **Chcę migrować to rozwiązanie z OCP GTM do komercyjnej witryny Marketplace** (patrz **wymagane** zdjęcie akcji [powyżej](#beginmigration)), a następnie **Kontynuuj**.
1. Na karcie **Akcja 1** wybierz [Typ oferty](publisher-guide-by-offer-type.md), a następnie **Kontynuuj**.

    [![Karta akcja 1 procesu migracji.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. Na karcie **Akcja 2** wybierz [profil wydawcy](partner-center-portal/create-account.md) z podanej listy. Jeśli nie masz konta wydawcy, utwórz je w [centrum partnerskim](https://partner.microsoft.com/solutions/migration), a następnie wybierz je tutaj.

    [![Karta Akcja 2 procesu migracji.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. Wybierz pozycję **Utwórz wersję roboczą** , aby przeprowadzić migrację rozwiązania do komercyjnej witryny Marketplace jako wersji roboczej. Nie będzie jeszcze na żywo.
1. Kontynuuj proces publikowania w centrum partnerskim. Aby uzyskać pomoc dotyczącą publikowania w centrum partnerskim, zobacz artykuł [wprowadzenie do oferty na żywo w portalu komercyjnym](#make-your-offer-live-in-the-marketplace) poniżej.

### <a name="discard-this-solution"></a>Odrzuć to rozwiązanie

Wybierz tę opcję, jeśli rozwiązanie w GTM Solutions nie ma już znaczenia. Zostanie wyświetlony monit o potwierdzenie odrzucenia, a później można go cofnąć.

1. Wybierz opcję **Chcę odrzucić to rozwiązanie** (Zobacz obraz **wymaganego działania** [powyżej](#beginmigration)), a następnie **Kontynuuj**.
2. Wybierz pozycję **Odrzuć**.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Potwierdź odrzucenie.":::

3. Aby cofnąć odrzucenie, wybierz polecenie **Cofnij Odrzuć**.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="Link Cofnij odrzucenie.":::

4. Jeśli potrzebujesz dodatkowej pomocy, wybierz kartę **Uzyskaj pomoc** , aby skontaktować się z zespołem pomocy technicznej dla partnerów.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="Łącze Pomoc techniczna na karcie Uzyskaj pomoc.":::

## <a name="make-your-offer-live-in-the-marketplace"></a>Złóż swoją ofertę na żywo w portalu Marketplace

Jeśli wybrano migrację oferty do komercyjnej witryny Marketplace, zostanie ona przypisana jako wersja robocza. Nadal musisz opublikować ofertę, aby ją udostępnić na rynku komercyjnym. spowoduje to zachowanie swojego stanu współsprzedaży, bodźców i potoku odwołań.

Szczegółowe instrukcje dotyczące informacji, które należy podać przed opublikowaniem oferty, można znaleźć w odpowiednim [przewodniku publikowania](publisher-guide-by-offer-type.md). Aby uzyskać podsumowanie, Przeczytaj poniżej.

1. Na stronie **Przegląd** w centrum partnerskim wybierz swoją nazwę oferty roboczej.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="Strona przeglądu oferty w centrum partnerskim.":::

<a name="cosell-tab"></a>

2. Wypełnij wymagane informacje na każdej karcie. Opcjonalnie Ukończ stronę **odsprzedaży za pomocą dostawcy CSP** (w menu po lewej stronie poniżej) do odsprzedaży przez program Cloud Solution Provider (CSP). Dodatkowe linki i etykietki narzędzi **zapoznaj** się z wymaganiami i szczegółami.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="Karty Ustawienia oferty przegląd w centrum partnerskim.":::

3. Niektóre szczegóły dotyczące sprzedawcy firmy Microsoft zostały skopiowane za pośrednictwem rozwiązania OCP GTM. Uzupełnij pozostałe wymagane informacje na karcie **współsprzedaż z firmą Microsoft** , aby zaoferować swoją ofertę gotowość do sprzedaży. Po zakończeniu wybierz pozycję **Przejrzyj i Opublikuj**. Aby uzyskać więcej informacji, zobacz [Konfigurowanie współsprzedaży dla komercyjnej oferty portalu Marketplace](commercial-marketplace-co-sell.md).

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="Karta oferta do sprzedaży w centrum partnerskim.":::

4. Po przejrzeniu wszystkich przesłanych informacji wybierz pozycję **Publikuj** , aby przesłać swoją wersję roboczą oferty przeglądu certyfikacji.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="Karta przegląd oferty i przycisk Publikuj w centrum partnerskim.":::

5. Śledź stan przesłane na karcie **Przegląd** .

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="Pasek stanu publikowania oferty na karcie Przegląd w centrum partnerskim.":::

6. Powiadomimy Cię, gdy nasze przeglądy certyfikacji zostaną zakończone. Jeśli postanowisz opinię z możliwością podejmowania działań, skontaktuj się z nią, a następnie wybierz pozycję **Publikuj** , aby zainicjować ponowną certyfikację.
7. Gdy oferta zostanie przekazana, Wyświetl podgląd oferty przy użyciu dostarczonego linku i wprowadź wszelkie ostateczne korekty. Gdy wszystko będzie gotowe, wybierz pozycję **Przejdź na żywo** (patrz przycisk powyżej), aby opublikować ofertę w odpowiednich reklamach portalu Marketplace. Po zakończeniu oferta będzie utrzymywać swój status współsprzedaży z oryginalnego rozwiązania OCP GTM.

## <a name="next-steps"></a>Następne kroki

- [Odsprzedaż za pośrednictwem partnerów CSP](cloud-solution-providers.md)
- [Konfigurowanie współsprzedaży dla komercyjnej oferty portalu Marketplace](commercial-marketplace-co-sell.md)
- Wyświetlanie tych [często zadawanych pytań](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)
