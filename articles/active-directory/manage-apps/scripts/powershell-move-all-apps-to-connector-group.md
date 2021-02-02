---
title: Przykład programu PowerShell — przenoszenie aplikacji serwera proxy aplikacji do innej grupy
description: Przykład programu PowerShell serwera proxy aplikacji usługi Azure Active Directory (Azure AD) służący do przenoszenia wszystkich aplikacji aktualnie przypisanych do grupy łączników do innej grupy łączników.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9a3338c01a6e665706ff7733be8fdc9f904c5a56
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253645"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Przenoszenie wszystkich aplikacji przypisanych do grupy łączników do innej grupy łączników

Ten przykład skryptu programu PowerShell przenosi wszystkie aplikacje Azure Active Directory (Azure AD), które są obecnie przypisane do grupy łączników do innej grupy łączników.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) lub [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Pobiera nazwę główną usługi. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację usługi Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Pobiera listę wszystkich grup łączników lub jeśli określono, szczegóły określonej grupy łączników. |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | Przypisuje daną grupę łączników do określonej aplikacji.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby poznać inne przykłady programu PowerShell dla serwera proxy aplikacji, zobacz [przykłady programu Azure AD PowerShell dla usługi azure serwer proxy aplikacji usługi Azure AD](../application-proxy-powershell-samples.md).
