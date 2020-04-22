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
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8bdc7e6e3795719128a8ecfb1e8bc97c1a9a08c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759031"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Jak: Ograniczanie aplikacji usługi Azure AD do zestawu użytkowników w dzierżawie usługi Azure AD

Aplikacje zarejestrowane w dzierżawie usługi Azure Active Directory (Azure AD) są domyślnie dostępne dla wszystkich użytkowników dzierżawy, którzy uwierzytelniają się pomyślnie.

Podobnie w przypadku aplikacji [z wieloma dzierżawami](howto-convert-app-to-be-multi-tenant.md) wszyscy użytkownicy w dzierżawie usługi Azure AD, w której ta aplikacja jest aprowizowana, będą mogli uzyskać dostęp do tej aplikacji po pomyślnym uwierzytelnieniu w odpowiedniej dzierżawie.

Administratorzy i deweloperzy dzierżawy często mają wymagania, w których aplikacja musi być ograniczona do określonego zestawu użytkowników. Deweloperzy mogą osiągnąć to samo przy użyciu popularnych wzorców autoryzacji, takich jak kontrola dostępu oparta na rolach (RBAC), ale takie podejście wymaga znacznej ilości pracy nad częścią dewelopera.

Administratorzy dzierżawy i deweloperzy mogą ograniczyć aplikację do określonego zestawu użytkowników lub grup zabezpieczeń w dzierżawie przy użyciu tej wbudowanej funkcji usługi Azure AD.

## <a name="supported-app-configurations"></a>Obsługiwane konfiguracje aplikacji

Opcja ograniczenia aplikacji do określonego zestawu użytkowników lub grup zabezpieczeń w dzierżawie działa z następującymi typami aplikacji:

- Aplikacje skonfigurowane do logowania sfederowanego z uwierzytelnianiem opartym na SAML
- Aplikacje proxy aplikacji korzystające z uwierzytelniania wstępnego usługi Azure AD
- Aplikacje utworzone bezpośrednio na platformie aplikacji usługi Azure AD, które używają uwierzytelniania OAuth 2.0/OpenID Connect po zgodę użytkownika lub administratora na tę aplikację.

     > [!NOTE]
     > Ta funkcja jest dostępna tylko dla aplikacji internetowych/interfejsu API i aplikacji korporacyjnych. Aplikacje, które są zarejestrowane jako [natywny](quickstart-v1-integrate-apps-with-azure-ad.md) nie mogą być ograniczone do zestawu użytkowników lub grup zabezpieczeń w dzierżawie.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizowanie aplikacji w celu włączenia przypisywania użytkowników

Istnieją dwa sposoby tworzenia aplikacji z włączonym przypisaniem użytkownika. Jeden wymaga roli **administratora globalnego,** drugi nie.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplikacje dla przedsiębiorstw (wymaga roli administratora globalnego)

1. Przejdź do [**witryny Azure portal**](https://portal.azure.com/) i zaloguj się jako administrator **globalny**.
1. Na górnym pasku wybierz konto zalogowane. 
1. W obszarze **Katalog**wybierz dzierżawę usługi Azure AD, w której zostanie zarejestrowana aplikacja.
1. W nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**. Jeśli usługa Azure Active Directory nie jest dostępna w okienku nawigacji, wykonaj następujące kroki:

    1. Wybierz **pozycję Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.
    1. Wpisz w **usłudze Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz element **usługi Azure Active Directory** z wyniku.

1. W okienku **usługi Azure Active Directory** wybierz pozycję **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie **usługi Azure Active Directory.**
1. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

     Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj różnych filtrów u góry listy **Wszystkie aplikacje,** aby ograniczyć listę lub przewiń listę w dół, aby zlokalizować aplikację.

1. Wybierz aplikację, do której chcesz przypisać użytkownika lub grupę zabezpieczeń, z listy.
1. Na stronie **Przegląd** aplikacji wybierz **pozycję Właściwości** z menu nawigacji po lewej stronie aplikacji.
1. Znajdź ustawienie **Wymagane przypisanie użytkownika?** i ustaw je na **Tak**. Gdy ta opcja jest ustawiona na **Tak,** użytkownicy w dzierżawie muszą najpierw zostać przypisani do tej aplikacji lub nie będą mogli zalogować się do tej aplikacji.
1. Wybierz **pozycję Zapisz,** aby zapisać tę zmianę konfiguracji.

### <a name="app-registration"></a>Rejestracja aplikacji

1. Przejdź do [**witryny Azure portal**](https://portal.azure.com/).
1. Na górnym pasku wybierz konto zalogowane. 
1. W obszarze **Katalog**wybierz dzierżawę usługi Azure AD, w której zostanie zarejestrowana aplikacja.
1. W nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.
1. W okienku **usługi Azure Active Directory** wybierz **pozycję Rejestracje aplikacji** z menu nawigacji po lewej stronie usługi Azure Active **Directory.**
1. Utwórz lub wybierz aplikację, którą chcesz zarządzać. Musisz być **właścicielem** tej rejestracji aplikacji.
1. Na stronie **Przegląd** aplikacji postępuj zgodnie z **aplikacją Zarządzana w katalogu lokalnym** pod podstawowymi elementami w górnej części strony. Spowoduje to przełączenie użytkownika do _zarządzanej aplikacji enterprise_ rejestracji aplikacji.
1. Z bloku nawigacyjnego po lewej stronie wybierz pozycję **Właściwości**.
1. Znajdź ustawienie **Wymagane przypisanie użytkownika?** i ustaw je na **Tak**. Gdy ta opcja jest ustawiona na **Tak,** użytkownicy w dzierżawie muszą najpierw zostać przypisani do tej aplikacji lub nie będą mogli zalogować się do tej aplikacji.
1. Wybierz **pozycję Zapisz,** aby zapisać tę zmianę konfiguracji.

## <a name="assign-users-and-groups-to-the-app"></a>Przypisywanie użytkowników i grup do aplikacji

Po skonfigurowaniu aplikacji do włączania przypisywania użytkowników można śmiało przypisywać użytkowników i grupy do aplikacji.

1. Wybierz okienko **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.
1. U góry listy **Użytkownicy i grupy** wybierz przycisk Dodaj **użytkownika,** aby otworzyć okienko **Dodaj przypisanie.**
1. Wybierz selektor **Użytkownicy** z okienka **Dodaj przypisanie.** 

     Zostanie wyświetlona lista użytkowników i grup zabezpieczeń wraz z polem tekstowym do wyszukiwania i lokalizowania określonego użytkownika lub grupy. Ten ekran umożliwia wybranie wielu użytkowników i grup za jednym zamachem.

1. Po wybraniu użytkowników i grup naciśnij przycisk **Wybierz** na dole, aby przejść do następnej części.
1. (Opcjonalnie) Jeśli zdefiniowano role aplikacji w aplikacji, można użyć **wybierz rolę** opcji, aby przypisać wybranych użytkowników i grupy do jednej z ról aplikacji. 
1. Naciśnij przycisk **Przypisz** u dołu, aby zakończyć przypisania użytkowników i grup do aplikacji. 
1. Upewnij się, że dodawani użytkownicy i grupy są wyświetlane na zaktualizowanej liście **Użytkownicy i grupy.**

## <a name="more-information"></a>Więcej informacji

- [Jak: Dodawanie ról aplikacji w aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [Dodawanie autoryzacji przy użyciu ról aplikacji & oświadczeń ról do aplikacji sieci web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Korzystanie z grup zabezpieczeń i ról aplikacji w aplikacjach (wideo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Usługa Azure Active Directory z oświadczeń grupowych i ról aplikacji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifest aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)