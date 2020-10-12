---
title: Analizowanie wydajności węzła brzegowego w Azure CDN | Microsoft Docs
description: Analizowanie wydajności węzłów brzegowych w Microsoft Azure sieci CDN. Usługa Edge wydajność Analytics zapewnia szczegółowe informacje o ruchu i przepustowości dla sieci CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: dc1599fc0c2f8c55c709ab674c10dd53c8d8dc04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887705"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analizowanie wydajności węzła brzegowego w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
Usługa Edge wydajność Analytics zapewnia szczegółowe informacje o ruchu i przepustowości dla sieci CDN. Te informacje mogą być następnie używane do generowania statystyk trendów, które umożliwiają uzyskanie wglądu w sposób buforowania i dostarczania zasobów do klientów. Dzięki temu można utworzyć strategię optymalizacji dostarczania zawartości i określić, jakie problemy należy zastosować, aby lepiej wykorzystać sieć CDN. W efekcie nie tylko będzie można poprawić wydajność dostarczania danych, ale będzie można również zmniejszyć koszty usługi CDN.

> [!NOTE]
> Wszystkie raporty używają notacji UTC/GMT podczas określania daty/godziny.
> 
> 

## <a name="reports-and-log-collection"></a>Raporty i zbieranie dzienników
Aby można było generować raporty na ten temat, dane działania sieci CDN muszą być zbierane przez moduł analiz wydajności programu Edge. Ten proces zbierania odbywa się raz dziennie i obejmuje aktywność, która miała miejsce w ciągu poprzedniego dnia. Oznacza to, że statystyki raportu przedstawiają przykład statystyk dnia w momencie przetworzenia i niekoniecznie zawierają pełny zestaw danych dla bieżącego dnia. Podstawową funkcją tych raportów jest Ocena wydajności. Nie powinny być używane na potrzeby rozliczeń ani dokładnej statystyki liczbowej.

> [!NOTE]
> Dane pierwotne, z których są generowane raporty analityczne wydajności Edge, są dostępne przez co najmniej 90 dni.
> 
> 

## <a name="dashboard"></a>Pulpit nawigacyjny
Pulpit nawigacyjny analizy wydajności brzegowej śledzi bieżący i historyczny ruch w sieci CDN za pomocą wykresu i statystyk. Ten pulpit nawigacyjny służy do wykrywania ostatnich i długoterminowych trendów związanych z wydajnością ruchu w sieci CDN dla konta.

Ten pulpit nawigacyjny składa się z:

* Interaktywny wykres, który umożliwia wizualizację najważniejszych metryk i trendów.
* Oś czasu, która stanowi sens długoterminowych wzorców dla najważniejszych metryk i trendów.
* Kluczowe metryki i informacje statystyczne na temat tego, jak nasza sieć CDN zwiększa ruch w lokacji mierzony przez ogólną wydajność, użycie i wydajność.

