---
title: Korzystaj z przesunięć czasowych i danych wyjściowych, aby utworzyć odtwarzanie wideo na żądanie
description: W tym artykule opisano, jak używać przesuwania i przesyłania strumieniowego na żywo w celu rejestrowania strumienia na żywo i tworzenia odtwarzania na żądanie.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f5b475f15a5dc751de154965e3d1e78a85fbd4c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897870"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Korzystaj z przesunięć czasowych i danych wyjściowych, aby utworzyć odtwarzanie wideo na żądanie

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W Azure Media Services obiekt [danych wyjściowych na żywo](/rest/api/media/liveoutputs) przypomina cyfrowy rejestrator wideo, który będzie przechwytywać i rejestrować strumień na żywo do zasobu na koncie Media Services. Zarejestrowana zawartość jest zachowywana w kontenerze zdefiniowanym [przez zasób zasobów](/rest/api/media/assets) (kontener znajduje się na koncie usługi Azure Storage podłączonym do Twojego konta). Na żywo dane wyjściowe umożliwiają również kontrolowanie niektórych właściwości wychodzącego strumienia na żywo, takich jak część strumienia jest przechowywana w archiwum archiwalnym (na przykład pojemność DVR w chmurze) lub gdy przeglądający mogą zacząć oglądać strumień na żywo. Archiwum na dysku jest archiwum cykliczne "okno", które zawiera tylko ilość zawartości, która jest określona we właściwości **archiveWindowLength** na żywo danych wyjściowych. Zawartość spoza tego okna jest automatycznie odrzucana z kontenera magazynu i nie jest odzyskiwalna. Wartość archiveWindowLength reprezentuje czas trwania przedziału ISO 8601 (na przykład PTHH: MM: SS), który określa pojemność DVR. Wartość można ustawić z co najmniej jednej minuty do maksymalnie 25 godzin.

