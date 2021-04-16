---
title: Understand linked sign-on in Azure Active Directory
description: Informacje na temat logowania połączonego w Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 6ed6f6b69326157573ea043457dbc8d8a3079146
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374578"
---
# <a name="understand-linked-sign-on"></a>Opis logowania połączonego

W serii [przewodników Szybki](view-applications-portal.md) start dotyczącej zarządzania aplikacją opisano, jak używać usługi Azure AD jako dostawcy tożsamości dla aplikacji. W przewodniku Szybki start skonfigurujesz logowanie jednokrotne oparte na saml lub OIDC. Inną opcją jest **połączony .** Ten artykuł zawiera bardziej szczegółowe informacje na temat połączonej opcji.

Opcja **Połączone** umożliwia skonfigurowanie lokalizacji docelowej, gdy użytkownik wybierze aplikację w portalu [usługi Moje aplikacje](https://myapps.microsoft.com/) lub Office 365.

Niektóre typowe scenariusze, w których opcja linku jest cenna, to:
- Dodaj link do niestandardowej aplikacji internetowej, która aktualnie używa federacji, na przykład Active Directory Federation Services (AD FS).
- Dodaj linki głębokie do określonych stron programu SharePoint lub innych stron sieci Web, które mają być wyświetlane na panelach dostępu użytkownika.
- Dodaj link do aplikacji, która nie wymaga uwierzytelniania. 
 
 Opcja **Połączone** nie zapewnia funkcji logowania za pomocą poświadczeń usługi Azure AD. Można jednak nadal korzystać z niektórych innych funkcji aplikacji **dla przedsiębiorstw.** Można na przykład użyć dzienników inspekcji oraz dodać niestandardowe logo i nazwę aplikacji.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby szybko uzyskać wiedzę, należy przejść przez [serię przewodników Szybki start](view-applications-portal.md) na temat zarządzania aplikacją. W przewodniku Szybki start, w którym skonfigurowano logowanie pojedyncze, znajdziesz również **opcję Połączone.** 

Opcja **Połączone** nie zapewnia funkcji logowania za pośrednictwem usługi Azure AD. Ta opcja po prostu określa lokalizację, do Moje aplikacje [](https://myapps.microsoft.com/) będą wysyłani użytkownicy po wybraniu aplikacji lub Microsoft 365 uruchamiania aplikacji.  Ponieważ logowanie nie zapewnia funkcji logowania za pośrednictwem usługi Azure AD, dostęp warunkowy nie jest dostępny dla aplikacji skonfigurowanych przy użyciu logowania pojedynczego połączonego.

> [!IMPORTANT] 
> Istnieją pewne scenariusze, w **których opcja** logowania pojedynczego nie będzie w nawigacji dla aplikacji w **aplikacjach dla przedsiębiorstw.** 
>
> Jeśli aplikacja została zarejestrowana przy użyciu **Rejestracje aplikacji,** funkcja logowania pojedynczego jest konfigurowana do domyślnego używania OIDC OAuth. W takim przypadku opcja **Logowanie pojedyncze** nie będzie pokazywana w obszarze nawigacji w obszarze Aplikacje **dla przedsiębiorstw.** Gdy używasz **Rejestracje aplikacji** do dodawania aplikacji niestandardowej, konfigurujesz opcje w pliku manifestu. Aby dowiedzieć się więcej na temat pliku manifestu, [zobacz Azure Active Directory manifestu aplikacji](../develop/reference-app-manifest.md). Aby dowiedzieć się więcej na temat standardów logowania jednokrotnego, zobacz [Authentication and authorization using Microsoft identity platform (Uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft).](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Inne scenariusze, w których w nawigacji brakuje logowania pojedynczego, obejmują sytuacje, w których aplikacja jest hostowana w innej dzierżawie lub jeśli Konto nie ma wymaganych uprawnień (administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi).  Uprawnienia mogą również spowodować scenariusz, w którym można otworzyć logowanie **pojedyncze,** ale nie będzie można go zapisać. Aby dowiedzieć się więcej na temat ról administracyjnych usługi Azure AD, zobacz ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Konfigurowanie linku

Aby ustawić link dla aplikacji, wybierz pozycję **Połączone** na **stronie Logowanie pojedyncze.** Następnie wprowadź link i wybierz pozycję **Zapisz.** Potrzebujesz przypomnienia, gdzie znaleźć te opcje? Zapoznaj się z [serią szybki start.](view-applications-portal.md)
 
Po skonfigurowaniu aplikacji przypisz do aplikacji użytkowników i grupy. Podczas przypisywania użytkowników możesz kontrolować, kiedy aplikacja jest wyświetlana Moje aplikacje [aplikacji](https://myapps.microsoft.com/) lub Microsoft 365 uruchamiania aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](./assign-user-or-group-access-portal.md)
- [Konfigurowanie automatycznego aprowizowania kont użytkowników](../app-provisioning/configure-automatic-user-provisioning-portal.md)
