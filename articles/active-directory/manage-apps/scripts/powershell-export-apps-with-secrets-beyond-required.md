---
title: Przykład programu PowerShell — eksportowanie aplikacji z wpisami tajnymi i certyfikatami, które w dzierżawie Azure Active Directory wymagane.
description: Przykład programu PowerShell, który eksportuje wszystkie aplikacje z wpisami tajnymi i certyfikatami, które wygasają po upływie wymaganej daty dla określonych aplikacji w Azure Active Directory dzierżawie.
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
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375393"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Eksportowanie aplikacji z wpisami tajnymi i certyfikatami, które wygasają po upływie wymaganej daty

Ten przykładowy skrypt programu PowerShell eksportuje wszystkie wpisy tajne i certyfikaty rejestracji aplikacji wygasające poza wymagany okres dla określonych aplikacji z katalogu w pliku CSV w sposób nieinterakcyjny.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt działa nieinterakcyjnie. Używający go administrator będzie musiał zmienić wartości w sekcji "#PARAMETERS TO CHANGE" z własnym identyfikatorem aplikacji, kluczem tajnym aplikacji, nazwą dzierżawy, okresem wygaśnięcia poświadczeń aplikacji i ścieżką, w której zostanie wyeksportowany plik CSV.
Ten skrypt używa [przepływu Client_Credential Oauth.](../../develop/v2-oauth2-client-creds-grant-flow.md) Funkcja "RefreshToken" skompilowała token dostępu na podstawie wartości parametrów zmodyfikowanych przez administratora.

Polecenie "Add-Member" jest odpowiedzialne za tworzenie kolumn w pliku CSV.

| Polecenie | Uwagi |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Wysyła żądania HTTP i HTTPS do strony internetowej lub usługi internetowej. Analizuje odpowiedź i zwraca kolekcje linków, obrazów i innych znaczących elementów HTML. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure AD PowerShell, zobacz Omówienie modułu [Azure AD PowerShell](/powershell/azure/active-directory/overview).

Aby uzyskać inne przykłady programu PowerShell dotyczące zarządzania aplikacją, zobacz [Azure AD PowerShell examples for Application Management (Przykłady programu Azure AD PowerShell do zarządzania aplikacją).](../app-management-powershell-samples.md)
