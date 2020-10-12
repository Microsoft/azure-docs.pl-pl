---
title: Polecenia cmdlet programu PowerShell usługi Azure AD dla raportowania | Microsoft Docs
description: Informacje o poleceniach cmdlet programu PowerShell usługi Azure AD na potrzeby raportowania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4353ed5413f76b13425a59d31bb6108542e3bd23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89231133"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Polecenia cmdlet programu PowerShell usługi Azure AD do raportowania

> [!NOTE] 
> Te polecenia cmdlet programu PowerShell działają obecnie tylko z modułem [wersji zapoznawczej usługi Azure AD](/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) . Należy pamiętać, że moduł w wersji zapoznawczej nie jest sugerowany do użycia w środowisku produkcyjnym. 

Aby zainstalować publiczną wersję zapoznawczą, użyj poniższego programu. 

```powershell
Install-module AzureADPreview
```

Aby uzyskać więcej informacji na temat nawiązywania połączenia z usługą Azure AD przy użyciu programu PowerShell, zobacz artykuł [Azure AD PowerShell dla programu Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Dzięki raportom Azure Active Directory (Azure AD) możesz uzyskać szczegółowe informacje o działaniach związanych ze wszystkimi operacjami zapisu w Twoim kierunku (dzienniki inspekcji) i danych uwierzytelniania (dzienniki logowania). Chociaż informacje są dostępne za pomocą programu MS interfejs API programu Graph, teraz można pobrać te same dane przy użyciu poleceń cmdlet programu PowerShell usługi Azure AD na potrzeby raportowania.

Ten artykuł zawiera omówienie poleceń cmdlet programu PowerShell, które mają być używane na potrzeby dzienników inspekcji i dzienników logowania.

## <a name="audit-logs"></a>Dzienniki inspekcji

[Dzienniki inspekcji](concept-audit-logs.md) zapewniają możliwość śledzenia za pomocą dzienników dla wszystkich zmian wykonywanych przez różne funkcje w ramach usługi Azure AD. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

Dostęp do dzienników inspekcji uzyskuje się za pomocą polecenia cmdlet Get-AzureADAuditDirectoryLogs.


| Scenariusz                      | Polecenie programu PowerShell |
| :--                           | :--                |
| Nazwa wyświetlana aplikacji      | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/App/displayName EQ" Azure AD Cloud Sync "" |
| Kategoria                      | Get-AzureADAuditDirectoryLogs-Filter "ApplicationManagement" " |
| Data i godzina działania            | Get-AzureADAuditDirectoryLogs-Filter "activityDateTime gt 2019-04-18" |
| Wszystkie powyższe              | Get-AzureADAuditDirectoryLogs — filtr "initiatedBy/App/displayName EQ" Azure AD Cloud Sync "oraz kategorii EQ" ApplicationManagement "i activityDateTime gt 2019-04-18"|


Na poniższej ilustracji przedstawiono przykład tego polecenia. 

![Przycisk "podsumowanie danych"](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Dzienniki logowania

Dzienniki [logowania](concept-sign-ins.md) zawierają informacje dotyczące użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

Dostęp do dzienników logowania można uzyskać za pomocą polecenia cmdlet "Get-AzureADAuditSignInLogs".


| Scenariusz                      | Polecenie programu PowerShell |
| :--                           | :--                |
| Nazwa wyświetlana użytkownika             | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ Tymotka Perkins" " |
| Data i godzina utworzenia              | Get-AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (wszystko od 5:30 PM na 4/18) |
| Stan                        | Get-AzureADAuditSignInLogs-Filter "status/errorCode EQ 50105" |
| Nazwa wyświetlana aplikacji      | Get-AzureADAuditSignInLogs-Filter "appDisplayName EQ StoreFrontStudio [wsfed Enabled]" " |
| Wszystkie powyższe              | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ Tymotka Perkins" i status/errorCode ne 0 i appDisplayName EQ "StoreFrontStudio [wsfed Enabled]" " |


Na poniższej ilustracji przedstawiono przykład tego polecenia. 

![Przycisk "podsumowanie danych"](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Następne kroki

- [Przegląd raportów usługi Azure AD](overview-reports.md).
- [Raport dotyczący dzienników inspekcji](concept-audit-logs.md). 
- [Programistyczny dostęp do raportów usługi Azure AD](concept-reporting-api.md)