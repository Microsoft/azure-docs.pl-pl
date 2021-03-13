---
title: Przykład programu PowerShell — eksportowanie aplikacji z wygaśniemi wpisami tajnymi i certyfikatami w dzierżawie Azure Active Directory.
description: Przykład programu PowerShell, który eksportuje wszystkie aplikacje z wpisami tajnymi i certyfikatami dla określonych aplikacji w dzierżawie Azure Active Directory.
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
ms.openlocfilehash: def9b55a1d873cccda5d1c48921e3f098beeced1
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149719"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Eksportowanie aplikacji z wygaśniemi wpisami tajnymi i certyfikatami

Ten przykładowy skrypt programu PowerShell eksportuje wszystkie rejestracje aplikacji z wygasłymi wpisami tajnymi, certyfikatami i ich właścicielami dla określonych aplikacji z katalogu w pliku CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ten przykład wymaga [AzureAD v2 PowerShell for Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) lub [AzureAD v2 PowerShell dla programu Graph (wersja zapoznawcza](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) ) (AzureADPreview).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Skryptu można używać bezpośrednio bez żadnych modyfikacji. Administrator zostanie poproszony o datę wygaśnięcia i informacje o tym, czy chcesz zobaczyć już wygasłe wpisy tajne lub certyfikaty.

Polecenie "Add-member" jest odpowiedzialne za tworzenie kolumn w pliku CSV.
Polecenie "New-Object" tworzy obiekt, który będzie używany dla kolumn w pliku CSV.
Możesz zmodyfikować zmienną "$Path" bezpośrednio w programie PowerShell, używając ścieżki pliku CSV, na wypadek, gdyby eksport nie był interaktywny.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Pobiera aplikację z katalogu. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Pobiera właścicieli aplikacji z katalogu. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby poznać inne przykłady dla programu PowerShell dotyczące zarządzania aplikacjami, zobacz [przykłady programu Azure AD PowerShell dotyczące zarządzania aplikacjami](../app-management-powershell-samples.md).
