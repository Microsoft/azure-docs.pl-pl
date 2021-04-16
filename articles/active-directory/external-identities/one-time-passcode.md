---
title: Uwierzytelnianie za pomocą kodu dostępu tylko raz dla użytkowników-gości B2B — Azure AD
description: Jak używać kodu dostępu do wiadomości e-mail z jednym kodem dostępu do uwierzytelniania użytkowników-gości B2B bez konieczności konto Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4089559b341dd268928b1f150b6fc173869ead
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529923"
---
# <a name="email-one-time-passcode-authentication"></a>Uwierzytelnianie za pomocą jednego kodu dostępu za pomocą poczty e-mail

W tym artykule opisano sposób włączania uwierzytelniania za pomocą jednego kodu dostępu w wiadomości e-mail dla użytkowników-gości B2B. Funkcja kodu dostępu w wiadomości e-mail z jednym kodem dostępu uwierzytelnia użytkowników-gości B2B, gdy nie można ich uwierzytelnić za pomocą innych środków, takich jak usługa Azure AD, usługa konto Microsoft (MSA) lub federacja Google. W przypadku uwierzytelniania za pomocą jednego kodu dostępu nie trzeba tworzyć konto Microsoft. Gdy użytkownik-gość zrealizuj zaproszenie lub uzyskuje dostęp do udostępnionego zasobu, może zażądać tymczasowego kodu, który jest wysyłany na jego adres e-mail. Następnie wprowadza ten kod, aby kontynuować logowanie.

