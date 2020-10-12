---
title: Utwórz stronę docelową oferty bezpłatnej usługi SaaS lub wersji próbnej na komercyjnej platformie Marketplace
description: Dowiedz się, jak utworzyć stronę docelową dla oferty bezpłatnej usługi SaaS lub wersji próbnej.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: b01b482b967ba6db90aa80ba537457597fb91046
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488613"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Utwórz stronę docelową oferty bezpłatnej usługi SaaS lub wersji próbnej na komercyjnej platformie Marketplace

Ten artykuł przeprowadzi Cię przez proces tworzenia strony docelowej dla bezpłatnej aplikacji SaaS lub wersji próbnej, która będzie sprzedawana w portalu komercyjnym firmy Microsoft.

## <a name="overview"></a>Omówienie

Stronę docelową możesz traktować jako "poczekalni" dla oferty oprogramowania jako usługi (SaaS). Gdy klient zdecyduje się na uzyskanie swojej aplikacji, komercyjna witryna Marketplace kieruje je do strony docelowej, aby aktywować i skonfigurować swoją subskrypcję w aplikacji SaaS. Podczas tworzenia oferty oprogramowanie jako usługa (SaaS) w centrum partnerskim możesz zdecydować, czy [sprzedawać w firmie Microsoft](plan-saas-offer.md#listing-options). Jeśli chcesz wyświetlić tylko swoją ofertę w komercyjnym portalu Microsoft Marketplace i nie sprzedawać jej przez firmę Microsoft, możesz określić, jak potencjalni klienci mogą korzystać z oferty. Po włączeniu opcji **Pobierz teraz (bezpłatnie)** lub **bezpłatnej wersji próbnej** należy określić adres URL strony docelowej, do której użytkownik może przejść w celu uzyskania dostępu do bezpłatnej subskrypcji lub wersji próbnej.

Strona docelowa to po prostu otrzymanie użytkownika w celu aktywowania bezpłatnej wersji próbnej lub bezpłatnej subskrypcji. Korzystając z Azure Active Directory (Azure AD) i Microsoft Graph, będziesz w stanie włączyć logowanie jednokrotne (SSO) dla użytkownika i uzyskać ważne informacje o użytkowniku, którego możesz użyć do aktywowania bezpłatnej wersji próbnej lub bezpłatnej subskrypcji, w tym jej nazwę, adres e-mail i organizację.

Ponieważ informacje wymagane do aktywowania subskrypcji są ograniczone i udostępniane przez usługę Azure AD i Microsoft Graph, nie powinno być wymagane żądanie informacji, które wymagają więcej niż podstawowa zgoda. Jeśli potrzebujesz szczegółowych informacji o użytkownikach, które wymagają dodatkowej zgody dla aplikacji, musisz zażądać tych informacji po zakończeniu aktywacji subskrypcji. Umożliwia to bezproblemową aktywację subskrypcji dla użytkownika i zmniejszenie ryzyka pominięcia.

Strona docelowa zawiera zwykle następujące informacje i opcje wyświetlania:

- Zanotuj nazwę i szczegóły bezpłatnej wersji próbnej lub bezpłatnej subskrypcji. Można na przykład określić limity użycia lub czas trwania wersji próbnej.
- Przedstaw szczegóły konta użytkownika, w tym imię i nazwisko, organizację i adres e-mail.
- Monituj użytkownika o potwierdzenie lub zastąpienie innych szczegółów konta.
- Przekieruj użytkownika na kolejne kroki po aktywacji. Na przykład otrzymasz powitalną wiadomość e-mail, Zarządzaj subskrypcją, uzyskaj pomoc techniczną lub przeczytaj dokumentację.

Poniższe sekcje tego artykułu przeprowadzą Cię przez proces tworzenia strony docelowej:

1. [Utwórz rejestrację aplikacji usługi Azure AD](#create-an-azure-ad-app-registration) na stronie docelowej.
2. [Użyj przykładu kodu jako punktu początkowego](#use-a-code-sample-as-a-starting-point) dla aplikacji.
3. [Odczytaj informacje z oświadczeń zakodowanych w tokenie ID](#read-information-from-claims-encoded-in-the-id-token)otrzymane z usługi Azure AD po zalogowaniu, które zostały wysłane wraz z żądaniem.
4. [Użyj interfejsu API Microsoft Graph](#use-the-microsoft-graph-api) , aby zbierać dodatkowe informacje zgodnie z potrzebami.

## <a name="create-an-azure-ad-app-registration"></a>Tworzenie rejestracji aplikacji usługi Azure AD

Komercyjna witryna Marketplace jest w pełni zintegrowana z usługą Azure AD. Użytkownicy docierają do portalu Marketplace uwierzytelnionego za pomocą [konta usługi Azure AD lub konto Microsoft (MSA)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology). Po uzyskaniu bezpłatnej lub bezpłatnej subskrypcji próbnej za pośrednictwem oferty z jedyną listą użytkownik przechodzi z komercyjnej witryny Marketplace do adresu URL strony docelowej w celu aktywowania i zarządzania subskrypcją aplikacji SaaS. Musisz pozwolić użytkownikom na logowanie się do aplikacji za pomocą logowania jednokrotnego usługi Azure AD. (Adres URL strony docelowej jest określony na stronie [konfiguracji technicznej](plan-saas-offer.md#technical-information) oferty.

Pierwszym krokiem do korzystania z tożsamości jest upewnienie się, że strona docelowa jest zarejestrowana jako aplikacja usługi Azure AD. Zarejestrowanie aplikacji pozwala używać usługi Azure AD do uwierzytelniania użytkowników i żądania dostępu do zasobów użytkownika. Może być uważana za definicję aplikacji, która pozwala usłudze poznać, jak wystawiać tokeny dla aplikacji na podstawie ustawień aplikacji.

### <a name="register-a-new-application-using-the-azure-portal"></a>Rejestrowanie nowej aplikacji w witrynie Azure Portal

Aby rozpocząć, postępuj zgodnie z instrukcjami dotyczącymi [rejestrowania nowej aplikacji](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Aby umożliwić użytkownikom z innych firm, odwiedzanie aplikacji, musisz wybrać **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępne) i osobiste konta Microsoft (na przykład Skype lub Xbox)** , gdy zostanie wyświetlony monit, kto może korzystać z aplikacji.

Jeśli zamierzasz wykonać zapytanie dotyczące interfejsu API Microsoft Graph, [Skonfiguruj nową aplikację w celu uzyskiwania dostępu do interfejsów API sieci Web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis). Po wybraniu uprawnień interfejsu API dla tej aplikacji domyślna wartość **User. Read** jest wystarczająca do zebrania podstawowych informacji o użytkowniku, aby proces dołączania był bezproblemowy i automatyczny. Nie Żądaj żadnych uprawnień interfejsu API oznaczonych jako **wymagające zgody administratora**, ponieważ spowoduje to zablokowanie na stronie docelowej wszystkich użytkowników niebędących administratorami.

Jeśli użytkownik wymaga podniesionych uprawnień w ramach procesu dołączania lub inicjowania obsługi administracyjnej, należy rozważyć użycie funkcji [przyrostowej zgody](https://aka.ms/incremental-consent) usługi Azure AD, aby wszyscy użytkownicy, którzy otrzymali z portalu Marketplace, mogli początkowo interaktywnie korzystać ze strony docelowej.

## <a name="use-a-code-sample-as-a-starting-point"></a>Użyj przykładu kodu jako punktu początkowego

Firma Microsoft udostępniła kilka przykładowych aplikacji, które implementują prostą witrynę sieci Web z włączonym logowaniem usługi Azure AD. Po zarejestrowaniu aplikacji w usłudze Azure AD blok **szybkiego startu** oferuje listę typowych typów aplikacji i stosów programowania (rysunek 1). Wybierz ten, który odpowiada Twojemu środowisku, i postępuj zgodnie z instrukcjami dotyczącymi pobierania i konfigurowania.

***Rysunek 1. blok szybki start w Azure Portal***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Ilustruje blok szybkiego startu w Azure Portal.":::

Po pobraniu kodu i skonfigurowaniu środowiska programistycznego Zmień ustawienia konfiguracji w aplikacji w celu odzwierciedlenia identyfikatora aplikacji, identyfikatora dzierżawy i wpisu tajnego klienta zarejestrowanego w poprzedniej procedurze. Należy zauważyć, że dokładne kroki różnią się w zależności od używanego przykładu.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Odczytaj informacje z oświadczeń zakodowanych w tokenie identyfikatora

W ramach przepływu [połączenia OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) usługa Azure AD dodaje [token identyfikatora](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) do żądania, gdy użytkownik jest wysyłany do strony docelowej. Ten token zawiera wiele podstawowych informacji, które mogą być przydatne w procesie aktywacji, w tym informacje widoczne w tej tabeli.

| Wartość | Opis |
| ------------ | ------------- |
| AUD | Zamierzone odbiorcy dla tego tokenu. W takim przypadku powinna być zgodna z IDENTYFIKATORem aplikacji i być zweryfikowany. |
| preferred_username | Podstawowa nazwa użytkownika, którego odwiedzasz. Może to być adres e-mail, numer telefonu lub inny identyfikator. |
| poczta e-mail | Adres e-mail użytkownika. Należy zauważyć, że to pole może być puste. |
| name | Wartość, którą można odczytać przez człowieka, która identyfikuje podmiot tokenu. W takim przypadku będzie to nazwa użytkownika. |
| OID | Identyfikator w systemie tożsamości firmy Microsoft, który jednoznacznie identyfikuje użytkownika w aplikacjach. Microsoft Graph zwróci tę wartość jako właściwość identyfikatora dla danego konta użytkownika. |
| TID | Identyfikator reprezentujący dzierżawę usługi Azure AD, z której korzysta użytkownik. W przypadku tożsamości MSA zawsze będzie to możliwe `9188040d-6c67-4c5b-b112-36a304b66dad` . Aby uzyskać więcej informacji, zobacz uwagi w następnej sekcji: Użyj interfejsu API Microsoft Graph. |
| Sub | Identyfikator, który jednoznacznie identyfikuje użytkownika w tej konkretnej aplikacji. |
|||

## <a name="use-the-microsoft-graph-api"></a>Korzystanie z interfejsu API programu Microsoft Graph

Token identyfikatora zawiera podstawowe informacje umożliwiające zidentyfikowanie użytkownika, ale proces aktywacji może wymagać dodatkowych szczegółów, takich jak firma użytkownika — w celu ukończenia procesu dołączania. Użyj [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) , aby zażądać tych informacji, aby uniknąć wymuszania wprowadzania tych szczegółów przez użytkownika. Uprawnienia **użytkownika standardowego. odczyt** domyślnie zawierają następujące informacje:

| Wartość | Opis |
| ------------ | ------------- |
| displayName | Nazwa wyświetlana w książce adresowej dla użytkownika. |
| givenName | Imię użytkownika. |
| Stanowiska | Stanowisko użytkownika. |
| mail (poczta) | Adres SMTP użytkownika. |
| mobilePhone | Podstawowy numer telefonu komórkowego użytkownika. |
| preferredLanguage | Kod ISO 639-1 dla preferowanego języka użytkownika. |
| surname | Nazwisko użytkownika. |
|||

Dodatkowe właściwości — takie jak nazwa firmy użytkownika lub lokalizacja użytkownika (kraj) — można wybrać do uwzględnienia w żądaniu. Aby uzyskać więcej informacji, zobacz [właściwości typu zasobu użytkownika](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties).

Większość aplikacji, które są zarejestrowane w usłudze Azure AD, przyznaje delegowane uprawnienia do odczytu informacji o użytkowniku z dzierżawy usługi Azure AD swojej firmy. Każdemu żądaniu do Microsoft Graph informacji musi towarzyszyć token dostępu jako uwierzytelnianie. Określone kroki w celu wygenerowania tokenu dostępu będą zależeć od stosu technologii, który jest używany, ale przykładowy kod będzie zawierać przykład. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu w imieniu użytkownika](https://docs.microsoft.com/graph/auth-v2-user).

> [!NOTE]
> Konta z dzierżawy MSA (z IDENTYFIKATORem dzierżawy `9188040d-6c67-4c5b-b112-36a304b66dad` ) nie zwracają więcej informacji, niż została już zebrana z tokenem ID. Możesz pominąć to wywołanie w interfejs API programu Graph dla tych kont.

## <a name="next-steps"></a>Następne kroki
- [Jak utworzyć ofertę SaaS w komercyjnej witrynie Marketplace](create-new-saas-offer.md)
