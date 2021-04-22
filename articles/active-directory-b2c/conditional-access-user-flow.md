---
title: Dodawanie dostępu warunkowego do przepływu użytkownika w Azure AD B2C
description: Dowiedz się, jak dodać dostęp warunkowy do przepływów Azure AD B2C użytkownika. Skonfiguruj ustawienia uwierzytelniania wieloskładnikowego (MFA) i zasady dostępu warunkowego w przepływach użytkownika, aby wymuszać zasady i korygować ryzykowne logowania.
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
ms.openlocfilehash: 0e6a872891f09f60ea963fa783e6f49dc4e94a54
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861863"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Dodawanie dostępu warunkowego do przepływów użytkownika w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Dostęp warunkowy można dodać do przepływów Azure Active Directory B2C użytkownika (Azure AD B2C) lub zasad niestandardowych w celu zarządzania ryzykownych logować się do aplikacji. Azure Active Directory warunkowego (Azure AD) to narzędzie używane przez usługę Azure AD B2C do tworzenia sygnałów, podejmowania decyzji i wymuszania zasad organizacyjnych.

![Przepływ dostępu warunkowego](media/conditional-access-user-flow/conditional-access-flow.png)

Automatyzacja oceny ryzyka przy użyciu warunków zasad oznacza, że ryzykowne logowania są identyfikowane natychmiast, a następnie korygowane lub blokowane.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Omówienie usługi

Azure AD B2C ocenia każde zdarzenie logowania i upewnia się, że wszystkie wymagania dotyczące zasad zostały spełnione przed udzieleniem użytkownikowi dostępu. W tej **fazie oceny** usługa dostępu warunkowego ocenia sygnały zebrane przez wykrycia ryzyka usługi Identity Protection podczas zdarzeń logowania. Wynikiem tego procesu oceny jest zestaw oświadczeń, które wskazują, czy logowanie powinno zostać przyznane, czy zablokowane. Zasady Azure AD B2C wykorzystują te oświadczenia do podejmowania działań w ramach przepływu użytkownika, takich jak blokowanie dostępu lub wyzwanie użytkownikowi określonego korygowania, takiego jak uwierzytelnianie wieloskładnikowe (MFA). "Blokuj dostęp" zastępuje wszystkie inne ustawienia.

::: zone pivot="b2c-custom-policy"
W poniższym przykładzie pokazano profil techniczny dostępu warunkowego używany do oceny zagrożenia logowania.

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

W **następującej fazie** korygowania użytkownik ma dostęp do uwierzytelniania wieloskładnikowego. Po zakończeniu Azure AD B2C identity protection informuje o tym, że zidentyfikowane zagrożenie logowania zostało skorygowane i za pomocą jakiej metody. W tym przykładzie Azure AD B2C, że użytkownik pomyślnie ukończył wyzwanie uwierzytelniania wieloskładnikowego. 

::: zone pivot="b2c-custom-policy"

W poniższym przykładzie przedstawiono profil techniczny dostępu warunkowego używany do korygowania zidentyfikowanych zagrożeń:

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

