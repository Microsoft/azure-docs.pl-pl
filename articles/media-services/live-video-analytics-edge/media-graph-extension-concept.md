---
title: Co to jest rozszerzenie Media Graph — Azure
description: Funkcja analizy filmów wideo na żywo w systemie IoT Edge umożliwia rozszerzanie możliwości przetwarzania wykresów multimedialnych za pomocą węzła rozszerzenia grafu.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 09fc2b71a6188caf1bdd9b7a2ef4da287f022845
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691856"
---
# <a name="media-graph-extension"></a>Rozszerzenie grafu multimediów

Funkcja analizy filmów wideo na żywo w systemie IoT Edge umożliwia rozszerzanie możliwości przetwarzania wykresów multimedialnych za pomocą węzła rozszerzenia grafu. Wtyczka rozszerzenia analitycznego może korzystać z tradycyjnych technik przetwarzania obrazów lub modeli obrazu systemu AI. Rozszerzenia programu Graph są włączane przez dołączenie węzła procesora rozszerzenia na grafie nośnika. Węzeł procesora rozszerzenia przekazuje ramki wideo do skonfigurowanego punktu końcowego i pełni rolę interfejsu dla rozszerzenia. Połączenie może być nawiązywane w lokalnym lub zdalnym punkcie końcowym i może być zabezpieczone przez uwierzytelnianie i szyfrowanie TLS, jeśli jest to wymagane. Ponadto węzeł procesora rozszerzenia wykresu umożliwia opcjonalne skalowanie i kodowanie klatek wideo przed ich przesłaniem do niestandardowego rozszerzenia.

Usługa Analiza filmów wideo na żywo obsługuje dwa rodzaje procesorów rozszerzeń programu Media Graph:

* [Procesor rozszerzeń HTTP](media-graph-concept.md#http-extension-processor)
* [Procesor rozszerzenia gRPC](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>Procesor rozszerzeń HTTP

Procesor rozszerzeń HTTP umożliwia scenariusze rozszerzalności przy użyciu protokołu HTTP, w którym wydajność i/lub optymalne wykorzystanie zasobów nie jest podstawowym problemem. Możesz uwidocznić własne AI na grafie multimediów za pośrednictwem punktu końcowego REST protokołu HTTP. 

Użyj węzła procesora rozszerzenia HTTP, gdy:

* Potrzebujesz lepszej współpracy z istniejącymi systemami HTTP Inferencing.
* Akceptowalny jest transfer danych o niskiej wydajności.
* Chcesz użyć prostego interfejsu odpowiedzi na żywo do analizy filmów wideo.

## <a name="grpc-extension-processor"></a>Procesor rozszerzenia gRPC

Procesor rozszerzeń gRPC umożliwia obsługę scenariuszy rozszerzalności przy użyciu opartego na gRPC protokołu o wysokiej dostępności. Jest to idealne rozwiązanie w scenariuszach, w których wydajność i/lub optymalne wykorzystanie zasobów jest priorytetem. Procesor rozszerzeń gRPC umożliwia pełne korzystanie z definicji danych strukturalnych. gRPC oferuje wysoką wydajność transferu zawartości przy użyciu:

* [pamięć udostępniona w usłudze Box](https://en.wikipedia.org/wiki/Shared_memory) lub 
* bezpośrednie osadzanie zawartości w treści gRPC komunikatów. 

Procesor rozszerzeń gRPC może służyć do wysyłania właściwości nośnika wraz z wymianą komunikatów wnioskowania.
Należy więc użyć węzła procesora rozszerzenia gRPC, gdy:

* Chcesz użyć kontraktu strukturalnego (na przykład komunikatów strukturalnych dla żądań i odpowiedzi)
* Chcesz użyć buforów protokołu ([protobuf](https://developers.google.com/protocol-buffers)) jako podstawowego formatu wymiany komunikatów do komunikacji.
* Chcesz komunikować się z serwerem gRPC w ramach sesji pojedynczego strumienia zamiast tradycyjnego modelu żądania-odpowiedzi, który wymaga obsługi niestandardowego żądania, aby analizować przychodzące żądania i wywoływać odpowiednie funkcje implementacji. 
* Potrzebujesz małych opóźnień i wysokiej przepustowości komunikacji między dynamicznymi analizami wideo a modułem.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Korzystanie z modelu inferencing przy użyciu usługi Live Video Analytics

Rozszerzenia grafu multimediów umożliwiają uruchamianie wybranych modeli wnioskowania na dowolnym dostępnym środowisku uruchomieniowym wnioskowania, takim jak ONNX, TensorFlow, PyTorch lub inne w swoim kontenerze platformy Docker. Rozszerzenie niestandardowe inferencing należy wdrożyć wraz z modułem Edge usługi Analiza wideo na żywo w celu uzyskania najlepszej wydajności, a następnie będzie wywoływana za pośrednictwem procesora rozszerzenia HTTP lub procesora rozszerzenia gRPC zawartego w topologii grafu. Ponadto częstotliwość wywołań do rozszerzenia niestandardowego można ograniczyć, opcjonalnie dodając [procesor detektora ruchu](media-graph-concept.md#motion-detection-processor) i przechodzący [procesor filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) do procesora rozszerzenia nośnika.

Na poniższym diagramie przedstawiono przepływ danych wysokiego poziomu:
 
![Przepływ danych](./media/media-graph-extension/data-flow.png)

## <a name="samples"></a>Przykłady

Wyewidencjonuj Przykłady naszych przykładów z [notesu Jupyter](https://jupyter.org/) na potrzeby analizy filmów wideo na żywo. Te notesy zawierają instrukcje krok po kroku dotyczące:

* Jak utworzyć obraz kontenera Docker usługi rozszerzenia
* Jak wdrożyć usługę rozszerzenia jako kontener wraz z kontenerem analizy wideo na żywo
* Jak korzystać z grafu multimediów analizy wideo na żywo z klientem rozszerzenia i wskazać go w punkcie końcowym rozszerzenia gRPC
