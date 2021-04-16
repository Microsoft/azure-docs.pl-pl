---
title: Przykład programu PowerShell — przenoszenie serwer proxy aplikacji do innej grupy
description: Azure Active Directory (Azure AD) serwer proxy aplikacji przykład programu PowerShell używany do przenoszenia wszystkich aplikacji aktualnie przypisanych do grupy łączników do innej grupy łączników.
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
ms.openlocfilehash: eb421a1a8040a406c8f3e4be04f9a99d881237d9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376923"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Przenoszenie wszystkich aplikacji przypisanych do grupy łączników do innej grupy łączników

Ten przykładowy skrypt programu PowerShell przenosi wszystkie Azure Active Directory (Azure AD) serwer proxy aplikacji obecnie przypisane do grupy łączników do innej grupy łączników.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga modułu [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) lub wersji zapoznawczej modułu [AzureAD V2 PowerShell dla](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) programu Graph (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Pobiera jednostkę usługi. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację usługi Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Pobiera listę wszystkich grup łączników lub, jeśli jest określona, szczegóły określonej grupy łączników. |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | Przypisuje podaną grupę łączników do określonej aplikacji.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Azure AD PowerShell module overview (Omówienie modułu Azure AD PowerShell).](/powershell/azure/active-directory/overview)

Aby uzyskać inne przykłady programu PowerShell dla serwer proxy aplikacji, zobacz [Przykłady programu Azure AD PowerShell dla usługi Azure AD serwer proxy aplikacji](../application-proxy-powershell-samples.md).
