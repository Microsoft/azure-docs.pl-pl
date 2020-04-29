---
title: Modyfikowanie firmy Microsoft identyfikacja kont aplikacji platformy | Azure
description: Konfigurowanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft pod kątem zmiany zbioru osób (kont) z dostępem do aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 94fed6f4aa62c7e649cf7d644e571b30561e0da4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80050241"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Szybki Start: modyfikowanie kont obsługiwanych przez aplikację

Podczas rejestrowania aplikacji na platformie tożsamości firmy Microsoft możesz zdecydować, że aplikacja powinna być dostępna tylko dla użytkowników w Twojej organizacji. Możesz także planować udostępnienie aplikacji użytkownikom w organizacjach zewnętrznych lub użytkownikom w organizacjach zewnętrznych i użytkownikom nienależącym do organizacji (kontom osobistym).

Z tego przewodnika Szybki start dowiesz się, jak zmodyfikować konfigurację aplikacji pod kątem zmiany zbioru osób (kont) z dostępem do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy upewnij się, że są spełnione następujące wymagania wstępne:

* Znasz obsługiwane [uprawnienia i zgody](v2-permissions-and-consent.md), ponieważ ich znajomość jest istotna podczas tworzenia aplikacji, które mają być używane przez innych użytkowników lub aplikacje.
* Masz dzierżawę z zarejestrowanymi w niej aplikacjami.
  * Jeśli nie masz zarejestrowanych aplikacji, [dowiedz się, jak zarejestrować aplikacje za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logowanie do witryny Azure Portal i wybranie aplikacji

Przed skonfigurowaniem aplikacji wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji**.
1. Znajdź i wybierz aplikację do skonfigurowania. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji lub główną stronę rejestracji.
1. Postępuj zgodnie z instrukcjami, aby [zmienić rejestrację aplikacji pod kątem obsługi różnych kont](#change-the-application-registration-to-support-different-accounts).
1. W przypadku aplikacji z jedną stroną [włącz niejawne udzielenie uwierzytelniania OAuth 2.0](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Zmiana rejestracji aplikacji pod kątem obsługi różnych kont

Jeśli piszesz aplikację, którą ma być dostępna dla klientów lub partnerów spoza organizacji, musisz zaktualizować definicję aplikacji w witrynie Azure Portal.

> [!IMPORTANT]
> Usługa Azure AD wymaga, aby identyfikator URI identyfikatora aplikacji wielodostępnej był globalnie unikatowy. Identyfikator URI identyfikatora aplikacji jest jednym ze sposobów, w jaki aplikacja jest identyfikowana w komunikatach protokołu. W przypadku aplikacji jednodostępnej wystarczy, aby identyfikator URI identyfikatora aplikacji był unikatowy w obrębie tej dzierżawy. W przypadku aplikacji wielodostępnej ten identyfikator musi być globalnie unikatowy, dzięki czemu usługa Azure AD będzie mogła znaleźć aplikację we wszystkich dzierżawach. Globalna unikatowość jest wymuszana poprzez wymaganie, aby identyfikator URI identyfikatora aplikacji miał nazwę hosta, która jest zgodna ze zweryfikowaną domeną dzierżawy usługi Azure AD. Jeśli na przykład nazwa dzierżawy to contoso.onmicrosoft.com, prawidłowy identyfikator URI identyfikatora aplikacji będzie mieć postać `https://contoso.onmicrosoft.com/myapp`. Jeśli Twoja dzierżawa ma zweryfikowaną domenę contoso.com, prawidłowy identyfikator URI identyfikatora aplikacji będzie również miał postać `https://contoso.com/myapp`. Jeśli identyfikator URI identyfikatora aplikacji nie jest zgodny z tym wzorcem, ustawienie aplikacji jako aplikacji wielodostępnej zakończy się niepowodzeniem.

### <a name="to-change-who-can-access-your-application"></a>Modyfikowanie zbioru osób z dostępem do aplikacji

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Uwierzytelnianie** i zmień wartość wybraną w pozycji **Obsługiwane typy konta**.
    * Wybierz pozycję **Konta tylko w tym katalogu organizacyjnym**, jeśli tworzysz aplikację biznesową. Ta opcja nie jest dostępna, jeśli aplikacja nie jest zarejestrowana w katalogu.
    * Wybierz pozycję **Konta w dowolnym katalogu organizacyjnym**, jeśli aplikacja jest przeznaczona dla wszystkich klientów biznesowych i edukacyjnych.
    * Wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**, aby przeznaczyć aplikację dla najszerszego możliwego grona klientów.
1. Wybierz pozycję **Zapisz**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Włączanie przyznawania niejawnego protokołu OAuth 2.0 dla aplikacji jednostronicowych

Aplikacje jednostronicowe (SPA) mają zwykle utworzony przy użyciu języka JavaScript fronton działający w przeglądarce, który wywołuje internetowy interfejs API aplikacji zaplecza w celu wykonania logiki biznesowej. W przypadku aplikacji jednostronicowych hostowanych w usłudze Azure AD do uwierzytelniania użytkownika w usłudze Azure AD i uzyskiwania tokenu, którego można używać do zabezpieczania wywołań z klienta JavaScript aplikacji do internetowego interfejsu API zaplecza, używane jest przyznawanie niejawne protokołu OAuth 2.0.

Po udzieleniu zgody przez użytkownika tego samego protokołu uwierzytelniania można użyć w celu uzyskania tokenów służących do zabezpieczania wywołań między klientem i innymi zasobami internetowego interfejsu API skonfigurowanymi dla aplikacji. Aby dowiedzieć się więcej o niejawnym przyznawaniu autoryzacji i ułatwić sobie podjęcie decyzji, czy jest to rozwiązanie odpowiednie dla Twojego scenariusza aplikacji, poznaj przepływ niejawnego udzielenia uwierzytelniania OAuth 2.0 w usłudze Azure AD [1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md) i [2.0](v2-oauth2-implicit-grant-flow.md).

Domyślnie niejawne udzielenie protokołu OAuth 2.0 jest wyłączone dla aplikacji. Niejawne udzielenie protokołu OAuth 2.0 można włączyć dla aplikacji, wykonując kroki opisane poniżej.

### <a name="to-enable-oauth-20-implicit-grant"></a>Włączanie przyznawania niejawnego protokołu OAuth 2.0

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Uwierzytelnianie**.
1. W obszarze **Ustawienia zaawansowane** znajdź sekcję **Niejawne udzielenie**.
1. Wybierz pozycję **Tokeny identyfikatora**, **Tokeny dostępu** lub je obie.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych powiązanych przewodników Szybki start dotyczących zarządzania aplikacjami:

* [Rejestrowanie aplikacji za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md)
* [Konfigurowanie aplikacji klienckiej na potrzeby uzyskiwania dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md)
* [Konfigurowanie aplikacji w celu uwidocznienia internetowych interfejsów API](quickstart-configure-app-expose-web-apis.md)
* [Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft](quickstart-remove-app.md)

Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

Aby dowiedzieć się więcej o wytycznych dotyczących oznaczania marką, które należy stosować podczas opracowywania aplikacji przy użyciu usługi Azure Active Directory, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).
