---
title: Planowanie ofert usługi Dynamics 365 dla Microsoft AppSource
description: Planowanie ofert usługi Dynamics 365 dla Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/16/2021
ms.openlocfilehash: c5746b70f84ff68ea578ba141cc5251cc5ce07ea
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819429"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Planowanie oferty usługi Microsoft Dynamics 365

W tym artykule wyjaśniono różne opcje i funkcje oferty Dynamics 365 w Microsoft AppSource na platformie handlowej. Usługa AppSource obejmuje oferty, które są rozbudowywy Microsoft 365, Dynamics 365, PowerApps i Power BI. Usługa AppSource umożliwia *korzystanie* z płatnych ofert (Pobierz teraz), list (Skontaktuj się ze *mną)* i wersji próbnej *(Wypróbuj teraz).*

Przed rozpoczęciem utwórz konto platformy handlowej w usłudze [Partner Center](./partner-center-portal/create-account.md) i upewnij się, że jest zarejestrowane w programie platformy handlowej. Zapoznaj się również z [procesem publikowania i wskazówkami.](/office/dev/store/submit-to-appsource-via-partner-center)

## <a name="licensing-options"></a>Opcje licencjonowania

Podczas przygotowywania się do opublikowania nowej oferty musisz zdecydować, którą opcję licencjonowania wybrać. Pozwoli to określić, jakie dodatkowe informacje należy podać później podczas tworzenia oferty w Partner Center.

Są to dostępne opcje licencjonowania ofert usługi Dynamics 365:

| Opcja licencjonowania | Proces transakcji |
| --- | --- |
| Pobierz teraz (bezpłatnie) | Bezpłatnie wyeksymuj swoją ofertę dla klientów. |
| Bezpłatna wersja próbna (lista) | Zaoferuj klientom bezpłatną wersję próbną do 1-, 3- lub 6-miesięcznej wersji próbnej. Bezpłatne wersje próbne oferty są tworzone, zarządzane i konfigurowane przez usługę, a subskrypcje nie są zarządzane przez firmę Microsoft. |
| Skontaktuj się ze mną | Zbieraj informacje kontaktowe klientów, łącząc system zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnianie swoich informacji. Te szczegóły klienta wraz z nazwą oferty, identyfikatorem i źródłem witryny Marketplace, w którym znalazła Ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Aby uzyskać więcej informacji na temat konfigurowania systemu CRM, zobacz sekcję **Potencjalni** klienci na stronie Konfiguracja **oferty typu** oferty. |
|

## <a name="test-drive"></a>Dysk testowy

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Potencjalni klienci

Gdy publikujesz ofertę na platformie handlowej za pomocą usługi Partner Center, możesz połączyć ją z systemem zarządzania relacjami z klientami (CRM). Umożliwia to otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie Twoim produktem lub użyje go. Nawiązywanie połączenia z systemem CRM jest wymagane, jeśli chcesz włączyć dysk testowy; W przeciwnym razie nawiązanie połączenia z systemem CRM jest opcjonalne. Partner Center obsługuje tabelę platformy Azure, usługę Dynamics 365 Customer Engagement, punkt końcowy HTTPS, usługę Marketo i usługę Salesforce.

## <a name="legal"></a>Informacje prawne

Utwórz warunki **i postanowienia.** Klienci będą musieli je zaakceptować, aby wypróbować Twoją ofertę. Firma Microsoft ma umowę standardową, ale nie dotyczy ofert usługi Dynamics 365.

## <a name="offer-listing-details"></a>Szczegóły oferty

Oto przykład sposobu, w jaki informacje o ofercie są wyświetlane w Microsoft AppSource (wszystkie wymienione ceny są tylko do celów przykładowych i nie odzwierciedlają rzeczywistych kosztów):

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Ilustruje, jak ta oferta pojawia się w Microsoft AppSource.":::

> [!NOTE]
> Zawartość oferty nie musi być w języku angielskim, jeśli opis oferty rozpoczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]".

Aby ułatwić tworzenie oferty, przygotuj te elementy z wyprzedzeniem. Wszystkie są wymagane, z wyjątkiem sytuacji, gdy zostały zanotowe.

