---
title: Sprawdź stan publikowania oferty komercyjnej witryny Marketplace
description: Sprawdź stan weryfikacji, certyfikacji i kroków w wersji zapoznawczej wymaganych do opublikowania oferty za pośrednictwem komercyjnej witryny Marketplace w centrum partnerskim firmy Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275683"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Sprawdź stan publikowania oferty komercyjnej witryny Marketplace

Bieżący **stan publikowania** można wyświetlić na karcie **Omówienie oferty** [portalu komercyjnego Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) w centrum partnerskim.

Dla każdej oferty należy wyświetlić jeden z następujących wskaźników stanu.

| **Stan**    | **Opis**  |
| :---------- | :-------------------|
| **Wersja robocza** | Oferta została utworzona, ale nie jest publikowana. |
| **Publikowanie w toku** | Oferta/plan działa w sposób w ramach kroków procesu publikowania. |
| **Wymagana Uwaga** | Wykryto problem krytyczny podczas certyfikacji przez firmę Microsoft lub dowolny z kroków publikacji. |
| **Przeglądania** | Oferta została certyfikowana przez firmę Microsoft i teraz czeka na ostateczną weryfikację przez wydawcę. Wybierz pozycję Przejdź na żywo, aby utworzyć ofertę na żywo. |
| **Na żywo** | Oferta jest aktywna w portalu Marketplace i może być widoczna i nabywana przez klientów. |
| **Oczekiwanie na zaprzestanie sprzedawania** | Wydawca zaznaczył opcję "Zatrzymaj sprzedaż" w ofercie lub planie, ale akcja nie została jeszcze ukończona. |
| **Niedostępne w portalu Marketplace** | Wcześniej opublikowana oferta/plan w portalu Marketplace został usunięty. |

## <a name="automated-validation"></a>Automatyczne sprawdzanie poprawności

Pierwszym krokiem w procesie publikowania jest zestaw zautomatyzowanych walidacji. Każdy krok walidacji odpowiada funkcji wybranej podczas tworzenia oferty. Jeśli ta funkcja nie została włączona, walidacja zostanie pominięta w następnym kroku publikowania. Przed zatwierdzeniem stanu publikacji należy wykonać każde sprawdzenie poprawności.

- **Konfiguracja oferty zakupu (<10 min)**

W tym kroku firma Microsoft gwarantuje, że oferta może zostać zrealizowana w przypadku zakupu przez klientów za pomocą Azure Portal. Ten krok ma zastosowanie tylko w przypadku ofert sprzedawanych przez firmę Microsoft.

- **Sprawdzanie poprawności danych na dysku testowym (~ 5 min)**

W tym kroku sprawdzimy dane podane w sekcji testowa konfiguracja techniczna w ramach tej oferty. Testowanie i zatwierdzanie funkcji na dysku. Ten krok ma zastosowanie tylko w przypadku ofert z włączonym dyskiem testowym.

- **Obsługa administracyjna stacji testowej (~ 30 min)**

W tym kroku po zweryfikowaniu danych i funkcjonalności dysku testowego w poprzednim kroku wdrażamy i replikujemy wystąpienia dysku testowego, aby były gotowe do użycia przez klienta.  Ten krok ma zastosowanie tylko w przypadku ofert z włączonym dyskiem testowym.

- **Sprawdzanie poprawności i rejestracja zarządzania potencjalnymi klientami (<15 min)**

W tym kroku potwierdzamy, że Twój system zarządzania potencjalnym klientem może odbierać potencjalnych klientów na podstawie szczegółowych informacji podanych w ustawieniach oferty. Ten krok dotyczy tylko ofert z włączonym zarządzaniem potencjalnym klientem.

## <a name="certification"></a>Certyfikacja

Przed opublikowaniem oferty przesłane do komercyjnej witryny Marketplace w centrum partnerskim muszą być certyfikowane. Przesłane oferty przechodzą rygorystyczne testy, kilka zautomatyzowanych i innych podręczników, w tym sprawdzanie [zasad certyfikacji portalu Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). Przed przejściem do następnego kroku w przepływie publikowania należy zaznaczyć odpowiednie przesłania do certyfikatu.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typy walidacji, które mają miejsce podczas certyfikacji

