---
title: Utwórz stronę docelową dla oferty SaaS z transakcyjnymi w komercyjnej witrynie Marketplace
description: Dowiedz się, jak utworzyć stronę docelową dla oferty SaaS z możliwością działania.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 04137fef640da46ca8876811e127e109a8c3d445
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348308"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Utwórz stronę docelową dla oferty SaaS z transakcyjnymi w komercyjnej witrynie Marketplace

Ten artykuł przeprowadzi Cię przez proces tworzenia strony docelowej dla aplikacji SaaS transakcyjnej, która będzie sprzedawana w komercyjnym portalu Microsoft Marketplace.

## <a name="overview"></a>Omówienie

Stronę docelową możesz traktować jako "poczekalni" dla oferty oprogramowania jako usługi (SaaS). Gdy kupujący subskrybuje ofertę, komercyjna witryna Marketplace kieruje je do strony docelowej, aby aktywować i skonfigurować swoją subskrypcję w aplikacji SaaS. Zastanów się tak jak krok potwierdzenia zamówienia, który umożliwia kupującemu sprawdzenie zakupionych elementów i potwierdzenie ich szczegółów konta. Korzystając z Azure Active Directory (Azure AD) i Microsoft Graph, możesz włączyć logowanie jednokrotne (SSO) dla kupującego i uzyskać ważne szczegółowe informacje o kupującym, za pomocą których można potwierdzić i aktywować swoją subskrypcję, w tym nazwę, adres e-mail i organizację.

Ponieważ informacje wymagane do aktywowania subskrypcji są ograniczone i udostępniane przez usługę Azure AD i Microsoft Graph, nie powinno być wymagane żądanie informacji, które wymagają więcej niż podstawowa zgoda. Jeśli potrzebujesz szczegółowych informacji o użytkownikach, które wymagają dodatkowej zgody dla aplikacji, musisz zażądać tych informacji po zakończeniu aktywacji subskrypcji. Umożliwia to bezproblemową aktywację subskrypcji dla kupującego i zmniejszenie ryzyka pominięcia.

Strona docelowa zazwyczaj obejmuje następujące elementy:

- Zanotuj nazwę oferty i zakupionego planu, a także warunki rozliczania.
- Przedstaw szczegóły konta kupującego, w tym imię i nazwisko, organizację i adres e-mail.
- Monituj kupującego, aby potwierdzić lub zastąpić inne szczegóły konta.
- Po aktywacji należy kierować do kupujących następne kroki. Na przykład otrzymasz powitalną wiadomość e-mail, Zarządzaj subskrypcją, uzyskaj pomoc techniczną lub przeczytaj dokumentację.

> [!NOTE]
> Kupujący zostanie również skierowany do strony docelowej podczas zarządzania swoją subskrypcją po aktywacji. Po aktywowaniu subskrypcji kupującego musisz użyć logowania jednokrotnego, aby umożliwić użytkownikowi logowanie się. Zaleca się kierowanie użytkownika do profilu konta lub strony konfiguracji.

Poniższe sekcje przeprowadzą Cię przez proces tworzenia strony docelowej:

