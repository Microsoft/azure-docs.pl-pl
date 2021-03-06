---
title: Przykład programu PowerShell — lista użytkowników & grup dla aplikacji serwera proxy aplikacji
description: Przykład programu PowerShell, który wyświetla listę wszystkich użytkowników i grup przypisanych do konkretnej aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0fdbf2ab7d33978a72e7455f3e2f5ba591cc4ab7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375495"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Wyświetlanie użytkowników i grup przypisanych do aplikacji serwera proxy aplikacji

Ten przykładowy skrypt programu PowerShell wyświetla listę użytkowników i grup przypisanych do określonej aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) lub [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| Pobiera użytkownika. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| Pobiera grupę. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Pobiera nazwę główną usługi. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | Pobierz przypisanie roli aplikacji użytkownika. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | Pobierz przypisanie roli aplikacji do grupy. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby poznać inne przykłady programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla usługi azure serwer proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).
