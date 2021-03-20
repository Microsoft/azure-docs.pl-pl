---
title: itsme OpenID Connect Połącz z Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C z usługą itsme OIDC przy użyciu client_secret zasad przepływu użytkownika. itsme jest aplikacją identyfikatora cyfrowego. Pozwala on bezpiecznie zalogować się bez czytników kart, haseł, uwierzytelniania dwuskładnikowego i wielu kodów PIN.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba7875caa6a1db7638bfeafcfea1efa7b2462152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87489519"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Konfigurowanie itsme OpenID Connect Connect (OIDC) z Azure Active Directory B2C

Aplikacja Digital ID itsme umożliwia bezpieczne logowanie bez czytników kart, haseł, uwierzytelniania dwuskładnikowego lub wielu kodów PIN. Aplikacja itsme zapewnia silne uwierzytelnianie klienta przy użyciu zweryfikowanej tożsamości. W tym artykule dowiesz się, jak zintegrować Azure AD B2C Authentication z usługą itsme OpenID Connect Connect (OIDC) przy użyciu zasad przepływu użytkownika klucza tajnego klienta.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.
* Identyfikator klienta, znany również jako kod partnera, zapewniany przez itsme.
* Kod usługi dostarczany przez itsme.
* Wpis tajny klienta dla konta usługi itsme.

## <a name="scenario-description"></a>Opis scenariusza

