---
title: Przykład programu PowerShell — lista serwer proxy aplikacji aplikacji z zasadami
description: Przykład programu PowerShell, który zawiera Azure Active Directory wszystkich aplikacji serwer proxy aplikacji (Azure AD) w katalogu, które mają zasady tokenu okresu istnienia.
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
ms.openlocfilehash: 53ec98ca4bd981655a3b4d6fdbccf10f7c7f9fb9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376634"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Pobierz wszystkie serwer proxy aplikacji aplikacji z zasadami okresu istnienia tokenu

Ten przykładowy skrypt programu PowerShell zawiera listę wszystkich Azure Active Directory (Azure AD) serwer proxy aplikacji w katalogu, które mają zasady okresu istnienia tokenu, oraz szczegółowe informacje o zasadach.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga wersji [zapoznawczej modułu AzureAD V2 PowerShell dla](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) programu Graph (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Pobiera jednostkę usługi. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Pobiera zasady w usłudze Azure AD. |
|[Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Pobiera zasady jednostki usługi w usłudze Azure AD. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Azure AD PowerShell module overview (Omówienie modułu Azure AD PowerShell).](/powershell/azure/active-directory/overview)

Aby uzyskać inne przykłady programu PowerShell serwer proxy aplikacji, zobacz [Azure AD PowerShell examples for Azure AD serwer proxy aplikacji](../application-proxy-powershell-samples.md)(Przykłady programu Azure AD PowerShell dla usługi Azure AD serwer proxy aplikacji).
