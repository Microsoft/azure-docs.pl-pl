---
title: Przykład programu PowerShell — eksportowanie wpisów tajnych i certyfikatów dla aplikacji dla przedsiębiorstw w dzierżawie Azure Active Directory.
description: Przykład programu PowerShell eksportujący wszystkie wpisy tajne i certyfikaty dla określonych aplikacji przedsiębiorstwa w dzierżawie Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 20caefe74a7c047fb8690bb1d9e6f4eb9da7e9b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635199"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Eksportuj wpisy tajne i certyfikaty dla aplikacji dla przedsiębiorstw
Ten przykładowy skrypt programu PowerShell eksportuje wszystkie wpisy tajne, certyfikaty i właścicieli dla określonych aplikacji przedsiębiorstwa z katalogu do pliku CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) lub [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Polecenie "Add-member" jest odpowiedzialne za tworzenie kolumn w pliku CSV.
Możesz zmodyfikować zmienną "$Path" bezpośrednio w programie PowerShell, używając ścieżki pliku CSV, na wypadek, gdyby eksport nie był interaktywny.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Pobiera aplikację przedsiębiorstwa z katalogu. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Pobiera właścicieli aplikacji przedsiębiorstwa z katalogu. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby poznać inne przykłady dla programu PowerShell dotyczące zarządzania aplikacjami, zobacz [przykłady programu Azure AD PowerShell dotyczące zarządzania aplikacjami](../app-management-powershell-samples.md).
