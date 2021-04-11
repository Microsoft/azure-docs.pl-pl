---
title: Integracja IDology z usługą Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować przykładową aplikację płatniczą online w Azure AD B2C z usługą IDology. IDology to weryfikacja tożsamości i dostawca weryfikacji z wieloma rozwiązaniami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: df0fcaf7987e30f9c2599346aaef5fcabb4f04e9
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256654"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania IDology z Azure Active Directory B2C 

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integrowania Azure AD B2C z usługą [IDology](https://www.idology.com/solutions/). IDology to weryfikacja tożsamości i dostawca weryfikacji z wieloma rozwiązaniami. W tym przykładzie będziemy pokryć ExpectID rozwiązanie według IDology.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

## <a name="scenario-description"></a>Opis scenariusza

Integracja IDology obejmuje następujące składniki:

- Azure AD B2C — serwer autoryzacji odpowiedzialny za Weryfikowanie poświadczeń użytkownika. Jest on również znany jako dostawca tożsamości.
- IDology — usługa IDology przyjmuje dane wejściowe dostarczone przez użytkownika i weryfikuje tożsamość użytkownika.
- Niestandardowy interfejs API REST — ten interfejs API implementuje integrację między usługami Azure AD i IDology.

Na poniższym diagramie architektury przedstawiono implementację.

![Diagram architektury IDology](media/partner-idology/idology-architecture-diagram.png)

| Krok | Opis |
|------|------|
|1     | Użytkownik dociera do strony logowania. |
|2     | Użytkownik wybierze opcję rejestracji, aby utworzyć nowe konto, a następnie wprowadzić informacje na stronie. Azure AD B2C zbiera atrybuty użytkownika. |
|3     | Azure AD B2C wywołuje interfejs API warstwy środkowej i przekazuje atrybuty użytkownika. |
|4     | Interfejs API warstwy środkowej zbiera atrybuty użytkownika i przekształca je w format, który może zużywać interfejs API IDOlogy. Następnie wysyła informacje do IDology. |
|5     | IDology zużywa informacje i przetwarza je, a następnie zwraca wynik do interfejsu API warstwy środkowej. |
|6     | Interfejs API warstwy środkowej przetwarza informacje i wysyła odpowiednie informacje z powrotem do Azure AD B2C. |
|7     | Azure AD B2C otrzymuje informacje z z interfejsu API platformy środkowej. Jeśli zostanie wyświetlona odpowiedź na **awarię** , zostanie wyświetlony komunikat o błędzie dla użytkownika. Jeśli zostanie wyświetlona odpowiedź o **powodzeniu** , użytkownik zostanie uwierzytelniony i zapisany w katalogu. |
|      |      |

> [!NOTE]
> Azure AD B2C może również polecić klientowi przeprowadzenie uwierzytelniania krokowego, ale ten scenariusz jest poza zakresem tego samouczka.

## <a name="onboard-with-idology"></a>Dołączanie do IDology

1. IDology oferuje różne rozwiązania, które można znaleźć [tutaj](https://www.idology.com/solutions/). W tym przykładzie używamy ExpectID.

2. Aby utworzyć konto IDology, skontaktuj się z [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/).

3. Po utworzeniu konta otrzymasz informacje potrzebne do konfiguracji interfejsu API. W poniższych sekcjach opisano proces.

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

### <a name="part-1---deploy-the-api"></a>Część 1 — wdrażanie interfejsu API

Wdróż podany [kod interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) w usłudze platformy Azure. Kod można opublikować z programu Visual Studio, postępując zgodnie z tymi [instrukcjami](/visualstudio/deployment/quickstart-deploy-to-azure).

Aby skonfigurować usługę Azure AD przy użyciu wymaganych ustawień, należy podać adres URL wdrożonej usługi.

### <a name="part-2---configure-the-api"></a>Część 2 — Konfigurowanie interfejsu API 

Ustawienia aplikacji można [skonfigurować w App Service na platformie Azure](../app-service/configure-common.md#configure-app-settings). Za pomocą tej metody ustawienia można bezpiecznie skonfigurować bez sprawdzania ich w repozytorium. Należy podać następujące ustawienia w interfejsie API REST:

| Ustawienia aplikacji | Element źródłowy | Uwagi |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | Konfiguracja konta IDology |     |
|IdologySettings:ApiPassword | Konfiguracja konta IDology |     |
|WebApiSettings:ApiUsername |Zdefiniuj nazwę użytkownika dla interfejsu API| Używane w konfiguracji ExtId |
|WebApiSettings:ApiPassword | Definiowanie hasła dla interfejsu API | Używane w konfiguracji ExtId

### <a name="part-3---create-api-policy-keys"></a>Część 3 — Tworzenie kluczy zasad interfejsu API

Postępuj zgodnie z tym [dokumentem](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) , aby utworzyć dwa klucze zasad: jeden dla nazwy użytkownika interfejsu API i jeden dla hasła interfejsu API zdefiniowanego powyżej.

Przykładowe zasady używają tych nazw kluczy:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Część 4 — Konfigurowanie zasad Azure AD B2C

1. Postępuj zgodnie z tym [dokumentem](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) , aby pobrać [pakiet LocalAccounts Starter](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) i skonfigurować zasady dla dzierżawy Azure AD B2C. Postępuj zgodnie z instrukcjami, aby zakończyć sekcję **testowanie zasad niestandardowych** .

2. Pobierz dwa przykładowe zasady [tutaj](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy).

3. Zaktualizuj dwie przykładowe zasady:

   1. Otwórz obie zasady:

      1. W sekcji `Idology-ExpectId-API` zaktualizuj `ServiceUrl` element metadanych przy użyciu lokalizacji wdrożonego powyżej interfejsu API.

      1. Zamień `yourtenant` na nazwę dzierżawy Azure AD B2C.
      Na przykład jeśli nazwa dzierżawy Azure AD B2C to  `contosotenant` , Zastąp wszystkie wystąpienia elementu  `yourtenant.onmicrosoft.com`   `contosotenant.onmicrosoft.com` .

   1. Otwórz TrustFrameworkExtensions.xml pliku:

      1. Znajdź element  `<TechnicalProfile Id="login-NonInteractive">` . Zastąp oba wystąpienia z  `IdentityExperienceFrameworkAppId`   identyfikatorem aplikacji utworzonej wcześniej aplikacji IdentityExperienceFramework.

      1. Zastąp oba wystąpienia z  `ProxyIdentityExperienceFrameworkAppId`   identyfikatorem aplikacji utworzonej wcześniej aplikacji ProxyIdentityExperienceFramework.

4. Zastąp SignInorSignUp.xml i TrustFrameworkExtensions.xml wcześniej przekazany do Azure AD B2C w kroku 1 z dwiema zaktualizowanymi przykładowymi zasadami.

> [!NOTE]
> Najlepszym rozwiązaniem jest, aby klienci mogli dodać powiadomienie o zgodzie na stronie kolekcji atrybutów. Powiadom użytkowników, że informacje będą wysyłane do usług innych firm w celu weryfikacji tożsamości.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze **zasady** wybierz pozycję **przepływy użytkownika**.

2. Wybierz wcześniej utworzony **przepływ użytkownika**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   1. **Aplikacja** — wybierz zarejestrowanej aplikacji (przykład: JWT).

   1. **Adres URL odpowiedzi** — wybierz **adres URL przekierowania**.

   1. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź do przepływu rejestracji i Utwórz konto.

5. Wyloguj się.

6. Przejdź przez przepływ logowania.

7. IDology Układanka zostanie wyświetlona po wprowadzeniu **dalej**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
