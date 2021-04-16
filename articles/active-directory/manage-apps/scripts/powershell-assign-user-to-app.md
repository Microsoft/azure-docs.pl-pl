---
title: Przykład programu PowerShell — przypisywanie użytkownika do serwer proxy aplikacji aplikacji
description: Przykład programu PowerShell, który przypisuje użytkownika do aplikacji Azure Active Directory (Azure AD) serwer proxy aplikacji aplikacji.
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
ms.openlocfilehash: 1b58a16363adefaca5f80e828e2bba5157c5da7b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375563"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Przypisywanie użytkownika do określonej aplikacji usługi Azure AD serwer proxy aplikacji aplikacji

Ten przykładowy skrypt programu PowerShell umożliwia przypisanie użytkownika do określonej aplikacji usługi Azure AD serwer proxy aplikacji aplikacji.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga modułu [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) lub wersji zapoznawczej modułu [AzureAD V2 PowerShell dla](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) programu Graph (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
| [New-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/new-azureaduserapproleassignment) | Przypisuje użytkownika do roli aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Azure AD PowerShell module overview (Omówienie modułu Azure AD PowerShell).](/powershell/azure/active-directory/overview)

Aby uzyskać inne przykłady programu PowerShell dla serwer proxy aplikacji, zobacz [Przykłady programu Azure AD PowerShell dla usługi Azure AD serwer proxy aplikacji](../application-proxy-powershell-samples.md).
