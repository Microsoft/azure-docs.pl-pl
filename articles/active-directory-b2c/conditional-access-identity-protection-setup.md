---
title: Konfigurowanie ochrony tożsamości i dostępu warunkowego w Azure AD B2C
description: Dowiedz się, jak skonfigurować ochronę tożsamości i dostęp warunkowy dla Azure AD B2C dzierżawy, aby wyświetlić ryzykowne logowania i inne zdarzenia związane z ryzykiem oraz utworzyć zasady na podstawie wykrytych zagrożeń.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2472183673e5f06f5664a306a69d14c2eaf5f82d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949772"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Konfigurowanie ochrony tożsamości i dostępu warunkowego w Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Program Identity Protection zapewnia stałe wykrywanie ryzyka dla dzierżawy Azure AD B2C. Jeśli Azure AD B2C warstwa cenowa dzierżawy to Premium P2, można wyświetlić szczegółowe zdarzenia dotyczące ryzyka związanego z ochroną tożsamości w Azure Portal. Możesz również użyć zasad [dostępu warunkowego](../active-directory/conditional-access/overview.md) na podstawie tych wykrycia ryzyka, aby określić akcje i wymusić zasady organizacyjne.

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawca Azure AD B2C musi być [połączony z subskrypcją usługi Azure AD](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).
- Aby można było korzystać z dostępu warunkowego opartego na ryzyku i użytkownika, Azure AD B2C Premium P2. W razie potrzeby [Zmień warstwę cenową Azure AD B2C na Premium P2](./billing.md). 
- Aby zarządzać usługą Identity Protection i dostępem warunkowym w dzierżawie B2C, musisz mieć konto przypisane do roli administratora globalnego lub administratora zabezpieczeń.
- Aby korzystać z tych funkcji w dzierżawie, musisz najpierw przełączyć się do warstwy cenowej "Azure AD B2C Premium P2".

## <a name="set-up-identity-protection"></a>Konfigurowanie ochrony tożsamości

Ochrona tożsamości jest domyślnie włączona. Aby móc wyświetlać zdarzenia dotyczące ryzyka związanego z ochroną tożsamości w dzierżawie Azure AD B2C, wystarczy połączyć dzierżawę Azure AD B2C z subskrypcją usługi Azure AD i wybrać warstwę cenową Azure AD B2C w warstwie Premium P2. Szczegółowe raporty dotyczące zdarzeń dotyczących ryzyka można przeglądać w Azure Portal.

### <a name="supported-identity-protection-risk-detections"></a>Obsługiwane wykrywanie ryzyka ochrony tożsamości

Następujące wykrycia ryzyka są obecnie obsługiwane w przypadku Azure AD B2C:  

|Typ wykrywania ryzyka  |Opis  |
|---------|---------|
| Nietypowa podróż     | Zaloguj się z nietypowej lokalizacji na podstawie ostatnich logowań użytkownika.        |
|Anonimowy adres IP     | Zaloguj się przy użyciu anonimowego adresu IP (na przykład: przeglądarki tor, sieci VPN Anonymizer).        |
|Połączony adres IP złośliwego oprogramowania     | Zaloguj się przy użyciu połączonego adresu IP złośliwego oprogramowania.         |
|Nieznane właściwości logowania     | Zaloguj się przy użyciu właściwości, które nie były ostatnio widziane dla danego użytkownika.        |
|Administrator zatwierdził naruszenie zabezpieczeń    | Administrator wykrył, że naruszono bezpieczeństwo użytkownika.             |
|Rozpylanie hasła     | Zalogowanie się za pomocą ataku polegającego na rozpylaniu hasła.      |
|Analiza zagrożeń usługi Azure AD     | Wewnętrzne i zewnętrzne źródła analizy zagrożeń firmy Microsoft określiły znany wzorzec ataku.        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Wyświetlanie zdarzeń o podwyższonym ryzyku dla dzierżawy Azure AD B2C

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.

1. W obszarze **zabezpieczenia** wybierz pozycję **ryzykowni użytkownicy (wersja zapoznawcza)**.

   ![Ryzykowni użytkownicy](media/conditional-access-identity-protection-setup/risky-users.png)

