---
title: 'Wzorzec: operator Count w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład użycia operatora Count w definicji zasad.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 807890b7fb08d790deff6e0be9e08ad91c4ec44d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565753"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Wzorzec Azure Policy: operator Count

Operator [Count](../concepts/definition-structure.md#count) oblicza elementy członkowskie \[ \* \] aliasu.

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad przeprowadza [inspekcję](../concepts/effects.md#audit) sieciowych grup zabezpieczeń skonfigurowanych tak, aby zezwalały na ruch przychodzący Remote Desktop Protocol (RDP).

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Objaśnienie

Podstawowe składniki operatora **Count** to _pola_, _gdzie_i warunek. Każdy z nich został wyróżniony w poniższym fragmencie kodu.

- _pole_ wskazuje, który [alias](../concepts/definition-structure.md#aliases) ma być obliczany przez członków. Tutaj szukamy _tablicy_ aliasów **securityRules \[ \* \] ** sieciowej grupy zabezpieczeń.
- _gdzie_ używa języka zasad do definiowania, które elementy członkowskie _tablicy_ spełniają kryteria. W tym przykładzie operator logiczny **allOf** grupuje trzy różne oceny warunku właściwości _tablicy_ aliasów: _kierunek_, _dostęp_i _destinationPortRange_.
- Warunek liczby w tym przykładzie jest **większy**. Licznik ma wartość true, jeśli co najmniej jeden element członkowski _tablicy_ aliasu jest zgodny z klauzulą _WHERE_ .

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).