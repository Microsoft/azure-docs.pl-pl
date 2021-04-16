---
title: Przykład programu PowerShell — eksportowanie wpisów tajnych i certyfikatów dla rejestracji aplikacji w Azure Active Directory dzierżawie.
description: Przykład programu PowerShell, który eksportuje wszystkie wpisy tajne i certyfikaty dla określonych rejestracji aplikacji w Azure Active Directory dzierżawie.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: b5cbb6b3843e81d9265405dcea24a092e57bf65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377048"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Eksportowanie wpisów tajnych i certyfikatów dla rejestracji aplikacji

Ten przykładowy skrypt programu PowerShell eksportuje wszystkie wpisy tajne i certyfikaty dla określonych rejestracji aplikacji z katalogu do pliku CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ten przykład wymaga programu PowerShell usługi [AzureAD w](/powershell/azure/active-directory/install-adv2) wersji 2 dla modułu Programu Graph (AzureAD) lub programu [AzureAD V2 PowerShell dla](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) modułu Graph w wersji zapoznawczej (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Polecenie "Add-Member" jest odpowiedzialne za tworzenie kolumn w pliku CSV.
Zmienną "$Path" można zmodyfikować bezpośrednio w programie PowerShell przy użyciu ścieżki pliku CSV, jeśli wolisz, aby eksport był nieinterakcyjny.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Pobiera aplikację z katalogu. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Pobiera właścicieli aplikacji z katalogu. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz Omówienie modułu [Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby uzyskać inne przykłady programu PowerShell dotyczące zarządzania aplikacją, zobacz [Azure AD PowerShell examples for Application Management (Przykłady programu Azure AD PowerShell do zarządzania aplikacją).](../app-management-powershell-samples.md)
