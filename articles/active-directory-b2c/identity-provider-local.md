---
title: Konfigurowanie dostawcy tożsamości konta lokalnego Azure AD B2C
titleSuffix: Azure AD B2C
description: Zdefiniuj typy tożsamości używane przez program do rejestracji lub logowania (adres e-mail, nazwa użytkownika, numer telefonu) w dzierżawie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2baff33d9e91e1b5259d79eca0a22535c00f419
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555237"
---
# <a name="set-up-the-local-account-identity-provider"></a>Konfigurowanie dostawcy tożsamości konta lokalnego

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C zapewnia różne sposoby uwierzytelniania użytkownika przez użytkowników. Użytkownicy mogą logować się do konta lokalnego przy użyciu nazwy użytkownika i hasła, weryfikacji telefonu (znanego również jako hasło bez uwierzytelniania) lub dostawców tożsamości społecznościowych. Rejestracja w wiadomościach e-mail jest domyślnie włączona w ustawieniach dostawcy tożsamości konta lokalnego. 

W tym artykule opisano sposób tworzenia przez użytkowników kont lokalnych dla tej Azure AD B2C dzierżawcy. W przypadku tożsamości społecznościowych i firmowych, w których tożsamość użytkownika jest zarządzana przez federacyjnego dostawcę tożsamości, takiego jak Facebook i Google, zobacz [Dodawanie dostawcy tożsamości](add-identity-provider.md).

## <a name="email-sign-in"></a>Logowanie za pośrednictwem poczty e-mail

Po wybraniu opcji poczty e-mail użytkownicy mogą logować się lub w górę przy użyciu adresu e-mail i hasła:

- **Zaloguj** się, użytkownicy są monitowani o podanie adresu e-mail i hasła.
- Po **zarejestrowaniu** użytkownicy otrzymają monit o podanie adresu e-mail, który zostanie zweryfikowany przy rejestrowaniu (opcjonalnie) i stanie się identyfikatorem logowania. Następnie użytkownik wprowadza wszelkie inne informacje wymagane na stronie rejestracji, na przykład nazwa wyświetlana, imię i nazwisko. Następnie wybierz pozycję Kontynuuj, aby utworzyć konto.
- **Resetowanie hasła** użytkownicy muszą wprowadzić i zweryfikować swój adres e-mail, po czym użytkownik może zresetować hasło.