Relacja między wydarzeniem działającym na żywo a jego wyjściem dynamicznym jest podobna do tradycyjnego rozgłaszania TV, ponieważ kanał (wydarzenie na żywo) reprezentuje stały strumień wideo, a nagrywanie (wyjście na żywo) jest ograniczone do określonego segmentu czasu (na przykład wiadomości wieczorem z 6:30 do 7:13:00). Gdy strumień przepływa do zdarzenia na żywo, możesz rozpocząć zdarzenie przesyłania strumieniowego, tworząc element zawartości, dane wyjściowe i lokalizator przesyłania strumieniowego. Na żywo wyjście będzie archiwizować strumień i udostępnić je osobom oglądającym za pomocą [punktu końcowego przesyłania strumieniowego](/rest/api/media/streamingendpoints). Można utworzyć wiele danych wyjściowych na żywo (maksymalnie trzy) dla zdarzenia na żywo o różnych długościach i ustawieniach archiwum. Informacje o przepływie pracy przesyłania strumieniowego na żywo znajdują się w sekcji [ogólnych kroków](live-streaming-overview.md#general-steps) .

## <a name="using-a-dvr-during-an-event"></a>Używanie DVR w trakcie zdarzenia

W tej sekcji omówiono sposób użycia DVR podczas zdarzenia do kontrolowania, jakie fragmenty strumienia są dostępne dla przewijania do tyłu.

`archiveWindowLength`Wartość określa, jak daleko wstecz w czasie przeglądarka może przechodzić z bieżącej pozycji na żywo. `archiveWindowLength`Wartość określa również, jak długo mogą się zwiększać manifesty klienta.

Załóżmy, że przesyłasz strumieniowo grę piłkarskią i zawiera ona `ArchiveWindowLength` tylko 30 minut. Przeglądarka, która zacznie oglądać wydarzenie 45 minut po rozpoczęciu gry, może odwrócić do maksymalnie 15 minut. Dane wyjściowe na żywo dla gry będą kontynuowane do momentu zatrzymania zdarzenia na żywo. Zawartość spoza archiveWindowLength jest ciągle odrzucana z magazynu i nie jest odzyskiwalna. W tym przykładzie film wideo między początkiem zdarzenia a 15-minutowy znacznik zostałby przeczyszczony z DVR i z kontenera w usłudze BLOB Storage dla elementu zawartości. Archiwum nie jest możliwe do odzyskania i zostaje usunięte z kontenera w usłudze Azure Blob Storage.

Wydarzenie na żywo obsługuje maksymalnie trzy współbieżnie uruchomione wyniki na żywo (można utworzyć maksymalnie 3 nagrania/archiwa z jednego strumienia na żywo w tym samym czasie). Ta obsługa umożliwia publikowanie i archiwizowanie różnych części zdarzenia zgodnie z wymaganiami. Załóżmy, że trzeba emitować 24x7 na żywo i utworzyć "nagrania" różnych programów w ciągu dnia, aby zaoferować klientom jako zawartość na żądanie w celu przeprowadzenia wyświetlania. W tym scenariuszu najpierw utworzysz podstawowe wyjście na żywo z krótkim oknem archiwum o wartości 1 godzinę lub mniej — jest to podstawowy strumień na żywo, w którym Oglądasz Twoje przeglądarki. Utworzysz lokalizator przesyłania strumieniowego dla tych danych wyjściowych na żywo i opublikujesz go w aplikacji lub witrynie sieci Web jako kanał informacyjny "Live". Gdy uruchomione jest wydarzenie na żywo, można programowo utworzyć dwa współbieżne dane wyjściowe na żywo na początku programu (lub 5 minut na wczesnym etapie, aby udostępnić kilka dojść do późniejszego przycięcia). Te drugie dane wyjściowe można usunąć 5 minut po zakończeniu programu. W tym drugim elemencie zawartości można utworzyć nowy lokalizator przesyłania strumieniowego w celu opublikowania tego programu jako zasobu na żądanie w katalogu aplikacji. Proces ten można powtórzyć wielokrotnie dla innych granic programu lub podświetlenia, które mają być udostępniane jako wideo na żądanie, a wszystko to, gdy kanał informacyjny "Live" z pierwszego wyjścia na żywo kontynuuje emitowanie strumieniowego źródła danych.

## <a name="creating-an-archive-for-on-demand-playback"></a>Tworzenie archiwum na potrzeby odtwarzania na żądanie

Element zawartości, który jest archiwizowany na żywo, ma automatycznie stać się zasobem na żądanie po usunięciu danych wyjściowych na żywo. Przed zatrzymaniem zdarzenia na żywo należy usunąć wszystkie dane wyjściowe na żywo. Można użyć opcjonalnej flagi [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) , aby automatycznie usuwać wyjścia na żywo po zatrzymaniu.

Nawet po zatrzymaniu i usunięciu zdarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość jako wideo na żądanie, tak długo, jak nie usuniesz elementu zawartości. Nie można usunąć elementu zawartości, jeśli jest on używany przez zdarzenie. najpierw należy usunąć zdarzenie.

Jeśli zasób danych wyjściowych na żywo został opublikowany przy użyciu lokalizatora przesyłania strumieniowego, zdarzenie na żywo (do okna DVR) będzie nadal widoczne do momentu wygaśnięcia lub usunięcia lokalizatora przesyłania strumieniowego, w zależności od tego, co nastąpi wcześniej.

Aby uzyskać więcej informacji, zobacz:

- [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)

> [!NOTE]
> Po usunięciu danych wyjściowych na żywo nie jest usuwany podstawowy element zawartości i zawartość w elemencie zawartości.

## <a name="next-steps"></a>Następne kroki

* [Przytnij klipy wideo](subclip-video-rest-howto.md).
* [Zdefiniuj filtry dla zasobów](filters-dynamic-manifest-rest-howto.md).
