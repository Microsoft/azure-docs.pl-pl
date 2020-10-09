---
title: Przykład programu PowerShell — wyświetlanie wszystkich aplikacji serwera proxy aplikacji przy użyciu zasad
description: Przykład programu PowerShell, który wyświetla listę wszystkich aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD) w katalogu, które mają zasady tokenu istnienia.
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
ms.openlocfilehash: aa66b842007d9471828171c44c2dcb7505e8b4d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88506864"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Pobierz wszystkie aplikacje serwera proxy aplikacji z zasadami istnienia tokenu

Ten przykładowy skrypt programu PowerShell zawiera listę wszystkich aplikacji serwera proxy aplikacji Azure Active Directory (Azure AD) w katalogu, które mają zasady istnienia tokenu i zawiera szczegółowe informacje dotyczące zasad.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Pobiera nazwę główną usługi. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Pobiera zasady w usłudze Azure AD. |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Pobiera zasady jednostki usługi w usłudze Azure AD. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Aby poznać inne przykłady programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla usługi azure serwer proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).