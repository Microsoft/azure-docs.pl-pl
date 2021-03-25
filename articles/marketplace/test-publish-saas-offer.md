---
title: Testowanie i publikowanie oferty SaaS na rynku komercyjnym firmy Microsoft
description: Użyj Centrum partnerskiego, aby przesłać ofertę SaaS do wersji zapoznawczej, wyświetlić podgląd oferty, test, a następnie opublikować ją na rynku komercyjnym firmy Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/25/2021
ms.openlocfilehash: aeab671c9283d64f9c1ca37cf184b80b1eca8f35
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045250"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Testowanie i publikowanie oferty SaaS na rynku komercyjnym

W tym artykule wyjaśniono, jak za pomocą Centrum partnerskiego przesłać ofertę SaaS do opublikowania, wyświetlić podgląd oferty, przetestować ją, a następnie opublikować ją na żywo na komercyjnej platformie Marketplace. Musisz mieć już utworzoną ofertę, która ma zostać opublikowana.

> [!NOTE]
> Zalecamy utworzenie oddzielnej oferty testowej i programistycznej (DEV) jako niskiego ryzyka, aby przeprowadzić test przed opublikowaniem oferty produkcyjnej (PROD). Wykonaj następujące kroki, aby utworzyć i przetestować ofertę DEV przed opublikowaniem oferty (PROD).

## <a name="submit-your-offer-for-publishing"></a>Prześlij swoją ofertę na potrzeby publikowania

1. Zaloguj się do komercyjnego pulpitu nawigacyjnego Marketplace w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Na stronie **Przegląd** wybierz ofertę, którą chcesz opublikować.
1. W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i Opublikuj**.
2. Upewnij się, że kolumna **stan** dla każdej strony jest wyświetlana jako **zakończona**. Trzy możliwe stany są następujące:

   - **Nieuruchomione** — strona jest niepełna.
   - **Niekompletne** — Strona nie zawiera wymaganych informacji lub zawiera błędy, które muszą zostać naprawione. Musisz wrócić do strony i zaktualizować ją.
   - **Ukończono** — Strona została ukończona. Wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy.

1. Jeśli którakolwiek ze stron ma stan inny niż **ukończono**, wybierz nazwę strony, usuń problem, Zapisz stronę, a następnie wybierz pozycję **Przejrzyj i Opublikuj** ponownie, aby powrócić do tej strony.
1. Po zakończeniu wszystkich stron w polu **Informacje o certyfikacji** wprowadź instrukcje testowania do zespołu certyfikacji, aby upewnić się, że aplikacja jest poprawnie testowana. Podaj wszelkie dodatkowe uwagi pomocne przy zrozumieniu swojej aplikacji.
1. Aby rozpocząć proces publikowania dla oferty, wybierz pozycję **Publikuj**. Zostanie wyświetlona strona **Przegląd oferty** z widocznym **stanem publikowania** oferty.

