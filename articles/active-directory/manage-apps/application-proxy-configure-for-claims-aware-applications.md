---
title: Aplikacje obsługujące oświadczenia — aplikacja usługi Azure AD proxy | Microsoft Docs
description: Jak publikować lokalne aplikacje ASP.NET, które akceptują oświadczenia usług ADFS dla bezpiecznego dostępu zdalnego przez użytkowników.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: b90c2d47e70a4f7595ac535d5f8ba9506087eb72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258528"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Praca z aplikacjami obsługującymi oświadczenia w serwerze proxy aplikacji
[Aplikacje obsługujące oświadczenia](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85)) wykonują przekierowanie do usługi tokenu zabezpieczającego (STS). Usługa STS żąda poświadczeń od użytkownika w programie Exchange dla tokenu, a następnie przekierowuje użytkownika do aplikacji. Istnieje kilka sposobów włączania serwera proxy aplikacji do pracy z tymi przekierowaniami. Ten artykuł służy do konfigurowania wdrożenia aplikacji obsługujących oświadczenia. 

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że usługa STS, którą przekierowuje aplikacja obsługująca oświadczenia, jest dostępna poza siecią lokalną. Usługę STS można udostępnić, ujawniając ją za pomocą serwera proxy lub zezwalając na połączenia zewnętrzne. 

## <a name="publish-your-application"></a>Publikowanie aplikacji

1. Opublikuj swoją aplikację zgodnie z instrukcjami opisanymi w artykule [publikowanie aplikacji przy użyciu serwera proxy aplikacji](application-proxy-add-on-premises-application.md).
2. Przejdź do strony aplikacji w portalu i wybierz pozycję **Logowanie jednokrotne**.
3. W przypadku wybrania **Azure Active Directory** jako **metody wstępnego uwierzytelniania** wybierz pozycję Logowanie jednokrotne **usługi Azure AD wyłączone** jako **Metoda uwierzytelniania wewnętrznego**. W przypadku wybrania opcji **przekazywania** jako **metody wstępnego uwierzytelniania** nie trzeba zmieniać żadnych elementów.

## <a name="configure-adfs"></a>Konfigurowanie usług AD FS

Usługi AD FS można skonfigurować dla aplikacji obsługujących oświadczenia na jeden z dwóch sposobów. Pierwszy z nich korzysta z domen niestandardowych. Drugi jest z usługą WS-Federation. 

### <a name="option-1-custom-domains"></a>Opcja 1: domeny niestandardowe

Jeśli wszystkie wewnętrzne adresy URL aplikacji są w pełni kwalifikowanymi nazwami domen (FQDN), można skonfigurować [niestandardowe domeny](application-proxy-configure-custom-domain.md) dla aplikacji. Użyj domen niestandardowych, aby utworzyć zewnętrzne adresy URL, które są takie same jak wewnętrzne adresy URL. Gdy zewnętrzne adresy URL są zgodne z wewnętrznymi adresami URL, przekierowania usługi STS działają niezależnie od tego, czy użytkownicy są lokalnie, czy zdalnie. 

### <a name="option-2-ws-federation"></a>Opcja 2: WS-Federation

1. Otwórz przystawkę Zarządzanie usługami AD FS.
2. Przejdź do obszaru **zaufania jednostki uzależnionej**, kliknij prawym przyciskiem myszy aplikację, którą publikujesz za pomocą serwera proxy aplikacji, a następnie wybierz polecenie **Właściwości**.  

   ![Relacje zaufania jednostki uzależnionej kliknij prawym przyciskiem myszy nazwę aplikacji — zrzut ekranu](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Na karcie **punkty końcowe** w obszarze **Typ punktu końcowego** wybierz pozycję **WS-Federation**.
4. W obszarze **zaufany adres URL** wprowadź adres URL wprowadzony w serwerze proxy aplikacji pod **zewnętrznym adresem URL** , a następnie kliknij przycisk **OK**.  

   ![Dodaj punkt końcowy — ustaw wartość zaufanego adresu URL — zrzut ekranu](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Następne kroki
* [Włącz natywne aplikacje klienckie do współpracy z aplikacjami proxy](application-proxy-configure-native-client-application.md)