- **Przepływ użytkownika** **lub zasady niestandardowe,** które przeprowadzi użytkownika przez proces logowania i rejestracji.
- **Zasady dostępu warunkowego,** które łączy sygnały w celu podejmowania decyzji i wymuszania zasad organizacji. Gdy użytkownik loguje się do aplikacji za pomocą zasad Azure AD B2C, zasady dostępu warunkowego wykorzystują sygnały usługi Azure AD Identity Protection do identyfikowania ryzykownych logie i prezentują odpowiednie akcje korygowania.
- **Zarejestrowana aplikacja,** która kieruje użytkowników do odpowiednich Azure AD B2C przepływu użytkownika lub zasad niestandardowych.
- [Przeglądarka TOR](https://www.torproject.org/download/) do symulowania ryzykownego logowania.

## <a name="service-limitations-and-considerations"></a>Ograniczenia i zagadnienia dotyczące usługi

W przypadku korzystania z dostępu warunkowego usługi Azure AD należy wziąć pod uwagę następujące kwestie:

- Usługa Identity Protection jest dostępna zarówno dla tożsamości lokalnych, jak i społecznościowych, takich jak Google lub Facebook. W przypadku tożsamości społecznościowych należy ręcznie aktywować dostęp warunkowy. Wykrywanie jest ograniczone, ponieważ poświadczenia konta społecznościowego są zarządzane przez zewnętrznego dostawcę tożsamości.
- W Azure AD B2C dzierżawców dostępny jest tylko podzbiór zasad dostępu warunkowego usługi [Azure AD.](../active-directory/conditional-access/overview.md)


## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Warstwa cenowa

Azure AD B2C Premium **P2** jest wymagana do tworzenia ryzykownych zasad logowania. **Dzierżawy Premium P1** mogą tworzyć zasady oparte na lokalizacji, aplikacji, użytkownikach lub zasadach opartych na grupach. Aby uzyskać więcej informacji, zobacz [Zmienianie warstwy Azure AD B2C cenowej](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Przygotowywanie dzierżawy Azure AD B2C dzierżawy

Aby dodać zasady dostępu warunkowego, wyłącz ustawienia domyślne zabezpieczeń:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz **ikonę Katalog i subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający Azure AD B2C dzierżawy.
3. W **obszarze Usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Możesz też użyć pola wyszukiwania, aby znaleźć i **wybrać Azure AD B2C**.
4. Wybierz **pozycję Właściwości**, a następnie wybierz pozycję Zarządzaj **ustawieniami domyślnymi zabezpieczeń.**

   ![Wyłączanie domyślnych ustawień zabezpieczeń](media/conditional-access-user-flow/disable-security-defaults.png)

5. W **obszarze Włącz domyślne ustawienia zabezpieczeń** wybierz pozycję **Nie.**

   ![Ustaw przełącznik Włącz ustawienia domyślne zabezpieczeń na wartość Nie](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Dodawanie zasad dostępu warunkowego

Zasady dostępu warunkowego to instrukcja przypisań i kontroli dostępu typu if-then. Zasady dostępu warunkowego łączy sygnały w celu podejmowania decyzji i wymuszania zasad organizacyjnych. Operator logiczny między przypisaniami to *And*. Operatorem w każdym przypisaniu jest *Lub*.

![Przypisania dostępu warunkowego](media/conditional-access-user-flow/conditional-access-assignments.png)

Aby dodać zasady dostępu warunkowego:

1. W Azure Portal wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W **obszarze Zabezpieczenia** wybierz pozycję Dostęp **warunkowy (wersja zapoznawcza).** Zostanie **otwarta strona Zasady dostępu** warunkowego.
1. Wybierz **pozycję + Nowe zasady.**
1. Wprowadź nazwę zasad, na przykład Blokuj *ryzykowne logowanie.*
1. W **obszarze Przypisania** wybierz pozycję Użytkownicy i **grupy,** a następnie wybierz jedną z następujących obsługiwanych konfiguracji:

    |Uwzględnij  |Licencja | Uwagi  |
    |---------|---------|---------|
    |**Wszyscy użytkownicy** | P1, P2 |Jeśli zdecydujesz się dołączyć **wszystkich użytkowników,** te zasady będą mieć wpływ na wszystkich użytkowników. Aby się nie zablokować, wyklucz konto administracyjne, wybierając pozycję Wyklucz, wybierając pozycję Role **katalogu,** a następnie wybierając pozycję **Administrator** globalny na liście. Możesz również wybrać pozycję **Użytkownicy i grupy,** a następnie wybrać swoje konto na liście **Wybierz wykluczonych** użytkowników.  | 
 
1. Wybierz **pozycję Aplikacje w chmurze lub akcje**, a następnie wybierz pozycję **Aplikacje.** Wyszukaj aplikację [jednostki zależnej.](tutorial-register-applications.md)

1. Wybierz **pozycję Warunki,** a następnie wybierz jeden z następujących warunków. Na przykład wybierz pozycję **Ryzyko logowania oraz** poziomy **wysokiego,** **średniego** **i** niskiego ryzyka.
    
    |Warunek  |Licencja  |Uwagi  |
    |---------|---------|---------|
    |**Ryzyko związane z użytkownikiem**|P2|Ryzyko związane z użytkownikiem reprezentuje prawdopodobieństwo naruszenia zabezpieczeń danej tożsamości lub konta.|
    |**Ryzyko związane z logowaniem**|P2|Ryzyko związane z logowaniem reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie zostało autoryzowane przez właściciela tożsamości.|
    |**Platformy urządzeń**|Nieobsługiwane| Scharakteryzowany przez system operacyjny działający na urządzeniu. Aby uzyskać więcej informacji, zobacz [Platformy urządzeń](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms).|
    |**Lokalizacje**|P1, P2|Nazwane lokalizacje mogą zawierać informacje o publicznej sieci IPv4, kraju lub regionie albo nieznane obszary, które nie są mapowe na określone kraje lub regiony. Aby uzyskać więcej informacji, zobacz [Lokalizacje](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations). |
 
1. W obszarze **Kontrole dostępu** wybierz pozycję **Udziel**. Następnie wybierz, czy dostęp ma być blokowany, czy udzielany:
    
    |Opcja  |Licencja |Uwaga  |
    |---------|---------|---------|
    |**Blokuj dostęp**|P1, P2| Uniemożliwia dostęp na podstawie warunków określonych w tych zasadach dostępu warunkowego.|
    |**Udzielanie dostępu przy** użyciu **ustawienia Wymagaj uwierzytelniania wieloskładnikowego**|P1, P2|Na podstawie warunków określonych w tych zasadach dostępu warunkowego użytkownik musi przejść przez Azure AD B2C uwierzytelnianie wieloskładnikowe.|

1. W **obszarze Włącz** zasady wybierz jedną z następujących opcji:
    
    |Opcja  |Licencja |Uwaga  |
    |---------|---------|---------|
    |**Tylko raport**|P1, P2| Tylko raport umożliwia administratorom ocenę wpływu zasad dostępu warunkowego przed włączeniem ich w ich środowisku. Zalecamy sprawdzenie zasad z tym stanem i określenie wpływu na użytkowników końcowych bez konieczności uwierzytelniania wieloskładnikowego ani blokowania użytkowników. Aby uzyskać więcej informacji, zobacz [Przeglądanie wyników dostępu warunkowego w raporcie inspekcji](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Włączone**| P1, P2| Zasady dostępu są oceniane i nie są wymuszane. |
    | **Wyłączone** | P1, P2| Zasady dostępu nie są aktywowane i nie mają wpływu na użytkowników. |

1. Włącz testowe zasady dostępu warunkowego, wybierając pozycję **Utwórz.**

## <a name="add-conditional-access-to-a-user-flow"></a>Dodawanie dostępu warunkowego do przepływu użytkownika

Po dodaniu zasad dostępu warunkowego usługi Azure AD włącz dostęp warunkowy w przepływie użytkownika lub zasadach niestandardowych. Po włączeniu dostępu warunkowego nie trzeba określać nazwy zasad.

W dowolnym momencie do poszczególnych użytkowników można zastosować wiele zasad dostępu warunkowego. W takim przypadku pierwszeństwo mają najbardziej rygorystyczne zasady kontroli dostępu. Na przykład jeśli jedna zasada wymaga uwierzytelniania wieloskładnikowego (MFA), podczas gdy inne blokują dostęp, użytkownik zostanie zablokowany.

## <a name="conditional-access-template-1-sign-in-risk-based-conditional-access"></a>Szablon dostępu warunkowego 1: dostęp warunkowy oparty na ryzyku logowania

Większości użytkowników dotyczy zachowanie normalne, które można śledzić. Gdy ta norma zostanie przekroczona, zezwolenie tym użytkownikom na zwykłe logowanie się może stanowić ryzyko. Możesz zablokować tego użytkownika lub po prostu poprosić go o przeprowadzenie uwierzytelniania wieloskładnikowego, aby udowodnić, że naprawdę jest tym, za kogo się podają.

Ryzyko związane z logowaniem reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie zostało autoryzowane przez właściciela tożsamości. Organizacje z licencjami P2 mogą tworzyć zasady dostępu warunkowego [zawierające Azure AD Identity Protection wykrywania](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)ryzyka logowania. Zwróć uwagę na [ograniczenia dotyczące wykrywania usługi Identity Protection dla usługi B2C.](https://docs.microsoft.com/azure/active-directory-b2c/identity-protection-investigate-risk?pivots=b2c-user-flow#service-limitations-and-considerations)

W przypadku wykrycia ryzyka użytkownicy mogą przeprowadzić uwierzytelnianie wieloskładnikowe, aby samodzielnie skorygować i zamknąć ryzykowne zdarzenie logowania, aby zapobiec niepotrzebnemu szumowi dla administratorów.

Organizacje powinny wybrać jedną z następujących opcji, aby włączyć zasady dostępu warunkowego opartego na ryzyku logowania wymagające uwierzytelniania wieloskładnikowego (MFA), gdy ryzyko logowania jest średnie LUB wysokie.

### <a name="enable-with-conditional-access-policy"></a>Włączanie przy użyciu zasad dostępu warunkowego

1. Zaloguj się w witrynie **Azure Portal**.
2. Przejdź do **Azure AD B2C**  >  **dostępu**  >  **warunkowego zabezpieczeń.**
3. Wybierz **pozycję Nowe zasady.**
4. Nadaj nazwę zasadom. Zalecamy, aby organizacje tworzyły znaczący standard nazw swoich zasad.
5. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
   1. W **obszarze Uwzględnij** wybierz pozycję **Wszyscy użytkownicy.**
   2. W **obszarze** Wyklucz wybierz pozycję **Użytkownicy** i grupy, a następnie wybierz konta dostępu awaryjnego lub włamań w organizacji. 
   3. Kliknij **Gotowe**.
6. W **obszarze Aplikacje w chmurze lub akcje**  >  **Uwzględnij** wybierz pozycję Wszystkie aplikacje w **chmurze.**
7. W **obszarze Ryzyko** związane z  >  **logowaniem w obszarze Warunki** ustaw opcję Konfiguruj **na** **wartość Tak.** W **obszarze Wybierz poziom ryzyka logowania te zasady będą stosowane** 
   1. Wybierz **pozycję Wysoki** i **Średni**.
   2. Kliknij **Gotowe**.
8. W **obszarze Kontrole dostępu**  >  **Wybierz** pozycję U **przyznaj dostęp,** **Wymagaj uwierzytelniania wieloskładnikowego** i wybierz **pozycję Wybierz.**
9. Potwierdź ustawienia i ustaw **opcję Włącz zasady** na wartość **Wł.**.
10. Wybierz **pozycję Utwórz,** aby utworzyć, aby włączyć zasady.

### <a name="enable-with-conditional-access-apis"></a>Włączanie przy użyciu interfejsów API dostępu warunkowego

Aby utworzyć zasady dostępu warunkowego opartego na ryzyku logowania za pomocą interfejsów API dostępu warunkowego, zapoznaj się z dokumentacją interfejsów [API dostępu warunkowego.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-apis#graph-api)

Przy użyciu następującego szablonu można utworzyć zasady dostępu warunkowego o nazwie wyświetlanej "CA002: Require MFA for medium+ sign-in risk" (Ca002: Wymagaj uwierzytelniania wieloskładnikowego w przypadku ryzyka logowania na średnim lub średnim ryzyku logowania) w trybie tylko do raportu.

```json
{
    "displayName": "Template 1: Require MFA for medium+ sign-in risk",
    "state": "enabledForReportingButNotEnforced",
    "conditions": {
        "signInRiskLevels": [ "high" ,
            "medium"
        ],
        "applications": {
            "includeApplications": [
                "All"
            ]
        },
        "users": {
            "includeUsers": [
                "All"
            ],
            "excludeUsers": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ]
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ]
    }
}
```

## <a name="enable-multi-factor-authentication-optional"></a>Włączanie uwierzytelniania wieloskładnikowego (opcjonalnie)

Podczas dodawania dostępu warunkowego do przepływu użytkownika należy rozważyć użycie uwierzytelniania **wieloskładnikowego (MFA).** Użytkownicy mogą używać kodu wielokrotnego za pośrednictwem wiadomości SMS lub głosu albo hasła za pośrednictwem poczty e-mail w celu uwierzytelniania wieloskładnikowego. Ustawienia usługi MFA są niezależne od ustawień dostępu warunkowego. Usługę MFA można ustawić na **zawsze wł.,** aby uwierzytelniania wieloskładnikowego było zawsze wymagane niezależnie od konfiguracji dostępu warunkowego. Możesz też ustawić uwierzytelniania wieloskładnikowego na **wartość Warunkowe,** aby uwierzytelniania wieloskładnikowego było wymagane tylko wtedy, gdy wymagają tego aktywne zasady dostępu warunkowego.

> [!IMPORTANT]
> Jeśli zasady dostępu warunkowego udzielają dostępu za pomocą usługi MFA, ale użytkownik nie zaestrował numeru telefonu, może on zostać zablokowany.

::: zone pivot="b2c-user-flow"

Aby włączyć dostęp warunkowy dla przepływu użytkownika, upewnij się, że wersja obsługuje dostęp warunkowy. Te wersje przepływu użytkownika mają etykietę **Zalecane.**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **ikonę Katalog i subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający Azure AD B2C dzierżawę.

1. W **obszarze Usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Możesz też użyć pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.

1. W **obszarze Zasady** wybierz pozycję **Przepływy użytkownika.** Następnie wybierz przepływ użytkownika.

1. Wybierz **pozycję Właściwości** i upewnij się, że przepływ użytkownika obsługuje dostęp warunkowy, szukając ustawienia z etykietą Dostęp **warunkowy.**
 
   ![Konfigurowanie uwierzytelniania wieloskładnikowego i dostępu warunkowego we właściwościach](media/conditional-access-user-flow/add-conditional-access.png)

1. W sekcji **Multi-factor authentication** (Uwierzytelnianie wieloskładnikowe) wybierz żądaną metodę **MFA,** a następnie w obszarze **MFA enforcement**(Wymuszanie uwierzytelniania wieloskładnikowego) wybierz pozycję **Conditional (Recommended) (Warunkowe (zalecane).**
 
1. W sekcji **Dostęp warunkowy** zaznacz pole wyboru **Wymusz zasady dostępu** warunkowego.

1. Wybierz pozycję **Zapisz**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Dodawanie dostępu warunkowego do zasad

1. Przykład zasad dostępu warunkowego można uzyskać w witrynie [GitHub.](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access)
1. W każdym pliku zastąp ciąg `yourtenant` nazwą swojej dzierżawy Azure AD B2C dzierżawy. Jeśli na przykład nazwa dzierżawy B2C to *contosob2c,* wszystkie wystąpienia obiektu stają `yourtenant.onmicrosoft.com` się `contosob2c.onmicrosoft.com` .
1. Przekaż pliki zasad.

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Wybierz zasady `B2C_1A_signup_signin_with_ca` lub , aby otworzyć jej stronę `B2C_1A_signup_signin_with_ca_whatif` przeglądu. Następnie wybierz **pozycję Uruchom przepływ użytkownika.** W **obszarze Aplikacja** wybierz pozycję *webapp1.* Adres **URL odpowiedzi** powinien pokazywać . `https://jwt.ms`
1. Skopiuj adres URL w obszarze **Uruchom punkt końcowy przepływu użytkownika**.

1. Aby symulować ryzykowne logowanie, otwórz przeglądarkę [Tor i](https://www.torproject.org/download/) użyj adresu URL skopiowanego w poprzednim kroku, aby zalogować się do zarejestrowanej aplikacji.

1. Wprowadź żądane informacje na stronie logowania, a następnie spróbuj się zalogować. Token jest zwracany do usługi `https://jwt.ms` i powinien być wyświetlany. W jwt.ms zdekodowany token powinien być wyświetlony, że logowanie zostało zablokowane.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz **pozycję Uruchom przepływ użytkownika.** W **obszarze Aplikacja** wybierz pozycję *webapp1.* Adres **URL odpowiedzi** powinien pokazywać . `https://jwt.ms`

1. Skopiuj adres URL w obszarze **Uruchom punkt końcowy przepływu użytkownika**.

1. Aby symulować ryzykowne logowanie, otwórz przeglądarkę [Tor i](https://www.torproject.org/download/) użyj adresu URL skopiowanego w poprzednim kroku, aby zalogować się do zarejestrowanej aplikacji.

1. Wprowadź żądane informacje na stronie logowania, a następnie spróbuj się zalogować. Token jest zwracany do i `https://jwt.ms` powinien być wyświetlany. W jwt.ms tokenu powinno być widać, że logowanie zostało zablokowane.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Przeglądanie wyników dostępu warunkowego w raporcie inspekcji

Aby przejrzeć wynik zdarzenia dostępu warunkowego:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz **ikonę Katalog i subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający Azure AD B2C dzierżawę.

3. W **obszarze Usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Możesz też użyć pola wyszukiwania, aby znaleźć i **wybrać Azure AD B2C**.

4. W **obszarze Działania** wybierz pozycję Dzienniki **inspekcji.**

5. Przefiltruj dziennik inspekcji, ustawiając **kategorię** **na B2C** i ustawiając typ zasobu **działania** na **IdentityProtection.** Następnie wybierz przycisk **Zastosuj**.

6. Przejrzyj działania inspekcji z ostatnich siedmiu dni. Uwzględniane są następujące typy działań:

   - **Ocena zasad dostępu warunkowego:** ten wpis dziennika inspekcji wskazuje, że ocena dostępu warunkowego została wykonana podczas uwierzytelniania.
   - **Korygowanie** użytkownika: ten wpis wskazuje, że użytkownik końcowy spełnił udzielenie lub wymagania zasad dostępu warunkowego, a to działanie zostało zgłoszone do aparatu ryzyka w celu ograniczenia (zmniejszenia ryzyka) użytkownika.

7. Wybierz wpis **dziennika** Ocena zasad dostępu warunkowego na liście, aby otworzyć stronę Szczegóły **aktywności: dziennik** inspekcji, na której są podane identyfikatory dziennika inspekcji wraz z informacjami w sekcji **Dodatkowe** szczegóły:

   - **ConditionalAccessResult:** przyznawanie wymagane przez ocenę zasad warunkowych.
   - **AppliedPolicies:** lista wszystkich zasad dostępu warunkowego, w których warunki zostały spełnione, a zasady są WŁ.
   - **ReportingPolicies:** lista zasad dostępu warunkowego, które zostały ustawione w trybie tylko do raportu i gdzie warunki zostały spełnione.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie interfejsu użytkownika w przepływie Azure AD B2C użytkownika](customize-ui-with-html.md)