- **Nazwa** — nazwa będzie wyświetlana jako tytuł oferty na platformie handlowej. Nazwa może być znakami towarowymi. Nie może zawierać emoji (chyba że są znakami towarowymi i symbolami praw autorskich) i jest ograniczona do 50 znaków.
- **Podsumowanie wyników wyszukiwania** — przeznaczenie lub funkcja oferty jako pojedyncze zdanie bez podziałów wierszy na 100 znaków lub mniej. Jest on używany w wynikach wyszukiwania na komercyjnej platformie handlowej.
- **Opis** — ten opis jest wyświetlany w przeglądzie ofert na platformie handlowej. Rozważ możliwość uwzględnienia wartościowej oferty, kluczowych korzyści, planowanej bazy użytkowników, dowolnej kategorii lub skojarzeń branżowych, możliwości zakupu w aplikacji, wszelkich wymaganych ujawnień i linku, aby dowiedzieć się więcej. To pole tekstowe zawiera kontrolki edytora tekstu sformatowanego, aby twój opis był bardziej angażujący. Opcjonalnie możesz użyć tagów HTML do formatowania.
- **Słowa kluczowe wyszukiwania** (opcjonalnie) — maksymalnie trzy słowa kluczowe wyszukiwania, których klienci mogą użyć do znalezienia Twojej oferty. Nie dołączaj nazwy oferty **i** **opisu**; ten tekst jest automatycznie uwzględniany w wyszukiwaniu.
- **Produkt, z który działa twoja aplikacja** (opcjonalnie) — nazwy maksymalnie trzech produktów, z których współpracuje Twoja oferta.
- **Linki do zasad pomocy i** ochrony prywatności — adresy URL pomocy dla twojej oferty i firmowe zasady ochrony prywatności. Użytkownik jest odpowiedzialny za zapewnienie, że aplikacja jest zgodna z przepisami i przepisami dotyczącymi ochrony prywatności.
- **Informacje kontaktowe**
  - **Kontakt z pomocą** techniczną — imię i nazwisko, telefon i adres e-mail, których partnerzy firmy Microsoft będą używać, gdy klienci otwierają bilety. Dołącz adres URL witryny sieci Web pomocy technicznej.
  - **Kontakt inżynieryjny** — imię i nazwisko, telefon i adres e-mail, których firma Microsoft będzie używać bezpośrednio w przypadku problemów z Twoją ofertą. Te informacje kontaktowe nie są wymienione na platformie handlowej.
- **Dokumenty obsługi —** maksymalnie trzy dokumenty dla klientów, takie jak dokumenty oficjalnych, nachyłki, listy kontrolne lub prezentacje programu PowerPoint, w formacie PDF.
- **Media**
    - **Logo —** plik PNG dla **dużego** logo. Partner Center użyje tej funkcji do utworzenia innych wymaganych rozmiarów logo. Opcjonalnie możesz zastąpić je różnymi obrazami później.
    - **Zrzuty** ekranu — co najmniej jeden lub maksymalnie pięć zrzutów ekranu przedstawiających sposób działania oferty. Obrazy muszą mieć format PNG 1280 x 720 pikseli i zawierać podpis.
    - **Wideo** (opcjonalnie) — maksymalnie cztery filmy wideo, które pokazują Twoją ofertę. Dołącz nazwę, adres URL usługi YouTube lub Vimeo oraz miniaturę PNG o rozmiarze 1280 x 720 pikseli.

> [!Note]
> Twoja oferta musi spełniać ogólne zasady certyfikacji komercyjnej platformy [handlowej,](/legal/marketplace/certification-policies#100-general) aby została opublikowana na platformie handlowej.

## <a name="additional-sales-opportunities"></a>Dodatkowe możliwości sprzedaży

Możesz zdecydować się na dostęp do obsługiwanych przez firmę Microsoft kanałów marketingowych i sprzedażowych. Podczas tworzenia oferty w Partner Center na końcu procesu sprzedaży z firmą **Microsoft** zobaczysz dwie karty. Ta opcja umożliwia zespołom sprzedaży firmy Microsoft rozważenie kwalifikującego się rozwiązania do współsprzedaży adresu IP podczas oceniania potrzeb swoich klientów. Aby [uzyskać szczegółowe informacje na](commercial-marketplace-co-sell.md) temat przygotowywania oferty do oceny, zobacz opcja Partner Center w 2018 r.

## <a name="next-steps"></a>Następne kroki

Po rozważeniu opisanych powyżej elementów planowania wybierz jedną z następujących pozycji (również dostępną w spisie treści po lewej stronie), aby rozpocząć tworzenie oferty.

| Przewodnik publikowania    | Uwagi  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) | Podczas tworzenia aplikacji dla Enterprise Edition zapoznaj się z tymi dodatkowymi procesami [publikowania i wskazówkami.](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source) |
| [Dynamics 365 for Business Central](partner-center-portal/create-new-business-central-offer.md) |   |
| [Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) | Najpierw zapoznaj się z dodatkowymi [procesami i wytycznymi dotyczącymi publikowania.](/dynamics365/customer-engagement/developer/publish-app-appsource) |
| [Power BI](/partner-center-portal/create-power-bi-app-offer.md) | Najpierw zapoznaj się z dodatkowymi [procesami i wytycznymi dotyczącymi publikowania.](/power-bi/developer/office-store) |
|
