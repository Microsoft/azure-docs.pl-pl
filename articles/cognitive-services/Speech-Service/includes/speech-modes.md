---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422220"
---
## <a name="speech-modes"></a>Tryby mowy

**Interaktywne**
- Przeznaczone dla scenariuszy poleceń i kontroli.
- Ma wartość limitu czasu segmentacji równą X.
- Po zakończeniu jednego rozpoznanego wypowiedź usługa zatrzymuje przetwarzanie dźwięku z tego identyfikatora żądania i kończy działanie. Połączenie nie zostało zamknięte.
- Maksymalny limit rozpoznawania to 20s.
- Typowym wywołaniem węgla do wywołania jest `RecognizeOnceAsync` .

**Konwersacja**
- Przeznaczone do długotrwałego rozpoznawania.
- Ma wartość limitu czasu segmentacji Y. (Y! = X)
- Przetworzy wiele kompletnych wyrażenia długościów bez kończenia obrotu.
- Nastąpi wyłączenie zbyt dużej ilości ciszy.
- Węgiel będzie kontynuował pracę z nowym IDENTYFIKATORem żądania i odtwarzaniem dźwięku zgodnie z wymaganiami.
- Usługa wymusi rozłączyć się po 10 minutach rozpoznawania mowy.
- Węgiel ponownie nawiąże połączenie i powtórzy niepotwierdzony dźwięk.
- Wywołane w Węgiele z `StartContinuousRecognition` .

**Dyktowanie**
- Pozwala użytkownikom na określenie interpunkcji, mówiąc ją.
- Wywoływana w węgiel przez określenie `EnableDictation` na `SpeechConfig` obiekcie niezależnie od wywołania interfejsu API, które uruchamia rozpoznawanie.
- Klaster z<sup>1</sup> podmiotu testowego zwraca `speech.fragment` komunikaty dla wyników pośrednich, a 3 wiadomości zwracanych przez tę firmę<sup>rd</sup> `speech.hypothesis` .