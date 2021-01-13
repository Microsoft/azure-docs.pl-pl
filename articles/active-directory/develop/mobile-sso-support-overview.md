---
title: Obsługa logowania jednokrotnego i zasad ochrony aplikacji w aplikacjach mobilnych, które tworzysz | Azure
titleSuffix: Microsoft identity platform
description: Wyjaśnienie i omówienie tworzenia aplikacji mobilnych, które obsługują Logowanie jednokrotne i zasady ochrony aplikacji przy użyciu platformy tożsamości firmy Microsoft i integrowanie z usługą Azure Active Directory.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 4f0588667df6acb11a43e8c3469c67f65ed3cdd9
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165182"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Obsługa logowania jednokrotnego i zasad ochrony aplikacji w aplikacjach mobilnych, które tworzysz

Logowanie jednokrotne to kluczowa oferta platformy tożsamości firmy Microsoft i Azure Active Directory, która zapewnia łatwe i bezpieczne Logowanie użytkowników aplikacji. Ponadto zasady ochrony aplikacji (aplikacje) umożliwiają obsługę najważniejszych zasad zabezpieczeń, które zachowują bezpieczeństwo danych użytkownika. Razem te funkcje umożliwiają bezpieczne Logowanie użytkowników i zarządzanie danymi aplikacji.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

W tym artykule wyjaśniono, dlaczego Logowanie jednokrotne i aplikacja jest ważne i zawiera ogólne wskazówki dotyczące kompilowania aplikacji mobilnych, które obsługują te funkcje. Dotyczy to zarówno aplikacji dla telefonu, jak i tabletu. Jeśli jesteś administratorem IT, który chce wdrożyć Logowanie jednokrotne w ramach dzierżawy Azure Active Directory w organizacji, zapoznaj się z naszymi [wskazówkami dotyczącymi planowania wdrożenia logowania](../manage-apps/plan-sso-deployment.md) jednokrotnego

## <a name="about-single-sign-on-and-app-protection-policies"></a>Informacje na temat rejestracji jednokrotnej i zasad ochrony aplikacji

[Logowanie](../manage-apps/plan-sso-deployment.md) jednokrotne umożliwia użytkownikowi Logowanie jednokrotne i uzyskanie dostępu do innych aplikacji bez konieczności ponownego wprowadzania poświadczeń. Dzięki temu można łatwiej uzyskiwać dostęp do aplikacji i eliminuje konieczność zapamiętywania długich list nazw użytkowników i haseł przez użytkowników. Wdrożenie go w aplikacji ułatwia uzyskiwanie dostępu do aplikacji i korzystanie z niej.

