---
title: Co to jest usługa Azure Active Directory B2C?
description: Dowiedz się, jak używać usługi Azure Active Directory B2C do obsługi tożsamości zewnętrznych w aplikacjach, w tym logowania do społeczności w serwisach Facebook, Google i innych dostawców tożsamości.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7126cd38a9d0f9ed0079a73e1235cb08a99bcbcf
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396539"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co to jest usługa Azure Active Directory B2C?

Azure Active Directory B2C zapewnia tożsamość biznesową dla klientów jako usługę. Klienci korzystają z własnych tożsamości konta społecznościowego, przedsiębiorstwa lub lokalnego, aby uzyskiwać dostęp do aplikacji i interfejsów API przy użyciu logowania jednokrotnego.

![Grafika informacyjna Azure AD B2C dostawców tożsamości i aplikacji podrzędnych](./media/overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) to rozwiązanie do zarządzania dostępem do tożsamości klienta (CIAM), które umożliwia obsługę milionów użytkowników i miliardów uwierzytelnień dziennie. Dział IT zajmuje się skalowaniem i bezpieczeństwem platformy uwierzytelniania, monitorowaniem i automatycznym obsługiwaniem zagrożeń, takich jak odmowa usługi, rozpylanie hasła lub ataki w postaci wymuszenia.

## <a name="custom-branded-identity-solution"></a>Rozwiązanie tożsamości o niestandardowym znakowaniu

Azure AD B2C to rozwiązanie uwierzytelniania z białymi etykietami. Możesz dostosować całe środowisko użytkownika przy użyciu marki, aby bezproblemowo przełączyć się z aplikacjami sieci Web i aplikacji mobilnych.

Dostosuj każdą stronę wyświetlaną przez Azure AD B2C, gdy użytkownicy logują się, logują się i modyfikują informacje o profilu. Dostosuj kod HTML, CSS i JavaScript w podróży użytkowników, tak aby środowisko Azure AD B2C wyglądało i wygląda tak, jak w przypadku natywnej części aplikacji.

