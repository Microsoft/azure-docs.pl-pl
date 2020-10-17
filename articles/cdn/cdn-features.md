---
title: Porównanie funkcji produktu Azure Content Delivery Network (CDN)
description: Informacje o funkcjach, które obsługuje każdy produkt Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c8d8b01e8c5f4ea3054e639ecc9dd8b14bbf048b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148734"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Jakie są porównania między funkcjami produktu Azure CDN?

Istnieją cztery produkty Azure Content Delivery Network (CDN): 

* **Azure CDN Standard od firmy Microsoft**
* **Azure CDN Standard z Akamai**
* **Azure CDN Standard z Verizon**
* **Azure CDN Premium od Verizon**. 

W poniższej tabeli przedstawiono porównanie funkcji dostępnych w poszczególnych produktach.

| **Funkcje wydajności i optymalizacje** | **Microsoft — Standardowa** | **Akamai — Standardowa** | **Verizon — Standardowa** | **Verizon — Premium** |
| --- | --- | --- | --- | --- |
| [Przyspieszanie witryn dynamicznych](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Oferowana za pośrednictwem [usługi Azure front-drzwi](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Przyspieszanie witryn dynamicznych — Adaptive Image Compression (Adaptacyjna kompresja obrazu)](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Przyspieszanie witryn dynamicznych — Object Prefetch (Pobieranie obiektów z wyprzedzeniem)](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Ogólna Optymalizacja dostarczania w sieci Web](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** wybierz ten typ optymalizacji, jeśli średni rozmiar pliku jest mniejszy niż 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optymalizacja przesyłania strumieniowego wideo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | za pośrednictwem ogólnego dostarczania w sieci Web | **&#x2713;**  | za pośrednictwem ogólnego dostarczania w sieci Web |  za pośrednictwem ogólnego dostarczania w sieci Web |
| [Optymalizacja dużych plików](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | za pośrednictwem ogólnego dostarczania w sieci Web | **&#x2713;** wybierz ten typ optymalizacji, jeśli średni rozmiar pliku jest większy niż 10 MB   | za pośrednictwem ogólnego dostarczania w sieci Web |  za pośrednictwem ogólnego dostarczania w sieci Web |
| Zmień typ optymalizacji | |**&#x2713;** | | |
| Port źródła |Wszystkie porty TCP |[Dozwolone porty pochodzenia](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Wszystkie porty TCP |Wszystkie porty TCP |
| [Globalne równoważenia obciążenia serwera (usługa GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Szybkie przeczyszczanie](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Przeczyść wszystkie i Przeczyść symbole wieloznaczne nie są obecnie obsługiwane przez Azure CDN z Akamai |**&#x2713;** |**&#x2713;** |
| [Wstępne ładowanie zasobów](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Ustawienia pamięci podręcznej/nagłówka (przy użyciu [reguł buforowania](cdn-caching-rules.md))  |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Dostosowywalny, oparty na regułach aparat dostarczania zawartości |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md)  | | |**&#x2713;** przy użyciu [aparatu reguł](cdn-rules-engine.md) |
| Ustawienia pamięci podręcznej/nagłówka  |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md) | | |**&#x2713;** przy użyciu [aparatu reguł Premium](cdn-rules-engine.md) |
| Przekierowanie/zapis adresu URL |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md)  | | |**&#x2713;** przy użyciu [aparatu reguł Premium](cdn-rules-engine.md) |
| Reguły urządzeń przenośnych  |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md) | | |**&#x2713;** przy użyciu [aparatu reguł Premium](cdn-rules-engine.md) |
| [Buforowanie ciągu zapytania](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Podwójny stos protokołów IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Obsługa protokołu HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Zabezpieczenia** | **Microsoft — Standardowa** | **Akamai — Standardowa** | **Verizon — Standardowa** | **Verizon — Premium** | 
| Obsługa protokołu HTTPS z punktem końcowym usługi CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Protokół HTTPS domen niestandardowych](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, wymaga bezpośredniego rekordu CNAME do włączenia |**&#x2713;** |**&#x2713;** |
| [Obsługa niestandardowych nazw domen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrowanie geograficzne](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Uwierzytelnianie przy użyciu tokenów](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrona DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Użyj własnego certyfikatu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Obsługiwane wersje protokołu TLS | TLS 1,2, TLS 1.0/1.1 — [konfigurowalne](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analiza i raportowanie** | **Microsoft — Standardowa** | **Akamai — Standardowa** | **Verizon — Standardowa** | **Verizon — Premium** | 
| [Dzienniki diagnostyczne platformy Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Podstawowe raporty z Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Raporty niestandardowe z usługi Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statystyki w czasie rzeczywistym](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Wydajność węzła brzegowego](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alerty w czasie rzeczywistym](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Łatwość użycia** | **Microsoft — Standardowa** | **Akamai — Standardowa** | **Verizon — Standardowa** | **Verizon — Premium** | 
| Łatwa integracja z usługami platformy Azure, takimi jak [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) i [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Zarządzanie za pomocą [interfejsu API REST](/rest/api/cdn/), [platformy .NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)lub [programu PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Typy kompresji MIME](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Tylko domyślne |Konfigurowalne |Konfigurowalne  |Konfigurowalne  |
| Kodowania kompresji  |gzip, brotli |gzip |gzip, Wklęśnięcie, BZIP2, brotili  |gzip, Wklęśnięcie, BZIP2, brotili  |

## <a name="migration"></a>Migracja

Aby uzyskać informacje na temat migracji profilu **Azure CDN Standard from Verizon** do profilu **Azure CDN Premium from Verizon**, zobacz artykuł [Migrate an Azure CDN profile from Standard Verizon to Premium Verizon (Migrowanie profilu usługi Azure CDN firmy Verizon z warstwy Standardowa do warstwy Premium)](cdn-migrate.md). 

> [!NOTE]
> Istnieje ścieżka uaktualnienia ze standardowego Verizon do warstwy Premium Verizon, w tej chwili nie istnieje mechanizm konwersji między innymi produktami.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure CDN](cdn-overview.md).



