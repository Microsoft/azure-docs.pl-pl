---
title: Przykład programu PowerShell — lista wszystkich serwer proxy aplikacji łączników
description: Przykład programu PowerShell, który zawiera listę Azure Active Directory (Azure AD) serwer proxy aplikacji łączników i łączników w katalogu.
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
ms.openlocfilehash: 032d4930e29126ec9636e710f016d9612dc2cda0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377571"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Pobierz wszystkie grupy łączników i łączniki w katalogu

Ten przykładowy skrypt programu PowerShell zawiera listę wszystkich Azure Active Directory (Azure AD) serwer proxy aplikacji łączników i łączników w katalogu.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ten przykład wymaga programu PowerShell usługi [AzureAD w](/powershell/azure/active-directory/install-adv2) wersji 2 dla modułu Programu Graph (AzureAD) lub programu [AzureAD V2 PowerShell dla](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) modułu Graph w wersji zapoznawczej (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

| Polecenie | Uwagi |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Pobiera listę wszystkich grup łączników lub, jeśli zostanie określona, szczegóły określonej grupy łączników. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | Pobiera wszystkie serwer proxy aplikacji skojarzone z każdą grupą łączników.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz Omówienie modułu [Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby uzyskać inne przykłady programu PowerShell dla serwer proxy aplikacji, zobacz [Przykłady programu Azure AD PowerShell dla usługi Azure AD serwer proxy aplikacji](../application-proxy-powershell-samples.md).
