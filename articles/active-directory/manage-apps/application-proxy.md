---
title: Dostęp zdalny do aplikacji lokalnych — serwer proxy aplikacji usługi Azure AD platformy Azure
description: Serwer proxy aplikacji Azure Active Directory zapewnia bezpieczny dostęp zdalny do lokalnych aplikacji sieci Web. Po jednokrotnym zalogowaniu się do usługi Azure AD użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze i lokalnych przy użyciu zewnętrznego adresu URL lub wewnętrznego portalu aplikacji. Na przykład serwer proxy aplikacji może zapewnić dostęp zdalny i logowanie jednokrotne do aplikacji Pulpit zdalny, SharePoint, Teams, Tableau, Qlik i LOB.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 668976e9777b35e514746070f0f4039a12b4e02b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651723"
---
# <a name="remote-access-to-on-premises-applications-through-azure-ad-application-proxy"></a>Zdalny dostęp do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji usługi Azure AD

Serwer proxy aplikacji Azure Active Directory zapewnia bezpieczny dostęp zdalny do lokalnych aplikacji sieci Web. Po jednokrotnym zalogowaniu się do usługi Azure AD użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze i lokalnych przy użyciu zewnętrznego adresu URL lub wewnętrznego portalu aplikacji. Na przykład serwer proxy aplikacji może zapewnić dostęp zdalny i logowanie jednokrotne do aplikacji Pulpit zdalny, SharePoint, Teams, Tableau, Qlik i LOB.

Serwer proxy aplikacji usługi Azure AD charakteryzuje się następującymi cechami:

- **Prosta do użycia**. Użytkownicy mogą uzyskiwać dostęp do aplikacji lokalnych w taki sam sposób, w jaki uzyskują dostęp do Microsoft 365 i innych aplikacji SaaS zintegrowanych z usługą Azure AD. Nie trzeba zmieniać ani aktualizować aplikacji, aby móc pracować z serwerem proxy aplikacji.

- **Zabezpiecz**. Aplikacje lokalne mogą korzystać z kontroli autoryzacji platformy Azure i analizy zabezpieczeń. Na przykład aplikacje lokalne mogą korzystać z dostępu warunkowego i weryfikacji dwuetapowej. Serwer proxy aplikacji nie wymaga otwierania połączeń przychodzących przez zaporę.

- **Ekonomiczne**. W przypadku rozwiązań lokalnych zwykle wymagane jest skonfigurowanie i konserwacja stref zdemilitaryzowana (stref DMZ), serwerów brzegowych lub innych złożonych infrastruktur. Serwer proxy aplikacji działa w chmurze, co ułatwia korzystanie z programu. Aby można było korzystać z serwera proxy aplikacji, nie trzeba zmieniać infrastruktury sieciowej ani instalować dodatkowych urządzeń w środowisku lokalnym.

## <a name="what-is-application-proxy"></a>Co to jest serwer proxy aplikacji?
Serwer proxy aplikacji to funkcja usługi Azure AD, która umożliwia użytkownikom dostęp do lokalnych aplikacji sieci Web z klienta zdalnego. Serwer proxy aplikacji obejmuje zarówno usługę serwera proxy aplikacji działającą w chmurze, jak i łącznik serwera proxy aplikacji, który działa na serwerze lokalnym. Azure AD, usługa serwera proxy aplikacji i łącznik serwera proxy aplikacji współpracują ze sobą, aby bezpiecznie przekazać token logowania użytkownika z usługi Azure AD do aplikacji sieci Web.

Serwer proxy aplikacji współpracuje z:

* Aplikacje sieci Web, które używają [zintegrowanego uwierzytelniania systemu Windows](application-proxy-configure-single-sign-on-with-kcd.md) na potrzeby uwierzytelniania
* Aplikacje sieci Web korzystające z dostępu opartego na formularzach lub [nagłówkach](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers)
* Interfejsy API sieci Web, które mają zostać ujawnione w rozbudowanych aplikacjach na różnych urządzeniach
* Aplikacje hostowane za [bramą pulpit zdalny](application-proxy-integrate-with-remote-desktop-services.md)
* Rozbudowane aplikacje klienckie zintegrowane z biblioteką uwierzytelniania firmy Microsoft (MSAL)

