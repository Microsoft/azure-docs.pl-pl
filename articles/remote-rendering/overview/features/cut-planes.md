---
title: Wycięte płaszczyzny
description: Wyjaśnia, jakie są wycięte płaszczyzny i jak ich używać
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 7adf9a9701eb2492f0b13a26af1dbaf8de631373
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021368"
---
# <a name="cut-planes"></a>Wycięte płaszczyzny

*Płaszczyzna wycinania* jest funkcją wizualną, która przycina piksele na jednej stronie płaszczyzny wirtualnej, odkrywając wewnątrz [siatki](../../concepts/meshes.md).
Na poniższej ilustracji przedstawiono efekt. Po lewej stronie zostanie wyświetlona oryginalna siatka, po prawej stronie może wyglądać wewnątrz siatki:

![Wytnij płaszczyznę](./media/cutplane-1.png)

## <a name="limitations"></a>Ograniczenia

* W tym czasie zdalne renderowanie na platformie Azure obsługuje **maksymalnie osiem aktywnych płaszczyzn wycinania**. Można utworzyć bardziej wycięte składniki płaszczyzny, ale jeśli spróbujesz go włączyć dłużej, zignoruje aktywację. Najpierw wyłącz inne płaszczyzny, jeśli chcesz zmienić składnik powinien wpływać na scenę.
* Każda płaszczyzna wycinania ma wpływ na wszystkie zdalnie renderowane obiekty. Obecnie nie ma możliwości wykluczenia określonych obiektów i części siatki.
* Wycięte płaszczyzny są czysto funkcją wizualną, nie wpływają na wynik [zapytań przestrzennych](spatial-queries.md). Jeśli chcesz otrzymywać dane z rzutu do siatki wyciętej, możesz dostosować punkt początkowy promienia, aby znajdować się na płaszczyźnie wycinania. W ten sposób promień może trafiać tylko widoczne części.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Każda aktywna płaszczyzna wycinania wiąże się z niewielkim kosztem podczas renderowania. Wyłącz lub Usuń wycięte płaszczyzny, gdy nie są potrzebne.

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
    cutPlane->Normal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->FadeColor(fadeColor);
    cutPlane->FadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>Właściwości CutPlaneComponent

Następujące właściwości są uwidocznione w składniku wycinania płaszczyzny:

* `Enabled`: Możesz tymczasowo wyłączyć wycięte płaszczyzny, wyłączając składnik. Wyłączone płaszczyzny wycinania nie wiążą się z obciążeniem renderowania, a także nie są wliczane do limitu globalnej płaszczyzny wycinania.

* `Normal`: Określa kierunek (+ X,-X, + Y,-Y, + Z,-Z) jest używany jako normalne. Ten kierunek jest określany względem orientacji jednostki właściciela. Przenieś i obróć jednostkę Owner w celu dokładnego umieszczania.

* `FadeColor` i `FadeLength`:

  Jeśli wartość alfa *FadeColor* jest różna od zera, piksele blisko płaszczyzny wycinania przestaną się na część RGB części FadeColor. Siła kanału alfa decyduje o tym, czy przejdzie w pełni do koloru zanikania, czy tylko częściowo. *FadeLength* definiuje odległość tego zaniku.

## <a name="next-steps"></a>Następne kroki

* [Renderowanie jednostronne](single-sided-rendering.md)
* [Zapytania przestrzenne](spatial-queries.md)
