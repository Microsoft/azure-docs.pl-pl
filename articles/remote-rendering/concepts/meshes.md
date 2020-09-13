---
title: Siatki
description: Definicja siatki w zakresie renderowania zdalnego na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: eea43f48abef5e2b258251d46eca1061a2263519
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613840"
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

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa siatki C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [Klasa MeshComponent języka C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Klasa siatki C++](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [Klasa C++ MeshComponent](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)

## <a name="next-steps"></a>Następne kroki

* [Materiały](materials.md)
