---
title: Optymalizacja przesyłania strumieniowego multimediów przy użyciu Azure CDN
description: Informacje o opcjach optymalizacji multimediów strumieniowych w usłudze Azure Content Delivery Network, takich jak częściowe udostępnianie pamięci podręcznej i czas oczekiwania na wypełnienie pamięci podręcznej.
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
ms.openlocfilehash: c3ab722f182e32cf2f3aca6bb2f3d5a9598264af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192615"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optymalizacja przesyłania strumieniowego multimediów przy użyciu Azure CDN 
 
Używanie wideo o wysokiej rozdzielczości zwiększa się w Internecie, dzięki czemu powstaje problemy umożliwiające wydajne dostarczanie dużych plików. Klienci oczekują niezakłóconego odtwarzania wideo na żądanie lub zawartości wideo na żywo w różnych sieciach i klientach na całym świecie. Szybki i wydajny mechanizm dostarczania plików przesyłania strumieniowego multimediów ma kluczowe znaczenie dla zapewnienia bezproblemowego i przyjemnego środowiska użytkownika.  

Multimedia przesyłane strumieniowo na żywo są szczególnie trudne do dostarczenia ze względu na duże rozmiary i liczbę równoczesnych podglądów. Długie opóźnienia powodują, że użytkownicy opuszczą. Ponieważ strumienie na żywo nie mogą być buforowane przed czasem i duże opóźnienia nie są akceptowalne dla osób przeglądających, fragmenty wideo muszą być dostarczane w odpowiednim czasie. 

Wzorce żądań przesyłania strumieniowego zawierają również pewne nowe wyzwania. Po udostępnieniu popularnego strumienia na żywo lub nowej serii dla wideo na żądanie, tysiące do milionów osób przeglądających może zażądać strumienia w tym samym czasie. W takim przypadku inteligentne konsolidacja żądań jest konieczna, aby nie przeciążać serwerów pierwotnych, gdy zasoby nie są jeszcze buforowane.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optymalizacje przesyłania strumieniowego multimediów dla Azure CDN firmy Microsoft

**Azure CDN Standard z** punktów końcowych firmy Microsoft dostarcza strumieniowo zasoby multimediów bezpośrednio przy użyciu ogólnego typu optymalizacji dostarczania w sieci Web. 

Optymalizacja przesyłania strumieniowego multimediów dla **standardu Azure CDN firmy Microsoft** jest skuteczna dla multimediów strumieniowych na żywo i wideo na żądanie, które wykorzystują pojedyncze fragmenty multimedialne do dostarczenia. Ten proces różni się od pojedynczego zasobu transferowanego przez pobranie progresywne lub za pomocą żądań o zakresie bajtów. Aby uzyskać informacje na temat tego stylu dostarczania multimediów, zobacz [Optymalizacja do pobierania dużych plików z Azure CDN](cdn-large-file-optimization.md).

Ogólne dostarczanie multimediów lub typy optymalizacji dostarczania multimediów wideo na żądanie przy użyciu usługi Azure Content Delivery Network (CDN) z optymalizacją zaplecza umożliwiają szybsze dostarczanie zasobów multimedialnych. Używają one również konfiguracji dla zasobów multimedialnych na podstawie najlepszych rozwiązań w czasie.

### <a name="partial-cache-sharing"></a>Częściowe udostępnianie pamięci podręcznej
Częściowe udostępnianie pamięci podręcznej umożliwia usłudze CDN obsłużyć częściowo buforowaną zawartość do nowych żądań. Na przykład jeśli pierwsze żądanie do usługi CDN spowoduje odrzucenie pamięci podręcznej, żądanie jest wysyłane do źródła. Mimo że ta Niekompletna zawartość jest załadowana do pamięci podręcznej usługi CDN, inne żądania kierowane do usługi CDN mogą rozpocząć pobieranie tych danych. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optymalizacje przesyłania strumieniowego multimediów dla Azure CDN z Verizon

