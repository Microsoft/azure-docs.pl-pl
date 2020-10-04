---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711409"
---
| Właściwość | Opis |
|:--- |:---|
|**tożsamość/typ** | Typ uwierzytelniania, który został użyty do wysłania żądania. Na przykład: `OAuth` , `SAS Key` , `Account Key` lub `Anonymous` |
|**tożsamość/tokenHash**|To pole jest zarezerwowane wyłącznie do użytku wewnętrznego. |
|**Autoryzacja/akcja** | Akcja, która jest przypisana do żądania. |
|**Autoryzacja/roleAssignmentId** | Identyfikator przypisania roli. Przykład: `4e2521b7-13be-4363-aeda-111111111111`.|
|**Autoryzacja/zduplikowanych** | Identyfikator definicji roli. Przykład: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**nazwy główne/identyfikator** | Identyfikator podmiotu zabezpieczeń. Przykład: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**podmioty zabezpieczeń/typ** | Typ podmiotu zabezpieczeń. Przykład: `ServicePrincipal`. |
|**żądający/appID** | Identyfikator aplikacji Open Authorization (OAuth) używany jako obiekt żądający. <br> Przykład: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**Obiekt żądający/odbiorcy** | Odbiorcy protokołu OAuth żądania. Przykład: `https://storage.azure.com`. |
|**Obiekt żądający/objectId** | Identyfikator obiektu OAuth osoby żądającej. W przypadku uwierzytelniania przy użyciu protokołu Kerberos reprezentuje identyfikator obiektu uwierzytelnionego użytkownika protokołu Kerberos. Przykład: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**Obiekt żądający/tenantId** | Identyfikator dzierżawy OAuth tożsamości. Przykład: `72f988bf-86f1-41af-91ab-222222222222`.|
|**Obiekt żądający/tokenIssuer** | Wystawca tokenu OAuth. Przykład: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**zleceniodawca/nazwa UPN** | Główna nazwa użytkownika (UPN) obiektu żądającego. Przykład: `someone@contoso.com`. |
|**Obiekt żądający/nazwa użytkownika** | To pole jest zarezerwowane wyłącznie do użytku wewnętrznego.|