---
title: Azure Active Directory i transakcyjne oferty SaaS w komercyjnej witrynie Marketplace
description: Dowiedz się, jak usługa Azure Active Directory współpracuje z ofertami SaaS z możliwością transakcji w komercyjnym portalu Microsoft Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 5a09105dac89f3dc241140f16f3d4be72cc97493
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89483630"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Oferty usługi Azure AD i transacte SaaS w portalu komercyjnym

Usługa zarządzania tożsamościami i dostępem w chmurze firmy Microsoft [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) ułatwia użytkownikom logowanie się i dostęp do zasobów wewnętrznych i zewnętrznych. W portalu komercyjnym firmy Microsoft usługa Azure AD sprawia, że SaaS są łatwiejsze i bardziej bezpieczne dla wszystkich osób, w tym wydawców, kupujących i użytkowników. Za pomocą usługi Azure AD wydawcy mogą zautomatyzować obsługę administracyjną użytkowników w aplikacjach SaaS (Software as a Service), a Kupujący mogą zarządzać tymi użytkownikami. 

Ponadto Logowanie jednokrotne w usłudze [Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) zapewnia bezpieczeństwo i wygodę, gdy użytkownicy logują się do aplikacji w usłudze Azure AD. Szybsze angażowanie i zoptymalizowane środowiska również pobudzają zatrudnienie i zaufanie użytkownika od pierwszej interakcji z aplikacją SaaS wydawcy. Daje to pozytywne wrażenie, które kompilują widoczność i zachęca do powtarzania działalności.

