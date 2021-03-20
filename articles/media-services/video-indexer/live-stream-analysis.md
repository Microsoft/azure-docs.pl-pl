---
title: Analiza strumienia na żywo przy użyciu Video Indexer
titleSuffix: Azure Media Services
description: W tym artykule pokazano, jak przeprowadzić analizę strumienia na żywo przy użyciu Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74186000"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Analiza strumienia na żywo za pomocą Video Indexer

Azure Media Services Video Indexer to usługa platformy Azure zaprojektowana w celu wyodrębnienia szczegółowych informacji z plików wideo i audio w trybie offline. Jest to analiza danego pliku multimedialnego, który został już utworzony z wyprzedzeniem. Jednak w przypadku niektórych przypadków użycia ważne jest, aby uzyskać informacje o multimediach z kanału informacyjnego na żywo tak szybko, jak to możliwe, aby odblokować działania i inne przypadki użycia naciśnięto w czasie. Na przykład takie bogate metadane w strumieniu na żywo mogą być używane przez producentów zawartości do automatyzowania produkcji w TELEWIZORze.

Rozwiązanie opisane w tym artykule pozwala klientom używać Video Indexer niemal w czasie rzeczywistym w ramach rozwiązań na żywo. Opóźnienie w indeksie może wynosić co najmniej cztery minuty przy użyciu tego rozwiązania, w zależności od fragmentów indeksowanych danych, rozdzielczości wejścia, typu zawartości i obliczeń używanych dla tego procesu.

![Metadane Video Indexer w strumieniu na żywo](./media/live-stream-analysis/live-stream-analysis01.png)

*Rysunek 1 — przykładowy odtwarzacz wyświetlający metadane Video Indexer w strumieniu na żywo*

[Rozwiązanie do analizy strumienia](https://aka.ms/livestreamanalysis) w tej chwili używa Azure Functions i dwóch Logic Apps do przetwarzania programu na żywo z kanału na żywo w Azure Media Services z video Indexer i wyświetla wynik z Azure Media Player pokazującego strumień wynikowy niemal w czasie rzeczywistym.

Na wysokim poziomie składa się z dwóch głównych kroków. Pierwszy krok jest uruchamiany co 60 sekund i pobiera podklip z ostatnich 60 sekund, a następnie tworzy element zawartości z niego i indeksuje go za pośrednictwem Video Indexer. Następnie drugi krok jest wywoływany po zakończeniu indeksowania. Przechwycone informacje są przetwarzane, wysyłane do Azure Cosmos DB, a podcinanie jest usuwane.

Przykładowy odtwarzacz odtwarza strumień na żywo i pobiera szczegółowe informacje z Azure Cosmos DB przy użyciu dedykowanej funkcji platformy Azure. Wyświetla metadane i miniatury zsynchronizowane z wideo na żywo.

![Dwie aplikacje logiki przetwarzają strumień na żywo co minutę w chmurze](./media/live-stream-analysis/live-stream-analysis02.png)

*Rysunek 2 — dwie aplikacje logiki przetwarzają strumień na żywo co minutę w chmurze.*

## <a name="step-by-step-guide"></a>Przewodnik krok po kroku 

Pełny kod i przewodnik krok po kroku służący do wdrażania wyników można znaleźć w projekcie usługi GitHub w [celu analizy multimediów na żywo za pomocą Video Indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)
