---
title: Zwiększenie odporności uwierzytelniania i autoryzacji w aplikacjach demonów, które tworzysz
titleSuffix: Microsoft identity platform
description: Wskazówki dotyczące zwiększenia odporności uwierzytelniania i autoryzacji w aplikacji demona przy użyciu platformy tożsamości firmy Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724845"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Zwiększenie odporności uwierzytelniania i autoryzacji w aplikacjach demonów, które tworzysz

Ten artykuł zawiera wskazówki dotyczące sposobu, w jaki deweloperzy mogą korzystać z platformy tożsamości firmy Microsoft i Azure Active Directory w celu zwiększenia odporności aplikacji demona. Obejmuje to procesy w tle, usługi, serwer do aplikacji serwerowych i aplikacje bez użytkowników.

![Aplikacja demona wywołująca wywołanie do tożsamości firmy Microsoft](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure

Deweloperzy tworzący aplikacje demona w Microsoft Azure mogą używać [zarządzanych tożsamości dla zasobów platformy Azure](../managed-identities-azure-resources/overview.md). Tożsamości zarządzane eliminują konieczność zarządzania kluczami tajnymi i poświadczeniami przez deweloperów. Ta funkcja poprawia odporność, unikając błędów dotyczących wygaśnięcia certyfikatów, błędów rotacji lub relacji zaufania. Ma także kilka wbudowanych funkcji przeznaczonych specjalnie do zwiększenia odporności.

Tożsamości zarządzane wykorzystują tokeny dostępu o długim okresie ważności i informacje od tożsamości firmy Microsoft w celu aktywnego pozyskiwania nowych tokenów w dużym oknie czasu przed wygaśnięciem istniejącego tokenu. Aplikacja może nadal działać podczas próby uzyskania nowego tokenu.

Tożsamości zarządzane również używają regionalnych punktów końcowych w celu zwiększenia wydajności i odporności na awarie poza regionem. Korzystanie z regionalnego punktu końcowego pomaga zachować cały ruch w obrębie obszaru geograficznego. Na przykład jeśli zasób platformy Azure znajduje się w WestUS2, cały ruch, w tym ruch wygenerowany przez firmę Microsoft, powinien pozostać w WestUS2. Eliminuje to możliwe punkty awarii przez skonsolidowanie zależności usługi.

## <a name="use-the-microsoft-authentication-library"></a>Korzystanie z biblioteki uwierzytelniania firmy Microsoft

Deweloperzy aplikacji demonów, którzy nie korzystają z tożsamości zarządzanych, mogą korzystać z [biblioteki Microsoft Authentication Library (MSAL)](../develop/msal-overview.md), która zapewnia proste wdrażanie uwierzytelniania i autoryzacji, a także automatycznie korzysta z najlepszych rozwiązań dotyczących odporności. MSAL proces udostępniania wymaganych poświadczeń klienta będzie łatwiejszy. Na przykład aplikacja nie musi implementować tworzenia i podpisywania potwierdzeń tokenów sieci Web JSON podczas korzystania z poświadczeń opartych na certyfikatach.

### <a name="use-microsoftidentityweb-for-net-developers"></a>Korzystanie z Microsoft. Identity. Web dla deweloperów platformy .NET

Deweloperzy tworzący aplikacje demona w ASP.NET Core mogą używać biblioteki [Microsoft. Identity. Web](../develop/microsoft-identity-web.md) . Ta biblioteka jest oparta na MSAL, aby ułatwić wdrażanie autoryzacji dla aplikacji ASP.NET Core. Zawiera kilka strategii [rozproszonej pamięci podręcznej tokenów](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) dla aplikacji rozproszonych, które mogą być uruchamiane w wielu regionach.

## <a name="cache-and-store-tokens"></a>Tokeny pamięci podręcznej i magazynu

Jeśli nie używasz MSAL do zaimplementowania uwierzytelniania i autoryzacji, możesz zaimplementować niektóre najlepsze rozwiązania dotyczące buforowania i przechowywania tokenów. MSAL implementuje i automatycznie stosuje te najlepsze rozwiązania.

Aplikacja uzyskuje tokeny od dostawcy tożsamości w celu autoryzowania aplikacji do wywoływania chronionych interfejsów API. Gdy aplikacja otrzymuje tokeny, odpowiedź zawierająca tokeny zawiera również właściwość "Expires \_ in" informującą aplikację o tym, jak długo pamięć podręczna i ponowne użycie, token. Należy pamiętać, że aplikacje używają właściwości "Expires \_ in", aby określić cykl życia tokenu. Aplikacja nigdy nie musi próbować zdekodować tokenu dostępu interfejsu API. Użycie buforowanego tokenu zapobiega niepotrzebnemu ruchowi między aplikacją i tożsamością firmy Microsoft. Użytkownik może pozostać zalogowany do aplikacji przez czas istnienia tego tokenu.

## <a name="properly-handle-service-responses"></a>Prawidłowo Obsługuj odpowiedzi usługi

Poza tym, gdy aplikacje powinny obsługiwać wszystkie odpowiedzi na błędy, niektóre odpowiedzi mogą mieć wpływ na odporność. Jeśli aplikacja otrzymuje kod odpowiedzi HTTP 429, zbyt wiele żądań, tożsamość firmy Microsoft ogranicza żądania. Jeśli aplikacja będzie w dalszym ciągu wykonywać zbyt wiele żądań, nadal będzie ograniczać, uniemożliwiając aplikacji otrzymywanie tokenów. Aplikacja nie powinna podejmować prób pozyskania tokenu ponownie do czasu, w sekundach, w polu odpowiedzi "Ponów po". Odebranie odpowiedzi 429 jest często oznaką, że aplikacja nie jest buforowana i wielokrotnie używa tokenów. Deweloperzy powinni sprawdzić, w jaki sposób tokeny są buforowane i ponownie używane w aplikacji.

Gdy aplikacja otrzymuje kod odpowiedzi HTTP 5xx, aplikacja nie może wprowadzać szybkiej pętli ponawiania. Gdy jest obecny, aplikacja powinna przestrzegać tej samej obsługi "ponawianie po", jak w przypadku odpowiedzi 429. Jeśli odpowiedź nie zawiera nagłówka "retry-After", zalecamy zaimplementowanie operacji wycofywania wykładniczego przy pierwszym ponowieniu próby co najmniej 5 sekund po odpowiedzi.

Gdy żądanie przetrwania aplikacji przez czas, nie należy natychmiast ponowić próby. Zaimplementuj wycofywanie wykładnicze przy pierwszym ponowieniu próby co najmniej 5 sekund po odpowiedzi.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie odporności na aplikacje, które logują użytkowników](resilience-client-app.md)
- [Tworzenie odporności w infrastrukturze zarządzania tożsamościami i dostępem](resilience-in-infrastructure.md)
- [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)