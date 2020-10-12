---
title: Interfejs API programu Graph usługi Azure Active Directory
description: Przewodnik przegląd i szybki start dotyczący usługi Azure AD interfejs API programu Graph, który umożliwia programistyczny dostęp do usługi Azure AD za pomocą punktów końcowych interfejsu API REST.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: da99468b1582c4acab192ad3b96761172aa69580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89068664"
---
# <a name="azure-active-directory-graph-api"></a>Interfejs API programu Graph usługi Azure Active Directory

> [!IMPORTANT]
> Zdecydowanie zalecamy używanie [Microsoft Graph](https://developer.microsoft.com/graph) zamiast usługi Azure AD interfejs API programu Graph do uzyskiwania dostępu do zasobów Azure Active Directory (Azure AD). Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, w których usługa Azure AD interfejs API programu Graph nadal może być odpowiednia; Aby uzyskać więcej informacji, zapoznaj się z wpisami w blogu [Microsoft Graph lub Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) i [Migrowanie aplikacji Azure ad Graph do Microsoft Graph](/graph/migrate-azure-ad-graph-planning-checklist).

Ten artykuł ma zastosowanie do usługi Azure AD interfejs API programu Graph. Aby poznać podobne informacje dotyczące interfejsu API Microsoft Graph, zobacz [Korzystanie z interfejsu api Microsoft Graph](/graph/use-the-api).

Interfejs API usługi Azure Active Directory Graph zapewnia dostęp programistyczny do usługi Azure AD za pomocą punktów końcowych interfejsu API REST. Aplikacje mogą używać usługi Azure AD interfejs API programu Graph do wykonywania operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych i obiektach katalogu. Na przykład usługa Azure AD interfejs API programu Graph obsługuje następujące typowe operacje dla obiektu użytkownika:

* Tworzenie nowego użytkownika w katalogu
* Uzyskiwanie szczegółowych właściwości użytkownika, takich jak ich grupy
* Aktualizowanie właściwości użytkownika, takich jak lokalizacja i numer telefonu, lub zmiana hasła
* Sprawdź członkostwo w grupie użytkownika w celu uzyskania dostępu opartego na rolach
* Wyłącz konto użytkownika lub usuń je całkowicie

Ponadto można wykonywać podobne operacje na innych obiektach, takich jak grupy i aplikacje. Aby wywoływać interfejs API programu Graph usługi Azure AD w katalogu, aplikacja musi być zarejestrowana w usłudze Azure AD. Do aplikacji należy również uzyskać dostęp do usługi Azure AD interfejs API programu Graph. Ten dostęp zazwyczaj uzyskuje się za pomocą przepływu zgody użytkownika lub administratora.

Aby rozpocząć korzystanie z interfejs API programu Graph Azure Active Directory, zapoznaj się z [przewodnikiem Szybki Start dotyczącym usługi Azure ad interfejs API programu Graph](./microsoft-graph-intro.md)lub Wyświetl [dokumentację referencyjną usługi Azure AD interfejs API programu Graph](/previous-versions/azure/ad/graph/api/api-catalog).

## <a name="features"></a>Funkcje

Usługa Azure AD interfejs API programu Graph udostępnia następujące funkcje:

* **Punkty końcowe interfejsu API REST**: usługa Azure AD interfejs API programu Graph jest usługą RESTfulą obejmującą punkty końcowe, do których uzyskuje się dostęp przy użyciu standardowych żądań HTTP. Usługa Azure AD interfejs API programu Graph obsługuje typy zawartości XML lub JavaScript (JSON) dla żądań i odpowiedzi. Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API REST usługi Azure AD Graph](/previous-versions/azure/ad/graph/api/api-catalog).
* **Uwierzytelnianie za pomocą usługi Azure AD**: każde żądanie do usługi azure AD interfejs API programu Graph musi zostać uwierzytelnione przez dołączenie tokenu w sieci Web JSON (JWT) w nagłówku autoryzacji żądania. Ten token jest uzyskiwany przez utworzenie żądania punktu końcowego tokenu usługi Azure AD i podanie prawidłowych poświadczeń. Możesz użyć przepływu poświadczeń klienta OAuth 2,0 lub przepływu przydzielenia kodu autoryzacji, aby uzyskać token do wywołania grafu. Aby uzyskać więcej informacji, [OAuth 2,0 w usłudze Azure AD](/previous-versions/azure/dn645545(v=azure.100)).
* **Autoryzacja oparta na rolach (RBAC)**: grupy zabezpieczeń służą do przeprowadzania RBAC w usłudze Azure AD interfejs API programu Graph. Na przykład jeśli chcesz określić, czy użytkownik ma dostęp do określonego zasobu, aplikacja może wywołać operację [Sprawdź członkostwo w grupie (przechodnie)](/previous-versions/azure/ad/graph/api/functions-and-actions#checkMemberGroups) , która zwraca wartość PRAWDA lub FAŁSZ.
* **Zapytanie różnicowe**: zapytanie różnicowe umożliwia śledzenie zmian w katalogu między dwoma okresami bez konieczności częstego wykonywania zapytań w usłudze Azure AD interfejs API programu Graph. Ten typ żądania zwróci tylko zmiany wprowadzone między poprzednim żądaniem zapytania różnicowego a bieżącym żądaniem. Aby uzyskać więcej informacji, zobacz [zapytanie różnicowe usługi Azure AD interfejs API programu Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-differential-query).
* **Rozszerzenia katalogów**: można dodawać niestandardowe właściwości do obiektów katalogu bez konieczności zewnętrznego magazynu danych. Na przykład jeśli aplikacja wymaga właściwości identyfikatora Skype dla każdego użytkownika, można zarejestrować nową właściwość w katalogu i będzie ona dostępna do użycia dla każdego obiektu użytkownika. Aby uzyskać więcej informacji, zobacz [rozszerzenia schematu katalogu usługi Azure AD interfejs API programu Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Zabezpieczone przez zakresy uprawnień**: usługa Azure AD interfejs API programu Graph uwidacznia zakresy uprawnień, które umożliwiają bezpieczny dostęp do danych usługi Azure AD przy użyciu protokołu OAuth 2,0. Obsługuje różne typy aplikacji klienckich, w tym:
  
  * interfejsy użytkownika, które mają delegowany dostęp do danych za pośrednictwem autoryzacji z zalogowanego użytkownika (delegowany)
  * aplikacje usługi/demona, które działają w tle bez zalogowanego użytkownika i używają kontroli dostępu opartej na rolach zdefiniowanej przez aplikację
    
    Uprawnienia delegowane i aplikacje przedstawiają uprawnienia udostępniane przez usługę Azure AD interfejs API programu Graph i mogą być żądane przez aplikacje klienckie za pomocą funkcji uprawnień do rejestracji aplikacji w [Azure Portal](https://portal.azure.com). [Zakresy uprawnień interfejs API programu Graph usługi Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes) zawierają informacje o tym, co jest dostępne do użycia przez aplikację kliencką.

## <a name="scenarios"></a>Scenariusze

Usługa Azure AD interfejs API programu Graph umożliwia wykonywanie wielu scenariuszy aplikacji. Poniżej przedstawiono typowe scenariusze:

* **Aplikacja biznesowa (z pojedynczym dzierżawcą)**: w tym scenariuszu deweloper przedsiębiorstwa działa w organizacji, która ma subskrypcję pakietu Office 365. Deweloper kompiluje aplikację sieci Web, która współdziała z usługą Azure AD w celu wykonywania zadań, takich jak Przypisywanie licencji do użytkownika. To zadanie wymaga dostępu do interfejs API programu Graph usługi Azure AD, dlatego deweloper rejestruje aplikację pojedynczego dzierżawy w usłudze Azure AD i konfiguruje uprawnienia do odczytu i zapisu dla interfejs API programu Graph usługi Azure AD. Następnie aplikacja jest skonfigurowana do korzystania z własnych poświadczeń lub obecnie zalogowanego użytkownika w celu uzyskania tokenu wywołującego interfejs API programu Graph usługi Azure AD.
* **Oprogramowanie jako usługa (wiele dzierżawców)**: w tym scenariuszu niezależny dostawca oprogramowania (ISV) opracowuje hostowaną aplikację sieci Web z wieloma dzierżawcami, która udostępnia funkcje zarządzania użytkownikami dla innych organizacji korzystających z usługi Azure AD. Te funkcje wymagają dostępu do obiektów katalogu, dlatego aplikacja musi wywołać interfejs API programu Graph usługi Azure AD. Deweloper rejestruje aplikację w usłudze Azure AD, konfiguruje ją tak, aby wymagała uprawnień do odczytu i zapisu w usłudze Azure AD interfejs API programu Graph, a następnie umożliwia dostęp zewnętrzny, tak aby inne organizacje mogły wyrazić zgodę na korzystanie z aplikacji w swoim katalogu. Gdy użytkownik w innej organizacji uwierzytelnia się do aplikacji po raz pierwszy, są wyświetlane okno dialogowe zgody z uprawnieniami, które aplikacja żąda. Przyznanie zgody spowoduje nadanie aplikacji tych, do których zażądano uprawnień do usługi Azure AD interfejs API programu Graph w katalogu użytkownika. Aby uzyskać więcej informacji na temat struktury zgody, zobacz [Omówienie struktury zgody](consent-framework.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z interfejs API programu Graph Azure Active Directory, zobacz następujące tematy:

* [Przewodnik Szybki Start dotyczący usługi Azure AD interfejs API programu Graph](./microsoft-graph-intro.md)
* [Dokumentacja REST programu Graph usługi Azure AD](/previous-versions/azure/ad/graph/api/api-catalog)
