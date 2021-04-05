---
title: Włączanie automatycznej aprowizacji użytkowników dla aplikacji wielodostępnych — Azure AD
description: Przewodnik dla niezależnych dostawców oprogramowania, który umożliwia automatyczne Inicjowanie obsługi
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 7bd0fc634109beb6cc674d89f56666e7035d33ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255699"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Włącz automatyczne Inicjowanie obsługi administracyjnej użytkowników dla aplikacji wielodostępnej

Automatyczne Inicjowanie obsługi użytkowników to proces automatyzowania tworzenia, obsługi i usuwania tożsamości użytkowników w systemach docelowych, takich jak aplikacje typu "oprogramowanie jako usługa".

## <a name="why-enable-automatic-user-provisioning"></a>Dlaczego należy włączyć automatyczne Inicjowanie obsługi użytkowników?

Aplikacje, które wymagają, aby rekord użytkownika był obecny w aplikacji przed pierwszym logowaniem użytkownika, wymaga zainicjowania obsługi użytkownika. Istnieją korzyści dla Ciebie jako dostawca usług i korzyści dla klientów.

### <a name="benefits-to-you-as-the-service-provider"></a>Korzyści dla Ciebie jako dostawca usług

* Zwiększ bezpieczeństwo aplikacji, korzystając z platformy tożsamości firmy Microsoft.

* Zmniejsz rzeczywiste i postrzegane wysiłki dla klientów, aby wdrożyć aplikację.

* Zmniejsz koszty integracji z wieloma dostawcami tożsamości (dostawców tożsamości) w celu automatycznego aprowizacji użytkowników przy użyciu systemu na potrzeby inicjowania obsługi administracyjnej między domenami (standard scim).

* Zmniejszenie kosztów pomocy technicznej dzięki udostępnieniu bogatych dzienników ułatwiających klientom Rozwiązywanie problemów z inicjowaniem obsługi użytkowników.

* Zwiększ widoczność aplikacji w [galerii aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps).

* Uzyskaj priorytetową listę na stronie samouczków aplikacji.

### <a name="benefits-to-your-customers"></a>Korzyści dla klientów

* Zwiększ bezpieczeństwo, automatycznie usuwając dostęp do aplikacji dla użytkowników, którzy zmienią role lub opuszczają organizację do swojej aplikacji.

* Uprość zarządzanie użytkownikami aplikacji, unikając błędu ludzkiego i powtarzającej się pracy związanej z ręczną obsługą administracyjną.

* Zmniejsz koszty hostingu i konserwowania rozwiązań do aprowizacji opracowane przez niestandardowo.

## <a name="choose-a-provisioning-method"></a>Wybór metody aprowizacji

Usługa Azure AD udostępnia kilka ścieżek integracji, które umożliwiają automatyczne Inicjowanie obsługi użytkowników dla aplikacji.

* [Usługa Azure AD Provisioning](../app-provisioning/user-provisioning.md) zarządza obsługą administracyjną i anulowaniem aprowizacji użytkowników z usługi Azure AD do aplikacji (aprowizacji wychodzące) i z aplikacji do usługi Azure AD (Udostępnianie przychodzące). Usługa nawiązuje połączenie z systemem dla punktów końcowych interfejsu API zarządzania tożsamościami w różnych domenach (standard scim) udostępnianych przez aplikację.

* W przypadku korzystania z [Microsoft Graph](/graph/)aplikacja zarządza przychodzącą i wychodzącą aprowizacji użytkowników i grup z usługi Azure AD do aplikacji przez przeszukiwanie interfejsu API Microsoft Graph.

* Inicjowanie obsługi użytkowników (just in Time) w SAML (JIT) może być włączone, jeśli aplikacja używa protokołu SAML dla Federacji. Używa on informacji o oświadczeniach wysyłanych w tokenie SAML do udostępniania użytkownikom.

Aby ułatwić określenie opcji integracji, która ma być używana dla aplikacji, zapoznaj się z tabelą porównawczą wysokiego poziomu, a następnie zapoznaj się z bardziej szczegółowymi informacjami na temat każdej z nich.

| Możliwości włączone lub ulepszone przez automatyczne Inicjowanie obsługi| Usługa Azure AD Provisioning (standard scim 2,0)| Interfejs API Microsoft Graph (OData v 4.0)| PROTOKÓŁ SAML JIT |
|---|---|---|---|
| Zarządzanie użytkownikami i grupami w usłudze Azure AD| √| √| Tylko użytkownik |
| Zarządzanie użytkownikami i grupami synchronizowanymi z poziomu lokalnego Active Directory| √*| √*| Tylko użytkownik * |
| Dostęp do danych poza użytkownikami i grupami podczas aprowizacji dostępu do Microsoft 365 danych (zespoły, SharePoint, Poczta E-mail, kalendarz, dokumenty itp.)| X +| √| X |
| Tworzenie, odczytywanie i aktualizowanie użytkowników na podstawie reguł firmy| √| √| √ |
| Usuwanie użytkowników na podstawie reguł firmy| √| √| X |
| Zarządzanie automatyczną obsługą użytkowników dla wszystkich aplikacji z Azure Portal| √| X| √ |
| Obsługa wielu dostawców tożsamości| √| X| √ |
| Obsługa kont gościa (B2B)| √| √| √ |
| Obsługa kont nienależących do przedsiębiorstwa (B2C)| X| √| √ |

