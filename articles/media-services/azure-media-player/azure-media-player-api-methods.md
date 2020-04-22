---
title: Metody interfejsu API programu Azure Media Player
description: Interfejs API odtwarzacza multimediów azure umożliwia interakcję z wideo za pośrednictwem języka JavaScript, niezależnie od tego, czy przeglądarka odtwarza wideo za pośrednictwem wideo HTML5, Flash, Silverlight lub innych obsługiwanych technologii odtwarzania.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727270"
---
# <a name="api"></a>interfejs API #

Interfejs API odtwarzacza multimediów azure umożliwia interakcję z wideo za pośrednictwem języka JavaScript, niezależnie od tego, czy przeglądarka odtwarza wideo za pośrednictwem wideo HTML5, Flash, Silverlight lub innych obsługiwanych technologii odtwarzania.

## <a name="referencing-the-player"></a>Odwoływanie się do gracza ##

Aby korzystać z funkcji interfejsu API, należy uzyskać dostęp do obiektu odtwarzacza. Na szczęście jest to łatwe do zdobycia. Wystarczy upewnić się, że tag wideo ma identyfikator. Przykładowy kod osadzania ma `vid1`identyfikator . Jeśli masz wiele filmów na jednej stronie, upewnij się, że każdy tag wideo ma unikatowy identyfikator.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Jeśli odtwarzacz nie został jeszcze zainicjowany za pomocą atrybutu konfiguracji danych lub innej metody, spowoduje to również zainicjowanie odtwarzacza.

## <a name="wait-until-the-player-is-ready"></a>Poczekaj, aż odtwarzacz będzie gotowy ##

Czas potrzebny programowi Azure Media Player do skonfigurowania wideo i interfejsu API będzie się różnić w zależności od używanej technologii odtwarzania. HTML5 będzie często znacznie szybszy do załadowania niż Flash lub Silverlight. Z tego powodu funkcja "gotowego" gracza powinna być używana do wyzwalania dowolnego kodu, który wymaga interfejsu API gracza.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

LUB

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Metody API ##

Teraz, gdy masz dostęp do gotowego odtwarzacza, możesz kontrolować film, otrzymywać wartości lub reagować na zdarzenia wideo. Nazwy funkcji interfejsu API programu Azure Media Player próbują podążać za [interfejsem API nośników HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). Główną różnicą jest to, że funkcje getter/setter są używane dla właściwości wideo.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Rejestracja wydarzeń ##
Zdarzenia powinny być rejestrowane bezpośrednio po zainicjowaniu odtwarzacza po raz pierwszy, aby upewnić się, że wszystkie zdarzenia są odpowiednio zgłaszane do aplikacji i powinny być wykonywane poza gotowym zdarzeniem.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Następne kroki ##

<!---Some context for the following links goes here--->
- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)