### <a name="accessing-the-edge-performance-dashboard"></a>Uzyskiwanie dostępu do pulpitu nawigacyjnego wydajności Edge
1. W bloku profil usługi CDN kliknij przycisk **Zarządzaj** .
   
    ![Przycisk zarządzania bloku profilu usługi CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.
2. Zatrzymaj wskaźnik myszy na karcie **Analiza** , a następnie umieść kursor nad menu wysuwanym **Analiza wydajności Edge** .  Kliknij pozycję **pulpit nawigacyjny**.
   
    Zostanie wyświetlony pulpit nawigacyjny analizy węzłów brzegowych.

### <a name="chart"></a>Wykres
Pulpit nawigacyjny zawiera wykres, który śledzi metrykę w określonym czasie wybranym na osi czasu wyświetlanej bezpośrednio poniżej.  Oś czasu, w której wykresy do ostatnich dwóch lat działania sieci CDN są wyświetlane bezpośrednio pod wykresem.

#### <a name="using-the-chart"></a>Używanie wykresu
* Domyślnie zostanie wyszukana stawka wydajności pamięci podręcznej z ostatnich 30 dni.
* Ten wykres jest generowany na podstawie danych sortowanych codziennie.
* Umieszczenie wskaźnika myszy na wykresie liniowym oznacza datę i wartość metryki dla tej daty.
* Kliknij pozycję Wyróżnij weekendy, aby włączyć nakładanie się szarych pionowych słupków, które reprezentują weekendy na wykresie. Ten typ nakładki jest przydatny do identyfikowania wzorców ruchu w weekendy.
* Kliknij pozycję Wyświetl rok temu, aby przełączyć nakładkę działania poprzedniego roku na wykres. Ten typ porównania zapewnia wgląd w długoterminowe wzorce użycia usługi CDN. Prawy górny róg wykresu zawiera legendę wskazującą kod koloru dla każdego wykresu liniowego.

#### <a name="updating-the-chart"></a>Aktualizowanie wykresu
* Zakres czasu: wykonaj jedną z następujących czynności:
  * Wybierz żądany region na osi czasu. Wykres zostanie zaktualizowany o dane odnoszące się do wybranego przedziału czasu.
  * Kliknij dwukrotnie wykres, aby wyświetlić wszystkie dostępne dane historyczne maksymalnie przez dwa lata.
* Metryka: kliknij ikonę wykresu widoczną obok żądanej metryki. Wykres i oś czasu zostaną odświeżone przy użyciu danych dla odpowiedniej metryki.

### <a name="key-metrics-and-statistics"></a>Kluczowe metryki i statystyki
#### <a name="efficiency-metrics"></a>Metryki wydajności
Te metryki mają na celu sprawdzenie, czy można zwiększyć wydajność pamięci podręcznej. Najważniejsze korzyści wynikające z wydajności pamięci podręcznej to:

* Zmniejszone obciążenie na serwerze źródłowym, co może prowadzić do:
  * Lepsza wydajność serwera sieci Web.
  * Zmniejszone koszty operacyjne.
* Ulepszone przyspieszenie dostarczania danych, ponieważ kolejne żądania będą obsługiwane bezpośrednio z sieci CDN.

| Pole | Opis |
| --- | --- |
| Wydajność pamięci podręcznej |Wskazuje procent transferu danych, który został obsłużony z pamięci podręcznej. Ta Metryka mierzy, kiedy buforowana wersja żądanej zawartości została obsłużona bezpośrednio z sieci CDN (serwerów brzegowych) do zleceniodawców (np. przeglądarka sieci Web). |
| Współczynnik trafień |Wskazuje procent żądań, które zostały obsłużone z pamięci podręcznej. Ta Metryka mierzy, kiedy buforowana wersja żądanej zawartości została obsłużona bezpośrednio z sieci CDN (serwerów brzegowych) do zleceniodawców (np. przeglądarka sieci Web). |
| % Bajtów zdalnych — brak konfiguracji pamięci podręcznej |Wskazuje procent ruchu, który został obsłużony z serwerów pochodzenia do sieci CDN (serwerów brzegowych), które nie będą buforowane w wyniku funkcji pomijania pamięci podręcznej (aparat reguł HTTP). |
| % liczby bajtów zdalnych — wygasła pamięć podręczna |Wskazuje procent ruchu, który został obsłużony z serwerów pochodzenia do sieci CDN (serwery brzegowe) w wyniku nieodświeżonej ponownej weryfikacji zawartości. |

#### <a name="usage-metrics"></a>Metryki użycia
Celem tych metryk jest zapewnienie wglądu w następujące miary dotyczące kosztów wycinania:

* Minimalizacja kosztów operacyjnych przez sieć CDN.
* Zmniejszenie wydatków usługi CDN dzięki wydajności pamięci podręcznej i kompresji.

> [!NOTE]
> Numery woluminów ruchu reprezentują ruch, który był używany w obliczeniach współczynników i wartości procentowych, i może zawierać tylko część całkowitego ruchu dla klientów o dużej pojemności.
> 
> 

| Pole | Opis |
| --- | --- |
| Wypełnianie bajtów |Wskazuje średnią liczbę bajtów przesłanych dla każdego żądania obsługiwanego z sieci CDN (serwerów brzegowych) do osoby żądającej (np. przeglądarki sieci Web). |
| Brak szybkości bajtów konfiguracji pamięci podręcznej |Wskazuje procent ruchu obsługiwanego z sieci CDN (serwery brzegowe) do osoby żądającej (np. przeglądarki sieci Web), która nie będzie buforowana z powodu funkcji pomijania pamięci podręcznej. |
| Szybkość kompresji bajtów |Wskazuje procent ruchu wysyłanego z sieci CDN (serwery brzegowe) do osoby żądającej (np. przeglądarki sieci Web) w formacie skompresowanym. |
| Bajtów out |Wskazuje ilość danych (w bajtach) dostarczonych z sieci CDN (serwerów brzegowych) do osoby żądającej (np. przeglądarki sieci Web). |
| Bajty w |Wskazuje ilość danych (w bajtach) wysyłanych z zleceniodawców (np. przeglądarka sieci Web) do usługi CDN (serwery brzegowe). |
| Bajty zdalne |Wskazuje ilość danych (w bajtach) wysyłanych z serwerów sieci CDN i klientów do sieci CDN (serwerów brzegowych). |

#### <a name="performance-metrics"></a>Metryki wydajności
Celem tych metryk jest śledzenie ogólnej wydajności sieci CDN dla ruchu.

| Pole | Opis |
| --- | --- |
| Szybkość transferu |Wskazuje średnią szybkość, z jaką zawartość została przeniesiona z sieci CDN do osoby żądającej. |
| Czas trwania |Wskazuje średni czas (w milisekundach), jaki zajęło dostarczenie zasobu do zleceniodawcy (np. przeglądarki sieci Web). |
| Szybkość żądania kompresji |Wskazuje procent trafień, które zostały dostarczone z sieci CDN (serwery brzegowe) do osoby żądającej (np. przeglądarki sieci Web) w formacie skompresowanym. |
| Współczynnik błędów 4xx |Wskazuje procent trafień, które wygenerowały kod stanu 4xx. |
| Współczynnik błędów 5xx |Wskazuje procent trafień, które wygenerowały kod stanu 5xx. |
| Trafienia |Wskazuje liczbę żądań dotyczących zawartości sieci CDN. |

#### <a name="secure-traffic-metrics"></a>Metryki bezpiecznego ruchu
Celem tych metryk jest śledzenie wydajności sieci CDN dla ruchu HTTPS.

| Pole | Opis |
| --- | --- |
| Wydajność bezpiecznej pamięci podręcznej |Wskazuje procent danych przesłanych dla żądań HTTPS, które zostały obsłużone z pamięci podręcznej. Ta Metryka mierzy, kiedy buforowana wersja żądanej zawartości została obsłużona bezpośrednio z sieci CDN (serwerów brzegowych) do zleceniodawców (np. przeglądarka sieci Web) za pośrednictwem protokołu HTTPS. |
| Szybkość bezpiecznego transferu |Wskazuje średnią szybkość przesyłania zawartości z sieci CDN (serwerów brzegowych) do osoby żądającej (np. serwerów sieci Web) za pośrednictwem protokołu HTTPS. |
| Średni bezpieczny czas trwania |Wskazuje średni czas (w milisekundach), jaki zajęło dostarczenie zasobu do żądającego (np. przeglądarki sieci Web) za pośrednictwem protokołu HTTPS. |
| Bezpieczne trafienia |Wskazuje liczbę żądań HTTPS dla zawartości usługi CDN. |
| Bezpieczne bajty wychodzące |Wskazuje ilość ruchu HTTPS w bajtach, która została dostarczona z sieci CDN (serwerów brzegowych) do osoby żądającej (np. przeglądarki sieci Web). |

## <a name="reports"></a>Raporty
Każdy raport w tym module zawiera wykres i statystyki dotyczące użycia przepustowości i ruchu dla różnych typów metryk (np. kody stanu HTTP, kody stanu pamięci podręcznej, adres URL żądania itp.). Te informacje mogą być używane do bardziej szczegółowych informacji na temat sposobu dostarczania zawartości do klientów i dostrajania zachowania usługi CDN w celu zwiększenia wydajności tworzenia danych.

### <a name="accessing-the-edge-performance-reports"></a>Uzyskiwanie dostępu do raportów o wydajności brzegowej
1. W bloku profil usługi CDN kliknij przycisk **Zarządzaj** .
   
    ![Przycisk zarządzania bloku profilu usługi CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.
2. Zatrzymaj wskaźnik myszy na karcie **Analiza** , a następnie umieść kursor nad menu wysuwanym **Analiza wydajności Edge** .  Kliknij opcję **duży obiekt http**.
   
    Zostanie wyświetlony ekran Raporty analizy węzłów brzegowych.

| Raport | Opis |
| --- | --- |
| Podsumowanie dzienne |Umożliwia wyświetlanie codziennych trendów ruchu w określonym przedziale czasu. Każdy pasek na tym grafie reprezentuje konkretną datę. Rozmiar słupka wskazuje całkowitą liczbę trafień, które wystąpiły w tym dniu. |
| Podsumowanie godzinowe |Umożliwia wyświetlanie trendów ruchu godzinowego w określonym przedziale czasu. Każdy pasek na tym grafie reprezentuje godzinę w określonym dniu. Rozmiar paska wskazuje całkowitą liczbę trafień, które wystąpiły w ciągu tej godziny. |
| Protokoły |Przedstawia podział ruchu między protokołami HTTP i HTTPS. Wykres pierścieniowy wskazuje procent trafień, które wystąpiły dla każdego typu protokołu. |
| Metody HTTP |Umożliwia szybkie wykrywanie metod protokołu HTTP używanych do żądania danych. Zazwyczaj najczęstszymi metodami żądania HTTP są GET, głowy i POST. Wykres pierścieniowy wskazuje procent trafień, które wystąpiły dla każdego typu metody żądania HTTP. |
| Adresy URL |Zawiera wykres przedstawiający 10 pierwszych żądanych adresów URL. Wyświetlany jest pasek dla każdego adresu URL. Wysokość paska wskazuje liczbę trafień generowanych przez określony adres URL w przedziale czasu, który jest objęty raportem. Statystyki dla najpopularniejszych adresów URL 100 są wyświetlane bezpośrednio poniżej tego grafu. |
| Rekordy CNAME |Zawiera wykres, który wyświetla 10 rekordów CNAME używanych do żądania zasobów w przedziale czasu raportu. Statystyki dla najpopularniejszych 100 żądanych rekordów CNAME są wyświetlane bezpośrednio poniżej tego grafu. |
| Źródła |Zawiera wykres przedstawiający 10 najważniejszych serwerów sieci CDN lub klientów, z których żądano zasobów w określonym przedziale czasu. Dane statystyczne dla najważniejszych 100 żądanych serwerów sieci CDN lub klientów pochodzenia są wyświetlane bezpośrednio poniżej tego grafu. Serwery pochodzenia klientów są identyfikowane za pomocą nazwy zdefiniowanej w opcji Nazwa katalogu. |
| Geograficzne punkty obecności |Pokazuje, jaka część ruchu sieciowego jest kierowana przez konkretny punkt obecności (POP). Trzyliterowy skrót reprezentuje punkt obecności w sieci CDN. |
| Klienci |Zawiera wykres przedstawiający 10 najważniejszych klientów, którzy żądają zasobów w określonym przedziale czasu. Na potrzeby tego raportu wszystkie żądania pochodzące z tego samego adresu IP są uważane za z tego samego klienta. Statystyki dla najważniejszych klientów 100 są wyświetlane bezpośrednio poniżej tego grafu. Ten raport jest przydatny do określania wzorców działania pobierania dla najważniejszych klientów. |
| Stany pamięci podręcznej |Zawiera szczegółowy podział zachowania pamięci podręcznej, które może ujawnić podejścia do poprawy ogólnego środowiska użytkownika końcowego. Ze względu na to, że najszybsza wydajność jest większa od trafień pamięci podręcznej, można zoptymalizować szybkość dostarczania danych przez zminimalizowanie chybień pamięci podręcznej i wygasłych |
| Brak szczegółów |Zawiera wykres przedstawiający 10 najważniejszych adresów URL dla zasobów, dla których świeżość zawartości pamięci podręcznej nie została sprawdzona w określonym przedziale czasu. Statystyki dla najpopularniejszych adresów URL 100 dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły CONFIG_NOCACHE |Zawiera wykres przedstawiający 10 najważniejszych adresów URL dla zasobów, które nie zostały zapisane w pamięci podręcznej ze względu na konfigurację sieci CDN klienta. Te typy zasobów zostały obsłużone bezpośrednio z serwera pochodzenia. Statystyki dla najpopularniejszych adresów URL 100 dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły niebuforowane |Zawiera wykres przedstawiający 10 najpopularniejszych adresów URL dla zasobów, których nie można było przetworzyć w pamięci podręcznej ze względu na dane nagłówka żądania. Statystyki dla najpopularniejszych adresów URL 100 dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_HIT |Zawiera wykres przedstawiający 10 najpopularniejszych adresów URL dla zasobów, które są obsługiwane bezpośrednio z pamięci podręcznej. Statystyki dla najpopularniejszych adresów URL 100 dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_MISS |Zawiera wykres przedstawiający 10 najważniejszych adresów URL dla zasobów, które mają stan pamięci podręcznej TCP_MISS. Statystyki dla najpopularniejszych adresów URL 100 dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_EXPIRED_HIT |Zawiera wykres przedstawiający 10 najpopularniejszych adresów URL dla starych zasobów, które zostały obsłużone bezpośrednio z punktu obecności. Statystyki dla najpopularniejszych adresów URL 100 dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_EXPIRED_MISS |Zawiera wykres przedstawiający 10 najpopularniejszych adresów URL dla starych zasobów, dla których należy pobrać nową wersję z serwera pochodzenia. Statystyki dla najpopularniejszych adresów URL 100 dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_CLIENT_REFRESH_MISS |Zawiera wykres słupkowy przedstawiający 10 najważniejszych adresów URL dla elementów zawartości pobranych z serwera pochodzenia z powodu żądania braku pamięci podręcznej od klienta. Statystyki dla najpopularniejszych adresów URL 100 dla tego typu żądań są wyświetlane bezpośrednio poniżej tego wykresu. |
| Typy żądań klienta |Wskazuje typ żądań wykonanych przez klientów HTTP (np. przeglądarki). Ten raport zawiera wykres pierścieniowy, który stanowi sens, w jaki sposób są obsługiwane żądania. Informacje o przepustowości i ruchu dla każdego typu żądania są wyświetlane poniżej wykresu. |
| Agent użytkownika |Zawiera wykres słupkowy przedstawiający 10 najlepszych agentów użytkowników, którzy zażądają zawartości za pomocą naszej sieci CDN. Zazwyczaj agent użytkownika to przeglądarka sieci Web, odtwarzacz multimedialny lub przeglądarka telefonu komórkowego. Statystyki dla najważniejszych agentów użytkownika 100 są wyświetlane bezpośrednio poniżej tego wykresu. |
| Przywołujących |Zawiera wykres słupkowy przedstawiający 10 najważniejszych odsprzedawców do zawartości, do której uzyskuje się dostęp za pomocą naszej sieci CDN. Zazwyczaj odwołujący jest adres URL strony sieci Web lub zasobu, który łączy się z zawartością. Szczegółowe informacje znajdują się poniżej wykresu dla pierwszych odsprzedawców 100. |
| Typy kompresji |Zawiera wykres pierścieniowy, który dzieli żądane zasoby na podstawie tego, czy zostały one skompresowane przez serwery brzegowe. Wartość procentowa skompresowanych zasobów jest dzielona na typ używanej kompresji. Szczegółowe informacje znajdują się poniżej wykresu dla każdego typu i stanu kompresji. |
| Typy plików |Zawiera wykres słupkowy, który wyświetla 10 najważniejszych typów plików, których zażądano za pomocą usługi CDN dla Twojego konta. Na potrzeby tego raportu typ pliku jest definiowany przez rozszerzenie nazwy pliku zasobu i typ nośnika internetowego (np. html \[ Text/HTML \] ,. htm \[ Text/HTML \] ,. aspx \[ Text/HTML \] itp.). Szczegółowe informacje znajdują się poniżej grafu dla najpopularniejszych typów plików 100. |
| Unikatowe pliki |Zawiera wykres przedstawiający łączną liczbę unikatowych zasobów, które zażądały w określonym dniu w określonym przedziale czasu. |
| Podsumowanie uwierzytelniania tokenu |Zawiera wykres kołowy, który zawiera krótkie omówienie tego, czy żądane zasoby były chronione za pomocą uwierzytelniania Token-Based. Chronione zasoby są wyświetlane na wykresie zgodnie z wynikami podejmowanych prób uwierzytelnienia. |
| Szczegóły Odmów autoryzacji tokenu |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 najważniejszych żądań, które zostały odrzucone z powodu Token-Based uwierzytelniania. |
| Kody odpowiedzi HTTP |Zawiera podział kodów stanu HTTP (np. 200 OK, 403 zabronione, nie znaleziono 404 itp.), które zostały dostarczone do klientów HTTP przez serwery brzegowe. Wykres kołowy umożliwia szybkie ocenianie sposobu, w jaki zostały obsłużone zasoby. Szczegółowe dane statystyczne są dostępne dla każdego kodu odpowiedzi poniżej grafu. |
| błędy 404 |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 najważniejszych żądań, które spowodowały, że nie znaleziono 404 kodu odpowiedzi. |
| Błędy 403 |Zawiera wykres słupkowy, który umożliwia wyświetlenie 10 najważniejszych żądań, które spowodowały niedozwolony kod odpowiedzi 403. Niedozwolony kod odpowiedzi 403 występuje, gdy żądanie jest odrzucane przez serwer pochodzenia klienta lub serwer graniczny w naszym wyskakującym okienku. |
| Błędy 4xx |Zawiera wykres słupkowy, który umożliwia wyświetlenie 10 pierwszych żądań, które spowodowały kod odpowiedzi w zakresie 400. Wykluczone z tego raportu to 403 nie znaleziono i 404 niedozwolonych kodów odpowiedzi. Zazwyczaj kod odpowiedzi 4xx występuje, gdy żądanie jest odrzucane w wyniku błędu klienta. |
| Błędy 504 |Zawiera wykres słupkowy, który umożliwia wyświetlenie 10 najważniejszych żądań, które spowodowały przekroczenie limitu czasu odpowiedzi bramy 504. Kod odpowiedzi z przekroczeniem limitu czasu bramy 504 występuje po upływie limitu czasu, gdy serwer proxy HTTP próbuje komunikować się z innym serwerem. W przypadku naszej sieci CDN kod odpowiedzi na limit czasu bramy 504 zwykle występuje, gdy serwer graniczny nie może nawiązać komunikacji z serwerem pochodzenia klienta. |
| Błędy 502 |Zawiera wykres słupkowy, który umożliwia wyświetlenie 10 najważniejszych żądań, które spowodowały niewłaściwy kod odpowiedzi bramy 502. 502 nieprawidłowy kod odpowiedzi bramy występuje, gdy wystąpi awaria protokołu HTTP między serwerem i serwerem proxy HTTP. W przypadku naszej sieci CDN niewłaściwy kod odpowiedzi bramy 502 zwykle występuje, gdy serwer pochodzenia klienta zwróci nieprawidłową odpowiedź na serwer graniczny. Odpowiedź jest nieprawidłowa, jeśli nie można jej przeanalizować lub jeśli jest niepełna. |
| Błędy 5xx |Zawiera wykres słupkowy, który umożliwia wyświetlenie 10 pierwszych żądań, które spowodowały kod odpowiedzi w zakresie 500.  Wykluczone z tego raportu to 502 niewłaściwe kody odpowiedzi bramy i bramy 504. |

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN](cdn-real-time-stats.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)

