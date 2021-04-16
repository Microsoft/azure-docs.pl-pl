---
title: Przykład programu PowerShell — serwer proxy aplikacji z identycznymi certyfikatami
description: Przykład programu PowerShell, który wyświetla Azure Active Directory (Azure AD) serwer proxy aplikacji, które są publikowane przy użyciu identycznego certyfikatu.
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
ms.openlocfilehash: 18200b5571c84c8bd442d5dcb2714c6d52d4bcf5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377053"
---
# <a name="get-all-application-proxy-apps-that-are-published-with-the-identical-certificate"></a>Pobierz wszystkie serwer proxy aplikacji, które są publikowane przy użyciu identycznego certyfikatu

Ten przykładowy skrypt programu PowerShell zawiera listę wszystkich Azure Active Directory (Azure AD) serwer proxy aplikacji, które są publikowane przy użyciu identycznego certyfikatu.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga modułu [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) lub wersji zapoznawczej modułu [AzureAD V2 PowerShell dla](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) programu Graph (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Pobiera jednostkę usługi. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Pobiera aplikację skonfigurowaną do serwer proxy aplikacji usłudze Azure AD. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Azure AD PowerShell module overview (Omówienie modułu Azure AD PowerShell).](/powershell/azure/active-directory/overview)

Aby uzyskać inne przykłady programu PowerShell dla serwer proxy aplikacji, zobacz [Przykłady programu Azure AD PowerShell dla usługi Azure AD serwer proxy aplikacji](../application-proxy-powershell-samples.md).
