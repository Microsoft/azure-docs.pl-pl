---
title: Jednostki
description: Definicja jednostek w zakresie interfejsu API renderowania zdalnego platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 5f6f7fc52a186117afcb92f6a2f80bf068e50ab9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84509206"
---
# <a name="entities"></a>Jednostki

*Jednostka* reprezentuje obiekt przenośny w miejscu i jest podstawowym blokiem konstrukcyjnym zdalnie renderowanej zawartości.

## <a name="entity-properties"></a>Właściwości jednostki

Jednostki mają transformację zdefiniowaną przez pozycję, rotację i skalę. Przez same jednostki nie mają żadnych zauważalnych funkcji. Zamiast tego, zachowanie jest dodawane przez składniki, które są dołączone do jednostek. Na przykład dołączenie elementu [CutPlaneComponent](../overview/features/cut-planes.md) spowoduje utworzenie płaszczyzny wycinania na pozycji jednostki.

Najważniejszym aspektem samej jednostki jest hierarchia i wyniki transformacji hierarchicznej. Na przykład jeśli wiele jednostek jest dołączanych jako elementy podrzędne do współużytkowanej jednostki nadrzędnej, wszystkie te jednostki można przenieść, obrócić i skalować w artykułem, zmieniając transformację jednostki nadrzędnej.

Obiekt jest jednoznacznie własnością elementu nadrzędnego, co oznacza, że gdy element nadrzędny zostanie zniszczony za pomocą `Entity.Destroy()` , to są jego elementy podrzędne i wszystkie połączone [składniki](components.md). W ten sposób usuwanie modelu z sceny odbywa się przez wywołanie `Destroy` na głównym węźle modelu, zwrócone przez `AzureSession.Actions.LoadModelAsync()` lub jego wariant SAS `AzureSession.Actions.LoadModelFromSASAsync()` .

Jednostki są tworzone, gdy serwer załaduje zawartość lub kiedy użytkownik chce dodać obiekt do sceny. Na przykład jeśli użytkownik chce dodać płaszczyznę wycinania w celu wizualizacji wnętrza siatki, użytkownik może utworzyć jednostkę, w której powinna istnieć płaszczyzna, a następnie dodać do niej składnik wycinania płaszczyzny.

## <a name="create-an-entity"></a>Tworzenie jednostki

Aby dodać nową jednostkę do sceny, na przykład przekazać ją jako obiekt główny do ładowania modeli lub dołączyć do niej składniki, użyj następującego kodu:

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->Position(Double3{ 1, 2, 3 });
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
ApiHandle<CutPlaneComponent> cutplane = *entity->FindComponentOfType<CutPlaneComponent>();
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
Double3 translation = *entity->Position();
Quaternion rotation = *entity->Rotation();
```


### <a name="querying-spatial-bounds"></a>Wykonywanie zapytania dotyczącego granic przestrzennych

Zapytania dotyczące powiązań to wywołania asynchroniczne, które działają w całej hierarchii obiektów, przy użyciu jednej jednostki jako katalogu głównego. Zobacz dedykowany rozdział dotyczący [granic obiektów](object-bounds.md).

### <a name="querying-metadata"></a>Wykonywanie zapytania dotyczącego metadanych

Metadane to dodatkowe dane przechowywane w obiektach, które są ignorowane przez serwer. Metadane obiektu są zasadniczo zestawem par (nazwa, wartość), gdzie _wartość_ może być typu liczbowego, Boolean lub String. Metadane można eksportować przy użyciu modelu.

Zapytania metadanych to wywołania asynchroniczne dla określonej jednostki. Zapytanie zwraca tylko metadane pojedynczej jednostki, a nie scalone informacje wykresu podrzędnego.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->IsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = *query->Result();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->AsInt64();

            // ...
        }
    });
```

Zapytanie powiedzie się, nawet jeśli obiekt nie zawiera żadnych metadanych.

## <a name="next-steps"></a>Następne kroki

* [Składniki](components.md)
* [Granice obiektów](object-bounds.md)
