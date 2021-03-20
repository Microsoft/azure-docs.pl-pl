---
title: Co nowego w usłudze Azure AD w Microsoft 365 instytucji rządowych? | Microsoft Docs
description: Dowiedz się więcej o zmianach w usłudze Azure Active Directory (Azure AD) w wystąpieniu chmury Microsoft 365 dla instytucji rządowych, które może mieć wpływ na Ciebie.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89318934"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Co nowego w Azure Active Directory w Microsoft 365 instytucji rządowych

Wprowadziliśmy pewne zmiany w usłudze Azure Active Directory (Azure AD) w wystąpieniu chmury Microsoft 365 dla instytucji rządowych, które ma zastosowanie do klientów korzystających z następujących usług:

- Microsoft Azure Government

- Microsoft 365e — Zarząd — wysoka

- Microsoft 365 rząd — DoD

Ten artykuł nie ma zastosowania do Microsoft 365 instytucji rządowych — w przypadku klientów z systemem w zatoce.

## <a name="changes-to-the-initial-domain-name"></a>Zmiany początkowej nazwy domeny

Podczas początkowego rejestrowania w organizacji dla Microsoft 365ej usługi online dla instytucji rządowych zostanie wyświetlony monit o wybranie nazwy domeny w organizacji `<your-domain-name>.onmicrosoft.com` . Jeśli masz już nazwę domeny z sufiksem. com, nic się nie zmieni.

Jeśli jednak rejestrujesz się w celu skorzystania z nowej usługi Microsoft 365 instytucji rządowych, zostanie wyświetlony monit o wybranie nazwy domeny przy użyciu `.us` sufiksu. Tak więc będzie `<your-domain-name>.onmicrosoft.us` .

>[!Note]
>Ta zmiana nie dotyczy żadnych klientów, którzy są zarządzani przez dostawców usług w chmurze (CSP).

## <a name="changes-to-portal-access"></a>Zmiany dostępu do portalu

Zaktualizowaliśmy punkty końcowe portalu dla Microsoft Azure Government, Microsoft 365 dla instytucji rządowych — do szkoły wyższej i Microsoft 365 dla instytucji rządowych — DoD, jak pokazano w [tabeli mapowania punktu końcowego](#endpoint-mapping).

Wcześniej klienci mogą zalogować się przy użyciu portali platformy Azure (portal.azure.com) i pakietu Office 365 (portal.office.com). Dzięki tej aktualizacji klienci muszą teraz zalogować się przy użyciu określonych Microsoft Azure Government, Microsoft 365 dla instytucji rządowych — w zatoce i Microsoft 365ych portalach rządowych i DoD.

## <a name="endpoint-mapping"></a>Mapowanie punktów końcowych

W poniższej tabeli przedstawiono punkty końcowe dla wszystkich klientów:

| Nazwa | Szczegóły punktu końcowego |
|------|------------------|
| Portale |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 rząd — w programie w zatoce: https://portal.office365.us<p>Microsoft 365 rząd — DoD: https://portal.apps.mil |
| Punkt końcowy urzędu Azure Active Directory | https://login.microsoftonline.us |
| Microsoft Graph API dla Microsoft 365 instytucji rządowych — w ramach programu w zatoce | https://graph.microsoft.us |
| Interfejs API Microsoft Graph dla Microsoft 365 dla instytucji rządowych — DoD | https://dod-graph.microsoft.us |
| Punkty końcowe usług Azure Government Services | Aby uzyskać szczegółowe informacje, zobacz [przewodnik dewelopera Azure Government](../../azure-government/documentation-government-developer-guide.md) |
| Microsoft 365 rząd — wysokie punkty końcowe w usłudze w zatoce | Aby uzyskać szczegółowe informacje, zobacz tematy dotyczące [górnych punktów końcowych pakietu Office 365 USA](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 rząd — DoD | Aby uzyskać szczegółowe informacje, zobacz [Office 365 Stany Zjednoczone i punkty końcowe dla instytucji rządowych USA](/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących artykułach:

- [Co to jest platforma Azure Government?](../../azure-government/documentation-government-welcome.md)

- [Aktualizacja punktu końcowego urzędu usługi AAD Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph punkty końcowe w chmurze dla instytucji rządowych USA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Pakiet Office 365 dla instytucji rządowych Stanów Zjednoczonych i DoD.](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)