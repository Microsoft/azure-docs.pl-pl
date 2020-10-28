---
title: Dostęp warunkowy dla użytkowników współpracy B2B — Azure AD
description: Dowiedz się, jak wymusić zasady uwierzytelniania wieloskładnikowego dla Azure Active Directory użytkowników B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: eccbbb22814788aaf06fa6fd10d8c376203c1d49
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892455"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Dostęp warunkowy dla użytkowników współpracy B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Uwierzytelnianie wieloskładnikowe dla użytkowników B2B
Dzięki funkcji współpracy B2B usługi Azure AD organizacje mogą wymuszać zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkowników B2B. Te zasady można wymusić na poziomie dzierżawy, aplikacji lub poszczególnych użytkowników w taki sam sposób, w jaki są one włączone dla pracowników i członków organizacji w pełnym czasie. Zasady usługi MFA są wymuszane w organizacji zasobów.

Przykład:
1. Administrator lub pracownik przetwarzający informacje w firmie A prosi użytkownika od firmy B do aplikacji *foo* w firmie a.
2. Aplikacja *foo* w firmie A jest skonfigurowana tak, aby wymagać uwierzytelniania wieloskładnikowego w przypadku dostępu.
3. Gdy użytkownik z firmy B próbuje uzyskać dostęp do aplikacji *foo* w dzierżawie firmy, zostanie poproszony o ukończenie wyzwania usługi MFA.
4. Użytkownik może skonfigurować uwierzytelnianie wieloskładnikowe za pomocą firmy A i wybrać opcję MFA.
5. Ten scenariusz działa w przypadku dowolnej tożsamości (Azure AD lub MSA, na przykład jeśli użytkownicy w firmie B uwierzytelniają się przy użyciu identyfikatora społecznościowego)
6. Firma A musi dysponować wystarczającą licencją usługi Azure AD, która obsługuje uwierzytelnianie wieloskładnikowe. Użytkownik z firmy B używa tej licencji od firmy A.

Dzierżawa zapraszana jest zawsze odpowiedzialna za usługę MFA dla użytkowników z organizacji partnerskiej, nawet jeśli organizacja partnerska ma możliwości usługi MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Konfigurowanie uwierzytelniania wieloskładnikowego dla użytkowników współpracy B2B
Aby dowiedzieć się, jak łatwo jest skonfigurować uwierzytelnianie wieloskładnikowe dla użytkowników współpracy B2B, zobacz jak to zrobić:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Środowisko MFA użytkowników B2B dla realizacji oferty
Zapoznaj się z następującą animacją, aby zobaczyć środowisko wykupu:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Resetowanie MFA dla użytkowników współpracy B2B
Obecnie administrator może wymagać, aby użytkownicy współpracy B2B mogli ponownie przeprowadzić weryfikację tylko przy użyciu następujących poleceń cmdlet programu PowerShell:

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

3. Zresetuj metodę MFA dla określonego użytkownika, aby wymagać od użytkownika współpracy B2B, aby ponownie ustawił metody potwierdzania. Przykład:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Dlaczego wykonujemy uwierzytelnianie wieloskładnikowe w dzierżawie zasobu?

W bieżącej wersji uwierzytelnianie wieloskładnikowe jest zawsze w dzierżawie zasobów z powodu przewidywalności. Załóżmy na przykład, że użytkownik contoso (Sally) jest zapraszany do firmy Fabrikam, a firma Fabrikam włączyła uwierzytelnianie wieloskładnikowe dla użytkowników B2B.

Jeśli firma Contoso ma włączone zasady MFA dla usługi APP1, ale nie APP2, wtedy, gdy w tokenie zostanie wyświetlona firma Contoso MFA, może zostać wyświetlony następujący problem:

* Dzień 1: użytkownik ma usługę MFA w firmie Contoso i ma dostęp do usługi APP1, a w firmie Fabrikam nie jest wyświetlany dodatkowy monit usługi MFA.

* Dzień 2: użytkownik uzyskał dostęp do aplikacji 2 w firmie Contoso, więc teraz podczas uzyskiwania dostępu do firmy Fabrikam muszą oni zarejestrować się na potrzeby usługi MFA.

Ten proces może być mylący i może prowadzić do porzucenia zaawansowania logowania.

Ponadto nawet jeśli firma Contoso ma funkcję MFA, nie zawsze jest to konieczne, a firma Fabrikam ufa zasadom usługi MFA firmy Contoso.

Na koniec uwierzytelnianie wieloskładnikowe dzierżawcy zasobów działa również w przypadku identyfikatorów kont MSA i społecznościowych oraz dla partnerów organizacje, dla których nie skonfigurowano usługi MFA.

W związku z tym zalecenie dotyczące usługi MFA dla użytkowników B2B ma zawsze wymagać uwierzytelniania wieloskładnikowego w przypadku zapraszania dzierżawy. To wymaganie może prowadzić do podwójnej MFA w niektórych przypadkach, ale w przypadku uzyskiwania dostępu do dzierżawy zapraszanej środowisko użytkowników końcowych jest przewidywalne: Sally musi zarejestrować się w usłudze MFA przy użyciu zapraszanej dzierżawy.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Dostęp warunkowy oparty na urządzeniach, lokalizacjach i na podstawie ryzyka dla użytkowników B2B

Gdy firma Contoso włącza zasady dostępu warunkowego opartego na urządzeniach dla danych firmowych, dostęp jest zablokowany przez urządzenia, które nie są zarządzane przez firmę Contoso i nie są zgodne z zasadami dotyczącymi urządzeń firmy Contoso.

Jeśli urządzenie użytkownika B2B nie jest zarządzane przez firmę Contoso, dostęp użytkowników B2B z organizacji partnerskich jest blokowany w kontekście, w którym te zasady są wymuszane. Firma Contoso może jednak utworzyć listę wykluczeń zawierającą określonych użytkowników partnerskich, aby wykluczyć je z zasad dostępu warunkowego opartego na urządzeniach.

#### <a name="mobile-application-management-policies-for-b2b"></a>Zasady zarządzania aplikacjami mobilnymi dla B2B

Zasady ochrony aplikacji dostępu warunkowego nie mogą być stosowane do użytkowników B2B, ponieważ zapraszana organizacja nie ma wglądu w organizację domową użytkownika B2B.

#### <a name="location-based-conditional-access-for-b2b"></a>Dostęp warunkowy oparty na lokalizacji dla B2B

Zasady dostępu warunkowego oparte na lokalizacji mogą być wymuszane dla użytkowników B2B, jeśli zapraszana organizacja może utworzyć zakres zaufanego adresu IP, który definiuje ich organizacje partnerskie.

#### <a name="risk-based-conditional-access-for-b2b"></a>Dostęp warunkowy oparty na ryzyku dla B2B

Obecnie zasady logowania oparte na ryzyku nie mogą być stosowane do użytkowników B2B, ponieważ Ocena ryzyka jest wykonywana w organizacji głównej użytkownika B2B.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi współpracy B2B w usłudze Azure AD:

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Cennik tożsamości zewnętrznych](external-identities-pricing.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
