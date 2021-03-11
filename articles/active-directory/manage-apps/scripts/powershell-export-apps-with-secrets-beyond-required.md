---
title: Przykład programu PowerShell — eksportowanie aplikacji z kluczami tajnymi i certyfikatami, które wygasają poza datą wymaganą w dzierżawie Azure Active Directory.
description: Przykład programu PowerShell eksportujący wszystkie aplikacje z wpisami tajnymi i certyfikatami, które wygasają poza wymaganą datą dla określonych aplikacji w dzierżawie Azure Active Directory.
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
ms.openlocfilehash: 3572f481cc2cbcb1df73b33eb2543e32256ad9fb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584395"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Eksportowanie aplikacji z użyciem wpisów tajnych i certyfikatów wygasających poza wymaganą datą

Ten przykładowy skrypt programu PowerShell eksportuje wszystkie wpisy tajne aplikacji i certyfikaty wygasające poza wymaganą datą dla określonych aplikacji z katalogu w pliku CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) lub [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Polecenie "Add-member" jest odpowiedzialne za tworzenie kolumn w pliku CSV.
Możesz zmodyfikować zmienną "$Path" bezpośrednio w programie PowerShell, używając ścieżki pliku CSV, na wypadek, gdyby eksport nie był interaktywny.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Pobiera aplikację z katalogu. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Pobiera właścicieli aplikacji z katalogu. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby poznać inne przykłady dla programu PowerShell dotyczące zarządzania aplikacjami, zobacz [przykłady programu Azure AD PowerShell dotyczące zarządzania aplikacjami](../app-management-powershell-samples.md).
