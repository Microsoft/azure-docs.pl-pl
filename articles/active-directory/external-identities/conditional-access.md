---
title: Dostęp warunkowy dla użytkowników współpracy B2B — Azure AD
description: Dowiedz się, jak wymuszać zasady uwierzytelniania wieloskładnikowego dla Azure Active Directory B2B.
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
ms.openlocfilehash: 22a5388d15b18180539eb95990a29f7ddf4f1951
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739552"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Dostęp warunkowy dla użytkowników współpracy B2B

W tym artykule opisano, jak organizacje mogą określać zakres zasad dostępu warunkowego dla użytkowników-gości B2B w celu uzyskania dostępu do swoich zasobów.
>[!NOTE]
>Ten przepływ uwierzytelniania lub autoryzacji różni się nieco w przypadku użytkowników-gości od istniejących użytkowników tego dostawcy tożsamości.

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Przepływ uwierzytelniania dla użytkowników-gości B2B z katalogu zewnętrznego

Na poniższym diagramie przedstawiono przepływ: obraz przedstawiający przepływ ![ uwierzytelniania dla użytkowników-gości B2B z katalogu zewnętrznego](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Krok | Opis |
|--------------|-----------------------|
| 1. | Użytkownik-gość B2B żąda dostępu do zasobu. Zasób przekierowuje użytkownika do jego dzierżawy zasobów, zaufanego identyfikatora.|
| 2. | Dzierżawa zasobów identyfikuje użytkownika jako zewnętrznego i przekierowuje go do identyfikatora użytkownika-gościa B2B. Użytkownik wykonuje uwierzytelnianie podstawowe w idP.
| 3. | Identyfikator użytkownika-gościa B2B wydaje token użytkownikowi. Użytkownik jest przekierowywany z powrotem do dzierżawy zasobów przy użyciu tokenu. Dzierżawa zasobów weryfikuje token, a następnie ocenia użytkownika pod względem zasad urzędu certyfikacji. Na przykład dzierżawa zasobów może wymagać od użytkownika wykonania Azure Active Directory (AD) Multi-Factor Authentication.
| 4. | Gdy wszystkie zasady urzędu certyfikacji dzierżawy zasobów zostaną spełnione, dzierżawa zasobów wydaje własny token i przekierowuje użytkownika do jego zasobu.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Przepływ uwierzytelniania dla użytkowników-gości B2B z jednym kodem dostępu

Na poniższym diagramie przedstawiono przepływ: obraz przedstawiający przepływ ![ uwierzytelniania dla użytkowników-gości B2B z jednym kodem dostępu](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Krok | Opis |
|--------------|-----------------------|
| 1. |Użytkownik żąda dostępu do zasobu w innej dzierżawie. Zasób przekierowuje użytkownika do jego dzierżawy zasobów, zaufanego identyfikatora.|
| 2. | Dzierżawa zasobów identyfikuje użytkownika jako zewnętrznego użytkownika jednorazowego kodu dostępu w wiadomości [e-mail (OTP)](./one-time-passcode.md) i wysyła do użytkownika wiadomość e-mail z uwierzytelnianiem OTP.|
| 3. | Użytkownik pobiera uwierzytelnianie OTP i przesyła kod. Dzierżawa zasobów ocenia użytkownika pod względem zasad urzędu certyfikacji.
| 4. | Gdy wszystkie zasady urzędu certyfikacji zostaną spełnione, dzierżawa zasobów wydaje token i przekierowuje użytkownika do jego zasobu. |

>[!NOTE]
>Jeśli użytkownik pochodzi z dzierżawy zasobów zewnętrznych, nie można również ocenić zasad urzędu certyfikacji użytkownika-gościa B2B. Obecnie tylko zasady urzędu certyfikacji dzierżawy zasobów mają zastosowanie do jego gości.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Usługa Azure AD Multi-Factor Authentication dla użytkowników B2B

Organizacje mogą wymuszać wiele zasad usługi Azure AD Multi-Factor Authentication dla użytkowników-gości B2B. Te zasady można wymuszać na poziomie dzierżawy, aplikacji lub poszczególnych użytkowników w taki sam sposób, w jaki są one włączone dla pełnoetatowych pracowników i członków organizacji.
Dzierżawa zasobów jest zawsze odpowiedzialna za usługę Azure AD Multi-Factor Authentication dla użytkowników, nawet jeśli organizacja użytkownika-gościa ma możliwości usługi Multi-Factor Authentication. Oto przykład:

1. Administrator lub pracownik informacyjny w firmie o nazwie Fabrikam zaprasza użytkownika z innej firmy o nazwie Contoso do korzystania z aplikacji Woodgrove.

2. Aplikacja Woodgrove w firmie Fabrikam jest skonfigurowana tak, aby wymagać uwierzytelniania wieloskładnikowego usługi Azure AD przy dostępie.

3. Gdy użytkownik-gość B2B z firmy Contoso próbuje uzyskać dostęp do woodgrove w dzierżawie firmy Fabrikam, zostanie poproszony o ukończenie zadania uwierzytelniania wieloskładnikowego usługi Azure AD.

4. Użytkownik-gość może następnie skonfigurować usługę Azure AD Multi-Factor Authentication przy użyciu firmy Fabrikam i wybrać opcje.

5. Ten scenariusz działa w przypadku dowolnej tożsamości — usługi Azure AD lub osobistego konta Microsoft (MSA). Na przykład jeśli użytkownik w firmie Contoso uwierzytelnia się przy użyciu identyfikatora społecznościowego.

6. Firma Fabrikam musi mieć wystarczającą ilość licencji usługi Azure AD w witrynie Premium, które obsługują usługę Azure AD Multi-Factor Authentication. Użytkownik z firmy Contoso następnie korzysta z tej licencji firmy Fabrikam. Aby uzyskać informacje na temat licencjonowania B2B, zobacz model rozliczeń dla tożsamości zewnętrznych usługi [Azure AD.](./external-identities-pricing.md)

>[!NOTE]
>Usługa Azure AD Multi-Factor Authentication jest wykonywana w ramach pracy nad zasobami w celu zapewnienia przewidywalności. Gdy użytkownik-gość się zaloguje, zobaczy stronę logowania dzierżawy zasobów wyświetlaną w tle oraz własną stronę logowania dzierżawy głównej i logo firmy na pierwszym planie.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Konfigurowanie usługi Azure AD Multi-Factor Authentication dla użytkowników B2B

Aby skonfigurować usługę Azure AD Multi-Factor Authentication dla użytkowników współpracy B2B, obejrzyj to wideo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>Użytkownicy B2B usługi Azure AD Multi-Factor Authentication w celu realizacji oferty

Aby dowiedzieć się więcej na temat realizacji usługi Azure AD Multi-Factor Authentication, obejrzyj ten film:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Resetowanie usługi Azure AD Multi-Factor Authentication dla użytkowników B2B

Teraz dostępne są następujące polecenia cmdlet programu PowerShell do weryfikacji użytkowników-gości B2B:

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

3. Zresetuj metodę Usługi Azure AD Multi-Factor Authentication dla określonego użytkownika, aby wymagać od użytkownika współpracy B2B ponownego skonfigurowania metod weryfikacji. 
   Oto przykład:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Dostęp warunkowy dla użytkowników B2B

Istnieją różne czynniki wpływające na zasady urzędu certyfikacji dla użytkowników-gości B2B.

### <a name="device-based-conditional-access"></a>Dostęp warunkowy oparty na urządzeniach

W przypadku urzędu certyfikacji istnieje możliwość wymagania, aby urządzenie użytkownika było zgodne lub zostało przyłączone do hybrydowej usługi [Azure AD.](../conditional-access/concept-conditional-access-conditions.md#device-state-preview) Użytkownicy-goście B2B mogą spełnić wymagania zgodności tylko wtedy, gdy dzierżawa zasobów może zarządzać swoim urządzeniem. Urządzenia nie mogą być zarządzane przez więcej niż jedną organizację na raz. Użytkownicy-goście B2B nie mogą spełnić wymagania dołączenia do hybrydowej usługi Azure AD, ponieważ nie mają lokalnego konta usługi AD. Tylko wtedy, gdy urządzenie użytkownika-gościa jest nieza pomocą zarządzania, może zarejestrować lub zarejestrować swoje urządzenie w dzierżawie zasobów, a następnie upewnić się, że urządzenie jest zgodne. Użytkownik może następnie spełnić wymagania kontrolki udzielania.

>[!Note]
>Nie zaleca się wymagania urządzenia zarządzanego dla użytkowników zewnętrznych.

### <a name="mobile-application-management-policies"></a>Zasady zarządzania aplikacjami mobilnymi

Urząd certyfikacji udziela kontrolek, takich jak **Wymagaj zatwierdzonych aplikacji klienckich** i **Wymagaj zasad** ochrony aplikacji, wymagają zarejestrowania urządzenia w dzierżawie. Te kontrolki można stosować tylko do urządzeń [z systemami iOS i Android.](../conditional-access/concept-conditional-access-conditions.md#device-platforms) Jednak żadna z tych kontrolek nie może być stosowana do użytkowników-gości B2B, jeśli urządzenie użytkownika jest już zarządzane przez inną organizację. Urządzenia przenośnego nie można jednocześnie zarejestrować w więcej niż jednej dzierżawie. Jeśli urządzenie przenośne jest zarządzane przez inną organizację, użytkownik zostanie zablokowany. Tylko wtedy, gdy urządzenie użytkownika-gościa jest nieza pomocą zarządzania, może zarejestrować swoje urządzenie w dzierżawie zasobów. Użytkownik może następnie spełnić warunki kontrolki udzielania.  

>[!NOTE]
>Nie zaleca się wymagania zasad ochrony aplikacji dla użytkowników zewnętrznych.

### <a name="location-based-conditional-access"></a>Dostęp warunkowy oparty na lokalizacji

Zasady [oparte na lokalizacji](../conditional-access/concept-conditional-access-conditions.md#locations) oparte na zakresach adresów IP można wymusić, jeśli organizacja zapraszająca może utworzyć zakres zaufanych adresów IP, który definiuje swoje organizacje partnerskie.

Zasady można również wymuszać na podstawie **lokalizacji geograficznych**.

### <a name="risk-based-conditional-access"></a>Dostęp warunkowy oparty na ryzyku

Zasady [ryzyka związanego z logowaniem](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) są wymuszane, jeśli użytkownik-gość B2B spełnia warunki kontroli udzielania. Na przykład organizacja może wymagać uwierzytelniania wieloskładnikowego usługi Azure AD w przypadku średniego lub wysokiego ryzyka związanego z logowaniem. Jeśli jednak użytkownik nie został wcześniej zarejestrowany w usłudze Azure AD Multi-Factor Authentication w dzierżawie zasobów, zostanie zablokowany. Ma to na celu uniemożliwienie złośliwym użytkownikom rejestrowania własnych poświadczeń usługi Azure AD Multi-Factor Authentication w przypadku naruszenia hasła uprawnionego użytkownika.

Zasady [ryzyka związanego z użytkownikiem](../conditional-access/concept-conditional-access-conditions.md#user-risk) nie mogą być jednak rozwiązane w dzierżawie zasobów. Jeśli na przykład wymagasz zmiany hasła dla użytkowników-gości o wysokim ryzyku, zostaną zablokowani z powodu możliwości resetowania haseł w katalogu zasobów.

### <a name="conditional-access-client-apps-condition"></a>Warunek aplikacji klienckich dostępu warunkowego

[Warunki aplikacji klienckich](../conditional-access/concept-conditional-access-conditions.md#client-apps) zachowują się tak samo dla użytkowników-gości B2B, jak w przypadku innych typów użytkowników. Można na przykład uniemożliwić użytkownikom-gościom korzystanie ze starszych protokołów uwierzytelniania.

### <a name="conditional-access-session-controls"></a>Kontrolki sesji dostępu warunkowego

[Kontrolki sesji](../conditional-access/concept-conditional-access-session.md) zachowują się tak samo w przypadku użytkowników-gości B2B, jak w przypadku każdego innego typu użytkownika.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły dotyczące współpracy B2B w usłudze Azure AD:

- [Czym jest współpraca B2B w usłudze Azure AD?](./what-is-b2b.md)
- [Usługa Identity Protection i użytkownicy B2B](../identity-protection/concept-identity-protection-b2b.md)
- [Cennik tożsamości zewnętrznych](https://azure.microsoft.com/pricing/details/active-directory/)
- [Często zadawane pytania](./faq.md)
