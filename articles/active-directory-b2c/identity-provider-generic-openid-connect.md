---
title: Konfigurowanie rejestrowania i logowania przy użyciu połączenia OpenID Connect
titleSuffix: Azure AD B2C
description: Skonfiguruj konto i zaloguj się za pomocą dowolnego dostawcy tożsamości OpenID Connect Connect (dostawcy tożsamości) w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9434bd4042798dc05a33401e1884e11a73774936
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102448340"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się za pomocą usługi OpenID Connect Connect przy użyciu Azure Active Directory B2C

[OpenID Connect Connect](openid-connect.md) to protokół uwierzytelniania oparty na protokole OAuth 2,0, który może służyć do bezpiecznego logowania użytkowników. Większość dostawców tożsamości korzystających z tego protokołu jest obsługiwana w Azure AD B2C. W tym artykule wyjaśniono, jak dodać niestandardowych dostawców tożsamości OpenID Connect Connect do przepływów użytkownika.

## <a name="add-the-identity-provider"></a>Dodawanie dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, klikając filtr **Directory + Subscription** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Nowy dostawca połączenia OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź *contoso*.

## <a name="configure-the-identity-provider"></a>Konfigurowanie dostawcy tożsamości

Każdy dostawca tożsamości OpenID Connect Connect zawiera opis dokumentu metadanych, który zawiera większość informacji wymaganych do przeprowadzenia logowania. Obejmuje to takie informacje, jak adresy URL, które mają być używane, oraz lokalizację publicznych kluczy podpisywania usługi. Dokument metadanych OpenID Connect Connect zawsze znajduje się w punkcie końcowym, który się skończy `.well-known/openid-configuration` . Dla dostawcy tożsamości OpenID Connect Connect, który chcesz dodać, wprowadź adres URL metadanych.

## <a name="client-id-and-secret"></a>Identyfikator klienta i klucz tajny

Aby umożliwić użytkownikom logowanie się, dostawca tożsamości wymaga od deweloperów zarejestrowania aplikacji w swojej usłudze. Ta aplikacja ma identyfikator, który jest określany jako **Identyfikator klienta** i **klucz tajny klienta**. Skopiuj te wartości z dostawcy tożsamości i wprowadź je do odpowiednich pól.

> [!NOTE]
> Wpis tajny klienta jest opcjonalny. Należy jednak wprowadzić klucz tajny klienta, jeśli chcesz użyć [przepływu kodu autoryzacji](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), który używa klucza tajnego do wymiany kodu dla tokenu.

## <a name="scope"></a>Zakres

Zakres definiuje informacje i uprawnienia, które chcesz zebrać od niestandardowego dostawcy tożsamości. Żądania połączenia OpenID Connect muszą zawierać `openid` wartość zakresu w celu uzyskania tokenu identyfikatora od dostawcy tożsamości. Bez tokenu identyfikatora użytkownicy nie mogą zalogować się do Azure AD B2C przy użyciu niestandardowego dostawcy tożsamości. Inne zakresy można dołączać rozdzielone spacjami. Zapoznaj się z dokumentacją niestandardowego dostawcy tożsamości, aby zobaczyć, jakie inne zakresy mogą być dostępne.

## <a name="response-type"></a>Typ odpowiedzi

Typ odpowiedzi opisuje, jakiego rodzaju informacje są wysyłane z powrotem podczas początkowego wywołania do `authorization_endpoint` niestandardowego dostawcy tożsamości. Można użyć następujących typów odpowiedzi:

* `code`: Zgodnie z [przepływem kodu autoryzacji](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), kod zostanie zwrócony z powrotem do Azure AD B2C. Azure AD B2C przechodzi do wywołania programu w `token_endpoint` celu wymiany kodu dla tokenu.
* `id_token`: Token identyfikatora jest zwracany z powrotem do Azure AD B2C z niestandardowego dostawcy tożsamości.

## <a name="response-mode"></a>Tryb odpowiedzi

Tryb odpowiedzi definiuje metodę, która powinna być używana do wysyłania danych z powrotem z niestandardowego dostawcy tożsamości do Azure AD B2C. Można używać następujących trybów odpowiedzi:

* `form_post`: Ten tryb odpowiedzi jest zalecany w celu uzyskania najlepszego zabezpieczenia. Odpowiedź jest przesyłana przez metodę HTTP `POST` , z kodem lub tokenem zakodowanym w treści przy użyciu `application/x-www-form-urlencoded` formatu.
* `query`: Kod lub token jest zwracany jako parametr zapytania.

## <a name="domain-hint"></a>Wskazówki dotyczące domeny

Wskazówki dotyczącej domeny można użyć, aby pominąć bezpośrednio na stronie logowania określonego dostawcy tożsamości, zamiast wybierać użytkowników z listy dostępnych dostawców tożsamości. Aby zezwolić na tego rodzaju zachowanie, wprowadź wartość dla wskazówki dotyczącej domeny. Aby przejść do niestandardowego dostawcy tożsamości, należy dołączyć parametr `domain_hint=<domain hint value>` do końca żądania podczas wywoływania Azure AD B2C logowania.

## <a name="claims-mapping"></a>Mapowanie oświadczeń

Gdy niestandardowy dostawca tożsamości wyśle token identyfikatora z powrotem do Azure AD B2C, Azure AD B2C musi mieć możliwość mapowania oświadczeń od otrzymanego tokenu do oświadczeń, które Azure AD B2C rozpoznawane i używane. Dla każdego z poniższych mapowań zapoznaj się z dokumentacją niestandardowego dostawcy tożsamości, aby zrozumieć oświadczenia, które są zwracane z powrotem do tokenów dostawcy tożsamości:

* **Identyfikator użytkownika**: wprowadź wartość, która zapewnia *unikatowy identyfikator* zalogowanego użytkownika.
* **Nazwa wyświetlana**: wprowadź wartość, która zawiera *nazwę wyświetlaną* lub *pełną nazwę* użytkownika.
* Imię i **nazwisko**: wprowadź wartość, która zawiera *imię* użytkownika.
* **Nazwisko**: wprowadź wartość, która zawiera *nazwisko* użytkownika.
* **Adres e-mail**: wprowadź wartość, która zawiera *adres e-mail* użytkownika.

## <a name="add-the-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości. 
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz dodany dostawca tożsamości. Na przykład *contoso*.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom przepływ użytkownika** .
1. Na stronie rejestracji lub logowania wybierz dostawcę tożsamości, który ma być zalogowany. Na przykład *contoso*.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.
