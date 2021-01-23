---
title: Dokumentacja roli administratora w usłudze Microsoft 365 Services — Azure AD | Microsoft Docs
description: Znajdź zawartość i informacje dotyczące interfejsów API dla ról administratorów dla usług Microsoft 365 w programie Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5579e0d2094c3dc596e8e50992ae6bc31818ff87
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743070"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Role usług Microsoft 365 Services w programie Azure Active Directory

Wszystkie produkty w Microsoft 365 mogą być zarządzane przy użyciu ról administracyjnych w usłudze Azure Active Directory (Azure AD). Niektóre produkty udostępniają także dodatkowe role, które są specyficzne dla danego produktu. Informacje o rolach obsługiwanych przez poszczególne produkty znajdują się w poniższej tabeli. Ogólne dyskusje dotyczące problemów z delegowaniem można znaleźć w temacie [Planowanie delegowania ról w Azure Active Directory](concept-delegation.md).

## <a name="where-to-find-content"></a>Gdzie można znaleźć zawartość

Usługa Microsoft 365 | Zawartość roli | Zawartość interfejsu API
---------------------- | ------------------ | -----------------
Role administratora w pakietach Office 365 i Microsoft 365 planach biznesowych | [Microsoft 365 ról administratorów](/office365/admin/add-users/about-admin-roles?view=o365-worldwide&preserve-view=true) | Niedostępne
Azure Active Directory (Azure AD) i Azure AD Identity Protection| [Role administratora usługi Azure AD](permissions-reference.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0&preserve-view=true&preserve-view=true)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Exchange Online| [Kontrola dostępu oparta na rolach programu Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Program PowerShell dla programu Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[Pobieranie przypisań ról](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
SharePoint Online | [Role administratora usługi Azure AD](permissions-reference.md)<br>Ponadto [rola administratora programu SharePoint w Microsoft 365](/sharepoint/sharepoint-admin-role) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Zespoły/Skype dla firm | [Role administratora usługi Azure AD](permissions-reference.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Security & — Centrum zgodności (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Role administratora usługi Office 365](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Program Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[Pobieranie przypisań ról](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
Wynik zabezpieczony | [Role administratora usługi Azure AD](permissions-reference.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Menedżer zgodności | [Role Menedżera zgodności](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Niedostępne
Azure Information Protection | [Role administratora usługi Azure AD](permissions-reference.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Microsoft Cloud App Security | [Kontrola dostępu oparta na rolach](/cloud-app-security/manage-admins) | [Dokumentacja interfejsu API](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Grupy ról Zaawansowanej ochrony przed zagrożeniami na platformie Azure](/azure-advanced-threat-protection/atp-role-groups) | Niedostępne
Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender | [Kontrola dostępu oparta na rolach w usłudze Windows Defender ATP](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Niedostępne
Privileged Identity Management | [Role administratora usługi Azure AD](permissions-reference.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Intune | [Kontrola dostępu oparta na rolach usługi Intune](/intune/role-based-access-control) | [Interfejs API programu Graph](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[Pobieranie przypisań ról](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Pulpit zarządzany | [Role administratora usługi Azure AD](permissions-reference.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>Następne kroki

* [Jak przypisać lub usunąć role administratorów usługi Azure AD](manage-roles-portal.md)
* [Informacje o rolach administratorów usługi Azure AD](permissions-reference.md)