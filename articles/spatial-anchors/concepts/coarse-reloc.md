---
title: Zgrubna ponowna lokalizacja
description: Dowiedz się, jak i kiedy używać zgrubnego ponownego lokalizowania. Zgrubna ponowna lokalizacja pomaga znaleźć kotwice, które znajdują się w pobliżu.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: d2737f58fa95d1aa45d9952e8b501c1b9be4d1b0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600356"
---
# <a name="coarse-relocalization"></a>Zgrubna ponowna lokalizacja

Zgrubna ponowna lokalizacja to funkcja umożliwiająca lokalizację na dużą skalę, zapewniając przybliżoną, ale szybką odpowiedź na następujące pytania: 
- *Gdzie jest teraz moje urządzenie?* 
- *Jaką zawartość należy obserwować?* 
 
Odpowiedź nie jest precyzyjna. Jest w tej postaci: *jesteś blisko tych kotwic. Spróbuj zlokalizować jeden z nich.*

Zgrubna ponowna lokalizacja działa przez tagowanie kotwic za pomocą różnych odczytów czujników na urządzeniu, które są później używane do szybkiego wykonywania zapytań. W scenariuszach na zewnątrz dane czujnika są zwykle położeniem GPS (GPS) urządzenia. Gdy GPS jest niedostępny lub zawodny, na przykład w pomieszczeniu, dane czujników składają się z punktów Wi-Fi dostępu i sygnałów nawigacyjnych Bluetooth w zasięgu. Zebrane dane czujników przyczyniają się do utrzymania indeksu przestrzennego używanego przez usługę Azure Spatial Anchors do szybkiego określenia, które kotwice znajdują się blisko urządzenia.

## <a name="when-to-use-coarse-relocalization"></a>Kiedy należy używać zgrubnego ponownego lokalizowania

Jeśli planujesz obsługę więcej niż 35 kotwic przestrzennych w przestrzeni większej niż na dworze okręgowym, prawdopodobnie skorzystasz z indeksowania przestrzennego zgrubnego ponownego lokalizowania.

Szybkie wyszukiwania kotwic włączane przez zgrubną ponowną lokalizację zaprojektowano w celu uproszczenia tworzenia aplikacji wsadowych przez kolekcje o skali światowej, na przykład milionów rozproszonych geograficznie kotwic. Złożoność indeksowania przestrzennego jest ukryta, więc możesz skupić się na logice aplikacji. Wszystkie trudne prace są wykonywane w tle przez usługę Azure Spatial Anchors.

## <a name="using-coarse-relocalization"></a>Używanie zgrubnego ponownego lokalizowania

Oto typowy przepływ pracy tworzenia i wykonywania zapytań na platformie Azure Spatial Anchors zgrubną ponowną alokacją:
1.  Utwórz i skonfiguruj dostawcę odcisków palców czujników w celu zbierania danych z czujników, których potrzebujesz.
2.  Uruchom sesję usługi Azure Spatial Anchors i utwórz kotwice. Ponieważ odciski palców czujników są włączone, kotwice są indeksowane przestrzennie przez zgrubną ponowną alokację.
3.  Wykonywanie zapytań dotyczących otaczających kotwic przy użyciu zgrubnej ponownej lokalizacji za pośrednictwem dedykowanych kryteriów wyszukiwania w Spatial Anchors sesji.

