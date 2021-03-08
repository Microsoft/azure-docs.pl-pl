---
title: Ciągłe nagrywanie wideo — Azure
description: Stałe nagrywanie wideo (CVR) odnosi się do procesu ciągłego rejestrowania wideo ze źródła wideo. W tym temacie omówiono, co to jest CVR.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453754"
---
# <a name="continuous-video-recording"></a>Ciągłe nagrywanie wideo  

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

* [Koncepcje grafu multimediów](media-graph-concept.md)
* [Koncepcja nagrywania wideo](video-recording-concept.md)

## <a name="overview"></a>Omówienie

Stałe nagrywanie wideo (CVR) odnosi się do procesu ciągłego rejestrowania wideo ze źródła wideo. Usługa Analiza filmów wideo na żywo na IoT Edge obsługuje stałe rejestrowanie wideo na podstawie 24x7 z aparatu CCTV za pośrednictwem [grafu multimedialnego](media-graph-concept.md) , który składa się z węzła źródłowego RTSP i węzła ujścia zasobów. Na poniższym diagramie przedstawiono graficzną reprezentację takiego wykresu multimedialnego. Reprezentację w formacie JSON [topologii wykresu](media-graph-concept.md#media-graph-topologies-and-instances) takiego wykresu multimedialnego można znaleźć [tutaj](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Ciągłe nagrywanie wideo":::

Wykres multimedialny przedstawiony powyżej można uruchomić na urządzeniu brzegowym, przy czym plik wideo dotyczący nagrywania ujścia zasobów do Azure Media Services [elementu zawartości](terminology.md#asset). Film wideo zostanie zarejestrowany tak długo, jak wykres multimedialny pozostaje w stanie aktywowany. Ponieważ film wideo jest rejestrowany jako zasób, można go odtworzyć przy użyciu istniejących możliwości przesyłania strumieniowego Media Services. Zobacz [odtwarzanie zapisanej zawartości](video-playback-concept.md) , aby uzyskać więcej szczegółów.

## <a name="resilient-recording"></a>Odporne nagrania

Analiza filmów wideo na żywo w systemie IoT Edge obsługuje mniej niż idealne warunki sieci, w których urządzenie brzegowe może czasami utracić łączność z chmurą lub obsłużyć spadek dostępnej przepustowości. W tym celu film wideo ze źródła jest rejestrowany lokalnie w pamięci podręcznej i automatycznie synchronizowany z elementem zawartości. W przypadku badania [JSON topologii wykresu](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)zostanie wyświetlonych następujące właściwości:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

Te dwie właściwości odnoszą się do odpornego na nagrania (obie są również wymagane właściwości dla węzła ujścia zasobów). Właściwość localMediaCachePath informuje ujścia zasobów o konieczności użycia tej ścieżki folderu do buforowania danych nośnika przed przekazaniem ich do elementu zawartości. Ten artykuł zawiera informacje o [tym](../../iot-edge/how-to-access-host-storage-from-module.md) , jak moduł Edge może korzystać z lokalnego magazynu urządzenia. Właściwość localMediaCacheMaximumSizeMiB definiuje ilość miejsca na dysku, które może być używane przez ujścia zasobów jako pamięć podręczną (1 MiB = 1024 * 1024 bajtów). 

Jeśli moduł graniczny utraci łączność przez bardzo długi czas, a zawartość przechowywana w folderze pamięci podręcznej osiągnie wartość localMediaCacheMaximumSizeMiB, ujścia zasobów zacznie odrzucać dane z pamięci podręcznej, rozpoczynając od najstarszych danych. Na przykład jeśli urządzenie utraciło łączność w od 10:00, a pamięć podręczna osiągnie limit maksymalny w 18:00, obiekt ujścia zasobów zacznie usuwać dane zarejestrowane w od 10:00. 

Po przywróceniu łączności sieciowej ujścia zasobów rozpocznie przekazywanie z pamięci podręcznej, rozpoczynając od najstarszych danych. W powyższym przykładzie Załóżmy, że w pamięci podręcznej należy odrzucić 5 minut, a połączenie zostało przywrócone (Załóżmy o godzinie 6:02PM), a następnie ujścia zasobów rozpocznie przekazywanie od znaku 10:05AM.

Jeśli później przeanalizuje zasób przy użyciu [tych](playback-recordings-how-to.md) interfejsów API, zobaczysz, że w elemencie zawartości występuje przerwy z około od 10:00 do 10:05AM.

## <a name="segmented-recording"></a>Nagrywanie z segmentacją  

Jak wspomniano powyżej, węzeł ujścia zasobów będzie rejestrować wideo w lokalnej pamięci podręcznej i okresowo ładować wideo do chmury. Właściwość segmentLength (pokazana w powyższej sekcji) pomoże Ci kontrolować koszt transakcji zapisu związanych z zapisywaniem danych na koncie magazynu, w którym jest rejestrowany element zawartości. Na przykład w przypadku zwiększenia okresu przekazywania z 30 sekund do 5 minut, liczba transakcji magazynu zostanie pokazana przez współczynnik 10 (5 * 60/30).

Właściwość segmentLength gwarantuje, że moduł Edge przekaże wideo maksymalnie raz na segmentLength sekund. Ta właściwość ma minimalną wartość 30 sekund (domyślnie) i może być zwiększona o 30 sekund, maksymalnie o 5 minut.

> [!NOTE]
> Zapoznaj się z artykułem [nagrania odtwarzania](playback-recordings-how-to.md) , aby uzyskać efekt segmentLength podczas odtwarzania.

## <a name="see-also"></a>Zobacz też

* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
* [Odtwarzanie zarejestrowanej zawartości](video-playback-concept.md)

## <a name="next-steps"></a>Następne kroki

[Samouczek: ciągłe nagrywanie filmów wideo](continuous-video-recording-tutorial.md)
