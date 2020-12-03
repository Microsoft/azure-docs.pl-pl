---
title: Jednokrotne uwierzytelnianie kodu dostępu dla użytkowników-Gości B2B — Azure AD
description: Jak używać jednorazowego kodu dostępu wiadomości E-mail do uwierzytelniania użytkowników Gości B2B bez konieczności konto Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15debb69172dba00163950fdd301826c903e5307
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548323"
---
# <a name="email-one-time-passcode-authentication"></a>Wyślij wiadomość e-mail do jednorazowego uwierzytelniania kodu dostępu

W tym artykule opisano, jak włączyć jednokrotne uwierzytelnianie kodu dostępu w wiadomości e-mail dla użytkowników-Gości B2B. Funkcja jednorazowego kodu dostępu wiadomości e-mail uwierzytelnia użytkowników gościa B2B, gdy nie mogą być uwierzytelniane za pośrednictwem innych takich metod jak Azure AD, konto Microsoft (MSA) lub Google Federation. W przypadku uwierzytelniania za pomocą jednorazowego kodu dostępu nie ma potrzeby tworzenia konto Microsoft. Gdy użytkownik-Gość zrealizuje zaproszenie lub uzyskuje dostęp do zasobu udostępnionego, może zażądać kodu tymczasowego, który jest wysyłany na adres e-mail. Następnie wprowadzają ten kod, aby kontynuować logowanie.

