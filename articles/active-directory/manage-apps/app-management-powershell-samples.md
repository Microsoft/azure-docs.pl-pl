---
title: Przykłady programu PowerShell dla Azure Active Directory zarządzania aplikacjami
description: Te przykłady dla programu PowerShell są używane w przypadku aplikacji zarządzanych w dzierżawie Azure Active Directory. Za pomocą tych przykładowych skryptów można znaleźć informacje o wygasaniu dla wpisów tajnych i certyfikatów.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 46297f7f0f648c8bebc887a9093e25dfea99f695
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561504"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Przykłady programu Azure Active Directory PowerShell dla zarządzania aplikacjami

Poniższa tabela zawiera linki do przykładów skryptów programu PowerShell do zarządzania aplikacjami usługi Azure AD. Te przykłady wymagają:
- [Moduł AzureAD v2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) lub,
- [Wersja zapoznawcza modułu AzureAD v2 PowerShell dla programu Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), chyba że wskazano inaczej.

Aby uzyskać więcej informacji o poleceniach cmdlet używanych w tych przykładach, zobacz [aplikacje](/powershell/module/azuread/#applications).

| Link | Opis |
|---|---|
|**Skrypty zarządzania aplikacjami**||
| [Eksportowanie wpisów tajnych i certyfikatów (rejestracje aplikacji)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Eksportowanie wpisów tajnych i certyfikatów dla rejestracji aplikacji w dzierżawie Azure Active Directory. |
| [Eksportowanie wpisów tajnych i certyfikatów (aplikacje dla przedsiębiorstw)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Eksportuj wpisy tajne i certyfikaty dla aplikacji dla przedsiębiorstw w dzierżawie Azure Active Directory. |
| [Eksportuj wpisy tajne i certyfikaty wygasające](scripts/powershell-export-apps-with-expriring-secrets.md) | Eksportuj aplikacje z wygaśniemi wpisami tajnymi i certyfikatami w dzierżawie Azure Active Directory. |
| [Eksport wpisów tajnych i certyfikatów wygasających poza datą wymaganą](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Eksportuj aplikacje z kluczami tajnymi i certyfikatami, które wygasają poza wymaganą datą w dzierżawie Azure Active Directory. |
