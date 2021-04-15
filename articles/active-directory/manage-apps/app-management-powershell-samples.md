---
title: Przykłady programu PowerShell Azure Active Directory zarządzania aplikacją
description: Te przykłady programu PowerShell są używane dla aplikacji, które zarządzasz w Azure Active Directory dzierżawie. Możesz użyć tych przykładowych skryptów, aby znaleźć informacje o wygasaniu wpisów tajnych i certyfikatów.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 6b6314935bfafc2fe6288c30619e1d01242a991d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378826"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory przykłady programu PowerShell do zarządzania aplikacją

W poniższej tabeli przedstawiono linki do przykładów skryptów programu PowerShell dla usługi Azure AD Application Management. Te przykłady wymagają:
- Moduł [AzureAD V2 PowerShell dla programu Graph lub](/powershell/azure/active-directory/install-adv2)
- Program [AzureAD V2 PowerShell dla modułu Graph w](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)wersji zapoznawczej , chyba że określono inaczej.

Aby uzyskać więcej informacji o poleceniach cmdlet używanych w tych przykładach, zobacz [Aplikacje](/powershell/module/azuread/#applications).

| Link | Opis |
|---|---|
|**Skrypty zarządzania aplikacją**||
| [Eksportowanie wpisów tajnych i certyfikatów (rejestracje aplikacji)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Eksportowanie wpisów tajnych i certyfikatów dla rejestracji aplikacji w Azure Active Directory dzierżawie. |
| [Eksportowanie wpisów tajnych i certyfikatów (aplikacje dla przedsiębiorstw)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Eksportowanie wpisów tajnych i certyfikatów dla aplikacji przedsiębiorstw w Azure Active Directory dzierżawy. |
| [Eksportowanie wygasających wpisów tajnych i certyfikatów](scripts/powershell-export-apps-with-expriring-secrets.md) | Wyeksportuj rejestracje aplikacji z wygasających wpisów tajnych i certyfikatów oraz ich Azure Active Directory dzierżawy. |
| [Eksportowanie wpisów tajnych i certyfikatów wygasających poza datą wymaganą](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Wyeksportuj rejestracje aplikacji z wpisami tajnymi i certyfikatami, które w dzierżawie Azure Active Directory wymagane. W tym celu jest używany nieinterakcyjny Client_Credentials Oauth. |
