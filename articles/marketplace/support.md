---
title: Uzyskaj pomoc techniczną dotyczącą komercyjnego programu w witrynie Marketplace w centrum partnerskim
description: Dowiedz się więcej o opcjach pomocy technicznej dla komercyjnego programu Marketplace w centrum partnerskim, w tym informacje na temat sposobu tworzenia żądania obsługi.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: cfe365af868f01b868e737a409724e92f1d57f81
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106993"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Wsparcie dla komercyjnego programu w witrynie Marketplace w centrum partnerskim

Firma Microsoft zapewnia pomoc techniczną dla wielu różnych produktów i usług. Znalezienie odpowiedniego zespołu pomocy technicznej jest ważne, aby zapewnić odpowiednią i terminową odpowiedź. Należy wziąć pod uwagę następujące scenariusze, które powinny pomóc w kierowaniu zapytania do odpowiedniego zespołu:

- Jeśli jesteś wydawcą i masz pytania od klienta, poproś klienta o zażądanie pomocy technicznej za pomocą linków pomocy technicznej w [Azure Portal](https://portal.azure.com/).
- Jeśli jesteś wydawcą i wykryjesz problem z zabezpieczeniami aplikacji działającej na platformie Azure, zobacz [jak rejestrować bilet pomocy technicznej dotyczącej zdarzeń zabezpieczeń](../security/fundamentals/event-support-ticket.md). Wydawcy muszą zgłaszać podejrzane zdarzenia zabezpieczeń, w tym zdarzenia dotyczące zabezpieczeń i luki w zabezpieczeniach oprogramowania i usług portalu Azure Marketplace, z najwcześniejszą możliwością.
- Jeśli jesteś wydawcą i masz pytanie dotyczące Twojej aplikacji lub usługi, zapoznaj się z następującymi opcjami pomocy technicznej.

## <a name="get-help-or-open-a-support-ticket"></a>Uzyskaj pomoc lub Otwórz bilet pomocy technicznej

1. Zaloguj się przy użyciu konta służbowego. Jeśli jeszcze tego nie zrobiono, należy [utworzyć konto Centrum partnerskiego](create-account.md).

1. W menu w prawym górnym rogu strony wybierz ikonę **Pomoc techniczna** . Okienko **Pomoc i obsługa techniczna** pojawia się po prawej stronie strony.

1. Aby uzyskać pomoc dotyczącą komercyjnej witryny Marketplace, wybierz pozycję **Marketing komercyjny**.

   ![Menu rozwijane obsługa](./media/support/commercial-marketplace-support-pane.png)

1. W polu **Podsumowanie problemu** wprowadź krótki opis problemu.

1. W polu **typ problemu** wykonaj jedną z następujących czynności:

    - **Opcja 1**: Wprowadź słowa kluczowe, takie jak: Marketplace, Azure App, SaaS oferta, zarządzanie kontami, zarządzanie potencjalnymi klientami, problem z wdrożeniem, wypłata lub migracja oferty do sprzedaży. Następnie wybierz typ problemu z wyświetlonej listy zalecane.

    - **Opcja 2**: wybierz pozycję **Przeglądaj tematy** z listy **Kategoria** , a następnie wybierz pozycję **Marketing komercyjny**. Następnie wybierz odpowiedni **temat** i **temat podrzędny**.

1. Po znalezieniu wybranego tematu wybierz pozycję **Przejrzyj rozwiązania**.

    ![Następny krok](./media/support/next-step.png)

Wyświetlane są następujące opcje:

- Aby wybrać inny temat, kliknij opcję **Wybierz inny problem**.
- Aby pomóc w rozwiązaniu problemu, zapoznaj się z zalecanymi krokami i dokumentami, jeśli są dostępne.

    ![Zalecane rozwiązania](./media/support/recommended-solutions.png)

Jeśli nie możesz znaleźć odpowiedzi w pomocy, wybierz pozycję **Podaj szczegóły problemu**. Wypełnij wszystkie wymagane pola, aby przyspieszyć proces rozwiązywania problemów, a następnie wybierz pozycję **Prześlij**.

>[!Note]
>Jeśli użytkownik nie zalogował się do Centrum partnerskiego, może być wymagane zalogowanie się, aby można było utworzyć bilet.

## <a name="track-your-existing-support-requests"></a>Śledź istniejące żądania obsługi

Aby przejrzeć otwarte i zamknięte bilety, w menu nawigacji po lewej stronie wybierz pozycję **komercyjna**  >  **Pomoc techniczna** Marketplace.

## <a name="record-issue-details-with-a-har-file"></a>Rejestruj Szczegóły problemu przy użyciu pliku HAR

Aby pomóc pracownikom w rozwiązywaniu problemów, rozważ dołączenie pliku formatu archiwum HTTP (HAR) do biletu pomocy technicznej. Pliki HAR to Dzienniki żądań sieciowych w przeglądarce internetowej.

> [!WARNING]
> Pliki HAR mogą rejestrować poufne dane dotyczące konta Centrum partnerskiego.

### <a name="microsoft-edge-and-google-chrome"></a>Przeglądarka Microsoft Edge i Google Chrome

Aby wygenerować plik HAR przy użyciu **przeglądarki Microsoft Edge** lub **Google Chrome**:

1. Przejdź do strony sieci Web, na której występuje problem.
2. W prawym górnym rogu okna wybierz ikonę wielokropka, a następnie **więcej narzędzi**  >  **deweloperskich**. Możesz nacisnąć klawisz F12 jako skrót.
3. W okienku narzędzia deweloperskie wybierz kartę **Sieć** .
4. Wybierz pozycję **Zatrzymaj rejestrowanie dziennika sieci** i **Wyczyść** , aby usunąć istniejące dzienniki. Ikona rekordu zmieni kolor na szary.

    ![Jak usunąć istniejące dzienniki w przeglądarce Microsoft Edge lub Google Chrome](media/support/chromium-stop-clear-session.png)

5. Wybierz pozycję **zarejestruj dziennik sieciowy** , aby rozpocząć nagrywanie. Po rozpoczęciu nagrywania ikona rekordu zmieni kolor na czerwony.

    ![Jak rozpocząć nagrywanie w przeglądarce Microsoft Edge lub Google Chrome](media/support/chromium-start-session.png)

6. Odtwórz problem, który chcesz rozwiązać.
7. Po ponownym utworzeniu problemu wybierz pozycję **Zatrzymaj rejestrowanie dziennika sieci**.
8. Wybierz pozycję **Export Har**, oznaczona ikoną z strzałką w dół i Zapisz plik.

    ![Jak wyeksportować plik HAR w przeglądarce Microsoft Edge lub Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Aby wygenerować plik HAR przy użyciu **przeglądarki Mozilla Firefox**:

1. Przejdź do strony sieci Web, na której występuje problem.
1. W prawym górnym rogu okna wybierz ikonę wielokropka, a następnie narzędzia do przełączania **deweloperów sieci Web**  >  . Możesz nacisnąć klawisz F12 jako skrót.
1. Wybierz kartę **Sieć** , a następnie wybierz pozycję **Wyczyść** , aby usunąć istniejące dzienniki.

    ![Jak usunąć istniejące dzienniki w Mozilla Firefox](media/support/firefox-clear-session.png)

1. Odtwórz problem, który chcesz rozwiązać.
1. Po ponownym utworzeniu problemu wybierz pozycję **Har Export/Import**  >  **Zapisz wszystko jako Har**.

    ![Jak wyeksportować plik HAR w przeglądarce Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Aby wygenerować plik HAR przy użyciu **przeglądarki Safari**:

1. Włącz narzędzia deweloperskie w przeglądarce Safari: wybierz pozycję  >  **Preferencje** Safari. Przejdź do karty **Zaawansowane** , a następnie wybierz pozycję **Pokaż menu rozwijania na pasku menu**.
1. Przejdź do strony sieci Web, na której występuje problem.
1. Wybierz pozycję **programowanie**, a następnie wybierz pozycję **Pokaż inspektora sieci Web**.
1. Wybierz kartę **Sieć** , a następnie wybierz pozycję **Wyczyść elementy sieciowe** , aby usunąć istniejące dzienniki.

    ![Jak usunąć istniejące dzienniki w przeglądarce Safari](media/support/safari-clear-session.png)

1. Odtwórz problem, który chcesz rozwiązać.
1. Po ponownym utworzeniu problemu wybierz opcję **Eksportuj** i Zapisz plik.

    ![Jak wyeksportować plik HAR w przeglądarce Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](partner-center-portal/update-existing-offer.md)