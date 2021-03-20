---
title: Testowanie i publikowanie oferty aplikacji platformy Azure
description: Skorzystaj z Centrum partnerskiego, aby przesłać ofertę aplikacji platformy Azure w wersji zapoznawczej, wyświetlić podgląd oferty, test, a następnie opublikować ją w komercyjnym portalu Microsoft Marketplace.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 461f9354bb3a6eae0af186de8fe9f39c6b5fff2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620923"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>Testowanie i publikowanie oferty aplikacji platformy Azure

W tym artykule wyjaśniono, jak za pomocą Centrum partnerskiego przesłać ofertę aplikacji platformy Azure do opublikowania, wyświetlić podgląd oferty, przetestować ją, a następnie opublikować ją na żywo na komercyjnej platformie Marketplace. Musisz mieć już utworzoną ofertę, która ma zostać opublikowana.

## <a name="submit-your-offer-for-publishing"></a>Prześlij swoją ofertę na potrzeby publikowania

1. Zaloguj się do komercyjnego pulpitu nawigacyjnego Marketplace w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Na stronie **Przegląd** wybierz ofertę, którą chcesz opublikować.
1. W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i Opublikuj**.
1. Upewnij się, że kolumna **stan** dla każdej strony jest wyświetlana jako **zakończona**. Trzy możliwe stany są następujące:
    - **Nieuruchomione** — strona jest niepełna.
    - **Niekompletne** — Strona nie zawiera wymaganych informacji lub zawiera błędy, które muszą zostać naprawione. Musisz wrócić do strony i zaktualizować ją.
    - **Ukończono** — Strona została ukończona. Wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy.
1. Jeśli którakolwiek ze stron ma stan inny niż **ukończono**, wybierz nazwę strony, usuń problem, Zapisz stronę, a następnie wybierz pozycję **Przejrzyj i Opublikuj** ponownie, aby powrócić do tej strony.
1. Po zakończeniu wszystkich stron w polu **Informacje o certyfikacji** wprowadź instrukcje testowania do zespołu certyfikacji, aby upewnić się, że aplikacja jest poprawnie testowana. Podaj wszelkie dodatkowe uwagi pomocne przy zrozumieniu swojej aplikacji.
1. Aby rozpocząć proces publikowania dla oferty, wybierz pozycję **Publikuj**. Zostanie wyświetlona strona **Przegląd oferty** z widocznym **stanem publikowania** oferty.

Stan publikowania oferty zmieni się w miarę ich przechodzenia przez proces publikacji. Aby uzyskać szczegółowe informacje na temat tego procesu, zobacz [Walidacja i publikowanie kroków](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Wyświetl podgląd i przetestuj ofertę

Gdy oferta będzie gotowa do wylogowania, wyślemy do Ciebie wiadomość e-mail z prośbą o przejrzenie i zatwierdzenie wersji zapoznawczej oferty. Możesz również odświeżyć stronę **przeglądu oferty** w przeglądarce, aby sprawdzić, czy Twoja oferta osiągnęła fazę wylogowania wydawcy. Jeśli ma, dostępny jest przycisk **Przejdź na żywo** i link do wersji zapoznawczej. W przypadku wybrania opcji sprzedaży oferty przez firmę Microsoft każda osoba, która została dodana do odbiorców w wersji zapoznawczej, może przetestować nabycie i wdrożenie oferty, aby upewnić się, że spełnia Twoje wymagania na tym etapie.

Poniższy zrzut ekranu przedstawia stronę **omówienia oferty** dla oferty z dwoma linkami w wersji zapoznawczej w obszarze przycisk **Przejdź na żywo** . Kroki walidacji, które zobaczysz na tej stronie, różnią się w zależności od wyborów dokonanych podczas tworzenia oferty.

[![Przedstawia stronę omówienia oferty dla oferty w centrum partnerskim. Widoczny jest przycisk Przejdź na żywo i linki do wersji zapoznawczej.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

Wykonaj poniższe kroki, aby wyświetlić podgląd oferty.

1. Na stronie **Przegląd oferty** wybierz link do wersji zapoznawczej w obszarze przycisk **Przejdź na żywo** . 

1. Aby sprawdzić, czy jest to kompleksowy przepływ zakupów i konfiguracji, Kup swoją ofertę, gdy jest ona dostępna w wersji zapoznawczej. Najpierw Powiadom firmę Microsoft za pomocą [biletu pomocy technicznej](https://aka.ms/marketplacesupport) , aby upewnić się, że nie przetwarzamy opłaty.

1. Jeśli aplikacja platformy Azure obsługuje [taryfowe rozliczanie za pomocą komercyjnej usługi pomiarów w portalu Marketplace](./partner-center-portal/azure-app-metered-billing.md), przejrzyj i postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi testowania, które opisano w temacie [interfejsy API rozliczeń](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)

1. Jeśli musisz wprowadzić zmiany po wyświetleniu podglądu i przetestowaniu oferty, możesz je edytować i przesłać ponownie w celu opublikowania nowej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publikuj swoją ofertę na żywo

Po zakończeniu wszystkich testów w wersji zapoznawczej wybierz pozycję **Przejdź na żywo** , aby opublikować swoją ofertę na żywo na rynku komercyjnym.

   > [!TIP]
   > Jeśli Twoja oferta już działa w komercyjnej witrynie Marketplace, wszystkie aktualizacje, które wprowadzisz, nie będą dostępne do momentu wybrania pozycji **Przejdź na żywo**.

Teraz, gdy wybrano opcję udostępnienia oferty w komercyjnej witrynie Marketplace, przeprowadzamy serię ostatecznych testów weryfikacyjnych, aby upewnić się, że oferta na żywo jest skonfigurowana tak jak wersja zapoznawcza oferty. Aby uzyskać szczegółowe informacje o tych kontrolach weryfikacji, zobacz [Publikowanie faz](review-publish-offer.md#publish-phase).

Po zakończeniu sprawdzania poprawności oferta będzie aktywna w portalu Marketplace.

### <a name="errors-and-review-feedback"></a>Błędy i przejrzyj opinię

Krok **walidacji ręcznej** w procesie publikowania reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych (zwłaszcza szablon Azure Resource Manager), które są zazwyczaj prezentowane jako linki żądania ściągnięcia. Informacje na temat sposobu wyświetlania tych żądań ściągnięcia i reagowania na nie można znaleźć w temacie [Obsługa informacji zwrotnych](partner-center-portal/azure-apps-review-feedback.md).

Jeśli występują błędy w co najmniej jednym z kroków publikowania, popraw je przed ponownym opublikowaniem oferty.

## <a name="next-step"></a>Następny krok

- [Dostęp do raportów analitycznych dla komercyjnego portalu Marketplace w centrum partnerskim](partner-center-portal/analytics.md)
- Dowiedz się, [jak sprzedawać oferty aplikacji platformy Azure](create-new-azure-apps-offer-marketing.md) w ramach współsprzedaży z firmą Microsoft i odsprzedawać je za pomocą programów CSP.
