---
title: 'Wzorzec: definicje zasad grupy z inicjatywami'
description: Ten Azure Policy wzorzec zawiera przykład sposobu grupowania definicji zasad w ramach inicjatywy
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172856"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Wzorzec Azure Policy: definicje zasad grupy

Inicjatywa jest grupą definicji zasad. Grupując definicje zasad powiązane w jeden obiekt, można utworzyć jedno przypisanie, które mogło mieć wiele przypisań.

## <a name="sample-initiative-definition"></a>Definicja przykładowej inicjatywy

W ramach tej inicjatywy wdrażane są dwie definicje zasad, z których każdy przyjmuje parametry **TagName** i **tagValue** . Sama inicjatywa ma dwa parametry: **costCenterValue** i **productNameValue**.
Te parametry inicjatywy są dostarczane do poszczególnych grup definicji zasad. Ten projekt maksymalizuje ponowne użycie istniejących definicji zasad podczas ograniczania liczby przypisań utworzonych w celu ich wdrożenia w razie potrzeby.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Wyjaśnienie

#### <a name="initiative-parameters"></a>Parametry inicjatywy

Inicjatywa może definiować własne parametry, które są następnie przesyłane do pogrupowanych definicji zasad.
W tym przykładzie zarówno **costCenterValue** , jak i **productNameValue** są zdefiniowane jako parametry inicjatywy. Wartości są podawane podczas przypisywania inicjatywy.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Zawiera definicje zasad

Każda uwzględniona definicja zasad musi dostarczyć tablicę **policyDefinitionId** i **Parametry** , jeśli definicja zasad akceptuje parametry. W poniższym fragmencie kodu uwzględniona definicja zasad przyjmuje dwa parametry: **TagName** i **tagValue**. element **TagName** jest zdefiniowany za pomocą literału, ale **TagValue** używa parametru **costCenterValue** zdefiniowanego przez inicjatywę. To przekazywanie wartości pozwala ulepszyć ponowne użycie.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).