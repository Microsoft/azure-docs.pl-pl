---
title: Video Indexer, zrzuty ekranu i ramki kluczowe
titleSuffix: Azure Media Services
description: Ten temat zawiera omówienie kluczowych Video Indexer, ujęć i ramek kluczowych.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 5a738152296aacbb5914e859a65976bd0f6dbf0a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532417"
---
# <a name="scenes-shots-and-keyframes"></a>Sceny, ujęcia i klatki kluczowe

Video Indexer segmentacji wideo na jednostki czasowe na podstawie właściwości strukturalnych i semantycznych. Ta funkcja umożliwia klientom łatwe przeglądanie i edytowanie zawartości wideo oraz zarządzanie nimi na podstawie różnych poziomów szczegółowości. Na przykład na podstawie scen, ujęć i ramek kluczowych opisanych w tym temacie.   

![Sceny, ujęcia i klatki kluczowe](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Wykrywanie sceny  
 
Video Indexer określa, kiedy scena zmienia się w wideo, na podstawie wizualnych wskaźników. Scena przedstawia pojedyncze zdarzenie i składa się z serii kolejnych ujęć, które są powiązane semantycznie. Miniatura sceny jest pierwszą ramką kluczową jej bazowego zrzutu ekranu. Indeksator wideo segmentuje wideo w sceny w oparciu o spójność kolorów w kolejnych zrzutach i pobiera czas rozpoczęcia i zakończenia każdej sceny. Wykrywanie scen jest uznawane za trudne zadanie, ponieważ obejmuje kwantyfikowanie semantycznych aspektów wideo.

> [!NOTE]
> Dotyczy filmów wideo zawierających co najmniej 3 sceny.

## <a name="shot-detection"></a>Wykrywanie zrzutów

Video Indexer określa, kiedy zmieni się film wideo na podstawie wizualnych wskaźników, śledząc nagłe i stopniowe przejścia w schemacie kolorów sąsiednich ramek. Metadane zrzutu obejmują czas rozpoczęcia i zakończenia, a także listę ramek kluczowych uwzględnionych w tym zdjęcie. Zrzuty są kolejnymi ramkami wyniesiowymi z tego samego aparatu w tym samym czasie.

## <a name="keyframe-detection"></a>Wykrywanie ramek kluczowych

Video Indexer wybiera ramki, które najlepiej reprezentują każde zdjęcie. Ramki kluczowe to reprezentatywne ramki wybrane z całego wideo w oparciu o właściwości wyglądu (na przykład kontrast i stabilność). Video Indexer pobiera listę identyfikatorów ramek kluczowych jako część metadanych zrzutu, na podstawie których klienci mogą wyodrębnić ramce kluczowej jako obraz o wysokiej rozdzielczości.  

### <a name="extracting-keyframes"></a>Wyodrębnianie ramek kluczowych

Aby wyodrębnić ramki kluczowe o wysokiej rozdzielczości dla wideo, należy najpierw przekazać i indeksować wideo.

![Klatek kluczowych](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Z witryną Video Indexer internetową

Aby wyodrębnić ramki kluczowe przy użyciu witryny Video Indexer internetowej, przekaż i zindeksuj plik wideo. Po zakończeniu zadania indeksowania kliknij przycisk **Pobierz** i wybierz pozycję **Artefakty (ZIP).** Spowoduje to pobranie folderu artifacts na komputer. 

![Zrzut ekranu przedstawiający menu rozwijane "Pobierz" z wybranymi elementami "Artefakty".](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Rozpakować i otworzyć folder. W *_KeyframeThumbnail* folderze znajdują się wszystkie ramki kluczowe wyodrębnione z wideo. 

#### <a name="with-the-video-indexer-api"></a>Za pomocą interfejsu API Video Indexer API

Aby uzyskać ramki kluczowe przy użyciu interfejsu API Video Indexer, przekaż i zindeksuj wideo przy użyciu wywołania [Przekaż](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) wideo. Po zakończeniu zadania indeksowania wywołaj wywołanie [get video index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index). Pozwoli to uzyskać wszystkie szczegółowe informacje, które Video Indexer z zawartości w pliku JSON.  

W ramach metadanych każdego zrzutu zostanie wyświetlona lista identyfikatorów ramek kluczowych. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Teraz należy uruchomić każdy z tych identyfikatorów ramek kluczowych w [wywołaniu Get Thumbnails.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) Spowoduje to pobranie wszystkich obrazów ramek kluczowych na komputer. 

## <a name="editorial-shot-type-detection"></a>Wykrywanie typu zrzutu redakcyjnego

Ramki kluczowe są skojarzone ze zrzutami w wyjściowym danych JSON. 

Typ zrzutu skojarzony z indywidualnym zrzutem w pliku JSON szczegółowych informacji reprezentuje jego typ redakcyjny. Te cechy typu zrzutu mogą być przydatne podczas edytowania filmów wideo w klipach, na przyczepach lub podczas wyszukiwania określonego stylu ramki kluczowej w celach chłonnych. Różne typy są określane na podstawie analizy pierwszej ramki kluczowej każdego zrzutu. Zrzuty są identyfikowane przez skalę, rozmiar i lokalizację twarzy wyświetlanych w pierwszej ramce kluczowej. 

Rozmiar i skala zdjęcia są określane na podstawie odległości między kamerą a twarzami wyświetlanymi w ramce. Używając tych właściwości, Video Indexer wykrywa następujące typy zrzutów:

* Szerokie: pokazuje całe treści osoby.
* Średni: pokazuje górną część ciała i twarz osoby.
* Z bliska: głównie pokazuje twarz osoby.
* Skrajne zbliżenie: pokazuje twarz osoby wypełniającej ekran. 

Typy ujęć można również określić na podstawie lokalizacji znaków podmiotu względem środka ramki. Ta właściwość definiuje następujące typy zrzutów w Video Indexer:

* Lewa twarz: osoba pojawia się po lewej stronie ramki.
* Środkowa twarz: osoba pojawia się w centralnym regionie ramki.
* Prawa twarz: osoba pojawia się po prawej stronie ramki.
* Na zewnątrz: osoba pojawia się na zewnątrz.
* Wewnątrz: osoba pojawia się w pomieszczeniu.

Dodatkowe cechy:

* Dwa zdjęcia: przedstawia twarze dwóch osób o średnim rozmiarze.
* Wiele twarzy: więcej niż dwie osoby.


## <a name="next-steps"></a>Następne kroki

[Badanie Video Indexer danych wyjściowych wytwarzanych przez interfejs API](video-indexer-output-json-v2.md#scenes)
