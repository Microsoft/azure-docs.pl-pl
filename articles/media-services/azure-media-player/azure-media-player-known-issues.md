---
title: Problemy znane w programie Azure Media Player
description: Bieżąca wersja ma następujące znane problemy.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727218"
---
# <a name="known-issues"></a>Znane problemy #

Bieżąca wersja ma następujące znane problemy:

## <a name="azure-media-player"></a>Azure Media Player ##

- Nieprawidłowo skonfigurowane kodery mogą powodować problemy z odtwarzaniem
- Strumienie o sygnaturach czasowych większe niż 2^53 mogą mieć problemy z odtwarzaniem.
  - Łagodzenie: użyj 90-kHz wideo i 44,1 kHz skali czasu audio
- Brak automatycznego odtwarzania na urządzeniach mobilnych bez interakcji z użytkownikiem. Jest zablokowany przez platformę.
- Poszukiwanie w pobliżu nieciągłości może spowodować niepowodzenie odtwarzania.
- Pobieranie dużych prezentacji może spowodować zablokowanie interfejsu użytkownika.
- Nie można automatycznie odtworzyć tego samego źródła ponownie po zakończeniu prezentacji.
  - Aby odtworzyć źródło po jego zakończeniu, należy ponownie ustawić źródło.
- Puste manifesty mogą powodować problemy z odtwarzaczem.
  - Ten problem może wystąpić podczas uruchamiania strumienia na żywo i nie wystarczającej ilości fragmentów znajdują się w manifeście.
- Pozycja odtwarzania może poza paskiem wyszukiwania interfejsu użytkownika.
- Kolejność zdarzeń nie jest spójna we wszystkich technikach.
- Właściwości buforowane nie jest spójne w całej technologii.
- nativeControlsForTouch musi być false (domyślnie), aby uniknąć "Obiekt nie obsługuje właściwości lub metody 'setControls'"
- Plakaty muszą być teraz bezwzględnymi adresami URL
- W interfejsie użytkownika mogą wystąpić drobne problemy estetyczne podczas korzystania z trybu wysokiego kontrastu urządzenia
- Adresy URL zawierające "%" lub "+" w w pełni zdekodowanym ciągu mogą mieć problemy z ustawieniem źródła

## <a name="ad-insertion"></a>Wstawianie reklamy ##

- Reklamy mogą mieć problemy z wstawianiem (na żądanie lub na żywo), gdy w przeglądarce jest zainstalowany bloker reklam
- Na urządzeniach mobilnych mogą występnąć problemy z odtwarzaniem reklam.

## <a name="azurehtml5js"></a>Platforma AzureHtml5JS ##

