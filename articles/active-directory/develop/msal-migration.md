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
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ac9264ea8d6cc71d19d2c9bbd23b2123bdf1f924
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88224360"
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
Odp. Tak. Od 30 czerwca 2020 nie będziemy już dodawać nowych funkcji do biblioteki ADAL. Będziemy kontynuować dodawanie krytycznych poprawek zabezpieczeń do biblioteki ADAL do 30 czerwca 2022. Po tej dacie aplikacje korzystające z biblioteki ADAL będą nadal działać, ale zalecamy uaktualnienie do MSAL w celu skorzystania z najnowszych funkcji i zapewnienia bezpieczeństwa.

__P: Czy moje istniejące aplikacje ADAL przestaną działać?__  
Odpowiedź: nie. Twoje istniejące aplikacje będą nadal działać bez żadnych modyfikacji. Jeśli planujesz przekroczenie 30 czerwca 2022, należy rozważyć zaktualizowanie aplikacji do MSAL, aby zapewnić ich bezpieczeństwo, ale migracja do MSAL nie jest wymagana do zachowania istniejących funkcji.

__P: Jak mogę wiedzieć, które aplikacje używają biblioteki ADAL?__  
Odp.: Jeśli masz kod źródłowy dla aplikacji, możesz odwoływać się do powyższych przewodników migracji, aby pomóc w ustaleniu, której biblioteki używają aplikacja i jak przeprowadzić migrację do MSAL. Jeśli masz partnera niezależnego dostawcy oprogramowania, zalecamy skontaktowanie się z nimi bezpośrednio, aby zrozumieć swoją drogę migracji do MSAL.

__P: Dlaczego należy zainwestować w przechodzenie do MSAL?__  
Odp.: MSAL zawiera nowe funkcje, które nie znajdują się w bibliotece ADAL, łącznie z zarządzeniem przyrostowym, logowaniem jednokrotnym i zarządzaniem pamięci Ponadto, w przeciwieństwie do biblioteki ADAL, MSAL będzie nadal otrzymywać poprawki zabezpieczeń poza 30 czerwca 2022. [Dowiedz się więcej](msal-overview.md).

__P: czy firma Microsoft aktualizuje własne aplikacje do MSAL?__  
Tak. Firma Microsoft jest w trakcie migrowania swoich aplikacji do MSAL przez ostateczny termin końca pomocy technicznej, dzięki czemu mogą oni korzystać z bieżących ulepszeń zabezpieczeń i funkcji MSAL.

__P: czy zostanie wydane narzędzie, które pomoże mi przenieść moje aplikacje z biblioteki ADAL do MSAL?__  
Odpowiedź: nie. Różnice między bibliotekami wymagały przydzielenia zasobów do programowania i konserwacji narzędzia, które w przeciwnym razie byłyby poświęcone ulepszaniu MSAL. Jednak udostępniamy poprzedni zestaw przewodników migracji, który ułatwia wprowadzanie wymaganych zmian w aplikacji.

__P: jak działa MSAL z AD FS?__  
Odp.: MSAL.NET obsługuje pewne scenariusze do uwierzytelniania w odniesieniu do AD FS 2019. Jeśli aplikacja musi uzyskać tokeny bezpośrednio z wcześniejszej wersji AD FS, należy pozostawać w bibliotece ADAL. [Dowiedz się więcej](msal-net-adfs-support.md).

__P: Jak mogę uzyskać pomoc w migrowaniu mojej aplikacji?__  
Odp.: Zobacz sekcję [wskazówki dotyczące migracji](#migration-guidance) w tym artykule. Jeśli po przeczytaniu przewodnika dotyczącego platformy aplikacji masz dodatkowe pytania, możesz opublikować Stack Overflow ze znacznikiem `[adal-deprecation]` lub otworzyć problem w repozytorium GitHub biblioteki. Aby uzyskać linki do repozytorium każdej biblioteki, zobacz sekcję [Języki i struktury](msal-overview.md#languages-and-frameworks) w artykule Omówienie MSAL.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie aplikacji tak, aby korzystały z biblioteki uwierzytelniania firmy Microsoft i interfejsu API Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Omówienie platformy tożsamości firmy Microsoft](v2-overview.md)
- [Przejrzyj nasze przykłady kodu MSAL](sample-v2-code.md)
