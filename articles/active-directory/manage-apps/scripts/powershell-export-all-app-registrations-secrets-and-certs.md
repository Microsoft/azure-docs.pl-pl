---
title: Przykład programu PowerShell — eksportowanie wpisów tajnych i certyfikatów dla rejestracji aplikacji w dzierżawie Azure Active Directory.
description: Przykład programu PowerShell eksportujący wszystkie wpisy tajne i certyfikaty dla określonych rejestracji aplikacji w dzierżawie Azure Active Directory.
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
ms.openlocfilehash: d0de96d0d8a5edc6fbacc25dcbcb868073e57183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556557"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Eksportowanie wpisów tajnych i certyfikatów na potrzeby rejestracji aplikacji

Ten przykładowy skrypt programu PowerShell eksportuje wszystkie wpisy tajne i certyfikaty dla określonych rejestracji aplikacji z katalogu do pliku CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) lub [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Polecenie "Add-member" jest odpowiedzialne za tworzenie kolumn w pliku CSV.
Możesz zmodyfikować zmienną "$Path" bezpośrednio w programie PowerShell, używając ścieżki pliku CSV, na wypadek, gdyby eksport nie był interaktywny.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację z katalogu. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Pobiera właścicieli aplikacji z katalogu. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby poznać inne przykłady dla programu PowerShell dotyczące zarządzania aplikacjami, zobacz [przykłady programu Azure AD PowerShell dotyczące zarządzania aplikacjami](../app-management-powershell-samples.md).
