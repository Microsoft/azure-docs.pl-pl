---
title: Siatki
description: Definicja siatki w zakresie renderowania zdalnego na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 08d80a5ec2099147c12bcecd3b52d64429837285
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563968"
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
Entity CloneEntityWithModel(RemoteManager manager, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = manager.CreateEntity();
        MeshComponent newMeshComp = manager.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RemoteManager> manager, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *manager->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = manager->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa siatki C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [Klasa MeshComponent języka C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Klasa siatki C++](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [Klasa C++ MeshComponent](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Następne kroki

* [Materiały](materials.md)
