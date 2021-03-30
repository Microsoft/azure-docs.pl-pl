---
title: Podstawowe raporty z Verizon | Microsoft Docs
description: Dowiedz się, jak uzyskiwać dostęp do podstawowych raportów Verizon i wyświetlać je za pośrednictwem portalu zarządzania dla profilów Verizon.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5353fa70be4705b28dab9350fcdf7819b22faf54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90985532"
---
# <a name="core-reports-from-verizon"></a>Raporty podstawowe z usługi Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Korzystając z raportów Verizon Core za pośrednictwem portalu zarządzania dla profilów Verizon, można wyświetlić wzorce użycia dla sieci CDN przy użyciu następujących raportów:

* Przepustowość
* Przesłane dane
* Trafienia
* Stany pamięci podręcznej
* Współczynnik trafień w pamięci podręcznej
* Przesyłane dane IPV4/IPV6

## <a name="accessing-verizon-core-reports"></a>Uzyskiwanie dostępu do podstawowych raportów Verizon
1. W bloku profil usługi CDN kliknij przycisk **Zarządzaj** .
   
    ![Przycisk zarządzania profilem CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.
2. Zatrzymaj wskaźnik myszy na karcie **Analiza** , a następnie umieść wskaźnik myszy nad głównym menu wysuwanym **raportów** . Kliknij raport w menu.
   
    ![Portal zarządzania sieci CDN — menu podstawowe raporty](./media/cdn-reports/cdn-core-reports.png)

3. Dla każdego raportu wybierz zakres dat z listy **zakres dat** . Można wybrać wstępnie zdefiniowany zakres dat, taki jak **dzisiaj** lub w **tym tygodniu**, lub wybrać opcję **niestandardowy** i ręcznie wprowadzić zakres dat, klikając ikonę kalendarza. 

4. Po wybraniu zakresu dat kliknij pozycję **Przejdź** , aby wygenerować raport. 

4. Jeśli chcesz wyeksportować dane w formacie programu Excel, kliknij ikonę programu Excel nad przyciskiem **Przejdź** .

## <a name="bandwidth"></a>Przepustowość
Raport o przepustowości składa się z wykresu i tabeli danych, która wskazuje użycie przepustowości sieci CDN dla protokołów HTTP i HTTPS w określonym przedziale czasu, w MB/s. Możesz wyświetlić użycie przepustowości dla wszystkich punktów obecności lub dla określonego punktu obecności. Ten raport umożliwia wyświetlenie wartości szczytowych i rozkładu ruchu dla punktów obecności.

Z listy **węzły krawędzi** zaznacz pozycję **wszystkie węzły krawędzi** , aby wyświetlić ruch ze wszystkich węzłów, lub wybierz konkretny region.

Raport jest aktualizowany co pięć minut.

![Raport o przepustowości](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Przesłane dane
Ten raport składa się z wykresu i tabeli danych, która wskazuje użycie ruchu w sieci CDN dla protokołów HTTP i HTTPS w określonym przedziale czasu, w GB. Możesz wyświetlić użycie ruchu dla wszystkich punktów obecności lub dla określonego punktu obecności. Ten raport umożliwia wyświetlenie wartości szczytowych i rozkładu ruchu między punktami obecności.

Z listy **węzły krawędzi** zaznacz pozycję **wszystkie węzły krawędzi** , aby wyświetlić ruch ze wszystkich węzłów, lub wybierz konkretny region.

Raport jest aktualizowany co pięć minut.

![Raport dotyczący transferu danych](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Trafienia (kody stanu)
Ten raport opisuje dystrybucję kodów stanu żądania dla zawartości. Każde żądanie dotyczące zawartości generuje kod stanu HTTP. Kod stanu opisuje sposób, w jaki punkty POP programu Edge obsłużyć żądanie. Na przykład kod stanu 2xx wskazuje, że żądanie zostało pomyślnie obsłużone do klienta, podczas gdy kod stanu 4xx wskazuje, że wystąpił błąd. Aby uzyskać więcej informacji na temat kodów stanu HTTP, zobacz [listę kodów stanu HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Raport o trafień](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Stany pamięci podręcznej
Ten raport opisuje rozkład trafień w pamięci podręcznej i chybień w pamięci podręcznej dla żądań klientów. Ze względu na najszybszą wydajność w przypadku trafień w pamięci podręcznej można zoptymalizować szybkość dostarczania danych przez zminimalizowanie chybień w pamięci podręcznej i nieaktualnych trafień 

Aby zmniejszyć liczbę chybień pamięci podręcznej, skonfiguruj serwer pochodzenia, aby zminimalizować użycie następujących elementów: 
 * `no-cache` nagłówki odpowiedzi
 * Buforowanie ciągu zapytania, chyba że jest to absolutnie niezbędne  
 * Kody odpowiedzi bez pamięci podręcznej

Aby zmniejszyć wygasłe trafienia pamięci podręcznej, należy ustawić zasób `max-age` na długi okres, aby zminimalizować liczbę żądań do serwera pochodzenia.

![Raport o stanie pamięci podręcznej](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Główne Stany pamięci podręcznej to:
* TCP_HIT: obsługiwane z serwera brzegowego. Obiekt był w pamięci podręcznej i nie przekroczył maksymalnego wieku.
* TCP_MISS: obsługiwane z serwera pochodzenia. Obiekt nie znajduje się w pamięci podręcznej i odpowiedź została pobrana z powrotem do źródła.
* TCP_EXPIRED _MISS: obsługiwane z serwera pochodzenia po ponownej walidacji ze źródłem. Obiekt znajdowała się w pamięci podręcznej, ale przekroczył maksymalny wiek. Ponowne sprawdzanie poprawności ze źródłem spowodowało zamienienie obiektu pamięci podręcznej przez nową odpowiedź z pochodzenia.
* TCP_EXPIRED _HIT: obsługiwane z krawędzi po ponownej walidacji ze źródłem. Obiekt był w pamięci podręcznej, ale przekroczył maksymalny wiek. Ponowne sprawdzanie poprawności z serwerem źródłowym spowodowało niezmodyfikowanie obiektu pamięci podręcznej.

### <a name="full-list-of-cache-statuses"></a>Pełna lista stanów pamięci podręcznej
* TCP_HIT — ten stan jest zgłaszany, gdy żądanie jest doręczane bezpośrednio z punktu obecności do klienta. Zasób jest natychmiast obsługiwany z punktu POP, gdy jest buforowany w punkcie POP najbliżej klienta i ma prawidłowy czas wygaśnięcia (TTL). Czas wygaśnięcia jest określany na podstawie następujących nagłówków odpowiedzi:
  
  * Cache-Control: s-maxage
  * Cache-Control: maks. wiek
  * Wygasa
* TCP_MISS: ten stan wskazuje na to, że w punkcie POP najbliżej klienta nie znaleziono buforowanej wersji żądanego zasobu. Zasób jest żądany z serwera pochodzenia lub z serwera ochrony źródła. Jeśli serwer pierwotny lub serwer funkcji ochrony źródła zwraca element zawartości, jest on obsługiwany przez klienta i buforowany zarówno na kliencie, jak i na serwerze brzegowym. W przeciwnym razie zwracany jest kod stanu inny niż 200 (na przykład 403 zabroniony lub 404 nie został znaleziony).
* TCP_EXPIRED_HIT: ten stan jest raportowany, gdy żądanie dotyczące elementu zawartości z wygasłym czasem wygaśnięcia zostało obsłużone bezpośrednio z punktu POP do klienta. Na przykład po wygaśnięciu maksymalnego wieku elementu zawartości. 
  
   Wygasłe żądanie zwykle powoduje żądanie ponownej walidacji serwera pochodzenia. Aby stan TCP_EXPIRED_HIT miał miejsce, serwer pierwotny musi wskazywać, że nie istnieje nowsza wersja elementu zawartości. Ta sytuacja zazwyczaj powoduje aktualizację Cache-Control i wygasania nagłówków zasobów.
* TCP_EXPIRED_MISS: ten stan jest raportowany, gdy w punkcie POP do klienta zostanie obsłużona nowsza wersja wygasłego, zbuforowanego elementu zawartości. Ten stan występuje, gdy wartość czasu wygaśnięcia w pamięci podręcznej wygasła (na przykład wygasła max-age), a serwer pierwotny zwraca nowszą wersję tego elementu zawartości. Ta nowa wersja elementu zawartości jest obsługiwana dla klienta, a nie w wersji zapisanej w pamięci podręcznej. Ponadto jest on buforowany na serwerze brzegowym i kliencie.
* CONFIG_NOCACHE: ten stan wskazuje, że Konfiguracja specyficzna dla klienta w punkcie POP nie uniemożliwiła buforowania elementu zawartości.
* Brak — ten stan wskazuje, że sprawdzanie Aktualności zawartości pamięci podręcznej nie zostało wykonane.
* TCP_CLIENT_REFRESH_MISS: ten stan jest raportowany, gdy klient HTTP, taki jak przeglądarka, wymusi w punkcie POP, aby pobrać nową wersję nieodświeżonego elementu zawartości z serwera pochodzenia. Domyślnie serwery uniemożliwiają klientowi protokołu HTTP wymuszenie serwerom brzegowym pobierania nowej wersji elementu zawartości z serwera pochodzenia.
* TCP_PARTIAL_HIT: ten stan jest raportowany, gdy żądanie zakresu bajtów powoduje trafienie dla częściowo buforowanego elementu zawartości. Żądany zakres bajtów jest natychmiast obsługiwany z punktu POP do klienta.
* Bez buforowania: ten stan jest raportowany, gdy element zawartości `Cache-Control` i `Expires` nagłówki wskazują, że nie powinny być buforowane w punkcie obecności lub przez klienta http. Te typy żądań są obsługiwane z serwera pochodzenia.

## <a name="cache-hit-ratio"></a>Współczynnik trafień w pamięci podręcznej
Ten raport wskazuje procent buforowanych żądań, które zostały obsłużone bezpośrednio z pamięci podręcznej.

Raport zawiera następujące informacje:

* Żądana zawartość została zbuforowana w punkcie obecności najbliżej osoby żądającej.
* Żądanie zostało obsłużone bezpośrednio z krawędzi naszej sieci.
* Żądanie nie wymagało ponownej walidacji z serwerem pochodzenia.

Raport nie zawiera:

* Żądania, które są odrzucane z powodu opcji filtrowania kraju/regionu.
* Żądania dotyczące zasobów, których nagłówki wskazują, że nie powinny być buforowane. Na przykład, `Cache-Control: private` , `Cache-Control: no-cache` lub `Pragma: no-cache` nagłówki uniemożliwiają buforowanie elementu zawartości.
* Żądania zakresu bajtów dla częściowo buforowanej zawartości.

Formuła: (TCP_ TRAFIeń/(TCP_ TRAFIeń + TCP_MISS)) * 100

![Raport współczynnika trafień pamięci podręcznej](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Przesyłane dane IPV4/IPV6
Ten raport przedstawia rozkład użycia ruchu w protokole IPV4 i IPV6.

![Przesyłane dane IPV4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Zagadnienia do rozważenia
Raporty można generować tylko w ciągu ostatnich 18 miesięcy.

