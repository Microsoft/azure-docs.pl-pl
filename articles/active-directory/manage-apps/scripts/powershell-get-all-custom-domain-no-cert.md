---
title: Przykład programu PowerShell — serwer proxy aplikacji aplikacji bez certyfikatu
description: Przykład programu PowerShell, który wyświetla listę wszystkich Azure Active Directory (Azure AD) serwer proxy aplikacji, które są korzystające z domen niestandardowych, ale nie mają przekazanego prawidłowego certyfikatu TLS/SSL.
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
ms.openlocfilehash: 00c2aa65c727bc614441b59c0021846855fcaf68
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377144"
---
# <a name="get-all-application-proxy-apps-published-with-no-certificate-uploaded"></a>Pobierz wszystkie aplikacje serwer proxy aplikacji opublikowane bez przekazanego certyfikatu

Ten przykładowy skrypt programu PowerShell zawiera listę wszystkich Azure Active Directory (Azure AD) serwer proxy aplikacji, które są korzystające z domen niestandardowych, ale nie mają przekazanego prawidłowego certyfikatu TLS/SSL.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga programu PowerShell usługi [AzureAD w](/powershell/azure/active-directory/install-adv2) wersji 2 dla modułu Programu Graph (AzureAD) lub programu [AzureAD V2 PowerShell dla](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) modułu Graph w wersji zapoznawczej (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Pobiera jednostkę usługi. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Pobiera aplikację skonfigurowaną na serwer proxy aplikacji w usłudze Azure AD. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz Omówienie modułu [Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby uzyskać inne przykłady programu PowerShell dla serwer proxy aplikacji, zobacz [Przykłady programu Azure AD PowerShell dla usługi Azure AD serwer proxy aplikacji](../application-proxy-powershell-samples.md).
