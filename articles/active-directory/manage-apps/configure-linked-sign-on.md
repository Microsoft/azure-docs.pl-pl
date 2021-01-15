---
title: Informacje o połączeniu związanym z logowaniem w Azure Active Directory
description: Zapoznaj się z połączoną logowaniem w Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b5d7cd303c8478f848ee89095d040030b702cde5
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219814"
---
# <a name="understand-linked-sign-on"></a>Opis połączonego logowania

W [serii szybkiego startu](view-applications-portal.md) w zarządzaniu aplikacjami wiesz, jak używać usługi Azure AD jako dostawcy tożsamości (dostawcy tożsamości) dla aplikacji. W przewodniku szybki start opisano konfigurowanie logowania jednokrotnego opartego na protokole SAML lub OIDC. Inna opcja jest **połączona**. Ten artykuł prowadzi do bardziej szczegółowych informacji na temat opcji połączonej.

Opcja **połączona** umożliwia skonfigurowanie lokalizacji docelowej, gdy użytkownik wybierze aplikację z aplikacji [Moje aplikacje](https://myapps.microsoft.com/) lub portal Office 365 w organizacji.

Niektóre typowe scenariusze, w których opcja link jest cenna, to m.in.:
- Dodaj link do niestandardowej aplikacji sieci Web, która obecnie używa Federacji, takiej jak Active Directory Federation Services (AD FS).
- Dodaj głębokie linki do określonych stron programu SharePoint lub innych stron sieci Web, które mają być wyświetlane w panelach dostępu użytkownika.
- Dodaj link do aplikacji, która nie wymaga uwierzytelniania. 
 
 **Połączona** opcja nie zapewnia funkcji logowania za pomocą poświadczeń usługi Azure AD. Można jednak nadal używać niektórych innych funkcji **aplikacji dla przedsiębiorstw**. Można na przykład użyć dzienników inspekcji i dodać niestandardowe logo i nazwę aplikacji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby szybko uzyskać informacje, zapoznaj się z [serią szybkiego startu](view-applications-portal.md) w zarządzaniu aplikacjami. Na stronie Szybki Start, w przypadku konfigurowania logowania jednokrotnego, należy również znaleźć opcję **połączona** . 

**Połączona** opcja nie zapewnia funkcji logowania za pomocą usługi Azure AD. Opcja po prostu ustawia lokalizację, do której użytkownicy będą wysyłane po wybraniu aplikacji w [aplikacjach](https://myapps.microsoft.com/) lub uruchamianiu aplikacji Microsoft 365.  Ponieważ logowanie nie zapewnia funkcji logowania za pomocą usługi Azure AD, dostęp warunkowy nie jest dostępny dla aplikacji skonfigurowanych przy użyciu połączonego logowania jednokrotnego.

> [!IMPORTANT] 
> Istnieją sytuacje, w których opcja **logowania** jednokrotnego nie będzie w nawigacji dla aplikacji w **aplikacjach dla przedsiębiorstw**. 
>
> Jeśli aplikacja została zarejestrowana przy użyciu **rejestracje aplikacji** , funkcja logowania jednokrotnego jest skonfigurowana do używania domyślnie OIDC uwierzytelniania OAuth. W takim przypadku opcja **logowania** jednokrotnego nie będzie widoczna w obszarze nawigacji w obszarze **aplikacje dla przedsiębiorstw**. W przypadku dodawania niestandardowej aplikacji przy użyciu **rejestracje aplikacji** można skonfigurować opcje w pliku manifestu. Aby dowiedzieć się więcej na temat pliku manifestu, zobacz [Azure Active Directory manifest aplikacji](../develop/reference-app-manifest.md). Aby dowiedzieć się więcej na temat standardów rejestracji jednokrotnej, zobacz [uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Inne scenariusze, w których nie będzie można korzystać z **logowania** jednokrotnego w nawigacji, obejmują, gdy aplikacja jest hostowana w innej dzierżawie lub że Twoje konto nie ma wymaganych uprawnień (Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi). Uprawnienia mogą również prowadzić do scenariusza, w którym można otworzyć **Logowanie jednokrotne** , ale nie będzie można go zapisać. Aby dowiedzieć się więcej na temat ról administracyjnych usługi Azure AD, zobacz https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Konfiguruj łącze

Aby ustawić link do aplikacji, wybierz pozycję **połączone** na stronie **logowania** jednokrotnego. Następnie wprowadź link i wybierz pozycję **Zapisz**. Potrzebujesz przypomnienia o miejscu, w którym można znaleźć te opcje? Zapoznaj się z [seriami szybkiego startu](view-applications-portal.md).
 
Po skonfigurowaniu aplikacji Przypisz do niej użytkowników i grupy. Podczas przypisywania użytkowników można kontrolować, kiedy aplikacja jest wyświetlana w [aplikacjach](https://myapps.microsoft.com/) lub uruchamianiu aplikacji Microsoft 365.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](./assign-user-or-group-access-portal.md)
- [Konfigurowanie automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
