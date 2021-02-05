---
title: Jednostki
description: Definicja jednostek w zakresie interfejsu API renderowania zdalnego platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 29952353b8c3452d95bcced163fafa81fe158f64
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593405"
---
# <a name="entities"></a>Jednostki

*Jednostka* reprezentuje obiekt przenośny w miejscu i jest podstawowym blokiem konstrukcyjnym zdalnie renderowanej zawartości.

## <a name="entity-properties"></a>Właściwości jednostki

Jednostki mają transformację zdefiniowaną przez pozycję, rotację i skalę. Przez same jednostki nie mają żadnych zauważalnych funkcji. Zamiast tego, zachowanie jest dodawane przez składniki, które są dołączone do jednostek. Na przykład dołączenie elementu [CutPlaneComponent](../overview/features/cut-planes.md)  spowoduje utworzenie płaszczyzny wycinania na pozycji jednostki.

Najważniejszym aspektem samej jednostki jest hierarchia i wyniki transformacji hierarchicznej. Na przykład jeśli wiele jednostek jest dołączanych jako elementy podrzędne do współużytkowanej jednostki nadrzędnej, wszystkie te jednostki można przenieść, obrócić i skalować w artykułem, zmieniając transformację jednostki nadrzędnej. Ponadto stan jednostki może służyć `enabled` do wyłączania widoczności i odpowiedzi na rzutowanie promieni dla pełnego wykresu podrzędnego w hierarchii.

Obiekt jest jednoznacznie własnością elementu nadrzędnego, co oznacza, że gdy element nadrzędny zostanie zniszczony za pomocą `Entity.Destroy()` , to są jego elementy podrzędne i wszystkie połączone [składniki](components.md). W ten sposób usuwanie modelu z sceny odbywa się przez wywołanie `Destroy` na głównym węźle modelu, zwrócone przez `RenderingSession.Connection.LoadModelAsync()` lub jego wariant SAS `RenderingSession.Connection.LoadModelFromSasAsync()` .

Jednostki są tworzone, gdy serwer załaduje zawartość lub kiedy użytkownik chce dodać obiekt do sceny. Na przykład jeśli użytkownik chce dodać płaszczyznę wycinania w celu wizualizacji wnętrza siatki, użytkownik może utworzyć jednostkę, w której powinna istnieć płaszczyzna, a następnie dodać do niej składnik wycinania płaszczyzny.

## <a name="create-an-entity"></a>Tworzenie jednostki

Aby dodać nową jednostkę do sceny, na przykład przekazać ją jako obiekt główny do ładowania modeli lub dołączyć do niej składniki, użyj następującego kodu:

```cs
Entity CreateNewEntity(RenderingSession session)
{
    Entity entity = session.Connection.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<RenderingSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Connection()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Funkcje zapytań

Istnieją dwa typy funkcji zapytania dotyczące jednostek: wywołań synchronicznych i asynchronicznych. Zapytań synchronicznych można używać tylko w przypadku danych, które znajdują się na kliencie i nie obejmują wielu obliczeń. Przykłady są zapytania dotyczące składników, przekształceń względnych obiektów lub relacji nadrzędny/podrzędny. Zapytania asynchroniczne są używane dla danych, które znajdują się tylko na serwerze, lub wymagają dodatkowych obliczeń, które byłyby zbyt kosztowne do uruchomienia na kliencie. Przykłady to zapytania dotyczące granic przestrzennych lub zapytania metadanych.

### <a name="querying-components"></a>Wykonywanie zapytań dotyczących składników

Aby znaleźć składnik określonego typu, użyj `FindComponentOfType` :

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Wykonywanie zapytań o przekształcenia

Zapytania transformacji są wywołaniami synchronicznymi dla obiektu. Należy pamiętać, że przekształcenia wykonywane za pomocą interfejsu API są przekształceń miejsc lokalnych względem elementu nadrzędnego obiektu. Wyjątki są obiektami głównymi, dla których przestrzeń lokalna i przestrzeń świata są identyczne.

> [!NOTE]
> Nie istnieje dedykowany interfejs API służący do wykonywania zapytań o transformację na całym świecie dla dowolnych obiektów.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>Wykonywanie zapytania dotyczącego granic przestrzennych

Zapytania dotyczące powiązań to wywołania asynchroniczne, które działają w całej hierarchii obiektów, przy użyciu jednej jednostki jako katalogu głównego. Zobacz dedykowany rozdział dotyczący [granic obiektów](object-bounds.md).

### <a name="querying-metadata"></a>Wykonywanie zapytania dotyczącego metadanych

Metadane to dodatkowe dane przechowywane w obiektach, które są ignorowane przez serwer. Metadane obiektu są zasadniczo zestawem par (nazwa, wartość), gdzie _wartość_ może być typu liczbowego, Boolean lub String. Metadane można eksportować przy użyciu modelu.

Zapytania metadanych to wywołania asynchroniczne dla określonej jednostki. Zapytanie zwraca tylko metadane pojedynczej jednostki, a nie scalone informacje wykresu podrzędnego.

```cs
Task<ObjectMetadata> metaDataQuery = entity.QueryMetadataAsync();
ObjectMetadata metaData = await metaDataQuery;
ObjectMetadataEntry entry = metaData.GetMetadataByName("MyInt64Value");
System.Int64 intValue = entry.AsInt64;
// ...
```

```cpp
entity->QueryMetadataAsync([](Status status, ApiHandle<ObjectMetadata> metaData) 
{
    if (status == Status::OK)
    {
        ApiHandle<ObjectMetadataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
        int64_t intValue = *entry->GetAsInt64();

        // ...
    }
});
```

Zapytanie powiedzie się, nawet jeśli obiekt nie zawiera żadnych metadanych.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa jednostki C#](/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RenderingConnection. GetEntity ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createentity)
* [Klasa jednostki C++](/cpp/api/remote-rendering/entity)
* [C++ RenderingConnection:: GetEntity ()](/cpp/api/remote-rendering/renderingconnection#createentity)

## <a name="next-steps"></a>Następne kroki

* [Składniki](components.md)
* [Granice obiektów](object-bounds.md)