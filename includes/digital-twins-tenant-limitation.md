---
author: baanders
description: uwzględnij plik opisujący ograniczenie między dzierżawami z Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589391"
---
W związku z tym żądania do interfejsów API Azure Digital Twins wymagają użytkownika lub jednostki usługi, która jest częścią tej samej dzierżawy, w której Azure Digital Twins wystąpienie usługi. Aby zapobiec złośliwemu skanowaniu Azure Digital Twins końcowych, żądania z tokenami dostępu spoza dzierżawy pochodzącej zostaną zwrócone z komunikatem o błędzie "Nie znaleziono Sub-Domain 404". Ten błąd zostanie  Azure Digital Twins zwrócony nawet wtedy, gdy użytkownik lub nazwa główna usługi [](../articles/digital-twins/concepts-security.md) otrzymała rolę właściciela danych lub Azure Digital Twins czytelnika danych w usłudze [Azure AD B2B.](../articles/active-directory/external-identities/what-is-b2b.md) 