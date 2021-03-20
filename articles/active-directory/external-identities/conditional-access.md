---
title: Dostęp warunkowy dla użytkowników współpracy B2B — Azure AD
description: Dowiedz się, jak wymusić zasady uwierzytelniania wieloskładnikowego dla Azure Active Directory użytkowników B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646286"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Dostęp warunkowy dla użytkowników współpracy B2B

W tym artykule opisano, jak organizacje mogą określać zasady dostępu warunkowego (CA) dla użytkowników gościa B2B w celu uzyskania dostępu do zasobów.
>[!NOTE]
>Ten przepływ uwierzytelniania lub autoryzacji jest nieco różny dla użytkowników-Gości niż w przypadku istniejących użytkowników tego dostawcy tożsamości (dostawcy tożsamości).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Przepływ uwierzytelniania dla użytkowników Gości B2B z zewnętrznego katalogu

Na poniższym diagramie przedstawiono przepływ: ![ obraz przedstawia przepływ uwierzytelniania dla użytkowników gościa B2B z zewnętrznego katalogu](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Krok | Opis |
|--------------|-----------------------|
| 1. | Użytkownik-Gość B2B żąda dostępu do zasobu. Zasób przekierowuje użytkownika do dzierżawy zasobów, zaufane dostawcy tożsamości.|
| 2. | Dzierżawca zasobów identyfikuje użytkownika jako zewnętrznego i przekierowuje użytkownika do dostawcy tożsamości użytkownika-gościa B2B. Użytkownik wykonuje podstawowe uwierzytelnianie w dostawcy tożsamości.
| 3. | Dostawcy tożsamości użytkownika-gościa B2B wystawia token dla użytkownika. Użytkownik zostanie przekierowany z powrotem do dzierżawy zasobu z tokenem. Dzierżawca zasobów weryfikuje token, a następnie szacuje użytkownika w odniesieniu do jego zasad urzędu certyfikacji. Na przykład dzierżawca zasobów może wymagać od użytkownika przeprowadzenia Multi-Factor Authentication Azure Active Directory (AD).
| 4. | Po spełnieniu wszystkich zasad urzędu certyfikacji dzierżawcy zasobów dzierżawa zasobów wystawia swój własny token i przekierowuje użytkownika do jego zasobu.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Przepływ uwierzytelniania dla użytkowników Gości B2B z jednorazowym kodem dostępu

Na poniższym diagramie przedstawiono przepływ: ![ obraz przedstawia przepływ uwierzytelniania dla użytkowników gościa B2B z jednorazowym kodem dostępu](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Krok | Opis |
|--------------|-----------------------|
| 1. |Użytkownik żąda dostępu do zasobu w innej dzierżawie. Zasób przekierowuje użytkownika do dzierżawy zasobów, zaufane dostawcy tożsamości.|
| 2. | Dzierżawca zasobów identyfikuje użytkownika jako [zewnętrzną wiadomość e-mail jednorazowy kod dostępu (OTP)](./one-time-passcode.md) , a następnie wysyła wiadomość E-mail z uwierzytelnianiem OTP do użytkownika.|
| 3. | Użytkownik pobiera uwierzytelnianie OTP i przesyła kod. Dzierżawca zasobów szacuje użytkownika w odniesieniu do zasad jego urzędu certyfikacji.
| 4. | Po spełnieniu wszystkich zasad urzędu certyfikacji dzierżawca zasobów wystawia token i przekierowuje użytkownika do jego zasobu. |

>[!NOTE]
>Jeśli użytkownik należy do dzierżawy zasobów zewnętrznych, nie jest możliwe przeprowadzenie oceny zasad urzędu certyfikacji dostawcy tożsamości użytkownika gościa B2B. Obecnie tylko zasady urzędu certyfikacji dzierżawcy zasobu dotyczą swoich Gości.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Multi-Factor Authentication usługi Azure AD dla użytkowników B2B

Organizacje mogą wymuszać wiele zasad Multi-Factor Authentication usługi Azure AD dla użytkowników Gości B2B. Te zasady można wymusić na poziomie dzierżawy, aplikacji lub poszczególnych użytkowników w taki sam sposób, w jaki są one dostępne dla pracowników i członków organizacji w pełnym czasie.
Dzierżawca zasobów jest zawsze odpowiedzialny za Multi-Factor Authentication usługi Azure AD dla użytkowników, nawet jeśli organizacja użytkownika-gościa ma Multi-Factor Authentication możliwości. Oto przykład —

1. Administrator lub pracownik przetwarzający informacje w firmie o nazwie Fabrikam zaprasza użytkownika z innej firmy o nazwie contoso do korzystania z aplikacji Woodgrove.

2. Aplikacja Woodgrove w firmie Fabrikam jest skonfigurowana tak, aby wymagała Multi-Factor Authentication usługi Azure AD w celu uzyskania dostępu.

3. Gdy użytkownik-Gość B2B firmy Contoso próbuje uzyskać dostęp do Woodgrove w dzierżawie firmy Fabrikam, zostanie poproszony o ukończenie wyzwania usługi Azure AD Multi-Factor Authentication.

4. Użytkownik-gość może następnie skonfigurować swoją Multi-Factor Authentication usługi Azure AD przy użyciu programu Fabrikam i wybrać opcje.

5. Ten scenariusz działa w przypadku każdej tożsamości — usługi Azure AD lub osobistego konta Microsoft (MSA). Na przykład jeśli użytkownik w firmie Contoso uwierzytelnia się przy użyciu identyfikatora społecznościowego.

6. Firma Fabrikam musi mieć wystarczającą liczbę licencji usługi Azure AD, które obsługują usługę Azure AD Multi-Factor Authentication. Użytkownik firmy Contoso używa tej licencji od firmy Fabrikam. Aby uzyskać informacje na temat licencjonowania B2B, zobacz [model rozliczeń dla tożsamości zewnętrznych usługi Azure AD](./external-identities-pricing.md) .

>[!NOTE]
>Usługa Azure AD Multi-Factor Authentication jest wykonywana w dzierżawie zasobów, aby zapewnić przewidywalność.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Konfigurowanie Multi-Factor Authentication usługi Azure AD dla użytkowników B2B

Aby skonfigurować usługę Azure AD Multi-Factor Authentication dla użytkowników współpracy B2B, Obejrzyj ten klip wideo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>Użytkownicy B2B usługi Azure AD Multi-Factor Authentication na potrzeby wykupu oferty

Aby dowiedzieć się więcej na temat środowiska wykupu usługi Azure AD Multi-Factor Authentication, Obejrzyj ten film wideo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Resetowanie Multi-Factor Authentication usługi Azure AD dla użytkowników B2B

Teraz następujące polecenia cmdlet programu PowerShell są dostępne do poświadczania użytkowników-Gości B2B:

1. Łączenie z usługą Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Pobierz wszystkich użytkowników z metodami weryfikacji

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Oto przykład:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Zresetuj metodę Multi-Factor Authentication usługi Azure AD dla określonego użytkownika, aby wymagać od użytkownika współpracy B2B, aby ponownie ustawił metody potwierdzania. 
   Oto przykład:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Dostęp warunkowy dla użytkowników B2B

Istnieją różne czynniki wpływające na zasady urzędu certyfikacji dla użytkowników gościa B2B.

### <a name="device-based-conditional-access"></a>Dostęp warunkowy oparty na urządzeniach

W urzędzie certyfikacji istnieje możliwość wymagania [zgodności urządzenia użytkownika z usługą Azure AD](../conditional-access/concept-conditional-access-conditions.md#device-state-preview). Użytkownicy-Goście B2B mogą spełnić wymagania dotyczące zgodności tylko wtedy, gdy dzierżawca zasobów może zarządzać swoim urządzeniem. Urządzenia nie mogą być zarządzane jednocześnie przez więcej niż jedną organizację. Użytkownicy-Goście B2B nie mogą zaspokoić hybrydowego sprzężenia usługi Azure AD, ponieważ nie mają lokalnego konta usługi AD. Tylko wtedy, gdy urządzenie użytkownika-gościa jest niezarządzane, może zarejestrować lub zarejestrować swoje urządzenie w dzierżawie zasobu, a następnie zapewnić zgodność urządzenia. Użytkownik może następnie spełnić wartość kontrolki Udziel.

>[!Note]
>Nie zaleca się wymagać urządzenia zarządzanego dla użytkowników zewnętrznych.

### <a name="mobile-application-management-policies"></a>Zasady zarządzania aplikacjami mobilnymi

Urząd certyfikacji przyznaje kontrolki, takie jak **Wymagaj zatwierdzonych aplikacji klienckich** i wymaga, aby **Zasady ochrony aplikacji wymagały** zarejestrowania urządzenia w dzierżawie. Te kontrolki można stosować tylko na [urządzeniach z systemami iOS i Android](../conditional-access/concept-conditional-access-conditions.md#device-platforms). Jednak żadna z tych kontrolek nie może być stosowana dla użytkowników-Gości B2B, jeśli urządzenie użytkownika jest już zarządzane przez inną organizację. Nie można zarejestrować urządzenia przenośnego w więcej niż jednej dzierżawie jednocześnie. Jeśli urządzenie przenośne jest zarządzane przez inną organizację, użytkownik zostanie zablokowany. Tylko wtedy, gdy urządzenie użytkownika-gościa jest niezarządzane, może zarejestrować swoje urządzenie w dzierżawie zasobów. Użytkownik może następnie spełnić wartość kontrolki Udziel.  

>[!NOTE]
>Nie zaleca się zadawania zasad ochrony aplikacji użytkownikom zewnętrznym.

### <a name="location-based-conditional-access"></a>Dostęp warunkowy oparty na lokalizacji

[Zasady oparte na lokalizacji](../conditional-access/concept-conditional-access-conditions.md#locations) oparte na zakresach adresów IP można wymusić, jeśli zapraszana organizacja może utworzyć zakres zaufanego adresu IP, który definiuje ich organizacje partnerskie.

Zasady mogą być również wymuszane na podstawie **lokalizacji geograficznych**.

### <a name="risk-based-conditional-access"></a>Dostęp warunkowy oparty na ryzyku

[Zasady dotyczące ryzyka związanego z logowaniem](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) są wymuszane, jeśli użytkownik Gość B2B spełni kontrolę dotacji. Organizacja może na przykład wymagać, aby usługa Azure AD Multi-Factor Authentication w celu zapewnienia średniego lub wysokiego ryzyka związanego z logowaniem. Jeśli jednak użytkownik nie zarejestrował wcześniej usługi Azure AD Multi-Factor Authentication w dzierżawie zasobów, zostanie on zablokowany. Jest to konieczne, aby uniemożliwić złośliwym użytkownikom rejestrowanie własnych poświadczeń usługi Azure AD Multi-Factor Authentication w przypadku naruszenia hasła przez uprawnionych użytkowników.

Nie można jednak rozpoznać [zasad ryzyka użytkownika](../conditional-access/concept-conditional-access-conditions.md#user-risk) w dzierżawie zasobu. Na przykład jeśli wymagana jest zmiana hasła dla użytkowników-Gości o wysokim ryzyku, będą one blokowane z powodu niezdolności do resetowania haseł w katalogu zasobów.

### <a name="conditional-access-client-apps-condition"></a>Warunek aplikacji klienta dostępu warunkowego

[Warunki aplikacji klienckich](../conditional-access/concept-conditional-access-conditions.md#client-apps) działają tak samo dla użytkowników Gości B2B, jak w przypadku każdego innego typu użytkownika. Można na przykład uniemożliwić użytkownikom-gościom używanie starszych protokołów uwierzytelniania.

### <a name="conditional-access-session-controls"></a>Kontrolki sesji dostępu warunkowego

[Kontrolki sesji](../conditional-access/concept-conditional-access-session.md) działają tak samo dla użytkowników Gości B2B, jak w przypadku każdego innego typu użytkownika.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły dotyczące współpracy B2B w usłudze Azure AD:

- [Czym jest współpraca B2B w usłudze Azure AD?](./what-is-b2b.md)
- [Usługa Identity Protection i użytkownicy B2B](../identity-protection/concept-identity-protection-b2b.md)
- [Cennik tożsamości zewnętrznych](https://azure.microsoft.com/pricing/details/active-directory/)
- [Często zadawane pytania](./faq.md)