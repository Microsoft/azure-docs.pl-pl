---
title: Rozwiązywanie problemów z protokołem TLS/SSL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, co zrobić, aby poznać różne problemy przy użyciu protokołu TLS/SSL z MSAL. Biblioteka zamierzania języka C.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80881081"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Instrukcje: Rozwiązywanie problemów z MSAL dla systemów iOS i macOS TLS/SSL

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą występować podczas korzystania z [biblioteki Microsoft Authentication Library (MSAL) dla systemów iOS i macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemy z siecią

**Błąd-1200**: "Wystąpił błąd SSL i nie można nawiązać bezpiecznego połączenia z serwerem".

Ten błąd oznacza, że połączenie nie jest bezpieczne. Występuje, gdy certyfikat jest nieprawidłowy. Aby uzyskać więcej informacji, w tym o tym, który serwer kończy sprawdzanie protokołu TLS, zapoznaj się z tematem `NSURLErrorFailingURLErrorKey` w `userInfo` słowniku obiektu Error.

Ten błąd pochodzi z biblioteki sieciowej firmy Apple. Pełna lista kodów błędów NSURL znajduje się w NSURLError. h w zestawach SDK macOS i iOS. Aby uzyskać więcej informacji na temat tego błędu, zobacz temat [ładowanie kodów błędów systemu adresów URL](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problemy z certyfikatami

Jeśli adres URL podający nieprawidłowy certyfikat nawiązuje połączenie z serwerem, który ma być używany w ramach przepływu uwierzytelniania, dobrym sposobem na zdiagnozowanie problemu jest przetestowanie adresu URL za pomocą usługi weryfikacji SSL, takiej jak [test serwera SSL](https://www.ssllabs.com/ssltest/analyze.html). Testuje serwer dla szerokiej gamy scenariuszy i przeglądarek i sprawdza wiele znanych luk w zabezpieczeniach.

Domyślnie funkcja [Security transport (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) firmy Apple obejmuje bardziej rygorystyczne zasady zabezpieczeń dla aplikacji korzystających z certyfikatów TLS/SSL. Niektóre systemy operacyjne i przeglądarki sieci Web zaczęły domyślnie wymuszać niektóre z tych zasad. Ze względów bezpieczeństwa zalecamy wyłączenie usługi ATS.

Certyfikaty używające skrótów SHA-1 mają znane luki w zabezpieczeniach. Większość nowoczesnych przeglądarek sieci Web nie zezwala na certyfikaty z użyciem skrótów SHA-1.

## <a name="captive-portals"></a>Portale wewnętrzne

Portal internetowy przedstawia użytkownikowi stronę internetową, gdy najpierw uzyskują dostęp do sieci Wi-Fi i nie udzielono jeszcze dostępu do tej sieci. Przechwytuje on ruch internetowy, dopóki użytkownik nie spełni wymagań portalu. Błędy sieci, ponieważ użytkownik nie może nawiązać połączenia z zasobami sieciowymi, dopóki użytkownik nie nawiąże połączenia z portalem.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [portalach](https://en.wikipedia.org/wiki/Captive_portal) międzyfirmowych i funkcji [Security transport (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) firmy Apple.
