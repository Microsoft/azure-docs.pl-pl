---
title: Koncepcje Video Indexer — Azure
titleSuffix: Azure Media Services Video Indexer
description: Ten artykuł zawiera krótkie omówienie Azure Media Services Video Indexer Terminologia i pojęcia.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633984"
---
# <a name="video-indexer-concepts"></a>Koncepcje Video Indexer

Ten artykuł zawiera krótkie omówienie Azure Media Services Video Indexer Terminologia i pojęcia.

## <a name="audiovideocombined-insights"></a>Audio/wideo/połączone szczegółowe dane

Po przekazaniu wideo do Video Indexer, analizuje wizualizacje i dźwięk, uruchamiając różne modele AI. Ponieważ Video Indexer analizuje wideo, szczegółowe informacje, które są wyodrębniane przez modele AI. Aby uzyskać więcej informacji, zobacz [Omówienie](video-indexer-overview.md).

## <a name="confidence-scores"></a>Wyniki pewności

Wynik pewności wskazuje na wiarygodność informacji. Jest to liczba z zakresu od 0,0 do 1,0. Im wyższy wynik — tym większy poziom zaufania odpowiedzi. Na przykład 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Moderowanie zawartości

Użyj modeli moderowania zawartości tekstowych i wizualnych, aby zapewnić użytkownikom bezpieczeństwo przed niestosowną zawartością i sprawdzać, czy zawartość publikowana odpowiada wartościom organizacji. Możesz automatycznie blokować niektóre wideo lub otrzymywać alerty dla użytkowników o zawartości. Aby uzyskać więcej informacji, zobacz [spostrzeżenia: wizualizacja zawartości wizualnej i tekstowej](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Bloki   

Bloki mają ułatwić przechodzenie przez dane. Blok może zostać wyodrębniony na przykład na podstawie zmiany osoby mówiącej lub wystąpienia długiej przerwy.  

## <a name="project-and-editor"></a>Projekt i Edytor

Witryna sieci Web [Video Indexer](https://www.videoindexer.ai/) umożliwia korzystanie z szczegółowego wglądu w dane wideo do: znajdowanie odpowiedniej zawartości multimedialnej, lokalizowanie interesujących Cię części i używanie wyników do tworzenia zupełnie nowego projektu. Po utworzeniu projekt może być renderowany i pobierany z Video Indexer i być używany we własnych aplikacjach do edycji lub podrzędnych przepływów pracy.

Niektóre scenariusze, w których może się okazać, że ta funkcja jest przydatna: 

* Tworzenie podświetlania filmów dla przyczep.
* Używanie starych klipów wideo w przypadku rzutowania wiadomości.
* Tworzenie krótszej zawartości dla mediów społecznościowych.

Aby uzyskać więcej informacji, zobacz [Używanie edytora do tworzenia projektów](use-editor-create-project.md).

## <a name="keyframes"></a>Ramki kluczowe

Video Indexer wybiera ramki, które najlepiej reprezentują każdy zrzut. Klatki kluczowe to reprezentatywne ramki wybrane z całego wideo w oparciu o właściwości estetyczne (na przykład kontrast i trwałość). Aby uzyskać więcej informacji, zobacz [sceny, zrzuty i ramki kluczowe](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>zakres czasu a dostosowany zakres czasu   

TimeRange to zakres czasu w oryginalnym wideo. AdjustedTimeRange jest zakresem czasu odnoszącym się do bieżącej listy odtwarzania. Ze względu na to, że możesz utworzyć listę odtwarzania z różnych wierszy z różnych filmów wideo, możesz użyć 1-godzinnego wideo i z niego korzystać tylko 1 wiersz, na przykład 10:00-10:15. W takim przypadku będziesz mieć listę odtwarzania z 1 wierszem, gdzie zakres czasu to 10:00-10:15, ale adjustedTimeRange to 00:00-00:15. 

## <a name="widgets"></a>Widżety

Video Indexer obsługuje osadzanie widżetów w aplikacjach. Aby uzyskać więcej informacji, zobacz [osadzanie Video Indexer widżetów w aplikacjach](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Podsumowanie szczegółowych informacji  

Podsumowanie szczegółowych informacji zawiera Zagregowany widok danych: twarze, tematy, emocji. Na przykład zamiast przechodzenia przez poszczególne tysiące zakresów czasu i sprawdzania, które twarze znajdują się w nim, podsumowanie szczegółowych informacji zawiera wszystkie powierzchnie i dla każdej z nich, przedziały czasu, w których pojawiają się i% czasu wyświetlania.  

## <a name="next-steps"></a>Następne kroki

- [omówienie](video-indexer-overview.md)
- [Insights](video-indexer-output-json-v2.md)