![Diagram przeglądu kodu dostępu w wiadomości e-mail](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **Od października 2021** r. funkcja jednego kodu dostępu do poczty e-mail zostanie włączona dla wszystkich istniejących dzierżaw i domyślnie włączona dla nowych dzierżaw. Jeśli nie chcesz zezwalać na automatyczne włączanie tej funkcji, możesz ją wyłączyć. Zobacz [Wyłączanie kodu dostępu do wiadomości e-mail z jednym kodem](#disable-email-one-time-passcode) dostępu poniżej.
> - Ustawienia kodu dostępu do wiadomości e-mail z kodem dostępu Azure Portal z ustawień współpracy **zewnętrznej** do ustawienia **Wszyscy dostawcy tożsamości.**

> [!NOTE]
> Użytkownicy korzystający z kodu dostępu tylko raz muszą zalogować się przy użyciu linku, który zawiera kontekst dzierżawy (na przykład , lub , w przypadku `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>` zweryfikowanego `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` domeny). Bezpośrednie linki do aplikacji i zasobów działają również tak długo, jak zawierają kontekst dzierżawy. Użytkownicy-goście nie mogą obecnie logować się przy użyciu punktów końcowych, które nie mają kontekstu dzierżawy. Na przykład użycie funkcji `https://myapps.microsoft.com` `https://portal.azure.com` spowoduje błąd.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Środowisko użytkownika dla użytkowników-gości z jednym kodem dostępu

Po włączeniu funkcji kodu dostępu dla wiadomości e-mail z jednym kodem dostępu nowo zaproszeni użytkownicy spełniający określone warunki będą korzystać z uwierzytelniania za pomocą kodu dostępu tylko raz. [](#when-does-a-guest-user-get-a-one-time-passcode) Użytkownicy-goście, którzy zrealizli zaproszenie przed włączeniu kodu dostępu w wiadomości e-mail, będą nadal korzystać z tej samej metody uwierzytelniania.

W przypadku uwierzytelniania za pomocą kodu dostępu raz użytkownik-gość może zrealizować zaproszenie, klikając link bezpośredni lub korzystając z wiadomości e-mail z zaproszeniem. W obu przypadkach komunikat w przeglądarce wskazuje, że kod zostanie wysłany na adres e-mail użytkownika-gościa. Użytkownik-gość wybiera pozycję **Wyślij kod:**

   ![Zrzut ekranu przedstawiający przycisk Wyślij kod](media/one-time-passcode/otp-send-code.png)

Kod dostępu jest wysyłany na adres e-mail użytkownika. Użytkownik pobiera kod dostępu z wiadomości e-mail i wprowadza go w oknie przeglądarki:

   ![Zrzut ekranu przedstawiający stronę wprowadzania kodu](media/one-time-passcode/otp-enter-code.png)

Użytkownik-gość jest teraz uwierzytelniony i może zobaczyć udostępniony zasób lub kontynuować logowanie.

> [!NOTE]
> Kody dostępu tylko raz są ważne przez 30 minut. Po upływie 30 minut ten jeden kod dostępu nie jest już prawidłowy, a użytkownik musi zażądać nowego. Sesje użytkowników wygasają po 24 godzinach. Po upływie tego czasu użytkownik-gość otrzyma nowy kod dostępu podczas uzyskiwania dostępu do zasobu. Wygaśnięcie sesji zapewnia dodatkowe zabezpieczenia, zwłaszcza gdy użytkownik-gość opuszcza firmę lub nie potrzebuje już dostępu.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Kiedy użytkownik-gość otrzyma jeden kod dostępu?

Gdy użytkownik-gość zrealizuj zaproszenie lub użyje linku do zasobu, który został im udostępniony, otrzyma kod dostępu, jeśli:

- Nie mają konta usługi Azure AD
- Nie mają one konto Microsoft
- Dzierżawa zapraszająca nie s skonfigurowania federacji Google dla @gmail.com użytkowników @googlemail.com i

W czasie zapraszania nie ma żadnego wskazania, że zapraszany użytkownik będzie używać uwierzytelniania za pomocą kodu dostępu jednego raz. Jednak po dodaniu użytkownika-gościa uwierzytelnianie za pomocą jednego kodu dostępu będzie metodą rezerwową, jeśli nie można użyć żadnych innych metod uwierzytelniania.

Aby sprawdzić, czy użytkownik-gość uwierzytelnia się przy użyciu kodów dostępu, wyświetl właściwość **Source** w szczegółach użytkownika. W Azure Portal przejdź **do** Azure Active Directory Użytkownicy, a następnie wybierz użytkownika,  >  aby otworzyć stronę szczegółów.

![Zrzut ekranu przedstawiający użytkownika jednorazowego kodu dostępu z wartością źródłową OTP](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Gdy użytkownik zrealizuje jedno hasło, a później uzyska konto MSA, konto usługi Azure AD lub inne konto federowane, będzie nadal uwierzytelniany przy użyciu kodu dostępu. Jeśli chcesz zaktualizować metodę uwierzytelniania użytkownika, możesz [zresetować jego stan realizacji](reset-redemption-status.md).

### <a name="example&quot;></a>Przykład

teri@gmail.comUżytkownik-gość jest zapraszany do firmy Fabrikam, która nie ma ustawionej federacji Google. Teri nie ma konto Microsoft. Otrzymają one kod dostępu do uwierzytelniania za pomocą jednego kodu dostępu.

## <a name=&quot;disable-email-one-time-passcode&quot;></a>Wyłączanie kodu dostępu do wiadomości e-mail z jednym kodem dostępu

Od października 2021 r. funkcja jednego kodu dostępu w wiadomości e-mail zostanie włączona dla wszystkich istniejących dzierżaw i domyślnie włączona dla nowych dzierżaw. W tym czasie firma Microsoft nie będzie już obsługiwać realizacji zaproszeń, tworząc niezamażażowane (&quot;wirusowe&quot; lub &quot;just-in-time") konta usługi Azure AD i dzierżawy na potrzeby scenariuszy współpracy B2B. Umożliwiamy funkcję kodu dostępu w wiadomości e-mail z jednym kodem dostępu, ponieważ zapewnia ona bezproblemową metodę uwierzytelniania rezerwowego dla użytkowników-gości. Możesz jednak wyłączyć tę funkcję, jeśli nie chcesz z niego korzystać.

> [!NOTE]
>
> Jeśli w dzierżawie została włączona funkcja kodu dostępu w wiadomości e-mail z kodem dostępu, która została wyłączona, zalogowanie się nie będzie możliwe dla wszystkich użytkowników-gości, którzy zrealizli kod dostępu. Możesz [zresetować ich stan realizacji,](reset-redemption-status.md) aby można było ponownie zalogować się przy użyciu innej metody uwierzytelniania.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Aby wyłączyć funkcję kodu dostępu do wiadomości e-mail z jednym kodem dostępu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator globalny usługi Azure AD.

2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.

3. Wybierz **External Identities**  >  **Wszyscy dostawcy tożsamości.**

4. Wybierz **pozycję Wyślij wiadomość e-mail z kodem dostępu** do konta , a następnie wybierz pozycję Wyłącz kod dostępu do wiadomości **e-mail** dla gości.

   > [!NOTE]
   > Ustawienia kodu dostępu do wiadomości e-mail z kodem dostępu Azure Portal z ustawień współpracy **zewnętrznej** do ustawienia **Wszyscy dostawcy tożsamości.**
   > Jeśli zamiast opcji kodu dostępu w wiadomości e-mail zostanie wyświetlony przełącznik, oznacza to, że wcześniej włączono, wyłączono lub włączono funkcję w wersji zapoznawczej. Wybierz **pozycję Nie,** aby wyłączyć tę funkcję.
   >
   >![Wyłączono przełącznik jednokierunkowego kodu dostępu w wiadomości e-mail](media/one-time-passcode/enable-email-otp-disabled.png)

5. Wybierz pozycję **Zapisz**.

## <a name="note-for-public-preview-customers"></a>Uwaga dla klientów korzystających z publicznej wersji zapoznawczej

Jeśli wcześniej zrezygnowano z otrzymywania wiadomości e-mail z jednym kodem dostępu w publicznej wersji zapoznawczej, data z października 2021 r. na potrzeby automatycznego włączania funkcji nie będzie mieć zastosowania, więc nie będzie to mieć wpływu na powiązane procesy biznesowe. Ponadto w oknie Azure Portal we właściwościach Wyślij wiadomość e-mail na adres e-mail z kodem dostępu tylko raz dla gości nie będzie dostępna opcja Automatycznie włącz kod dostępu do wiadomości **e-mail** dla gości od października **2021 r.**. Zamiast tego zobaczysz następujący przełącznik **Tak** **lub** Nie:

![Wyślij wiadomość e-mail z kodem dostępu do wiadomości e-mail z rezygnacją](media/one-time-passcode/enable-email-otp-opted-in.png)

Jeśli jednak wolisz zrezygnować z tej funkcji i zezwolić na jej automatyczne wyłączenie w październiku 2021 r., możesz przywrócić ustawienia domyślne przy użyciu typu zasobu metoda konfiguracji uwierzytelniania poczty e-mail interfejsu [API](/graph/api/resources/emailauthenticationmethodconfiguration)usługi Microsoft Graph . Po przywróceniu ustawień domyślnych następujące opcje będą dostępne w obszarze **Wiadomość e-mail** z kodem dostępu dla gości:

![Włączanie opcji Wyślij wiadomość e-mail z jednym kodem dostępu z rezygnacją](media/one-time-passcode/email-otp-options.png)

- **Od października 2021** r. automatycznie włączaj dla gości kod dostępu do wiadomości e-mail. (Ustawienie domyślne) Jeśli funkcja kodu dostępu do wiadomości e-mail z jednym kodem dostępu nie została jeszcze włączona dla dzierżawy, zostanie ona automatycznie włączona od października 2021 r. Jeśli chcesz, aby funkcja została włączona w tym czasie, nie są wymagane żadne dalsze działania. Jeśli funkcja została już włączona lub wyłączona, ta opcja będzie niedostępna.

- **Włącz kod dostępu do wiadomości e-mail z jednym kodem dostępu dla gości już teraz.** Włącza funkcję jednego kodu dostępu w wiadomości e-mail dla dzierżawy.

- **Wyłącz kod dostępu do wiadomości e-mail dla gości.** Wyłącza funkcję kodu dostępu do wiadomości e-mail z jednym kodem dostępu dla dzierżawy i uniemożliwia włączenie tej funkcji w październiku 2021 r.

## <a name="note-for-azure-us-government-customers"></a>Uwaga dla klientów platformy Azure dla instytucji rządowych USA

Funkcja kodu dostępu do poczty e-mail z jednym kodem dostępu jest domyślnie wyłączona w chmurze platformy Azure dla instytucji rządowych USA. Partnerzy nie będą mogli się zalogować, chyba że ta funkcja jest włączona. W przeciwieństwie do chmury publicznej Platformy Azure chmura azure dla instytucji rządowych USA nie obsługuje realizacji zaproszeń przy użyciu samoobsługowych kont Azure Active Directory rządowych.

 ![Wyłączono kod dostępu do wiadomości e-mail z jednym kodem dostępu](media/one-time-passcode/enable-email-otp-disabled.png)

Aby włączyć funkcję kodu dostępu w wiadomości e-mail z jednym kodem dostępu w chmurze platformy Azure dla instytucji rządowych USA:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny usługi Azure AD.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. Wybierz **pozycję Relacje organizacyjne** Wszyscy dostawcy   >  **tożsamości.**

   > [!NOTE]
   > - Jeśli nie widzisz relacji **organizacyjnych,** wyszukaj "External Identities" na pasku wyszukiwania u góry.

4. Wybierz **pozycję Wyślij wiadomość e-mail z kodem dostępu do konta**, a następnie wybierz pozycję **Tak.**
5. Wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat bieżących ograniczeń, zobacz [Chmury platformy Azure dla instytucji rządowych USA.](current-limitations.md#azure-us-government-clouds)
