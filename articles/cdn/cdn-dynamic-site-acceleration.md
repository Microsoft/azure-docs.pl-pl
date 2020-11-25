---
title: Przyspieszanie witryn dynamicznych za pośrednictwem Azure CDN
description: Azure CDN obsługuje optymalizację usługi Dynamic site Acceleration (DSA) dla plików z zawartością dynamiczną.
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
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 5387fdc224cd77ee5273767df5033a51dc27608c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008365"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Przyspieszanie witryn dynamicznych za pośrednictwem Azure CDN

Dzięki rozłożeniu mediów społecznościowych, handlu elektronicznego i witryny sieci Web z spersonalizowaną funkcją Hyper-w czasie rzeczywistym jest generowana szybko zwiększająca się wartość procentowa zawartości dostarczanej użytkownikom końcowym. Użytkownicy oczekują szybkiego, niezawodnego i spersonalizowanego środowiska internetowego niezależnie od ich przeglądarki, lokalizacji, urządzenia lub sieci. Jednak bardzo innowacje, które sprawiają, że te środowiska mają również na celu powolne pobieranie stron i przełączenie jakości środowiska konsumenta na ryzyko. 

Funkcja usługi Content Delivery Network (CDN) obejmuje możliwość przełączenia plików do pamięci podręcznej do użytkowników końcowych w celu przyspieszenia dostarczania plików statycznych. Jednak dzięki dynamicznym aplikacjom sieci Web buforowanie zawartości w lokalizacjach brzegowych nie jest możliwe, ponieważ serwer generuje zawartość w odpowiedzi na zachowanie użytkownika. Przyspieszenie dostarczania takiej zawartości jest bardziej skomplikowane niż tradycyjne buforowanie brzegowe i wymaga kompleksowego rozwiązania, które w sposób dostraja każdy element do całej ścieżki danych od rozpoczęcia do dostarczenia. Dzięki optymalizacji Azure CDN dynamicznej (DSA) można mierzyć wydajność stron sieci Web z zawartością dynamiczną.

**Azure CDN z Akamai** i **Azure CDN z usługi Verizon** oferują optymalizację DSA za pośrednictwem **zoptymalizowanego** menu podczas tworzenia punktu końcowego. Przyspieszanie witryn dynamicznych od firmy Microsoft jest oferowane przez [usługę Azure front-drzwi](../frontdoor/front-door-overview.md).

> [!Important]
> W przypadku **Azure CDN z profilów Akamai** można zmienić optymalizację punktu końcowego usługi CDN po jego utworzeniu.
>   
> W przypadku profili usługi **Azure CDN from Verizon** nie można zmienić optymalizacji punktu końcowego usługi CDN po jego utworzeniu.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfiguracja punktu końcowego usługi CDN w celu przyspieszenia dostarczania plików dynamicznych

Aby skonfigurować punkt końcowy usługi CDN w celu zoptymalizowania dostarczania plików dynamicznych, można użyć Azure Portal, interfejsów API REST lub dowolnego z zestawów SDK klienta, aby programowo wykonać te same czynności. 

**Aby skonfigurować punkt końcowy usługi CDN na potrzeby optymalizacji DSA przy użyciu Azure Portal:**

