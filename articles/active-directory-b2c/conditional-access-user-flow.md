---
title: Dodawanie dostępu warunkowego do przepływu użytkownika w Azure AD B2C
description: Dowiedz się, jak dodać dostęp warunkowy do przepływów użytkownika Azure AD B2C. Skonfiguruj ustawienia uwierzytelniania wieloskładnikowego (MFA) i zasady dostępu warunkowego w przepływie użytkownika, aby wymusić zasady i skorygować ryzykowne logowania.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6325a890ea297a3aa2bdad76a1d95c10448a7b61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033918"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Dodawanie dostępu warunkowego do przepływów użytkowników w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Dostęp warunkowy można dodać do przepływów użytkownika Azure Active Directory B2C (Azure AD B2C) lub zasad niestandardowych w celu zarządzania ryzykownymi logowaniami do aplikacji. Azure Active Directory (Azure AD) dostęp warunkowy jest narzędziem używanym przez Azure AD B2C do przenoszenia sygnałów, podejmowania decyzji i wymuszania zasad organizacji.

![Przepływ dostępu warunkowego](media/conditional-access-user-flow/conditional-access-flow.png)

Automatyzacja oceny ryzyka z warunkami zasad oznacza, że ryzykowne logowania są identyfikowane natychmiast, a następnie korygowane lub blokowane.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Omówienie usługi

Azure AD B2C oblicza każde zdarzenie logowania i gwarantuje, że wszystkie wymagania dotyczące zasad są spełnione przed udzieleniem dostępu użytkownikowi. W trakcie tej fazy **oceny** usługa dostępu warunkowego ocenia sygnały zebrane przez wykrywanie ryzyka ochrony tożsamości podczas zdarzeń logowania. Wynikiem tego procesu oceny jest zestaw oświadczeń, który wskazuje, czy logowanie powinno być udzielone lub zablokowane. Zasady Azure AD B2C używają tych oświadczeń do podejmowania akcji w ramach przepływu użytkownika, takich jak blokowanie dostępu lub zajmowanie użytkownika z konkretnym korygowaniem, takim jak uwierzytelnianie wieloskładnikowe (MFA). "Blokuj dostęp" przesłania wszystkie inne ustawienia.

::: zone pivot="b2c-custom-policy"
W poniższym przykładzie przedstawiono profil techniczny dostępu warunkowego, który jest używany do oszacowania zagrożenia związanego z logowaniem.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

W poniższej fazie **korygowania** użytkownik jest wzywany do uwierzytelniania wieloskładnikowego. Po zakończeniu Azure AD B2C informuje ochronę tożsamości, że zidentyfikowane zagrożenie logowania zostało skorygowane i według jakiej metody. W tym przykładzie Azure AD B2C informuje, że użytkownik pomyślnie ukończył wyzwanie usługi wieloskładnikowe uwierzytelnianie. 

::: zone pivot="b2c-custom-policy"

Poniższy przykład przedstawia profil techniczny dostępu warunkowego służący do korygowania zidentyfikowanego zagrożenia:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>Składniki rozwiązania

Są to składniki, które umożliwiają dostęp warunkowy w Azure AD B2C:

