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
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164934"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrowanie aplikacji do biblioteki uwierzytelniania firmy Microsoft (MSAL)

Zarówno Biblioteka uwierzytelniania firmy Microsoft (MSAL), jak i Biblioteka Azure AD Authentication Library (ADAL) służą do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z usługą Azure AD for Developers platform (v 1.0) do uwierzytelniania tożsamości usługi Azure AD (konta służbowe) przez żądanie tokenów przy użyciu biblioteki Azure AD Authentication Library (ADAL). Korzystanie z MSAL:

- Można uwierzytelnić szerszy zbiór tożsamości firmy Microsoft (tożsamości usługi Azure AD i konta Microsoft, a także konta społecznościowe i lokalne za pośrednictwem Azure AD B2C), ponieważ korzysta on z punktu końcowego platformy tożsamości firmy Microsoft.
- Użytkownicy będą korzystać z najlepszego środowiska logowania jednokrotnego.
- Aplikacja może włączyć przyrostową zgodę i ułatwić dostęp warunkowy.
- Korzystasz z innowacji.

**MSAL jest teraz zalecaną biblioteką uwierzytelniania do użycia z platformą tożsamości firmy Microsoft**. Nie będą implementowane żadne nowe funkcje w bibliotece ADAL. Wysiłki są skoncentrowane na ulepszaniu MSAL.

W poniższych artykułach opisano różnice między bibliotekami MSAL i ADAL i ułatwiają migrację do MSAL:
- [Migrowanie do platformy MSAL.NET](msal-net-migration.md)
- [Migrowanie do biblioteki MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrowanie do biblioteki MSAL.Android](migrate-android-adal-msal.md)
- [Migrowanie do biblioteki MSAL.iOS/systemu macOS](migrate-objc-adal-msal.md)
- [Migrowanie do biblioteki MSAL dla języka Python](migrate-python-adal-msal.md)
- [Migrowanie do biblioteki MSAL dla języka Java](migrate-adal-msal-java.md)
- [Migrowanie aplikacji platformy Xamarin do biblioteki MSAL.NET przy użyciu brokerów](msal-net-migration-ios-broker.md)
