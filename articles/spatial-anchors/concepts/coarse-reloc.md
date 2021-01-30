---
title: Duże przelokalizowanie
description: Dowiedz się więcej o korzystaniu ze zbyt dużej lokalizacji, aby znaleźć kotwice w sąsiedztwie.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071153"
---
# <a name="coarse-relocalization"></a>Zgrubna ponowna lokalizacja

Ogromne relokalizacja jest funkcją, która umożliwia szybkie lokalizowanie w dużej skali, zapewniając przybliżoną i szybką odpowiedź na pytanie: *gdzie jest teraz moje urządzenie/jakie treści należy zaobserwować?* Odpowiedź nie jest precyzyjna, ale zamiast tego jest w postaci: znajdują się w *pobliżu tych kotwic; Spróbuj zlokalizować jeden z nich*.

Niestandardowa relokalizacja działa przez tagowanie kotwic z różnymi odczytami czujników na urządzeniu, które są później używane do szybkiego wykonywania zapytań. W przypadku scenariuszy z zewnątrz dane czujnika są zwykle pozycjami GPS (GPS) urządzenia. Gdy GPS jest niedostępny lub niezawodny (na przykład w przypadku braku drzwi), dane czujnika składają się z punktów dostępu Wi-Fi i sygnałów nawigacyjnych Bluetooth w zakresie. Zebrane dane czujnika przyczyniają się do utrzymania indeksu przestrzennego używanego przez kotwice przestrzenne platformy Azure, aby szybko określić, które kotwice znajdują się w pobliżu urządzenia.

## <a name="when-to-use-coarse-relocalization"></a>Kiedy używać grubej relokalizacji

Jeśli planujesz obsługę więcej niż 35 kotwic przestrzennych w miejscu większym niż sąd odtenisa, prawdopodobnie skorzystasz z niedużych indeksowań przestrzennych relokalizacji.

