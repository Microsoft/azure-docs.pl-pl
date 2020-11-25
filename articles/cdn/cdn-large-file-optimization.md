---
title: Optymalizacja pobierania dużych plików przy użyciu Azure CDN
description: Dowiedz się, jak duże ilości plików można zoptymalizować na platformie Azure Content Delivery Network. Ten artykuł zawiera kilka scenariuszy.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: ed5768e89482d32bb140e9ba7064de2d20809892
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020725"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optymalizacja pobierania dużych plików przy użyciu Azure CDN

Rozmiary plików zawartości dostarczonej przez Internet nadal rośnie ze względu na rozszerzoną funkcjonalność, ulepszoną grafikę i zaawansowaną zawartość multimedialną. Ten wzrost jest spowodowany przez wiele czynników: Penetracja szerokopasmowa, większe niedrogie urządzenia magazynujące, rozległy wzrost wideo o wysokiej rozdzielczości i urządzenia połączone z Internetem (IoT). Szybki i wydajny mechanizm dostarczania dużych plików ma kluczowe znaczenie dla zapewnienia bezproblemowego i przyjemnego środowiska użytkownika.

Dostarczanie dużych plików ma kilka wyzwań. Najpierw średni czas pobierania dużego pliku może być znaczący, ponieważ aplikacje mogą nie pobierać wszystkich danych sekwencyjnie. W niektórych przypadkach aplikacje mogą pobrać ostatnią część pliku przed pierwszą częścią. Gdy wymagana jest tylko niewielka ilość pliku lub użytkownik wstrzyma pobieranie, pobieranie może zakończyć się niepowodzeniem. Pobieranie może być również opóźnione do momentu, gdy usługa Content Delivery Network (CDN) pobierze cały plik z serwera pochodzenia. 

Po drugie opóźnienie między komputerem użytkownika a plikiem określa szybkość, z jaką mogą wyświetlać zawartość. Ponadto ograniczenia przepustowości sieci i pojemności wpływają również na przepływność. Większa odległość między serwerami i użytkownikami powoduje utworzenie dodatkowych możliwości utraty pakietów, co pozwala na zmniejszenie jakości. Zmniejszenie jakości spowodowane ograniczoną przepływność i zwiększoną utratą pakietów może zwiększyć czas oczekiwania na zakończenie pobierania pliku. 

Po trzecie wiele dużych plików nie jest dostarczanych w całości. Użytkownicy mogą anulować pobieranie w połowie lub obserwować tylko pierwsze kilka minut długiego wideo MP4. W związku z tym firmy i oprogramowanie do dostarczania multimediów chcą dostarczyć tylko część żądanego pliku. Wydajna dystrybucja żądanych części zmniejsza ruch wychodzący z serwera pochodzenia. Wydajna dystrybucja zmniejsza również obciążenie pamięci i operacji we/wy na serwerze źródłowym. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optymalizuj pod kątem dostarczania dużych plików Azure CDN od firmy Microsoft

**Standard Azure CDN from Microsoft** Endpoints dostarcza duże pliki bez limitu rozmiaru pliku. Dodatkowe funkcje są domyślnie włączone, aby zapewnić szybsze dostarczanie dużych plików.

### <a name="object-chunking"></a>Dzielenie obiektów 

**Standard Azure CDN firmy Microsoft korzysta z** techniki nazywanej fragmentacją obiektów. Po zażądaniu dużego pliku Usługa CDN pobiera mniejsze fragmenty pliku ze źródła. Po odebraniu przez serwer POP usługi CDN pełnych lub jednobajtowych żądań pliku serwer brzegowy usługi CDN żąda pliku od źródła w fragmentach 8 MB. 

