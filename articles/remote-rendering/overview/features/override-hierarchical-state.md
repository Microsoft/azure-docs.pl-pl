---
title: Hierarchiczne zastępowanie stanu
description: Wyjaśnia koncepcję składników przesłonięcia stanu hierarchicznego.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 99f57c212dfc44d84640224b1526ab770fe97230
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009461"
---
# <a name="hierarchical-state-override"></a>Hierarchiczne zastępowanie stanu

W wielu przypadkach konieczna jest Dynamiczna zmiana wyglądu części [modelu](../../concepts/models.md), na przykład ukrycie podwykresów lub przełączenie części do przezroczystego renderowania. Zmiana materiałów dla każdej części nie jest praktyczna, ponieważ wymaga ona iteracji całego wykresu sceny i zarządzania klonowania i przypisywania materiału w każdym węźle.

Aby osiągnąć ten przypadek użycia przy minimalnym możliwym obciążeniu, użyj `HierarchicalStateOverrideComponent` . Ten składnik implementuje hierarchiczne aktualizacje stanu dla dowolnych gałęzi grafu sceny. Oznacza to, że stan można zdefiniować na dowolnym poziomie wykresu sceny i Trickles hierarchię do momentu, aż zostanie zastąpiony przez nowy stan lub zastosowany do obiektu liścia.

Na przykład rozważmy model samochodu i chcesz przełączyć cały samochód, aby był przezroczysty, z wyjątkiem części aparatu wewnętrznego. Ten przypadek użycia obejmuje tylko dwa wystąpienia składnika:

* Pierwszy składnik jest przypisany do głównego węzła modelu i włącza renderowanie przezroczyste dla całego samochodu.
* Drugi składnik jest przypisany do węzła głównego aparatu i ponownie zastępuje stan, przez jawne wyłączenie trybu wyświetlania.

## <a name="features"></a>Funkcje

Stały zestaw Stanów, które mogą zostać zastąpione, to:

* **`Hidden`**: Odpowiednie siatki w grafie sceny są ukryte lub pokazywane.
* **`Tint color`**: Renderowany obiekt może być odbarwione kolorami z odcieniami poszczególnych kolorów i odcienia. Na poniższym obrazie pokazano kolor tinty obręczy koła.
  
  ![Odcień koloru](./media/color-tint.png)

* **`See-through`**: Geometria jest renderowany częściowo w sposób przezroczysty, na przykład w celu ujawnienia wewnętrznych części obiektu. Na poniższej ilustracji przedstawiono cały samochód, który jest renderowany w trybie Zobacz, z wyjątkiem czerwonych Caliper hamulców:

  ![Zapoznaj się z artykułem](./media/see-through.png)

  > [!IMPORTANT]
  > Efekt uboczny działa tylko wtedy, gdy używany jest [tryb renderowania](../../concepts/rendering-modes.md) *TileBasedComposition* .

* **`Selected`**: Geometria jest renderowana z [konturem zaznaczenia](outlines.md).

  ![Kontur zaznaczenia](./media/selection-outline.png)

* **`DisableCollision`**: Geometria jest wykluczona z [zapytań przestrzennych](spatial-queries.md). **`Hidden`** Flaga nie ma wpływu na flagę stanu kolizji, dlatego te dwie flagi są często ustawiane razem.

## <a name="hierarchical-overrides"></a>Zastąpienia hierarchiczne

`HierarchicalStateOverrideComponent`Można je dołączyć na wielu poziomach hierarchii obiektów. Ponieważ może istnieć tylko jeden składnik każdego typu w jednostce, każda z nich `HierarchicalStateOverrideComponent` zarządza Stanami ukryty, zapoznaj się z elementem, wybranym, tintą kolorów i kolizją.

W związku z tym każdy stan może być ustawiony na jeden z:

* `ForceOn` -stan jest włączony dla wszystkich siatek w tym węźle i poniżej niego
* `ForceOff` -stan jest wyłączony dla wszystkich siatek w i poniżej tego węzła
* `InheritFromParent` -Ten składnik przesłonięcia nie ma wpływ na stan.

Stany można zmienić bezpośrednio lub za pomocą `SetState` funkcji:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Kolor tinty

`tint color`Przesłonięcie jest nieco specjalne w tym, że istnieje zarówno stan włączania/wyłączania/dziedziczenia, jak i kolor tinty. Część alfa koloru tinty definiuje wagę efektu tinty: w przypadku ustawienia na 0,0, kolor tinty nie jest widoczny i jeśli zostanie ustawiony na 1,0, obiekt będzie renderowany z czystym kolorem tinty. Dla wartości in-between kolor końcowy zostanie zmieszany z kolorem odcienia. Kolor odcienia można zmienić na poszczególnych klatkach, aby osiągnąć animację koloru.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Wystąpienie `HierarchicalStateOverrideComponent` samego siebie nie dodaje znacznie obciążenia środowiska uruchomieniowego. Jest jednak zawsze dobrym sposobem, aby zachować liczbę aktywnych składników. Na przykład podczas implementowania systemu wyboru, który podświetla wybrany obiekt, zaleca się usunięcie składnika po usunięciu wyróżnienia. Utrzymywanie składników z neutralnymi funkcjami może szybko dodać.

Renderowanie przezroczyste zwiększa obciążenie procesora GPU serwera niż w przypadku renderowania standardowego. Jeśli duże części wykresu sceny są przełączane do *wyświetlania, a*wiele warstw geometrii jest widocznych, może stać się wąskim gardłem wydajności. Ta sama wartość jest prawidłowa dla obiektów z [konturami wyboru](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Kolejne kroki

* [Przedstawiono](../../overview/features/outlines.md)
* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Zapytania przestrzenne](../../overview/features/spatial-queries.md)
