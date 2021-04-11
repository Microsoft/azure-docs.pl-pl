---
title: Azure Media Player znane problemy
description: W bieżącej wersji znajdują się następujące znane problemy.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 9d2289f1cec0d3679b0e9400f75c75cf9fece60b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448829"
---
# <a name="known-issues"></a>Znane problemy #

W bieżącej wersji znajdują się następujące znane problemy:

## <a name="azure-media-player"></a>Azure Media Player ##

- Nieprawidłowo skonfigurowane kodery mogą powodować problemy z odtwarzaniem
- Strumienie z sygnaturami czasowymi większymi niż 2 ^ 53 mogą mieć problemy z odtwarzaniem.
  - Środki zaradcze: korzystanie z filmów wideo 90-kHz i 44,1-kHz
- Brak autoodtwarzania na urządzeniach przenośnych bez interakcji z użytkownikiem. Jest ona blokowana przez platformę.
- Wyszukiwanie w bliskich przerwań może spowodować niepowodzenie odtwarzania.
- Pobieranie dużych prezentacji może spowodować blokowanie interfejsu użytkownika.
- Nie można automatycznie odtworzyć tego samego źródła po zakończeniu prezentacji.
  - Aby odtworzyć Źródło po jego zakończeniu, należy ponownie ustawić źródło.
- Puste manifesty mogą powodować problemy z odtwarzaczem.
  - Ten problem może wystąpić podczas uruchamiania strumienia na żywo, a w manifeście nie znaleziono wystarczającej liczby fragmentów.
- Pozycja odtwarzania może poza interfejsem użytkownika SeekBar.
- Kolejność zdarzeń nie jest spójna ze wszystkimi technicznymi.
- Właściwość buforowana nie jest spójna w ramach Tech.
- nativeControlsForTouch musi mieć wartość false (domyślnie), aby uniknąć "obiekt nie obsługuje właściwości ani metody" setcontrols ""
- Afisze muszą teraz być bezwzględnymi adresami URL
- Podczas korzystania z trybu dużego kontrastu urządzenia mogą wystąpić drobne problemy estetyczne w interfejsie użytkownika
- Adresy URL zawierające "%" lub "+" w w pełni zdekodowanym ciągu mogą mieć problemy z ustawieniem źródła

## <a name="ad-insertion"></a>Wstawianie AD ##

- W przypadku usługi AD-Block w przeglądarce mogą być wstawiane problemy (na żądanie lub na żywo).
- Urządzenia przenośne mogą mieć problemy z odtwarzaniem reklam.
- Usługa MP4 midroll ads nie jest obecnie obsługiwana przez Azure Media Player.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- W oknie DVR zawartości na żywo, gdy zawartość zostanie zakończona, nadal rośnie, dopóki nie przejdziesz do obszaru lub osiągniesz koniec prezentacji.
- Prezentacje na żywo w programie Firefox z włączoną funkcją MSE zawierają pewne problemy