1. [Utwórz rejestrację aplikacji usługi Azure AD](#create-an-azure-ad-app-registration) na stronie docelowej.
1. [Użyj przykładu kodu jako punktu początkowego](#use-a-code-sample-as-a-starting-point) dla aplikacji.
1. [Użyj dwóch aplikacji usługi Azure AD, aby zwiększyć bezpieczeństwo w środowisku produkcyjnym](#use-two-azure-ad-apps-to-improve-security-in-production).
1. [Rozpoznaj token identyfikacji zakupu w witrynie Marketplace](#resolve-the-marketplace-purchase-identification-token) dodany do adresu URL przez komercyjne witryny Marketplace.
1. [Przeczytaj informacje z oświadczeń zakodowanych w tokenie identyfikatora](#read-information-from-claims-encoded-in-the-id-token), które zostały odebrane z usługi Azure AD po zalogowaniu, które zostały wysłane wraz z żądaniem.
1. [Użyj interfejsu API Microsoft Graph](#use-the-microsoft-graph-api) , aby zbierać dodatkowe informacje zgodnie z potrzebami.

## <a name="create-an-azure-ad-app-registration"></a>Tworzenie rejestracji aplikacji usługi Azure AD

Komercyjna witryna Marketplace jest w pełni zintegrowana z usługą Azure AD. Nabywcy docierają do portalu Marketplace uwierzytelnionego za pomocą [konta usługi Azure AD lub konto Microsoft (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Po zakupie, kupujący przechodzi od komercyjnej witryny Marketplace do adresu URL strony docelowej, aby aktywować swoją subskrypcję aplikacji SaaS i zarządzać nią. Musisz pozwolić, aby kupujący zalogować się do aplikacji za pomocą logowania jednokrotnego usługi Azure AD. (Adres URL strony docelowej jest określony na stronie [konfiguracji technicznej](plan-saas-offer.md#technical-information) oferty.

Pierwszym krokiem do korzystania z tożsamości jest upewnienie się, że strona docelowa jest zarejestrowana jako aplikacja usługi Azure AD. Zarejestrowanie aplikacji pozwala używać usługi Azure AD do uwierzytelniania użytkowników i żądania dostępu do zasobów użytkownika. Może być uważana za definicję aplikacji, która pozwala usłudze poznać, jak wystawiać tokeny dla aplikacji na podstawie ustawień aplikacji.

### <a name="register-a-new-application-using-the-azure-portal"></a>Rejestrowanie nowej aplikacji w witrynie Azure Portal

Aby rozpocząć, postępuj zgodnie z instrukcjami dotyczącymi [rejestrowania nowej aplikacji](../active-directory/develop/quickstart-register-app.md). Aby umożliwić użytkownikom z innych firm, odwiedzanie aplikacji, należy wybrać jedną z opcji wielodostępnych, gdy zostanie wyświetlony monit, kto może korzystać z aplikacji.

Jeśli zamierzasz wykonać zapytanie dotyczące interfejsu API Microsoft Graph, [Skonfiguruj nową aplikację w celu uzyskiwania dostępu do interfejsów API sieci Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Po wybraniu uprawnień interfejsu API dla tej aplikacji wartość domyślna **User. Read** jest wystarczająca, aby zebrać podstawowe informacje o kupującym, które mają być bezproblemowo i automatyczne. Nie Żądaj żadnych uprawnień interfejsu API oznaczonych jako **wymagające zgody administratora** , ponieważ spowoduje to zablokowanie na stronie docelowej wszystkich użytkowników niebędących administratorami.

Jeśli potrzebujesz podwyższonych uprawnień w ramach procesu dołączania lub inicjowania obsługi administracyjnej, rozważ użycie funkcji [przyrostowej zgody](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) usługi Azure AD, aby wszyscy kupujący z portalu Marketplace mogli interaktywnie korzystać ze strony docelowej.

## <a name="use-a-code-sample-as-a-starting-point"></a>Użyj przykładu kodu jako punktu początkowego

Udostępniamy kilka przykładowych aplikacji, które implementują prostą witrynę sieci Web z włączonym logowaniem usługi Azure AD. Po zarejestrowaniu aplikacji w usłudze Azure AD blok **szybkiego startu** oferuje listę typowych typów aplikacji i stosów programowania, jak pokazano na rysunku 1. Wybierz ten, który odpowiada Twojemu środowisku, i postępuj zgodnie z instrukcjami dotyczącymi pobierania i konfigurowania.

**_Rysunek 1. blok szybki start w Azure Portal_* _

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Ilustruje blok szybkiego startu w Azure Portal.":::

Po pobraniu kodu i skonfigurowaniu środowiska programistycznego Zmień ustawienia konfiguracji w aplikacji w celu odzwierciedlenia identyfikatora aplikacji, identyfikatora dzierżawy i wpisu tajnego klienta zarejestrowanego w poprzedniej procedurze. Należy zauważyć, że dokładne kroki różnią się w zależności od używanego przykładu.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Korzystanie z dwóch aplikacji usługi Azure AD w celu zwiększenia bezpieczeństwa w środowisku produkcyjnym

W tym artykule przedstawiono uproszczoną wersję architektury służącą do implementowania strony docelowej dla komercyjnej oferty SaaS w portalu Marketplace. Podczas uruchamiania strony w środowisku produkcyjnym zalecamy podwyższenie poziomu zabezpieczeń, komunikując się z interfejsami API realizacji SaaS tylko za pomocą innej, zabezpieczonej aplikacji. Wymaga to utworzenia dwóch nowych aplikacji:

- Najpierw aplikacja wielodostępna do strony docelowej z wieloma dzierżawcami opisana do tego punktu, z wyjątkiem sytuacji, w której nie można skontaktować się z interfejsami API realizacji SaaS. Ta funkcjonalność zostanie odciążać do innej aplikacji, zgodnie z poniższym opisem.
- Druga aplikacja do obsługi komunikacji z interfejsami API realizacji SaaS. Ta aplikacja powinna być tylko jedną dzierżawą, która ma być używana przez organizację, i można nawiązać listę kontroli dostępu, aby ograniczyć dostęp do interfejsów API tylko z tej aplikacji.

Dzięki temu rozwiązanie będzie działało w scenariuszach, które obserwują zasadę [oddzielenia obaw](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) . Na przykład strona docelowa używa pierwszej zarejestrowanej aplikacji usługi Azure AD w celu zalogowania użytkownika. Po zalogowaniu się użytkownika strona docelowa używa drugiej usługi Azure AD do żądania tokenu dostępu w celu wywołania interfejsu API realizacji SaaS i wywołania operacji rozpoznawania.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Rozpoznawanie tokenu identyfikacji zakupu w portalu Marketplace

Gdy Kupujący zostanie wysłany do strony docelowej, do parametru adresu URL zostanie dodany token. Token ten różni się od tokenu wystawionego przez usługę Azure AD i tokenu dostępu używanego do uwierzytelniania między usługami i służy jako dane wejściowe dla [interfejsów API realizacji SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) rozwiązanie umożliwiające uzyskanie szczegółów dotyczących subskrypcji. Podobnie jak w przypadku wszystkich wywołań interfejsów API realizacji SaaS, żądanie usługi do obsługi zostanie uwierzytelnione przy użyciu tokenu dostępu opartego na IDENTYFIKATORze aplikacji usługi Azure AD dla aplikacji na potrzeby uwierzytelniania między usługami.

> [!NOTE]
> W większości przypadków preferowane jest wywołanie tego wywołania z drugiej aplikacji z jedną dzierżawą. Zobacz [Korzystanie z dwóch aplikacji usługi Azure AD, aby zwiększyć bezpieczeństwo w środowisku produkcyjnym](#use-two-azure-ad-apps-to-improve-security-in-production) wcześniej w tym artykule.

### <a name="request-an-access-token"></a>Żądanie tokenu dostępu

Aby uwierzytelnić aplikację przy użyciu interfejsów API realizacji SaaS, potrzebujesz tokenu dostępu, który można wygenerować, wywołując punkt końcowy protokołu OAuth usługi Azure AD. Zobacz [, jak uzyskać Token autoryzacji wydawcy](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

### <a name="call-the-resolve-endpoint"></a>Wywoływanie punktu końcowego rozpoznawania

Interfejsy API realizacji SaaS implementują [rozwiązanie](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) punktu końcowego, który można wywołać w celu potwierdzenia ważności tokenu portalu Marketplace i zwrócenia informacji o subskrypcji.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Odczytaj informacje z oświadczeń zakodowanych w tokenie identyfikatora

W ramach przepływu [połączenia OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) usługa Azure AD dodaje [token identyfikatora](../active-directory/develop/id-tokens.md) do żądania, gdy Kupujący jest wysyłany do strony docelowej. Ten token zawiera wiele podstawowych informacji, które mogą być przydatne w procesie aktywacji, w tym informacje widoczne w tej tabeli.

| Wartość | Opis |
| ------------ | ------------- |
| AUD | Zamierzone odbiorcy dla tego tokenu. W takim przypadku powinna być zgodna z IDENTYFIKATORem aplikacji i być zweryfikowany. |
| preferred_username | Podstawowa nazwa użytkownika, którego odwiedzasz. Może to być adres e-mail, numer telefonu lub inny identyfikator. |
| poczta e-mail | Adres e-mail użytkownika. Należy zauważyć, że to pole może być puste. |
| name | Wartość, którą można odczytać przez człowieka, która identyfikuje podmiot tokenu. W takim przypadku będzie to nazwa kupującego. |
| OID | Identyfikator w systemie tożsamości firmy Microsoft, który jednoznacznie identyfikuje użytkownika w aplikacjach. Microsoft Graph zwróci tę wartość jako właściwość identyfikatora dla danego konta użytkownika. |
| TID | Identyfikator reprezentujący dzierżawę usługi Azure AD, z której pochodzi kupujący. W przypadku tożsamości MSA zawsze będzie to możliwe ``9188040d-6c67-4c5b-b112-36a304b66dad`` . Aby uzyskać więcej informacji, zobacz uwagi w następnej sekcji: Użyj interfejsu API Microsoft Graph. |
| Sub | Identyfikator, który jednoznacznie identyfikuje użytkownika w tej konkretnej aplikacji. |
|||

## <a name="use-the-microsoft-graph-api"></a>Korzystanie z interfejsu API programu Microsoft Graph

Token identyfikatora zawiera podstawowe informacje umożliwiające identyfikację kupującego, ale proces aktywacji może wymagać dodatkowych szczegółów, takich jak firma kupująca — w celu ukończenia procesu dołączania. Użyj [interfejsu API Microsoft Graph](/graph/use-the-api) , aby zażądać tych informacji, aby uniknąć wymuszania wprowadzania tych szczegółów przez użytkownika. Standardowe _ *użytkownika. odczyt* * domyślnie są dostępne następujące informacje.

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

Dodatkowe właściwości — takie jak nazwa firmy użytkownika lub lokalizacja użytkownika (kraj) — można wybrać do uwzględnienia w żądaniu. Aby uzyskać więcej informacji [, zobacz właściwości typu zasobu użytkownika](/graph/api/resources/user?view=graph-rest-1.0&preserve-view=true#properties) .

Większość aplikacji, które są zarejestrowane w usłudze Azure AD, przyznaje delegowane uprawnienia do odczytu informacji o użytkowniku z dzierżawy usługi Azure AD swojej firmy. Każdemu żądaniu do Microsoft Graph informacji musi towarzyszyć token dostępu do uwierzytelniania. Określone kroki w celu wygenerowania tokenu dostępu będą zależeć od stosu technologii, który jest używany, ale przykładowy kod będzie zawierać przykład. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu w imieniu użytkownika](/graph/auth-v2-user).

> [!NOTE]
> Konta z dzierżawy MSA (z IDENTYFIKATORem dzierżawy ``9188040d-6c67-4c5b-b112-36a304b66dad`` ) nie zwracają więcej informacji, niż została już zebrana z tokenem ID. Możesz pominąć to wywołanie w interfejs API programu Graph dla tych kont.

## <a name="next-steps"></a>Następne kroki

- [Jak utworzyć ofertę SaaS w komercyjnej witrynie Marketplace](create-new-saas-offer.md)