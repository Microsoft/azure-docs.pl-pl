---
title: Migrowanie do biblioteki uwierzytelniania firmy Microsoft (MSAL)
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania firmy Microsoft (MSAL) i biblioteką uwierzytelniania usługi Azure AD (ADAL) oraz jak przeprowadzić migrację do MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 52a4a7131c85231107a2a23a1916016776b219fd
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85367431"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrowanie aplikacji do biblioteki uwierzytelniania firmy Microsoft (MSAL)

Wielu deweloperów ma skompilowane i wdrożone aplikacje korzystające z biblioteki Azure Active Directory Authentication Library (ADAL). Zalecamy używanie biblioteki uwierzytelniania firmy Microsoft (MSAL) do uwierzytelniania i autoryzacji jednostek usługi Azure AD.

Przy użyciu MSAL zamiast biblioteki ADAL:

- Można uwierzytelnić szerszy zbiór tożsamości:
  - Tożsamości usługi Azure AD
  - Konta Microsoft
  - Konta społecznościowe i lokalne przy użyciu Azure AD B2C
- Użytkownicy będą korzystać z najlepszego środowiska logowania jednokrotnego.
- Twoja aplikacja może włączyć przyrostową zgodę.
- Obsługa dostępu warunkowego jest łatwiejsza.
- Korzystasz z innowacji. Ze względu na to, że wszystkie działania związane z programowaniem firmy Microsoft są teraz skoncentrowane na MSAL, nie zostaną zaimplementowane nowe funkcje w bibliotece ADAL.

**MSAL jest teraz zalecaną biblioteką uwierzytelniania do użycia z platformą tożsamości firmy Microsoft**.

## <a name="migration-guidance"></a>Wskazówki dotyczące migracji

Następujące artykuły mogą pomóc w migracji do MSAL:

- [Migrowanie do biblioteki MSAL.Android](migrate-android-adal-msal.md)
- [Migrowanie do biblioteki MSAL.iOS/systemu macOS](migrate-objc-adal-msal.md)
- [Migrowanie do platformy MSAL Java](migrate-adal-msal-java.md)
- [Migrowanie do biblioteki MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrowanie do platformy MSAL.NET](msal-net-migration.md)
- [Migrowanie do biblioteki MSAL dla języka Python](migrate-python-adal-msal.md)
- [Migrowanie aplikacji platformy Xamarin do biblioteki MSAL.NET przy użyciu brokerów](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

__P: Czy biblioteka ADAL jest przestarzała?__  
Odp. Tak. Od 30 czerwca 2020 nie będziemy już dodawać nowych funkcji do biblioteki ADAL. Będziemy kontynuować dodawanie krytycznych poprawek zabezpieczeń do biblioteki ADAL do 30 czerwca 2022.

__P: Jak mogę wiedzieć, które aplikacje używają biblioteki ADAL?__  
Odp.: Jeśli masz kod źródłowy dla aplikacji, możesz odwoływać się do powyższych przewodników migracji, aby pomóc w ustaleniu, której biblioteki używają aplikacja i jak przeprowadzić migrację do MSAL. Jeśli nie masz dostępu do kodu źródłowego aplikacji, możesz [otworzyć żądanie pomocy technicznej](developer-support-help-options.md#open-a-support-request) w celu uzyskania listy zarejestrowanych aplikacji i biblioteki używanej przez każdą aplikację.

__P: Czy moje istniejące aplikacje ADAL będą nadal działały?__  
Odp.: istniejące aplikacje będą nadal działały bez żadnych modyfikacji. Jeśli planujesz zachować te wymagania poza 30 czerwca, 2022, należy rozważyć zaktualizowanie ich do MSAL w celu zapewnienia ich bezpieczeństwa, ale Migrowanie do MSAL nie jest wymagane do zachowania istniejących funkcji.

__P: Dlaczego należy zainwestować w przechodzenie do MSAL?__  
Odp.: MSAL zawiera nowe funkcje, które nie znajdują się w bibliotece ADAL, łącznie z zarządzeniem przyrostowym, logowaniem jednokrotnym i zarządzaniem pamięci Ponadto, w przeciwieństwie do biblioteki ADAL, MSAL będzie nadal otrzymywać poprawki zabezpieczeń poza 30 czerwca 2022. [Dowiedz się więcej](msal-overview.md).

__P: czy zostanie wydane narzędzie, które pomoże mi przenieść moje aplikacje z biblioteki ADAL do MSAL?__  
Odpowiedź: nie. Różnice między bibliotekami wymagały przydzielenia zasobów do programowania i konserwacji narzędzia, które w przeciwnym razie byłyby poświęcone ulepszaniu MSAL. Jednak udostępniamy poprzedni zestaw przewodników migracji, który ułatwia wprowadzanie wymaganych zmian w aplikacji.

__P: jak działa MSAL z AD FS?__  
Odp.: MSAL.NET obsługuje pewne scenariusze do uwierzytelniania w odniesieniu do AD FS 2019. Jeśli aplikacja musi uzyskać tokeny bezpośrednio z wcześniejszej wersji AD FS, należy pozostawać w bibliotece ADAL. [Dowiedz się więcej](msal-net-adfs-support.md).

__P: Jak mogę uzyskać pomoc w migrowaniu mojej aplikacji?__  
Odp.: Zobacz sekcję [wskazówki dotyczące migracji](#migration-guidance) w tym artykule. Jeśli po przeczytaniu przewodnika dotyczącego platformy aplikacji masz dodatkowe pytania, możesz opublikować Stack Overflow ze znacznikiem `[adal-deprecation]` lub otworzyć problem w repozytorium GitHub biblioteki. Aby uzyskać linki do repozytorium każdej biblioteki, zobacz sekcję [Języki i struktury](msal-overview.md#languages-and-frameworks) w artykule Omówienie MSAL.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie aplikacji tak, aby korzystały z biblioteki uwierzytelniania firmy Microsoft i interfejsu API Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Dowiedz się więcej o platformie Identity firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Przejrzyj nasze przykłady kodu MSAL](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)