Szybkie wyszukiwanie kotwic z obsługą ogromnej lokalizacji jest zaprojektowane w celu uproszczenia opracowywania aplikacji, których kopie zapasowe są obsługiwane przez światowe kolekcje ("czyli miliony rozproszonych geograficznie) kotwic. Złożoność indeksowania przestrzennego jest ukryta, co pozwala skupić się na logice aplikacji. Wszystkie duże zakotwiczenie są wykonywane w tle przez kotwice przestrzenne platformy Azure.

## <a name="using-coarse-relocalization"></a>Używanie bardzo dużej lokalizacji

Typowym przepływem pracy do tworzenia i wykonywania zapytań o kotwice przestrzenne platformy Azure przy użyciu bardzo dużej lokalizacji jest:
1.  Utwórz i Skonfiguruj dostawcę odcisku palca czujnika, aby zbierać wybrane przez siebie dane czujników.
2.  Rozpocznij sesję zakotwiczenia platformy Azure i Utwórz kotwice. Ponieważ włączono odciski palców czujnika, kotwice są indeksowane w sposób przestrzenny przez niegrubą relokalizację.
3.  Zapytanie otaczające kotwice korzystające ze bardzo dużej lokalizacji przy użyciu dedykowanych kryteriów wyszukiwania w sesji kotwicy przestrzennej platformy Azure.

W celu skonfigurowania bardzo nieprawidłowej lokalizacji w aplikacji można zapoznać się z odpowiednim podanym samouczkiem:
* [Duże przelokalizowanie w środowisku Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Niestandardowa relokalizacja w zamierzeniu C](../how-tos/set-up-coarse-reloc-objc.md)
* [Gruba relokalizacja w SWIFT](../how-tos/set-up-coarse-reloc-swift.md)
* [Duże przelokalizowanie w języku Java](../how-tos/set-up-coarse-reloc-java.md)
* [Duże relokalizacja w języku C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Duże relokalizacja w języku C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Czujniki i platformy

### <a name="platform-availability"></a>Dostępność platformy

Typy danych czujników, które można wysłać do usługi zakotwiczenia:

* Pozycja GPS: Szerokość geograficzna, Długość geograficzna, Wysokość.
* Siła sygnału punktów dostępu Wi-Fi w zakresie.
* Siła sygnału sygnałów nawigacyjnych Bluetooth w zakresie.

Poniższa tabela zawiera podsumowanie dostępności danych czujników na obsługiwanych platformach wraz z wszelkimi zastrzeżeniami specyficznymi dla platformy:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | NIE<sup>1</sup>  | TAK<sup>2</sup> | TAK<sup>3</sup> |
| **Karta**        | TAK<sup>4</sup> | TAK<sup>5</sup> | NO  |
| **Sygnały nawigacyjne dotyczące beli** | TAK<sup>6</sup> | TAK<sup>6</sup> | TAK<sup>6</sup>|


<sup>1</sup> zewnętrzne urządzenie GPS może być skojarzone z urządzeniem HoloLens. Skontaktuj się z [naszą pomocą techniczną](../spatial-anchor-support.md) , jeśli wolisz korzystać z urządzenia HoloLens z systemem śledzącym GPS.<br/>
<sup>2</sup> obsługiwane za poorednictwem interfejsów API [lokalizacji][3] (GPS i sieć)<br/>
<sup>3</sup> obsługiwane przez interfejsy API [CLLocationManager][4]<br/>
<sup>4</sup> obsługiwane z szybkością około jednego skanowania co 3 sekundy <br/>
<sup>5</sup> począwszy od poziomu interfejsu API 28, skanowania Wi-Fi są ograniczone do 4 wywołań co 2 minuty. W przypadku systemu Android 10 ograniczenie przepustowości można wyłączyć w menu Ustawienia dewelopera. Aby uzyskać więcej informacji, zobacz [dokumentację systemu Android][5].<br/>
<sup>6</sup> — ograniczone do [Eddystone][1] i [iBeacon][2]

### <a name="which-sensor-to-enable"></a>Który czujnik do włączenia

Wybór czujnika jest specyficzny dla opracowywanej aplikacji i platformy.
Na poniższym diagramie przedstawiono punkt początkowy, w którym można włączyć kombinację czujników w zależności od scenariusza lokalizacji:

![Diagram włączonych czujników wyboru](media/coarse-relocalization-enabling-sensors.png)

Poniższe sekcje zawierają więcej szczegółowych informacji na temat zalet i ograniczeń dla każdego typu czujnika.

### <a name="gps"></a>GPS

GPS to opcja do użycia w scenariuszach z zewnątrz.
W przypadku korzystania z GPS w aplikacji należy pamiętać, że odczyty udostępniane przez sprzęt są zwykle następujące:

* częstotliwość asynchroniczna i niska (mniej niż 1 Hz).
* niezawodne/zakłócenia (średnio 7-m odchylenie standardowe).

Ogólnie rzecz biorąc, zarówno system operacyjny, jak i kotwice przestrzenne platformy Azure będą wykonywać pewne filtrowanie i ekstrapolację na nieprzetworzonym sygnale GPS, próbując wyeliminować te problemy. To dodatkowe przetwarzanie wymaga czasu na zbieżność, dlatego w celu uzyskania najlepszych wyników należy spróbować:

* Utwórz dostawcę odcisku palca czujnika jako wczesny, jak to możliwe w aplikacji
* utrzymywanie dostawcy odcisków palca czujnika między wieloma sesjami
* Udostępnianie dostawcy odcisków palca czujnika między wieloma sesjami

Urządzenia GPS klasy konsumenckiej są zwykle nieprecyzyjne. Badania przez [Zandenbergen i Barbeau (2011)][6] umożliwiają raportowanie średniej dokładności telefonów komórkowych z obsługą GPS (a-GPS) do około 7 metrów — dość dużej wartości do zignorowania. Aby uwzględnić te błędy pomiarów, usługa traktuje kotwice jako rozkład prawdopodobieństwa w przestrzeni GPS. W związku z tym zakotwiczenie jest teraz regionem obszaru, który najprawdopodobniej (czyli z ponad 95% pewnością) zawiera jego prawdziwe, nieznane położenie GPS.

Te same przyczyny są stosowane podczas wykonywania zapytań przy użyciu GPS. Urządzenie jest reprezentowane jako inny region zaufania przestrzennego wokół jego prawdziwej, nieznanej pozycji GPS. Odnajdowanie kotwic w pobliżu jest tłumaczone na po prostu znalezienie kotwic z regionem zaufania *wystarczająco blisko* obszaru pewności urządzenia, jak pokazano na poniższej ilustracji:

![Wybór kandydatów zakotwiczenia przy użyciu GPS](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>Sieć Wi-Fi

W przypadku urządzeń HoloLens i Android siła sygnału Wi-Fi może być dobrym rozwiązaniem umożliwiającym włączenie niewielkiej lokalizacji.
Jej zaletą jest potencjalna Natychmiastowa dostępność punktów dostępu Wi-Fi (często używanych w systemie, np. miejsc biurowych lub Malls zakupów) bez konieczności dodatkowej konfiguracji.

> [!NOTE]
> System iOS nie udostępnia żadnego interfejsu API do odczytywania siły sygnału Wi-Fi i nie może być używany w przypadku bardzo wysokiej lokalizacji z włączoną obsługą sieci Wi-Fi.

W przypadku korzystania z sieci Wi-Fi w aplikacji należy pamiętać, że odczyty udostępniane przez sprzęt są zwykle następujące:

* częstotliwość asynchroniczna i niska (mniej niż 0,1 Hz).
* możliwe ograniczenie na poziomie systemu operacyjnego.
* niezawodne/zakłócenia (średnio 3-dBm odchylenie standardowe).

Kotwice przestrzenne platformy Azure podejmują próbę skompilowania odfiltrowanej mapy siły sygnału sieci Wi-Fi podczas sesji w celu rozwiązania tych problemów. Aby uzyskać najlepsze wyniki, należy spróbować:

* przed wprowadzeniem pierwszej kotwicy utwórz sesję.
* Utrzymuj sesję aktywności tak długo, jak to możliwe (to oznacza, że wszystkie kotwice i zapytania są tworzone w jednej sesji).

### <a name="bluetooth-beacons"></a>Sygnały nawigacyjne Bluetooth
<a name="beaconsDetails"></a>

Starannie Wdrażaj sygnały nawigacyjne Bluetooth to dobre rozwiązanie w przypadku dużych scenariuszy relokalizacji w dużej skali, w których GPS nie są obecne lub niedokładne. Jest również jedyną metodą pozostałą, która jest obsługiwana przez wszystkie trzy platformy.

Sygnały nawigacyjne są zazwyczaj uniwersalnymi urządzeniami, gdzie wszystko — w tym Identyfikatory UUID i adresy MAC — można skonfigurować. Kotwice przestrzenne platformy Azure oczekują, że sygnały są jednoznacznie identyfikowane przez ich identyfikatory UUID. Niepowodzenie, aby upewnić się, że unikatowość najprawdopodobniej będzie powodowała nieprawidłowe wyniki. Aby uzyskać najlepsze wyniki, należy:

* Przypisywanie unikatowych identyfikatorów UUID do sygnałów nawigacyjnych.
* Wdrażaj je w taki sposób, aby obejmowały miejsce jednorodne i aby co najmniej 3 sygnały nawigacyjne były dostępne z dowolnego miejsca w miejscu.
* Przekaż listę unikatowych identyfikatorów UUID sygnałów do dostawcy odcisków palca czujnika

Sygnały radiowe, takie jak Bluetooth, mają wpływ na przeszkody i mogą zakłócać inne sygnały radiowe. Z tego względu trudno jest złamać, czy Twoje miejsce jest jednolicie pokryte. Aby zapewnić lepszy komfort obsługi klienta, zalecamy ręczne przetestowanie zakresu sygnałów nawigacyjnych. Można to zrobić, przedając miejsce na urządzenia kandydujące i aplikację pokazującą Bluetooth w zakresie. Podczas testowania pokrycia upewnij się, że możesz dotrzeć do co najmniej 3 sygnałów nawigacyjnych z dowolnej strategicznej pozycji obszaru. Skonfigurowanie zbyt wielu sygnałów nawigacyjnych może doprowadzić do większej ingerencji między nimi i niekoniecznie poprawić dokładnej dokładności relokalizacji.

Sygnały nawigacyjne Bluetooth zwykle mają pokrycie 80 metrów, jeśli w tym miejscu nie ma przeszkód.
Oznacza to, że w przypadku miejsca, które nie ma żadnych dużych przeszkód, jedna może wdrożyć sygnały nawigacyjne wzorca siatki co 40 metrów.

Sygnały o wyczerpaniu baterii wpłyną negatywnie na wyniki, więc pamiętaj, aby okresowo monitorować wdrożenie dla niskich lub martwych baterii.

Kotwice przestrzenne platformy Azure śledzą sygnały nawigacyjne Bluetooth, które znajdują się w znanej liście identyfikatorów UUID sąsiedztwa. Złośliwe sygnały są programowane w taki sposób, że identyfikatory UUID mogą mieć negatywny wpływ na jakość usługi. Z tego powodu uzyskasz najlepsze wyniki w nadzorowanych miejscach, w których można kontrolować ich wdrażanie.

### <a name="sensors-accuracy"></a>Dokładność czujników

Dokładność sygnału GPS, zarówno przy tworzeniu kotwic, jak i podczas zapytań, ma duży wpływ na zestaw zwracanych kotwic. W przeciwieństwie do zapytań opartych na sieci Wi-Fi/nadajnikach wszystkie kotwice, które mają co najmniej jeden punkt dostępu/sygnalizator wspólnych w zapytaniu. W tym sensie wynik zapytania opartego na sieci Wi-Fi/sygnałów jest głównie określony przez fizyczny zakres punktów dostępu/sygnałów nawigacyjnych i przeszkód środowiskowych.
W poniższej tabeli szacuje oczekiwany obszar wyszukiwania dla każdego typu czujnika:

| Czujnik      | Promień obszaru wyszukiwania (w przybliżeniu) | Szczegóły |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | Określana na podstawie niepewności GPS między innymi czynnikami. Raportowane liczby są szacowane dla średniej dokładności GPS telefonów komórkowych z-GPS, czyli 7 metrów. |
| Sieć Wi-Fi        | 50 m – 100 m | Określany przez zakres punktów dostępu bezprzewodowego. Zależy od częstotliwości, siły nadajnika, przeszkód fizycznych, zakłóceń i tak dalej. |
| Sygnały nawigacyjne dotyczące beli |  70 m | Określany przez zakres sygnałów nawigacyjnych. Zależy od częstotliwości, siły transmisji, przeszkód fizycznych, zakłóceń i tak dalej. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
