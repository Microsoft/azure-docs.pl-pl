---
title: Przykład programu PowerShell — lista rozszerzonych informacji serwer proxy aplikacji aplikacji
description: Przykład programu PowerShell, który wyświetla listę wszystkich aplikacji usługi Azure Active Directory (Azure AD) serwer proxy aplikacji wraz z identyfikatorem aplikacji (AppId), nazwą (DisplayName), zewnętrznym adresem URL (ExternalUrl), wewnętrznym adresem URL (InternalUrl) i typem uwierzytelniania (ExternalAuthenticationType).
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
ms.openlocfilehash: 1b3234a13a0e3fac760a899ce66e7faa7e7b642c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377365"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Uzyskiwanie wszystkich serwer proxy aplikacji aplikacji i uzyskiwanie listy rozszerzonych informacji

Ten przykładowy skrypt programu PowerShell zawiera informacje o wszystkich aplikacjach Azure Active Directory (Azure AD) serwer proxy aplikacji, w tym identyfikator aplikacji (AppId), nazwę (DisplayName), zewnętrzny adres URL (ExternalUrl), wewnętrzny adres URL (InternalUrl), typ uwierzytelniania (ExternalAuthenticationType), tryb logowania jednokrotnego i dalsze ustawienia.

Zmiana wartości zmiennej logowania $ssoMode włącza filtrowane dane wyjściowe według trybu logowania jednokrotnego. Dalsze szczegóły zostały udokumentowane w skrypcie.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga modułu [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) (AzureAD).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Pobiera jednostkę usługi. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Pobiera aplikację skonfigurowaną do serwer proxy aplikacji usłudze Azure AD. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Azure AD PowerShell module overview (Omówienie modułu Azure AD PowerShell).](/powershell/azure/active-directory/overview)

Aby uzyskać inne przykłady programu PowerShell serwer proxy aplikacji, zobacz [Azure AD PowerShell examples for Azure AD serwer proxy aplikacji](../application-proxy-powershell-samples.md)(Przykłady programu Azure AD PowerShell dla usługi Azure AD serwer proxy aplikacji).
