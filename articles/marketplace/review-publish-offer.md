---
title: Przeglądanie i publikowanie oferty w portalu komercyjnym firmy Microsoft
description: Skorzystaj z Centrum partnerskiego, aby przesłać ofertę do wersji zapoznawczej, wyświetlić podgląd oferty, a następnie opublikować ją w komercyjnym portalu Microsoft Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 08/12/2020
ms.openlocfilehash: 53e5331ab36d5466bd5cdb495ac505dae29e57bf
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142302"
---
# <a name="review-and-publish-an-offer-to-the-commercial-marketplace"></a>Przeglądanie i publikowanie oferty w portalu komercyjnym

W tym artykule przedstawiono sposób użycia Centrum partnerskiego w celu przesłania oferty do wersji zapoznawczej, przejrzenia oferty, a następnie opublikowania jej w komercyjnym portalu Microsoft Marketplace. Omówiono również sposób sprawdzania stanu publikowania w trakcie wykonywania kroków publikacji. Musisz mieć już utworzoną ofertę, która ma zostać opublikowana.

## <a name="go-to-your-offer-in-the-commercial-marketplace"></a>Przejdź do oferty w komercyjnej witrynie Marketplace

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu nawigacji po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na karcie **Przegląd** w obszarze **oferty**w kolumnie **stan** dla każdej oferty zostanie wyświetlony jeden z następujących wskaźników stanu.

    | Stan | Opis |
    | ------------ | ------------- |
    | Wersja robocza | Oferta została utworzona, ale nie jest publikowana. |
    | Publikowanie w toku | Oferta działa w sposób w trakcie procesu publikowania. |
    | Wymagana Uwaga | Wykryliśmy problem krytyczny podczas certyfikacji lub w innej fazie publikowania. |
    | Wersja zapoznawcza | Firma Microsoft potwierdziła ofertę, która teraz czeka na ostateczną weryfikację przez wydawcę. Wybierz pozycję **Przejdź na żywo** , aby opublikować ofertę na żywo. |
    | Na żywo | Oferta jest aktywna w portalu Marketplace i może być widoczna i nabywana przez klientów. |
    | Oczekiwanie na zaprzestanie sprzedawania | Wydawca zaznaczył "Zatrzymaj sprzedawanie" w ofercie lub planie, ale akcja nie została jeszcze ukończona. |
    | Niedostępne w portalu Marketplace | Wcześniej opublikowana oferta w portalu Marketplace została usunięta. |
    |||

4. W kolumnie **alias oferty** wybierz ofertę, którą chcesz wyświetlić i opublikować.

## <a name="submit-your-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

1. Aby przesłać ofertę do wersji zapoznawczej, wybierz pozycję **Przejrzyj i Opublikuj** w prawym górnym rogu portalu. Zostanie wyświetlona strona **przeglądanie i publikowanie** .
1. Upewnij się, że kolumna **stan** dla każdej strony jest wyświetlana jako **zakończona**. Trzy możliwe stany są następujące:

   - **Nieuruchomione** — strona jest niepełna.
   - **Niekompletne** — Strona nie zawiera wymaganych informacji lub zawiera błędy, które muszą zostać naprawione. Musisz wrócić do strony i zaktualizować ją.
   - **Ukończono** — Strona została ukończona. Wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy.

1. Jeśli którakolwiek ze stron ma stan inny niż **ukończono**, w kolumnie **Strona** wybierz nazwę strony, usuń problem, Zapisz stronę, a następnie wybierz pozycję **Przejrzyj i Opublikuj** ponownie, aby powrócić do tej strony.
1. Po zakończeniu wszystkich stron w polu **Informacje o certyfikacji** wprowadź instrukcje testowania do zespołu certyfikacji, aby upewnić się, że aplikacja jest poprawnie testowana. Podaj wszelkie dodatkowe uwagi pomocne przy zrozumieniu swojej aplikacji.
1. Aby przesłać ofertę do publikacji, wybierz pozycję **Publikuj**. Zostanie wyświetlona strona **Przegląd oferty** z widocznym stanem publikowania.

