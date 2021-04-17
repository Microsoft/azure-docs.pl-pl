---
title: 'Azure Digital Twins nie powiodło się ze stanem: 404 Sub-Domain nie znaleziono'
description: 'Przyczyny i rozwiązania problemu "Żądanie obsługi nie powiodło się. Stan: 404 Sub-Domain nie znaleziono" na Azure Digital Twins.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590284"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>Żądanie obsługi nie powiodło się. Stan: Nie znaleziono Sub-Domain 404

W tym artykule opisano przyczyny i kroki rozwiązywania problemu w przypadku otrzymania błędu 404 z żądań obsługi do Azure Digital Twins. 

## <a name="symptoms"></a>Objawy

Ten błąd może wystąpić podczas uzyskiwania dostępu do wystąpienia Azure Digital Twins przy użyciu jednostki usługi lub konta użytkownika, które należy do innej [dzierżawy usługi Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) niż wystąpienie. Prawidłowe role [wydają](concepts-security.md) się być przypisane do tożsamości, ale żądania interfejsu API nie powiodą się ze stanem błędu `404 Sub-Domain not found` .

## <a name="causes"></a>Przyczyny

### <a name="cause-1"></a>Przyczyna #1

Azure Digital Twins wymaga, aby wszyscy uwierzytelniający użytkownicy należeli do tej samej dzierżawy usługi Azure AD co Azure Digital Twins wystąpienia.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Rozwiązania

### <a name="solution-1"></a>Rozwiązanie #1

Ten problem można rozwiązać, mając każdą tożsamość federacyjną z innej dzierżawy żądać **tokenu** od Azure Digital Twins dzierżawy "home" wystąpienia usługi . 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>Rozwiązanie #2

Jeśli używasz klasy w kodzie i nadal napotykasz ten problem po otrzymaniu tokenu, możesz określić dzierżawę domową w opcjach, aby wyjaśnić dzierżawę nawet wtedy, gdy uwierzytelnianie zostanie domyślnie włączone do innego `DefaultAzureCredential` `DefaultAzureCredential` typu.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat zabezpieczeń i uprawnień na Azure Digital Twins:
* [*Pojęcia: Zabezpieczenia dla Azure Digital Twins rozwiązań*](concepts-security.md)