Po nadejściu fragmentu do krawędzi sieci CDN jest on buforowany i natychmiast obsługiwany przez użytkownika. Usługa CDN ponownie pobiera Następny fragment równolegle. Ten pobrany z wyprzedzeniem zapewnia, że zawartość pozostaje jednego fragmentu przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu pobrania całego pliku (jeśli będzie to wymagane), wszystkie zakresy bajtów są dostępne (jeśli jest to wymagane) lub klient przerwał połączenie. 

Aby uzyskać więcej informacji na temat żądania zakresu bajtów, zobacz [RFC 7233](https://tools.ietf.org/html/rfc7233).

Usługa CDN buforuje wszystkie fragmenty, gdy są odbierane. Cały plik nie musi być buforowany w pamięci podręcznej usługi CDN. Kolejne żądania dla plików lub zakresów bajtów są obsługiwane z pamięci podręcznej usługi CDN. Jeśli nie wszystkie fragmenty są buforowane w sieci CDN, pobieranie z wyprzedzeniem jest używane do żądania fragmentów z pochodzenia. Ta optymalizacja polega na umożliwieniu serwerowi pochodzenia obsługi żądań o zakresie bajtów; Jeśli serwer pochodzenia nie obsługuje żądań o zakresie bajtów, żądania pobrania danych o rozmiarze większym niż 8 MB zakończą się niepowodzeniem. 

### <a name="conditions-for-large-file-optimization"></a>Warunki optymalizacji dużych plików
Nie ma ograniczeń dotyczących maksymalnego rozmiaru pliku.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optymalizuj pod kątem dostarczania dużych plików Azure CDN z Verizon

**Azure CDN Standard from Verizon** i **Azure CDN Premium z** punktów końcowych Verizon dostarcza dużych plików bez limitu rozmiaru pliku. Dodatkowe funkcje są domyślnie włączone, aby zapewnić szybsze dostarczanie dużych plików.

### <a name="complete-cache-fill"></a>Wypełnienie kompletnej pamięci podręcznej

Domyślnie pełna funkcja wypełnienia pamięci podręcznej umożliwia usłudze CDN ściąganie plików do pamięci podręcznej w przypadku porzucania lub utraty początkowego żądania. 

Pełne wypełnienie pamięci podręcznej jest najbardziej przydatne w przypadku dużych zasobów. Zazwyczaj użytkownicy nie pobierają ich od początku do końca. Używają one pobierania progresywnego. Domyślne zachowanie powoduje, że serwer graniczny zainicjuje pobieranie elementu zawartości w tle z serwera pochodzenia. Następnie zasób znajduje się w lokalnej pamięci podręcznej serwera brzegowego. Gdy pełny obiekt znajduje się w pamięci podręcznej, serwer graniczny spełnia żądania zakresu bajtów do sieci CDN dla obiektu w pamięci podręcznej.

Zachowanie domyślne można wyłączyć za pomocą aparatu reguł w **Azure CDN Premium z Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Umieszczanie w pamięci podręcznej równorzędnego wypełniania

Domyślna funkcja wypełniania funkcji umieszczania na gorąco przy użyciu zaawansowanego algorytmu równorzędnego. Korzysta ona z dodatkowych serwerów pamięci podręcznej, opartych na metrykach i agregowaniu żądań do realizacji żądań klientów dla dużych, wysoce popularnych obiektów. Ta funkcja zapobiega sytuacji, w której wiele dodatkowych żądań jest wysyłanych do serwera pochodzenia użytkownika. 

### <a name="conditions-for-large-file-optimization"></a>Warunki optymalizacji dużych plików

Funkcje optymalizacji dużych plików dla **standardu Azure CDN z Verizon** i **Azure CDN Premium z Verizon** są domyślnie włączone w przypadku korzystania z ogólnego typu optymalizacji dostarczania w sieci Web. Nie ma ograniczeń dotyczących maksymalnego rozmiaru pliku. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optymalizuj pod kątem dostarczania dużych plików przy użyciu standardu Azure CDN Akamai

Punkty końcowe **Azure CDN profilu Akamai** oferują funkcję, która dostarcza użytkownikom duże pliki w odpowiedniej skali. Funkcja zmniejsza opóźnienia, ponieważ zmniejsza obciążenie serwerów pochodzenia.

Funkcja typ optymalizacji dużych plików włącza optymalizacje i konfiguracje sieci w celu szybszego i bardziej niezawodnego dostarczania dużych plików. Ogólne dostarczanie w sieci Web przy użyciu **standardu Azure CDN z** punktów końcowych Akamai w pamięci podręcznej plików tylko poniżej 1,8 GB i może być tunelowane (nie pamięci podręcznej) do 150 GB. Optymalizacja dużego pliku buforuje pliki do 150 GB.

Optymalizacja dużych plików jest skuteczna w przypadku spełnienia określonych warunków. Warunki obejmują sposób działania serwera pochodzenia oraz rozmiary i typy wymaganych plików. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurowanie punktu końcowego usługi Akamai CDN w celu zoptymalizowania dostarczania dużych plików

Aby zoptymalizować dostarczanie dużych plików za pośrednictwem Azure Portal, można skonfigurować **Standard Azure CDN z** punktu końcowego Akamai. Do tego celu można również użyć interfejsów API REST lub dowolnego z zestawów SDK klienta. Poniższe kroki pokazują proces za pośrednictwem Azure Portal w przypadku **standardowego Azure CDN profilu Akamai** :

1. Aby dodać nowy punkt końcowy, na stronie Akamai **profil usługi CDN** wybierz pozycję **Endpoint (punkt końcowy**).

    ![Nowy punkt końcowy](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Z listy rozwijanej **zoptymalizowane dla** wybierz opcję **pobieranie dużych plików**.

    ![Wybrano optymalizację dużych plików](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Po utworzeniu punktu końcowego usługi CDN stosowane są optymalizacje dużych plików dla wszystkich plików spełniających określone kryteria. W poniższej sekcji opisano ten proces.

### <a name="object-chunking"></a>Dzielenie obiektów 

Optymalizacja dużych plików ze **standardem Azure CDN from Akamai** korzysta z techniki nazywanej fragmentacją obiektów. Po zażądaniu dużego pliku Usługa CDN pobiera mniejsze fragmenty pliku ze źródła. Po odebraniu przez serwer POP w sieci CDN pełnych lub jednobajtowych żądań plik sprawdza, czy typ pliku jest obsługiwany dla tej optymalizacji. Sprawdza również, czy typ pliku spełnia wymagania dotyczące rozmiaru pliku. Jeśli rozmiar pliku jest większy niż 10 MB, serwer brzegowy usługi CDN żąda pliku od źródła w fragmentach 2 MB. 

Po nadejściu fragmentu do krawędzi sieci CDN jest on buforowany i natychmiast obsługiwany przez użytkownika. Usługa CDN ponownie pobiera Następny fragment równolegle. Ten pobrany z wyprzedzeniem zapewnia, że zawartość pozostaje jednego fragmentu przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu pobrania całego pliku (jeśli będzie to wymagane), wszystkie zakresy bajtów są dostępne (jeśli jest to wymagane) lub klient przerwał połączenie. 

Aby uzyskać więcej informacji na temat żądania zakresu bajtów, zobacz [RFC 7233](https://tools.ietf.org/html/rfc7233).

Usługa CDN buforuje wszystkie fragmenty, gdy są odbierane. Cały plik nie musi być buforowany w pamięci podręcznej usługi CDN. Kolejne żądania dla plików lub zakresów bajtów są obsługiwane z pamięci podręcznej usługi CDN. Jeśli nie wszystkie fragmenty są buforowane w sieci CDN, pobieranie z wyprzedzeniem jest używane do żądania fragmentów z pochodzenia. Ta optymalizacja polega na umożliwieniu serwerowi pochodzenia obsługi żądań o zakresie bajtów; Jeśli serwer pochodzenia nie obsługuje żądań zakresu bajtów, Ta optymalizacja nie jest skuteczna.

### <a name="caching"></a>Buforowanie
Optymalizacja dużych plików używa różnych domyślnych czasów wygaśnięcia buforowania z ogólnego dostarczania w sieci Web. Różni się między buforem dodatnim i negatywnym buforowaniem na podstawie kodów odpowiedzi HTTP. Jeśli serwer pierwotny określa czas wygaśnięcia za pośrednictwem w odpowiedzi w pamięci podręcznej lub nagłówek wygaśnięcia, Usługa CDN przestrzega tej wartości. Gdy punkt początkowy nie zostanie określony, a plik jest zgodny z warunkiem typu i rozmiaru dla tego typu optymalizacji, Sieć CDN używa wartości domyślnych dla optymalizacji dużych plików. W przeciwnym razie sieć CDN używa ustawień domyślnych dla ogólnego dostarczania w sieci Web.

| Buforowanie  | Ogólne sieci Web | Optymalizacja dużych plików 
--- | --- | --- 
Buforowanie: dodatnie <br> HTTP 200, 203, 300, <br> 301, 302 i 410 | 7 dni |1 dzień  
Buforowanie: ujemne <br> HTTP 204, 305, 404, <br> i 405 | Brak | 1 sekunda 

### <a name="deal-with-origin-failure"></a>Postępowanie z niepowodzeniem pochodzenia

Długość przekroczenia limitu czasu odczytu jest zwiększana z dwóch sekund w przypadku dostarczania w sieci Web do dwóch minut dla typu optymalizacji dużych plików. Zwiększa to konta większych rozmiarów plików, aby uniknąć przedwcześnie połączenia z upływem limitu czasu.

Po upływie limitu czasu połączenia Usługa CDN ponawia próbę po wysłaniu błędu "504-Gateway timeout" do klienta. 

### <a name="conditions-for-large-file-optimization"></a>Warunki optymalizacji dużych plików

Poniższa tabela zawiera listę kryteriów, które mają zostać spełnione w przypadku optymalizacji dużych plików:

Warunek | Wartości 
--- | --- 
Obsługiwane typy plików | 3g2, 3GP, ASF, AVI, bz2, DMG, exe, F4V, FLV, <br> GZ, HDP, ISO, JXR, M4V, MKV, MOV, MP4, <br> MPEG, MPG, MTS, pkg, QT, RM, SWF, tar, <br> tgz, WDP, WebM, WEBP, WMA, WMV, zip  
Minimalny rozmiar pliku | 10 MB 
Maksymalna wielkość pliku | 150 GB 
Charakterystyki serwera pochodzenia | Musi obsługiwać żądania zakresu bajtów 

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Należy wziąć pod uwagę następujące dodatkowe aspekty dla tego typu optymalizacji:

- Proces tworzenia fragmentów generuje dodatkowe żądania do serwera pochodzenia. Jednak ogólna ilość danych dostarczanych z lokalizacji źródłowej jest znacznie mniejsza. Rozdzielenie powoduje zwiększenie jakości buforowania w sieci CDN.

- Wykorzystanie pamięci i operacji we/wy zmniejsza się w miejscu pochodzenia, ponieważ są dostarczane mniejsze fragmenty pliku.

- W przypadku fragmentów w pamięci podręcznej w sieci CDN nie ma dodatkowych żądań do pochodzenia do momentu wygaśnięcia zawartości lub jej wykluczenia z pamięci podręcznej.

- Użytkownicy mogą przekonywać żądań zakresu do sieci CDN, które są traktowane jak każdy normalny plik. Optymalizacja ma zastosowanie tylko wtedy, gdy jest to prawidłowy typ pliku, a zakres bajtów jest z przedziału od 10 MB do 150 GB. Jeśli średni rozmiar żądanego pliku jest mniejszy niż 10 MB, zamiast tego użyj ogólnego dostarczania w sieci Web.

