---
title: Zgrubna ponowna lokalizacja
description: Dowiedz się, jak i kiedy używać bardzo dużej lokalizacji. Funkcja bardzo gruba umożliwia znalezienie zakotwiczenia, które znajdują się blisko siebie.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656179"
---
# <a name="coarse-relocalization"></a>Zgrubna ponowna lokalizacja

Funkcja, która umożliwia szybkie lokalizowanie w dużej skali, zapewniając przybliżoną, ale szybką odpowiedź na te pytania: 
- *Gdzie jest teraz moje urządzenie?* 
- *Jaką zawartość należy zaobserwować?* 
 
Odpowiedź nie jest precyzyjna. W tej postaci: Jesteś *blisko tych kotwic. Spróbuj zlokalizować jeden z nich*.

Niestandardowa relokalizacja działa przez tagowanie kotwic z różnymi odczytami czujników na urządzeniu, które są później używane do szybkiego wykonywania zapytań. W przypadku scenariuszy z zewnątrz dane czujnika są zwykle pozycjami GPS (GPS) urządzenia. Gdy GPS jest niedostępna lub niezawodna, jak w przypadku braku drzwi, dane czujnika składają się z Wi-Fi punktów dostępu i sygnałów Bluetooth w zakresie. Zebrane dane czujnika przyczyniają się do utrzymania indeksu przestrzennego używanego przez kotwice przestrzenne platformy Azure, aby szybko określić, które kotwice znajdują się blisko Twojego urządzenia.

## <a name="when-to-use-coarse-relocalization"></a>Kiedy używać grubej relokalizacji

Jeśli planujesz obsługę więcej niż 35 kotwic przestrzennych w obszarze większym niż sąd odtenisa, prawdopodobnie skorzystasz z niedużych indeksowań przestrzennych relokalizacji.

Szybkie wyszukiwanie kotwic włączonych przy użyciu ogromnej relokalizacji jest zaprojektowane w celu uproszczenia opracowywania aplikacji, które są obsługiwane przez światowe kolekcje, na przykład, miliony kotwic rozproszonych geograficznie. Złożoność indeksowania przestrzennego jest ukryta, dzięki czemu można skupić się na logice aplikacji. Wszystkie trudne zadania są wykonywane w tle przez kotwice przestrzenne platformy Azure.

## <a name="using-coarse-relocalization"></a>Używanie bardzo dużej lokalizacji

Poniżej przedstawiono typowy przepływ pracy służący do tworzenia i wykonywania zapytań o kotwice przestrzenne platformy Azure z bardzo niespotykaną lokalizacją:
1.  Utwórz i Skonfiguruj dostawcę odcisków palca czujnika w celu zebrania żądanych danych czujników.
2.  Rozpocznij sesję zakotwiczeń przestrzennych platformy Azure i Utwórz kotwice. Ponieważ włączono odciski palców czujnika, kotwice są indeksowane w sposób przestrzenny przez niegrubą relokalizację.
3.  Zapytanie otaczające kotwice przy użyciu bardzo dużej lokalizacji w ramach dedykowanych kryteriów wyszukiwania w sesji kotwic przestrzennych.

