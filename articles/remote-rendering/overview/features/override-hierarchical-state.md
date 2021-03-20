---
title: Hierarchiczne zastępowanie stanu
description: Wyjaśnia koncepcję składników przesłonięcia stanu hierarchicznego.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94445158"
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
  
  ![Kolor odcienia używany do przekształcania obiektu na zielony](./media/color-tint.png)

* **`See-through`**: Geometria jest renderowany częściowo w sposób przezroczysty, na przykład w celu ujawnienia wewnętrznych części obiektu. Na poniższej ilustracji przedstawiono cały samochód, który jest renderowany w trybie Zobacz, z wyjątkiem czerwonych Caliper hamulców:

  ![Tryb wyświetlania, używany do przezroczystości wybranych obiektów](./media/see-through.png)

  > [!IMPORTANT]
  > Efekt uboczny działa tylko wtedy, gdy używany jest [tryb renderowania](../../concepts/rendering-modes.md) *TileBasedComposition* .

* **`Shell`**: Geometria jest renderowana jako przezroczysta, nienasyconej powłoki. Ten tryb pozwala zanikać nieważne części sceny przy zachowaniu sensu kształtu i względnego pozycjonowania. Aby zmienić wygląd renderowania powłoki, użyj stanu [ShellRenderingSettings](shell-effect.md) . Zapoznaj się z poniższym obrazem dla modelu samochodu w całości renderowanej powłoki, z wyjątkiem niebieskich sprężyn:

  ![Tryb powłoki używany do zanikania określonych obiektów](./media/shell.png)

  > [!IMPORTANT]
  > Efekt powłoki działa tylko wtedy, gdy używany jest [tryb renderowania](../../concepts/rendering-modes.md) *TileBasedComposition* .

* **`Selected`**: Geometria jest renderowana z [konturem zaznaczenia](outlines.md).

  ![Opcja konspektu używana do wyróżnienia wybranej części](./media/selection-outline.png)

* **`DisableCollision`**: Geometria jest wykluczona z [zapytań przestrzennych](spatial-queries.md). **`Hidden`** Flaga nie ma wpływu na flagę stanu kolizji, dlatego te dwie flagi są często ustawiane razem.

* **`UseCutPlaneFilterMask`**: Użyj pojedynczej maski bitów filtru, aby kontrolować zaznaczenie opcji wycinania. Ta flaga określa, czy dana maska filtru powinna być używana, czy dziedziczona od jej elementu nadrzędnego. Sama maska bitów filtru jest ustawiana za pośrednictwem `CutPlaneFilterMask` właściwości. Aby uzyskać szczegółowe informacje na temat sposobu działania filtrowania, zapoznaj się z [akapitem selektywne wycinanie płaszczyzn](cut-planes.md#selective-cut-planes). Zapoznaj się z poniższym przykładem, gdy tylko opona i brzeg są obcinane, gdy pozostała część sceny pozostaje bez zmian.
![Selektywne wycinanie płaszczyzn](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> Alternatywnie, aby wyłączyć widoczność i przestrzenne zapytania dla pełnego wykresu podrzędnego, `enabled` stan obiektu gry można przełączać. Jeśli hierarchia jest wyłączona, ma preferencję nad dowolnym `HierarchicalStateOverrideComponent` .

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

Renderowanie przezroczyste zwiększa obciążenie procesora GPU serwera niż w przypadku renderowania standardowego. Jeśli duże części wykresu sceny są przełączane do *wyświetlania, a* wiele warstw geometrii jest widocznych, może stać się wąskim gardłem wydajności. Ta sama wartość jest prawidłowa dla obiektów z [konturami wyboru](../../overview/features/outlines.md#performance) i dla [renderowania powłoki](../../overview/features/shell-effect.md#performance) . 

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa HierarchicalStateOverrideComponent języka C#](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [Klasa C++ HierarchicalStateOverrideComponent](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>Następne kroki

* [Przedstawiono](../../overview/features/outlines.md)
* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Zapytania przestrzenne](../../overview/features/spatial-queries.md)