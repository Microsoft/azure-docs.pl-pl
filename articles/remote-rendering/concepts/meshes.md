---
title: Siatki
description: Definicja siatki w zakresie renderowania zdalnego na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681768"
---
# <a name="meshes"></a>Siatki

## <a name="mesh-resource"></a>Zasób siatki

Siatki są niezmiennym [zasobem udostępnionym](../concepts/lifetime.md), które mogą być tworzone tylko przy użyciu [konwersji modelu](../how-tos/conversion/model-conversion.md). Siatki zawierają jedną lub kilka *podsiatki*. Każda Podsiatka odwołuje się do [materiału](materials.md) , z którym powinien być domyślnie renderowany. Aby umieścić siatkę w przestrzeni 3D, Dodaj [MeshComponent](#meshcomponent) do [jednostki](entities.md).

### <a name="mesh-resource-properties"></a>Właściwości zasobów siatki

`Mesh`Właściwości klasy:

* **Materiały:** Tablica materiałów. Każdy materiał jest używany przez inną podsiatkę. Wiele wpisów w tablicy może odwoływać się do tego samego [materiału](materials.md). Te dane nie mogą być modyfikowane w czasie wykonywania.

* **Granice:** Wyrównane do osi miejsca lokalnego pole ograniczenia (AABB) wierzchołków siatki.

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`Klasa jest używana do umieszczania wystąpienia zasobu siatki. Każdy MeshComponent odwołuje się do jednej siatki. Może przesłonić, które materiały są używane do renderowania każdej podsiatki.

### <a name="meshcomponent-properties"></a>Właściwości MeshComponent

* **Siatka:** Zasób siatki używany przez ten składnik.

* **Materiały:** Tablica materiałów określona w samym składniku siatki. Tablica będzie zawsze miała taką samą długość jak tablica *materiałów* w zasobie siatki. Materiały, które nie są zastępowane wartością domyślną siatki, są ustawione na *wartość null* w tej tablicy.

* **UsedMaterials:** Tablica rzeczywiście używanych materiałów dla każdej podsiatki. Będzie taka sama jak dane w tablicy *materiałów* dla wartości innych niż null. W przeciwnym razie zawiera wartość z tablicy *materiałów* w wystąpieniu siatki.

## <a name="next-steps"></a>Następne kroki

* [Materiały](materials.md)
