---
title: Znane problemy — Azure Digital bliźniaczych reprezentacji
description: Uzyskaj pomoc w rozpoznawaniu i eliminowaniu znanych problemów z usługą Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044145"
---
# <a name="known-issues-in-azure-digital-twins"></a>Znane problemy w usłudze Azure Digital bliźniaczych reprezentacji

Ten artykuł zawiera informacje o znanych problemach związanych z usługą Azure Digital bliźniaczych reprezentacji.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 błąd klienta: Nieprawidłowe żądanie" w Cloud Shell

Polecenia w Cloud Shell mogą sporadycznie kończyć się niepowodzeniem z powodu błędu "400 błąd klienta: Nieprawidłowe żądanie adresu URL: http://localhost:50342/oauth2/token ", po którym następuje pełny ślad stosu.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Można to rozwiązać przez ponowne uruchomienie `az login` polecenia i zakończenie kolejnych kroków logowania.

Następnie powinno być możliwe ponowne uruchomienie polecenia.

### <a name="possible-causes"></a>Możliwe przyczyny

Jest to wynik znanego problemu w Cloud Shell: [*pobieranie tokenu z Cloud Shell sporadycznie kończy się niepowodzeniem z powodu błędu 400 klienta: Nieprawidłowe żądanie*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat zabezpieczeń i uprawnień w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)