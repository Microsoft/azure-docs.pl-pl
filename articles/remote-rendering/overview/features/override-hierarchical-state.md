---
title: Hierarchiczne zastępowanie stanu
description: Wyjaśnia koncepcję składników przesłonięcia stanu hierarchicznego.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680793"
---
# <a name="hierarchical-state-override"></a>Hierarchiczne zastępowanie stanu

W wielu przypadkach konieczna jest Dynamiczna zmiana wyglądu części [modelu](../../concepts/models.md), na przykład ukrycie podwykresów lub przełączenie części do przezroczystego renderowania. Zmiana materiałów dla każdej części nie jest praktyczna, ponieważ wymaga ona iteracji całego wykresu sceny i zarządzania klonowania i przypisywania materiału w każdym węźle.

Aby osiągnąć ten przypadek użycia przy minimalnym możliwym obciążeniu `HierarchicalStateOverrideComponent`, użyj. Ten składnik implementuje hierarchiczne aktualizacje stanu dla dowolnych gałęzi grafu sceny. Oznacza to, że stan można zdefiniować na dowolnym poziomie wykresu sceny i Trickles hierarchię do momentu, aż zostanie zastąpiony przez nowy stan lub zastosowany do obiektu liścia.

Na przykład rozważmy model samochodu i chcesz przełączyć cały samochód, aby był przezroczysty, z wyjątkiem części aparatu wewnętrznego. Ten przypadek użycia obejmuje tylko dwa wystąpienia składnika:

* Pierwszy składnik jest przypisany do głównego węzła modelu i włącza renderowanie przezroczyste dla całego samochodu.
* Drugi składnik jest przypisany do węzła głównego aparatu i ponownie zastępuje stan, przez jawne wyłączenie trybu wyświetlania.

## <a name="features"></a>Funkcje

Stały zestaw Stanów, które mogą zostać zastąpione, to:

* **Ukryte**: odpowiednie siatki w grafie sceny są ukryte lub pokazane.
* **Kolor odcienia**: renderowany obiekt może być odbarwione kolorami z odcieniami poszczególnych kolorów i grubości odcienia. Na poniższym obrazie pokazano kolor tinty obręczy koła.
  
  ![Odcień koloru](./media/color-tint.png)

* Zapoznaj się z **tematem**: geometria jest renderowana częściowo w sposób przezroczysty, na przykład w celu ujawnienia wewnętrznych części obiektu. Na poniższej ilustracji przedstawiono cały samochód, który jest renderowany w trybie Zobacz, z wyjątkiem czerwonych Caliper hamulców:

  ![Zapoznaj się z artykułem](./media/see-through.png)

  > [!IMPORTANT]
  > Efekt uboczny działa tylko wtedy, gdy używany jest [tryb renderowania](../../concepts/rendering-modes.md) *TileBasedComposition* .

* **Zaznaczone**: geometria jest renderowana z [konturem zaznaczenia](outlines.md).

  ![Kontur zaznaczenia](./media/selection-outline.png)

* **DisableCollision**: geometria jest wykluczona z [zapytań przestrzennych](spatial-queries.md). Flaga **Ukryta** nie wyłącza kolizji, dlatego te dwie flagi są często ustawiane razem.

## <a name="hierarchical-overrides"></a>Zastąpienia hierarchiczne

`HierarchicalStateOverrideComponent` Można je dołączyć na wielu poziomach hierarchii obiektów. Ponieważ może istnieć tylko jeden składnik każdego typu w jednostce, każda z nich `HierarchicalStateOverrideComponent` zarządza Stanami ukryty, zapoznaj się z elementem, wybranym, tintą kolorów i kolizją.

W związku z tym każdy stan może być ustawiony na jeden z:

* `ForceOn`-stan jest włączony dla wszystkich siatek w tym węźle i poniżej niego
* `ForceOff`-stan jest wyłączony dla wszystkich siatek w i poniżej tego węzła
* `InheritFromParent`-Ten składnik przesłonięcia nie ma wpływ na stan.

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

### <a name="tint-color"></a>Kolor tinty

Zastępowanie koloru tinty jest nieco specjalne w przypadku, gdy istnieje zarówno stan on/off/dziedziczenia, jak i kolor tinty. Część alfa koloru tinty definiuje wagę efektu tinty: w przypadku ustawienia na 0,0, kolor tinty nie jest widoczny i jeśli zostanie ustawiony na 1,0, obiekt będzie renderowany z czystym kolorem tinty. Dla wartości in-between kolor końcowy zostanie zmieszany z kolorem odcienia. Kolor odcienia można zmienić na poszczególnych klatkach, aby osiągnąć animację koloru.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Wystąpienie `HierarchicalStateOverrideComponent` samego siebie nie dodaje znacznie obciążenia środowiska uruchomieniowego. Jest jednak zawsze dobrym sposobem, aby zachować liczbę aktywnych składników. Na przykład podczas implementowania systemu wyboru, który podświetla wybrany obiekt, zaleca się usunięcie składnika po usunięciu wyróżnienia. Utrzymywanie składników z neutralnymi funkcjami może szybko dodać.

Renderowanie przezroczyste zwiększa obciążenie procesora GPU serwera niż w przypadku renderowania standardowego. Jeśli duże części wykresu sceny są przełączane do *wyświetlania, a*wiele warstw geometrii jest widocznych, może stać się wąskim gardłem wydajności. Ta sama wartość jest prawidłowa dla obiektów z [konturami wyboru](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Następne kroki

* [Przedstawiono](../../overview/features/outlines.md)
* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Zapytania przestrzenne](../../overview/features/spatial-queries.md)