![Dostosowane strony do rejestracji i logowania oraz obraz tła](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Dostęp z logowaniem jednokrotnym przy użyciu tożsamości dostarczonej przez użytkownika

Azure AD B2C korzysta z protokołów uwierzytelniania opartych na standardach, takich jak OpenID Connect Connect, OAuth 2,0 i SAML. Integruje się z większością nowoczesnych aplikacji i komercyjnym oprogramowaniem.

![Diagram tożsamości innych firm federowanie do Azure AD B2C](./media/overview/scenario-singlesignon.png)

Dzięki obsłużeniu jako centralny urząd uwierzytelniania dla aplikacji sieci Web, aplikacji mobilnych i interfejsów API, Azure AD B2C umożliwia tworzenie rozwiązania logowania jednokrotnego (SSO) dla każdej z nich. Scentralizowane zbieranie informacji o profilach użytkownika i preferencjach oraz przechwytywanie szczegółowej analizy dotyczącej zachowań logowania i konwersji.

## <a name="integrate-with-external-user-stores"></a>Integracja z zewnętrznymi magazynami użytkowników

Azure AD B2C udostępnia katalog, który może zawierać 100 atrybutów niestandardowych na użytkownika. Można jednak również zintegrować z systemami zewnętrznymi. Na przykład użyj Azure AD B2C na potrzeby uwierzytelniania, ale Deleguj do zewnętrznej usługi zarządzania relacjami z klientami (CRM) lub do usługi lojalnościowej klienta jako źródła prawdziwie dla danych klienta.

Inny scenariusz zewnętrznego magazynu użytkowników ma Azure AD B2C obsługiwać uwierzytelnianie aplikacji, ale integruje się z systemem zewnętrznym przechowującym profil użytkownika lub dane osobowe. Na przykład, aby spełnić wymagania dotyczące miejsca zamieszkania danych, takie jak lokalne lub lokalne zasady magazynu danych.

![Logiczny diagram Azure AD B2C komunikacji z zewnętrznym magazynem użytkowników](./media/overview/scenario-remoteprofile.png)

Azure AD B2C może ułatwić zbieranie informacji od użytkownika podczas rejestracji lub edytowania profilu, a następnie przekazanie tych danych do systemu zewnętrznego. Następnie w trakcie przyszłych operacji uwierzytelniania Azure AD B2C mogą pobrać dane z systemu zewnętrznego i w razie potrzeby uwzględnić je w ramach odpowiedzi tokenu uwierzytelniania wysyłanej do aplikacji.

## <a name="progressive-profiling"></a>Profilowanie progresywne

Opcja podróż innego użytkownika obejmuje profilowanie progresywne. Profilowanie progresywne pozwala klientom szybko wykonać swoją pierwszą transakcję przez zebranie minimalnej ilości informacji. Następnie stopniowo Zbieraj więcej danych profilu od klienta podczas przyszłych logowań.

![Wizualizacja z profilem progresywnym](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Weryfikacja tożsamości innych firm i sprawdzanie ich poprawności

Użyj Azure AD B2C, aby ułatwić weryfikację tożsamości i sprawdzanie poprawności przez zebranie danych użytkownika, a następnie przekazanie go do systemu innej firmy w celu przeprowadzenia weryfikacji, oceny zaufania i zatwierdzenia dla tworzenia konta użytkownika.

![Diagram przedstawiający przepływ użytkowników na potrzeby weryfikacji tożsamości innych firm](./media/overview/scenario-idproofing.png)

Poniżej przedstawiono niektóre czynności, które można wykonać przy użyciu Azure AD B2C jako platformy tożsamości między firmami. Poniższe sekcje tego omówienia przeprowadzą Cię przez aplikację demonstracyjną, która używa Azure AD B2C. Możesz też przejść bezpośrednio do bardziej szczegółowych [informacji technicznych dotyczących Azure AD B2C](technical-overview.md).

## <a name="example-woodgrove-groceries"></a>Przykład: Artykuły spożywcze WoodGrove

[Artykuły spożywcze WoodGrove][woodgrove] to działająca aplikacja sieci Web utworzona przez firmę Microsoft, która prezentuje kilka Azure AD B2C funkcji. W następnych sekcjach omówiono niektóre opcje uwierzytelniania udostępniane przez Azure AD B2C w witrynie sieci Web WoodGrove.

### <a name="business-overview"></a>Przegląd biznesowy

WoodGrove to sklep spożywczy w trybie online, który sprzedaje Artykuły spożywcze zarówno dla indywidualnych odbiorców, jak i klientów branżowych. Klienci biznesowi kupują Artykuły spożywcze w imieniu swojej firmy lub firm, którymi zarządzają.

### <a name="sign-in-options"></a>Opcje logowania

W przypadku artykułów spożywczych WoodGrove dostępne są kilka opcji logowania w zależności od relacji ich klientów ze sklepem:

* **Indywidualni** klienci mogą zarejestrować się lub zalogować przy użyciu poszczególnych kont, takich jak dostawca tożsamości społecznościowej lub adres e-mail oraz hasło.
* Klienci **biznesowi** mogą zarejestrować się lub zalogować przy użyciu swoich poświadczeń przedsiębiorstwa.
* **Partnerzy** i dostawcy to osoby, które dostarczają sklep spożywczy do sprzedawanych produktów. Tożsamość partnera jest zapewniana przez [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Strony logowania indywidualnego (B2C), Business (B2C) i partnera (B2B)](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Uwierzytelnianie poszczególnych klientów

Gdy klient wybierze opcję **Zaloguj się przy użyciu konta osobistego**, nastąpi przekierowanie do dostosowanej strony logowania hostowanej przez Azure AD B2C. Na poniższej ilustracji można zobaczyć, że interfejs użytkownika został dostosowany do wyglądu i działania, podobnie jak w przypadku witryny sieci Web z artykułami o spożywczych WoodGrove. Klienci WoodGrove powinni mieć nieświadome, że środowisko uwierzytelniania jest hostowane i zabezpieczone przez Azure AD B2C.

![Niestandardowa strona logowania WoodGrove hostowana przez Azure AD B2C](./media/overview/sign-in.png)

WoodGrove umożliwia klientom rejestrowanie się i logowanie za pomocą konta usługi Google, Facebook lub Microsoft jako dostawcy tożsamości. Możesz też zarejestrować się przy użyciu swojego adresu e-mail i hasła, aby utworzyć nazwę *konta lokalnego*.

Gdy klient wybierze opcję **Utwórz konto przy użyciu konta osobistego** , a następnie **zarejestruj się teraz**, zobaczysz niestandardową stronę rejestracji.

![Niestandardowa strona rejestracji WoodGrove hostowana przez Azure AD B2C](./media/overview/sign-up.png)

Po wprowadzeniu adresu e-mail i wybraniu pozycji **Wyślij kod weryfikacyjny**, Azure AD B2C wysyła do nich kod. Po wprowadzeniu kodu, wybierz opcję **Weryfikuj kod**, a następnie wprowadź inne informacje w formularzu, muszą także wyrazić zgodę na warunki korzystania z usługi.

Kliknięcie przycisku **Utwórz** powoduje, Azure AD B2C przekierować użytkownika z powrotem do witryny sieci Web z witryną spożywczą WoodGrove. Gdy przekieruje, Azure AD B2C przekazuje token uwierzytelniania OpenID Connect Connect do aplikacji sieci Web WoodGrove. Użytkownik jest teraz zalogowany i gotowy do użycia, jego nazwa wyświetlana wyświetlana w prawym górnym rogu, aby wskazać, że są zalogowani.

![Nagłówek witryny internetowej WoodGrove z informacjami o użytkowniku jest zalogowany](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Uwierzytelnianie klientów handlowych

Gdy klient wybierze jedną z opcji w obszarze **klienci biznesowa**, w witrynie sieci Web z systemem WoodGrove są dostępne różne zasady Azure AD B2C niż dla poszczególnych klientów.

Te zasady przedstawiają użytkownikowi opcję użycia poświadczeń firmowych do rejestracji i logowania. W przykładzie WoodGrove użytkownicy są monitowani o zalogowanie się przy użyciu dowolnego konta służbowego. Te zasady używają [aplikacji usługi Azure AD z wieloma dzierżawcami](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) i `/common` punktu końcowego usługi Azure ad do sfederować Azure AD B2C z dowolnym Microsoft 365 klientem na świecie.

### <a name="authenticate-partners"></a>Uwierzytelnianie partnerów

Link **Zaloguj się przy użyciu konta dostawcy** używa funkcji współpracy Azure Active Directory B2B's. Usługa Azure AD B2B jest rodziną funkcji w Azure Active Directory do zarządzania tożsamościami partnerów. Te tożsamości mogą być federacyjne z Azure Active Directory w celu uzyskania dostępu do aplikacji chronionych Azure AD B2C.

Dowiedz się więcej o usłudze Azure AD B2B w temacie [co to jest dostęp gościa w Azure Active Directory B2B?](../active-directory/b2b/what-is-b2b.md)

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz pomysł na to, co Azure AD B2C to i niektóre z nich mogą pomóc, DIG nieco bardziej szczegółowe informacje o jego funkcjach i aspektach technicznych.

> [!div class="nextstepaction"]
> [Azure AD B2C omówienie techniczne >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
