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
ms.openlocfilehash: 9c0e5508830343561833785fbce31f547a8a7428
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149685"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Eksportowanie aplikacji z użyciem wpisów tajnych i certyfikatów wygasających poza wymaganą datą

Ten przykładowy skrypt programu PowerShell eksportuje wszystkie wpisy tajne rejestracji aplikacji i certyfikaty, które wygasają po upływie wymaganego okresu dla określonych aplikacji z katalogu w pliku CSV, nieinteraktywnie.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt działa nieinteraktywnie. Administrator korzystający z tego programu będzie musiał zmienić wartości w sekcji "#PARAMETERS do zmiany" z IDENTYFIKATORem aplikacji, wpisem tajnym aplikacji, nazwą dzierżawy, okresem ważności poświadczeń aplikacji oraz ścieżką, w której będzie eksportowany wolumin CSV.
Ten skrypt używa [przepływu Client_Credential OAuth](../../develop/v2-oauth2-client-creds-grant-flow.md) funkcja "RefreshToken" kompiluje token dostępu na podstawie wartości parametrów zmodyfikowanych przez administratora.

Polecenie "Add-member" jest odpowiedzialne za tworzenie kolumn w pliku CSV.

| Polecenie | Uwagi |
|---|---|
| [Invoke-żądanie](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) | Wysyła żądania HTTP i HTTPS do strony sieci Web lub usługi sieci Web. Analizuje odpowiedź i zwraca kolekcje linków, obrazów i innych istotnych elementów HTML. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz [Omówienie modułu programu Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby poznać inne przykłady dla programu PowerShell dotyczące zarządzania aplikacjami, zobacz [przykłady programu Azure AD PowerShell dotyczące zarządzania aplikacjami](../app-management-powershell-samples.md).
