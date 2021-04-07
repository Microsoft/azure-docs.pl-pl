---
title: Składniki
description: Definicja składników w zakresie renderowania zdalnego na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99592198"
---
# <a name="components"></a>Składniki

Renderowanie zdalne na platformie Azure używa wzorca [systemu składników jednostki](https://en.wikipedia.org/wiki/Entity_component_system) . Chociaż [jednostki](entities.md) reprezentują położenie i hierarchiczne składowe obiektów, składniki są odpowiedzialne za wdrożenie zachowań.

Najczęściej używane typy składników to [:::no-loc text="mesh components":::](meshes.md) , które dodają oczka do potoku renderowania. Podobnie [składniki światła](../overview/features/lights.md) są używane do dodawania oświetlenia i [wycinania składników płaszczyzny](../overview/features/cut-planes.md) są używane do wycinania otwartych siatek.

Wszystkie te składniki używają transformacji (położenia, obrotu i skali) jednostki, do której są dołączone, jako punktu odniesienia.

## <a name="working-with-components"></a>Praca ze składnikami

Można łatwo dodawać, usuwać i manipulować składnikami programowo:

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

Składnik jest dołączany do jednostki podczas tworzenia. Nie można go później przenieść do innej jednostki. Składniki są jawnie usuwane z `Component.Destroy()` lub automatycznie, gdy jednostka właściciela składnika zostanie zniszczona.

Tylko jedno wystąpienie każdego typu składnika można dodać do jednostki jednocześnie.

## <a name="unity-specific"></a>Specyficzne dla aparatu Unity

Integracja aparatu Unity ma dodatkowe funkcje rozszerzenia do współpracy ze składnikami programu. Zobacz [obiekty i składniki gier środowiska Unity](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [ComponentBase C#](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection. GetComponent ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [Obiekt C# Entity. FindComponentOfType ()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++ ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection:: ()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [Jednostka C++:: FindComponentOfType ()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Następne kroki

* [Granice obiektów](object-bounds.md)
* [Siatki](meshes.md)