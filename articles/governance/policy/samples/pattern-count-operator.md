---
title: 'Wzorzec: operator Count w definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład użycia operatora Count w definicji zasad.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 1339dff7f8bc92a8e38ec5635690cc2069dd8df4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005422"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Wzorzec Azure Policy: operator Count

Operator [Count](../concepts/definition-structure.md#count) oblicza elementy członkowskie \[ \* \] aliasu.

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad przeprowadza [inspekcję](../concepts/effects.md#audit) sieciowych grup zabezpieczeń skonfigurowanych tak, aby zezwalały na ruch przychodzący Remote Desktop Protocol (RDP).

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Wyjaśnienie

Podstawowe składniki operatora **Count** to _pola_, _gdzie_ i warunek. Każdy z nich został wyróżniony w poniższym fragmencie kodu.

- _pole_ wskazuje, który [alias](../concepts/definition-structure.md#aliases) ma być obliczany przez członków. Tutaj szukamy _tablicy_ aliasów **securityRules \[ \* \]** sieciowej grupy zabezpieczeń.
- _gdzie_ używa języka zasad do definiowania, które elementy członkowskie _tablicy_ spełniają kryteria. W tym przykładzie operator logiczny **allOf** grupuje trzy różne oceny warunku właściwości _tablicy_ aliasów: _kierunek_, _dostęp_ i _destinationPortRange_.
- Warunek liczby w tym przykładzie jest **większy**. Licznik ma wartość true, jeśli co najmniej jeden element członkowski _tablicy_ aliasu jest zgodny z klauzulą _WHERE_ .

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).