Możesz odwołać się do jednego z tych samouczków, aby skonfigurować niegrubą relokalizację w aplikacji:
* [Duże przelokalizowanie w środowisku Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Niestandardowa relokalizacja w zamierzeniu C](../how-tos/set-up-coarse-reloc-objc.md)
* [Gruba relokalizacja w SWIFT](../how-tos/set-up-coarse-reloc-swift.md)
* [Duże przelokalizowanie w języku Java](../how-tos/set-up-coarse-reloc-java.md)
* [Duże relokalizacja w języku C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Duże relokalizacja w języku C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Czujniki i platformy

### <a name="platform-availability"></a>Dostępność platformy

Te typy danych czujników można wysłać do usługi zakotwiczenia:

* Pozycja GPS: Szerokość geograficzna, Długość geograficzna, Wysokość
* Siła sygnału punktów dostępu Wi-Fi w zakresie
* Siła sygnału sygnałów nawigacyjnych Bluetooth w zakresie

Ta tabela zawiera podsumowanie dostępności danych czujników na obsługiwanych platformach i zawiera informacje, o których należy pamiętać:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Nie<sup>1</sup>  | Tak<sup>2</sup> | Tak<sup>3</sup> |
| **Wi-Fi**        | Tak<sup>4</sup> | Tak<sup>5</sup> | Nie  |
| **Sygnały nawigacyjne dotyczące beli** | Tak<sup>6</sup> | Tak<sup>6</sup> | Tak<sup>6</sup>|


<sup>1</sup> zewnętrzne urządzenie GPS może być skojarzone z urządzeniem HoloLens. Skontaktuj się z [naszą pomocą techniczną](../spatial-anchor-support.md) , jeśli wolisz korzystać z urządzenia HoloLens z systemem śledzącym GPS.<br/>
<sup>2</sup> obsługiwane za poorednictwem interfejsów API [lokalizacji][3] (GPS i Network).<br/>
<sup>3</sup> obsługiwane przez interfejsy API [CLLocationManager][4] .<br/>
<sup>4</sup> są obsługiwane z szybkością około jednego skanowania co 3 sekundy. <br/>
<sup>5</sup> począwszy od poziomu interfejsu API 28, Wi-Fi skanowania są ograniczone do czterech wywołań co 2 minuty. Począwszy od systemu Android 10, można wyłączyć to ograniczenie z poziomu menu **Ustawienia dewelopera** . Aby uzyskać więcej informacji, zobacz [dokumentację systemu Android][5].<br/>
<sup>6</sup> — ograniczone do [Eddystone][1] i [iBeacon][2].

### <a name="which-sensor-to-enable"></a>Który czujnik do włączenia

Wybór czujnika zależy od opracowywanej aplikacji i platformy.
Ten diagram przedstawia punkt wyjścia do określania, którą kombinację czujników można włączyć, w zależności od scenariusza lokalizacji:

![Diagram przedstawiający obsługiwane czujniki dla różnych scenariuszy.](media/coarse-relocalization-enabling-sensors.png)

Poniższe sekcje zawierają więcej szczegółowych informacji na temat zalet i ograniczeń poszczególnych typów czujników.

### <a name="gps"></a>GPS

GPS to opcja do użycia w scenariuszach z zewnątrz.
W przypadku korzystania z GPS w aplikacji należy pamiętać, że odczyty udostępniane przez sprzęt są zwykle następujące:

* Częstotliwość asynchroniczna i niska (mniej niż 1 Hz).
* Niezawodne/zakłócenia (średnio, 7-m odchylenie standardowe).

Ogólnie rzecz biorąc, zarówno system operacyjny, jak i kotwice przestrzenne będą wykonywać pewne filtrowanie i ekstrapolację pierwotnego sygnału GPS, próbując wyeliminować te problemy. To dodatkowe przetwarzanie wymaga czasu na zbieżność, dlatego w celu uzyskania najlepszych wyników należy spróbować:

* Utwórz dostawcę odcisku palca czujnika jako wczesny, jak to możliwe w aplikacji.
* Utrzymywanie dostawcy odcisków palca czujnika między wieloma sesjami.
* Udostępnij dostawcę odcisku palca czujnika między wieloma sesjami.

Urządzenia GPS klasy konsumenckiej są zwykle nieprecyzyjne. Badania przez [Zandenbergen i Barbeau (2011)][6] raportują, że mediana dokładność telefonów komórkowych z obsługą GPS (a-GPS) ma około 7 metrów. Jest to dość duża wartość do zignorowania. Aby uwzględnić te błędy pomiarów, usługa traktuje kotwice jako rozkład prawdopodobieństwa w przestrzeni GPS. Dlatego zakotwiczenie jest regionem obszaru, który najprawdopodobniej (z więcej niż 95% pewnością) zawiera jego prawdziwe, nieznane położenie GPS.

Takie samo działanie ma zastosowanie podczas wykonywania zapytania przy użyciu aplikacji GPS. Urządzenie jest reprezentowane jako inny region zaufania przestrzennego wokół jego prawdziwej, nieznanej pozycji GPS. Odnajdowanie kotwic w pobliżu tłumaczy się, aby znaleźć kotwice z regionami zaufania *wystarczająco blisko* obszaru pewności urządzenia, jak pokazano tutaj:

![Diagram przedstawiający Znajdowanie kandydatów zakotwiczonych przy użyciu GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

W przypadku urządzeń HoloLens i Android siła sygnału Wi-Fi może być dobrym sposobem na włączenie niewielkiej lokalizacji.
Zaletą jest potencjalna Natychmiastowa dostępność punktów dostępu Wi-Fi (na przykład często używanych w programie Office Spaces i zakupów) bez konieczności dodatkowej instalacji.

> [!NOTE]
> System iOS nie udostępnia interfejsu API do odczytywania siły sygnału Wi-Fi, dlatego nie można go używać w przypadku niedużej lokalizacji włączonej za pośrednictwem sieci Wi-Fi.

W przypadku korzystania z Wi-Fi w aplikacji należy pamiętać, że odczyty udostępniane przez sprzęt są zwykle następujące:

* Częstotliwość asynchroniczna i niska (mniej niż 0,1 Hz).
* Możliwe ograniczenie na poziomie systemu operacyjnego.
* Niezawodne/zakłócenia (średnio, 3-dBm odchylenie standardowe).

Kotwice przestrzenne spróbują utworzyć przefiltrowaną mapę Wi-Fi siły sygnału podczas sesji, próbując wyeliminować te problemy. Aby uzyskać najlepsze wyniki, spróbuj wykonać następujące polecenie:

* Przed wprowadzeniem pierwszej kotwicy utwórz sesję.
* Utrzymywanie aktywności sesji przez tak długo, jak to możliwe. (Oznacza to, że wszystkie kotwice i zapytania są tworzone w jednej sesji).

### <a name="bluetooth-beacons"></a>Sygnały nawigacyjne Bluetooth
<a name="beaconsDetails"></a>

Dokładne wdrożenie sygnałów nawigacyjnych Bluetooth to dobre rozwiązanie w przypadku dużych scenariuszy relokalizacyjnych w dużej skali, w których GPS nie są obecne lub niedokładne. Jest ona również jedyną metodą pozostała, która jest obsługiwana przez wszystkie trzy platformy.

Sygnały nawigacyjne to zazwyczaj uniwersalne urządzenia, na których można skonfigurować wszystko, w tym Identyfikatory UUID i adresy MAC. Kotwice przestrzenne platformy Azure oczekują, że sygnały są jednoznacznie identyfikowane przez ich identyfikatory UUID. Jeśli nie zagwarantujesz tej unikalności, prawdopodobnie otrzymasz nieprawidłowe wyniki. Aby uzyskać najlepsze wyniki:

* Przypisywanie unikatowych identyfikatorów UUID do sygnałów nawigacyjnych.
* Wdrażaj sygnały nawigacyjne w taki sposób, aby w sposób jednorodny pokrywał się ze swoim miejscem, co pozwala uzyskać dostęp do co najmniej trzech sygnałów nawigacyjnych z dowolnego miejsca.
* Przekaż listę unikatowych identyfikatorów UUID sygnałów do dostawcy odcisków palca czujnika.

Sygnały radiowe, takie jak te, mają wpływ na przeszkody i mogą zakłócać inne sygnały radiowe. Dlatego może być trudne do odgadnięcia, czy Twoje miejsce jest jednolicie pokryte. Aby zapewnić lepszą obsługę klienta, zalecamy ręczne testowanie zakresu sygnałów nawigacyjnych. Możesz przeprowadzić test, przechodzenia przez idącą ilość miejsca na urządzenia kandydujące i aplikację, która pokazuje Bluetooth w zakresie. Podczas testowania pokrycia upewnij się, że możesz dotrzeć do co najmniej trzech sygnałów nawigacyjnych z dowolnej pozycji strategicznej w miejscu. Zbyt wiele sygnałów nawigacyjnych może powodować zwiększenie zakłóceń między nimi i niekoniecznie poprawić dokładność nieścisłej lokalizacji.

Sygnały nawigacyjne Bluetooth zazwyczaj obejmują 80 liczników, jeśli nie występują żadne przeszkody w tym miejscu.
Tak więc w przypadku miejsca, które nie ma dużych przeszkód, można wdrożyć sygnały nawigacyjne w deseniu siatki co 40 metrów.

Sygnał, który jest zasilany z baterii, będzie miał wpływ na wyniki, dlatego należy koniecznie monitorować wdrożenie w przypadku niskich lub nieobciążona baterii.

Kotwice przestrzenne platformy Azure będą śledzić tylko sygnały Bluetooth, które znajdują się na liście identyfikatorów UUID bliskych sygnałów nawigacyjnych. Jednak złośliwe sygnały są zaprogramowane w taki sposób, aby allowlisted UUID mogły mieć negatywny wpływ na jakość usługi. Dzięki temu uzyskasz najlepsze wyniki w nadzorowanych miejscach, w których można kontrolować wdrażanie sygnałów.

### <a name="sensor-accuracy"></a>Dokładność czujnika

Dokładność sygnału GPS, zarówno podczas tworzenia kotwicy, jak i podczas wykonywania zapytań, ma znaczny wpływ na zestaw zwracanych kotwic. W przeciwieństwie do zapytań opartych na sieci Wi-Fi/nadajniki będą brane pod uwagę wszystkie kotwice, które mają co najmniej jeden punkt dostępu/sygnalizator wspólny dla zapytania. W tym sensie wynik zapytania, które jest oparte na sieci Wi-Fi/sygnały nawigacyjny, jest określany głównie przez fizyczny zakres punktów dostępu/sygnałów nawigacyjnych i przeszkód w środowisku.
W tej tabeli szacuje oczekiwany obszar wyszukiwania dla każdego typu czujnika:

| Czujnik      | Promień miejsca wyszukiwania (w przybliżeniu) | Szczegóły |
|-------------|:-------:|---------|
| **GPS**         | od 20 do 30 m | Określana na podstawie niepewności GPS, między innymi czynnikami. Raportowane liczby są szacowane dla średniego dokładności GPS telefonów komórkowych z-GPS: 7 metrów. |
| **Wi-Fi**        | 50 m do 100 m | Określany przez zakres punktów dostępu bezprzewodowego. Zależy od częstotliwości, siły nadajnika, przeszkód fizycznych, zakłóceń i tak dalej. |
| **Sygnały nawigacyjne dotyczące beli** |  70 m | Określany przez zakres sygnałów nawigacyjnych. Zależy od częstotliwości, siły transmisji, przeszkód fizycznych, zakłóceń i tak dalej. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
