---
title: Informacje o cenach — Application Gateway platformy Azure
description: W tym artykule opisano proces rozliczania dla usługi Azure Application Gateway i zapory aplikacji sieci Web dla obu jednostek SKU od 1 do v2
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96601615"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Informacje o cenach usługi Azure Application Gateway i zapory aplikacji sieci Web

>[!NOTE]
>Ceny przedstawione w tym artykule są przykładami i służą wyłącznie do celów ilustracyjnych. Aby uzyskać informacje o cenach zgodnie z Twoim regionem, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/application-gateway/).

Usługa Azure Application Gateway to rozwiązanie równoważenia obciążenia warstwy 7, które umożliwia skalowalne, wysoko dostępne i bezpieczne dostarczanie aplikacji sieci Web na platformie Azure.

Nie ma kosztów ponoszonych z góry ani kosztów zakończenia związanych z Application Gateway.
Opłaty są naliczane tylko za zasoby wstępnie udostępniane i wykorzystane w oparciu o rzeczywiste zużycie godzinowe. Koszty związane z Application Gateway są klasyfikowane do dwóch składników: stałych kosztów i kosztów zmiennych. Rzeczywiste koszty w poszczególnych składnikach różnią się w zależności od używanej jednostki SKU.

W tym artykule opisano koszty związane z poszczególnymi jednostkami SKU i zaleca się, aby użytkownicy korzystali z tego dokumentu w celu planowania kosztów związanych z Application Gateway platformy Azure i zarządzania nimi.

## <a name="v1-skus"></a>Jednostki SKU w wersji 1

Jednostki SKU w warstwie Standardowa Application Gateway i WAF V1 są rozliczane jako kombinacja:

* Stały koszt

    Koszt oparty na czasie określonego typu Application Gateway/WAF jest inicjowany i uruchamiany w celu przetwarzania żądań.
    Składnik kosztu stałego bierze pod uwagę następujące czynniki:
    * Warstwa standardowa Application Gateway lub WAF
    * Rozmiar — mały, średni & duży
    * Liczba wystąpień — liczba wystąpień do wdrożenia

    W przypadku N wystąpień koszty skojarzone będą N * kosztem jednego wystąpienia określonej warstwy (standardowa & WAF) & rozmiar (mały, średni & duże).

* Zmienny koszt

    Koszt na podstawie ilości danych przetworzonych przez Application Gateway/WAF. Do rozliczania są brane pod uwagę zarówno bajty żądania, jak i odpowiedzi przetwarzane przez Application Gateway.

Łączny koszt = koszt stały + koszt zmienny

### <a name="standard-application-gateway"></a>Standardowa Application Gateway

Koszt naliczania kosztu & zmienny będzie obliczany na podstawie typu Application Gateway.
W poniższej tabeli przedstawiono przykładowe ceny oparte na migawce cen Wschodnie stany USA i są przeznaczone tylko do celów informacyjnych.

#### <a name="fixed-cost-east-us-region-pricing"></a>Stały koszt (Cennik regionu Wschodnie stany USA)

|              Typ Application Gateway             |  Koszty ($/godz.)  |
| ------------------------------------------------- | ---------------|
|                     Mały                         |    $0,025      |
|                     Śred.                        |    $0,07       |
|                     Duży                         |    $0,32       |

Miesięczne oszacowania cen zostały wyznaczone dla 730 godzin użytkowania miesięcznie.

#### <a name="variable-cost-east-us-region-pricing"></a>Zmienny koszt (Cennik regionu Wschodnie stany USA)

|              Przetworzone dane             |  Mały ($/GB)  |  Średni ($/GB) |  Duże ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Pierwsze 10 TB/miesiąc                       |     $0,008     |      Bezpłatna      |     Bezpłatna      |
| Następne 30 TB (10 – 40 TB)/miesiąc             |     $0,008     |     $0,007     |     Bezpłatna      |
| Ponad 40 TB/miesiąc                        |     $0,008     |     $0,007     |     $0,0035   |

Aby uzyskać więcej informacji o cenach zgodnie z Twoim regionem, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/application-gateway/).

### <a name="waf-v1"></a>WAF V1

Koszt naliczania & kosztów zmiennych zostanie obliczony zgodnie z typem Application Gateway aprowizacji.