Postępując zgodnie ze wskazówkami w tym artykule, pomożesz zatwierdzić swoją ofertę SaaS na komercyjnym rynku. Aby uzyskać więcej informacji na temat certyfikacji, zapoznaj się z szczegółowymi [komercyjnymi zasadami certyfikacji portalu Marketplace](https://aka.ms/commercial-marketplace-certification-policies#100-general), w tym [określonymi dla SaaS](https://aka.ms/commercial-marketplace-certification-policies#1000-software-as-a-service-saas).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Po [utworzeniu oferty SaaS](./partner-center-portal/create-new-saas-offer.md) w centrum partnerskim możesz wybrać jedną z opcji wyświetlania listy, która będzie wyświetlana na liście oferty. Wybór określa sposób, w jaki Twoja oferta została zrealizowana na komercyjnym rynku. Oferty sprzedawane za poorednictwem firmy Microsoft są nazywane ofertami transakcyjnymi. Klient jest obciążany w Twoim imieniu wszystkimi ofertami z obsługą transakcji. W przypadku wybrania opcji sprzedaży za pomocą firmy Microsoft i zrealizowania transakcji hostów w Twoim imieniu (opcja **tak** ) wybrano opcję utworzenia oferty transakcyjnej i tego artykułu. Zalecamy, aby przeczytał go w całości.

Jeśli zdecydujesz się na wyświetlanie oferty tylko za pomocą komercyjnej witryny Marketplace i Przetwarzaj transakcje osobno ( **bez** opcji), masz trzy opcje, w przypadku których potencjalni klienci uzyskają dostęp do oferty: Pobierz teraz (bezpłatnie), bezpłatna wersja próbna i skontaktuj się ze mną. Jeśli wybierzesz pozycję **Pobierz teraz (bezpłatnie)** lub **bezpłatną wersję próbną**, ten artykuł nie zostanie wyświetlony. Zamiast tego, aby uzyskać więcej informacji, zobacz sekcję [Tworzenie strony docelowej dla oferty bezpłatnej lub próbnej SaaS w portalu komercyjnym](./azure-ad-free-or-trial-landing-page.md) . Jeśli wybierzesz opcję **kontakt ze mną**, nie ma bezpośrednich obowiązków wydawcy. Kontynuuj tworzenie oferty w centrum partnerskim.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Jak usługa Azure AD współpracuje z komercyjnymi ofertami w witrynie Marketplace for SaaS

Usługa Azure AD umożliwia bezproblemowe kupowanie, realizację i zarządzanie komercyjnymi rozwiązaniami Marketplace. Rysunek 1 pokazuje, w jaki sposób Wydawca, kupujący i użytkownik mogą korzystać z zakupu i aktywowania subskrypcji. Pokazano w nim także, jak klienci korzystają z aplikacji SaaS i zarządzać nimi z komercyjnej witryny Marketplace. Na potrzeby tej ilustracji, Kupujący jest użytkownikiem aplikacji SaaS, który inicjuje zakup z komercyjnej witryny Marketplace.

Jak pokazano na rysunku 1, gdy kupujący wybierze ofertę, rozpoczynają się one w łańcuchu przepływów pracy obejmujących zakup, subskrypcję i zarządzanie użytkownikami. W ramach tego łańcucha użytkownik jest odpowiedzialny za pewne wymagania, a firma Microsoft zapewnia pomoc techniczną w najważniejszych punktach.

***Rysunek 1. Korzystanie z oferty usługi Azure AD for SaaS w portalu komercyjnym***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Ilustruje zarządzanie zakupami, zarządzanie subskrypcjami i opcjonalne etapy procesu zarządzania użytkownikami.":::

Poniższe sekcje zawierają szczegółowe informacje o wymaganiach dla każdego kroku procesu.

## <a name="process-steps-for-purchase-management"></a>Etapy procesu zarządzania zakupami

Ten rysunek przedstawia cztery etapy procesu zarządzania zakupami.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="Ilustruje zarządzanie zakupami, zarządzanie subskrypcjami i opcjonalne etapy procesu zarządzania użytkownikami.":::

Ta tabela zawiera szczegółowe informacje na temat kroków procesu zarządzania zakupami.

| Krok procesu | Akcja wydawcy | Zalecane lub wymagane przez wydawców |
| ------------ | ------------- | ------------- |
| 1. Kupujący loguje się do komercyjnej witryny Marketplace przy użyciu tożsamości identyfikatora platformy Azure i wybiera ofertę SaaS. | Nie jest wymagana żadna akcja wydawcy. | Nie dotyczy |
| 2. po zakupie, kupujący wybierze opcję **Skonfiguruj konto** w portalu Azure Marketplace lub **Skonfiguruj teraz** w usłudze AppSource, który kieruje kupującego do strony docelowej wydawcy dla tej oferty. Kupujący musi być w stanie zalogować się do aplikacji SaaS wydawcy z logowaniem jednokrotnym w usłudze Azure AD i uzyskać tylko prośbę o podanie minimalnej zgody, która nie wymaga zatwierdzenia przez administratora usługi Azure AD. | Zaprojektuj [stronę](azure-ad-transactable-saas-landing-page.md) docelową oferty w taki sposób, aby odbierze użytkownikowi swoją tożsamość usługi Azure AD lub konto Microsoft (MSA), a także dodatkową wymaganą obsługę lub konfigurację. | Wymagane |
| 3. Wydawca żąda szczegółowych informacji o zakupie z interfejsu API realizacji SaaS. | Przy użyciu [tokenu dostępu](./partner-center-portal/pc-saas-registration.md) wygenerowanego na podstawie identyfikatora aplikacji strony docelowej należy [wywołać rozwiązanie punktu końcowego](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) , aby uzyskać szczegółowe informacje o zakupie. | Wymagane |
| 4. za pomocą usługi Azure AD i interfejsu API Microsoft Graph, Wydawca zbiera szczegóły firmy i użytkownika wymagane do aprowizacji nabywcy w aplikacji SaaS wydawcy.  | Rozłożyć token użytkownika usługi Azure AD, aby znaleźć nazwę i adres e-mail, lub [Wywołaj interfejs API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) i użyć uprawnień delegowanych do [pobrania informacji](https://docs.microsoft.com/graph/api/user-get) o zalogowanym użytkowniku. | Wymagane |
||||

## <a name="process-steps-for-subscription-management"></a>Etapy procesu zarządzania subskrypcjami

Na tym rysunku przedstawiono dwa etapy procesu zarządzania subskrypcją.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Ilustruje zarządzanie zakupami, zarządzanie subskrypcjami i opcjonalne etapy procesu zarządzania użytkownikami.":::

W tej tabeli opisano szczegóły kroków procesu zarządzania subskrypcją.

| Krok procesu | Akcja wydawcy | Zalecane lub wymagane przez wydawców |
| ------------ | ------------- | ------------- |
| 5. Wydawca zarządza subskrypcją aplikacji SaaS za pomocą interfejsu API realizacji SaaS. | Obsługa zmian subskrypcji i innych zadań zarządzania za pomocą [interfejsów API realizacji SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md).<br><br>Ten krok wymaga tokenu dostępu, zgodnie z opisem w kroku 3. | Wymagane |
| 6. w przypadku korzystania z cen taryfowych Wydawca emituje zdarzenia użycia do interfejsu API usługi pomiaru. | Jeśli Twoja aplikacja SaaS korzysta z rozliczeń opartych na użyciu, Utwórz powiadomienia dotyczące użycia za pośrednictwem [interfejsów API usługi pomiaru Marketplace](./partner-center-portal/marketplace-metering-service-apis.md).<br><br>Ten krok wymaga tokenu dostępu, zgodnie z opisem w kroku 3. | Wymagane do pomiaru |
||||

## <a name="process-steps-for-user-management"></a>Etapy procesu zarządzania użytkownikami

Na tym rysunku przedstawiono trzy etapy procesu zarządzania użytkownikami.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="Ilustruje zarządzanie zakupami, zarządzanie subskrypcjami i opcjonalne etapy procesu zarządzania użytkownikami.":::

Kroki od 7 do 9 są opcjonalnymi krokami procesu zarządzania użytkownikami. Zapewniają one dodatkowe korzyści dla wydawców, którzy obsługują Logowanie jednokrotne w usłudze Azure AD. W tej tabeli opisano szczegółowe informacje o procedurach procesu zarządzania użytkownikami.

| Krok procesu | Akcja wydawcy | Zalecane lub wymagane przez wydawców |
| ------------ | ------------- | ------------- |
| 7. Administratorzy usługi Azure AD w firmie Kupujący mogą opcjonalnie zarządzać dostępem użytkowników i grup za pomocą usługi Azure AD. | W przypadku skonfigurowania usługi Azure AD SSO dla użytkowników (krok 9) nie jest wymagana żadna akcja wydawcy. | Nie dotyczy |
| 8. Usługa aprowizacji usługi Azure AD komunikuje zmiany między usługą Azure AD a aplikacją SaaS wydawcy. | [Zaimplementuj Standard scim punkt końcowy](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) , aby otrzymywać aktualizacje z usługi Azure AD, gdy użytkownicy zostaną dodani i usunięci. | Zalecane |
| 9. po uzyskaniu dostępu do aplikacji i zainicjowaniu obsługi administracyjnej użytkownicy z firmy kupującej mogą korzystać z logowania jednokrotnego w usłudze Azure AD w celu zalogowania się do aplikacji SaaS wydawcy. | [Użyj logowania jednokrotnego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) , aby umożliwić użytkownikom logowanie jednokrotne przy użyciu jednego konta do aplikacji SaaS wydawcy. | Zalecane |
||||

## <a name="next-steps"></a>Następne kroki

- [Utwórz stronę docelową dla oferty SaaS z transakcyjnymi w komercyjnej witrynie Marketplace](azure-ad-transactable-saas-landing-page.md)
- [Utwórz stronę docelową oferty bezpłatnej usługi SaaS lub wersji próbnej na komercyjnej platformie Marketplace](azure-ad-free-or-trial-landing-page.md)
- [Jak utworzyć ofertę SaaS w komercyjnej witrynie Marketplace](create-new-saas-offer.md)
