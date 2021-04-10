---
title: Opis zakresu w Azure Policy
description: Opisuje koncepcję zakresu w Azure Resource Manager i sposób, w jaki ma zastosowanie do Azure Policy do kontrolowania, które zasoby Azure Policy oceniane.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: c198d2d2961b6d9e10a3b78481183cba7f7197ca
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167740"
---
# <a name="understand-scope-in-azure-policy"></a>Opis zakresu w Azure Policy

Istnieje wiele ustawień, które określają, które zasoby mogą być oceniane i które zasoby są oceniane przez Azure Policy. Podstawową koncepcją dla tych formantów jest _zakres_. Zakres w Azure Policy jest oparty na tym, jak zakres działa w Azure Resource Manager. Aby zapoznać się z ogólnym omówieniem, zobacz [zakres w Azure Resource Manager](../../../azure-resource-manager/management/overview.md#understand-scope).
W tym artykule opisano znaczenie _zakresu_ w Azure Policy i powiązane z nim obiekty i właściwości.

## <a name="definition-location"></a>Lokalizacja definicji

Pierwszy zakres wystąpienia używany przez Azure Policy jest tworzony podczas tworzenia definicji zasad. Definicja może zostać zapisana w grupie zarządzania lub w ramach subskrypcji. Lokalizacja określa zakres, do którego można przypisać inicjatywę lub zasady. Zasoby muszą znajdować się w hierarchii zasobów lokalizacji definicji, aby można było je przypisywać.

Jeśli lokalizacja definicji jest:

- Tylko zasoby z **subskrypcją** w ramach tej subskrypcji mogą mieć przypisaną definicję zasad.
- Do definicji zasad można przypisać tylko zasoby **grupy** zarządzania należące do podrzędnych grup zarządzania i subskrypcji podrzędnych. Jeśli planujesz zastosowanie definicji zasad do kilku subskrypcji, lokalizacja musi być grupą zarządzania, która zawiera każdą subskrypcję.

Lokalizacja powinna być kontenerem zasobów udostępnionym przez wszystkie zasoby, dla których chcesz użyć definicji zasad. Ten kontener zasobów jest zazwyczaj grupą zarządzania w sąsiedztwie głównej grupy zarządzania.

## <a name="assignment-scopes"></a>Zakresy przypisania

Przypisanie ma kilka właściwości, które ustawiają zakres. Te właściwości określają, który zasób Azure Policy do oceny i ile zasobów jest zgodnych ze zgodnością. Te właściwości są mapowane na następujące koncepcje:

- Dołączenie — hierarchia zasobów lub indywidualny zasób należy ocenić pod kątem zgodności przez definicję. `properties.scope`Właściwość obiektu przypisania określa elementy, które mają zostać uwzględnione i oszacowane pod kątem zgodności. Aby uzyskać więcej informacji, zobacz [Definicja przypisania](./assignment-structure.md).

- Wykluczanie — nie należy oceniać hierarchii zasobów lub pojedynczego zasobu pod kątem zgodności przez definicję. Właściwość `properties.notScopes` _Array_ obiektu przypisania określa elementy, które mają zostać wykluczone. Zasoby znajdujące się w tych zakresach nie są oceniane ani uwzględniane w liczniku zgodności. Aby uzyskać więcej informacji, zobacz [definicje przypisania — wykluczone zakresy](./assignment-structure.md#excluded-scopes).

Poza właściwościami przypisywania zasad jest obiektem [wykluczenia zasad](./exemption-structure.md) . Wykluczenia rozszerzają historię zakresu, dostarczając metodę identyfikującą część przypisania, która nie zostanie oceniona.

- Wykluczanie (**bezpłatne w wersji zapoznawczej** ) — hierarchia zasobów lub poszczególne zasoby powinny być oceniane pod kątem zgodności przez definicję, ale nie będą oceniane z przyczyn, takich jak rezygnacja lub ograniczenie przez inną metodę. Zasoby w tym stanie są wyświetlane jako **wykluczone** w raportach zgodności, aby mogły być śledzone. Obiekt wykluczenia jest tworzony w hierarchii zasobów lub pojedynczym zasobie jako obiekt podrzędny, który określa zakres wykluczenia. Hierarchia zasobów lub pojedynczy zasób może być wykluczony dla wielu przypisań. Wykluczenie można skonfigurować tak, aby wygasał zgodnie z harmonogramem przy użyciu `expiresOn` właściwości. Aby uzyskać więcej informacji, zobacz [Definicja wykluczenia](./exemption-structure.md).

  > [!NOTE]
  > Ze względu na wpływ przyznawania zwolnienia dla hierarchii zasobów lub pojedynczego zasobu wykluczenia mają dodatkowe środki bezpieczeństwa. Oprócz wymagania `Microsoft.Authorization/policyExemptions/write` operacji w hierarchii zasobów lub poszczególnych zasobów, twórca wykluczenia musi mieć `exempt/Action` zlecenie dotyczące przypisania docelowego.

## <a name="scope-comparison"></a>Porównanie zakresu

Poniższa tabela zawiera porównanie opcji zakresu:

| | Wymaga | Wykluczenie (notScopes) | Przyzna |
|---|:---:|:---:|:---:|
|**Zasoby są oceniane** | &#10004; | - | - |
|**Menedżer zasobów, obiekt** | - | - | &#10004; |
|**Wymaga modyfikacji obiektu przypisania zasad** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).