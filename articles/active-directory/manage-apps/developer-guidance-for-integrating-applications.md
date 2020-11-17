---
title: Zarejestruj swoją aplikację, aby użyć Azure Active Directory | Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące integrowania aplikacji platformy Azure z Active Directory.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: de16c947c59f5a0111b9325dbefe7daf1268fb40
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649164"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Opracowywanie aplikacji biznesowych dla Azure Active Directory
Ten przewodnik zawiera omówienie opracowywania aplikacji biznesowych (LoB) dla Azure Active Directory (AD). Zaznaczeni odbiorcy to Active Directory/Microsoft 365 Administratorzy globalni.

## <a name="overview"></a>Omówienie
Tworzenie aplikacji zintegrowanych z usługą Azure AD zapewnia użytkownikom w organizacji Logowanie jednokrotne przy użyciu Microsoft 365. Aplikacja w usłudze Azure AD zapewnia kontrolę nad zasadami uwierzytelniania aplikacji. Aby dowiedzieć się więcej o dostępie warunkowym i sposobach ochrony aplikacji za pomocą uwierzytelniania wieloskładnikowego (MFA), zobacz [Konfigurowanie reguł dostępu](../authentication/tutorial-enable-azure-mfa.md).

Zarejestruj swoją aplikację, aby użyć Azure Active Directory. Zarejestrowanie aplikacji oznacza, że deweloperzy mogą używać usługi Azure AD do uwierzytelniania użytkowników i żądania dostępu do zasobów użytkowników, takich jak poczta e-mail, kalendarz i dokumenty.

Każdy członek katalogu (nie Goście) może zarejestrować aplikację, w przeciwnym razie, jak w przypadku *tworzenia obiektu aplikacji*. Jeśli nie możesz zarejestrować aplikacji, oznacza to, że administrator globalny katalogu ograniczył tę funkcjonalność i może być konieczne skontaktowanie się z nimi w celu [uzyskania odpowiednich praw](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) do zarejestrowania aplikacji. Aby dowiedzieć się więcej o tym, jak ograniczyć użytkownikom [uprawnienia do rejestracji aplikacji delegowania w Azure Active Directory](../roles/delegate-app-roles.md#restrict-who-can-create-applications).

Zarejestrowanie aplikacji umożliwia każdemu użytkownikowi wykonywanie następujących czynności:

* Uzyskaj tożsamość dla swojej aplikacji, którą rozpoznaje usługa Azure AD
* Pobierz co najmniej jedno sekret/klucze, których aplikacja może użyć do samodzielnego uwierzytelnienia w usłudze AD
* Markę aplikacji w Azure Portal z niestandardową nazwą, logo itp.
* Zastosuj funkcje autoryzacji usługi Azure AD do swojej aplikacji, w tym:

  * Kontrola dostępu oparta na rolach (RBAC)
  * Azure Active Directory jako serwer autoryzacji oAuth (Zabezpieczanie interfejsu API uwidocznionego przez aplikację)
* Zadeklaruj wymagane uprawnienia, aby aplikacja mogła działać zgodnie z oczekiwaniami, w tym:

     - Uprawnienia aplikacji (tylko Administratorzy globalni). Na przykład: członkostwo w roli w innej aplikacji usługi Azure AD lub przynależności do roli względem zasobu platformy Azure, grupy zasobów lub subskrypcji
     - Delegowane uprawnienia (dowolny użytkownik). Na przykład: Azure AD, logowanie i przeczytanie profilu

> [!NOTE]
> Domyślnie każdy element członkowski może zarejestrować aplikację. Aby dowiedzieć się, jak ograniczyć uprawnienia do rejestrowania aplikacji dla określonych członków, zobacz [jak aplikacje są dodawane do usługi Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Oto co należy zrobić, aby pomóc deweloperom w przygotowaniu aplikacji do produkcji:

* Konfigurowanie reguł dostępu (zasady dostępu/MFA)
* Skonfiguruj aplikację tak, aby wymagała przypisania użytkownika i przypisania użytkowników
* Pomiń domyślne środowisko wyrażania zgody użytkownika

## <a name="configure-access-rules"></a>Konfigurowanie reguł dostępu
Skonfiguruj reguły dostępu dla poszczególnych aplikacji w aplikacjach SaaS. Można na przykład wymagać uwierzytelniania wieloskładnikowego lub zezwalać na dostęp tylko użytkownikom w zaufanych sieciach. Szczegóły tego elementu są dostępne w dokumencie [Configuring Access Rules](../authentication/tutorial-enable-azure-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Skonfiguruj aplikację tak, aby wymagała przypisania użytkownika i przypisania użytkowników
Domyślnie użytkownicy mogą uzyskiwać dostęp do aplikacji bez przypisywania. Jeśli jednak aplikacja uwidacznia role lub jeśli chcesz, aby aplikacja była wyświetlana w aplikacjach użytkownika, należy wymagać przypisania użytkownika.

Jeśli jesteś subskrybentem usługi Azure AD — wersja Premium lub pakietu Enterprise Mobility Suite (EMS), zdecydowanie zalecamy korzystanie z grup. Przypisywanie grup do aplikacji umożliwia delegowanie trwającego zarządzania dostępem do właściciela grupy. Możesz utworzyć grupę lub poproszenie osoby odpowiedzialnej w organizacji o utworzenie grupy przy użyciu funkcji zarządzania grupami.

[Przypisywanie użytkowników i grup do aplikacji](./assign-user-or-group-access-portal.md)  


## <a name="suppress-user-consent"></a>Pomiń wyrażanie zgody użytkownika
Domyślnie każdy użytkownik przechodzi przez środowisko zgody, aby się zalogować. W przypadku użytkowników, którzy nie znają podejmowania takich decyzji, może być konieczne przeprowadzenia zgody na korzystanie z uprawnień do aplikacji.

W przypadku aplikacji, które ufasz, można uprościć środowisko użytkownika, wyrażając zgodę na aplikację w imieniu organizacji.

Aby uzyskać więcej informacji na temat zgody użytkowników i środowiska zgody na platformie Azure, zobacz [Integrowanie aplikacji z Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="related-articles"></a>Powiązane artykuły
* [Włączanie bezpiecznego dostępu zdalnego do aplikacji lokalnych przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy.md)
* [Zarządzanie dostępem do aplikacji za pomocą usługi Azure AD](what-is-access-management.md)