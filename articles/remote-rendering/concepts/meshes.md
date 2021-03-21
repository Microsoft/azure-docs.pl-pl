---
title: Siatki
description: Definicja siatki w zakresie renderowania zdalnego na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594522"
---
# <a name="meshes"></a>Siatki

## <a name="mesh-resource"></a>Zasób siatki

Siatki są niezmiennym [zasobem udostępnionym](../concepts/lifetime.md), które mogą być tworzone tylko przy użyciu [konwersji modelu](../how-tos/conversion/model-conversion.md). Siatki zawierają jedną lub wiele *podsiatk* wraz z fizyką reprezentacją dla [zapytań raycast](../overview/features/spatial-queries.md). Każda Podsiatka odwołuje się do [materiału](materials.md) , z którym powinien być domyślnie renderowany. Aby umieścić siatkę w przestrzeni 3D, Dodaj [MeshComponent](#meshcomponent) do [jednostki](entities.md).

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

### <a name="sharing-of-meshes"></a>Udostępnianie siatek

`Mesh`Zasób może być współużytkowany przez wiele wystąpień składników siatki. Ponadto `Mesh` zasób przypisany do składnika siatki można programowo zmienić w dowolnym momencie. Poniższy kod ilustruje sposób klonowania siatki:

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa siatki C#](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [Klasa MeshComponent języka C#](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Klasa siatki C++](/cpp/api/remote-rendering/mesh)
* [Klasa C++ MeshComponent](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Następne kroki

* [Materiały](materials.md)