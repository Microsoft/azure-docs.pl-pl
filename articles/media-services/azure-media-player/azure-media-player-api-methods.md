---
title: Metody interfejsu API Azure Media Player
description: Interfejs API Azure Media Player umożliwia korzystanie z filmu wideo za pomocą języka JavaScript, niezależnie od tego, czy przeglądarka odtwarza wideo za pomocą wideo HTML5, Flash, Silverlight czy innych obsługiwanych technologii odtwarzania.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81727270"
---
# <a name="api"></a>Interfejs API #

Interfejs API Azure Media Player umożliwia korzystanie z filmu wideo za pomocą języka JavaScript, niezależnie od tego, czy przeglądarka odtwarza wideo za pomocą wideo HTML5, Flash, Silverlight czy innych obsługiwanych technologii odtwarzania.

## <a name="referencing-the-player"></a>Odwoływanie się do odtwarzacza ##

Aby korzystać z funkcji interfejsu API, wymagany jest dostęp do obiektu odtwarzacza. Na szczęście. Wystarczy upewnić się, że tag wideo ma identyfikator. Przykładowy kod osadzania ma identyfikator `vid1` . Jeśli masz wiele filmów wideo na jednej stronie, upewnij się, że każdy tag wideo ma unikatowy identyfikator.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Jeśli gracz nie został jeszcze zainicjowany za pośrednictwem atrybutu konfiguracji danych lub innej metody, spowoduje to również zainicjowanie odtwarzacza.

## <a name="wait-until-the-player-is-ready"></a>Poczekaj, aż odtwarzacz będzie gotowy ##

Czas potrzebny na Azure Media Player konfigurowania wideo i interfejsu API różni się w zależności od używanej technologii odtwarzania. Usługa HTML5 będzie często znacznie szybsza niż w przypadku wersji Flash lub Silverlight. Z tego powodu funkcja "gotowe" odtwarzacza powinna być używana do wyzwalania dowolnego kodu, który wymaga interfejsu API odtwarzacza.

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

## <a name="api-methods"></a>Metody interfejsu API ##

Teraz, gdy masz dostęp do gotowego odtwarzacza, możesz kontrolować wideo, uzyskiwać wartości lub reagować na zdarzenia wideo. Nazwy funkcji interfejsu API Azure Media Player próbują postępować zgodnie z [interfejsem API multimediów HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). Główną różnicą jest to, że funkcje pobierającej i ustawiającej są używane do właściwości wideo.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Rejestrowanie zdarzeń ##
Zdarzenia powinny być rejestrowane bezpośrednio po zainicjowaniu odtwarzacza po raz pierwszy, aby upewnić się, że wszystkie zdarzenia zostały odpowiednio zgłoszone do aplikacji i powinny być wykonywane poza zdarzeniem gotowości.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Następne kroki ##

<!---Some context for the following links goes here--->
- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)