---
title: Samouczek konfigurowania usługi Less przy użyciu Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Samouczek konfigurowania usługi Less przy użyciu Azure Active Directory B2C do uwierzytelniania bezhasła
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 7e6f19e03eee6fb6ddf946ea79d197a231f5f113
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690632"
---
# <a name="tutorial-for-configuring-keyless-with-azure-active-directory-b2c"></a>Samouczek konfigurowania usługi Less przy użyciu Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące konfigurowania Azure Active Directory (AD) B2C z [mniejszą ilością](https://keyless.io/). Korzystając z Azure AD B2C jako dostawcy tożsamości, możesz zintegrować program z dowolną obsługą klienta, aby zapewnić użytkownikom prawdziwe uwierzytelnianie bez hasła.

Mniej niż **Zero-Knowledge biometryczne rozwiązania™** nie zapewnia uwierzytelniania wieloskładnikowego bez hasła, które eliminuje oszustwo, phishing i ponowne użycie poświadczeń — wszystko to podczas ulepszania środowiska klienta i ochrony prywatności.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Dzierżawca musi być połączony z subskrypcją platformy Azure.

- Bezpłatna dzierżawa w chmurze — Uzyskaj bezpłatne [Konto próbne](https://keyless.io/go).

- Aplikacja wystawcy nie może być zainstalowana na urządzeniu użytkownika.

## <a name="scenario-description"></a>Opis scenariusza

Integracja z niemniejszą ilością obejmuje następujące składniki:

- Azure AD B2C — serwer autoryzacji odpowiedzialny za Weryfikowanie poświadczeń użytkownika, nazywanych również dostawcą tożsamości.

- Aplikacje internetowe i mobilne — Twoje aplikacje mobilne lub sieci Web, które chcesz chronić przy użyciu kluczy mniejszych i Azure AD B2C.

- Aplikacja mobilna bez oprogramowania — aplikacja mobilna bez oprogramowania zostanie użyta do uwierzytelniania w aplikacjach z włączonymi Azure AD B2C.

Na poniższym diagramie architektury przedstawiono implementację.

![Obraz przedstawia diagram architektury less](./media/partner-keyless/keyless-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dotarł do strony logowania. Użytkownicy wybierają logowanie/logowanie się i wprowadzają nazwę użytkownika
| 2. | Aplikacja wysyła atrybuty użytkownika w celu Azure AD B2C na potrzeby weryfikacji tożsamości.
| 3. | Azure AD B2C gromadzi atrybuty użytkownika i wysyła atrybuty do mniejszej ilości, aby uwierzytelnić użytkownika za pomocą aplikacji mobilnej bez oprogramowania.
| 4. | Opcja nie wysyła powiadomień wypychanych do urządzenia przenośnego zarejestrowanego użytkownika w celu zachowania poufności uwierzytelniania w postaci skanowania biometrycznego.
| 5. | Gdy użytkownik odpowie na powiadomienie wypychane, użytkownikowi zostanie udzielony lub odmówiony dostęp do aplikacji klienta na podstawie wyników weryfikacji.

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Dodawanie nowego dostawcy tożsamości

Aby dodać nowego dostawcę tożsamości, wykonaj następujące kroki:

1. Zaloguj się do **[Azure Portal](https://portal.azure.com/#home)** jako Administrator globalny dzierżawy Azure AD B2C.

2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.

4. Przejdź do **pulpitu nawigacyjnego**  >  **Azure Active Directory B2C**  >   **dostawców tożsamości**

5. Wybierz pozycję **dostawcy tożsamości**.

6. Wybierz pozycję **Dodaj**.

### <a name="configure-an-identity-provider"></a>Konfigurowanie dostawcy tożsamości

Aby skonfigurować dostawcę tożsamości, wykonaj następujące kroki:

1. Wybierz **Typ dostawcy tożsamości**  >  **OpenID Connect Connect (wersja zapoznawcza)**
2. Wypełnij formularz, aby skonfigurować dostawcę tożsamości:

   |Właściwość | Wartość |
   |:-----| :-----------|
   | Nazwa   | Bez kluczy |
   | Adres URL metadanych | Wstaw identyfikator URI hostowanej aplikacji uwierzytelniania bez użycia, a następnie ścieżkę określoną na przykład https://keyless.auth/.well-known/openid-configuration |
   | Klucz tajny klienta | Wpis tajny skojarzony z wystąpieniem uwierzytelniania nie jest taki sam jak wcześniej skonfigurowany. Wstaw złożony ciąg. Ten klucz tajny będzie później używany w konfiguracji kontenera bez kluczy.|
   | Identyfikator klienta | Identyfikator klienta. Ten identyfikator zostanie użyty w dalszej części konfiguracji kontenera.|
   | Zakres | OpenID Connect |
   | Typ odpowiedzi | id_token |
   | Tryb odpowiedzi | form_post|

3. Wybierz przycisk **OK**.

4. Wybierz pozycję **Mapuj oświadczenia tego dostawcy tożsamości**.

5. Wypełnij formularz, aby zamapować dostawcę tożsamości:

   |Właściwość | Wartość |
   |:-----| :-----------|
   | UserID    | Z subskrypcji |
   | Nazwa wyświetlana | Z subskrypcji |
   | Tryb odpowiedzi | Z subskrypcji |

6. Wybierz pozycję **Zapisz** , aby zakończyć instalację nowego dostawcy tożsamości programu Open ID Connect (OIDC).

### <a name="create-a-user-flow-policy"></a>Tworzenie zasad przepływu użytkownika

Powinien być teraz widoczny jako nowy dostawca tożsamości OIDC wymieniony w ramach dostawców tożsamości B2C.

1. W dzierżawie Azure AD B2C w obszarze **zasady** wybierz pozycję **przepływy użytkownika**.

2. Wybierz pozycję **Nowy** przepływ użytkownika.

3. Wybierz pozycję **zarejestruj się i zaloguj**, wybierz **wersję**, a następnie wybierz pozycję **Utwórz**.

4. Podaj **nazwę** zasad.

5. W sekcji dostawcy tożsamości wybierz nowo utworzony dostawca tożsamości o niższym stopniu.

6. Skonfiguruj parametry przepływu użytkownika. Wstaw nazwę i wybierz utworzonego dostawcę tożsamości. Możesz również dodać adres e-mail. W takim przypadku platforma Azure nie będzie przekierowywać procedury logowania bezpośrednio do opcji nie rzadziej, zamiast tego będzie wyświetlał ekran, w którym użytkownik może wybrać opcję, która ma zostać użyta.

7. Pozostaw pole **uwierzytelnianie wieloskładnikowe** jako.

8. Wybierz pozycję **Wymuszaj zasady dostępu warunkowego**

9. W obszarze **atrybuty użytkownika i oświadczenia tokenu** wybierz opcję **adres E-mail** w polu Zbieraj atrybutu. Można dodać wszystkie atrybuty, które Azure Active Directory mogą zbierać informacje o użytkowniku wraz z oświadczeniami, które Azure AD B2C mogą zwrócić do aplikacji klienckiej.

10. Wybierz przycisk **Utwórz**.

11. Po pomyślnym utworzeniu wybierz nowy **przepływ użytkownika**.

12. Na panelu po lewej stronie wybierz pozycję **oświadczenia aplikacji**. W obszarze Opcje zaznacz pole wyboru **Wyślij wiadomość e-mail** , a następnie wybierz pozycję **Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję platforma obsługi tożsamości.

2. Wybierz wcześniej utworzone SignUpSignIn.

3. Wybierz pozycję Uruchom przepływ użytkownika i wybierz ustawienia:

   a. Aplikacja: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. Adres URL odpowiedzi: Wybierz adres URL przekierowania

   c. Wybierz pozycję Uruchom przepływ użytkownika.

4. Przejdź przez przepływ rejestracji i Utwórz konto

5. Polecenie "Less" zostanie wywołane podczas przepływu, po utworzeniu atrybutu użytkownika. Jeśli przepływ jest niekompletny, sprawdź, czy użytkownik nie jest zapisany w katalogu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)