---
title: Analizowanie statystyk użycia przy użyciu zaawansowanych raportów HTTP Azure CDN | Microsoft Docs
description: Dowiedz się, jak tworzyć zaawansowane raporty HTTP w usłudze Microsoft Azure CDN. Te raporty zawierają szczegółowe informacje na temat aktywności sieci CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e2ef1367cbbb1fb71159c4520c3d1481904c1769
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779326"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analizowanie statystyk użycia przy użyciu zaawansowanych raportów HTTP usługi Azure CDN
## <a name="overview"></a>Omówienie
W tym dokumencie wyjaśniono zaawansowane raportowanie protokołu HTTP w usłudze Microsoft Azure CDN. Te raporty zawierają szczegółowe informacje na temat aktywności sieci CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Uzyskiwanie dostępu do zaawansowanych raportów HTTP
1. W bloku profil usługi CDN kliknij przycisk **Zarządzaj** .
   
    ![Przycisk zarządzania bloku profilu usługi CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.
2. Zatrzymaj wskaźnik myszy na karcie **Analiza** , a następnie umieść wskaźnik myszy nad zaawansowanymi menu wysuwane **raporty http** .  Kliknij pozycję **http Large platform** .
   
    ![Portal zarządzania sieci CDN — Zaawansowane raporty — menu](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Wyświetlane są opcje raportu.

## <a name="geography-reports-map-based"></a>Raporty geografii (oparte na mapowaniu)
Istnieje pięć raportów, które wykorzystują mapę, aby wskazać regiony, z których dana zawartość jest żądana. Te raporty to mapa świata, Mapa Stany Zjednoczone, Mapa Kanady, mapa Europa i Azja i Pacyfik.

Każdy raport na podstawie mapy określa jednostki geograficzne (tj. kraje/regiony), która ułatwia wizualizację lokalizacji, z których żądaną zawartość. Można to zrobić przez kolorowe kodowanie poszczególnych regionów zgodnie z ilością zapotrzebowania w danym regionie. Jaśniejsze regiony zacieniowane wskazują na mniejsze zapotrzebowanie na zawartość, a ciemne regiony wskazują wyższy poziom popytu na zawartość.

Szczegółowy ruch i informacje o przepustowości dla każdego regionu są podawane bezpośrednio poniżej mapy. Pozwala to wyświetlić łączną liczbę trafień, procent trafień, łączną ilość przesłanych danych (w gigabajtach) i procent danych transferowanych dla każdego regionu. Wyświetl opis każdej z tych metryk. Na koniec po umieszczeniu wskaźnika myszy na regionie (tj. kraju/regionu, stanu lub prowincji) nazwa i procent trafień w regionie będą wyświetlane jako etykietka narzędzia.

Poniżej przedstawiono krótki opis każdego typu raportu Geografia oparta na mapie.

| Nazwa raportu | Opis |
| --- | --- |
| Mapa świata |Ten raport umożliwia wyświetlenie ogólnoświatowego popytu dla zawartości sieci CDN. Każdy kraj/region jest kodowany kolorem na mapie świata, aby wskazać procent trafień pochodzących z tego regionu. |
| Mapa Stany Zjednoczone |Ten raport umożliwia wyświetlenie zapotrzebowania na zawartość usługi CDN w Stany Zjednoczone. Każdy stan jest kodowany kolorem na tej mapie, aby wskazać procent trafień pochodzących z tego regionu. |
| Mapa Kanady |Ten raport umożliwia wyświetlenie zapotrzebowania na zawartość usługi CDN w Kanadzie. Każda Prowincja jest zakodowana kolorem na tej mapie, aby wskazać procent trafień pochodzących z tego regionu. |
| Mapa Europa |Ten raport umożliwia wyświetlenie zapotrzebowania na zawartość usługi CDN w Europie. Każdy kraj/region jest kodowany kolorem na tej mapie, aby wskazać procent trafień pochodzących z tego regionu. |
| Mapa Azja i Pacyfik |Ten raport umożliwia wyświetlenie zapotrzebowania na zawartość usługi CDN w Azji. Każdy kraj/region jest kodowany kolorem na tej mapie, aby wskazać procent trafień pochodzących z tego regionu. |

## <a name="geography-reports-bar-charts"></a>Raporty geografii (wykresy słupkowe)
Istnieją dwa dodatkowe raporty, które udostępniają informacje statystyczne według lokalizacji geograficznej, czyli najważniejszych miast i krajów. Te raporty zawierają odpowiednio rangę miasta i kraje/regiony, zgodnie z liczbą trafień pochodzących z tych krajów/regionów. Po wygenerowaniu tego typu raportu wykres słupkowy będzie wskazywał górne 10 miast lub kraje/regiony, które żądają zawartości za pośrednictwem określonej platformy. Ten wykres słupkowy pozwala szybko ocenić regiony, które generują największą liczbę żądań dla zawartości.

Lewa strona wykresu (oś y) wskazuje liczbę trafień w określonym regionie. Bezpośrednio pod wykresem (oś x) znajdziesz etykietę dla każdego z 10 najważniejszych regionów.

### <a name="using-the-bar-charts"></a>Korzystanie z wykresów słupkowych
* Po umieszczeniu wskaźnika myszy na pasku nazwa i łączna liczba trafień w regionie będzie wyświetlana jako etykietka narzędzia.
* Etykietka narzędzia raportu dla najważniejszych miast identyfikuje miasto według nazwy, województwa i skrótu kraju/regionu.
* Jeśli miasto lub region (tzn. Województwo), z którego pochodzi żądanie nie mogło zostać określone, będzie wskazywać, że są one nieznane. Jeśli kraj/region jest nieznany, zostaną wyświetlone dwa znaki zapytania (tj.??).
* Raport może zawierać metryki dla "Europa" lub "Region Azja/Pacyfik". Te elementy nie są przeznaczone do przekazywania informacji statystycznych dotyczących wszystkich adresów IP w tych regionach. Zamiast tego mają zastosowanie tylko do żądań pochodzących z adresów IP, które są rozłożone w Europie lub Azji/Pacyfiku zamiast do określonego miasta lub kraju/regionu.

Dane użyte do wygenerowania wykresu słupkowego mogą być wyświetlane poniżej. Znajdziesz łączną liczbę trafień, procent trafień, ilość transferowanych danych (w gigabajtach) i procent danych transferowanych dla najważniejszych regionów 250. Wyświetl opis każdej z tych metryk.

Krótki opis jest dostępny dla obu typów raportów poniżej.

| Nazwa raportu | Opis |
| --- | --- |
| Najpopularniejsze miasta |Ten raport określa liczbę miast zgodnie z liczbą trafień, które pochodzą z tego regionu. |
| Najważniejsze kraje |Ten raport porządkuje kraje/regiony zgodnie z liczbą trafień, które pochodzą z tego kraju/regionu. |

## <a name="daily-summary"></a>Podsumowanie dzienne
Raport podsumowujący dzienny umożliwia wyświetlenie łącznej liczby trafień i danych przesyłanych przez określoną platformę codziennie. Te informacje mogą służyć do szybkiego rozpoznać wzorców aktywności sieci CDN. Na przykład ten raport może pomóc wykryć, które dni mają wyższy lub niższy niż oczekiwany ruch.

Po wygenerowaniu tego typu raportu wykres słupkowy dostarczy Wskaźnik wizualny do wielkości popytu specyficznego dla platformy, który jest codziennie realizowany w okresie objętym raportem. Spowoduje to wyświetlenie paska dla każdego dnia w raporcie. Na przykład wybranie przedziału czasu o nazwie "ostatni tydzień" spowoduje wygenerowanie wykresu słupkowego z siedem słupków. Każdy pasek będzie wskazywał całkowitą liczbę trafień w danym dniu.

Lewa strona wykresu (oś y) wskazuje liczbę trafień w określonym dniu. Bezpośrednio pod wykresem (oś x) znajduje się etykieta wskazująca datę (format: RRRR-MM-DD) dla każdego dnia zawartego w raporcie.

> [!TIP]
> Po umieszczeniu wskaźnika myszy na pasku całkowita liczba trafień w tej dacie będzie wyświetlana jako etykietka narzędzia.
> 
> 

Dane użyte do wygenerowania wykresu słupkowego mogą być wyświetlane poniżej. W każdym dniu pokrytym przez raport znajdziesz łączną liczbę trafień i ilość transferowanych danych (w gigabajtach).

## <a name="by-hour"></a>Według godziny
Raport za godzinę umożliwia wyświetlenie łącznej liczby trafień i danych transferowanych na konkretną platformę w godzinie. Te informacje mogą służyć do szybkiego rozpoznać wzorców aktywności sieci CDN. Na przykład ten raport może pomóc wykryć okresy czasu w ciągu dnia, który jest większy lub niższy niż oczekiwany ruch.

Po wygenerowaniu tego typu raportu wykres słupkowy dostarczy wskazówkę wizualną do wielkości popytu specyficznego dla platformy, która jest naliczana co godzinę w przedziale czasowym objętym raportem. Spowoduje to wyświetlenie paska dla każdej godziny pokrytego przez raport. Na przykład wybranie 24-godzinnego okresu spowoduje wygenerowanie wykresu słupkowego zawierającego dwadzieścia cztery słupki. Każdy pasek będzie wskazywał łączną liczbę trafień w ciągu tej godziny.

Lewa strona wykresu (oś y) wskazuje, ile trafień wystąpiło w określonej godzinie. Bezpośrednio pod wykresem (oś x) znajduje się etykieta wskazująca datę/godzinę (format: RRRR-MM-DD hh: mm) dla każdej godziny zawartej w raporcie. Czas jest raportowany przy użyciu formatu 24-godzinnego i jest określany przy użyciu strefy czasowej UTC/GMT.

> [!TIP]
> Po umieszczeniu wskaźnika myszy na pasku całkowita liczba trafień w ciągu tej godziny będzie wyświetlana jako etykietka narzędzia.
> 
> 

Dane użyte do wygenerowania wykresu słupkowego mogą być wyświetlane poniżej. W każdej godzinie uwzględnionej w raporcie znajdziesz łączną liczbę trafień i ilość transferowanych danych (w gigabajtach).

## <a name="by-file"></a>Według pliku
Raport według pliku umożliwia wyświetlenie ilości żądania i ruchu ponoszonego przez określoną platformę dla najbardziej żądanych zasobów. Po wygenerowaniu tego typu raportu wykres słupkowy zostanie wygenerowany na 10 najczęściej żądanych zasobach w określonym przedziale czasu.

> [!NOTE]
> Na potrzeby tego raportu, adresy URL brzegów CNAME są konwertowane na odpowiadające im adresy URL sieci CDN. Umożliwia to dokładne określenie łącznej liczby trafień skojarzonych z zasobem, niezależnie od adresu URL sieci CDN lub rekordu CNAME używanego do żądania.
> 
> 

Lewa strona wykresu (oś y) wskazuje liczbę żądań dla każdego zasobu w określonym przedziale czasu. Bezpośrednio pod wykresem (oś x) znajduje się etykieta wskazująca nazwę pliku dla każdego z pierwszych 10 żądanych zasobów.

Dane użyte do wygenerowania wykresu słupkowego mogą być wyświetlane poniżej. Dostępne są następujące informacje dotyczące każdego z 250 najbardziej wymaganych zasobów: ścieżki względnej, łącznej liczby trafień, procentu trafień, ilości transferowanych danych (w gigabajtach) oraz procentu transferu danych.

## <a name="by-file-detail"></a>Według szczegółów pliku
Raport szczegóły według pliku umożliwia wyświetlenie ilości żądania i ruchu ponoszonego przez określoną platformę dla określonego zasobu. Na początku tego raportu znajdują się szczegóły pliku dla opcji. Ta opcja zapewnia listę najbardziej żądanych zasobów na wybranej platformie. Aby wygenerować raport o szczegółach według pliku, należy wybrać żądany element zawartości z opcji plik szczegóły. Po upływie tego wykresu słupkowego będzie wskazywać dzienny popyt generowany w określonym przedziale czasu.

Lewa strona wykresu (oś y) wskazuje łączną liczbę żądań, które zasób napotkał w danym dniu. Bezpośrednio pod wykresem (oś x) znajduje się etykieta wskazująca datę (format: RRRR-MM-DD), dla której zgłoszono żądanie sieci CDN dla zasobu.

Dane użyte do wygenerowania wykresu słupkowego mogą być wyświetlane poniżej. W każdym dniu pokrytym przez raport znajdziesz łączną liczbę trafień i ilość transferowanych danych (w gigabajtach).

## <a name="by-file-type"></a>Według typu pliku
Raport według typu pliku umożliwia wyświetlenie ilości żądania i ruchu ponoszonego przez typ pliku. Po wygenerowaniu tego typu raportu wykres pierścieniowy będzie wskazywał procent trafień generowanych przez pierwsze 10 typów plików.

> [!TIP]
> Jeśli umieścisz kursor na wycinku wykresu pierścieniowego, typ nośnika internetowego tego typu będzie wyświetlany jako etykietka narzędzia.
> 
> 

Dane, które były używane do generowania wykresu pierścieniowego, można wyświetlić poniżej. Znajduje się w nim rozszerzenie nazwy pliku/typ nośnika internetowego, Łączna liczba trafień, procent trafień, ilość transferowanych danych (w gigabajtach) i procent danych transferowanych dla każdego z najpopularniejszych typów plików 250.

## <a name="by-directory"></a>Według katalogu
Raport według katalogu umożliwia wyświetlenie ilości żądania i ruchu ponoszonego przez określoną platformę dla zawartości z określonego katalogu. Po wygenerowaniu tego typu raportu wykres słupkowy będzie wskazywał łączną liczbę trafień generowanych przez zawartość w głównych 10 katalogach.

### <a name="using-the-bar-chart"></a>Używanie wykresu słupkowego
* Umieść kursor nad paskiem, aby wyświetlić ścieżkę względną do odpowiedniego katalogu.
* Zawartość przechowywana w podfolderze katalogu nie jest uwzględniana podczas obliczania popytu według katalogu. To obliczenie opiera się wyłącznie na liczbie żądań wygenerowanych dla zawartości przechowywanej w bieżącym katalogu.
* Na potrzeby tego raportu, adresy URL brzegów CNAME są konwertowane na odpowiadające im adresy URL sieci CDN. Pozwala to na dokładne określenie wszystkich statystyk skojarzonych z elementem zawartości, niezależnie od adresu URL sieci CDN lub rekordu CNAME używanego do żądania.

Lewa strona wykresu (oś y) wskazuje całkowitą liczbę żądań zawartości przechowywanej w głównych 10 katalogach. Każdy pasek wykresu reprezentuje katalog. Użyj schematu kodowania kolorów, aby dopasować pasek do katalogu podanego w sekcji Najpopularniejsze katalogi w pełnym 250.

Dane użyte do wygenerowania wykresu słupkowego mogą być wyświetlane poniżej. Dostępne są następujące informacje dotyczące każdego z najpopularniejszych katalogów 250: ścieżka względna, Łączna liczba trafień, procent trafień, ilość transferowanych danych (w gigabajtach) oraz procent transferowanych danych.

## <a name="by-browser"></a>Według przeglądarki
Raport za pomocą przeglądarki pozwala wyświetlić przeglądarki używane do żądania zawartości. Po wygenerowaniu tego typu raportu wykres kołowy będzie wskazywać procent żądań obsłużonych przez 10 najpopularniejszych przeglądarek.

### <a name="using-the-pie-chart"></a>Używanie wykresu kołowego
* Umieść kursor na wycinku wykresu kołowego, aby wyświetlić nazwę i wersję przeglądarki.
* Na potrzeby tego raportu każda unikatowa kombinacja przeglądarki/wersji jest traktowana jako inna przeglądarka.
* Wycinek o nazwie "Other" wskazuje procent żądań obsłużonych przez wszystkie inne przeglądarki i wersje.

Dane użyte do wygenerowania wykresu kołowego mogą być wyświetlane poniżej. Zostanie znaleziony typ przeglądarki/numer wersji, Łączna liczba trafień i procent trafień dla każdej z najpopularniejszych przeglądarek 250.

## <a name="by-referrer"></a>Według odwołującego
Raport za pomocą odwołującego umożliwia wyświetlenie najważniejszych odwołujących się do zawartości na wybranej platformie. Odwołujący wskazuje nazwę hosta, z którego zostało wygenerowane żądanie. Po wygenerowaniu tego typu raportu wykres słupkowy będzie wskazywać ilość żądania (tj. trafień) wygenerowaną przez 10 najlepszych odwołujących się.

Lewa strona wykresu (oś y) wskazuje łączną liczbę żądań, które zasób napotkał dla każdego obiektu odwołującego. Każdy pasek na wykresie reprezentuje przystawkę. Użyj schematu kodowania kolorów, aby dopasować pasek do elementu przywołującego na liście w górnej sekcji odwołującej 250.

Dane użyte do wygenerowania wykresu słupkowego mogą być wyświetlane poniżej. Znajduje się w nim adres URL, Łączna liczba trafień i procent trafień generowanych przez poszczególne odsprzedawców 250.

## <a name="by-download"></a>Pobierając
Raport pobieranie przez pobranie umożliwia przeanalizowanie wzorców pobierania dla najbardziej wymaganej zawartości. Góra raportu zawiera wykres słupkowy, który porównuje próby pobrania z zakończonymi pobraniami dla 10 najważniejszych zasobów. Każdy pasek jest kodowany kolorem w zależności od tego, czy jest to próba pobrania (niebieska) czy ukończone pobieranie (kolor zielony).

> [!NOTE]
> Na potrzeby tego raportu, adresy URL brzegów CNAME są konwertowane na odpowiadające im adresy URL sieci CDN. Pozwala to na dokładne określenie wszystkich statystyk skojarzonych z elementem zawartości, niezależnie od adresu URL sieci CDN lub rekordu CNAME używanego do żądania.
> 
> 

Lewa strona grafu (oś y) wskazuje nazwę pliku dla każdego z pierwszych 10 żądanych zasobów. Bezpośrednio poniżej wykresu (oś x) znajdują się etykiety wskazujące łączną liczbę prób pobrania/zakończenia.

Bezpośrednio poniżej wykresu słupkowego zostaną wyświetlone następujące informacje dotyczące górnego 250 żądanych zasobów: ścieżki względnej (w tym nazwy pliku), liczby prób pobrania do zakończenia, liczby przewidzianych żądań oraz wartości procentowej żądania, które spowodowało zakończenie pobierania.

> [!TIP]
> Nasza sieć CDN nie jest informowana przez klienta HTTP (tj. przeglądarki), gdy zasób został całkowicie pobrany. W związku z tym należy obliczyć, czy zasób został całkowicie pobrany według kodów stanu i żądań zakresu bajtów. Pierwszym zadaniem szukanym podczas obliczania tego obliczenia jest to, czy żądanie spowoduje utworzenie kodu stanu 200 OK. W takim przypadku należy sprawdzić żądania zakresu bajtów, aby zapewnić, że będą one obejmować cały element zawartości. Na koniec porównano ilość danych transferowanych do rozmiaru żądanego elementu zawartości. Jeśli transferowane dane są równe lub większe niż rozmiar pliku, a żądania zakresu bajtów są odpowiednie dla tego elementu zawartości, trafienie zostanie zliczane jako kompletne pobieranie.
> 
> Ze względu na interpretowanie natury tego raportu należy pamiętać o następujących kwestiach, które mogą zmienić spójność i dokładność tego raportu.
> 
> * Wzorców ruchu nie można dokładnie przewidzieć, gdy agenty użytkownika zadziałają inaczej. Może to spowodować zakończono pobieranie wyników, które są większe niż 100%.
> * Zasoby, które wykorzystują pobieranie progresywne HTTP, mogą nie być dokładnie reprezentowane przez ten raport. Jest to spowodowane tym, że użytkownicy szukają różnych pozycji w filmie wideo.
> 
> 

## <a name="by-404-errors"></a>Z błędami 404
Raport o błędach według 404 umożliwia zidentyfikowanie typu zawartości, która generuje największą liczbę kodów stanu 404 nie znaleziono. Góra raportu zawiera wykres słupkowy dla 10 najważniejszych zasobów, dla których został zwrócony kod stanu 404. Ten wykres słupkowy porównuje łączną liczbę żądań z żądaniami, które spowodowały, że nie znaleziono 404 kodu stanu dla tych zasobów. Każdy pasek jest kodowany kolorem. Żółty pasek służy do wskazywania, że żądanie spowodowało nieznalezienie kodu stanu 404. Czerwony pasek służy do wskazywania całkowitej liczby żądań dla elementu zawartości.

> [!NOTE]
> Na potrzeby tego raportu należy zwrócić uwagę na następujące kwestie:
> 
> * Trafienie reprezentuje dowolne żądanie dla zasobu niezależnie od kodu stanu.
> * Adresy URL rekordu CNAME są konwertowane na odpowiadające im adresy URL sieci CDN. Pozwala to na dokładne określenie wszystkich statystyk skojarzonych z elementem zawartości, niezależnie od adresu URL sieci CDN lub rekordu CNAME używanego do żądania.
> 
> 

Lewa strona grafu (oś y) wskazuje nazwę pliku dla każdego z pierwszych 10 żądanych zasobów, które spowodowały, że nie znaleziono kodu stanu 404. Bezpośrednio poniżej wykresu (oś x) znajdują się etykiety wskazujące całkowitą liczbę żądań i liczbę żądań, które spowodowały, że nie znaleziono kodu stanu 404.

Bezpośrednio poniżej wykresu słupkowego zostaną wyświetlone następujące informacje dotyczące górnego 250 żądanych zasobów: ścieżki względnej (w tym nazwy pliku), liczby żądań, które spowodowały, że nie znaleziono kodu stanu 404, Łączna liczba przypadków, w których zażądano zasobu, oraz procent żądań, które 404 spowodowały, że nie znaleziono kodu stanu.

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN](cdn-real-time-stats.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](./cdn-verizon-premium-rules-engine.md)
* [Analizowanie wydajności brzegowej](cdn-edge-performance.md)