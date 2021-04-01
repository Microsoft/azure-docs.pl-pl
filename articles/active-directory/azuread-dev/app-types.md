---
title: Typy aplikacji w wersji 1.0 | Azure
description: Opisuje typy aplikacji i scenariuszy obsługiwanych przez punkt końcowy Azure Active Directory v 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5ff2858dd8b91ba036c517cbff07be96a729ef8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88116448"
---
# <a name="application-types-in-v10"></a>Typy aplikacji w wersji 1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) obsługuje uwierzytelnianie dla różnych nowoczesnych architektur aplikacji, wszystkie z nich oparte na standardach branżowych OAuth 2,0 lub OpenID Connect Connect.

Na poniższym diagramie przedstawiono scenariusze i typy aplikacji oraz sposób dodawania różnych składników:

![Scenariusze i typy aplikacji](./media/authentication-scenarios/application-types-scenarios.png)

Oto pięć scenariuszy aplikacji podstawowych obsługiwanych przez usługę Azure AD:

- **[Aplikacja jednostronicowa (Spa)](single-page-application.md)**: użytkownik musi zalogować się do aplikacji jednostronicowej zabezpieczonej przez usługę Azure AD.
- **[Przeglądarka sieci Web do aplikacji sieci Web](web-app.md)**: użytkownik musi zalogować się do aplikacji sieci Web, która jest zabezpieczona przez usługę Azure AD.
- **[Natywna aplikacja do interfejsu API sieci Web](native-app.md)**: Natywna aplikacja działająca na telefonie, tablecie lub komputerze musi uwierzytelnić użytkownika w celu pobierania zasobów z internetowego interfejsu API, który jest zabezpieczony przez usługę Azure AD.
- **[Aplikacja sieci Web do interfejsu API sieci Web](web-api.md)**: aplikacja sieci Web musi pobrać zasoby z internetowego interfejsu API zabezpieczonego przez usługę Azure AD.
- **[Demon lub aplikacja serwera do interfejsu API sieci Web](service-to-service.md)**: aplikacja demona lub aplikacja serwera bez interfejsu użytkownika sieci Web musi pobierać zasoby z internetowego interfejsu API zabezpieczonego przez usługę Azure AD.

Skorzystaj z linków, aby dowiedzieć się więcej na temat poszczególnych typów aplikacji i zrozumieć scenariusze wysokiego poziomu przed rozpoczęciem pracy z kodem. Możesz również zapoznać się z różnicami, które należy znać podczas pisania konkretnej aplikacji, która współpracuje z punktem końcowym v 1.0 lub punktem końcowym v 2.0.

> [!NOTE]
> Punkt końcowy v 2.0 nie obsługuje wszystkich scenariuszy i funkcji usługi Azure AD. Aby określić, czy należy używać punktu końcowego v 2.0, przeczytaj temat [ograniczenia dotyczące wersji v 2.0](./azure-ad-endpoint-comparison.md?bc=%2fazure%2factive-directory%2fazuread-dev%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fazuread-dev%2ftoc.json).