Serwer proxy aplikacji obsługuje logowanie jednokrotne. Aby uzyskać więcej informacji na temat obsługiwanych metod, zobacz [Wybieranie metody logowania](sso-options.md#choosing-a-single-sign-on-method)jednokrotnego.

Serwer proxy aplikacji jest zalecany do zapewniania użytkownikom zdalnym dostępu do zasobów wewnętrznych. Serwer proxy aplikacji zastępuje potrzebę sieci VPN lub zwrotnego serwera proxy. Nie jest on przeznaczony dla użytkowników wewnętrznych w sieci firmowej.  Ci użytkownicy niekoniecznie korzystający z serwera proxy aplikacji mogą wprowadzać nieoczekiwane i niepożądane problemy z wydajnością.

## <a name="how-application-proxy-works"></a>Jak działa serwer proxy aplikacji

Na poniższym diagramie pokazano, w jaki sposób usługa Azure AD i serwer proxy aplikacji współpracują ze sobą w celu zapewnienia logowania jednokrotnego do aplikacji lokalnych.

![Diagram serwera proxy aplikacji AzureAD](./media/application-proxy/azureappproxxy.png)

1. Gdy użytkownik uzyska dostęp do aplikacji za pomocą punktu końcowego, zostaje on skierowany do strony logowania usługi Azure AD.
2. Po pomyślnym zalogowaniu usługa Azure AD wysyła token do urządzenia klienckiego użytkownika.
3. Klient wysyła token do usługi serwera proxy aplikacji, która pobiera główną nazwę użytkownika (UPN) i główną nazwę zabezpieczeń (SPN) z tokenu. Serwer proxy aplikacji wysyła żądanie do łącznika serwera proxy aplikacji.
4. Jeśli skonfigurowano Logowanie jednokrotne, łącznik wykonuje dodatkowe wymagane uwierzytelnienie w imieniu użytkownika.
5. Łącznik wysyła żądanie do aplikacji lokalnej.
6. Odpowiedź jest wysyłana przez łącznik i usługę serwera proxy aplikacji do użytkownika.

> [!NOTE]
> Podobnie jak większość agentów hybrydowych usługi Azure AD, łącznik serwera proxy aplikacji nie wymaga otwierania połączeń przychodzących przez zaporę. Ruch użytkownika w kroku 3 kończy działanie w usłudze serwera proxy aplikacji (w usłudze Azure AD). Łącznik serwera proxy aplikacji (lokalny) jest odpowiedzialny za resztę komunikacji.
>


| Składnik | Opis |
| --------- | ----------- |
| Punkt końcowy  | Punkt końcowy jest adresem URL lub [portalem użytkowników końcowych](end-user-experiences.md). Użytkownicy mogą uzyskiwać dostęp do aplikacji poza siecią, uzyskując dostęp do zewnętrznego adresu URL. Użytkownicy w sieci mogą uzyskiwać dostęp do aplikacji za pomocą adresu URL lub portalu użytkowników końcowych. Gdy użytkownicy przejdą do jednego z tych punktów końcowych, uwierzytelniają się w usłudze Azure AD, a następnie są kierowani przez łącznik do aplikacji lokalnej.|
| Azure AD | Usługa Azure AD wykonuje uwierzytelnianie przy użyciu katalogu dzierżawy przechowywanego w chmurze. |
| Usługa serwera proxy aplikacji | Ta usługa serwera proxy aplikacji działa w chmurze w ramach usługi Azure AD. Przekazuje on token logowania od użytkownika do łącznika serwera proxy aplikacji. Serwer proxy aplikacji przekazuje wszystkie dostępne nagłówki w żądaniu i ustawia nagłówki zgodnie z protokołem na adres IP klienta. Jeśli żądanie przychodzące do serwera proxy ma już ten nagłówek, adres IP klienta zostanie dodany na końcu listy rozdzielanej przecinkami, która jest wartością nagłówka.|
| Łącznik serwera proxy aplikacji | Łącznik jest lekkim agentem działającym na serwerze z systemem Windows w sieci. Łącznik zarządza komunikacją między usługą serwera proxy aplikacji w chmurze a aplikacją lokalną. Łącznik używa tylko połączeń wychodzących, więc nie trzeba otwierać żadnych portów przychodzących ani umieszczać żadnych elementów w strefie DMZ. Łączniki są bezstanowe i pobierają informacje z chmury w razie potrzeby. Aby uzyskać więcej informacji na temat łączników, takich jak równoważenie obciążenia i uwierzytelnianie, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory działa lokalnie, aby przeprowadzić uwierzytelnianie dla kont domeny. Po skonfigurowaniu logowania jednokrotnego łącznik komunikuje się z usługą AD, aby wykonać wymagane dodatkowe uwierzytelnianie.
| Aplikacja lokalna | Na koniec użytkownik może uzyskać dostęp do aplikacji lokalnej.

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć korzystanie z serwera proxy aplikacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego za pośrednictwem serwera proxy aplikacji](application-proxy-add-on-premises-application.md).