---
title: Ograniczanie aplikacji usługi Azure AD do zestawu użytkowników | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak ograniczyć dostęp do aplikacji zarejestrowanych w usłudze Azure AD do wybranego zestawu użytkowników.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5f6ac11fc5c7bbe7a8f81e6ea89e2c582ebcf264
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178742"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Instrukcje: ograniczanie aplikacji usługi Azure AD do zestawu użytkowników w dzierżawie usługi Azure AD

Aplikacje zarejestrowane w dzierżawie usługi Azure Active Directory (Azure AD) są domyślnie dostępne dla wszystkich użytkowników dzierżawy, którzy pomyślnie uwierzytelniają się.

Podobnie, w [przypadku aplikacji](howto-convert-app-to-be-multi-tenant.md) wielodostępnej, wszyscy użytkownicy w dzierżawie usługi Azure AD, w której ta aplikacja jest obsługiwana, będą mogli uzyskiwać dostęp do tej aplikacji po pomyślnym uwierzytelnieniu w odpowiedniej dzierżawie.

Administratorzy dzierżawy i deweloperzy często mają wymagania, w przypadku których aplikacja musi być ograniczona do określonego zbioru użytkowników. Deweloperzy mogą wykonywać te same czynności, korzystając ze popularnych wzorców autoryzacji, takich jak kontrola dostępu oparta na rolach (RBAC) platformy Azure, ale takie podejście wymaga dużej ilości pracy w ramach dewelopera.

Administratorzy dzierżawy i deweloperzy mogą ograniczyć aplikację do określonego zestawu użytkowników lub grup zabezpieczeń w dzierżawie przy użyciu tej wbudowanej funkcji usługi Azure AD.

## <a name="supported-app-configurations"></a>Obsługiwane konfiguracje aplikacji

Opcja ograniczenia aplikacji do określonego zestawu użytkowników lub grup zabezpieczeń w dzierżawie współpracuje z następującymi typami aplikacji:

- Aplikacje skonfigurowane do federacyjnego logowania jednokrotnego przy użyciu uwierzytelniania opartego na protokole SAML.
- Aplikacje serwera proxy aplikacji korzystające z uwierzytelniania wstępnego usługi Azure AD.
- Aplikacje utworzone bezpośrednio na platformie aplikacji usługi Azure AD, która korzysta z uwierzytelniania OAuth 2.0/OpenID Connect, gdy użytkownik lub administrator wyraził zgodę na tę aplikację.

     > [!NOTE]
     > Ta funkcja jest dostępna tylko dla aplikacji sieci Web/internetowego interfejsu API i aplikacje dla przedsiębiorstw. Aplikacje zarejestrowane jako [natywne](./quickstart-register-app.md) nie mogą być ograniczone do zestawu użytkowników lub grup zabezpieczeń w dzierżawie.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizowanie aplikacji w celu włączenia przypisania użytkownika

Istnieją dwa sposoby tworzenia aplikacji z włączonym przypisaniem użytkownika. Jeden z nich wymaga roli **administratora globalnego** , a druga nie.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplikacje dla przedsiębiorstw (wymaga roli administratora globalnego)

1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a> jako **administrator globalny**.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Wybierz aplikację, do której chcesz przypisać użytkownika lub grupę zabezpieczeń. 
    Użyj filtrów w górnej części okna, aby wyszukać określoną aplikację.
1. Na stronie **Przegląd** aplikacji w obszarze **Zarządzanie** wybierz pozycję **Właściwości**.
1. Znajdź **wymagane przypisanie użytkownika?** i ustaw wartość **tak**. Jeśli ta opcja jest ustawiona na **tak**, użytkownicy w dzierżawie muszą najpierw zostać przypisani do tej aplikacji lub nie będą mogli zalogować się do tej aplikacji.
1. Wybierz pozycję **Zapisz**.

### <a name="app-registration"></a>Rejestracja aplikacji

1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
1. Utwórz lub wybierz aplikację, którą chcesz zarządzać. Musisz być **właścicielem** tej aplikacji.
1. Na stronie **Przegląd** aplikacji wybierz pozycję **zarządzana aplikacja w katalogu lokalnym** w sekcji **podstawowe** informacje.
1. W obszarze **Zarządzaj** wybierz pozycję **Właściwości**.
1. Znajdź **wymagane przypisanie użytkownika?** i ustaw wartość **tak**. Jeśli ta opcja jest ustawiona na **tak**, użytkownicy w dzierżawie muszą najpierw zostać przypisani do tej aplikacji lub nie będą mogli zalogować się do tej aplikacji.
1. Wybierz pozycję **Zapisz**.

## <a name="assign-users-and-groups-to-the-app"></a>Przypisywanie użytkowników i grup do aplikacji

Po skonfigurowaniu aplikacji do włączania przypisywania użytkowników można przypisywać użytkowników i grupy do aplikacji.

1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy i grupy**  >  **Dodaj użytkownika/grupę** .
1. Wybierz selektora **użytkowników** . 

     Zostanie wyświetlona lista użytkowników i grup zabezpieczeń wraz z polem tekstowym umożliwiającym wyszukiwanie i lokalizowanie określonego użytkownika lub grupy. Ten ekran umożliwia wybranie wielu użytkowników i grup w jednym przejściu.

1. Po wybraniu pozycji Użytkownicy i grupy wybierz pozycję **Wybierz**.
1. Obowiązkowe Jeśli zdefiniowano role aplikacji w aplikacji, można użyć opcji **Wybierz rolę** , aby przypisać wybranych użytkowników i grupy do jednej z ról aplikacji. 
1. Wybierz pozycję **Przypisz** , aby zakończyć przydziały użytkowników i grup do aplikacji. 
1. Upewnij się, że dodani Użytkownicy i grupy znajdują się na liście zaktualizowanych **użytkowników i grup** .

## <a name="more-information"></a>Więcej informacji

- [Instrukcje: Dodawanie ról aplikacji w aplikacji](./howto-add-app-roles-in-azure-ad-apps.md)
- [Dodawanie autoryzacji przy użyciu ról aplikacji & oświadczenia ról do aplikacji sieci Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Używanie grup zabezpieczeń i ról aplikacji w aplikacjach (wideo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, teraz z oświadczeniami grupy i rolami aplikacji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifest aplikacji usługi Azure Active Directory](./reference-app-manifest.md)
