---
title: Raporty niestandardowe z usługi Verizon | Microsoft Docs
description: 'Wzorce użycia dla sieci CDN można wyświetlić, korzystając z następujących raportów: przepustowości, transferów danych, trafień, stanu pamięci podręcznej, współczynnika trafień pamięci podręcznej, przesłanych danych IPV4/IPV6.'
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
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: cff355aeb74efc9dec2780840ae57fea4c5764cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84888709"
---
# <a name="custom-reports-from-verizon"></a>Raporty niestandardowe z usługi Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Za pomocą raportów niestandardowych Verizon za pośrednictwem portalu zarządzania dla profilów Verizon, można zdefiniować typ danych, które mają być zbierane dla raportów CNAME dla krawędzi.


## <a name="accessing-verizon-custom-reports"></a>Uzyskiwanie dostępu do raportów niestandardowych Verizon
1. W bloku profil usługi CDN kliknij przycisk **Zarządzaj** .
   
    ![Przycisk zarządzania profilem CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.
2. Zatrzymaj wskaźnik myszy na karcie **Analiza** , a następnie umieść wskaźnik myszy na pasku menu wysuwanym **raporty niestandardowe** . Kliknij pozycję **krawędź CNAME**.
   
    ![Portal zarządzania CDN — menu raportów niestandardowych](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Raport niestandardowy CNAME dla krawędzi
Raport niestandardowy CNAME (Brzeg) zawiera dane statystyczne dotyczące trafień i transferów danych dla krawędzi CNAME, od których włączono rejestrowanie raportów niestandardowych. Rekordy CNAME brzegowe składają się z Azure CDN nazw hostów punktów końcowych i skojarzonych z nimi nazw hostów domeny niestandardowej. 

Niestandardowe rejestrowanie danych raportu zaczyna się jedną godzinę po włączeniu niestandardowej funkcji raportowania CNAME. Możesz wyświetlić dane raportu, generując raport CNAME dla określonej platformy lub dla wszystkich platform. Pokrycie tego raportu jest ograniczone do rekordu CNAME, dla którego dane raportu niestandardowego zostały zebrane w określonym przedziale czasu. Raport CNAME w dół składa się z wykresu i tabeli danych dla 10. rekordów CNAME o najwyższej krawędzi, zgodnie z metryką zdefiniowaną w opcji metryki. 

Generowanie raportu niestandardowego przez definiowanie następujących opcji raportu:

- Metryki: obsługiwane są następujące opcje:

   - Trafienia: wskazuje całkowitą liczbę żądań, które są kierowane do granicy CNAME, dla której włączono funkcję raportowania niestandardowego. Ta Metryka nie obejmuje kodu stanu zwróconego do klienta.

   - Przesyłane dane: wskazuje całkowitą ilość danych transferowanych z serwerów brzegowych do klientów HTTP (na przykład przeglądarki sieci Web) dla żądań skierowanych do krawędzi CNAME, dla których włączono funkcję raportowania niestandardowego. Ilość transferowanych danych jest obliczana przez dodanie nagłówków odpowiedzi HTTP do treści odpowiedzi. W związku z tym ilość danych transferowanych dla każdego elementu zawartości jest większa niż jego rzeczywisty rozmiar pliku.

- Grupowanie: określa typ statystyk, które są wyświetlane poniżej wykresu słupkowego. Obsługiwane są następujące opcje:

   - Kody odpowiedzi HTTP: organizuje statystyki według kodu odpowiedzi HTTP (na przykład 200, 403 itd.) zwracanego do klienta. 

   - Stan pamięci podręcznej: organizuje statystyki według stanu pamięci podręcznej.


Aby ustawić zakres dat dla raportu, możesz wybrać wstępnie zdefiniowany zakres dat, taki jak **dzisiaj** lub w **tym tygodniu**, z listy rozwijanej lub wybrać opcję **niestandardowy** i ręcznie wprowadzić zakres dat, klikając ikonę kalendarza. 

Po wybraniu zakresu dat kliknij pozycję **Przejdź** , aby wygenerować raport.

Dane można wyeksportować w formacie programu Excel, klikając symbol programu Excel po prawej stronie przycisku **Przejdź** .

![Rekordy CNAME](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Pola raportu w niestandardowym rekordzie CNAME

| Pole                     | Opis   |
|---------------------------|---------------|
| 2xx                       | Wskazuje łączną liczbę żądań lub danych przesłanych (MB) dla krawędzi CNAME, która powoduje kod stanu HTTP 2xx (na przykład 200 OK). |
| 3xx                       | Wskazuje łączną liczbę żądań lub danych transferowanych (MB) dla krawędzi CNAME, która powoduje 3xx kod stanu HTTP (na przykład 302 znaleziony lub 304 nie został zmodyfikowany. |
| 4xx                       | Wskazuje łączną liczbę żądań lub danych transferowanych (MB) dla krawędzi CNAME, która powoduje 4xx kod stanu HTTP (na przykład 400 Nieprawidłowe żądanie, 403 zabronione lub 404 nie został znaleziony). |
| 5xx                       | Wskazuje łączną liczbę żądań lub danych transferowanych (MB) dla krawędzi CNAME, która powoduje 5xx kod stanu HTTP (na przykład 500 wewnętrzny błąd serwera lub 502 niewłaściwa brama). |
| Procent trafień w pamięci podręcznej               | Wskazuje procent żądań buforowanych, które zostały obsłużone bezpośrednio z pamięci podręcznej do obiektu żądającego. |
| Trafienia w pamięci podręcznej                | Wskazuje łączną liczbę żądań lub danych transferowanych (MB) dla krawędzi CNAME, która powoduje trafienie pamięci podręcznej (na przykład TCP_EXPIRED_HIT, TCP_HIT lub TCP_PARTIAL_HIT). Trafień pamięci podręcznej występuje, gdy zostanie znaleziona buforowana wersja żądanej zawartości. |
| Transferowane dane (MB)     | Wskazuje łączną ilość danych transferowanych (MB) z serwerów brzegowych do klientów HTTP (przeglądarki sieci Web) dla krawędzi CNAME. Ilość transferowanych danych jest obliczana przez dodanie nagłówków odpowiedzi HTTP do treści odpowiedzi. W związku z tym ilość danych transferowanych dla każdego elementu zawartości jest większa niż jego rzeczywisty rozmiar pliku. |
| Opis               | Identyfikuje krawędź CNAME dla nazwy hosta |
| Trafienia                      | Wskazuje łączną liczbę żądań do granicy CNAME |
| Chybienia                    | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla krawędzi CNAME, która powoduje chybień w pamięci podręcznej (na przykład TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS lub TCP_MISS). Chybienia w pamięci podręcznej występuje, gdy żądana zawartość nie została zbuforowana na serwerze brzegowym, który zgłosił żądanie. | 
| Brak pamięci podręcznej                  | Wskazuje łączną liczbę żądań lub danych transferowanych (MB) dla krawędzi CNAME, która powoduje CONFIG_NOCACHE kod stanu pamięci podręcznej.  |
| Inne                     | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla krawędzi CNAME wskazywanych przez kod stanu HTTP, który znajduje się poza zakresem 2xx-5xx. |
| Platforma                  | Wskazuje platformę obsługującą ruch w ramach rekordu CNAME. |
| Nieprzypisane               | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla krawędzi CNAME dla których kod stanu pamięci podręcznej lub informacje o kodzie stanu HTTP nie zostały zarejestrowane.  |
| Brak pamięci podręcznej               | Wskazuje łączną liczbę żądań lub danych przesłanych (MB) dla krawędzi CNAME, która powoduje niebuforowany kod stanu pamięci podręcznej.  |


## <a name="considerations"></a>Zagadnienia do rozważenia
Raporty można generować tylko w ciągu ostatnich 18 miesięcy.

