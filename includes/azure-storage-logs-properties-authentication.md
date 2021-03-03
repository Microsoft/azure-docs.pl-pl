---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 61576de4a57d55ea9d1ea209c52df556f0069617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750644"
---
| Właściwość | Opis |
|:--- |:---|
|**tożsamość/typ** | Typ uwierzytelniania, który został użyty do wysłania żądania. Na przykład: `OAuth` , `Kerberos` , `SAS Key` , `Account Key` lub `Anonymous` |
|**tożsamość/tokenHash**|To pole jest zarezerwowane wyłącznie do użytku wewnętrznego. |
|**Autoryzacja/akcja** | Akcja, która jest przypisana do żądania. |
|**Autoryzacja/roleAssignmentId** | Identyfikator przypisania roli. Na przykład: `4e2521b7-13be-4363-aeda-111111111111`.|
|**Autoryzacja/zduplikowanych** | Identyfikator definicji roli. Na przykład: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**nazwy główne/identyfikator** | Identyfikator podmiotu zabezpieczeń. Na przykład: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**podmioty zabezpieczeń/typ** | Typ podmiotu zabezpieczeń. Na przykład: `ServicePrincipal`. |
|**żądający/appID** | Identyfikator aplikacji Open Authorization (OAuth) używany jako obiekt żądający. <br> Na przykład: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**Obiekt żądający/odbiorcy** | Odbiorcy protokołu OAuth żądania. Na przykład: `https://storage.azure.com`. |
|**Obiekt żądający/objectId** | Identyfikator obiektu OAuth osoby żądającej. W przypadku uwierzytelniania przy użyciu protokołu Kerberos reprezentuje identyfikator obiektu uwierzytelnionego użytkownika protokołu Kerberos. Na przykład: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**Obiekt żądający/tenantId** | Identyfikator dzierżawy OAuth tożsamości. Na przykład: `72f988bf-86f1-41af-91ab-222222222222`.|
|**Obiekt żądający/tokenIssuer** | Wystawca tokenu OAuth. Na przykład: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**zleceniodawca/nazwa UPN** | Główna nazwa użytkownika (UPN) obiektu żądającego. Na przykład: `someone@contoso.com`. |
|**Obiekt żądający/nazwa użytkownika** | To pole jest zarezerwowane wyłącznie do użytku wewnętrznego.|