**Azure CDN Standard from Verizon** i **Azure CDN Premium z** punktów końcowych Verizon dostarcza zasoby multimediów strumieniowych bezpośrednio przy użyciu ogólnego typu optymalizacji dostarczania w sieci Web. Kilka funkcji usługi CDN bezpośrednio pomaga w dostarczaniu zasobów multimedialnych domyślnie.

### <a name="partial-cache-sharing"></a>Częściowe udostępnianie pamięci podręcznej

Częściowe udostępnianie pamięci podręcznej umożliwia usłudze CDN obsłużyć częściowo buforowaną zawartość do nowych żądań. Na przykład jeśli pierwsze żądanie do usługi CDN spowoduje odrzucenie pamięci podręcznej, żądanie jest wysyłane do źródła. Mimo że ta Niekompletna zawartość jest załadowana do pamięci podręcznej usługi CDN, inne żądania kierowane do usługi CDN mogą rozpocząć pobieranie tych danych. 

### <a name="cache-fill-wait-time"></a>Czas oczekiwania na wypełnienie pamięci podręcznej

 Funkcja czas oczekiwania wypełnienia pamięci podręcznej wymusza na serwerze brzegowym przechowywanie wszelkich kolejnych żądań dla tego samego zasobu do momentu odebrania nagłówków odpowiedzi HTTP z serwera pochodzenia. Jeśli nagłówki odpowiedzi HTTP z punktu początkowego zostaną odebrane przed upływem czasomierza, wszystkie żądania, które zostały wstrzymane, są obsługiwane z rosnącej pamięci podręcznej. W tym samym czasie pamięć podręczna jest wypełniana danymi ze źródła. Domyślnie czas oczekiwania wypełnienia pamięci podręcznej wynosi 3 000 milisekund. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optymalizacje przesyłania strumieniowego multimediów dla Azure CDN z Akamai
 
**Azure CDN Standard from Akamai** oferuje funkcję, która dostarcza strumieniowo zasoby multimediów w całym świecie na dużą skalę. Funkcja zmniejsza opóźnienia, ponieważ zmniejsza obciążenie serwerów pochodzenia. Ta funkcja jest dostępna w warstwie cenowej standardowa Akamai. 

Optymalizacja przesyłania strumieniowego multimediów dla **Azure CDN Standard z Akamai** jest skuteczna dla multimediów strumieniowych na żywo i wideo na żądanie, które wykorzystują pojedyncze fragmenty multimedialne do dostarczenia. Ten proces różni się od pojedynczego zasobu transferowanego przez pobranie progresywne lub za pomocą żądań o zakresie bajtów. Aby uzyskać informacje na temat tego stylu dostarczania multimediów, zobacz [Optymalizacja dużych plików](cdn-large-file-optimization.md).

Ogólne dostarczanie multimediów lub typy optymalizacji dostarczania multimediów wideo na żądanie używają sieci CDN z optymalizacjami zaplecza, aby szybciej dostarczać zasoby multimedialne. Używają one również konfiguracji dla zasobów multimedialnych na podstawie najlepszych rozwiązań w czasie.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurowanie punktu końcowego usługi Akamai CDN w celu zoptymalizowania przesyłania strumieniowego multimediów
 
Można skonfigurować punkt końcowy usługi Content Delivery Network (CDN) w celu zoptymalizowania dostarczania dużych plików za pośrednictwem Azure Portal. Do tego celu można również użyć interfejsów API REST lub dowolnego z zestawów SDK klienta. Poniższe kroki pokazują proces za pośrednictwem Azure Portal w przypadku **standardowego Azure CDN profilu Akamai** :

