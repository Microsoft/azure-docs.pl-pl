---
title: Zgodność z przepisami w definicjach inicjatyw
description: Opisuje sposób użycia definicji inicjatywy do grupy zasad według domeny regulacyjnej, takiej jak Access Control, zarządzania konfiguracją i innych.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 08173cd94e0841fa7a18fd55d53aa97281724ede
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094068"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Zgodność z przepisami w Azure Policy

Zgodność z przepisami w Azure Policy zawiera wbudowane definicje inicjatyw do wyświetlania listy **kontroli** i **domen zgodności** na podstawie odpowiedzialności (_Klient_, _firma Microsoft_, _udostępnione_).
W przypadku kontrolek odpowiedzialnych za firmę Microsoft udostępniamy dodatkowe szczegóły naszych wyników inspekcji na podstawie zaświadczania innych firm oraz szczegóły implementacji w celu osiągnięcia tej zgodności.
Formanty odpowiedzialne za firmę Microsoft są `type` [statyczne](./definition-structure.md#type).

> [!NOTE]
> Zgodność z przepisami jest funkcją w wersji zapoznawczej. W przypadku zaktualizowanych wbudowanych formantów inicjatywy kontroluje odpowiednie normy zgodności. Istniejące standardowe inicjatywy dotyczące zgodności są w trakcie aktualizacji w celu zapewnienia zgodności z przepisami.

## <a name="regulatory-compliance-defined"></a>Określono zgodność z przepisami

Zgodność z przepisami jest oparta na części [grupującej](./initiative-definition-structure.md#policy-definition-groups) definicji inicjatywy. W programie wbudowane, każde grupowanie w definicji inicjatywy definiuje nazwę (**kontrolkę**), kategorię (**domenę zgodności**) i zawiera odwołanie do obiektu [policyMetadata](./initiative-definition-structure.md#metadata-objects) , który zawiera informacje o tym **formancie**. Definicja inicjatywy zgodności z przepisami musi mieć `category` ustawioną właściwość na **zgodność z przepisami**. Zgodnie ze standardową definicją inicjatywy, inicjatywy zgodności z przepisami obsługują [Parametry](./initiative-definition-structure.md#parameters) do tworzenia przypisań dynamicznych.

Klienci mogą tworzyć własne inicjatywy zgodności z przepisami. Definicje te mogą być oryginalne lub kopiowane z istniejących wbudowanych definicji. W przypadku używania wbudowanej definicji inicjatywy zgodności z przepisami jako odniesienia zaleca się monitorowanie źródła definicji zgodności z przepisami w [repozytorium GitHub Azure Policy](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance).

Aby połączyć niestandardową inicjatywę zgodności z pulpitem nawigacyjnym Azure Security Center, zobacz [Azure Security Center — używanie niestandardowych zasad zabezpieczeń](../../../security-center/custom-security-policies.md).

## <a name="regulatory-compliance-in-portal"></a>Zgodność z przepisami w portalu

Po utworzeniu definicji inicjatywy z [grupami](./initiative-definition-structure.md#policy-definition-groups)Strona szczegółów **zgodności** w portalu dla tej inicjatywy ma dodatkowe informacje. 

Nowa karta, **kontrolki** są dodawane do strony. Filtrowanie jest dostępne przez **domenę zgodności** i definicje zasad są pogrupowane według `title` pola z obiektu **policyMetadata** . Każdy wiersz reprezentuje **kontrolkę** , która pokazuje jej stan zgodności, **domenę zgodności** , **do której należy**, informacje o odpowiedzialności oraz liczbę niezgodnych i zgodnych definicji zasad.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="Zrzut ekranu przedstawiający przegląd zgodności z przepisami dla programu NIST SP 800-53 R4 — wbudowana definicja pokazująca zgodne i niezgodne kontrolki.":::

Wybranie **kontrolki** powoduje otwarcie strony ze szczegółowymi informacjami o tym formancie. **Przegląd** zawiera informacje z `description` i `requirements` . Na karcie **zasady** znajdują się wszystkie poszczególne definicje zasad w ramach inicjatywy, które przyczyniają się do tej **kontrolki**. Karta **zgodność zasobów** zawiera szczegółowy widok wszystkich zasobów, które są oceniane przez zasady elementu członkowskiego aktualnie wyświetlanej **kontrolki**.

> [!NOTE]
> Typ oceny **zarządzanej przez firmę Microsoft** jest przeznaczony dla definicji zasad [statycznych](./definition-structure.md#type) `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="Zrzut ekranu przedstawiający szczegóły zgodności z przepisami dotyczącymi kontroli ochrony granic w ramach wbudowanej definicji programu NIST SP 800-53 R4.":::

Na tej samej stronie **kontrolnej** zmiana na karcie **zgodność zasobów** powoduje wyświetlenie wszystkich zasobów zawierających definicje zasad tej **kontrolki**. Filtry są dostępne dla nazwy lub identyfikatora, stanu zgodności, typu zasobu i lokalizacji.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="Zrzut ekranu przedstawiający zgodność zasobów z kontrolą ochrony granic w ramach wbudowanej definicji programu NIST SP 800-53 R4.":::

## <a name="regulatory-compliance-in-sdk"></a>Zgodność z przepisami w zestawie SDK

Jeśli zgodność z przepisami jest włączona w definicji inicjatywy, zestaw SDK skanowania w celu oceny, zdarzeń i Stanów zasad, każdy zwraca dodatkowe właściwości. Te dodatkowe właściwości są pogrupowane według stanu zgodności i zawierają informacje o liczbie grup w poszczególnych Stanach.

Poniższy kod jest przykładem dodanego wyniku `summarize` wywołania:

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Następne kroki

- Zobacz [strukturę definicji inicjatywy](./initiative-definition-structure.md)
- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [wyjaśnienie działania zasad](./effects.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
