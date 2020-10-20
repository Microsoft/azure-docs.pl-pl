---
title: Wycięte płaszczyzny
description: Wyjaśnia, jakie są wycięte płaszczyzny i jak ich używać
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7d8905fbdcfc03f2683698cca57ab6c066e77863
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205935"
---
# <a name="cut-planes"></a>Wycięte płaszczyzny

*Płaszczyzna wycinania* jest funkcją wizualną, która przycina piksele na jednej stronie płaszczyzny wirtualnej, odkrywając wewnątrz [siatki](../../concepts/meshes.md).
Na poniższej ilustracji przedstawiono efekt. Po lewej stronie zostanie wyświetlona oryginalna siatka, po prawej stronie może wyglądać wewnątrz siatki:

![Wytnij płaszczyznę](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

Do sceny należy dodać płaszczyznę wycinania, tworząc *CutPlaneComponent*. Położenie i orientacja płaszczyzny są określane przez [jednostkę](../../concepts/entities.md)właściciela składnika.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Właściwości CutPlaneComponent

Następujące właściwości są uwidocznione w składniku wycinania płaszczyzny:

* `Enabled`: Możesz tymczasowo wyłączyć wycięte płaszczyzny, wyłączając składnik. Wyłączone płaszczyzny wycinania nie wiążą się z obciążeniem renderowania, a także nie są wliczane do limitu globalnej płaszczyzny wycinania.

* `Normal`: Określa kierunek (+ X,-X, + Y,-Y, + Z,-Z) jest używany jako normalne. Ten kierunek jest określany względem orientacji jednostki właściciela. Przenieś i obróć jednostkę Owner w celu dokładnego umieszczania.

* `FadeColor` i `FadeLength`:

  Jeśli wartość alfa *FadeColor* jest różna od zera, piksele blisko płaszczyzny wycinania przestaną się na część RGB części FadeColor. Siła kanału alfa decyduje o tym, czy przejdzie w pełni do koloru zanikania, czy tylko częściowo. *FadeLength* definiuje odległość tego zaniku.

* `ObjectFilterMask`: Maska bitów filtru, która określa, której geometrii dotyczy płaszczyzna wycinania. Szczegółowe informacje znajdują się w następnym akapicie.

### <a name="selective-cut-planes"></a>Selektywne wycinanie płaszczyzn

Można skonfigurować poszczególne płaszczyzny wycinania, aby miały wpływ tylko na określoną geometrię. Na poniższej ilustracji przedstawiono sposób, w jaki ta konfiguracja może wyglądać następująco:

![Selektywne wycinanie płaszczyzn](./media/selective-cut-planes.png)

Filtrowanie odbywa się za pomocą **porównania logicznej maski bitowej** , między maską bitową po stronie z płaszczyzną wycinania a drugą maską bitową ustawioną w geometrii. Jeśli wynik operacji logicznej `AND` między maskami nie jest zerem, płaszczyzna wycinania będzie miała wpływ na geometrię.

* Maska bitów składnika wycinania jest ustawiana za pośrednictwem jego `ObjectFilterMask` właściwości
* Maska bitów w hierarchii podrzędnej geometrycznej jest ustawiana za pośrednictwem [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features)

Przykłady:

| Maska filtru płaszczyzny wycinania | Maska filtru geometrii  | Wynik logiczny `AND` | Płaszczyzna wycinania ma wpływ na geometrię?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) = = 1   | (0000 0001) = = 1  | (0000 0001) = = 1  | Yes |
| (1111 0000) = = 240 | (0001 0001) = = 17 | (0001 0000) = = 16 | Yes |
| (0000 0001) = = 1   | (0000 0010) = = 2  | (0000 0000) = = 0  | Nie |
| (0000 0011) = = 3   | (0000 1000) = = 8  | (0000 0000) = = 0  | Nie |

>[!TIP]
> Ustawienie płaszczyzny wycinania `ObjectFilterMask` na wartość 0 oznacza, że nie będzie to miało wpływu na żadnej geometrii, ponieważ wynik operacji logicznej `AND` nie może być inny niż null. System renderowania nie uwzględnia tych płaszczyzn w pierwszym miejscu, więc jest to metoda uproszczona, aby wyłączyć poszczególne płaszczyzny wycinania. Te płaszczyzny wycinania również nie są wliczane do limitu 8 płaszczyzn aktywnych.

## <a name="limitations"></a>Ograniczenia

* Zdalne renderowanie na platformie Azure obsługuje **maksymalnie osiem aktywnych płaszczyzn wycinania**. Można utworzyć bardziej wycięte składniki płaszczyzny, ale jeśli spróbujesz go włączyć dłużej, zignoruje aktywację. Najpierw wyłącz inne płaszczyzny, jeśli chcesz przełączyć składniki mające wpływ na scenę.
* Wycięte płaszczyzny są czysto wizualną funkcją, nie wpływają na wyniki [zapytań przestrzennych](spatial-queries.md). Jeśli chcesz otrzymywać dane z rzutu do siatki wyciętej, możesz dostosować punkt początkowy promienia, aby znajdować się na płaszczyźnie wycinania. W ten sposób promień może trafiać tylko widoczne części.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Każda aktywna płaszczyzna wycinania wiąże się z niewielkim kosztem podczas renderowania. Wyłącz lub Usuń wycięte płaszczyzny, gdy nie są potrzebne.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa CutPlaneComponent języka C#](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [Klasa C++ CutPlaneComponent](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Następne kroki

* [Renderowanie jednostronne](single-sided-rendering.md)
* [Zapytania przestrzenne](spatial-queries.md)