Można opracowywać dowolne aplikacje i scenariusze opisane w tym miejscu przy użyciu różnych języków i platform. Są one obsługiwane przez kompletne przykłady kodu dostępne w przewodniku przykładów kodu: [przykłady kodu w wersji 1.0 według scenariusza](sample-v1-code.md) i [przykładów kodu v 2.0 według scenariusza](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Możesz również pobrać przykłady kodu bezpośrednio z odpowiednich [repozytoriów przykładów usługi GitHub](https://github.com/Azure-Samples?q=active-directory).

Ponadto, jeśli aplikacja wymaga określonego fragmentu lub segmentu kompleksowego scenariusza, w większości przypadków można dodać funkcje niezależnie. Na przykład jeśli masz natywną aplikację, która wywołuje interfejs API sieci Web, możesz łatwo dodać aplikację sieci Web, która również wywołuje internetowy interfejs API.

## <a name="app-registration"></a>Rejestrowanie aplikacji

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Rejestrowanie aplikacji korzystającej z punktu końcowego usługi Azure AD v 1.0

Wszystkie aplikacje, które są źródłem uwierzytelniania do usługi Azure AD, muszą być zarejestrowane w katalogu. Ten krok polega na poinformowaniu usługi Azure AD o swojej aplikacji, w tym o adresie URL, na którym się znajduje, adresie URL do wysyłania odpowiedzi po uwierzytelnieniu, identyfikatorze URI służącym do identyfikowania aplikacji i nie tylko. Te informacje są wymagane z kilku najważniejszych przyczyn:

* Usługa Azure AD musi komunikować się z aplikacją podczas obsługi logowania lub wymiany tokenów. Informacje przesyłane między usługą Azure AD a aplikacją obejmują następujące elementy:
  
  * Identyfikator **URI aplikacji** — identyfikator dla aplikacji. Ta wartość jest wysyłana do usługi Azure AD podczas uwierzytelniania, aby wskazać, której aplikacji obiekt wywołujący chce uzyskać token. Ponadto ta wartość jest uwzględniona w tokenie, aby aplikacja wiedziała, że była zamierzoną wartością docelową.
  * **Adres URL odpowiedzi** i **Identyfikator URI przekierowania** — dla interfejsu API sieci Web lub aplikacji sieci Web adres URL odpowiedzi jest lokalizacją, w której usługa Azure AD wyśle odpowiedź uwierzytelniania, łącznie z tokenem, jeśli uwierzytelnianie zakończyło się pomyślnie. W przypadku aplikacji natywnych identyfikator URI przekierowania jest unikatowym identyfikatorem, do którego usługa Azure AD będzie przekierowywać agenta użytkownika w żądaniu OAuth 2,0.
  * **Identyfikator aplikacji** — identyfikator aplikacji, która jest generowana przez usługę Azure AD po zarejestrowaniu aplikacji. Podczas żądania kodu autoryzacji lub tokenu, identyfikator aplikacji i klucz są wysyłane do usługi Azure AD podczas uwierzytelniania.
  * **Klucz** — klucz, który jest wysyłany wraz z identyfikatorem aplikacji podczas uwierzytelniania w usłudze Azure AD w celu wywołania interfejsu API sieci Web.
* Usługa Azure AD musi upewnić się, że aplikacja ma wymagane uprawnienia dostępu do danych katalogu, innych aplikacji w organizacji i tak dalej.

Aby uzyskać szczegółowe informacje, Dowiedz się, jak [zarejestrować aplikację](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplikacje z jedną dzierżawą i wieloma dzierżawcami

Inicjowanie obsługi stanie się wyraźniejsze, gdy zrozumiesz, że istnieją dwie kategorie aplikacji, które można opracować i zintegrować z usługą Azure AD:

* **Aplikacja o pojedynczej dzierżawie** — aplikacja pojedynczego dzierżawy jest przeznaczona do użytku w jednej organizacji. Są to zazwyczaj aplikacje biznesowe, które są zapisywane przez dewelopera przedsiębiorstwa. Tylko aplikacja o pojedynczej dzierżawie musi być dostępna dla użytkowników w jednym katalogu, a w związku z tym należy tylko zainicjować ją w jednym katalogu. Te aplikacje są zwykle zarejestrowane przez dewelopera w organizacji.
* **Aplikacja wielodostępna** — aplikacja wielodostępna jest przeznaczona do użytku w wielu organizacjach, A nie tylko w jednej organizacji. Są to zazwyczaj aplikacje typu SaaS (software-as-a-service) napisane przez niezależnych dostawców oprogramowania (ISV). Aplikacje z wieloma dzierżawcami muszą być obsługiwane w każdym katalogu, w którym będą używane, co wymaga zgody użytkownika lub administratora na ich zarejestrowanie. Proces wyrażania zgody rozpoczyna się po zarejestrowaniu aplikacji w katalogu i udzieleniu jej dostępu do interfejsu API programu Graph lub innego internetowego interfejsu API. Gdy użytkownik lub administrator w innej organizacji rejestruje się w celu korzystania z aplikacji, zostanie wyświetlony okno dialogowe, w którym są wyświetlane uprawnienia wymagane przez aplikację. Użytkownik lub administrator może następnie wyrazić zgodę na aplikację, która zapewnia aplikacji dostęp do danych, a wreszcie rejestruje aplikację w katalogu. Aby uzyskać więcej informacji, zobacz [Omówienie struktury zgody](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Dodatkowe zagadnienia dotyczące tworzenia aplikacji z pojedynczym dzierżawcą lub wieloma dzierżawcami

Podczas tworzenia aplikacji wielodostępnej zamiast pojedynczej aplikacji dzierżawy powstają pewne dodatkowe zagadnienia. Na przykład jeśli udostępnisz aplikację użytkownikom w wielu katalogach, potrzebujesz mechanizmu, aby określić dzierżawcę, w którym się znajdują. Aplikacja pojedynczej dzierżawy musi tylko sprawdzić w swoim własnym katalogu dla użytkownika, podczas gdy aplikacja wielodostępna musi identyfikować określonego użytkownika ze wszystkich katalogów w usłudze Azure AD. Aby wykonać to zadanie, usługa Azure AD udostępnia wspólny punkt końcowy uwierzytelniania, w którym dowolna aplikacja wielodostępna może kierować żądania logowania, zamiast punktu końcowego określonego dla dzierżawy. Ten punkt końcowy jest `https://login.microsoftonline.com/common` przeznaczony dla wszystkich katalogów w usłudze Azure AD, podczas gdy może to być punkt końcowy specyficzny dla dzierżawy `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Typowy punkt końcowy jest szczególnie istotny do uwzględnienia podczas tworzenia aplikacji, ponieważ będzie potrzebna logika niezbędna do obsługi wielu dzierżawców podczas logowania, wylogowywania i weryfikacji tokenu.

Jeśli obecnie tworzysz aplikację z jedną dzierżawą, ale chcesz ją udostępnić dla wielu organizacji, możesz łatwo wprowadzić zmiany w aplikacji i jej konfiguracji w usłudze Azure AD, aby umożliwić obsługę wielu dzierżawców. Ponadto usługa Azure AD używa tego samego klucza podpisywania dla wszystkich tokenów we wszystkich katalogach, niezależnie od tego, czy jest to uwierzytelnianie w ramach jednej dzierżawy czy aplikacji wielodostępnych.

Każdy scenariusz wymieniony w tym dokumencie zawiera podsekcję opisującą wymagania dotyczące aprowizacji. Aby uzyskać bardziej szczegółowe informacje na temat aprowizacji aplikacji w usłudze Azure AD i różnice między aplikacjami z jedną i wieloma dzierżawcami, zobacz [Integrowanie aplikacji z Azure Active Directory](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) , aby uzyskać więcej informacji. Kontynuuj odczytywanie, aby zrozumieć typowe scenariusze aplikacji w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat innych [podstaw uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD