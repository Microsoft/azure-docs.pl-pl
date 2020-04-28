---
title: Możliwości tworzenia tokenów łańcucha bloków platformy Azure
description: Możliwość tworzenia tokenów łańcucha bloków na platformie Azure zapewnia elastyczność w tworzeniu tokenów dla zaawansowanych scenariuszy.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74325120"
---
# <a name="azure-blockchain-tokens-composability"></a>Możliwości tworzenia tokenów łańcucha bloków platformy Azure

[!INCLUDE [Preview note](./includes/preview.md)]

Możliwość tworzenia tokenów jest elastyczna, aby tworzyć tokeny dla zaawansowanych scenariuszy. Może istnieć złożony scenariusz, którego nie można zaimplementować przy użyciu [czterech wstępnie skompilowanych szablonów tokenów](templates.md#base-token-types). Możliwość tworzenia tokenów umożliwia projektowanie własnych szablonów tokenów przez dodanie lub usunięcie określonych zachowań w celu utworzenia własnego szablonu tokenu. Podczas tworzenia nowego szablonu tokenu tokeny usługi Azure łańcucha bloków sprawdza wszystkie reguły gramatyki tokenu. Szablony złożone są zapisywane w usłudze tokenów łańcucha bloków Azure w celu wystawiania w połączonych sieciach łańcucha bloków.

Możesz użyć [zachowań tokenu](templates.md#token-behaviors) w poniższych sekcjach, aby zaprojektować szablon tokenu.

## <a name="burnable-b"></a>Wypalanie (b)

Możliwość usuwania tokenów z dostawy.

Na przykład po zrealizowaniu punktów karty kredytowej w trybie online dla karty upominkowej punkty karty kredytowej są nagrane.

## <a name="delegable-g"></a>Delegowalne (g)

Możliwość delegowania akcji podjętych na własnym tokenie.

Delegat może wykonywać akcje jako właściciel tokenu. Na przykład można użyć tokenu delegowalne do zaimplementowania głosu. Token delegowalne umożliwia właścicielowi tokenu głosu, aby ktoś inny zagłosować w ich imieniu.

## <a name="logable-l"></a>Logable (l)

Możliwość rejestrowania.

Na przykład można wydać token logable dla dystrybucji filmów do każdego kina pokazującego konkretny film. W przypadku filmu, który ma być odtwarzany, Pokazywanie musi rejestrować transakcję dla każdego z nich, ponieważ wypłaty za tantiemy są pokazywane podczas uruchamiania filmu. Kompilacje aktorów mogą używać tokenów filmów do sprawdzania, czy wypłaty na film są widoczne dla każdego kina w dystrybucji.

## <a name="mint-able-m"></a>Mennic — możliwość (m)

Możliwość mennic dodatkowych tokenów dla klasy tokenów. Rola Minter obejmuje zachowanie mintable.

Na przykład firma detaliczna, która chce zaimplementować program lojalnościowy, może używać tokenów mintable dla programu lojalnościowego. Mogą oni mennic dodatkowe punkty lojalnościowe dla swoich klientów w miarę wzrostu ich bazy klientów.  

## <a name="non-subdividable-or-whole-d"></a>Bez podziału lub całości (~ d)

Ograniczenie, aby zapobiec poddzieleniu tokenu na mniejsze części.

Na przykład nie można podzielić pojedynczego elementu ozdobnego na kilka mniejszych części. 

## <a name="non-transferable-t"></a>Niezbywalne (~ t)

Ograniczenie, aby uniemożliwić zmianę własności od początkowego właściciela tokenu.

Na przykład, dyplom University jest tokenem nieprzekazującym. Po nadaniu dyplomu do szkoły nie można jej przenieść z szkoły do innej osoby.

## <a name="roles-r"></a>Role (r)

Możliwość definiowania ról w klasie szablonu tokenu dla określonych zachowań.

Możesz podać listę nazw ról, które token obsługuje w czasie tworzenia tokenu. Po określeniu ról użytkownik może przypisać role do tych zachowań. Obecnie obsługiwana jest tylko rola Minter.

## <a name="singleton-s"></a>Pojedyncze (s)

Ograniczenie zezwalające na dostarczenie jednego tokenu.

Na przykład artefakt muzeów jest pojedynczym tokenem. Artefakty muzeów są unikatowe. Token reprezentujący artefakt ma tylko jeden element w dostawie.

## <a name="subdividable-d"></a>Poddzielenie (d)

Możliwość dzielenia tokenu na mniejsze części.

Na przykład Dolar może być podzielony na centy.

## <a name="transferable-t"></a>Przetransferowane (t)

Możliwość przeniesienia własności tokenu.

Na przykład tytuł właściwości jest tokenem możliwym do przeniesienia, który może zostać przesłany od jednej osoby do innej podczas sprzedaży właściwości.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [zarządzania kontami usługi Azure łańcucha bloków Tokens](account-management.md).
