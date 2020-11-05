---
title: Uprawnienia do zgody aplikacji dla ról niestandardowych w Azure Active Directory | Microsoft Docs
description: Zapodgląd uprawnień do zgody aplikacji na niestandardowe role usługi Azure AD w Azure Portal, programie PowerShell lub interfejs API programu Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: 358a458698ec1fd8443e15f71a84e057f33af527
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376735"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Uprawnienia do zgody aplikacji dla ról niestandardowych w Azure Active Directory

Ten artykuł zawiera aktualnie dostępne uprawnienia do zgody na aplikacje dla definicji ról niestandardowych w Azure Active Directory (Azure AD). W tym artykule znajdziesz uprawnienia wymagane do niektórych typowych scenariuszy związanych z zgodą i uprawnieniami aplikacji.

## <a name="required-license-plan"></a>Wymagany plan licencji

Korzystanie z tej funkcji wymaga licencji na Azure AD — wersja Premium P1 dla organizacji usługi Azure AD. Aby znaleźć licencję odpowiednią do wymagań, zobacz [porównanie ogólnodostępnych funkcji w wersji bezpłatnej, podstawowej i premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>Uprawnienia dotyczące zgód aplikacji

Skorzystaj z uprawnień wymienionych w tym artykule, aby zarządzać zasadami wyrażania zgody na aplikacje, a także uprawnieniami do udzielania zgody dla aplikacji.

> [!NOTE]
> Portal administracyjny usługi Azure AD nie obsługuje jeszcze dodawania uprawnień wymienionych w tym artykule do definicji niestandardowej roli katalogu. [Aby utworzyć niestandardową rolę katalogu](custom-create.md#create-a-role-using-powershell) z uprawnieniami wymienionymi w tym artykule, należy użyć programu Azure AD PowerShell.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Przyznawanie delegowanych uprawnień do aplikacji w imieniu użytkownika

Umożliwienie użytkownikom udzielania zgody na aplikacje w imieniu użytkownika (zgoda użytkowników), które podlegają zasadom zgody aplikacji.

- Microsoft. Directory/serviceprincipals/managePermissionGrantsForSelf. #c1

Gdzie `{id}` jest zastępowana identyfikatorem [zasad zgody aplikacji](../manage-apps/manage-app-consent-policies.md) , które ustawi warunki, które muszą zostać spełnione, aby to uprawnienie było aktywne.

Na przykład, aby zezwolić użytkownikom na udzielenie zgody w swoim imieniu, z uwzględnieniem wbudowanych zasad zgody aplikacji o IDENTYFIKATORze `microsoft-user-default-low` , należy użyć uprawnienia `...managePermissionGrantsForSelf.microsoft-user-default-low` .

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Przyznawanie uprawnień do aplikacji w imieniu wszystkich (zgoda administratora)

Aby delegować zgodę administratora w całej dzierżawie na aplikacje, dla uprawnień delegowanych i uprawnień aplikacji (ról aplikacji):

- Microsoft. Directory/serviceprincipals/managePermissionGrantsForAll. #c1

Gdzie `{id}` jest zastępowana identyfikatorem [zasad zgody aplikacji](../manage-apps/manage-app-consent-policies.md) , które ustawi warunki, które muszą zostać spełnione, aby można było korzystać z tego uprawnienia.

Na przykład, aby zezwolić przyznanym roli na udzielanie zgody administratora w całej dzierżawie na aplikacje podlegające niestandardowym [zasadom zgody aplikacji](../manage-apps/manage-app-consent-policies.md) o identyfikatorze `low-risk-any-app` , należy użyć uprawnienia `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` .

### <a name="managing-app-consent-policies"></a>Zarządzanie zasadami wyrażania zgody aplikacji

Delegowanie tworzenia, aktualizowania i usuwania [zasad zgody aplikacji](../manage-apps/manage-app-consent-policies.md).

- Microsoft. Directory/permissionGrantPolicies/Create
- Microsoft. Directory/permissionGrantPolicies/Standard/Read
- Microsoft. Directory/permissionGrantPolicies/Basic/Update
- Microsoft. Directory/permissionGrantPolicies/Delete

## <a name="full-list-of-permissions"></a>Pełna lista uprawnień

Uprawnienie | Opis
---------- | -----------
Microsoft. Directory/serviceprincipals/managePermissionGrantsForSelf. #c1 | Umożliwia użytkownikom wyrażanie zgody na aplikacje w imieniu użytkownika (zgoda użytkowników), które podlegają zasadom zgody aplikacji `{id}` .
Microsoft. Directory/serviceprincipals/managePermissionGrantsForAll. #c1 | Przyznaje uprawnienie do wyrażania zgody na aplikacje w imieniu wszystkich (zgoda administratora dla całej dzierżawy), z zastrzeżeniem zasad zgody aplikacji `{id}` .
Microsoft. Directory/permissionGrantPolicies/Standard/Read | Przyznaje możliwość odczytywania zasad zgody na aplikacje.
Microsoft. Directory/permissionGrantPolicies/Basic/Update | Przyznaje możliwość aktualizowania podstawowych właściwości istniejących zasad zgody aplikacji.
Microsoft. Directory/permissionGrantPolicies/Create | Przyznaje możliwość tworzenia zasad zgody aplikacji.
Microsoft. Directory/permissionGrantPolicies/Delete | Przyznaje możliwość usuwania zasad zgody aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie ról niestandardowych przy użyciu Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph](custom-create.md)
- [Wyświetlanie przypisań roli niestandardowej](../roles/view-assignments.md)