Ponadto włączenie logowania jednokrotnego w aplikacji odblokowuje nowe mechanizmy uwierzytelniania, które mają nowoczesne uwierzytelnianie, takie jak logowania bez [hasła](../authentication/concept-authentication-passwordless.md). Nazwy użytkowników i hasła są jednym z najpopularniejszych wektorów ataków na aplikacje, a włączenie rejestracji jednokrotnej pozwala uniknąć tego ryzyka poprzez wymuszanie dostępu warunkowego lub logowania bez hasła, które zwiększają bezpieczeństwo lub polegają na bardziej bezpiecznych mechanizmach uwierzytelniania. Na koniec włączenie logowania jednokrotnego umożliwia także [Logowanie](v2-protocols-oidc.md#single-sign-out)jednokrotne. Jest to przydatne w sytuacjach, takich jak aplikacje służbowe, które będą używane na urządzeniach udostępnionych.

[Zasady ochrony aplikacji (aplikacje)](/mem/intune/apps/app-protection-policy) zapewniają, że dane organizacji pozostają bezpieczne i zawarte. Umożliwiają one firmom zarządzanie danymi i ich ochronę w ramach aplikacji oraz umożliwiają kontrolę nad tym, kto może uzyskiwać dostęp do aplikacji i jej danych. Implementacja zasad ochrony aplikacji umożliwia aplikacji Łączenie użytkowników z zasobami chronionymi przez zasady dostępu warunkowego i bezpieczne Transferowanie danych do i z innych chronionych aplikacji. Scenariusze odblokowywane przez zasady ochrony aplikacji obejmują wymaganie, aby program mógł otworzyć aplikację, kontrolować udostępnianie danych między aplikacjami i uniemożliwiać zapisywanie danych aplikacji firmowych w osobistych lokalizacjach magazynu.

## <a name="implementing-single-sign-on"></a>Implementowanie logowania jednokrotnego

Zalecamy wykonanie poniższych czynności, aby umożliwić aplikacji korzystanie z funkcji logowania jednokrotnego.

### <a name="use-the-microsoft-authentication-library-msal"></a>Korzystanie z biblioteki uwierzytelniania firmy Microsoft (MSAL)

Najlepszym wyborem do implementowania logowania jednokrotnego w aplikacji jest użycie [biblioteki uwierzytelniania firmy Microsoft (MSAL)](msal-overview.md). Za pomocą MSAL można dodać uwierzytelnianie do aplikacji z minimalnymi wywołaniami kodu i interfejsu API, uzyskać pełne funkcje [platformy tożsamości firmy Microsoft](./index.yml), a firma Microsoft może obsłużyć konserwację bezpiecznego rozwiązania do uwierzytelniania. Domyślnie MSAL dodaje obsługę logowania jednokrotnego dla aplikacji. Ponadto korzystanie z MSAL jest wymagane, jeśli planujesz również wdrożenie zasad ochrony aplikacji.

> [!NOTE]
> Można skonfigurować MSAL tak, aby korzystał z osadzonego widoku sieci Web. Uniemożliwi to Logowanie jednokrotne. Użyj zachowania domyślnego (czyli przeglądarki sieci Web systemu), aby upewnić się, że logowanie jednokrotne będzie działało.

Jeśli obecnie korzystasz z [biblioteki ADAL](../azuread-dev/active-directory-authentication-libraries.md) w aplikacji, zdecydowanie zalecamy [przeprowadzenie migracji do MSAL](msal-migration.md), ponieważ [Biblioteka ADAL jest przestarzała](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

W przypadku aplikacji dla systemu iOS mamy [Przewodnik Szybki Start](quickstart-v2-ios.md) pokazujący, jak skonfigurować logowania za pomocą usługi MSAL, a także [wskazówki dotyczące konfigurowania MSAL dla różnych scenariuszy logowania jednokrotnego](single-sign-on-macos-ios.md).

W przypadku aplikacji dla systemu Android mamy [Przewodnik Szybki Start](quickstart-v2-android.md) przedstawiający sposób konfigurowania logowania za pomocą MSAL oraz wskazówki dotyczące [sposobu włączania logowania jednokrotnego dla aplikacji w systemie Android przy użyciu MSAL](msal-android-single-sign-on.md).

### <a name="use-the-system-web-browser"></a>Korzystanie z systemowej przeglądarki sieci Web

Do uwierzytelniania interakcyjnego jest wymagana przeglądarka sieci Web. W przypadku aplikacji mobilnych, które korzystają z nowoczesnych bibliotek uwierzytelniania innych niż MSAL (inne OpenID Connect Connect lub biblioteka SAML), lub jeśli zaimplementowasz własny kod uwierzytelniania, użyj przeglądarki systemowej jako obszaru uwierzytelniania, aby włączyć logowanie jednokrotne.

Firma Google ma wskazówki dotyczące tego działania w aplikacjach systemu Android: [niestandardowe karty Chrome — Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

Firma Apple ma wskazówki dotyczące tego działania w aplikacjach systemu iOS: [uwierzytelnianie użytkownika za pomocą usługi sieci Web | Dokumentacja dla deweloperów firmy Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> [Wtyczka logowania jednokrotnego dla urządzeń firmy Apple](apple-sso-plugin.md) umożliwia logowanie jednokrotne dla aplikacji systemu iOS, które używają osadzonych widoków sieci Web na zarządzanych urządzeniach przy użyciu usługi Intune. Zalecamy MSAL i przeglądarkę systemową jako najlepszą opcję tworzenia aplikacji, które włączają Logowanie jednokrotne dla wszystkich użytkowników, ale umożliwi to Logowanie jednokrotne w niektórych scenariuszach, w których w przeciwnym razie nie będzie możliwe.

## <a name="enable-app-protection-policies"></a>Włączanie zasad ochrony aplikacji

Aby włączyć zasady ochrony aplikacji, użyj [biblioteki Microsoft Authentication Library (MSAL)](msal-overview.md). MSAL to biblioteka uwierzytelniania i autoryzacji platformy tożsamości firmy Microsoft, a zestaw SDK usługi Intune jest opracowywany do współpracy z działem IT.

Ponadto do uwierzytelniania należy użyć aplikacji brokera. Broker wymaga, aby aplikacja zapewniała informacje o aplikacji i urządzeniu w celu zapewnienia zgodności aplikacji. Użytkownicy systemu iOS będą korzystać z [aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md) i użytkownicy systemu Android będą korzystać z aplikacji Microsoft Authenticator lub [aplikacji Portal firmy](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) na potrzeby uwierzytelniania obsługiwanego przez [brokera](./msal-android-single-sign-on.md). Domyślnie MSAL korzysta z brokera jako pierwszej opcji do realizacji żądania uwierzytelnienia, dlatego przy użyciu brokera do uwierzytelniania zostanie włączona automatyczna aplikacja podczas korzystania z MSAL.

Na koniec [Dodaj do aplikacji zestaw SDK usługi Intune](/mem/intune/developer/app-sdk-get-started) , aby włączyć zasady ochrony aplikacji. Zestaw SDK dla najbardziej części jest zgodny z modelem przechwytywania i automatycznie zastosuje zasady ochrony aplikacji w celu określenia, czy akcje podejmowane przez aplikację są dozwolone, czy nie. Istnieją również interfejsy API, które można wywoływać ręcznie, aby poinformować aplikację, jeśli istnieją ograniczenia dotyczące pewnych akcji.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Planowanie Azure Active Directory wdrożenia przy użyciu logowania jednokrotnego](../manage-apps/plan-sso-deployment.md)
- [Instrukcje: Konfigurowanie logowania jednokrotnego w systemach macOS i iOS](single-sign-on-macos-ios.md)
- [Wtyczka Microsoft Enterprise SSO dla urządzeń firmy Apple (wersja zapoznawcza)](apple-sso-plugin.md)
- [Uwierzytelnianie obsługiwane przez brokera w systemie Android](./msal-android-single-sign-on.md)
- [Agenci autoryzacji i sposoby ich włączania](./msal-android-single-sign-on.md)
- [Wprowadzenie do zestawu SDK aplikacji usługi Microsoft Intune](/mem/intune/developer/app-sdk-get-started)
- [Konfigurowanie ustawień zestawu SDK aplikacji usługi Intune](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Chronione aplikacje w usłudze Microsoft Intune](/mem/intune/apps/apps-supported-intune-apps)
