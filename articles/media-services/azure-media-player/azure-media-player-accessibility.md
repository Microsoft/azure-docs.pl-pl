---
title: Ułatwienia dostępu Azure Media Player
description: Dowiedz się więcej na temat ustawień ułatwień dostępu Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81726557"
---
# <a name="accessibility"></a>Ułatwienia dostępu #

Azure Media Player współpracuje z funkcjami czytnika ekranu, takimi jak narrator systemu Windows i Apple OSX/iOS VoiceOver. Znaczniki alternatywne są dostępne dla przycisków interfejsu użytkownika, a czytnik ekranu może odczytywać te alternatywne Tagi, gdy użytkownik nawiguje do nich. Dodatkowe konfiguracje można ustawić na poziomie systemu operacyjnego.

## <a name="captions-and-subtitles"></a>Podpisy i napisy ##

W tej chwili Azure Media Player obsługuje obecnie podpisy tylko dla zdarzeń na żądanie z formatem WebVTT i zdarzeniami na żywo przy użyciu CEA 708. Format TTML nie jest obecnie obsługiwany. Dzięki podpisom i napisom odtwarzacz może obsługiwać i angażować szersze grono odbiorców, w tym osoby z wadami słuchu lub osób chcących jednocześnie czytać w innym języku. Ponadto podpisy zwiększają zaangażowanie w film wideo, poprawiają zrozumienie i umożliwiają wyświetlanie wideo w środowiskach o dużych wymaganiach dotyczących hałasy, takich jak miejsce pracy.  

## <a name="high-contrast-mode"></a>Tryb dużego kontrastu ##

Domyślny interfejs użytkownika w Azure Media Player jest zgodny z większością trybów widoku dużego kontrastu urządzenia/przeglądarki. Konfiguracje można ustawić na poziomie systemu operacyjnego.

## <a name="mobility-options"></a>Opcje mobilności ##

### <a name="tabbing-focus"></a>Fokus tabulacji ###

Fokus tabulacji, udostępniany przez ogólne standardy HTML, jest dostępny w Azure Media Player. Aby włączyć skoncentrowanie się na kartach, należy dodać `tabindex=0` (lub inną wartość, jeśli rozumiesz, w jaki sposób kolejność tabulacji ma wpływ na kod HTML), `<video>` np `<video ... tabindex=0>...</video>` .:. Na niektórych platformach fokus dla kontrolek może być obecny tylko wtedy, gdy kontrolki są widoczne, a Platforma obsługuje te funkcje.

Po włączeniu fokusu na kartach użytkownik końcowy może efektywnie nawigować i kontrolować odtwarzacz wideo bez względu na ich mysz. Do każdego menu kontekstowego lub elementu z możliwością kontrolowania można przejść do przycisku tabulacji i wybrać polecenie Enter lub spacja. Naciśnięcie klawisza Enter lub Spacja w menu kontekstowym spowoduje jego rozwinięcie, aby użytkownik końcowy mógł kontynuować tabulację w celu wybrania elementu menu. Gdy masz już kontekst wybieranego elementu, naciśnij ponownie klawisz Enter lub spację, aby ukończyć wybieranie.

### <a name="hotkeys"></a>Klawisze dostępu ###

Azure Media Player obsługuje kontrolowanie za pomocą klawisza skrótu klawiaturowego. W przeglądarce internetowej jedynym sposobem na kontrolowanie bazowego elementu wideo jest skoncentrowanie się na odtwarzaczu. Gdy gracz nastąpi fokus, klawisz dostępu może sterować funkcją odtwarzacza.  W poniższej tabeli opisano różne klawisze dostępu i skojarzone z nimi zachowanie:

| Klawisz dostępu              | Zachowanie                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | W odtwarzaczu będzie włączany/wyłączany tryb pełnoekranowy                                  |
| M/m                  | Dźwięk odtwarzacza będzie wyciszany/włączany                                          |
| Strzałka w górę i w dół    | Głośność dźwięku odtwarzacza będzie zwiększana/zmniejszana                                    |
| Strzałka w lewo i w prawo | Postęp filmu wideo będzie zwiększany/zmniejszany                                  |
| 0,1,2,3,4,5,6,7,8,9  | Postęp filmu wideo zostanie zmieniony na 0% \- 90% w zależności od naciśniętego klawisza |
| Kliknij akcję         | Film wideo będzie odtwarzany/wstrzymywany                                                   |

## <a name="next-steps"></a>Następne kroki

<!---Some context for the following links goes here--->
- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)