![diagram architektury itsme](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Krok | Opis |
|------|------|
|1     | W witrynie sieci Web lub aplikacji Dołącz przycisk **Zaloguj się przy użyciu itsme** , dostosowując go do przepływu użytkownika Azure AD B2C. Przepływ interakcji jest uruchamiany po kliknięciu tego przycisku przez użytkownika.  |
|2     | Azure AD B2C uruchamia przepływ połączenia OpenID Connect, wysyłając żądanie autoryzacji do interfejsu API tajnego klienta itsme. Dostępny jest dobrze znany/OpenID connecty punkt końcowy konfiguracji zawierający informacje o punktach końcowych.  |
|3     | Środowisko itsme przekierowuje użytkownika do strony itsme Zidentyfikuj siebie, umożliwiając użytkownikowi wypełnienie numeru telefonu.  |
|4     | Środowisko itsme odbiera numer telefonu od użytkownika i sprawdza poprawność.  |
|5     | Jeśli numer telefonu należy do aktywnego użytkownika itsme, zostanie utworzona akcja dla aplikacji itsme.  |
|6     | Użytkownik otwiera aplikację itsme, sprawdza żądanie i potwierdza akcję.  |
|7     |  Aplikacja informuje środowisko itsme o tym, że akcja została potwierdzona. |
|8     |  Środowisko itsme zwraca kod autoryzacji OAuth do Azure AD B2C. |
|9     |  Przy użyciu kodu autoryzacji Azure AD B2C wykonuje żądanie tokenu. |
| 10 | Środowisko itsme sprawdza żądanie tokenu, a jeśli jest nadal ważne, zwraca token dostępu OAuth i token ID zawierający żądane informacje o użytkowniku. |
| 11 | Na koniec użytkownik zostanie przekierowany do adresu URL przekierowania jako uwierzytelniony użytkownik.  |
|   |   |

## <a name="onboard-with-itsme"></a>Dołączanie do itsme

1. Aby utworzyć konto w usłudze itsme, odwiedź witrynę itsme w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

2. Aktywuj swoje konto itsme, wysyłając wiadomość e-mail na adres onboarding@itsme.be . Otrzymasz **Kod partnera** i **kod usługi** , który będzie potrzebny dla Instalatora B2C.

3. Po aktywacji konta partnera itsme otrzymasz wiadomość e-mail z linkiem jednorazowym do **wpisu tajnego klienta**.

4. Postępuj zgodnie z instrukcjami w [itsme](https://business.itsme.be/en) , aby zakończyć konfigurację.

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Skonfiguruj nowego dostawcę tożsamości w Azure AD B2C

> [!NOTE]
> Jeśli jeszcze tego nie zrobiono, [Utwórz dzierżawę Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

1. Upewnij się, że używasz katalogu, który zawiera Azure AD B2C dzierżawcy. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

2. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C** (lub wybierz pozycję **więcej usług** i użyj pola wyszukiwania **wszystkie usługi** , aby wyszukać *Azure AD B2C*).

3. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Nowy dostawca połączenia OpenID Connect**.

4. Wypełnij formularz, podając następujące informacje:

   |Właściwość | Wartość |
   |------------ |------- |
   | Nazwa | itsme |
   | Adres URL metadanych | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>gdzie `<environment>` jest `e2e` (środowisko testowe) lub `prd` (produkcja)  |
   | ClientID     | **Identyfikator klienta**, znany również jako **Kod partnera**  |
   | Klucz tajny klienta | Twoje **client_secret** |
   | Zakres  | Usługa OpenID Connect: YOURSERVICECODE profilu e-mail [telefon] [adres]  |
   |Typ odpowiedzi | kod |
   |Tryb odpowiedzi | query |
   |Wskazówki dotyczące domeny | *Możesz pozostawić to pole puste* |
   |UserID | Sub |
   |Nazwa wyświetlana | name |
   |Imię | given_name |
   |Nazwisko | family_name |
   |E-mail | poczta e-mail|

5. Wybierz pozycję **Zapisz**.

### <a name="configure-a-user-flow"></a>Konfigurowanie przepływu użytkownika

1. W dzierżawie Azure AD B2C w obszarze **zasady** wybierz pozycję **przepływy użytkownika**.

2. Wybierz pozycję **Nowy przepływ użytkownika**.

3. Wybierz pozycję **zarejestruj się i zaloguj**, wybierz wersję, a następnie wybierz pozycję **Utwórz**.

4. Wprowadź **nazwę**.

5. W sekcji **dostawcy tożsamości** wybierz pozycję **itsme**.

6. Wybierz przycisk **Utwórz**.

7. Otwórz nowo utworzony przepływ użytkownika, wybierając nazwę przepływu użytkownika.

8. Wybierz pozycję **Właściwości** i Dostosuj następujące wartości:

   * Zmień **identyfikatory okresów istnienia tokenu & dostępu (minuty)** do **5**.
   * Zmień **okres istnienia okna przewijania tokenu odświeżania** na **Brak ważności**.

### <a name="register-an-application"></a>Rejestrowanie aplikacji

1. W dzierżawie B2C w obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.

2. Podaj **nazwę** aplikacji, a następnie wprowadź **Identyfikator URI przekierowania**. W celach testowych wprowadź `https://jwt.ms` .

3. Upewnij się, że uwierzytelnianie wieloskładnikowe jest **wyłączone**.

4. Wybierz pozycję **Zarejestruj**.

   a. W celach testowych wybierz pozycję **uwierzytelnianie** i w obszarze **niejawne przyznanie** zaznacz pola wyboru **tokeny dostępu** i **tokeny identyfikatorów** .  

   b. Wybierz pozycję **Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. W dzierżawie B2C w obszarze **zasady** wybierz pozycję **przepływy użytkownika**.

2. Wybierz wcześniej utworzony przepływ użytkownika.

3. Wybierz pozycję **Uruchom przepływ użytkownika**.

   a. **Aplikacja**: *Wybieranie zarejestrowanej aplikacji*

   b. **Adres URL odpowiedzi**: *Wybierz adres URL przekierowania*

4. Zostanie wyświetlona strona itsme **Identyfikuj siebie** .  

5. Wprowadź numer telefonu komórkowego i wybierz pozycję **Wyślij**.

6. Potwierdź akcję w aplikacji itsme.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

* [Zasady niestandardowe w usłudze Azure AD B2C](custom-policy-overview.md)

* [Wprowadzenie do zasad niestandardowych w Azure AD B2C](custom-policy-get-started.md?tabs=applications)