- W oknie DVR zawartości live, po zakończeniu zawartości oś czasu będzie nadal rosnąć aż do poszukiwania do obszaru lub osiągnięcia końca prezentacji.
- Prezentacje na żywo w Firefoksie z włączoną funkcją MSE mają pewne problemy
- Zasoby, które są tylko audio lub wideo, nie będą odtwarzane za pośrednictwem technologii AzureHtml5JS.
  - Jeśli chcesz odtwarzać zasoby bez dźwięku lub wideo, możesz to zrobić, wstawiając pusty dźwięk lub wideo za pomocą [narzędzia Azure Media Services Explorer](https://aka.ms/amse)
    - Instrukcje dotyczące wstawiania cichego dźwięku można znaleźć [tutaj](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>Flash ##

- Zawartość AES nie jest odtwarzana w wersji Flash 30.0.0.134 z powodu błędu w logice buforowania firmy Adobe. Firma Adobe rozwiązała problem i wydała go w 30.0.0.154
- Awarie technologii i http (takie jak 404 limity czasu sieci), odtwarzacz będzie trwać dłużej niż inne technologie.
- Kliknij obszar wideo z technologią flashSS nie będzie odtwarzać / pauzować odtwarzacz.
- Jeśli użytkownik ma zainstalowaną lampę Flash, ale nie udziela uprawnień do załadowania go na lokację, może wystąpić nieskończone wirowanie. To dlatego, że gracz uważa, że wtyczka jest zainstalowana i dostępna i myśli, że wtyczka jest uruchomiona zawartość. Kod JavaScript został wysłany, ale ustawienia przeglądarki zablokowały wykonanie wtyczki, dopóki użytkownik nie zaakceptuje monitu o zezwolenie na wtyczkę. Może to nastąpić we wszystkich przeglądarkach.  

## <a name="silverlight"></a>Silverlight ##

- Brakujące funkcje
- Awarie technologii i http (takie jak 404 limity czasu sieci), odtwarzacz będzie trwać dłużej niż inne technologie.
- Odtwarzanie przeglądarki Safari i Firefox na komputerze `"http://` Mac `https://` za pomocą programu Silverlight wymaga jawnego zdefiniowania lub źródła.
- Jeśli brakuje interfejsu API dla tej technologii, zazwyczaj zwraca wartość null.
- Jeśli użytkownik ma zainstalowaną lampę Flash, ale nie udziela uprawnień do załadowania go na lokację, może wystąpić nieskończone wirowanie. To dlatego, że gracz uważa, że wtyczka jest zainstalowana i dostępna i myśli, że wtyczka jest uruchomiona zawartość. Kod JavaScript został wysłany, ale ustawienia przeglądarki zablokowały wykonanie wtyczki, dopóki użytkownik nie zaakceptuje monitu o zezwolenie na wtyczkę. Może to nastąpić we wszystkich przeglądarkach.  

## <a name="native-html5"></a>Natywny html5 ##

- Html5 tech wysyła tylko zdarzenie canplaythrough dla pierwszego źródła zestawu.
- Ta technologia obsługuje tylko to, co zaimplementowała przeglądarka.  Niektóre funkcje mogą brakować w tej technologii.  
- Jeśli brakuje interfejsu API dla tej technologii, zazwyczaj zwraca wartość null.
- Istnieją problemy z podpisami i napisami na tej technologii. Mogą one lub nie mogą być dostępne lub widoczne na tej technologii.
- Mając ograniczoną przepustowość w scenariuszu technologicznym HLS/Html5 powoduje odtwarzanie dźwięku bez wideo.

### <a name="microsoft"></a>Microsoft ###

- Odtwarzanie IE8 obecnie nie działa z powodu niezgodności z ecmascript 5
- W IE i niektórych wersjach edge'a pełnego ekranu nie można wprowadzić przez tabulator do przycisku i wybranie go lub użycie klawisza skrótu F/f.

## <a name="google"></a>Google ##

- Wiele profilów kodowania w tym samym manifeście ma pewne problemy z odtwarzaniem w Chrome i nie jest zalecane.
- Chrome nie może odtwarzać he-aac za pomocą usługi AzureHtml5JS. Postępuj zgodnie ze szczegółami na [tracker błędów](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- Od Chrome v58, widevine zawartość musi być załadowany / odtworzone za pośrednictwem protokołu https:// w przeciwnym razie odtwarzanie zakończy się niepowodzeniem.

## <a name="mozilla"></a>Mozilla ##

- Przełącznik strumienia audio wymaga strumieni audio mieć te same dane prywatne kodeka podczas korzystania z usługi AzureHtml5JS. Platforma Firefox tego wymaga.

## <a name="apple"></a>Apple ##

- Safari na Macu często domyślnie włącza tryb Oszczędzania Energii z ustawieniem "Zatrzymaj wtyczki, aby oszczędzać energię", które blokuje wtyczki takie jak Flash i Silverlight, gdy uważają, że nie jest to korzystne dla użytkownika. Ten blok nie blokuje istnienia wtyczki, tylko możliwości. Biorąc pod uwagę domyślną techOrder, może to spowodować problemy podczas próby
  - Łagodzenie 1: Jeśli odtwarzacz wideo jest "z przodu i na środku" (w granicach 3000 x 3000 pikseli, począwszy od lewego górnego rogu dokumentu), powinien nadal grać.
  - Łagodzenie 2: Zmień techOrder dla Safari być ["azureHtml5JS", "html5"]. To ograniczenie ma wpływ na nie uzyskanie wszystkich funkcji, które są dostępne w technologii FlashSS.
- Zawartość PlayReady za pośrednictwem programu Silverlight może mieć problemy z odtwarzaniem w przeglądarce Safari.
- AES i zawartość tokenu z ograniczeniami nie jest odtwarzana przy użyciu systemu iOS i starszych urządzeń z systemem Android.
  - Aby osiągnąć ten scenariusz, serwer proxy musi zostać dodany do usługi.
- Domyślna karnacja telefonu z systemem iOS jest wyświetlana.
- Odtwarzanie iPhone'a zawsze odbywa się na ekranie odtwarzacza macierzystego.
  - Funkcje, w tym podpisy, mogą nie utrwalić się w tym odtwarzaniu niewłaskowym.
- Po zakończeniu prezentacji na żywo urządzenia z systemem iOS nie zakończą poprawnie prezentacji.
- iOS nie pozwala na funkcje DVR.
- Przełącznik strumienia audio w iOS można wykonać tylko za jednym interfejsem użytkownika odtwarzacza iOS i wymaga strumieni audio, aby mieć te same prywatne dane kodeka
- Starsze wersje przeglądarki Safari mogą mieć problemy z odtwarzaniem transmisji na żywo.

## <a name="older-android"></a>Starszy Android ##

- AES i zawartość tokenu z ograniczeniami nie jest odtwarzana przy użyciu systemu iOS i starszych urządzeń z systemem Android.
  - Aby osiągnąć ten scenariusz, serwer proxy musi zostać dodany do usługi.

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)