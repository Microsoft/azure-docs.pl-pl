---
title: Przykład programu PowerShell — rozszerzone informacje dotyczące aplikacji serwera proxy aplikacji
description: Przykład programu PowerShell, który wyświetla listę wszystkich aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD) wraz z IDENTYFIKATORem aplikacji (AppId), nazwą (DisplayName), zewnętrznym adresem URL (ExternalUrl), wewnętrznym adresem URL (InternalUrl) i typem uwierzytelniania (ExternalAuthenticationType).
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
ms.openlocfilehash: 62dae175b529bd54b2f139eece89bbde6f98cd38
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055012"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Pobierz wszystkie aplikacje serwera proxy aplikacji i Wyświetl informacje rozszerzone

Ten przykładowy skrypt programu PowerShell zawiera listę informacji na temat wszystkich aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD), w tym identyfikatora aplikacji (AppId), nazwy (DisplayName), zewnętrznego adresu URL (ExternalUrl), wewnętrznego adresu URL (InternalUrl), typu uwierzytelniania (ExternalAuthenticationType), trybu logowania jednokrotnego i innych ustawień.

Zmiana wartości zmiennej $ssoMode włącza filtrowanie danych wyjściowych za pomocą trybu logowania jednokrotnego. Dalsze szczegóły są udokumentowane w skrypcie.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Pobiera nazwę główną usługi. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Pobiera aplikację skonfigurowaną dla serwera proxy aplikacji w usłudze Azure AD. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby poznać inne przykłady programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla usługi azure serwer proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).