1. Aby dodać nowy punkt końcowy, na stronie Akamai **profil usługi CDN** wybierz pozycję **Endpoint (punkt końcowy**).
  
    ![Nowy punkt końcowy](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Z listy rozwijanej **zoptymalizowane** pod kątem wybierz pozycję **wideo przesyłanie strumieniowe multimediów na żądanie** dla zasobów wideo na żądanie. Jeśli używasz kombinacji przesyłania strumieniowego na żywo i wideo na żądanie, wybierz pozycję **Ogólne przesyłanie strumieniowe multimediów**.

    ![Wybrano przesyłanie strumieniowe](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Po utworzeniu punktu końcowego stosowana jest optymalizacja dla wszystkich plików spełniających określone kryteria. W poniższej sekcji opisano ten proces. 

### <a name="caching"></a>Buforowanie

Jeśli **Azure CDN Standard from Akamai** wykryje, że element zawartości jest manifestem lub fragmentem przesyłania strumieniowego, używa różnych czasów wygaśnięcia pamięci podręcznej z ogólnego dostarczania w sieci Web. (Zapoznaj się z pełną listą w poniższej tabeli). Jak zawsze, są honorowane nagłówki Cache-Control lub Expires, które są wysyłane z pochodzenia. Jeśli zasób nie jest zasobem multimedialnym, pamięć podręczna jest używana przez czas wygaśnięcia dla ogólnego dostarczania w sieci Web.

Krótki ujemny czas buforowania jest przydatny do odciążania pochodzenia, gdy wielu użytkowników żąda fragmentu, który jeszcze nie istnieje. Przykładem jest strumień na żywo, w którym pakiety nie są dostępne z innego źródła. Dłuższy interwał buforowania ułatwia również odciążanie żądań od pochodzenia, ponieważ zawartość wideo nie jest zwykle modyfikowana.

| Buforowanie  | Ogólne dostarczanie w sieci Web | Ogólne przesyłanie strumieniowe multimediów | Przesyłanie strumieniowe multimediów wideo na żądanie  
|--- | --- | --- | ---
| Buforowanie: dodatnie <br> HTTP 200, 203, 300, <br> 301, 302 i 410 | 7 dni |365 dni | 365 dni   
| Buforowanie: ujemne <br> HTTP 204, 305, 404, <br> i 405 | Brak | 1 sekunda | 1 sekunda
 
### <a name="deal-with-origin-failure"></a>Postępowanie z niepowodzeniem pochodzenia  

Ogólne dostarczanie multimediów i dostarczanie multimediów wideo na żądanie również mają przekroczenia limitu czasu pochodzenia i dziennika ponownych prób w oparciu o najlepsze rozwiązania typowych wzorców żądań. Na przykład, ponieważ ogólne dostarczanie multimediów jest przeznaczone do dostarczania multimediów na żywo i wideo na żądanie, używa krótszego limitu czasu połączenia ze względu na wielkość liter w transmisji strumieniowej na żywo.

Po upływie limitu czasu połączenia Usługa CDN ponawia próbę po wysłaniu błędu "504-Gateway timeout" do klienta. 

Gdy plik jest zgodny z listą Stanów plików i warunków, Usługa CDN używa zachowania przesyłania strumieniowego multimediów. W przeciwnym razie używa ogólnego dostarczania w sieci Web.
   
### <a name="conditions-for-media-streaming-optimization"></a>Warunki optymalizacji przesyłania strumieniowego multimediów 

W poniższej tabeli przedstawiono zestaw kryteriów, które należy spełnić w przypadku optymalizacji przesyłania strumieniowego multimediów: 
 
Obsługiwane typy przesyłania strumieniowego | Rozszerzenia plików  
--- | ---  
Apple HLS | M3U8, M3U, m3ub, Key, TS, AAC
Adobe HDS | f4m, F4X, drmmeta, Bootstrap, F4F,<br>Struktura adresu URL Seg-Frag <br> (zgodne wyrażenie regularne: ^ (/. *) SEQ (\d +)-Frag (\d +)
OBRAMOWAN | MPD, kreska, DivX, ISMV, M4S, M4V, MP4, MP4V, <br> sidx, webm, mp4a, m4a, isma
Płynne przesyłanie strumieniowe | /manifest/, /QualityLevels/Fragments/
  
 
