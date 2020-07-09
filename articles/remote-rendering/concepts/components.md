---
title: Składniki
description: Definicja składników w zakresie renderowania zdalnego na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: bd2a6e16d34bf6c5b59ce7fa5c99975d44947770
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021674"
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

## <a name="next-steps"></a>Następne kroki

* [Granice obiektów](object-bounds.md)
* [Siatki](meshes.md)