Stan publikowania oferty zmieni się w miarę ich przechodzenia przez proces publikacji. Aby uzyskać szczegółowe informacje na temat tego procesu, zobacz [Walidacja i publikowanie kroków](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Wyświetl podgląd i przetestuj ofertę

Gdy oferta będzie gotowa do wylogowania, wyślemy do Ciebie wiadomość e-mail z prośbą o przejrzenie i zatwierdzenie wersji zapoznawczej oferty. Możesz również odświeżyć stronę **przeglądu oferty** w przeglądarce, aby sprawdzić, czy Twoja oferta osiągnęła fazę wylogowania wydawcy. Jeśli ma, dostępny jest przycisk **Przejdź na żywo** i linki do wersji zapoznawczej. Będzie dostępny link Microsoft AppSource do wersji zapoznawczej, wersji zapoznawczej witryny Azure Marketplace lub w zależności od opcji wybranych podczas tworzenia oferty. W przypadku wybrania opcji sprzedaży oferty przez firmę Microsoft każda osoba, która została dodana do odbiorców w wersji zapoznawczej, może przetestować nabycie i wdrożenie oferty, aby upewnić się, że spełnia Twoje wymagania na tym etapie.

Poniższy zrzut ekranu przedstawia stronę **omówienia oferty** dla oferty SaaS z dwoma linkami w wersji zapoznawczej w obszarze przycisk **Przejdź na żywo** . Kroki walidacji, które zobaczysz na tej stronie, różnią się w zależności od wyborów dokonanych podczas tworzenia oferty.

![Przedstawia stronę omówienia oferty dla oferty w centrum partnerskim. Widoczny jest przycisk Przejdź na żywo i linki do wersji zapoznawczej. Łącze Wyświetl raport weryfikacji jest również wyświetlane w obszarze Automatyczne sprawdzanie poprawności.](./media/review-publish-offer/publish-status-saas.png)

Wykonaj poniższe kroki, aby wyświetlić podgląd oferty.

1. Na stronie **Przegląd oferty** wybierz link do wersji zapoznawczej w obszarze przycisk **Przejdź na żywo** .

1. Aby sprawdzić, czy jest to kompleksowy przepływ zakupów i konfiguracji, Kup plany w ofercie, gdy jest ona dostępna w wersji zapoznawczej. Najpierw Powiadom firmę Microsoft za pomocą [biletu pomocy technicznej](https://aka.ms/marketplacesupport) , aby upewnić się, że nie przetwarzamy opłaty.

1. Jeśli oferta SaaS obsługuje [naliczanie opłat naliczanych przy użyciu komercyjnej usługi pomiarów w portalu Marketplace](./partner-center-portal/saas-metered-billing.md), należy zapoznać się z najlepszymi rozwiązaniami dotyczącymi testowania i postępować zgodnie z informacjami w temacie [interfejsy API Rozlicz](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)

1. Zapoznaj się z instrukcjami dotyczącymi testowania w [interfejsie API realizacji usługi SaaS w wersji 2 w portalu komercyjnym firmy Microsoft,](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) aby upewnić się, że oferta została pomyślnie zintegrowana z interfejsami API, zanim udostępnisz ofertę na żywo.

1. Jeśli etap weryfikacji oferty spowodował ostrzeżenia, na stronie **przeglądu oferty** pojawia się łącze **Wyświetl raport weryfikacji** . Pamiętaj, aby przejrzeć raport i rozwiązać problemy przed wybraniem przycisku Przejdź na **żywo** . W przeciwnym razie certyfikaty najprawdopodobniej zakończą się niepowodzeniem i opóźnią swoją ofertę na żywo.

1. Jeśli musisz wprowadzić zmiany po wyświetleniu podglądu i przetestowaniu oferty, możesz je edytować i przesłać ponownie w celu opublikowania nowej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publikuj swoją ofertę na żywo

Po zakończeniu wszystkich testów w wersji zapoznawczej wybierz pozycję **Przejdź na żywo** , aby opublikować swoją ofertę na żywo na rynku komercyjnym. Jeśli Twoja oferta już działa w komercyjnej witrynie Marketplace, wszystkie aktualizacje, które wprowadzisz, nie będą dostępne do momentu wybrania pozycji **Przejdź na żywo**.

> [!IMPORTANT]
> Nie musisz wybierać opcji **Przejdź na żywo** w celu [tworzenia i testowania oferty](create-saas-dev-test-offer.md).

Teraz, gdy wybrano opcję udostępnienia oferty w komercyjnej witrynie Marketplace, przeprowadzamy serię ostatecznych testów weryfikacyjnych, aby upewnić się, że oferta na żywo jest skonfigurowana tak jak wersja zapoznawcza oferty. Aby uzyskać szczegółowe informacje o tych kontrolach weryfikacji, zobacz [Publikowanie faz](review-publish-offer.md#publish-phase).

Po zakończeniu sprawdzania poprawności oferta będzie aktywna w portalu Marketplace.

## <a name="next-steps"></a>Następne kroki

- [Dostęp do raportów analitycznych dla komercyjnego portalu Marketplace w centrum partnerskim](./partner-center-portal/analytics.md)
