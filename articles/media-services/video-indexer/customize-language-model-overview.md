---
title: Dostosowywanie modelu języka w Video Indexer — Azure
titleSuffix: Azure Media Services
description: Ten artykuł zawiera omówienie modelu języka w Video Indexer i sposobu jego dostosowywania.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 29490e08748a37f7eb93fbb8804f55f74c53df35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047136"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Dostosowywanie modelu języka za pomocą Video Indexer

Video Indexer obsługuje automatyczne rozpoznawanie mowy poprzez integrację z usługą Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Możesz dostosować model języka, przekazując tekst do adaptacji, czyli tekst z domeny, której słownictwo chcesz dostosować do aparatu. Po przeprowadzeniu szkolenia modelu nowe wyrazy pojawiające się w tekście adaptacyjnym zostaną rozpoznane, przy założeniu, że domyślna wymowa i model języka będzie uczyć się nowych prawdopodobnych sekwencji wyrazów. Niestandardowe modele języków są obsługiwane w języku angielskim, hiszpańskim, francuskim, niemieckim, włoskim, chińskim (uproszczonym), japońskim, rosyjskim, portugalskim, hindi i koreańskim. 

Utwórzmy słowo o wysokiej dostępności, takie jak "Kubernetes" (w kontekście usługi Azure Kubernetes Service), na przykład. Ponieważ wyraz jest nowy do Video Indexer, jest rozpoznawany jako "społeczności". Musisz nauczyć model, aby rozpoznawał go jako "Kubernetes". W innych przypadkach słowa istnieją, ale model języka nie oczekuje, że są one wyświetlane w określonym kontekście. Na przykład "Usługa kontenera" nie jest sekwencją 2-słowną, którą Niewyspecjalizowany model języka może rozpoznać jako określony zestaw słów.

Istnieje możliwość przekazania wyrazów bez kontekstu na liście w pliku tekstowym. Jest to traktowane jako częściowe adaptacje. Alternatywnie można przekazać pliki tekstowe dokumentacji lub zdania dotyczące zawartości w celu lepszego dostosowania.

Za pomocą interfejsów API Video Indexer lub witryny sieci Web można tworzyć i edytować niestandardowe modele języka, jak opisano w tematach w sekcji [następne kroki](#next-steps) w tym temacie.

## <a name="best-practices-for-custom-language-models"></a>Najlepsze rozwiązania dotyczące niestandardowych modeli języków

Video Indexer uczy się na podstawie prawdopodobieństwa kombinacji wyrazów, aby poznać najlepsze:

* Zapewnij wystarczającą liczbę przykładowych zdań w miarę ich wymawiania.
* Umieść tylko jedno zdanie na wiersz, nie więcej. W przeciwnym razie system będzie uczyć się prawdopodobieństwa w zdaniach.
* Nie można umieścić jednego wyrazu jako zdania, aby zwiększyć słowo względem innych, ale system uczy się najlepiej z pełnych zdań.
* Wprowadzając nowe wyrazy lub akronimy, jeśli to możliwe, podaj wiele przykładów użycia w pełnych zdaniach, aby zapewnić możliwie największą ilość kontekstu dla systemu.
* Spróbuj wprowadzić kilka opcji adaptacji i zobaczyć, jak działają.
* Unikaj powtarzania dokładnie tego samego zdania wielokrotnie. Może to spowodować odliczenie od reszty danych wejściowych.
* Należy unikać umieszczania nietypowych symboli (~, # @% &), ponieważ zostaną odrzucone. Zdania, w których pojawiają się, zostaną również odrzucone.
* Unikaj umieszczania zbyt dużych danych wejściowych, takich jak setki tysięcy zdań, ponieważ dzięki temu zostanie rozcieńczony efekt podwyższania poziomu.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu języka przy użyciu interfejsów API](customize-language-model-with-api.md)

[Dostosowywanie modelu języka za pomocą witryny sieci Web](customize-language-model-with-website.md)
