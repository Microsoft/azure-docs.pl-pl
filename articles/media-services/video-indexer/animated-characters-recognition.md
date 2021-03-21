---
title: Wykrywanie znaków animowanych za pomocą Video Indexer
titleSuffix: Azure Media Services
description: W tym temacie przedstawiono sposób korzystania z wykrywania animowanych znaków w Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854754"
---
# <a name="animated-character-detection-preview"></a>Wykrywanie znaków animowanych (wersja zapoznawcza)

Azure Media Services Video Indexer obsługuje wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości poprzez integrację z [Cognitive Services wzrokiem niestandardowym](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Ta funkcja jest dostępna zarówno w portalu, jak i za pomocą interfejsu API.

Po przekazaniu animowanego wideo z określonym modelem animacji Video Indexer wyodrębnia ramki kluczowe, wykrywa animowane znaki w tych ramkach, grupuje podobne znaki i wybiera najlepszy przykład. Następnie wysyła zgrupowane znaki do Custom Vision, które identyfikują znaki na podstawie modeli, na których nadano szkolenia. 

Przed rozpoczęciem szkolenia modelu znaki są wykrywane namelessly. Podczas dodawania nazw i uczenia modelu Video Indexer rozpoznawania znaków i nadaje im odpowiednie nazwy.

## <a name="flow-diagram"></a>Diagram przepływu

Na poniższym diagramie przedstawiono przepływ procesu wykrywania animowanych znaków.

![Diagram przepływu](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Konta

W zależności od typu konta Video Indexer dostępne są różne zestawy funkcji. Aby uzyskać informacje na temat sposobu łączenia konta z platformą Azure, zobacz [Tworzenie konta Video Indexer połączonego z platformą Azure](connect-to-azure.md).

* Konto wersji próbnej: Video Indexer używa konta wewnętrznego Custom Vision do tworzenia modelu i łączenia go z kontem Video Indexer. 
* Płatne konto: połączenie konta Custom Vision z kontem Video Indexer (jeśli jeszcze tego nie zrobiono, należy najpierw utworzyć konto).

### <a name="trial-vs-paid"></a>Wersja próbna a płatna

|Funkcjonalność|Wersja próbna|Płatna|
|---|---|---|
|Konto Custom Vision|Zarządzane w tle przez Video Indexer. |Twoje konto Custom Vision jest połączone z Video Indexer.|
|Liczba modeli animacji|Jeden|Do 100 modeli na konto (ograniczenie Custom Vision).|
|Trenowanie modelu|Video Indexer pociąga za model dla nowych znaków dodatkowe przykłady istniejących znaków.|Właściciel konta pociąga za niego gotowość do wprowadzenia zmian.|
|Opcje zaawansowane w Custom Vision|Brak dostępu do portalu Custom Vision.|Modele można dostosować samodzielnie w portalu Custom Vision.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Używanie wykrywania animowanych znaków z portalem i interfejsem API

Aby uzyskać szczegółowe informacje, zobacz [Używanie wykrywania animowanych znaków z portalem i interfejsem API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Ograniczenia

* Obecnie funkcja "Identyfikacja animacji" nie jest obsługiwana w regionie East-Asia.
* Znaki, które wydaje się być małe lub daleko w wideo, mogą nie być poprawnie identyfikowane, jeśli jakość wideo jest niska.
* Zalecenie polega na użyciu modelu na zestaw animowanych znaków (na przykład dla serii animowanej).

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)