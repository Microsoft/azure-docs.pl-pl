---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e28dc20fe5e789bbaa5aad9402d37f039eabe6e5
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499150"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Udostępnione pulpity nawigacyjne nie powinny mieć kafelków markdown z zawartością w tekście](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Nie zezwalaj na tworzenie udostępnionego pulpitu nawigacyjnego, który zawiera zawartość w tekście na kafelkach markdown, i wymuszanie, aby zawartość była przechowywana jako plik markdown hostowany w trybie online. Jeśli używasz zawartości wbudowanej na kafelku markdown, nie możesz zarządzać szyfrowaniem zawartości. Konfigurując własny magazyn, możesz szyfrować, szyfrować dwukrotnie, a nawet korzystać z własnych kluczy. Włączenie tych zasad ogranicza użytkownikom możliwość korzystania z interfejsu API REST udostępnionych pulpitów nawigacyjnych w wersji 2020-09-01-preview lub powyższej. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
