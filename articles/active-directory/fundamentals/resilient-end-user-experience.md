---
title: Odporne środowisko użytkownika końcowego za pomocą Azure AD B2C | Microsoft Docs
description: Metody tworzenia odporności w środowisku użytkownika końcowego przy użyciu Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53cdbfb3ba531330c37f86233cbed990da4efaf4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258388"
---
# <a name="resilient-end-user-experience"></a>Odporne środowisko użytkownika końcowego

Środowisko użytkownika końcowego dotyczące rejestracji i logowania składa się z następujących elementów:

- Interfejsy, z którymi użytkownik współpracuje — na przykład CSS, HTML i JavaScript

- Przepływy użytkownika i utworzone przez Ciebie zasady niestandardowe — takie jak rejestrowanie, logowanie i edytowanie profilu

- Dostawcy tożsamości (dostawców tożsamości) dla aplikacji — takie jak nazwa użytkownika i hasło konta lokalnego, Outlook, Facebook i Google

![Obraz przedstawia składniki środowiska użytkownika końcowego](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Wybieranie między przepływem użytkownika a zasadami niestandardowymi  

Aby ułatwić konfigurowanie typowych zadań związanych z tożsamościami, Azure AD B2C udostępnia wbudowane, konfigurowalne [przepływy użytkowników](../../active-directory-b2c/user-flow-overview.md). Możesz również utworzyć własne [zasady niestandardowe](../../active-directory-b2c/custom-policy-overview.md), które oferują maksymalną elastyczność. Zaleca się jednak używanie zasad niestandardowych tylko w przypadku złożonych scenariuszy.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Jak podjąć decyzję między przepływem użytkownika a zasadami niestandardowymi

Wybierz wbudowane przepływy użytkownika, jeśli Twoje wymagania biznesowe mogą zostać przez nie spełnione. Ze względu na to, że jest to szeroko przetestowane przez firmę Microsoft, można zminimalizować testowanie, które jest potrzebne do sprawdzania funkcjonalności, wydajności lub skali tych przepływów na poziomie zasad. Nadal konieczne jest przetestowanie aplikacji w celu zapewnienia funkcjonalności, wydajności i skalowania.

W przypadku [wybrania zasad niestandardowych](../../active-directory-b2c/user-flow-overview.md) ze względu na wymagania biznesowe upewnij się, że przeprowadzasz testowanie na poziomie zasad dotyczące funkcjonalności, wydajności lub skalowania oprócz testowania na poziomie aplikacji.

Zapoznaj się z artykułem, który [porównuje przepływy użytkowników i zasady niestandardowe](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies) , aby ułatwić podjęcie decyzji.

## <a name="choose-multiple-idps"></a>Wybierz wiele dostawców tożsamości

W przypadku korzystania z [zewnętrznego dostawcy tożsamości](../../active-directory-b2c/technical-overview.md#external-identity-providers) , takiego jak Facebook, upewnij się, że plan rezerwowy jest ustawiony na wypadek, gdyby zewnętrzny dostawca stał się niedostępny.

### <a name="how-to-set-up-multiple-idps"></a>Jak skonfigurować wiele dostawców tożsamości

W ramach procesu rejestracji zewnętrznego dostawcy tożsamości należy dołączyć zweryfikowane zgłoszenie tożsamości, takie jak numer telefonu lub adres e-mail użytkownika. Zatwierdź zweryfikowane oświadczenia do podstawowego wystąpienia katalogu Azure AD B2C. Jeśli dostawca zewnętrzny jest niedostępny, Wróć do zweryfikowanego żądania tożsamości i powróć do numeru telefonu jako metody uwierzytelniania. Kolejną opcją jest wysłanie użytkownikowi jednorazowego kodu dostępu, aby umożliwić użytkownikowi logowanie się.

 Wykonaj następujące kroki, aby [utworzyć alternatywne ścieżki uwierzytelniania](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter):

 1. Skonfiguruj zasady rejestracji, aby zezwolić na rejestrowanie się przy użyciu konta lokalnego i zewnętrznego dostawców tożsamości.

 2. Skonfiguruj zasady profilu, aby umożliwić użytkownikom [łączenie z inną tożsamością z konta](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) po zalogowaniu się.

 3. Powiadamiaj i Zezwalaj użytkownikom na [przełączanie się do alternatywnej dostawcy tożsamości](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) podczas przestoju.

## <a name="availability-of-multi-factor-authentication"></a>Dostępność uwierzytelniania wieloskładnikowego

W przypadku korzystania z [usługi telefonicznej do uwierzytelniania wieloskładnikowego (MFA)](../../active-directory-b2c/phone-authentication-user-flows.md)należy wziąć pod uwagę alternatywnego dostawcę usług. Lokalny odpływ lub dostawca usług telefonicznych może powodować zakłócenia w swojej usłudze.

### <a name="how-to-choose-an-alternate-mfa"></a>Jak wybrać alternatywną usługę MFA  

Usługa Azure AD B2C używa wbudowanego dostawcy usługi MFA opartego na telefonie, aby dostarczać jednorazowe kody dostępu oparte na czasie (OTPs). Jest on w formie połączenia głosowego i wiadomości tekstowej do wstępnie zarejestrowanego numeru telefonu użytkownika. Następujące alternatywne metody są dostępne dla różnych scenariuszy:

W przypadku korzystania z **przepływów użytkownika** istnieją dwie metody tworzenia odporności:

- **Zmień konfigurację przepływu użytkownika**: w przypadku wykrywania przerwy w dostarczaniu OTP na telefonie należy zmienić metodę dostarczania OTP z telefonu na adres e-mail i ponownie wdrożyć przepływ użytkownika, pozostawiając niezmienione aplikacje.

![zrzut ekranu przedstawia rejestrowanie logowania](media/resilient-end-user-experiences/create-sign-in.png)

- **Zmień aplikacje**: dla każdego zadania tożsamości, takiego jak rejestracja i logowanie, zdefiniuj dwa zestawy przepływów użytkownika. Skonfiguruj pierwszy zestaw, aby używać uwierzytelniania OTP opartego na telefonie, a drugi dla uwierzytelniania OTP na podstawie poczty e-mail. Po wykryciu przerwy w dostawie OTP opartej na telefonie należy zmienić i ponownie wdrożyć aplikacje, aby przełączać się od pierwszego zestawu przepływów użytkownika do drugiego, pozostawiając przepływy użytkownika bez zmian.  

W przypadku korzystania z **zasad niestandardowych** istnieją cztery metody tworzenia odporności. Poniższa lista ma porządek złożoności i konieczne będzie ponowne wdrożenie zaktualizowanych zasad.

- **Włącz wybór użytkownika dla uwierzytelniania OTP opartego na telefonie lub na podstawie poczty e-mail**: udostępnienie obu opcji użytkownikom i umożliwienie użytkownikom wyboru jednej z opcji. Nie ma potrzeby wprowadzania zmian w zasadach lub aplikacjach.

- **Dynamicznie przełączaj między uwierzytelnianiem OTP opartym na telefonie a uwierzytelnianiem opartym na adresie e-mail**: Zbierz informacje dotyczące telefonu i wiadomości e-mail podczas tworzenia konta. Zdefiniuj zasady niestandardowe z wyprzedzeniem, aby warunkowo przełączać się w trakcie przerwy w działaniu telefonu od telefonu do poczty e-mail opartej na protokole uwierzytelniania OTP. Nie ma potrzeby wprowadzania zmian w zasadach lub aplikacjach.

- **Korzystanie z aplikacji Authenticator**: aktualizowanie zasad niestandardowych w celu korzystania z [aplikacji uwierzytelniania](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp). Jeśli normalna usługa MFA jest oparta na protokole telefonicznym lub na adresie e-mail, należy ponownie wdrożyć zasady niestandardowe, aby przełączyć się do korzystania z aplikacji Authenticator.

>[!Note]
>Użytkownicy muszą skonfigurować integrację aplikacji uwierzytelniającej podczas rejestracji.

- **Użyj pytań zabezpieczających**: Jeśli żadna z powyższych metod nie ma zastosowania, zaimplementuj pytania zabezpieczające jako kopię zapasową. Skonfiguruj pytania zabezpieczające dla użytkowników podczas dołączania lub edytowania profilu oraz przechowuj odpowiedzi w oddzielnej bazie danych innej niż katalog. Ta metoda nie spełnia wymagań usługi MFA "coś", na przykład numeru telefonu, ale oferuje pomocniczy "coś, co znasz".

## <a name="use-a-content-delivery-network"></a>Korzystanie z sieci dostarczania zawartości

Usługi Content Delivery Networks (sieci CDN) są lepiej wydajne i tańsze niż magazyny obiektów BLOB do przechowywania niestandardowego interfejsu użytkownika przepływu. Zawartość strony sieci Web jest dostarczana szybciej z geograficznie rozproszonej sieci serwerów o wysokiej dostępności.  

Okresowe testowanie dostępności sieci CDN oraz wydajności dystrybucji zawartości Dzięki kompleksowemu scenariuszowi i testom obciążenia. Jeśli planujesz nadchodzący wzrost ze względu na promocję lub ruch świąteczny, Popraw szacunki dotyczące testowania obciążenia.
  
## <a name="next-steps"></a>Następne kroki

- [Zasoby dotyczące odporności dla deweloperów Azure AD B2C](resilience-b2c.md)
  
  - [Odporne interfejsy z procesami zewnętrznymi](resilient-external-processes.md)
  - [Odporność na najlepsze rozwiązania dla deweloperów](resilience-b2c-developer-best-practices.md)
  - [Odporność dzięki monitorowaniu i analizie](resilience-with-monitoring-alerting.md)
- [Tworzenie odporności w infrastrukturze uwierzytelniania](resilience-in-infrastructure.md)
- [Zwiększanie odporności uwierzytelniania i autoryzacji w aplikacjach](resilience-app-development-overview.md)