![Tworzenie pocztą e-mail jednokrotnego diagramu przeglądu kodu dostępu](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> **Od marca 2021** funkcja jednorazowego kodu dostępu wiadomości e-mail zostanie włączona dla wszystkich istniejących dzierżawców i domyślnie włączona dla nowych dzierżawców. Jeśli nie chcesz zezwolić na automatyczne włączenie tej funkcji, możesz ją wyłączyć. Zobacz sekcję [wyłączanie jednorazowego kodu dostępu wiadomości e-mail](#disable-email-one-time-passcode) poniżej.

> [!NOTE]
> Jednorazowy kod dostępu użytkownicy muszą logować się przy użyciu linku zawierającego kontekst dzierżawy (na przykład `https://myapps.microsoft.com/?tenantid=<tenant id>` lub `https://portal.azure.com/<tenant id>` w przypadku zweryfikowanej domeny `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Bezpośrednie linki do aplikacji i zasobów działają również tak długo, jak w przypadku kontekstu dzierżawy. Użytkownicy-Goście nie mogą obecnie zalogować się za pomocą punktów końcowych, które nie mają kontekstu dzierżawy. Na przykład użycie programu `https://myapps.microsoft.com` `https://portal.azure.com` spowoduje wystąpienie błędu.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Środowisko użytkownika dla użytkowników-Gości jednorazowego kodu dostępu

Po włączeniu funkcji jednokrotnego kodu dostępu wiadomości e-mail nowo zaproszeni użytkownicy, [którzy spełniają określone warunki](#when-does-a-guest-user-get-a-one-time-passcode) , będą korzystać z uwierzytelniania jednorazowego kodu dostępu. Użytkownicy-Goście, którzy korzystali z zaproszenia przed przekazaniem jednorazowego kodu dostępu do wiadomości e-mail, będą nadal używać tej samej metody uwierzytelniania.

W przypadku uwierzytelniania za pomocą jednorazowego kodu dostępu użytkownik-Gość może skorzystać z zaproszenia przez kliknięcie linku bezpośredniego lub wysłanie wiadomości e-mail z zaproszeniem. W obu przypadkach komunikat w przeglądarce wskazuje, że kod zostanie wysłany na adres e-mail użytkownika-gościa. Użytkownik-Gość wybierze opcję **Wyślij kod**:

   ![Zrzut ekranu przedstawiający przycisk Wyślij kod](media/one-time-passcode/otp-send-code.png)

Kod dostępu jest wysyłany na adres e-mail użytkownika. Użytkownik pobiera kod dostępu z wiadomości e-mail i wprowadza go w oknie przeglądarki:

   ![Zrzut ekranu przedstawiający stronę wprowadzanie kodu](media/one-time-passcode/otp-enter-code.png)

Użytkownik-Gość jest teraz uwierzytelniany i może zobaczyć zasób udostępniony lub kontynuować logowanie.

> [!NOTE]
> Jednorazowe kody dostępu są ważne przez 30 minut. Po 30 minutach określony jednorazowy kod dostępu nie jest już ważny, a użytkownik musi zażądać nowego. Sesje użytkowników wygasają po upływie 24 godzin. Po upływie tego czasu użytkownik-gość otrzyma nowy kod dostępu podczas uzyskiwania dostępu do zasobu. Wygaśnięcie sesji zapewnia dodatkowe zabezpieczenia, zwłaszcza gdy użytkownik-Gość opuszcza swoją firmę lub nie potrzebuje już dostępu.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Kiedy użytkownik-Gość otrzymuje jednorazowy kod dostępu?

Gdy użytkownik-Gość zrealizuje zaproszenie lub używa linku do zasobu, który został Ci udostępniony, otrzyma jednorazowy kod dostępu, jeśli:

- Nie mają konta usługi Azure AD
- Nie mają konto Microsoft
- Zapraszana dzierżawa nie została skonfigurowana dla użytkowników usługi Google Federation for @gmail.com i @googlemail.com

W momencie zaproszenia nie ma wskazania, że użytkownik, którego zapraszasz, będzie używać uwierzytelniania jednorazowego kodu dostępu. Jednak po zalogowaniu się użytkownika-gościa uwierzytelnianie jednorazowego kodu dostępu będzie metodą rezerwową, jeśli nie można użyć innych metod uwierzytelniania.

Możesz sprawdzić, czy użytkownik-Gość uwierzytelnia się przy użyciu jednorazowych kodów dostępu, wyświetlając Właściwość **Source** w szczegółach użytkownika. W Azure Portal przejdź do pozycji **Azure Active Directory**  >  **Użytkownicy**, a następnie wybierz użytkownika, aby otworzyć stronę szczegóły.

![Zrzut ekranu przedstawiający użytkownika jednorazowego kodu dostępu z wartością źródłową OTP](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Gdy użytkownik zrealizuje jednorazowy kod dostępu, a następnie uzyska konto MSA, konta usługi Azure AD lub inne konto federacyjne, będzie nadal uwierzytelniane przy użyciu jednorazowego kodu dostępu. Jeśli chcesz zaktualizować metodę uwierzytelniania, możesz usunąć konto użytkownika-gościa i je zaprosić.

### <a name="example"></a>Przykład

Użytkownik teri@gmail.com -gość jest zapraszany do firmy Fabrikam, która nie ma skonfigurowanej usługi Google Federation. Teri nie ma konto Microsoft. Otrzymają one jednorazowy kod dostępu do uwierzytelniania.

## <a name="disable-email-one-time-passcode"></a>Wyłącz jednorazowy kod dostępu wiadomości e-mail

Od marca 2021 funkcja jednorazowego kodu dostępu wiadomości e-mail zostanie włączona dla wszystkich istniejących dzierżawców i domyślnie włączona dla nowych dzierżawców. W tym czasie firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych ("wirusowego" lub "just-in-Time") kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. Włączamy funkcję jednorazowego kodu dostępu wiadomości e-mail, ponieważ zapewnia ona bezproblemową metodę uwierzytelniania dla użytkowników-Gości. Istnieje jednak możliwość wyłączenia tej funkcji, jeśli nie zostanie ona użyta.

> [!NOTE]
>
> Jeśli funkcja jednorazowego kodu dostępu w wiadomości e-mail została włączona w dzierżawie i zostanie wyłączona, wszyscy użytkownicy-Goście, którzy wykonali jednorazowy kod dostępu, nie będą mogli się zalogować. Można usunąć użytkownika-gościa, a następnie ponownie je zaprosić, aby mogli się zalogować przy użyciu innej metody uwierzytelniania.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Aby wyłączyć funkcję jednorazowego kodu dostępu wiadomości e-mail

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako Administrator globalny usługi Azure AD.

2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.

3. Wybierz pozycję **tożsamości zewnętrzne**  >  **Ustawienia współpracy zewnętrznej**.

4. W obszarze **Wyślij pocztą e-mail jednorazowy kod dostępu dla Gości**, zaznacz opcję **Wyłącz jednorazowy kod dostępu wiadomości e-mail dla Gości**.

    ![Wyślij wiadomość e-mail do jednorazowego ustawienia kodu dostępu](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > Jeśli zobaczysz następujący przełącznik zamiast opcji przedstawionych powyżej, oznacza to, że wcześniej włączono, wyłączono lub wybrano w wersji zapoznawczej funkcji. Wybierz pozycję **nie** , aby wyłączyć funkcję.
   >
   >![Włącz jednorazowy kod dostępu wiadomości E-mail w programie](media/delegate-invitations/enable-email-otp-opted-in.png)

5. Wybierz pozycję **Zapisz**.

## <a name="note-for-public-preview-customers"></a>Uwaga dla klientów z publicznej wersji zapoznawczej

Jeśli wcześniej zaznaczono w publicznej wersji zapoznawczej jednorazowego kodu dostępu do wiadomości e-mail, Data 2021 dla automatycznego włączenia funkcji nie ma zastosowania do użytkownika, więc nie ma to wpływu na powiązane procesy biznesowe. Ponadto w Azure Portal w obszarze **wiadomości e-mail jednorazowy kod dostępu dla Gości** nie zobaczysz opcji automatycznego włączania jednokrotnego **dostępu do poczty e-mail dla gości w marcu 2021**. Zamiast tego zobaczysz następujący przełącznik **tak** lub **nie** :

![Włącz jednorazowy kod dostępu wiadomości E-mail w programie](media/delegate-invitations/enable-email-otp-opted-in.png)

Jeśli jednak wolisz zrezygnować z funkcji i zezwolić na jej automatyczne włączenie w marcu 2021, możesz przywrócić ustawienia domyślne za pomocą [typu zasobu konfiguracja Metoda uwierzytelniania poczty e-mail](https://aka.ms/exid-graphemailauth)Microsoft Graph API. Po przywróceniu ustawień domyślnych dostępne są następujące opcje **dla Gości poczty e-mail jednorazowo**:

- **Automatycznie Włącz jednorazowy kod dostępu wiadomości e-mail dla Gości w marcu 2021**. Wartooć Jeśli funkcja jednorazowego kodu dostępu wiadomości e-mail nie została już włączona dla Twojej dzierżawy, zostanie automatycznie włączona w marcu 2021. Jeśli funkcja ma być włączona w tym czasie, nie są wymagane żadne dalsze działania. Jeśli funkcja została już włączona lub wyłączona, ta opcja będzie niedostępna.

- **Włącz jednorazowy kod dostępu do wiadomości e-mail dla Gości**. Włącza funkcję jednorazowego kodu dostępu wiadomości e-mail dla dzierżawy.

- **Wyłącz jednorazowy kod dostępu wiadomości e-mail dla Gości**. Wyłącza funkcję jednorazowego kodu dostępu wiadomości e-mail dla dzierżawy i uniemożliwia włączenie tej funkcji w marcu 2021.
