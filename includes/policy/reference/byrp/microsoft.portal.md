---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4d419d8e338228132a4aa8a32b7e29d8fa2f46ca
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425462"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Udostępnione pulpity nawigacyjne nie powinny zawierać kafelków promocji z zawartością wbudowaną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Nie Zezwalaj na tworzenie udostępnionego pulpitu nawigacyjnego, który ma zawartość wbudowaną na kafelkach promocji, i Wymuś, że zawartość powinna być przechowywana jako plik promocji, który jest hostowany w trybie online. Jeśli używasz zawartości wbudowanej na kafelku promocji, nie możesz zarządzać szyfrowaniem zawartości. Konfigurując własny magazyn, możesz zaszyfrować, dwukrotnie szyfrować i nawet przenieść własne klucze. Włączenie tych zasad ogranicza użytkownikom możliwość korzystania z wersji 2020-09-01-Preview lub nowszej udostępnianej przez interfejs API REST udostępnionych pulpitów nawigacyjnych. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