W procesie certyfikacji uwzględniono trzy poziomy weryfikacji dla każdej przesłanej oferty.

- Kwalifikacje biznesowe wydawcy
- Weryfikacja zawartości
- Weryfikacja techniczna

#### <a name="publisher-business-eligibility"></a>Kwalifikacje biznesowe wydawcy

Każdy typ oferty sprawdza zestaw podstawowych kryteriów kwalifikacji, które musi spełnić Wydawca. Kryteria kwalifikujące mogą obejmować stan MPN wydawcy, posiadane kompetencje, poziomy kompetencji itp.

#### <a name="content-validation"></a>Weryfikacja zawartości

Podczas weryfikacji zawartości informacje wprowadzane podczas tworzenia oferty są sprawdzane pod kątem jakości i przydatności. Te sprawdzenia zapoznają się z wpisami dotyczącymi szczegółowych informacji dotyczących aukcji portalu Marketplace, cen, dostępności, skojarzonych planów itp. Aby spełnić kryteria dotyczące aukcji portalu Azure Marketplace i/lub AppSource, sprawdzimy, czy oferta obejmuje:

- tytuł, który dokładnie opisuje ofertę;
- dobrze napisano opisy, które zapewniają szczegółowe omówienie i propozycję wartości;
- Zrzuty ekranu jakości i dołączone wideo lub
- wyjaśnienie, jak oferta używa platform i narzędzi firmy Microsoft.

