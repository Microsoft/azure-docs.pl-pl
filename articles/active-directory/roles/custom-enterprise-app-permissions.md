---
title: Uprawnienia aplikacji dla ról niestandardowych w Azure Active Directory | Microsoft Docs
description: Zapodgląd uprawnień aplikacji przedsiębiorstwa dla niestandardowych ról usługi Azure AD w Azure Portal, PowerShell lub interfejs API programu Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 39d9cf9ed87c8a8f45cb2a6239292562035d31e3
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379642"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Uprawnienia aplikacji przedsiębiorstwa dla ról niestandardowych w Azure Active Directory

Ten artykuł zawiera aktualnie dostępne uprawnienia aplikacji dla przedsiębiorstw dla niestandardowych definicji ról w Azure Active Directory (Azure AD). W tym artykule znajdziesz listy uprawnień dla niektórych typowych scenariuszy i pełną listę uprawnień aplikacji dla przedsiębiorstw. Uprawnienia serwera proxy aplikacji nie są obecnie wdrożone w tej wersji.

## <a name="required-license-plan"></a>Wymagany plan licencji

Korzystanie z tej funkcji wymaga licencji na Azure AD — wersja Premium P1 dla organizacji usługi Azure AD. Aby znaleźć licencję odpowiednią do wymagań, zobacz [porównanie ogólnodostępnych funkcji w wersji bezpłatnej, podstawowej i premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Uprawnienia aplikacji dla przedsiębiorstw

Więcej informacji o sposobach korzystania z tych uprawnień znajduje się w temacie [Przypisywanie ról niestandardowych do zarządzania aplikacjami dla przedsiębiorstw](custom-enterprise-apps.md) .

### <a name="assigning-users-or-groups-to-an-application"></a>Przypisywanie użytkowników lub grup do aplikacji

Delegowanie przypisania użytkowników i grup, które mogą uzyskiwać dostęp do aplikacji logowania jednokrotnego opartego na protokole SAML. Wymagane uprawnienia

- Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update

### <a name="creating-gallery-applications"></a>Tworzenie aplikacji galerii

Aby delegować tworzenie aplikacji w galerii usługi Azure AD, takich jak usługi ServiceNow, F5, Salesforce, między innymi. Wymagane uprawnienia:

- Microsoft. Directory/applicationTemplates/wystąpienie

### <a name="configuring-basic-saml-urls"></a>Konfigurowanie podstawowych adresów URL SAML

Aby delegować aktualizację i odczytać podstawowe konfiguracje języka SAML dla aplikacji logowania jednokrotnego opartego na protokole SAML. Wymagane uprawnienia:

- Microsoft. Directory/serviceprincipals/Authentication/Update
- Microsoft. Directory/Applications. weborganization/Authentication/Update

### <a name="rolling-over-or-creating-signing-certs"></a>Przerzucanie lub tworzenie certyfikatów podpisywania

Delegowanie zarządzania certyfikatami podpisywania dla aplikacji logowania jednokrotnego opartego na protokole SAML. Wymagane uprawnienia.

Microsoft. Directory/Applications/Credentials/Update

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Zaktualizuj adres e-mail powiadomienia o wygaśnięciu certyfikatu logowania

Aby delegować aktualizację wygasających certyfikatów logowania adresy e-mail dla aplikacji logowania jednokrotnego opartego na protokole SAML. Wymagane uprawnienia:

- Microsoft. Directory/Applications. weborganization/Authentication/Update
- Microsoft. katalog/aplikacje. weborganizations/Permissions/Update
- Microsoft. Directory/serviceprincipals/Authentication/Update
- Microsoft. Directory/serviceprincipals/Basic/Update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>Zarządzanie sygnaturą i algorytmem logowania tokenu SAML

Aby delegować aktualizację sygnatury tokenu języka SAML i algorytmu logowania dla aplikacji logowania jednokrotnego opartego na protokole SAML. Wymagane uprawnienia:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja
- Microsoft. Directory/serviceprincipals/policies/Update

### <a name="manage-user-attributes-and-claims"></a>Zarządzanie atrybutami i oświadczeniami użytkowników

Aby delegować uprawnienia do tworzenia, usuwania i aktualizowania atrybutów użytkowników oraz oświadczeń dla aplikacji logowania jednokrotnego opartego na protokole SAML. Wymagane uprawnienia:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja
- Microsoft. Directory/serviceprincipals/policies/Update

## <a name="app-provisioning-permissions"></a>Uprawnienia do aprowizacji aplikacji

Wykonywanie dowolnej operacji zapisu, takiej jak Zarządzanie zadaniem, schematem lub poświadczeniami za pomocą interfejsu użytkownika, wymaga również uprawnień Odczyt do wyświetlania strony aprowizacji.

Ustawienie zakresu wszystkim użytkownikom i grupom lub przypisanym użytkownikom i grupom obecnie wymaga uprawnień synchronizationJob i synchronizationCredentials.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Włącz lub ponownie uruchom zadania aprowizacji

W celu delegowania możliwości włączania, wyłączania i ponownego uruchamiania zadań aprowizacji. Wymagane uprawnienia:

- Microsoft. Directory/serviceprincipals/synchronizationJobs/Manage  

### <a name="configure-the-provisioning-schema"></a>Skonfiguruj schemat aprowizacji  

Aby delegować aktualizacje do mapowania atrybutów. Wymagane uprawnienia:

- Microsoft. Directory/serviceprincipals/synchronizationSchema/Manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Odczytaj ustawienia aprowizacji skojarzone z obiektem aplikacji

Delegowanie możliwości odczytu ustawień aprowizacji skojarzonych z obiektem. Wymagane uprawnienia:

- Microsoft. Directory/Applications/Synchronization/Standard/Read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Odczytaj ustawienia aprowizacji skojarzone z jednostką usługi

Aby delegować możliwość odczytywania ustawień aprowizacji skojarzonych z jednostką usługi. Wymagane uprawnienia:

- Microsoft. Directory/serviceprincipals/Synchronization/Standard/Read

### <a name="authorize-application-access-for-provisioning"></a>Autoryzuj dostęp do aplikacji w celu aprowizacji  

Delegowanie możliwości autoryzacji dostępu do aplikacji w celu aprowizacji. Przykład wejściowego tokenu okaziciela OAuth. Wymagane uprawnienia:

- Microsoft. Directory/serviceprincipals/synchronizationCredentials/Manage

## <a name="full-list-of-permissions"></a>Pełna lista uprawnień

Uprawnienie | Opis
---------- | -----------
Microsoft. Directory/applicationPolicies/allProperties/odczyt | Odczytaj wszystkie właściwości zasad aplikacji.
Microsoft. Directory/applicationPolicies/allProperties/Update | Aktualizowanie wszystkich właściwości zasad aplikacji.
Microsoft. Directory/applicationPolicies/Basic/Update | Aktualizowanie standardowych właściwości zasad aplikacji.
Microsoft. Directory/applicationPolicies/Create | Utwórz zasady aplikacji.
Microsoft. Directory/applicationPolicies/createAsOwner | Utwórz zasady aplikacji. Twórca jest dodawany jako pierwszy właściciel.
Microsoft. Directory/applicationPolicies/Delete | Usuń zasady aplikacji.
Microsoft. Directory/applicationPolicies/właściciele/odczyt | Odczytuj właścicieli zasad aplikacji.
Microsoft. Directory/applicationPolicies/właściciele/aktualizacja | Zaktualizuj Właściwość Owner zasad aplikacji.
Microsoft. Directory/applicationPolicies/policyAppliedTo/odczyt | Odczytaj zasady aplikacji zastosowane do listy obiektów.
Microsoft. Directory/applicationPolicies/Standard/Read | Odczytywanie standardowych właściwości zasad aplikacji.
Microsoft. Directory/serviceprincipals/allProperties/allTasks | Tworzenie i usuwanie obiektów serviceprincipals oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory.
Microsoft. Directory/serviceprincipals/allProperties/Read | Odczytaj wszystkie właściwości obiektów serviceprincipals.
Microsoft. Directory/serviceprincipals/allProperties/Update | Aktualizowanie wszystkich właściwości obiektów serviceprincipals.
Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Read | Odczytaj przypisania roli głównej usługi.
Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Aktualizowanie przypisań ról głównych usługi.
Microsoft. Directory/serviceprincipals/appRoleAssignments/Read | Odczytaj przypisania ról przypisane do jednostek usługi.
Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizowanie właściwości odbiorców w jednostkach usługi.
Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizowanie właściwości uwierzytelniania w jednostkach usługi.
Microsoft. Directory/serviceprincipals/Basic/Update | Aktualizowanie podstawowych właściwości w jednostkach usługi.
Microsoft. Directory/serviceprincipals/Create | Utwórz jednostki usługi.
Microsoft. Directory/serviceprincipals/createAsOwner | Utwórz jednostki usługi. Twórca jest dodawany jako pierwszy właściciel.
Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizowanie właściwości poświadczeń w jednostkach usługi.
Microsoft. Directory/serviceprincipals/Delete | Usuwanie nazw głównych usługi.
Microsoft. Directory/serviceprincipals/Disable | Wyłącz jednostki usługi.
Microsoft. Directory/serviceprincipals/Enable | Włącz jednostki usługi.
Microsoft. Directory/serviceprincipals/getPasswordSingleSignOnCredentials | Odczytywanie poświadczeń logowania jednokrotnego w nazwach głównych usługi.
Microsoft. Directory/serviceprincipals/managePasswordSingleSignOnCredentials | Zarządzanie poświadczeniami logowania jednokrotnego w nazwach głównych usługi.
Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/Read | Odczytaj przyznane delegowania uprawnień w jednostkach usługi.
Microsoft. Directory/serviceprincipals/Owners/Read | Odczytuj właścicieli w jednostkach usługi.
Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizowanie właścicieli w jednostkach usługi.
Microsoft. Directory/serviceprincipals/Permissions/Update |  
Microsoft. Directory/serviceprincipals/policies/Read | Odczytaj zasady w jednostkach usługi.
Microsoft. Directory/serviceprincipals/policies/Update | Aktualizowanie zasad w jednostkach usługi.
Microsoft. Directory/serviceprincipals/Standard/Read | Odczytywanie standardowych właściwości jednostek usługi.
Microsoft. Directory/serviceprincipals/Synchronization/Standard/Read | Odczytaj ustawienia aprowizacji skojarzone z jednostką usługi.
Microsoft. Directory/serviceprincipals/tag/Update | Aktualizowanie właściwości Tags w jednostkach usługi.
Microsoft. Directory/applicationTemplates/wystąpienie | Tworzenie wystąpienia aplikacji galerii z szablonów aplikacji.
Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj dzienniki inspekcji.
Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj raporty logowania.
Microsoft. Directory/Applications/Synchronization/Standard/Read | Odczytaj ustawienia aprowizacji skojarzone z obiektem aplikacji.
Microsoft. Directory/serviceprincipals/synchronizationJobs/Manage | Zarządzanie wszystkimi aspektami synchronizacji zadań dla zasobów głównych usługi
Microsoft. Directory/serviceprincipals/Synchronization/Standard/Read | Odczytaj ustawienia aprowizacji skojarzone z jednostkami usługi
Microsoft. Directory/serviceprincipals/synchronizationSchema/Manage | Zarządzanie wszystkimi aspektami synchronizacji schematu dla zasobów głównych usługi
Microsoft. Directory/provisioningLogs/allProperties/odczyt | Odczytywanie wszystkich właściwości dzienników aprowizacji

## <a name="next-steps"></a>Następne kroki

- [Tworzenie ról niestandardowych przy użyciu Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph](custom-create.md)
- [Wyświetlanie przypisań roli niestandardowej](custom-view-assignments.md)