1. Na stronie **profil usługi CDN** wybierz pozycję **punkt końcowy**.

   ![Dodaj nowy punkt końcowy usługi CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Zostanie wyświetlone okienko **Dodawanie punktu końcowego**.

2. W obszarze **zoptymalizowane** pod kątem wybierz pozycję **przyspieszenie witryny dynamicznej**.

    ![Utwórz nowy punkt końcowy usługi CDN przy użyciu agenta DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Dla **ścieżki sondy** Wprowadź prawidłową ścieżkę do pliku.

    Ścieżka sondy jest funkcją specyficzną dla DSA, a do utworzenia wymagana jest prawidłowa ścieżka. Agent DSA używa pliku z małym *sondowaniem* umieszczonym na serwerze źródłowym w celu zoptymalizowania konfiguracji routingu sieciowego dla sieci CDN. Plik z ścieżką sondy umożliwia pobranie i przekazanie przykładowego pliku do witryny lub użycie istniejącego elementu zawartości dla źródła o rozmiarze około 10 KB.

4. Wprowadź inne wymagane opcje punktu końcowego (Aby uzyskać więcej informacji, zobacz [Tworzenie nowego punktu końcowego usługi CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), a następnie wybierz pozycję **Dodaj**.

   Po utworzeniu punktu końcowego usługi CDN stosowane są optymalizacje DSA dla wszystkich plików spełniających określone kryteria. 


**Aby skonfigurować istniejący punkt końcowy dla agenta DSA (Azure CDN tylko z profilów Akamai):**

1. Na stronie **profil usługi CDN** wybierz punkt końcowy, który chcesz zmodyfikować.

2. W okienku po lewej stronie wybierz pozycję **Optymalizacja**. 

   Zostanie wyświetlona strona **Optymalizacja** .

3. W obszarze **zoptymalizowane** pod kątem wybierz pozycję **przyspieszenie witryny dynamicznej**, a następnie wybierz pozycję **Zapisz**.

> [!Note]
> DSA wiąże się z dodatkowymi opłatami. Aby uzyskać więcej informacji, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optymalizacja DSA przy użyciu Azure CDN

Przyspieszenie witryny dynamicznej na Azure CDN przyspiesza dostarczanie dynamicznych zasobów przy użyciu następujących technik:

-   [Optymalizacja tras](#route-optimization)
-   [Optymalizacje protokołu TCP](#tcp-optimizations)
-   [Pobieranie z wyprzedzeniem obiektów (Azure CDN tylko z Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptacyjna kompresja obrazu (Azure CDN tylko z Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optymalizacja tras

Optymalizacja trasy jest ważna, ponieważ Internet jest miejscem dynamicznym, w którym ruch i tymczasowa awaria ciągle zmieniają się w topologii sieci. Border Gateway Protocol (BGP) to protokół routingu sieci Internet, ale mogą występować szybsze trasy za pośrednictwem serwerów punktu obecności (PoP). 

Optymalizacja tras wybiera najbardziej optymalną ścieżkę do źródła, dzięki czemu lokacja jest stale dostępna i dynamiczna zawartość jest dostarczana do użytkowników końcowych za pośrednictwem najszybszych i najbardziej niezawodnej możliwej trasy. 

Sieć Akamai używa technik do zbierania danych w czasie rzeczywistym i porównywania różnych ścieżek za pośrednictwem różnych węzłów na serwerze Akamai, a także domyślnej trasy BGP w ramach otwartego Internetu, aby określić najszybszą trasę między źródłem a usługą CDN. Techniki te zapobiegają punktom przeciążenia Internetu i długim trasom. 

Analogicznie, Sieć Verizon używa kombinacji punktów obecności systemu DNS o dużej pojemności i kontroli kondycji, aby określić najlepsze bramy, aby najlepiej przesyłać dane z klienta do źródła.
 
W związku z tym w pełni dynamiczna i transakcyjna zawartość jest dostarczana szybciej i bardziej niezawodnie użytkownikom końcowym, nawet w przypadku braku pamięci podręcznej. 

### <a name="tcp-optimizations"></a>Optymalizacje protokołu TCP

Transmission Control Protocol (TCP) to standardowy zestaw protokołów internetowych służący do dostarczania informacji między aplikacjami w sieci IP.  Domyślnie w celu skonfigurowania połączenia TCP jest wymagane kilka żądań z poprzednimi i z góry, a także limity pozwalające uniknąć przeciążenia sieci, co powoduje niewydajne skalowanie. **Azure CDN z Akamai** obsługuje ten problem przez optymalizację w trzech obszarach: 

 - [Eliminowanie wolnego uruchomienia protokołu TCP](#eliminating-tcp-slow-start)
 - [Korzystanie z połączeń trwałych](#leveraging-persistent-connections)
 - [Dostrajanie parametrów pakietu TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminowanie wolnego uruchomienia protokołu TCP

*Wolny Start* TCP jest algorytmem protokołu TCP, który uniemożliwia Przeciążenie sieci przez ograniczenie ilości danych przesyłanych przez sieć. Zaczyna się od małych rozmiarów okna przeciążenia między nadawcą i odbiornikiem do momentu osiągnięcia maksymalnego limitu lub utraty pakietów.

 Zarówno **Azure CDN z Akamai** , jak i **Azure CDN z profilów Verizonów** nie należy uruchamiać protokołu TCP, wykonując następujące trzy kroki:

1. Monitorowanie kondycji i przepustowości służy do mierzenia przepustowości połączeń między serwerami z punktami PoP.
    
2. Metryki są współdzielone przez serwery z systemem Edge, aby każdy serwer był świadomy warunków sieciowych i kondycji serwera innych punktów obecności.  
    
3. Serwery brzegowe usługi CDN tworzą założenia dotyczące niektórych parametrów transmisji, takich jak optymalny rozmiar okna, który powinien być w trakcie komunikacji z innymi serwerami brzegowymi usługi CDN w sąsiedztwie. Ten krok oznacza, że rozmiar okna początkowej przeciążenia można zwiększyć, jeśli kondycja połączenia między serwerami brzegowymi usługi CDN jest w stanie wyższym niż transfer danych pakietów.  

#### <a name="leveraging-persistent-connections"></a>Korzystanie z połączeń trwałych

W przypadku korzystania z sieci CDN mniejsza liczba unikatowych maszyn nawiązuje połączenie z serwerem źródłowym bezpośrednio w porównaniu z użytkownikami łączącymi się bezpośrednio ze źródłem. Azure CDN również pule żądań użytkowników, aby ustanowić mniejszą liczbę połączeń ze źródłem.

Jak wspomniano wcześniej, niektóre żądania uzgadniania są wymagane do nawiązania połączenia TCP. Połączenia trwałe, które są implementowane przez `Keep-Alive` nagłówek HTTP, ponownie korzystają z istniejących połączeń TCP dla wielu żądań HTTP, aby zaoszczędzić czas błądzenia i przyspieszyć dostarczanie. 

**Azure CDN z Verizon** również wysyła okresowe pakiety utrzymywania aktywności za pośrednictwem połączenia TCP, aby zapobiec zamykaniu otwartego połączenia.

#### <a name="tuning-tcp-packet-parameters"></a>Dostrajanie parametrów pakietu TCP

**Azure CDN z Akamai** dostosowuje parametry, które regulują połączenia między serwerami i zmniejszają liczbę podróży, które są wymagane do pobrania zawartości osadzonej w lokacji przy użyciu następujących technik:

- Zwiększenie początkowej okna przeciążenia w taki sposób, aby kolejne pakiety mogły być wysyłane bez oczekiwania na potwierdzenie.
- Zmniejszenie limitu czasu początkowej ponownej transmisji, aby wykryto utratę, a ponowna transmisja przebiega szybciej.
- Zmniejszenie minimalnego i maksymalnego limitu czasu ponownego przesyłania, aby skrócić czas oczekiwania przed utratą pakietów w transmisji.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Pobieranie z wyprzedzeniem obiektów (Azure CDN tylko z Akamai)

Większość witryn sieci Web składa się ze strony HTML, która odwołuje się do różnych innych zasobów, takich jak obrazy i skrypty. Zazwyczaj gdy klient żąda strony sieci Web, przeglądarka najpierw pobiera i analizuje obiekt HTML, a następnie wysyła dodatkowe żądania do połączonych zasobów, które są wymagane do całkowitego załadowania strony. 

*Pobieranie z wyprzedzeniem* jest techniką do pobierania obrazów i skryptów osadzonych na stronie HTML, gdy kod HTML jest obsługiwany przez przeglądarkę, a także przed wykonaniem tych żądań przez przeglądarkę. 

Po włączeniu opcji pobierania z wyprzedzeniem w momencie, gdy sieć CDN obsługuje stronę bazową HTML do przeglądarki klienta, Usługa CDN analizuje plik HTML i tworzy dodatkowe żądania dla wszystkich połączonych zasobów i zapisuje je w pamięci podręcznej. Gdy klient wysyła żądania dotyczące połączonych zasobów, serwer brzegowy usługi CDN ma już żądane obiekty i może je od razu obsłużyć bez przejazdu do źródła. Ta optymalizacja zapewnia zarówno zawartość pamięci podręcznej, jak i niebędącą w pamięci podręcznej.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptacyjna kompresja obrazu (Azure CDN tylko z Akamai)

Niektóre urządzenia, szczególnie mobilne, umożliwiają wolniejszą szybkość sieci od czasu do czasu. W tych scenariuszach bardziej korzystne jest, aby użytkownik szybciej odbierał mniejsze obrazy na swojej stronie sieci Web, a nie czekał długiego czasu na obrazy pełnej rozdzielczości.

Ta funkcja automatycznie monitoruje jakość sieci i wykorzystuje standardowe metody kompresji JPEG, gdy szybkość sieci jest wolniejsza, aby zwiększyć czas dostarczania.

Adaptacyjna kompresja obrazu | Rozszerzenia plików  
--- | ---  
Kompresja JPEG | . jpg,. jpeg,. jpe,. Jig,. jgig,. JGI

## <a name="caching"></a>Buforowanie

W przypadku programu DSA buforowanie jest domyślnie wyłączone w sieci CDN, nawet gdy źródło zawiera `Cache-Control` lub `Expires` nagłówki w odpowiedzi. Agent DSA jest zazwyczaj używany w przypadku zasobów dynamicznych, które nie powinny być buforowane, ponieważ są unikatowe dla każdego klienta. Buforowanie może przerwać takie zachowanie.

Jeśli masz witrynę sieci Web z mieszaniną statycznych i dynamicznych zasobów, najlepiej zastosować podejście hybrydowe, aby uzyskać najlepszą wydajność. 

W przypadku **Azure CDN Standard from Verizon** i **Azure CDN Standard z profilów Akamai** można włączyć buforowanie dla określonych punktów końcowych DSA przy użyciu [reguł buforowania](cdn-caching-rules.md).

Aby uzyskać dostęp do reguł buforowania:

1. Na stronie **profil usługi CDN** w obszarze Ustawienia wybierz pozycję **Zasady buforowania**.  
    
    ![Przycisk Reguły buforowania usługi CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Zostanie otwarta strona **reguły buforowania** .

2. Utwórz globalną lub niestandardową regułę buforowania, aby włączyć buforowanie dla punktu końcowego DSA. 

W przypadku **Azure CDN Premium tylko w** profilach Verizon należy włączyć buforowanie dla określonych punktów KOŃCOWYch DSA przy użyciu [aparatu reguł](./cdn-verizon-premium-rules-engine.md). Wszystkie utworzone reguły mają wpływ tylko na punkty końcowe Twojego profilu zoptymalizowane pod kątem DSA. 

Aby uzyskać dostęp do aparatu reguł:
    
1. Na stronie **profil usługi CDN** wybierz pozycję **Zarządzaj**.  
    
    ![Przycisk zarządzania profilem CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Zostanie otwarty portal zarządzania sieci CDN.

2. W portalu zarządzania usługi CDN wybierz pozycję **wdrożenie**, a następnie wybierz pozycję **aparat reguł**. 

    ![Aparat reguł dla agenta DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternatywnie można użyć dwóch punktów końcowych usługi CDN: jednego punktu końcowego zoptymalizowanego za pomocą agenta DSA do dostarczania dynamicznych zasobów i innego punktu końcowego zoptymalizowanego za pomocą typu optymalizacji statycznej, takiego jak ogólne dostarczanie w sieci Web, do dostarczania zasobów pamięci podręcznej. Zmodyfikuj adresy URL strony sieci Web, aby połączyć się bezpośrednio z zasobem w punkcie końcowym usługi CDN, który ma być używany. 

Na przykład: `mydynamic.azureedge.net/index.html` jest stroną dynamiczną i jest załadowana z punktu końcowego DSA.  Strona HTML odwołuje się do wielu statycznych zasobów, takich jak biblioteki lub obrazy języka JavaScript, które są ładowane ze statycznego punktu końcowego sieci CDN, takiego jak `mystatic.azureedge.net/banner.jpg` i `mystatic.azureedge.net/scripts.js` .