W poniższej tabeli przedstawiono przykładowe ceny na podstawie migawki cen Wschodnie stany USA i służą wyłącznie do celów informacyjnych.

#### <a name="fixed-cost-east-us-region-pricing"></a>Stały koszt (Cennik regionu Wschodnie stany USA)

|              Typ Application Gateway             |  Koszty ($/godz.)  |
| ------------------------------------------------- | ---------------|
|                     Mały                         |       NA       |
|                     Śred.                        |     $0,126     |
|                     Duży                         |     $0,448     |

Miesięczne oszacowania cen zostały wyznaczone dla 730 godzin użytkowania miesięcznie.

#### <a name="variable-cost-east-us-region-pricing"></a>Zmienny koszt (Cennik regionu Wschodnie stany USA)

|              Przetworzone dane             |  Mały ($/GB)  |  Średni ($/GB) |  Duże ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Pierwsze 10 TB/miesiąc                       |     $0,008     |      Bezpłatna      |     Bezpłatna      |
| Następne 30 TB (10 – 40 TB)/miesiąc             |     $0,008     |     $0,007     |     Bezpłatna      |
| Ponad 40 TB/miesiąc                        |     $0,008     |     $0,007     |     $0,0035   |

Aby uzyskać więcej informacji o cenach zgodnie z Twoim regionem, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Wychodzące transfery danych — w przypadku danych wychodzących z centrów danych platformy Azure z bram aplikacji zostaną naliczone standardowe [stawki za transfer danych](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Przykład 1 (a) — standardowa Application Gateway z 1 liczbą wystąpień

Załóżmy, że została zainicjowana standardowa Application Gateway typu średniego z 1 wystąpieniem i proces przetwarza 500 GB w ciągu miesiąca. Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

Stała cena = $0,07 * 730 (godz.) = $51,1 miesięczne oszacowania cen opierają się 730 na godzinach użytkowania miesięcznie.

Zmienne koszty = bezpłatna (średnia warstwa nie ma kosztów dla pierwszych 10 TB przetworzonych miesięcznie) całkowitych kosztów = $51,1 + 0 = $51,1 

> [!NOTE]
> Aby zapewnić obsługę scenariuszy wysokiej dostępności, wymagane jest skonfigurowanie co najmniej dwóch wystąpień dla jednostek SKU w wersji 1. Zobacz umowę [SLA dla Application Gateway](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Przykład 1 (b) — standardowa Application Gateway z liczbą wystąpień > 1

Załóżmy, że została wstępnie zainicjowana standardowa Application Gateway typu średniego z pięcioma wystąpieniami i przetwarza 500 GB w ciągu miesiąca. Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

Stała cena = 5 (liczba wystąpień) * $0,07 * 730 (godz.) = $255,5 miesięczne oszacowania cen opierają się 730 na godzinach użytkowania miesięcznie.

Zmienne koszty = bezpłatna (średnia warstwa nie ma kosztów dla pierwszych 10 TB przetworzonych miesięcznie) całkowitych kosztów = $255,5 + 0 = $255,5 

### <a name="example-2--waf-application-gateway"></a>Przykład 2 — WAF Application Gateway

Załóżmy, że Zainicjowano obsługę małego typu standardowego Application Gateway i duży typ WAF Application Gateway przez pierwsze 15 dni miesiąca. Mała Brama aplikacji przetwarza 15 TB w czasie trwania aktywności, a duża WAF Brama aplikacji przetwarza 100 TB w czasie trwania aktywności. Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób: 

###### <a name="small-instance-standard-application-gateway"></a>Application Gateway standardowa małych wystąpień

24 godziny * 15 dni = 360 godzin

Stała cena = $0,025 * 360 (godz.) = $9

Zmienne koszty = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

Łączna liczba kosztów: $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>Duże wystąpienie WAF Application Gateway
24 godziny * 15 dni = 360 godzin

Stała cena = $0,448 * 360 (godz.) = $161,28

Zmienne koszty = 60 * 1000 * $0.0035/GB = $210 (duża warstwa nie ma kosztów dla pierwszych 40 TB przetworzonych miesięcznie)

Łączna liczba kosztów: $161,28 + $210 = $371,28

## <a name="v2-skus"></a>Wersje SKU v2  

Jednostki SKU Application Gateway v2 i WAF v2 obsługują Skalowanie automatyczne i gwarantują wysoką dostępność.

### <a name="key-terms"></a>Najważniejsze terminy

##### <a name="capacity-unit"></a>Jednostka pojemności 
Jednostka pojemności to miara wykorzystania pojemności dla Application Gateway w wielu parametrach.

Jednostka o pojedynczej pojemności składa się z następujących parametrów:
* 2500 połączeń trwałych
* 2,22 – przepływność w MB/s
* 1 jednostka obliczeniowa

Jeśli którykolwiek z tych parametrów zostanie przekroczony, kolejne n jednostek wydajności są niezbędne, nawet jeśli pozostałe dwa parametry nie przekraczają limitów jednostki miary pojedynczej.
Parametr o największym wykorzystaniu spośród trzech powyższych wartości będzie używany wewnętrznie do obliczania jednostek wydajności, które są naliczane.

##### <a name="compute-unit"></a>Jednostka obliczeniowa
Jednostka obliczeniowa to miara zużywanej pojemności obliczeniowej. Czynniki wpływające na użycie jednostek obliczeniowych to połączenia protokołu TLS/s, obliczenia dotyczące ponownego zapisywania adresów URL i przetwarzania reguł WAF. Liczba żądań, które może obsłużyć jednostka obliczeniowa, zależy od różnych kryteriów, takich jak rozmiar klucza certyfikatu TLS, algorytm wymiany kluczy, ponowne zapisywanie nagłówka, a w przypadku WAF — rozmiar żądania przychodzącego.

Wskazówki dotyczące jednostek obliczeniowych:
* Standard_v2 — każda jednostka obliczeniowa jest w stanie około 50 połączeń na sekundę z certyfikatem klucza RSA 2048-bitowym.

* WAF_v2 — każda jednostka obliczeniowa może obsłużyć około 10 współbieżnych żądań na sekundę w przypadku 70-30% ruchu z 70% żądań mniejszych niż 2 KB GET/POST i pozostałych. Obecnie nie ma to wpływ na wydajność WAF.

##### <a name="instance-count"></a>Liczba wystąpień 
Wstępne Inicjowanie obsługi administracyjnej zasobów dla jednostek SKU Application Gateway v2 jest zdefiniowane w zakresie liczby wystąpień. Każde wystąpienie gwarantuje minimalną 10 jednostek wydajności pod względem możliwości przetwarzania. To samo wystąpienie może potencjalnie obsługiwać więcej niż 10 jednostek pojemności dla różnych wzorców ruchu w zależności od parametrów jednostki pojemności.

Ręcznie zdefiniowana skala i limity ustawione na potrzeby skalowania automatycznego (minimum lub maksimum) są ustawiane jako liczba wystąpień. Ręcznie ustawiona skala liczby wystąpień i minimalna liczba wystąpień w konfiguracji automatycznego skalowania będzie zarezerwować 10 jednostek wydajności/wystąpienia. Te zastrzeżone usługi CUs będą rozliczane tak długo, jak Application Gateway jest aktywny, niezależnie od rzeczywistego zużycia zasobów. Jeśli rzeczywiste zużycie przekracza 10 jednostek wydajności/progu wystąpienia, dodatkowe jednostki wydajności będą naliczane pod składnikiem zmiennej.

Jednostki SKU v2 są rozliczane na podstawie zużycia i składają się z dwóch części:

* Stałe koszty

    Koszt oparty na czasie, w którym Zainicjowano obsługę Application Gateway v2/WAF v2 i jest dostępny do przetwarzania żądań. Zapewnia to wysoką dostępność — nawet jeśli 0 wystąpień jest zarezerwowanych przez określenie 0 w minimalnej liczbie wystąpień w ramach skalowania automatycznego. 
    
    Koszt ustalony obejmuje również koszt związany z publicznym adresem IP dołączonym do Application Gateway.

    Liczba wystąpień uruchomionych w dowolnym momencie nie jest traktowana jako współczynnik dla stałych kosztów dla jednostek SKU w wersji 2. Stałe koszty uruchamiania Standard_V2 (lub WAF_V2) byłyby takie same na godzinę, niezależnie od liczby wystąpień uruchomionych w ramach tego samego regionu świadczenia usługi Azure.

* Koszty jednostkowe wydajności 

    Koszt oparty na liczbie jednostek pojemności, które są zarezerwowane lub wykorzystane, a także w przypadku przetwarzania żądań przychodzących.  Koszty oparte na zużyciu są obliczane co godzinę.

Łączne koszty = koszty stałe + koszty jednostki wydajności

> [!NOTE]
> Godzina częściowa jest rozliczana jako pełna godzina.

W poniższej tabeli przedstawiono przykładowe ceny na podstawie migawki cen Wschodnie stany USA i służą wyłącznie do celów informacyjnych.

#### <a name="fixed-costs-east-us-region-pricing"></a>Stałe koszty (Cennik regionu Wschodnie stany USA)

|              Jednostka SKU w wersji 2             |  Koszty ($/godz.)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0,246     |
|              WAF_V2             |     $0,443     |

Miesięczne oszacowania cen zostały wyznaczone dla 730 godzin użytkowania miesięcznie.

#### <a name="variable-costs-east-us-region-pricing"></a>Zmienne koszty (Cennik regionu Wschodnie stany USA)

|        Jednostka pojemności         |  Standard_V2 ($/godz.)  |  WAF_V2 ($/godz.) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        $0,008        |     $0,0144    |

Aby uzyskać więcej informacji o cenach zgodnie z Twoim regionem, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Wychodzące transfery danych — w przypadku danych wychodzących z centrów danych platformy Azure z bram aplikacji zostaną naliczone standardowe [stawki za transfer danych](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--manual-scaling"></a>Przykład 1 (a) — skalowanie ręczne 
Załóżmy, że Zainicjowano obsługę Standard_V2 Application Gateway z ręcznym skalowaniem ustawionym na 8 wystąpień przez cały miesiąc. W tym czasie otrzymujemy średnią liczbę transferów danych 88,8 MB/s.

Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

1 CU może obsłużyć przepływność 2,22-MB/s.

Wartość CUs wymagana do obsługi 88,8 MB/s = 88,8/2,22 = 40 CUs 

Wstępnie zainicjowana wartość CUs = 8 (liczba wystąpień) * 10 = 80 

Od 80 (pojemność zastrzeżona) > 40 (wymagana pojemność) nie są wymagane żadne dodatkowe możliwości. 

Stała cena = $0,246 * 730 (godz.) = $179,58

Zmienne koszty = $0,008 * 8 (jednostki wystąpień) * 10 (jednostki pojemności) * 730 (godz.) = $467,2

Łączna liczba kosztów: $179,58 + $467,2 = $646,78

![Diagram ręcznego skalowania 1.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Przykład 1 (b) — ręczne skalowanie z ruchem przechodzącym poza zainicjowaną pojemność

Załóżmy, że Zainicjowano obsługę Standard_V2 Application Gateway z ręcznym skalowaniem ustawionym na 3 wystąpienia przez cały miesiąc. W tym czasie otrzymujemy średnią liczbę transferów danych 88,8 MB/s.

Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

1 CU może obsłużyć przepływność 2,22 MB/s.

Wartość CUs wymagana do obsługi 88,8 MB/s = 88,8/2,22 = 40 

Wstępnie zainicjowana wartość CUs = 3 (liczba wystąpień) * 10 = 30 

Ponieważ 40 (wymagana pojemność) > 30 (zarezerwowana pojemność), wymagane są dodatkowe jednostki CUs.
Liczba dodatkowych użytych elementów konfiguracji będzie zależeć od wolnej pojemności dostępnej dla każdego wystąpienia.

Jeśli pojemność przetwarzania równoważna 10 dodatkowej operacji CUs była dostępna do użycia w ramach 3 wystąpień zarezerwowanych.

Stała cena = $0,246 * 730 (godz.) = $179,58

Zmienne koszty = $0,008 * (3 (jednostki wystąpień) * 10 (jednostki pojemności) + 10 (dodatkowe jednostki pojemności)) * 730 (godz.) = $233,6

Łączna liczba kosztów: $179,58 + $233,6 = $413,18

Jednak w przypadku, gdy pojemność przetwarzania równoważy tylko 7. dodatkowa funkcja CUs była dostępna do użycia w ramach 3 wystąpień zarezerwowanych.
W tym scenariuszu zasób Application Gateway podlega skalowaniu i może potencjalnie prowadzić do wzrostu opóźnień lub żądań porzucenia.

Stała cena = $0,246 * 730 (godz.) = $179,58

Zmienne koszty = $0,008 * (3 (jednostki wystąpień) * 10 (jednostki pojemności) + 7 (dodatkowe jednostki pojemności)) * 730 (godz.) = $216,08

Łączna liczba kosztów: $179,58 + $216,08 = $395,66


![Diagram ręcznego skalowania 2.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> W przypadku ręcznego skalowania wszelkie dodatkowe żądania przekraczające maksymalną moc obliczeniową wystąpień zarezerwowanych mogą spowodować wpływ na dostępność aplikacji. W przypadku dużego obciążenia wystąpienia zarezerwowane mogą zapewnić więcej niż 10 jednostek pojemności przetwarzania pojemności, w zależności od konfiguracji i typu żądań przychodzących. Jednak zaleca się zainicjowanie liczby wystąpień zgodnie z wymaganiami dotyczącymi ruchu.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Przykład 2 — wystąpienie WAF_V2 z skalowaniem automatycznym

Załóżmy, że Zainicjowano obsługę WAF_V2 z włączonym skalowaniem automatycznym i ustawimy minimalną liczbę wystąpień na 6 przez cały miesiąc. Załadowanie żądania spowodowało przeskalowanie w poziomie i 65 wykorzystanie jednostek wydajności WAF (skalowanie w poziomie do 5 jednostek wydajności, podczas gdy jednostki 60 zostały zarezerwowane) przez cały miesiąc.
Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

Miesięczne oszacowania cen zostały wyznaczone dla 730 godzin użytkowania miesięcznie.

Stała cena = $0,443 * 730 (godz.) = $323,39

Zmienne koszty = $0,0144 * 65 (jednostki pojemności) * 730 (godziny) = $683,28

Łączna liczba kosztów: $323,39 + $683,28 = $1006,67

![Diagram automatycznego skalowania 2.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Rzeczywisty ruch zaobserwowany dla Application Gateway prawdopodobnie nie będzie miał stałego wzorca ruchu, a zaobserwowane obciążenie Application Gateway może się różnić w zależności od rzeczywistego użycia.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Przykład 3 (a) — wystąpienie WAF_V2 z funkcją skalowania automatycznego i 0 min konfiguracja skalowania

Załóżmy, że Zainicjowano obsługę WAF_V2 z włączonym skalowaniem automatycznym i ustawiono minimalną liczbę wystąpień równą 0 przez cały miesiąc. Obciążenie żądaniami w WAF jest minimalne, ale spójne na godzinę przez cały miesiąc. Obciążenie jest mniejsze niż pojemność pojedynczej jednostki pojemności.
Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

Miesięczne oszacowania cen zostały wyznaczone dla 730 godzin użytkowania miesięcznie.

Stała cena = $0,443 * 730 (godz.) = $323,39

Zmienne koszty = $0,0144 * 1 (jednostki pojemności) * 730 (godz.) = $10,512

Łączna liczba kosztów: $323,39 + $10,512 = $333,902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Przykład 3 (b) — wystąpienie WAF_V2 z skalowaniem automatycznym z 0 min liczba wystąpień

Załóżmy, że Zainicjowano obsługę WAF_V2 z włączonym skalowaniem automatycznym i ustawimy minimalną liczbę wystąpień na 0 przez cały miesiąc. Istnieje jednak 0 ruch kierowany do wystąpienia WAF przez cały miesiąc.
Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

Stała cena = $0,443 * 730 (godz.) = $323,39

Zmienne koszty = $0,0144 * 0 (jednostki pojemności) * 730 (godziny) = $0

Łączna liczba kosztów: $323,39 + $0 = $323,39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Przykład 3 (C) — wystąpienie WAF_V2 ze skalowaniem ręcznym ustawionym na 1 wystąpienie

Załóżmy, że Zainicjowano obsługę WAF_V2 i ustawisz ją na ręczne skalowanie przy minimalnej akceptowalnej wartości 1 wystąpienia przez cały miesiąc. Istnieje jednak 0 ruchu kierowanego do WAF przez cały miesiąc.
Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

Miesięczne oszacowania cen zostały wyznaczone dla 730 godzin użytkowania miesięcznie.

Stała cena = $0,443 * 730 (godz.) = $323,39

Zmienne koszty = $0,0144 * 1 (liczba wystąpień) * 10 (jednostki pojemności) * 730 (godz.) = $105,12

Łączna liczba kosztów: $323,39 + $105,12 = $428,51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Przykład 4 — WAF_V2 z skalowaniem automatycznym, obliczenia jednostki pojemności

Załóżmy, że Zainicjowano obsługę WAF_V2 z włączonym skalowaniem automatycznym i ustawimy minimalną liczbę wystąpień na 0 przez cały miesiąc. W tym czasie otrzymujesz 25 nowych połączeń TLS/s z średnim transferem danych 8,88 MB/s.
Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

Miesięczne oszacowania cen zostały wyznaczone dla 730 godzin użytkowania miesięcznie.

Stała cena = $0,443 * 730 (godz.) = $323,39

Zmienne koszty = $0,0144 * 730 (godz.) * {Max (25/50, 8.88/2.22)} = $23,36 (4 jednostki pojemności wymagane do obsługi 8,88 MB/s)

Łączna liczba kosztów: $323,39 + $23,36 = $346,75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Przykład 5 (a) — Standard_V2 z skalowaniem automatycznym, obliczenia na podstawie czasu

Załóżmy, że Zainicjowano obsługę standard_V2 z włączonym skalowaniem automatycznym i ustawimy minimalną liczbę wystąpień na 0, a Brama aplikacji jest aktywna przez 2 godziny.
W pierwszej godzinie odbiera ruch, który może być obsługiwany przez 10 jednostek wydajności, a w ciągu drugiego czasu odbiera ruch, który wymaga 20 jednostek wydajności do obsługi obciążenia.
Koszty Application Gateway przy użyciu cen wymienionych powyżej zostałyby obliczone w następujący sposób:

Stała cena = $0,246 * 2 (godziny) = $0,492

Zmienne koszty = $0,008 * 10 (jednostki pojemności) * 1 (godz.) + $0,008 * 20 (jednostki pojemności) * 1 (godz.) = $0,24

Łączna liczba kosztów: $0,492 + $0,24 = $0,732


## <a name="monitoring-billed-usage"></a>Monitorowanie rozliczane użycie

Można wyświetlić ilość użycia dla różnych parametrów (jednostka obliczeniowa, przepływność & połączeń trwałych), a także jednostki pojemności używane jako część metryk Application Gateway w sekcji **monitorowanie** .

![Diagram sekcji metryk.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Przydatne metryki szacowania kosztów

* Jednostki bieżącej wydajności

    Liczba jednostek pojemności zużytych do równoważenia obciążenia ruchu przez trzy parametry — bieżące połączenia, przepływność i jednostka obliczeniowa

* Jednostki stałej wydajności rozliczanej

    Minimalna liczba jednostek pojemności obsługiwana zgodnie z ustawieniem minimalna liczba wystąpień (jedno wystąpienie jest tłumaczone na 10 jednostek pojemności) w konfiguracji Application Gateway.

* Szacowane rozliczane jednostki wydajności

    Szacowane rozliczane jednostki wydajności wskazują liczbę jednostek wydajności, na podstawie której szacowane jest rozliczenie. Oblicza się je na podstawie większej z dwóch wartości: liczby jednostek bieżącej wydajności (jednostek wydajności wymaganych do równoważenia obciążenia ruchu) oraz jednostek stałej wydajności rozliczanej (minimalnej aprowizowanej liczby jednostek wydajności).

Dodatkowe metryki, takie jak przepływność, bieżące połączenia i jednostki obliczeniowe, są również dostępne w celu zrozumienia wąskich gardeł i oszacowania liczby wymaganych jednostek pojemności. Szczegółowe informacje są dostępne na [Application Gateway metryki](application-gateway-metrics.md)

#### <a name="example---estimating-capacity-units-being-utilized"></a>Przykład — szacowanie używanych jednostek wydajności

**Obserwowane metryki:**

* Jednostki obliczeniowe = 17,38
* Przepływność = 1.37 M bajtów/s-10,96 MB/s
* Bieżące połączenia = 123.08 k
* Obliczone jednostki wydajności = Max (17.38, 10.96/2.22, 123.08 k/2500) = 49,232

Obserwowane jednostki wydajności w metrykach = 49,23

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat sposobu działania cen w usłudze Azure Application Gateway:

* [Strona cennika usługi Azure Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Kalkulator cen Application Gateway platformy Azure](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
