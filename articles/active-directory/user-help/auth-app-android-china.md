---
title: Microsoft Authenticator dostępność i ograniczenia dotyczące systemu Android w Chinach | Microsoft Docs
description: Dowiedz się, jak uzyskać dostęp do Microsoft Authenticator aplikacji w Chinach
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/27/2021
ms.author: curtand
ms.openlocfilehash: d499417414e560bdaa3fcd0a12255946aca413d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054843"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator dla systemu Android w chmurze publicznej w Chinach

Aplikacja Microsoft Authenticator dla systemu Android jest dostępna do pobrania w Chinach. Sklep Google Play nie jest dostępna w Chinach, więc aplikacja musi zostać pobrana z innych chińskich rynków aplikacji. Aplikacja Microsoft Authenticator dla systemu Android jest obecnie dostępna w następujących sklepach w Chinach:

- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Magazyn Samsung Galaxy](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

Najbardziej aktualna kompilacja aplikacji znajduje się w Sklep Google Play, ale aktualizujemy aplikację we wszystkich innych sklepach z aplikacjami tak szybko, jak to możliwe. Ponieważ w żadnym sklepie z aplikacjami nie ma wdrożonego niestandardowego pakietu aplikacji dla systemu Android (APK), aplikacja może być bezproblemowo aktualizowana z jednej z następujących lokalizacji:

- Magazyn, z którego został pobrany
- Sklep Google Play, jeśli użytkownik przecina regiony

## <a name="limitations"></a>Ograniczenia

W aplikacji Microsoft Authenticator dla systemu Android jest wykorzystywany system obsługi komunikatów w chmurze firmy Google Firebase i Usługi Google Play do odbierania powiadomień wypychanych. Ponieważ żadna usługa nie jest dostępna w Chinach, istnieją pewne ograniczenia dotyczące funkcjonalności aplikacji:

- Rejestracja aplikacji Authenticator jako metody uwierzytelniania wieloskładnikowego (MFA) przy użyciu powiadomień wypychanych nie działa.

- Nie można skonfigurować [logowania do telefonu](../authentication/howto-authentication-sms-signin.md) . Użytkownik musi skonfigurować aplikację jako metodę MFA przy użyciu powiadomień wypychanych, które obecnie nie działają.

Jeśli użytkownik wcześniej zarządzał konfigurowaniem logowania za pomocą telefonu lub uwierzytelniania wieloskładnikowego przy użyciu aplikacji, może przeprowadzić ręczne sprawdzanie żądań powiadomień w aplikacji i używać ich do weryfikacji tożsamości.

## <a name="multi-factor-authentication-workaround"></a>Obejście usługi uwierzytelnianie wieloskładnikowe

Zamiast używać powiadomień wypychanych do uwierzytelniania wieloskładnikowego, użytkownicy mogą [konfigurować swoją aplikację uwierzytelniania, aby otrzymywać kody weryfikacyjne](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes) na urządzeniu, które mogą być używane przez usługę MFA do weryfikacji tożsamości. Te kody weryfikacyjne są ważne przez 30 sekund i mogą być używane, Administratorzy muszą umożliwić swoim dzierżawcom przeprowadzenie weryfikacji przy użyciu kodów weryfikacyjnych opartych na czasie One-Time hasła (TOTP).

## <a name="availability"></a>Dostępność

Funkcja Microsoft Authenticator | Dostępność w Chinach
------------------------------- | ---------------------
Rejestracja w usłudze MFA przy użyciu powiadomień wypychanych | Nie
Istniejące wcześniej konto usługi MFA weryfikujące tożsamość przy użyciu powiadomień wypychanych | Nie
Wcześniej istniejące konto usługi MFA wykonujące ręczne sprawdzanie powiadomień | Tak
Rejestracja/uwierzytelnianie usługi MFA przy użyciu tylko kodów TOTP/weryfikacyjnych | Tak
Rejestracja przy użyciu telefonu | Nie
Istniejące logowanie za pomocą telefonu przy użyciu powiadomień wypychanych | Nie
Istniejąca Weryfikacja logowania za pomocą telefonu przez wykonywanie ręcznego sprawdzania żądań uwierzytelniania | Tak
Obsługa aplikacji uwierzytelniania dla osobistych kont Microsoft | Nie

## <a name="next-steps"></a>Następne kroki

- [Pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md)