- **Przepływ użytkownika** lub **zasady niestandardowe** , które przeprowadzi użytkownika przez proces logowania i tworzenia konta.
- **Zasady dostępu warunkowego** , które łączą sygnały, aby podejmować decyzje i wymuszać zasady organizacyjne. Gdy użytkownik loguje się do aplikacji za pomocą zasad Azure AD B2C, zasady dostępu warunkowego używają sygnałów Azure AD Identity Protection do identyfikowania ryzykownych logowań i przedstawiają odpowiednie akcje naprawcze.
- **Zarejestrowana aplikacja** , która kieruje użytkowników do odpowiedniego przepływu użytkownika Azure AD B2C lub zasad niestandardowych.
- [Przeglądarka sieci Tor](https://www.torproject.org/download/) , która symuluje ryzykowne logowanie.

## <a name="service-limitations-and-considerations"></a>Ograniczenia dotyczące usługi i zagadnienia

W przypadku korzystania z dostępu warunkowego usługi Azure AD należy wziąć pod uwagę następujące kwestie:

- Usługi Identity Protection są dostępne zarówno dla tożsamości lokalnych, jak i społecznościowych, takich jak Google lub Facebook. W przypadku tożsamości społecznościowych należy ręcznie aktywować dostęp warunkowy. Wykrywanie jest ograniczone, ponieważ poświadczenia konta społecznościowego są zarządzane przez zewnętrznego dostawcę tożsamości.
- W dzierżawach Azure AD B2C są dostępne tylko podzbiór zasad [dostępu warunkowego usługi Azure AD](../active-directory/conditional-access/overview.md) .


## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Warstwa cenowa

Do utworzenia ryzykownych zasad logowania jest wymagany Azure AD B2C **Premium P2** . Dzierżawy w **warstwie Premium P1** mogą tworzyć zasady oparte na zasadach lokalizacji, aplikacji, użytkownika lub grupy. Aby uzyskać więcej informacji, zobacz [Zmienianie warstwy cenowej usługi Azure AD B2C](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Przygotowywanie dzierżawy Azure AD B2C

Aby dodać zasady dostępu warunkowego, Wyłącz domyślne ustawienia zabezpieczeń:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
3. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Lub użyj pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.
4. Wybierz pozycję **Właściwości**, a następnie wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.

   ![Wyłącz ustawienia domyślne zabezpieczeń](media/conditional-access-user-flow/disable-security-defaults.png)

5. W obszarze **Włącz domyślne ustawienia zabezpieczeń** wybierz pozycję **nie**.

   ![Ustawienie opcji Włącz ustawienia domyślne zabezpieczeń włącza wartość nie](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Dodawanie zasad dostępu warunkowego

Zasady dostępu warunkowego to instrukcja if-then przypisań i kontroli dostępu. Zasady dostępu warunkowego zapewniają sygnały ze sobą, aby podejmować decyzje i wymuszać zasady organizacyjne. Operator logiczny między przypisaniami jest *i*. Operator w każdym przypisaniu ma wartość *lub*.

![Przypisania dostępu warunkowego](media/conditional-access-user-flow/conditional-access-assignments.png)

Aby dodać zasady dostępu warunkowego:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zabezpieczenia** wybierz pozycję **dostęp warunkowy (wersja zapoznawcza)**. Zostanie otwarta strona **zasady dostępu warunkowego** .
1. Wybierz pozycję **+ nowe zasady**.
1. Wprowadź nazwę zasad, na przykład *Zablokuj ryzykowne logowanie*.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz jedną z następujących obsługiwanych konfiguracji:

    |Uwzględnij  |Licencja | Uwagi  |
    |---------|---------|---------|
    |**Wszyscy użytkownicy** | P1, P2 |Jeśli zdecydujesz się na uwzględnienie **wszystkich użytkowników**, te zasady będą miały wpływ na wszystkich użytkowników. Aby upewnić się, że nie zablokujesz samodzielnie, Wyłącz konto administracyjne, wybierając opcję **Wyklucz**, wybierając pozycję **role katalogu**, a następnie wybierając pozycję **administrator globalny** na liście. Możesz również wybrać **użytkowników i grupy** , a następnie wybrać konto na liście **Wybierz wykluczonych użytkowników** .  | 
 
1. Wybierz pozycję **aplikacje w chmurze lub akcje**, a następnie **Wybierz pozycję aplikacje**. Wyszukaj [aplikację jednostki uzależnionej](tutorial-register-applications.md).

1. Wybierz pozycję **warunki**, a następnie wybierz jedną z następujących warunków. Na przykład wybierz pozycję **ryzyko związane z logowaniem** i **wysoki**, **Średni** i **niski** poziom ryzyka.
    
    |Warunek  |Licencja  |Uwagi  |
    |---------|---------|---------|
    |**Ryzyko związane z użytkownikiem**|P2|Ryzyko użytkownika reprezentuje prawdopodobieństwo naruszenia bezpieczeństwa tożsamości lub konta.|
    |**Ryzyko związane z logowaniem**|P2|Ryzyko związane z logowaniem reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie jest autoryzowane przez właściciela tożsamości.|
    |**Platformy urządzeń**|Nieobsługiwane| Scharakteryzowany przez system operacyjny uruchomiony na urządzeniu. Aby uzyskać więcej informacji, zobacz [platformę urządzeń](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms).|
    |**Lokalizacje**|P1, P2|Nazwane lokalizacje mogą zawierać publiczne informacje o sieci IPv4, kraj lub region lub nieznane obszary, które nie są mapowane na określone kraje lub regiony. Aby uzyskać więcej informacji, zobacz [lokalizacje](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations). |
 
1. W obszarze **Kontrole dostępu** wybierz pozycję **Udziel**. Następnie zdecyduj, czy dostęp ma być blokowany, czy udzielony:
    
    |Opcja  |Licencja |Uwaga  |
    |---------|---------|---------|
    |**Blokuj dostęp**|P1, P2| Uniemożliwia dostęp na podstawie warunków określonych w tych zasadach dostępu warunkowego.|
    |**Udzielanie dostępu** z **wymaganiem uwierzytelniania wieloskładnikowego**|P1, P2|Na podstawie warunków określonych w tych zasadach dostępu warunkowego użytkownik musi przejść przez Azure AD B2C uwierzytelnianie wieloskładnikowe.|

1. W obszarze **Włączanie zasad** wybierz jedną z następujących pozycji:
    
    |Opcja  |Licencja |Uwaga  |
    |---------|---------|---------|
    |**Tylko raporty**|P1, P2| Raport — umożliwia administratorom ocenę wpływu zasad dostępu warunkowego przed włączeniem ich w środowisku. Zalecamy sprawdzanie zasad w tym stanie i określanie wpływu na użytkowników końcowych bez konieczności uwierzytelniania wieloskładnikowego ani blokowania użytkowników. Aby uzyskać więcej informacji, zobacz sekcję [przegląd wyników dostępu warunkowego w raporcie inspekcji](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Włączone**| P1, P2| Zasady dostępu są oceniane i nie są wymuszane. |
    | **Wyłączone** | P1, P2| Zasady dostępu nie są aktywowane i nie mają wpływu na użytkowników. |

1. Włącz testowe zasady dostępu warunkowego, wybierając pozycję **Utwórz**.

## <a name="add-conditional-access-to-a-user-flow"></a>Dodawanie dostępu warunkowego do przepływu użytkownika

Po dodaniu zasad dostępu warunkowego usługi Azure AD Włącz dostęp warunkowy w przepływie użytkownika lub w zasadach niestandardowych. Po włączeniu dostępu warunkowego nie trzeba określać nazwy zasad.

Zasady dostępu warunkowego mogą być stosowane do poszczególnych użytkowników w dowolnym momencie. W takim przypadku pierwszeństwo ma najbardziej rygorystyczne zasady kontroli dostępu. Na przykład jeśli jedna zasada wymaga uwierzytelniania wieloskładnikowego (MFA), podczas gdy inne bloki dostępu, użytkownik zostanie zablokowany.

## <a name="enable-multi-factor-authentication-optional"></a>Włącz uwierzytelnianie wieloskładnikowe (opcjonalnie)

Podczas dodawania dostępu warunkowego do przepływu użytkownika należy rozważyć użycie **uwierzytelniania wieloskładnikowego (MFA)**. Użytkownicy mogą używać jednorazowego kodu za pośrednictwem wiadomości SMS lub głos lub hasła jednorazowego za pośrednictwem poczty e-mail na potrzeby uwierzytelniania wieloskładnikowego. Ustawienia usługi MFA są niezależne od ustawień dostępu warunkowego. Można ustawić usługę MFA na **zawsze włączone** , aby uwierzytelnianie wieloskładnikowe było zawsze wymagane niezależnie od konfiguracji dostępu warunkowego. Można też ustawić uwierzytelnianie wieloskładnikowe na **warunkowe** , aby usługa MFA była wymagana tylko wtedy, gdy wymaga tego zasady.

> [!IMPORTANT]
> Jeśli zasady dostępu warunkowego przyznają dostęp za pomocą usługi MFA, ale użytkownik nie zarejestrował numeru telefonu, użytkownik może zostać zablokowany.

::: zone pivot="b2c-user-flow"

Aby włączyć dostęp warunkowy dla przepływu użytkownika, upewnij się, że wersja obsługuje dostęp warunkowy. Te wersje przepływu użytkownika mają etykietę **zalecane**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

1. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Lub użyj pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.

1. W obszarze **zasady** wybierz pozycję **przepływy użytkownika**. Następnie wybierz przepływ użytkownika.

1. Wybierz pozycję **Właściwości** i upewnij się, że przepływ użytkownika obsługuje dostęp warunkowy, szukając ustawienia etykieta **dostęp warunkowy**.
 
   ![Konfigurowanie uwierzytelniania wieloskładnikowego i dostępu warunkowego we właściwościach](media/conditional-access-user-flow/add-conditional-access.png)

1. W sekcji **uwierzytelnianie wieloskładnikowe** wybierz żądaną **metodę MFA**, a następnie w obszarze **wymuszania MFA** wybierz pozycję **warunkowe (zalecane)**.
 
1. W sekcji **dostęp warunkowy** zaznacz pole wyboru **Wymuszaj zasady dostępu warunkowego** .

1. Wybierz pozycję **Zapisz**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Dodawanie dostępu warunkowego do zasad

1. Zapoznaj się z przykładem zasad dostępu warunkowego w witrynie [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
1. W każdym pliku Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C. Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się dostępne `contosob2c.onmicrosoft.com` .
1. Przekaż pliki zasad.

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Wybierz `B2C_1A_signup_signin_with_ca` zasady lub, `B2C_1A_signup_signin_with_ca_whatif` Aby otworzyć jej stronę przeglądu. Następnie wybierz pozycję **Uruchom przepływ użytkownika**. W obszarze **aplikacja** wybierz pozycję *webapp1*. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Skopiuj adres URL w obszarze **Uruchom punkt końcowy przepływu użytkownika**.

1. Aby zasymulować ryzykowne logowanie, Otwórz [przeglądarkę tor](https://www.torproject.org/download/) i użyj adresu URL skopiowanego w poprzednim kroku, aby zalogować się do zarejestrowanej aplikacji.

1. Wprowadź żądane informacje na stronie logowania, a następnie spróbuj się zalogować. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony. W tokenie dekodowane jwt.ms należy sprawdzić, czy logowanie zostało zablokowane.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**. W obszarze **aplikacja** wybierz pozycję *webapp1*. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .

1. Skopiuj adres URL w obszarze **Uruchom punkt końcowy przepływu użytkownika**.

1. Aby zasymulować ryzykowne logowanie, Otwórz [przeglądarkę tor](https://www.torproject.org/download/) i użyj adresu URL skopiowanego w poprzednim kroku, aby zalogować się do zarejestrowanej aplikacji.

1. Wprowadź żądane informacje na stronie logowania, a następnie spróbuj się zalogować. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony. W tokenie dekodowane jwt.ms należy sprawdzić, czy logowanie zostało zablokowane.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Przejrzyj wyniki dostępu warunkowego w raporcie inspekcji

Aby sprawdzić wynik zdarzenia dostępu warunkowego:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

3. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Lub użyj pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.

4. W obszarze **działania** wybierz pozycję **dzienniki inspekcji**.

5. Przefiltruj dziennik inspekcji, ustawiając **kategorię** na **B2C** i ustawiając **Typ zasobu działania** na **IdentityProtection**. Następnie wybierz przycisk **Zastosuj**.

6. Przeglądanie działania inspekcji przez maksymalnie siedem dni. Uwzględniane są następujące typy działań:

   - **Oceń zasady dostępu warunkowego**: Ten wpis dziennika inspekcji wskazuje, że podczas uwierzytelniania wykonano ocenę dostępu warunkowego.
   - **Koryguj użytkownika**: Ten wpis wskazuje, że udzielenie lub wymagania zasad dostępu warunkowego zostały spełnione przez użytkownika końcowego, a to działanie zostało zgłoszone do aparatu ryzyka w celu ograniczenia (zmniejszenie ryzyka).

7. Wybierz pozycję **Oceń dziennik zasad dostępu warunkowego** na liście, aby otworzyć stronę **szczegóły działania: dziennik inspekcji** , która zawiera identyfikatory dziennika inspekcji wraz z tymi informacjami w sekcji **dodatkowe szczegóły** :

   - **ConditionalAccessResult**: przyznanie wymagane przez ocenę zasad warunkowych.
   - **AppliedPolicies**: Lista wszystkich zasad dostępu warunkowego, w których warunki zostały spełnione i zasady są włączone.
   - **ReportingPolicies**: lista zasad dostępu warunkowego, które zostały ustawione na tryb "tylko raportowanie" i, w których warunki zostały spełnione.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie interfejsu użytkownika w przepływie użytkownika Azure AD B2C](customize-ui-with-html.md)
