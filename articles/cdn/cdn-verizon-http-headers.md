---
title: Verizon-specyficzne dla aparatu reguł Azure CDN | Microsoft Docs
description: W tym artykule opisano sposób użycia nagłówków HTTP specyficznych dla Verizon z aparatem reguł Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: allensu
ms.openlocfilehash: e20f6ce9540d357b61ae2cfdf0e8f96d127dc6c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84343221"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon nagłówki HTTP dla aparatu reguł Azure CDN

W przypadku **Azure CDN Premium z produktów Verizon** , gdy żądanie HTTP jest wysyłane do serwera pochodzenia, serwer punktu obecności (pop) może dodać jeden lub więcej zarezerwowanych nagłówków (lub specjalne nagłówki proxy) w żądaniu klienta do punktu obecności. Te nagłówki są uzupełnieniem standardowych nagłówków przesyłania dalej. Aby uzyskać informacje o nagłówkach standardowych żądań, zobacz [pola żądania](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Aby zapobiec dodawaniu jednego z tych zarezerwowanych nagłówków do żądania POP Azure CDN (Content Delivery Network) do serwera pochodzenia, należy utworzyć regułę z [funkcją specjalnych nagłówków serwera proxy](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) w aparacie reguł. W tej regule Wyklucz nagłówek, który chcesz usunąć z domyślnej listy nagłówków w polu nagłówki. Jeśli włączono [funkcję nagłówków odpowiedzi w pamięci podręcznej debugowania](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm), pamiętaj o dodaniu niepotrzebnych `X-EC-Debug` nagłówków. 

Na przykład, aby usunąć `Via` nagłówek, pole nagłówki reguły powinno zawierać następującą listę nagłówków: *x-Forwarded-For, x-Forwarded-proto, x-host, x-Midgress, x-Gateway-list, x-we-Name, Host*. 

![Reguła specjalnych nagłówków serwera proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

W poniższej tabeli opisano nagłówki, które mogą zostać dodane przez POP Verizon CDN w żądaniu:

Nagłówek żądania | Opis | Przykład
---------------|-------------|--------
[Korzystając](#via-request-header) | Identyfikuje serwer POP, który proxy żądania do serwera pochodzenia. | HTTP/1.1 ECS (DCA/1A2B)
X-Forwarded-For | Wskazuje adres IP osoby żądającej.| 10.10.10.10
X-Forwarded-proto | Wskazuje protokół żądania. | http
X-Host | Wskazuje nazwę hosta żądania. | cdn.mydomain.com
X-Midgress | Wskazuje, czy żądanie zostało przekazane przy użyciu dodatkowego serwera sieci CDN. Na przykład serwer podręczny serwer osłony lub serwer z systemem POP do serwera bramy wdrożenie. <br />Ten nagłówek jest dodawany do żądania tylko wtedy, gdy odbywa się ruch midgress. W takim przypadku nagłówek jest ustawiony na 1, aby wskazać, że żądanie zostało przekazane za pomocą dodatkowego serwera sieci CDN.| 1
[Host](#host-request-header) | Identyfikuje hosta i port, w którym można znaleźć żądaną zawartość. | marketing.mydomain.com:80
[Lista X-Gateway](#x-gateway-list-request-header) | Wdrożenie: identyfikuje listę trybu failover serwerów bramy wdrożenie przypisanej do źródła klienta. <br />Tarcza źródła: wskazuje zestaw serwerów osłony pochodzenia przypisanych do źródła klienta. | `icn1,hhp1,hnd1`
X-we-_ &lt; name &gt; _ | Nagłówki żądań, które zaczynają się od *X-we* (na przykład x-we-tag, [x-we-Debug](cdn-http-debug-headers.md)), są zarezerwowane do użytku przez sieć CDN.| WAF — produkcja

## <a name="via-request-header"></a>Za pomocą nagłówka żądania
Format, za pomocą którego `Via` nagłówek żądania identyfikuje serwer pop, jest określany przez następującą składnię:

`Via: Protocol from Platform (POP/ID)` 

Terminy używane w składni są zdefiniowane w następujący sposób:
- Protokół: wskazuje wersję protokołu (na przykład HTTP/1.1) służącą do proxy żądania. 

- Platforma: wskazuje platformę, w której zażądano zawartości. Następujące kody są prawidłowe dla tego pola: 

    Kod | Platforma
    -----|---------
    ECAcc | Duże HTTP
    ECS   | Niewielka HTTP
    ECD   | Sieć dostarczania aplikacji (wdrożenie)

- POP: wskazuje [punkt obecności](cdn-pop-abbreviations.md) , który obsłużył żądanie. 

- Identyfikator: tylko do użytku wewnętrznego.

### <a name="example-via-request-header"></a>Przykład za pośrednictwem nagłówka żądania

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Nagłówek żądania hosta
Serwery POP zastąpią `Host` nagłówek, gdy spełnione są oba poniższe warunki:
- Źródło dla wymaganej zawartości jest serwerem pochodzenia klienta.
- Opcja nagłówka hosta HTTP odpowiedniego pochodzenia klienta nie jest pusta.

`Host`Nagłówek żądania zostanie nadpisany w celu odzwierciedlenia wartości zdefiniowanej w opcji nagłówka hosta http.
Jeśli opcja nagłówka hosta HTTP pochodzenia klienta jest ustawiona na wartość puste, wówczas `Host` nagłówek żądania przesłany przez żądającego zostanie przekazany do serwera pochodzenia klienta.

## <a name="x-gateway-list-request-header"></a>Nagłówek żądania X-Gateway-list
Serwer POP doda/zastąpi nagłówek żądania X-Gateway-list po spełnieniu jednego z następujących warunków:
- Żądanie wskazuje na platformę wdrożenie.
- Żądanie jest przekazywane do serwera pochodzenia klienta, który jest chroniony przez funkcję osłony pochodzenia.

