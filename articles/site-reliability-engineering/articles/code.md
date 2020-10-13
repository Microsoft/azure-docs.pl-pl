---
title: 'Często zadawane pytania: SRE i kodowanie | Microsoft Docs'
titleSuffix: Azure
description: 'Często zadawane pytania: zrozumienie relacji między SRE i kodowaniem'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089075"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Często zadawane pytania: Czy muszę dowiedzieć się, jak kod, aby uzyskać dostęp do SRE?

W przypadku, gdy osoby rozważają zaangażowanie w SRE i zespoły mają na celu dostosowanie się do SRE praktyk, często zadawane pytanie "czy musisz wiedzieć, jak kod?"

Krótka odpowiedź: tak. 

Ale Pełna odpowiedź jest nieco większa złożonych. Przyjrzyjmy się do trzech miejsc, w których kodowanie jest odtwarzane w ramach inżynierów niezawodności lokacji wraz z poziomem wiedzy fachowej wymaganej dla każdego z nich. Ta lista nie jest kompletna, ale te scenariusze stanowią kilka typowych przypadków użycia.

## <a name="scenario-1-removing-toil-through-automation"></a>Scenariusz 1: usuwanie Toil za poorednictwem automatyzacji

Inżynierowie niezawodności witryny i inni, którzy korzystają z praktyk SRE, podejmują próbę, gdy to możliwe, aby usunąć Toil. "Toil" oznacza konkretną rzecz w SRE. Toil odnosi się do działania wykonywanego przez człowieka, który ma pewne cechy. Trud nie ma długoterminowej wartości realizacji. Nie posuwa usługi do przodu w żaden znaczący sposób. Jest często powtarzalny i w dużej mierze ręczny (chociaż można go zautomatyzować). W miarę wzrostu usługi lub systemów w czasie prawdopodobnie proporcjonalnie wzrośnie liczba żądań dla danego systemu, co będzie przekładać się na jeszcze więcej zadań wykonywanych ręcznie.

Na przykład, jeśli usługa wymaga zespołu SRE do zresetowania czegoś co tydzień lub do aprowizacji nowych kont i miejsca na dysku, lub wielokrotnie ponownie uruchamiać je ręcznie — jest to obciążenie operacyjne, które jest Toil. Wykonanie tych czynności nie poprawi usługi w jakikolwiek długoterminowy, trwały sposób. Te czynności prawdopodobnie będą musiały być powtarzane wielokrotnie.

Inżynierowie ds. niezawodności witryny nienawidzą trudu. Pracują, aby go wyeliminować tam, gdzie jest to możliwe i stosowne. Jest to jedno z miejsc, w których do gry wkracza automatyzacja w SRE. Jeśli te żądania mogą być obsługiwane automatycznie, dzięki czemu zespół będzie mógł pracować nad większymi nagrodami i wpływać na siebie.

*Znajomość kodu*: Automatyzacja wymaga pewnej znajomości kodu, ale nie musi wymagać pełnego umiejętności inżynieryjnych. Jeśli możesz pisać małe skrypty (prawdopodobnie w programie PowerShell lub powłoki Bourne), a nawet jeśli utworzysz [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) kod przy użyciu stanowią jedynie ułamek dowolnego kodu, ta aplikacja nadal może pomóc wyeliminować Toil.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>Scenariusz 2: sterowanie za poorednictwem interfejsów API/języków specyficznych dla domeny (językami DSL)/templates

Chociaż nie jest to absolutnie konieczne w przypadku pracy z SRE, możliwość kontrolowania środowisk za pośrednictwem interfejsów API, językami DSL i szablonów (szczególnie w środowiskach chmurowych) umożliwia SREs skalowanie w górę ich pracy. Obsługa administracyjna/dezaktywowanie infrastruktury, konfigurowanie monitorowania i integrowanie kilku usług stało się znacznie bardziej wydajne poprzez kodowanie.

*Znajomość kodowania*: podobnie jak w poprzednim scenariuszu, wymaga to pewnej znajomości kodu, ale nie musi to wymagać pełnych umiejętności inżynieryjnych. Oprócz skryptów i aplikacji logiki wymienionych wcześniej [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) mogą być również używane z minimalnym doświadczeniem w kodowaniu.

## <a name="scenario-3-fixing-the-code"></a>Scenariusz 3: naprawianie kodu

Inżynierowie niezawodności lokacji pomogą poprawić niezawodność systemu. Ten cel czasami wymaga przeszukiwanie stosów w kodzie źródłowym systemu, określenie problemu i często przyczynia się do usunięcia poprawki z powrotem do bazy kodu. Mimo że poziom złożoności tej pracy może się różnić w zależności od sytuacji, w takich przypadkach specjalistyczne doświadczenie w zakresie kodowania jest określone.

*Znajomość kodu*: w tym scenariuszu często jest wymagana pełna specjalizacja inżyniera oprogramowania.


## <a name="next-steps"></a>Następne kroki

Chcesz dowiedzieć się więcej o niezawodności witryny i pracy z niską ilością kodu? Zapoznaj się z naszym [centrum inżynierów niezawodności lokacji](../index.yml), połączoną powyżej dokumentacją produktu.