## <a name="validation-and-publishing-steps"></a>Sprawdzanie poprawności i publikowanie kroków

Po wybraniu opcji **Publikuj**procesy weryfikacji i publikowania są wykonywane w pożądanej kolejności. W tej tabeli przedstawiono najbardziej typowy proces publikowania:

| Faza | Co się dzieje |
| ------------ | ------------- | ------------- |
| Automatyczne sprawdzanie poprawności | Przetwarzamy zestaw zautomatyzowanych walidacji. |
| Certyfikacja | Przeprowadzamy ręczne walidacje. |
| Tworzenie podglądu | Strona aukcji dla wersji zapoznawczej oferty jest dostępna dla każdego, kto ma link do wersji zapoznawczej. Jeśli oferta będzie sprzedawana w firmie Microsoft (transacted), tylko odbiorcy określeni na stronie **odbiorca wersji zapoznawczej** oferty będą mogli kupować i uzyskiwać dostęp do oferty do testowania. |
| Wylogowanie wydawcy | Wyślemy do Ciebie wiadomość e-mail z prośbą o przeszukanie i zaakceptowanie oferty. |
| Opublikuj | Uruchamiamy serię kroków, aby sprawdzić, czy oferta wersji zapoznawczej została opublikowana na żywo na rynku komercyjnym. |
|||

## <a name="automated-validation-phase"></a>Faza automatycznej weryfikacji

Pierwszym krokiem w procesie publikowania jest zestaw zautomatyzowanych walidacji. Każdy krok walidacji odpowiada funkcji wybranej podczas tworzenia oferty. Przed przejściem do następnego kroku w procesie publikowania należy wykonać każde sprawdzenie poprawności.

- **Konfiguracja oferty zakupu** (<10 min)

   Firma Microsoft gwarantuje, że oferta może zostać zrealizowana w przypadku zakupu przez klientów w Azure Portal. Ten krok ma zastosowanie tylko w przypadku ofert sprzedawanych przez firmę Microsoft.

- **Sprawdzanie poprawności danych na dysku testowym** (~ 5 min)

   Weryfikujemy dane podane na stronie konfiguracji technicznej oferty. Testujemy i zatwierdzimy funkcję testowania dysków. Ten krok ma zastosowanie tylko w przypadku ofert z włączonym dyskiem testowym.

- **Obsługa administracyjna stacji testowej** (~ 30 min)

    Po zweryfikowaniu danych i funkcjonalności dysku testowego w poprzednim kroku wdrażamy i replikujemy wystąpienia dysku testowego, aby były gotowe do użycia przez klienta. Ten krok ma zastosowanie tylko w przypadku ofert z włączonym dyskiem testowym.

- **Sprawdzanie poprawności i rejestracja zarządzania potencjalnymi klientami** (<15 min)

    Upewnij się, że Twój system zarządzania potencjalnymi klientami może odbierać potencjalnych klientów w oparciu o szczegóły podane na stronie **Konfiguracja oferty** . Ten krok dotyczy tylko ofert z włączonym zarządzaniem potencjalnym klientem.

## <a name="certification-phase"></a>Faza certyfikacji