1. W obszarze **zabezpieczenia** wybierz pozycję **wykrywanie ryzyka (wersja zapoznawcza)**.

   ![Wykrycia ryzyka](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>Dodawanie zasad dostępu warunkowego 

Aby dodać zasady dostępu warunkowego oparte na wykryciu ryzyka ochrony tożsamości, upewnij się, że wartości domyślne zabezpieczeń są wyłączone dla dzierżawy usługi Azure AD B2C, a następnie utwórz zasady dostępu warunkowego.

### <a name="to-disable-security-defaults"></a>Aby wyłączyć ustawienia domyślne zabezpieczeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

3. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.

4. Wybierz pozycję **Właściwości**, a następnie wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.

   ![Wyłącz ustawienia domyślne zabezpieczeń](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. W obszarze Włącz domyślne ustawienia zabezpieczeń wybierz pozycję nie. 

   ![Ustawienie opcji Włącz ustawienia domyślne zabezpieczeń włącza wartość nie](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>Aby utworzyć zasady dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.

1. W obszarze **zabezpieczenia** wybierz pozycję **dostęp warunkowy (wersja zapoznawcza)**. Zostanie otwarta strona **zasady dostępu warunkowego** . 

1. Wybierz pozycję **nowe zasady** i postępuj zgodnie z dokumentacją dostępu warunkowego usługi Azure AD, aby utworzyć nowe zasady. Poniżej przedstawiono przykład:

   - [Dostęp warunkowy na podstawie ryzyka związanego z logowaniem: Włączanie przy użyciu zasad dostępu warunkowego](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > Wybierając użytkowników, do których chcesz zastosować zasady, nie zaznaczaj tylko opcji **Wszyscy użytkownicy** lub Zablokuj logowanie się.

## <a name="test-the-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

1. Utwórz zasady dostępu warunkowego, jak wspomniano powyżej, z następującymi ustawieniami:
   
   - W przypadku **użytkowników i grup** wybierz użytkownika testowego. Nie zaznaczaj opcji **Wszyscy użytkownicy** lub Zablokuj się przed zalogowaniem się.
   - W przypadku **aplikacji lub akcji w chmurze** wybierz **pozycję Wybierz aplikacje**, a następnie wybierz aplikację jednostki uzależnionej.
   - W obszarze warunki wybierz pozycję **ryzyko związane z logowaniem** i **wysoki**, **Średni** i **niski** poziom ryzyka.
   - W obszarze **Udziel** wybierz opcję **Blokuj dostęp**.

      ![Wybierz pozycję Blokuj dostęp](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. Włącz testowe zasady dostępu warunkowego, wybierając pozycję **Utwórz**.

1. Symuluj ryzykowne Logowanie przy użyciu [przeglądarki tor](https://www.torproject.org/download/). 

1. W dekodowanej tokenie jwt.ms dla próby zalogowania powinno być widoczne, że logowanie zostało zablokowane:

   ![Testowanie zablokowanego logowania](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Przejrzyj wyniki dostępu warunkowego w raporcie inspekcji

Aby sprawdzić wynik zdarzenia dostępu warunkowego:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

3. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.

4. W obszarze **działania** wybierz pozycję **dzienniki inspekcji**.

5. Przefiltruj dziennik inspekcji, ustawiając **kategorię** na **B2C** i ustawiając **Typ zasobu działania** na **IdentityProtection**. Następnie wybierz przycisk **Zastosuj**.

6. Przeglądanie działania inspekcji przez maksymalnie 7 dni. Uwzględniane są następujące typy działań:

   - **Oceń zasady dostępu warunkowego**: Ten wpis dziennika inspekcji wskazuje, że podczas uwierzytelniania wykonano ocenę dostępu warunkowego.
   - **Koryguj użytkownika**: Ten wpis wskazuje, że udzielenie lub wymagania zasad dostępu warunkowego zostały spełnione przez użytkownika końcowego, a to działanie zostało zgłoszone do aparatu ryzyka, aby zmniejszyć ryzyko (łagodzenie) użytkownika.

7. Wybierz pozycję **Oceń dziennik zasad dostępu warunkowego** na liście, aby otworzyć stronę **szczegóły działania: dziennik inspekcji** , która zawiera identyfikatory dziennika inspekcji wraz z tymi informacjami w sekcji **dodatkowe szczegóły** :

   - ConditionalAccessResult: przyznanie wymagane przez ocenę zasad warunkowych.
   - AppliedPolicies: Lista wszystkich zasad dostępu warunkowego, w których warunki zostały spełnione i zasady są włączone.
   - ReportingPolicies: lista zasad dostępu warunkowego, które zostały ustawione na tryb "tylko raportowanie" i, w których warunki zostały spełnione.

## <a name="next-steps"></a>Następne kroki

[Dodaj dostęp warunkowy do przepływu użytkownika](conditional-access-user-flow.md).