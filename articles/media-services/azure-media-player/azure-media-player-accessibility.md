---
title: Ułatwienia dostępu w programie Azure Media Player
description: Dowiedz się więcej o ustawieniach ułatwień dostępu programu Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726557"
---
# <a name="accessibility"></a>Ułatwienia dostępu #

Usługa Azure Media Player współpracuje z funkcjami czytnika ekranu, takimi jak Narrator systemu Windows i apple OSX/iOS VoiceOver. Alternatywne tagi są dostępne dla przycisków interfejsu użytkownika, a czytnik ekranu jest w stanie odczytać te alternatywne tagi, gdy użytkownik przejdzie do nich. Dodatkowe konfiguracje można ustawić na poziomie systemu operacyjnego.

## <a name="captions-and-subtitles"></a>Napisy i napisy ##

W tej chwili program Azure Media Player obsługuje obecnie podpisy tylko dla zdarzeń na żądanie w formacie WebVTT i zdarzeniach na żywo przy użyciu cea 708. Format TTML jest obecnie nieobsługiwał. Dzięki podpisom i napisom odtwarzacz może obsługiwać i angażować szersze grono odbiorców, w tym osoby z wadami słuchu lub osób chcących jednocześnie czytać w innym języku. Ponadto podpisy zwiększają zaangażowanie w film wideo, poprawiają zrozumienie i umożliwiają wyświetlanie wideo w środowiskach o dużych wymaganiach dotyczących hałasy, takich jak miejsce pracy.  

## <a name="high-contrast-mode"></a>Tryb wysokiego kontrastu ##

Domyślny interfejs użytkownika w usłudze Azure Media Player jest zgodny z większością trybów widoku o wysokim kontraście/przeglądarki. Konfiguracje można ustawić na poziomie systemu operacyjnego.

## <a name="mobility-options"></a>Opcje mobilności ##

### <a name="tabbing-focus"></a>Tabbing ostrości ###

Tabulator fokus, dostarczany przez ogólne standardy HTML, jest dostępny w usłudze Azure Media Player. Aby włączyć ustawianie ostrości `tabindex=0` na karcie, należy dodać (lub inną wartość, jeśli rozumiesz, jak kolejność kart jest wpłynęła na HTML) do html `<video>` tak: `<video ... tabindex=0>...</video>`. Na niektórych platformach fokus dla formantów może być obecny tylko wtedy, gdy formanty są widoczne i jeśli platforma obsługuje te możliwości.

Po włączeniu tabulatora ostrość, użytkownik końcowy może skutecznie poruszać się i kontrolować odtwarzacz wideo bez zależności od myszy. Do każdego menu kontekstowego lub elementu sterującego można przejść, naciskając przycisk tabulacji i wybrać go za pomocą klawisza Enter lub spacji. Naciśnięcie klawisza Enter lub spacji w menu kontekstowym spowoduje rozwinięcie go, aby użytkownik końcowy mógł kontynuować tabulację, aby wybrać element menu. Gdy masz już kontekst wybieranego elementu, naciśnij ponownie klawisz Enter lub spację, aby ukończyć wybieranie.

### <a name="hotkeys"></a>Skróty klawiszowe ###

Program Azure Media Player obsługuje sterowanie za pomocą klawisza skrótu klawiatury. W przeglądarce internetowej jedynym sposobem kontrolowania podstawowego elementu wideo jest skupienie się na odtwarzaczu. Gdy na odtwarzaczu skupisz się, klawisz skrótu może kontrolować funkcjonalność odtwarzacza.  W poniższej tabeli opisano różne klawisze dostępu i skojarzone z nimi zachowanie:

| Klawisz dostępu              | Zachowanie                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | W odtwarzaczu będzie włączany/wyłączany tryb pełnoekranowy                                  |
| M/m                  | Dźwięk odtwarzacza będzie wyciszany/włączany                                          |
| Strzałka w górę i w dół    | Głośność dźwięku odtwarzacza będzie zwiększana/zmniejszana                                    |
| Strzałka w lewo i w prawo | Postęp filmu wideo będzie zwiększany/zmniejszany                                  |
| 0,1,2,3,4,5,6,7,8,9  | Postęp wideo zostanie zmieniony na\- 0% 90% w zależności od wciśniętego klawisza |
| Kliknij pozycję Akcja         | Film wideo będzie odtwarzany/wstrzymywany                                                   |

## <a name="next-steps"></a>Następne kroki

<!---Some context for the following links goes here--->
- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)