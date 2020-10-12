---
title: Składniki
description: Definicja składników w zakresie renderowania zdalnego na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: a488e2499b92b290ad2b55120c3c70a18d45d426
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613945"
---
# <a name="components"></a>Składniki

Renderowanie zdalne na platformie Azure używa wzorca [systemu składników jednostki](https://en.wikipedia.org/wiki/Entity_component_system) . Chociaż [jednostki](entities.md) reprezentują położenie i hierarchiczne składowe obiektów, składniki są odpowiedzialne za wdrożenie zachowań.

Najczęściej używane typy składników to [:::no-loc text="mesh components":::](meshes.md) , które dodają oczka do potoku renderowania. Podobnie [składniki światła](../overview/features/lights.md) są używane do dodawania oświetlenia i [wycinania składników płaszczyzny](../overview/features/cut-planes.md) są używane do wycinania otwartych siatek.

Wszystkie te składniki używają transformacji (położenia, obrotu i skali) jednostki, do której są dołączone, jako punktu odniesienia.

## <a name="working-with-components"></a>Praca ze składnikami

Można łatwo dodawać, usuwać i manipulować składnikami programowo:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

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

* [ComponentBase C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [Zdalnymanager.](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.createcomponent)
* [Obiekt C# Entity. FindComponentOfType ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++ ComponentBase](https://docs.microsoft.com/cpp/api/remote-rendering/componentbase)
* [C++ RemoteManager::](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#createcomponent)
* [Jednostka C++:: FindComponentOfType ()](https://docs.microsoft.com/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Następne kroki

* [Granice obiektów](object-bounds.md)
* [Siatki](meshes.md)
