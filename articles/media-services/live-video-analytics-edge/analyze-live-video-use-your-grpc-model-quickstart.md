---
title: Analizowanie wideo na żywo przy użyciu własnego modelu gRPC — Azure
description: W tym przewodniku szybki start nastąpi zastosowanie wizji komputerowej w celu przeanalizowania na żywo kanału informacyjnego wideo z (symulowanej) kamery IP.
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 907d402a4b0ef5d5e437322d02431a0ccd2d8da3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89421537"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model"></a>Szybki Start: analizowanie wideo na żywo przy użyciu własnego modelu gRPC

W tym przewodniku szybki start przedstawiono sposób korzystania z usługi Analiza filmów wideo na żywo na IoT Edge do analizowania strumieniowego kanału informacyjnego wideo z (symulowanej) kamery IP. Zobaczysz, jak zastosować model przetwarzania obrazów w celu wykrycia obiektów. Podzestaw ramek w dynamicznym kanale wideo jest wysyłany do usługi wnioskowania. Wyniki są wysyłane do centrum IoT Edge.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Omówienie

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Tworzenie i wdrażanie grafu multimediów

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretowanie wyników

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wypróbować inne Przewodniki Szybki Start, Zachowaj utworzone zasoby. W przeciwnym razie przejdź do Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

* Spróbuj uruchomić różne topologie grafu mediów przy użyciu protokołu gRPC.
* **Kompiluj i uruchamiaj Przykładowe rozszerzenia usługi Analiza wideo na żywo (LVA)**
<br/>Wypróbuj nasze Jupyter przykładowe notesy, które umożliwiają kompilowanie i uruchamianie opartych na [ONNX](http://onnx.ai/) modelach Yolo jako rozszerzenia Video Analytics (LVA).
    * [Przykładowy model YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/readme.md)
    * [Przykładowy model YOLOv4](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov4/yolov4-grpc-icpu-onnx/readme.md)

