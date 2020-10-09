---
title: Przykład programu PowerShell — przypisywanie grupy do aplikacji serwera proxy aplikacji
description: Przykład programu PowerShell przypisujący grupę do aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c1a8ebe170c499febc42fed028a17f7e6e591fc6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507063"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>Przypisywanie grupy do określonej aplikacji serwer proxy aplikacji usługi Azure AD platformy Azure

Ten przykładowy skrypt programu PowerShell umożliwia przypisanie określonej grupy do aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) lub [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
| [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/New-azureadgroupapproleassignment?view=azureadps-2.0) | Przypisuje grupę do roli aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Aby poznać inne przykłady programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla usługi azure serwer proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).