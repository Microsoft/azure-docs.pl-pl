---
title: Koncepcje Video Indexer
titleSuffix: Azure Media Services
description: W tym artykule opisano niektóre koncepcje Azure Media Services usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900673"
---
# <a name="video-indexer-concepts"></a>Koncepcje Video Indexer
 
W tym artykule opisano niektóre koncepcje usługi Video Indexer.
    
## <a name="summarized-insights"></a>Podsumowanie szczegółowych informacji

Podsumowanie szczegółowych informacji zawiera Zagregowany widok danych: twarze, tematy, emocji. Na przykład zamiast przechodzenia przez poszczególne tysiące zakresów czasu i sprawdzania, które twarze znajdują się w nim, podsumowanie szczegółowych informacji zawiera wszystkie powierzchnie i dla każdej z nich, przedziały czasu, w których pojawiają się i% czasu wyświetlania.

## <a name="time-range-vs-adjusted-time-range"></a>zakres czasu a dostosowany zakres czasu

TimeRange to zakres czasu w oryginalnym wideo. AdjustedTimeRange jest zakresem czasu odnoszącym się do bieżącej listy odtwarzania. Ze względu na to, że możesz utworzyć listę odtwarzania z różnych wierszy z różnych filmów wideo, możesz użyć 1-godzinnego wideo i z niego korzystać tylko 1 wiersz, na przykład 10:00-10:15. W takim przypadku będziesz mieć listę odtwarzania z 1 wierszem, gdzie zakres czasu to 10:00-10:15, ale adjustedTimeRange to 00:00-00:15.
 
## <a name="blocks"></a>Bloki

Bloki mają ułatwić przechodzenie przez dane. Blok może zostać wyodrębniony na przykład na podstawie zmiany osoby mówiącej lub wystąpienia długiej przerwy.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat rozpoczynania pracy, zobacz [jak utworzyć konto i przekazać swoje pierwsze wideo](video-indexer-get-started.md).

## <a name="see-also"></a>Zobacz także

[Omówienie usługi Video Indexer](video-indexer-overview.md)
