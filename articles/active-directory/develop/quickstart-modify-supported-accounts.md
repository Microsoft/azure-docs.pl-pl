---
title: 'Szybki Start: modyfikowanie firmy Microsoft identyfikacja kont aplikacji platformy | Azure'
description: Ten przewodnik Szybki Start umożliwia skonfigurowanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft w celu zmiany użytkownika lub konta, które mogą uzyskiwać dostęp do aplikacji.
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
ms.openlocfilehash: d143bde9c22bc726f00b5c209d1b7fbc131905b0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91258017"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Szybki Start: modyfikowanie kont obsługiwanych przez aplikację

Podczas rejestrowania aplikacji na platformie tożsamości firmy Microsoft możesz zdecydować, że aplikacja powinna być dostępna tylko dla użytkowników w Twojej organizacji. Możesz także planować udostępnienie aplikacji użytkownikom w organizacjach zewnętrznych lub użytkownikom w organizacjach zewnętrznych i użytkownikom nienależącym do organizacji (kontom osobistym).

Z tego przewodnika Szybki start dowiesz się, jak zmodyfikować konfigurację aplikacji pod kątem zmiany zbioru osób (kont) z dostępem do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md)

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

1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji**.
1. Znajdź i wybierz aplikację do skonfigurowania. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji lub główną stronę rejestracji.
1. Na stronie **Przegląd** aplikacji wybierz sekcję **Uwierzytelnianie**.
1. W obszarze **Ustawienia zaawansowane** znajdź sekcję **Niejawne udzielenie**.
1. Wybierz pozycję **Tokeny identyfikatora**, **Tokeny dostępu** lub je obie.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md)