![Rejestrowanie lub logowanie za pośrednictwem poczty e-mail](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Logowanie użytkownika

Korzystając z opcji użytkownik, użytkownicy mogą logować się do konta przy użyciu nazwy użytkownika i hasła:

- **Logowanie**: użytkownicy są monitowani o podanie nazwy użytkownika i hasła.
- **Rejestracja**: użytkownicy otrzymają monit o podanie nazwy użytkownika, która stanie się identyfikatorem logowania. Użytkownikom zostanie również wyświetlony monit o podanie adresu e-mail, który zostanie zweryfikowany podczas rejestracji. Ten adres e-mail będzie używany podczas przepływu resetowania hasła. Użytkownik wprowadza wszelkie inne informacje wymagane na stronie rejestracji, na przykład nazwa wyświetlana, imię i nazwisko. Następnie użytkownik wybierze pozycję Kontynuuj, aby utworzyć konto.
- **Resetowanie hasła**: użytkownicy muszą wprowadzić swoją nazwę użytkownika i skojarzony adres e-mail. Adres e-mail musi zostać zweryfikowany, a następnie użytkownik może zresetować hasło.

![Logowanie lub logowanie przy użyciu nazwy użytkownika](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Logowanie przy użyciu telefonu (wersja zapoznawcza)

Uwierzytelnianie bez hasła jest typem uwierzytelniania, w którym użytkownik nie musi zalogować się przy użyciu hasła. Przy rejestrowaniu i logowaniu użytkownik może zarejestrować się w aplikacji przy użyciu numeru telefonu jako podstawowego identyfikatora logowania. Podczas rejestracji i logowania użytkownik będzie miał następujące doświadczenie:

- **Logowanie**: Jeśli użytkownik ma konto z numerem telefonu w postaci identyfikatora, użytkownik wprowadzi numer telefonu i wybierze opcję *Zaloguj*. Potwierdzają one kraj i numer telefonu, wybierając pozycję *Kontynuuj*, a jednorazowy kod weryfikacyjny jest wysyłany do telefonu. Użytkownik wprowadzi kod weryfikacyjny i wybierze opcję *Kontynuuj* , aby się zalogować.
- **Rejestracja**: Jeśli użytkownik nie ma jeszcze konta dla aplikacji, może go utworzyć, klikając link *zarejestruj się teraz* . 
    1. Zostanie wyświetlona strona rejestracji, w której użytkownik wybiera swój *kraj*, wprowadzi numer telefonu i wybierze pozycję *Wyślij kod*. 
    1. Jednorazowy kod weryfikacyjny jest wysyłany do numeru telefonu użytkownika. Użytkownik wprowadzi *kod weryfikacyjny* na stronie rejestracji, a następnie wybierze pozycję *Weryfikuj kod*. (Jeśli użytkownik nie może pobrać kodu, może wybrać pozycję *Wyślij nowy kod*). 
    1. Użytkownik wprowadza wszelkie inne informacje wymagane na stronie rejestracji, na przykład nazwa wyświetlana, imię i nazwisko. Następnie wybierz pozycję Kontynuuj.
    1. Następnie użytkownik zostanie poproszony o podanie **wiadomości e-mail** dotyczącej odzyskiwania. Użytkownik wprowadza swój adres e-mail, a następnie wybiera pozycję *Wyślij kod weryfikacyjny*. Kod jest wysyłany do skrzynki odbiorczej poczty e-mail użytkownika, którą można pobrać i wprowadzić w polu kod weryfikacyjny. Następnie użytkownik wybiera polecenie Weryfikuj kod.
    1. Po zweryfikowaniu kodu użytkownik wybiera pozycję *Utwórz* , aby utworzyć konto. 

![Rejestrowanie lub logowanie na telefonie](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Cennik

Hasła jednorazowe są wysyłane do użytkowników za pomocą wiadomości SMS. W zależności od operatora sieci komórkowej może zostać naliczona opłata za każdy wysłany komunikat. Aby uzyskać informacje o cenach, zapoznaj się z sekcją **oddzielne opłaty** w [cenniku Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> Uwierzytelnianie wieloskładnikowe (MFA) jest domyślnie wyłączone podczas konfigurowania przepływu użytkownika przy użyciu usługi rejestracji na telefonie. Uwierzytelnianie wieloskładnikowe można włączyć w przepływach użytkownika przy użyciu rejestracji w telefonie, ale ponieważ numer telefonu jest używany jako identyfikator podstawowy, jedyną opcją dostępną dla drugiego czynnika uwierzytelniania jest adres e-mail.

### <a name="phone-recovery"></a>Odzyskiwanie telefonu

Po włączeniu rejestracji i zalogowaniu się w telefonie dla użytkowników warto również włączyć funkcję poczty e-mail do odzyskiwania. Korzystając z tej funkcji, użytkownik może podać adres e-mail, którego można użyć do odzyskania konta, jeśli nie mają telefonu. Ten adres e-mail służy tylko do odzyskiwania konta. Nie można jej użyć do zalogowania się.

- Po wyświetleniu monitu **dotyczącego** wiadomości e-mail z odzyskiwaniem zostanie wyświetlony monit o zweryfikowanie wiadomości e-mail z kopią zapasową. Użytkownik, który nie dostarczył wiadomości e-mail z odzyskiwaniem, przed wyświetleniem monitu o zweryfikowanie wiadomości e-mail dotyczącej kopii zapasowej podczas kolejnego logowania.

- Po **wyłączeniu** wiadomości e-mail dotyczącej odzyskiwania nie jest wyświetlany monit dotyczący odzyskiwania wiadomości e-mail.
 
Na poniższych zrzutach ekranu przedstawiono przepływ odzyskiwania telefonu:

![Przepływ użytkownika odzyskiwania telefonu](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Logowanie za pośrednictwem telefonu lub poczty e-mail (wersja zapoznawcza)

Możesz połączyć [się z logowaniem do telefonu](#phone-sign-in-preview)i [logowaniem do poczty e-mail](#email-sign-in). Na stronie rejestracji lub logowania użytkownik może wpisać numer telefonu lub adres e-mail. Na podstawie danych wejściowych użytkownika Azure AD B2C przenosi użytkownika do odpowiedniego przepływu. 

![Logowanie za pośrednictwem telefonu lub poczty e-mail lub logowanie](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Konfigurowanie ustawień dostawcy tożsamości konta lokalnego

Można skonfigurować lokalnych dostawców tożsamości dostępnych do użycia w przepływie użytkownika przez włączenie lub wyłączenie dostawców (adres e-mail, nazwa użytkownika lub numer telefonu).  Na poziomie dzierżawy może być włączony więcej niż jeden lokalny dostawca tożsamości.

Przepływ użytkownika można skonfigurować tylko w taki sposób, aby jednocześnie używał jednego z dostawców tożsamości konta lokalnego. Każdy przepływ użytkownika może mieć ustawiony inny dostawca tożsamości konta lokalnego, jeśli na poziomie dzierżawy włączono więcej niż jeden.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. W obszarze **Zarządzaj** wybierz pozycję **dostawcy tożsamości**.
1. Na liście dostawca tożsamości wybierz pozycję **konto lokalne**.
1. Na stronie **Konfiguruj lokalną dostawcy tożsamości** wybierz co najmniej jeden z dozwolonych typów tożsamości, których użytkownicy mogą używać do tworzenia kont lokalnych w dzierżawie Azure AD B2C.
1. Wybierz pozycję **Zapisz**.

## <a name="configure-your-user-flow"></a>Konfigurowanie przepływu użytkownika

1. W menu po lewej stronie Azure Portal wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **przepływy użytkownika (zasady)**.
1. Wybierz przepływ użytkownika, dla którego chcesz skonfigurować proces rejestracji i logowania.
1. Wybierz **dostawców tożsamości**
1. W obszarze **konta lokalne** wybierz jedną z następujących pozycji: **Zapisywanie wiadomości E-mail**,  **Rejestrowanie identyfikatorów użytkowników**, **rejestracja telefonu**, rejestracja **telefonu/poczty e-mail** lub **Brak**.

### <a name="enable-the-recovery-email-prompt"></a>Włącz monit dotyczący odzyskiwania wiadomości e-mail

W przypadku wybrania opcji rejestracji **telefonu**, **numeru telefonu/poczty e-mail** Włącz monit dotyczący odzyskiwania wiadomości e-mail.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W Azure AD B2C w obszarze **zasady** wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika z listy.
1. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.
1. Obok pozycji **Włącz monit e-mail dotyczącej odzyskiwania numeru telefonu i logowania (wersja zapoznawcza)** wybierz pozycję:
   - **W** celu wyświetlenia monitu o wiadomość e-mail z odzyskiwaniem podczas rejestracji i logowania.
   - **Aby ukryć** monit dotyczący odzyskiwania wiadomości e-mail.
1. Wybierz pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Pobierz pakiet startowy

Zasady niestandardowe są zestawem plików XML przekazywanym do dzierżawy Azure AD B2C w celu zdefiniowania podróży użytkowników. Udostępniamy pakiety początkowe z kilkoma wstępnie utworzonymi zasadami. Pobierz odpowiedni pakiet Starter-Pack: 

- [Logowanie za pośrednictwem poczty e-mail](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Logowanie użytkownika](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Logowanie przy użyciu telefonu](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Wybierz [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) zasady jednostki uzależnionej. 
- [Logowanie za pośrednictwem telefonu lub poczty e-mail](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Wybierz [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) zasady jednostki uzależnionej.

Po pobraniu pakietu początkowego.

1. W każdym pliku Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C. Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się dostępne `contosob2c.onmicrosoft.com` .

1. Wykonaj kroki opisane w sekcji [Dodawanie identyfikatorów aplikacji do zasad niestandardowych](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) [w temacie Rozpoczynanie pracy z zasadami niestandardowymi w Azure Active Directory B2C](custom-policy-get-started.md). Na przykład zaktualizuj `/phone-number-passwordless/` **`Phone_Email_Base.xml`** przy użyciu **identyfikatorów aplikacji (klienta)** dwóch aplikacji zarejestrowanych podczas kończenia wymagań wstępnych, *IdentityExperienceFramework* i *ProxyIdentityExperienceFramework*.
1. Przekazywanie plików zasad

::: zone-end

## <a name="next-steps"></a>Następne kroki

- [Dodawanie zewnętrznych dostawców tożsamości](add-identity-provider.md)
- [Tworzenie przepływu użytkownika](tutorial-create-user-flows.md)