Oferty przesłane do komercyjnej witryny Marketplace muszą być certyfikowane przed opublikowaniem. Oferty przechodzą rygorystyczne testy, kilka zautomatyzowanych i innych ręcznych. Aby dowiedzieć się więcej, zobacz [komercyjne zasady certyfikacji witryny Marketplace](https://aka.ms/commercial-marketplace-certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Typy walidacji, które mają miejsce podczas certyfikacji

W procesie certyfikacji dla każdej przesłanej oferty istnieją trzy poziomy weryfikacji.

- Kwalifikacje biznesowe wydawcy
- Weryfikacja zawartości
- Weryfikacja techniczna

#### <a name="publisher-business-eligibility"></a>Kwalifikacje biznesowe wydawcy

Każdy typ oferty sprawdza zestaw wymaganych podstawowych kryteriów uprawnień. Te kryteria mogą obejmować stan MPN wydawcy, posiadane kompetencje, poziomy kompetencji i tak dalej.

#### <a name="content-validation"></a>Weryfikacja zawartości

Informacje wprowadzone podczas tworzenia oferty są sprawdzane pod kątem jakości i przydatności. Te sprawdzenia zapoznają się z wpisami dotyczącymi szczegółowych informacji, cen, dostępności, skojarzonych planów i tak dalej. Aby spełnić kryteria oferty w witrynie Azure Marketplace i Microsoft AppSource, sprawdzimy, czy oferta obejmuje:

- Tytuł, który dokładnie opisuje ofertę
- Dobrze napisano opisy zawierające szczegółowe omówienie i propozycję wartości
- Zrzuty ekranu i wideo dotyczące jakości
- Wyjaśnienie, jak oferta używa platform i narzędzi firmy Microsoft.

Dowiedz się więcej na temat kryteriów weryfikacji zawartości, odczytując [ogólne zasady wyświetlania](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Weryfikacja techniczna

Podczas weryfikacji technicznej oferta (pakiet lub plik binarny) jest poddawana następującym testom.

- Skanowanie w poszukiwaniu złośliwego oprogramowania
- Monitorowane wywołania sieciowe
- Przeanalizowane pakiety
- Dokładne skanowanie funkcji oferty

Oferta jest testowana między różnymi platformami i wersjami, aby zapewnić jej niezawodność.

### <a name="certification-failure-report"></a>Raport o błędach certyfikacji

Jeśli oferta nie zostanie wyświetlona na liście, techniczne lub sprawdzanie zasad lub jeśli nie masz uprawnień do przesłania oferty tego typu, wyślemy wiadomość e-mail z raportem o niepowodzeniach certyfikacji.

Ten raport zawiera opisy wszelkich zasad, które zakończyły się niepowodzeniem, a także uwagi dotyczące recenzowania. Przejrzyj ten raport wiadomości e-mail, Rozwiąż wszelkie problemy, w razie potrzeby wprowadź aktualizacje oferty, a następnie prześlij ją ponownie za pomocą [portalu komercyjnej witryny Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) w centrum partnerskim. Ofertę można przesłać ponownie dowolną liczbę razy do momentu przekazania certyfikatu.

## <a name="preview-creation-phase"></a>Faza tworzenia podglądu

W fazie tworzenia podglądu tworzymy wersję oferty, która będzie dostępna tylko dla odbiorców określonych na stronie **odbiorcy wersji zapoznawczej** oferty (jeśli istnieje). Wersja zapoznawcza oferty nie będzie dostępna dla wszystkich użytkowników spoza wersji zapoznawczej do momentu opublikowania oferty na żywo.

> [!NOTE]
> Nie używaj odbiorców w wersji zapoznawczej, aby umożliwić osobom spoza organizacji wgląd w ofertę. Zamiast tego użyj opcji oferta prywatna. W tym momencie Twoja oferta nie została w pełni przetestowana i zweryfikowana i nie jest gotowa do dystrybucji poza nią.

## <a name="publisher-sign-off-phase"></a>Faza wylogowania wydawcy

Gdy oferta będzie gotowa do przejrzenia i wylogowania, wyślemy wiadomość e-mail z prośbą o zażądanie przejrzenia i zaakceptowania wersji zapoznawczej oferty. Możesz również odświeżyć stronę **przeglądu oferty** w przeglądarce, aby sprawdzić, czy Twoja oferta osiągnęła fazę wylogowania wydawcy. Jeśli ma, dostępny jest przycisk **Przejdź na żywo** i linki do wersji zapoznawczej.

Poniższy zrzut ekranu przedstawia stronę **omówienia oferty** dla oferty SaaS. Kroki walidacji, które zobaczysz na tej stronie, różnią się w zależności od typu oferty i opcji dokonanych podczas tworzenia oferty.

![Przedstawia stronę omówienia oferty dla oferty w centrum partnerskim. Widoczny jest przycisk Przejdź na żywo i linki do wersji zapoznawczej.](./partner-center-portal/media/publish-status-publisher-signoff.png)

### <a name="to-preview-your-offer-and-sign-off"></a>Aby wyświetlić podgląd oferty i się wylogować

1. Na stronie **Przegląd oferty** , aby wyświetlić podgląd oferty, wybierz link pod przyciskiem **Przejdź na żywo** .

   > [!NOTE]
   > Będzie dostępny link do wersji zapoznawczej AppSource, wersji zapoznawczej platformy Azure Marketplace lub w zależności od opcji wybranych podczas tworzenia oferty. W przypadku wybrania opcji sprzedaży oferty przez firmę Microsoft każda osoba, która została dodana do odbiorców w wersji zapoznawczej, może przetestować nabycie i wdrożenie oferty, aby upewnić się, że spełnia Twoje wymagania na tym etapie.

1. Jeśli chcesz wprowadzić zmiany po przejrzeniu oferty, możesz je edytować i przesłać ponownie w celu opublikowania nowej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](./partner-center-portal/update-existing-offer.md).

1. Po zatwierdzeniu wersji zapoznawczej w celu opublikowania oferty na żywo w portalu komercyjnym wybierz pozycję **Przejdź na żywo**.

   > [!TIP]
   > Jeśli Twoja oferta już działa i jest dostępna publicznie w portalu Marketplace, wszystkie aktualizacje, które wprowadzisz, nie będą aktywne do momentu wybrania pozycji **Przejdź na żywo**.

## <a name="publish-phase"></a>Faza publikowania

Teraz, po wybraniu oferty na żywo z ofertą, która udostępnia ją w komercyjnej witrynie Marketplace, firma Microsoft przeprowadza serię ostatecznych testów weryfikacyjnych, aby upewnić się, że oferta na żywo jest skonfigurowana tak jak wersja zapoznawcza oferty.

- **Konfiguracja oferty zakupu** (>10 min)

    Firma Microsoft gwarantuje, że oferta może zostać zrealizowana w przypadku zakupu przez klientów w Azure Portal. Ten krok ma zastosowanie tylko w przypadku ofert sprzedawanych przez firmę Microsoft.

- **Sprawdzanie poprawności danych na dysku testowym** (~ 5 min)

    Weryfikujemy dane podane na stronie konfiguracji technicznej oferty. Testujemy i zatwierdzimy funkcję testowania dysków. Ten krok ma zastosowanie tylko w przypadku ofert z włączonym dyskiem testowym.

- **Obsługa administracyjna stacji testowej** (~ 30 min)

    Wdrażamy i replikujemy wystąpienia dysku testowego, aby były gotowe do użycia przez klienta. Ten krok ma zastosowanie tylko w przypadku ofert z włączonym dyskiem testowym.

- **Sprawdzanie poprawności i rejestracja zarządzania potencjalnymi klientami** (>15 min)

    Upewnij się, że Twój system zarządzania potencjalnymi klientami może odbierać potencjalnych klientów w oparciu o szczegóły podane na stronie **konfiguracji oferty** . Ten krok dotyczy tylko ofert z włączonym zarządzaniem potencjalnym klientem.

- **Publikowanie końcowe (>30 minut)**

    Firma Microsoft gwarantuje, że Twoja oferta będzie publicznie dostępna w portalu Marketplace.

Po zakończeniu sprawdzania poprawności oferta będzie aktywna w portalu Marketplace.

### <a name="reporting-usage-of-preview-and-other-internal-usage"></a>Raportowanie użycia wersji zapoznawczej i innego wewnętrznego użycia

Aby sprawdzić, czy jest to kompleksowa usługa zakupów i konfiguracji, Kup ofertę, gdy jest ona dostępna w wersji zapoznawczej. Najpierw Powiadom firmę Microsoft za pomocą [biletu pomocy technicznej](https://aka.ms/marketplacesupport) , aby upewnić się, że nie przetwarzamy opłaty.

## <a name="next-step"></a>Następny krok

[Dostęp do raportów analitycznych dla komercyjnego portalu Marketplace w centrum partnerskim](./partner-center-portal/analytics.md)