<sup>*</sup> — Konfiguracja Azure AD Connect jest wymagana do synchronizowania użytkowników z usługi AD z usługą Azure AD.  
<sup>+</sup >— Korzystanie z programu Standard scim do aprowizacji nie wyklucza integracji aplikacji z Microsoft Graph do innych celów.

## <a name="azure-ad-provisioning-service-scim"></a>Usługa Azure AD Provisioning (standard scim)

Usługi Azure AD Provisioning wykorzystują [Standard scim](https://aka.ms/SCIMOverview), branżowe standardy do aprowizacji obsługiwane przez wielu dostawców tożsamości (dostawców tożsamości), a także aplikacje (np. zapasy, usługa g Suite, Dropbox). Zalecamy użycie usługi Azure AD Provisioning w celu obsługi dostawców tożsamości oprócz usługi Azure AD, ponieważ wszelkie dostawcy tożsamości zgodne z standard scim mogą łączyć się z punktem końcowym Standard scim. Kompilowanie prostego punktu końcowego/User umożliwia obsługę administracyjną bez konieczności obsługi własnego aparatu synchronizacji. 

Aby uzyskać więcej informacji o tym, jak użytkownicy usługi Azure AD Provisioning Standard scim, zobacz: 

* [Dowiedz się więcej na temat standardu Standard scim](https://aka.ms/SCIMOverview)

* [Używanie systemu do zarządzania tożsamościami między domenami (standard scim) w celu automatycznego aprowizacji użytkowników i grup z Azure Active Directory do aplikacji](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Opis implementacji usługi Azure AD Standard scim](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph do aprowizacji

W przypadku korzystania z Microsoft Graph do inicjowania obsługi administracyjnej masz dostęp do wszystkich bogatych danych użytkownika dostępnych w grafie. Oprócz szczegółów dotyczących użytkowników i grup można także pobrać dodatkowe informacje, takie jak role użytkownika, Menedżer i raporty bezpośrednie, należące do Ciebie i zarejestrowane urządzenia oraz setki innych danych dostępnych w [Microsoft Graph](/graph/api/overview). 

Ponad 15 000 000 organizacji i 90% listy Fortune 500 firmy korzystają z usługi Azure AD podczas subskrybowania usług w chmurze firmy Microsoft, takich jak Microsoft 365, Microsoft Azure lub Enterprise Mobility Suite. Za pomocą Microsoft Graph można zintegrować swoją aplikację z administracyjnymi przepływami pracy, takimi jak dołączanie pracownika (i zakończenie), konserwacja profilu i wiele więcej. 

Dowiedz się więcej o korzystaniu z Microsoft Graph do aprowizacji:

* [Strona główna Microsoft Graph](https://developer.microsoft.com/graph)

* [Omówienie programu Microsoft Graph](/graph/overview)

* [Omówienie uwierzytelniania Microsoft Graph](/graph/auth/)

* [Wprowadzenie do Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Korzystanie z protokołu SAML JIT do aprowizacji

Jeśli chcesz udostępnić użytkownikom tylko po raz pierwszy, zaloguj się do aplikacji i nie musisz automatycznie cofać aprowizacji użytkowników, jest to opcja. Aplikacja musi obsługiwać SAML 2,0 jako protokół federacyjny, aby można było używać protokołu SAML JIT.

Protokół SAML JIT używa informacji o oświadczeniach w tokenie SAML do tworzenia i aktualizowania informacji o użytkowniku w aplikacji. Klienci mogą skonfigurować te wymagane oświadczenia w aplikacji usługi Azure AD zgodnie z potrzebami. Czasami Inicjowanie obsługi JIT musi być włączone ze strony aplikacji, aby klient mógł używać tej funkcji. Protokół SAML JIT jest przydatny do tworzenia i aktualizowania użytkowników, ale nie może usuwać ani dezaktywować użytkowników w aplikacji.

## <a name="next-steps"></a>Następne kroki

* [Włącz logowanie jednokrotne dla aplikacji](../develop/v2-howto-app-gallery-listing.md)

* [Prześlij swoją listę aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) i partnera do firmy Microsoft, aby utworzyć dokumentację witryny firmy Microsoft.

* [Dołącz do Microsoft Partner Network (bezpłatnie) i Utwórz plan na rynku](https://partner.microsoft.com/explore/commercial).
