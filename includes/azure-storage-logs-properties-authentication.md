---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612723"
---
| Właściwość | Opis |
|:--- |:---|
|**tożsamość/typ** | Typ uwierzytelniania, który został użyty do wysłania żądania. Na przykład: `OAuth` , `Kerberos` , `SAS Key` , `Account Key` lub `Anonymous` |
|**tożsamość/tokenHash**|Skrót SHA-256 tokenu uwierzytelniania używanego w żądaniu. <br>Gdy typem uwierzytelniania jest `Account Key` , format jest "Klucz1 \| KLUCZ2 (SHA256 hash klucza)". Na przykład: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>Gdy typem uwierzytelniania jest `SAS Key` , format jest "Klucz1 \| klucz2 (skrót SHA 256 klucza), SasSignature (skrót SHA 256 tokenu sygnatury dostępu współdzielonego)". Na przykład: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. Gdy typem uwierzytelniania jest `OAuth` , format jest "skrót SHA 256 tokenu OAuth". Na przykład: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> W przypadku innych typów uwierzytelniania nie ma pola tokenHash. |
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