Możesz zapoznać się z jednym z tych samouczków, aby skonfigurować zgrubną ponowną alokację w aplikacji:
* [Zgrubna ponowna lokalizacja w a unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Zgrubna ponowna lokalizacja w języku Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Zgrubna ponowna lokalizacja w języku Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Zgrubna ponowna lokalizacja w języku Java](../how-tos/set-up-coarse-reloc-java.md)
* [Zgrubna ponowna lokalizacja w C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Zgrubna ponowna lokalizacja w języku C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Czujniki i platformy

### <a name="platform-availability"></a>Dostępność platformy

Do usługi zakotwiczenia można wysyłać następujące typy danych z czujników:

* Położenie GPS: szerokość geograficzna, długość geograficzna, wysokość nad poziomem morza
* Siła sygnału Wi-Fi dostępu w zakresie
* Siła sygnału sygnałów nawigacyjnych Bluetooth w zasięgu

Ta tabela zawiera podsumowanie dostępności danych czujników na obsługiwanych platformach i informacje, o których należy wiedzieć:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **Gps**         | Nr<sup>1</sup>  | Tak<sup>2</sup> | Tak<sup>3</sup> |
| **Wi-Fi**        | Tak<sup>4</sup> | Tak<sup>5</sup> | Nie  |
| **Sygnały nawigacyjne LAMP** | Tak<sup>6</sup> | Tak<sup>6</sup> | Tak<sup>6</sup>|


<sup>1</sup> Z urządzeniem HoloLens można skojarzyć zewnętrzne urządzenie GPS. Jeśli [chcesz używać](../spatial-anchor-support.md) urządzenia HoloLens z trackerem GPS, skontaktuj się z naszą pomocą techniczną.<br/>
<sup>2 Obsługiwane</sup> za [pośrednictwem interfejsów][3] API LocationManager (GPS i SIECI).<br/>
<sup>3 Obsługiwane</sup> za [pośrednictwem interfejsów API CLLocationManager.][4]<br/>
<sup>4</sup> Obsługiwane z szybkością około jednego skanowania co 3 sekundy. <br/>
<sup>5</sup> Począwszy od poziomu 28 interfejsu API, Wi-Fi skanowania są ograniczane do czterech wywołań co 2 minuty. Począwszy od systemu Android 10, możesz wyłączyć to ograniczanie w menu **Ustawienia dewelopera.** Aby uzyskać więcej informacji, zobacz [dokumentację systemu Android.][5]<br/>
<sup>6</sup> Ograniczone do [Eddystone][1] [i iBeacon.][2]

### <a name="which-sensor-to-enable"></a>Który czujnik należy włączyć

Wybór czujnika zależy od projektowej aplikacji i platformy.
Ten diagram stanowi punkt wyjścia do określenia, którą kombinację czujników można włączyć, w zależności od scenariusza lokalizacji:

![Diagram przedstawiający włączone czujniki dla różnych scenariuszy.](media/coarse-relocalization-enabling-sensors.png)

Poniższe sekcje zawierają więcej informacji na temat zalet i ograniczeń poszczególnych typów czujników.

### <a name="gps"></a>Gps

GPS to opcja przejdź do scenariuszy na zewnątrz.
W przypadku korzystania z gps w aplikacji należy pamiętać, że odczyty dostarczone przez sprzęt są zwykle:

* Częstotliwość asynchroniczna i niska (mniejsza niż 1 Hz).
* Zawodne/hałaśliwe (średnio odchylenie standardowe 7 m).

Ogólnie rzecz biorąc, zarówno system operacyjny urządzenia, jak i Spatial Anchors będą filtrować i ekstrapolować nieprzetworzonym sygnałem GPS w celu ograniczenia tych problemów. To dodatkowe przetwarzanie wymaga czasu na zbieżność, dlatego aby uzyskać najlepsze wyniki, należy spróbować:

* Utwórz jak najszybciej jednego dostawcę odcisku palca czujnika w aplikacji.
* Utrzymuj aktywności dostawcy odcisków palców czujników między wieloma sesjami.
* Udostępnianie dostawcy odcisków palców czujników między wieloma sesjami.

Urządzenia GPS klasy konsumenta są zwykle niedokładne. Badania [Zandenbergen i Barbeau (2011)][6] raporty, że mediana dokładności telefonów komórkowych, które mają GPS (A-GPS) jest około 7 metrów. To dość duża wartość do zignorowania. Aby uwzględnić te błędy pomiaru, usługa traktuje kotwice jako rozkłady prawdopodobieństwa w przestrzeni GPS. Dlatego kotwica jest regionem miejsca, które najprawdopodobniej (z ponad 95% ufnością) zawiera swoją prawdziwą, nieznaną pozycję GPS.

To samo uzasadnienie ma zastosowanie podczas wykonywania zapytania przy użyciu GPS. Urządzenie jest reprezentowane jako inny obszar ufności przestrzennej wokół jego prawdziwej, nieznanej pozycji GPS. Odnajdywanie kotwic w pobliżu przekłada się na  znalezienie kotwic z regionami ufności wystarczająco zbliżonymi do regionu ufności urządzenia, jak pokazano tutaj:

![Diagram, który ilustruje znajdowanie kandydatów kotwicy przy użyciu GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

Na urządzeniach HoloLens i Wi-Fi siły sygnału może być dobrym sposobem na włączenie zgrubnego ponownego lokalizowania pomieszczeń.
Zaletą jest potencjalna natychmiastowa dostępność punktów Wi-Fi (na przykład typowych w biurach i sklepach) bez dodatkowej konfiguracji.

> [!NOTE]
> System iOS nie zapewnia interfejsu API do odczytywania Wi-Fi siły sygnału, dlatego nie może być używany do zgrubnego ponownego lokalizowania włączonego za pośrednictwem sieci Wi-Fi.

W przypadku Wi-Fi w aplikacji należy pamiętać, że odczyty udostępniane przez sprzęt są zwykle:

* Asynchroniczna i niska częstotliwość (mniej niż 0,1 Hz).
* Potencjalnie ograniczone na poziomie systemu operacyjnego.
* Zawodne/hałaśliwe (średnio odchylenie standardowe 3 dBm).

Spatial Anchors spróbuje utworzyć filtrowaną mapę Wi-Fi siły sygnału podczas sesji w celu ograniczenia tych problemów. Aby uzyskać najlepsze wyniki, spróbuj:

* Utwórz sesję przed umieścić pierwszą kotwicę.
* Utrzymuj żywą sesję tak długo, jak to możliwe. (Oznacza to, że utwórz wszystkie kotwice i zapytania w jednej sesji).

### <a name="bluetooth-beacons"></a>Sygnały nawigacyjne bluetooth
<a name="beaconsDetails"></a>

Dokładne wdrożenie sygnałów nawigacyjnych Bluetooth jest dobrym rozwiązaniem w przypadku scenariuszy zgrubnej lokalizacji na dużą skalę, w których GPS jest niedokładny lub niedokładny. Jest to również jedyna metoda w pomieszczeniu obsługiwana na wszystkich trzech platformach.

Sygnały nawigacyjne to zwykle uniwersalne urządzenia, na których można skonfigurować wszystko, w tym identyfikatory UUID i adresy MAC. Usługa Azure Spatial Anchors oczekuje, że sygnały nawigacyjne będą jednoznacznie identyfikowane przez ich identyfikatory UUID. Jeśli nie zapewnisz tej unikatowości, prawdopodobnie otrzymasz niepoprawne wyniki. Aby uzyskać najlepsze wyniki:

* Przypisz unikatowe identyfikatory UUID do sygnałów nawigacyjnych.
* Wdrażanie sygnałów nawigacyjnych w sposób, który równomiernie obejmuje przestrzeń i tak, aby co najmniej trzy sygnały nawigacyjne są dostępne z dowolnego punktu w przestrzeni.
* Przekaż listę unikatowych identyfikatorów UUID sygnałów nawigacyjnych do dostawcy odcisków palców czujnika.

Na sygnały radiowe, takie jak te z połączenia Bluetooth, wpływają przeszkody i mogą zakłócać inne sygnały radiowe. Dlatego trudno jest odgadnąć, czy przestrzeń jest równomiernie zasłonięte. Aby zagwarantować lepsze środowisko obsługi klienta, zalecamy ręczne przetestowanie pokrycia sygnałów nawigacyjnych. Test można przeprowadzić, przechodząc przez obszar z urządzeniami kandydacyjnie i aplikacją, która pokazuje łączność Bluetooth w zasięgu. Podczas testowania pokrycia upewnij się, że możesz uzyskać co najmniej trzy sygnały nawigacyjne z dowolnego strategicznego położenia w Twojej przestrzeni. Zbyt wiele sygnałów nawigacyjnych może spowodować większą interferenację między nimi i niekoniecznie poprawi dokładność zgrubnego ponownego lokalizowania.

Sygnały nawigacyjne Bluetooth zwykle obejmują 80 metrów, jeśli w przestrzeni nie ma żadnych przeszkód.
Dlatego w przypadku przestrzeni, która nie ma dużych przeszkód, można wdrażać sygnały nawigacyjne we wzorcu siatki co 40 metrów.

Sygnał nawigacyjny, na który jest wyczyszczana bateria, będzie miał wpływ na wyniki, dlatego należy okresowo monitorować wdrożenie pod względu na niskie lub nieładowe baterii.

Usługa Azure Spatial Anchors będzie śledzić tylko sygnały nawigacyjne Bluetooth, które znajdują się na znanej liście identyfikatorów UUID zbliżeniowych sygnałów nawigacyjnych. Jednak złośliwe sygnały nawigacyjne, które zostały zaprogramowane jako identyfikatory UUID z listy do zezwalania, mogą negatywnie wpłynąć na jakość usługi. Dlatego najlepsze wyniki można uzyskać w miejscach, w których można kontrolować wdrażanie sygnałów nawigacyjnych.

### <a name="sensor-accuracy"></a>Dokładność czujnika

Dokładność sygnału GPS, zarówno podczas tworzenia kotwicy, jak i podczas zapytań, ma znaczący wpływ na zestaw zwróconych kotwic. Z kolei zapytania oparte na sieci Wi-Fi/sygnałach nawigacyjnych będą uwzględniać wszystkie kotwice, które mają co najmniej jeden punkt dostępu/sygnał nawigacyjny wspólny dla zapytania. W tym sensie wynik zapytania opartego na sieci Wi-Fi/sygnałach nawigacyjnych zależy głównie od fizycznego zakresu punktów dostępu/sygnałów nawigacyjnych i środowisk.
Ta tabela szacuje oczekiwaną przestrzeń wyszukiwania dla każdego typu czujnika:

| Czujnik      | Promień obszaru wyszukiwania (przybliżony) | Szczegóły |
|-------------|:-------:|---------|
| **Gps**         | od 20 m do 30 m | Jest to określane między innymi przez niepewność GPS. Zgłaszane liczby są szacowane dla mediany dokładności GPS telefonów komórkowych z A-GPS: 7 metrów. |
| **Wi-Fi**        | 50 m do 100 m | Zależy od zakresu punktów dostępu bezprzewodowego. Zależy od częstotliwości, siły powietrza, fizycznych przeszkód, interferencji i tak dalej. |
| **Sygnały nawigacyjne LAMP** |  70 m | Zależy od zakresu sygnału nawigacyjnego. Zależy od częstotliwości, siły transmisji, fizycznych czynników, interferencji i tak dalej. |

<!-- Reference links in article -->
[1]: https://developer.estimote.com/eddystone/
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
