---
title: Tryby renderowania
description: Opisuje różne tryby renderowania po stronie serwera
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594505"
---
# <a name="rendering-modes"></a>Tryby renderowania

Renderowanie zdalne oferuje dwa główne tryby działania, tryb **TileBasedComposition** i tryb **DepthBasedComposition** . Te tryby określają sposób dystrybuowania obciążenia między wieloma procesorami GPU na serwerze. Tryb należy określić w czasie połączenia i nie można go zmienić w czasie wykonywania.

Oba tryby są wyposażone w zalety, ale również z nieodłącznymi ograniczeniami funkcji, więc wybranie najbardziej odpowiedniego trybu jest zależne od przypadku użycia.

## <a name="modes"></a>Środka

Dwa tryby są teraz omówione bardziej szczegółowo.

### <a name="tilebasedcomposition-mode"></a>Tryb "TileBasedComposition"

W trybie **TileBasedComposition** każdy związany z procesorem GPU renderuje określone podprostokąty (kafelki) na ekranie. Główny procesor GPU składa końcowy obraz z kafelków, zanim zostanie wysłany jako ramka wideo do klienta. W związku z tym wszystkie procesory GPU wymagają tego samego zestawu zasobów do renderowania, więc ładowane zasoby muszą pasować do pamięci pojedynczego procesora GPU.

Jakość renderowania w tym trybie jest nieco lepsza niż w trybie **DepthBasedComposition** , ponieważ MSAA może współdziałać z pełnym zestawem geometrii dla każdego procesora GPU. Poniższy zrzut ekranu pokazuje, że wygładzanie działa prawidłowo dla obu krawędzi:

![MSAA w TileBasedComposition](./media/service-render-mode-quality.png)

Ponadto w tym trybie każda część może zostać przełączona do przezroczystego materiału lub przełączona w tryb **wyświetlania** przez [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Tryb "DepthBasedComposition"

W trybie **DepthBasedComposition** każdy procesor GPU jest renderowany w pełnej rozdzielczości ekranu, ale tylko podzestaw siatki. Ostateczne składanie obrazów na głównym procesorze GPU wymaga, aby części zostały prawidłowo scalone zgodnie ze swoimi informacjami o głębokości. W naturalny sposób ładunek pamięci jest dystrybuowany między procesorami GPU, co pozwala na renderowanie modeli, które nie mieszczą się w pamięci jednego procesora GPU.

Każdy pojedynczy procesor GPU używa technologii MSAA do AntiAlias zawartości lokalnej. Jednak może istnieć nieodłączne aliasowanie między krawędziami z różnych procesorów GPU. Ten efekt jest skorygowany przez dostosujesz końcowego obrazu, ale jakość technologii MSAA nadal jest gorsza niż w trybie **TileBasedComposition** .

Artefakty MSAA są zilustrowane na poniższym obrazie: ![ MSAA w DepthBasedComposition](./media/service-render-mode-balanced.png)

Antyaliasowanie działa prawidłowo między rzeźbami i Curtain, ponieważ obie części są renderowane na tym samym procesorze GPU. Z drugiej strony krawędzie między Curtain i ścianą pokazują pewne aliasy, ponieważ te dwie części składają się z różnych procesorów GPU.

Największym ograniczeniem tego trybu jest, że części geometrii nie mogą być przełączane do przezroczystych materiałów dynamicznie ani nie działają w trybie **wyświetlania** przez [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Inne funkcje przesłaniania stanu (kontur, tinta koloru,...) działają, chociaż. Również materiały oznaczone jako przezroczyste w czasie konwersji działają prawidłowo w tym trybie.

### <a name="performance"></a>Wydajność

Charakterystyki wydajności obu trybów różnią się w zależności od przypadków użycia i trudno jest przyczynić się do uzyskania ogólnych zaleceń. Jeśli ograniczenia wymienione powyżej (pamięć lub przezroczystość/wyświetlanie) nie są ograniczone, zaleca się wypróbowanie obu trybów i monitorowanie wydajności przy użyciu różnych pozycji aparatu.

## <a name="setting-the-render-mode"></a>Ustawianie trybu renderowania

Tryb renderowania używany na serwerze renderowania zdalnego jest określany w trakcie `RenderingSession.ConnectAsync` za pośrednictwem `RendererInitOptions` .

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [C# RenderingSession. ConnectAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [Struktura RendererInitOptions języka C#](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [C++ RenderingSession:: ConnectToConnectAsyncRuntime ()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [Struktura C++ RendererInitOptions](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>Następne kroki

* [Sesje](../concepts/sessions.md)
* [Składnik przesłonięcia stanu hierarchicznego](../overview/features/override-hierarchical-state.md)