Dowiedz się więcej na temat kryteriów weryfikacji zawartości, odczytując [ogólne zasady wyświetlania](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Weryfikacja techniczna

Podczas weryfikacji technicznej oferta (pakiet lub plik binarny) jest poddawana następującym testom.
- Skanowanie w poszukiwaniu złośliwego oprogramowania
- Monitorowane wywołania sieciowe
- Przeanalizowane pakiety
- Dokładne skanowanie rzeczywistej funkcji oferty

Oferta jest testowana między różnymi platformami i wersjami w celu zapewnienia niezawodnego działania.

Przejrzyj szczegółowe informacje dotyczące konfiguracji wymagane w ramach oferty w sekcji konfiguracja techniczna tego dokumentu.

### <a name="certification-failure-report"></a>Raport o błędach certyfikacji

Po zakończeniu przeglądu, jeśli oferta została przeniesiona, przejdzie do następnego kroku procesu publikowania. Jeśli Twoja oferta zakończyła się niepowodzeniem na liście, techniczne lub sprawdzanie zasad lub jeśli nie masz uprawnień do przesłania oferty tego typu, raport o błędach certyfikacji jest generowany i wysyłany pocztą e-mail.

Ten raport zawiera opisy wszelkich zasad, które zakończyły się niepowodzeniem, a także uwagi dotyczące recenzowania. Przejrzyj ten raport wiadomości e-mail, Rozwiąż wszelkie problemy, w razie potrzeby, wprowadzając aktualizacje oferty, a następnie prześlij ją ponownie za pomocą [portalu komercyjnej witryny Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) w centrum partnerskim. (Ofertę można przesłać ponownie dowolną liczbę razy do momentu przekazania certyfikatu).

## <a name="preview-creation"></a>Tworzenie podglądu

W ramach kroku **tworzenia podglądu** tworzymy wersję oferty dostępną tylko dla odbiorców określonych w sekcji w wersji zapoznawczej oferty.

>[!Note]
> Nie należy używać tego kroku, aby zapewnić osobom spoza organizacji wgląd w ofertę. Zamiast tego użyj opcji **oferta prywatna** . W tym momencie Twoja oferta nie została w pełni przetestowana i zweryfikowana i nie jest gotowa do dystrybucji poza nią.

## <a name="publisher-approval"></a>Zatwierdzenie wydawcy

W tym kroku zostanie wysłana wiadomość e-mail z prośbą o przejrzenie i zatwierdzenie wersji zapoznawczej oferty przed ostatnim krokiem publikacji.

Jeśli wybrano sprzedawanie oferty przez firmę Microsoft, można przetestować pozyskiwanie i wdrażanie oferty, aby upewnić się, że spełnia Twoje wymagania na etapie zatwierdzania wersji zapoznawczej. Twoja oferta nie będzie jeszcze dostępna na publicznej platformie Marketplace. Po przetestowaniu i zaakceptowaniu tej wersji zapoznawczej musisz wybrać pozycję **Przejdź na żywo** na pulpicie nawigacyjnym [**przeglądu oferty**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

Jeśli chcesz wprowadzić zmiany w ofercie na tym etapie wersji zapoznawczej, możesz edytować i ponownie przesłać w celu opublikowania nowej wersji zapoznawczej. Zapoznaj się z artykułem [aktualizowanie istniejących ofert w portalu Marketplace](#update-existing-marketplace-offers) , aby uzyskać szczegółowe informacje na temat dodatkowych zmian.

Jeśli Twoja oferta już działa i jest dostępna publicznie w portalu Marketplace, wszystkie aktualizacje, które wprowadzisz, nie będą dostępne, dopóki nie wybierzesz opcji przejdź do usługi **Live** na pulpicie nawigacyjnym [**przeglądu oferty**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

### <a name="publish-offer-to-the-public"></a>Publikuj ofertę publicznie

Zaloguj się do Centrum partnerskiego i uzyskaj dostęp do oferty. Nastąpi przekierowanie do strony **przeglądu oferty** . W górnej części tej strony zobaczysz opcję dla opcji **Przejdź na żywo**. Wybierz pozycję **Przejdź na żywo** i po potwierdzeniu, że oferta zacznie być opublikowana publicznie. Gdy oferta będzie aktywna, otrzymasz wiadomość e-mail z powiadomieniem.

## <a name="publish"></a>Publikowanie

Teraz, **gdy zamierzasz korzystać z oferty** , udostępniając ją w portalu Marketplace, istnieje szereg końcowych testów weryfikacyjnych, które zostaną przełączone w celu zapewnienia, że usługa Live Offer zostanie skonfigurowana tak jak wersja zapoznawcza oferty.

- **Konfiguracja oferty zakupu (>10 min)**

W tym kroku firma Microsoft gwarantuje, że oferta może zostać zrealizowana w przypadku zakupu przez klientów za pomocą Azure Portal. Ten krok ma zastosowanie tylko w przypadku ofert sprzedawanych przez firmę Microsoft.

- **Sprawdzanie poprawności danych na dysku testowym (~ 5 min)**

W tym kroku sprawdzimy dane podane w sekcji testowa konfiguracja techniczna w ramach tej oferty. Testowanie i zatwierdzanie funkcji na dysku. Ten krok ma zastosowanie tylko w przypadku ofert z włączonym dyskiem testowym.

- **Obsługa administracyjna stacji testowej (~ 30 min)**

W tym kroku wdrażamy i replikujemy wystąpienia dysku testowego, aby były gotowe do użycia przez klienta.  Ten krok ma zastosowanie tylko w przypadku ofert z włączonym dyskiem testowym.

- **Sprawdzanie poprawności i rejestracja zarządzania potencjalnymi klientami (>15 min)**

W tym kroku potwierdzamy, że Twój system zarządzania potencjalnym klientem może odbierać potencjalnych klientów na podstawie szczegółowych informacji podanych w ustawieniach oferty. Ten krok dotyczy tylko ofert z włączonym zarządzaniem potencjalnym klientem.

- **Publikowanie końcowe (>30 minut)**

W tym kroku zagwarantujemy, że oferta będzie publicznie dostępna w portalu Marketplace.

## <a name="update-existing-marketplace-offers"></a>Aktualizowanie istniejących ofert w portalu Marketplace

Jeśli chcesz wprowadzić zmiany w już opublikowanej ofercie, musisz najpierw zaktualizować istniejącą ofertę, a następnie opublikować ją ponownie.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
