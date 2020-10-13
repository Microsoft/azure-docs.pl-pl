---
title: Dokumentacja roli administratora w usłudze Microsoft 365 Services — Azure AD | Microsoft Docs
description: Znajdź zawartość i informacje dotyczące interfejsów API dla ról administratorów dla usług Microsoft 365 w programie Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6060bc286024183661024abd56567eeefed76430
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90053552"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Role administratorów dla usług Microsoft 365 Services

Wszystkie produkty w Microsoft 365 mogą być zarządzane przy użyciu ról administracyjnych w usłudze Azure AD. Niektóre produkty udostępniają także dodatkowe role, które są specyficzne dla danego produktu. Informacje o rolach obsługiwanych przez poszczególne produkty znajdują się w poniższej tabeli. Ogólne dyskusje dotyczące problemów z delegowaniem można znaleźć w temacie [Planowanie delegowania ról w Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Gdzie można znaleźć zawartość

Usługa Microsoft 365 | Zawartość roli | Zawartość interfejsu API
---------------------- | ------------------ | -----------------
Role administratora w pakietach Office 365 i Microsoft 365 planach biznesowych | [Microsoft 365 ról administratorów](/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Niedostępne
Azure Active Directory (Azure AD) i Azure AD Identity Protection| [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Kontrola dostępu oparta na rolach programu Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Program PowerShell dla programu Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Pobieranie przypisań ról](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Role administratora usługi Azure AD](directory-assign-admin-roles.md)<br>Ponadto [rola administratora programu SharePoint w Microsoft 365](/sharepoint/sharepoint-admin-role) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0)
Zespoły/Skype dla firm | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0)
Security & — Centrum zgodności (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Role administratora usługi Office 365](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Program Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Pobieranie przypisań ról](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Wynik zabezpieczony | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0)
Menedżer zgodności | [Role Menedżera zgodności](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Niedostępne
Azure Information Protection | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Kontrola dostępu oparta na rolach](/cloud-app-security/manage-admins) | [Dokumentacja interfejsu API](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Grupy ról Zaawansowanej ochrony przed zagrożeniami na platformie Azure](/azure-advanced-threat-protection/atp-role-groups) | Niedostępne
Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender | [Kontrola dostępu oparta na rolach w usłudze Windows Defender ATP](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Niedostępne
Privileged Identity Management | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Kontrola dostępu oparta na rolach usługi Intune](/intune/role-based-access-control) | [Interfejs API programu Graph](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Pobieranie przypisań ról](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Pulpit zarządzany | [Role administratora usługi Azure AD](directory-assign-admin-roles.md) | [Interfejs API programu Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Pobieranie przypisań ról](/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Następne kroki

* [Jak przypisać lub usunąć role administratorów usługi Azure AD](directory-manage-roles-portal.md)
* [Informacje o rolach administratorów usługi Azure AD](directory-assign-admin-roles.md)