- Zasoby, które są tylko audio, nie będą odtwarzane za pośrednictwem AzureHtml5JS Tech.
  - Jeśli chcesz odtworzyć zasoby bez audio, możesz to zrobić, wstawiając puste audio przy użyciu [narzędzia Azure Media Services Explorer](https://aka.ms/amse)
  - Instrukcje dotyczące sposobu wstawiania dyskretnego dźwięku można znaleźć [tutaj](../previous/media-services-advanced-encoding-with-mes.md#silent_audio)

## <a name="flash"></a>Flash ##

- Zawartość AES nie jest odtwarzana w programie Flash w wersji 30.0.0.134 ze względu na usterkę w logice buforowania Adobe. Firma Adobe rozwiązała problem i wydała ją w 30.0.0.154
- Błędy techniczne i http (takie jak 404 limitów czasu sieci), gracz zajmie dłużej odzyskiwanie niż inne techniczne.
- Kliknięcie obszaru wideo z funkcją Flash Tech nie spowoduje odtworzenia/wstrzymania odtwarzacza.
- Jeśli użytkownik ma zainstalowany program Flash, ale nie udzieli uprawnienia do załadowania go w lokacji, może wystąpić nieskończona wirowanie. Wynika to z faktu, że gracz uzna, że wtyczka jest zainstalowana i dostępna, a wtyczka jest uruchomiona. Kod JavaScript został wysłany, ale ustawienia przeglądarki blokują wykonywanie wtyczki, dopóki użytkownik nie zaakceptuje monitu o zezwolenie na wtyczkę. Taka sytuacja może wystąpić we wszystkich przeglądarkach.  

## <a name="silverlight"></a>Silverlight ##

- Brakujące funkcje
- Błędy techniczne i http (takie jak 404 limitów czasu sieci), gracz zajmie dłużej odzyskiwanie niż inne techniczne.
- Przeglądarki Safari i Firefox na potrzeby odtwarzania komputerów Mac z technologią Silverlight wymagają jawnie zdefiniowania `"http://` lub `https://` źródła.
- Jeśli brakuje interfejsu API dla tej technologii technicznej, zazwyczaj zwróci wartość null.
- Jeśli użytkownik ma zainstalowany program Flash, ale nie udzieli uprawnienia do załadowania go w lokacji, może wystąpić nieskończona wirowanie. Wynika to z faktu, że gracz uzna, że wtyczka jest zainstalowana i dostępna, a wtyczka jest uruchomiona. Kod JavaScript został wysłany, ale ustawienia przeglądarki blokują wykonywanie wtyczki, dopóki użytkownik nie zaakceptuje monitu o zezwolenie na wtyczkę. Taka sytuacja może wystąpić we wszystkich przeglądarkach.  

## <a name="native-html5"></a>Natywny HTML5 ##

- W języku HTML5 Tech jest wysyłana tylko zdarzenie canplaythrough dla pierwszego źródła zestawu.
- Ta technika Tech obsługuje tylko te elementy, które zostały zaimplementowane przez przeglądarkę.  Niektóre funkcje mogą być w tym przypadku niedostępne.  
- Jeśli brakuje interfejsu API dla tej technologii technicznej, zazwyczaj zwróci wartość null.
- W tej konferencji są problemy z napisami i napisami. Mogą one być niedostępne lub dostępne w tym trybie technicznym.
- Posiadanie ograniczonej przepustowości w scenariuszu technicznym HLS/HTML5 powoduje odtwarzanie dźwięku bez wideo.

### <a name="microsoft"></a>Microsoft ###

- Odtwarzanie programu IE8 nie działa obecnie z powodu niezgodności z językiem ECMAScript 5
- W programie IE i niektórych wersjach programu Edge pełny ekran nie może zostać wprowadzony przez tabulację na przycisk i zaznaczenie lub użycie klawisza skrótu F/f.

## <a name="google"></a>Google ##

- W przypadku wielu profilów kodowania w tym samym manifeście występują problemy z odtwarzaniem w przeglądarce Chrome i nie jest to zalecane.
- Program Chrome nie może odtworzyć elementu AAC z AzureHtml5JS. Postępuj zgodnie ze szczegółowymi informacjami na temat [śledzenia usterek](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- W przypadku programu Chrome v58 zawartość Widevine musi zostać załadowana/odtworzona przy użyciu protokołu https://, w przeciwnym razie odtwarzanie nie powiedzie się.

## <a name="mozilla"></a>Mozilla ##

- Przełącznik strumienia audio wymaga, aby strumienie audio miały te same dane prywatne dekodera podczas korzystania z AzureHtml5JS. Platforma Firefox wymaga tego.

## <a name="apple"></a>Apple ##

- Przeglądarka Safari na komputerze Mac często domyślnie włącza tryb oszczędzania zasilania z ustawieniem "Zatrzymaj wtyczki, aby zaoszczędzić moc", która blokuje wtyczki, takie jak Flash i Silverlight, gdy sądzi, że nie jest to użytkownik. Ten blok nie blokuje istniejącej wtyczki, tylko możliwości. Z uwzględnieniem domyślnej techOrder, może to spowodować problemy podczas próby odtworzenia
  - Środki zaradcze 1: Jeśli odtwarzacz wideo to "przód i środek" (w granicy 3000 x 3000 pikseli, zaczynając od lewego górnego rogu dokumentu), powinien być nadal odtwarzany.
  - Środki zaradcze 2: zmiana techOrder w przeglądarce Safari na wartość ["azureHtml5JS", "HTML5"]. To ograniczenie jest skutkiem nieuzyskania wszystkich funkcji, które są dostępne w błysku technicznym.
- Zawartość oprogramowania PlayReady za pośrednictwem programu Silverlight może mieć problemy z odtwarzaniem w przeglądarce Safari.
- Zawartość AES i token z ograniczeniami nie są odtwarzane przy użyciu systemu iOS i starszych urządzeń z systemem Android.
  - Aby można było zrealizować ten scenariusz, należy dodać serwer proxy do usługi.
- Domyślna skórka dla telefonu z systemem iOS jest wyświetlana przez.
- Odtwarzanie telefonu iPhone zawsze odbywa się na pełnym ekranie.
  - Funkcje, w tym podpisy, mogą nie być zachowywane w tym rozłączeniu innym niż inline.
- Po zakończeniu prezentacji na żywo urządzenia z systemem iOS nie będą prawidłowo kończyć prezentacji.
- System iOS nie zezwala na korzystanie z funkcji DVR.
- Przełącznik strumienia audio systemu iOS można wykonać tylko wtedy, gdy interfejs użytkownika natywnego odtwarzacza systemu iOS i wymaga, aby strumienie audio miały te same dane prywatne dekodera
- Starsze wersje programu Safari mogą potencjalnie mieć problemy z odtwarzaniem strumieni na żywo.

## <a name="older-android"></a>Starsza wersja systemu Android ##

- Zawartość AES i token z ograniczeniami nie są odtwarzane przy użyciu systemu iOS i starszych urządzeń z systemem Android.
  - Aby można było zrealizować ten scenariusz, należy dodać serwer proxy do usługi.

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
