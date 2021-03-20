---
title: Szukaj dokładnego momentu w filmach wideo z Video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak wyszukiwać dokładne chwily w filmach wideo przy użyciu Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96030530"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Szukaj dokładnego momentu w filmach wideo z Video Indexer

W tym temacie pokazano, jak używać witryny sieci Web Video Indexer do wyszukiwania dokładnego momentu w filmach wideo.

1. Przejdź do witryny sieci Web [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Określ słowa kluczowe wyszukiwania, a wyszukiwanie zostanie wykonane wśród wszystkich filmów wideo w bibliotece Twojego konta. 

    Wyszukiwanie można filtrować, wybierając pozycję **filtry**. W poniższym przykładzie szukamy "Microsoft", który jest wyświetlany jako tylko tekst na ekranie (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filtr, tylko tekst":::
1. Naciśnij pozycję **Wyszukaj** , aby zobaczyć wynik.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Wynik wyszukiwania wideo":::

    Jeśli wybierzesz jeden z wyników, gracz nawiąże to dokładne chwilę w filmie wideo.
1. Wyświetl i Przeszukaj podsumowanie informacji o filmie wideo, klikając pozycję **Odtwórz** na filmie wideo lub wybierając jeden z oryginalnych wyników wyszukiwania. 

    Można wyświetlać, przeszukiwać i edytować **szczegółowe informacje**. Po wybraniu jednego z szczegółowych informacji gracz zostanie pożądany do tego samego miejsca w filmie wideo.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Wyświetlanie, wyszukiwanie i edytowanie szczegółowych informacji o filmie wideo":::

    Jeśli osadzisz film wideo za pomocą widżetów Video Indexer, możesz uzyskać widok odtwarzacza/wglądu i synchronizację w aplikacji. Aby uzyskać więcej informacji, zobacz [osadzanie Video Indexer elementów widget w aplikacji](video-indexer-embed-widgets.md).
1. Transkrypcje można wyświetlać, przeszukiwać i edytować, klikając kartę **oś czasu** . 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Wyświetlanie, wyszukiwanie i edytowanie transkrypcji filmu wideo":::

    Aby edytować tekst, wybierz pozycję **Edytuj** w prawym górnym rogu i Zmień wymagany tekst. 

    Możesz również przetłumaczyć i pobrać transkrypcje, wybierając odpowiednią opcję w prawym górnym rogu. 

## <a name="embed-download-create-projects"></a>Osadź, pobieraj, Twórz projekty

Film wideo można osadzić, wybierając pozycję **</>Osadź** w filmie wideo. Aby uzyskać szczegółowe informacje, zobacz [osadzanie wizualizacji wizualnych w aplikacji](video-indexer-embed-widgets.md).

Możesz pobrać źródłowy film wideo, wgląd w dane wideo, transkrypcje, klikając pozycję **Pobierz** w obszarze wideo.

Można utworzyć klip na podstawie wideo dla określonych wierszy i chwilę, klikając polecenie **Otwórz w edytorze**. Następnie edytując wideo i zapisując projekt. Aby uzyskać szczegółowe informacje, zobacz [szczegółowe informacje o użyciu wideo](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Osadź, pobieraj, Twórz projekty wideo":::

## <a name="next-steps"></a>Następne kroki

[Przetwarzanie zawartości przy użyciu interfejsu REST API usługi Video Indexer](video-indexer-use-apis.md)
