---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 961762559ba27deee519ad3a9516bc351ca724f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605582"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Udostępnione pulpity nawigacyjne nie powinny zawierać kafelków promocji z zawartością wbudowaną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Nie Zezwalaj na tworzenie udostępnionego pulpitu nawigacyjnego, który ma zawartość wbudowaną na kafelkach promocji, i Wymuś, że zawartość powinna być przechowywana jako plik promocji, który jest hostowany w trybie online. Jeśli używasz zawartości wbudowanej na kafelku promocji, nie możesz zarządzać szyfrowaniem zawartości. Konfigurując własny magazyn, możesz zaszyfrować, dwukrotnie szyfrować i nawet przenieść własne klucze. Włączenie tych zasad ogranicza użytkownikom możliwość korzystania z wersji 2020-09-01-Preview lub nowszej udostępnianej przez interfejs API REST udostępnionych pulpitów nawigacyjnych. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
