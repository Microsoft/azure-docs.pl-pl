---
title: Wyświetl listę grup łączników usługi Azure serwer proxy aplikacji usługi Azure AD dla aplikacji
description: Przykład programu PowerShell, który wyświetla listę wszystkich grup łączników serwera proxy aplikacji programu Azure Active Directory (Azure AD) z przypisanymi aplikacjami.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f381d64e06d8b7abf67b08297baa616397cc439
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019441"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Pobierz wszystkie aplikacje i listy serwerów proxy aplikacji według grupy łączników

Ten przykładowy skrypt programu PowerShell zawiera listę informacji o wszystkich grupach łączników serwera proxy aplikacji programu Azure Active Directory (Azure AD) z przypisanymi aplikacjami.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) lub [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Pobiera nazwę główną usługi. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Pobiera aplikację usługi Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Pobiera aplikację skonfigurowaną dla serwera proxy aplikacji w usłudze Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Pobiera listę wszystkich grup łączników lub jeśli określono, szczegóły określonej grupy łączników. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Aby poznać inne przykłady programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla usługi azure serwer proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).