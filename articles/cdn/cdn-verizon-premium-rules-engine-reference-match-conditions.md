---
title: Azure CDN z warunków dopasowania aparatu programu Verizon Premium
description: Dokumentacja referencyjna dotycząca platformy Azure Content Delivery Network z reguł Verizon w warstwie Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84323318"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN z warunków dopasowania aparatu programu Verizon Premium

W tym artykule przedstawiono szczegółowe opisy warunków dopasowania dostępne dla usługi Azure Content Delivery Network (CDN) z [aparatu reguł](cdn-verizon-premium-rules-engine.md)Verizon Premium.

Drugą częścią reguły jest warunek dopasowania. Warunek dopasowania służy do identyfikowania określonych typów żądań, dla których zostanie wykonany zestaw funkcji.

Można na przykład użyć warunku dopasowywania do:

- Filtrowanie żądań dotyczących zawartości w określonej lokalizacji.
- Filtruj żądania wygenerowane na podstawie określonego adresu IP lub kraju/regionu.
- Filtruj żądania według informacji nagłówka.

## <a name="match-conditions"></a><a name="top"></a>Warunki dopasowania

* [Always](#always) (Zawsze)
* [Urządzenie](#device)
* [Lokalizacja](#location)
* [Źródł](#origin)
* [Żądanie](#request)
* [Adres URL](#url)

### <a name="always"></a><a name="always"></a>Zawsze

[Warunek zawsze dopasowany](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) został zaprojektowany w celu zastosowania domyślnego zestawu funkcji dla wszystkich żądań.

### <a name="device"></a><a name="device"></a>Urządzenie

Te warunki dopasowania są przeznaczone do identyfikowania żądań opartych na agencie użytkownika klienta.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| Nazwa marki | Identyfikuje żądania, określając, czy nazwa marki urządzenia jest zgodna z: <br> **-** Określona wartość ([literał nazwy marki](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** Wyrażenie regularne ([Nazwa marki](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny nazwy marki](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| System operacyjny urządzenia | Identyfikuje żądania, czy system operacyjny urządzenia jest zgodny z: <br> **-** Określona wartość ([literał systemu operacyjnego urządzenia](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** Wyrażenie regularne ([regularnego systemu operacyjnego urządzenia](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny systemu operacyjnego urządzenia](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Wersja systemu operacyjnego urządzenia | Identyfikuje żądania, czy wersja systemu operacyjnego urządzenia jest zgodna z: <br> **-** Określona wartość ([literał wersji systemu operacyjnego urządzenia](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** Wyrażenie regularne (regularna[wersja systemu operacyjnego urządzenia](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny w wersji systemu operacyjnego urządzenia](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [Dwie orientacje?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Identyfikuje żądania, czy urządzenie obsługuje dwie orientacje. |
| Preferowany plik DTD HTML | Identyfikuje żądania, czy preferowany plik HTML urządzenia jest zgodny z: <br> **-** Określona wartość ([preferowany LITERAŁ DTD w kodzie HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** Wyrażenie regularne ([preferowane wyrażenie DTD języka HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** Konkretny wzorzec ([preferowany symbol wieloznaczny HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [Dekreślenie obrazu?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Identyfikuje żądania, czy urządzenie obsługuje obrazy kodowane algorytmem Base64. |
| [Jest systemem Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Identyfikuje żądania, czy urządzenie używa systemu operacyjnego Android. |
| [Czy jest to aplikacja?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | Identyfikuje żądania, określając, czy aplikacja natywna zażądała zawartości. |
| [Czy jest pełny pulpit?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Identyfikuje żądania, czy urządzenie zapewnia pełne środowisko pulpitu. |
| [Czy jest iOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Identyfikuje żądania, czy urządzenie używa systemu iOS. |
| [Jesteś robotem?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Identyfikuje żądania, czy urządzenie jest uznawane za zautomatyzowany klient HTTP (np. przeszukiwarki robota). |
| [Czy jest to inteligentna telewizja?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Identyfikuje żądania, czy urządzenie jest inteligentną TV. |
| [Czy telefon smartphone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Identyfikuje żądania, czy urządzenie jest smartfonem.
| [Jest tabletem?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Identyfikuje żądania, czy urządzenie jest tabletem. |
| [Czy jest ekran dotykowy?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Identyfikuje żądania, wskazując, czy podstawowe urządzenie wskazujące to ekran dotykowy. |
| [Czy Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Identyfikuje żądania, czy urządzenie jest urządzeniem z systemem Windows Mobile 6.5/Windows Phone 7 lub nowszym. |
| [Czy urządzenie bezprzewodowe jest?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Identyfikuje żądania, czy urządzenie jest bezprzewodowe. 
| Nazwa marketingowa | Identyfikuje żądania według nazwy marketingowej urządzenia zgodnej z: <br> **-** Określona wartość ([literał nazwy marketingowej](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** Wyrażenie regularne ([regularne nazwy marketingowe](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny nazwy marketingowej](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| Przeglądarka mobilna | Identyfikuje żądania na podstawie tego, czy przeglądarka urządzenia pasuje do: <br> **-** Określona wartość ([literał przeglądarki mobilnej](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** Wyrażenie regularne ([regularne przeglądarki dla urządzeń przenośnych](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny przeglądarki mobilnej](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Wersja przeglądarki mobilnej | Identyfikuje żądania, czy wersja przeglądarki urządzenia pasuje do: <br> **-** Określona wartość ([literał wersji przeglądarki mobilnej](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** Wyrażenie regularne (regularna[wersja przeglądarki mobilnej](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny wersji przeglądarki mobilnej](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Nazwa modelu | Identyfikuje żądania, czy nazwa modelu urządzenia pasuje do: <br> **-** Określona wartość ([literał nazwy modelu](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** Wyrażenie regularne ([Nazwa modelu wyrażenie regularne](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** Konkretny wzorzec ([Nazwa modelu — symbol wieloznaczny](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [Pobieranie progresywne?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Identyfikuje żądania, określając, czy urządzenie obsługuje pobieranie progresywne. |
| Data wydania | Identyfikuje żądania, określając, czy Data wydania urządzenia odpowiada: <br> **-** Określona wartość ([literał daty wydania](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** Wyrażenie regularne (wyrażenia[daty wydania](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol zastępczy daty wydania](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Wysokość rozwiązania](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Identyfikuje żądania według wysokości urządzenia. |
| [Szerokość rozdzielczości](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Identyfikuje żądania według szerokości urządzenia. |

**[Powrót do początku](#top)**

### <a name="location"></a><a name="location"></a>Przeniesienie

Te warunki dopasowania są przeznaczone do identyfikowania żądań na podstawie lokalizacji obiektu żądającego.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Numer AS](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Identyfikuje żądania pochodzące z określonej sieci. |
| Nazwa miasta | Identyfikuje żądania, które pochodzą z miasta, którego nazwa jest zgodna z nazwą: <br> **-** Określona wartość ([literał nazwy miasta](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** Wyrażenie regularne ([regularne nazwy miasta](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Kontynent](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Identyfikuje żądania, które pochodzą z określonych kontynentów. |
| [Kraj](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Identyfikuje żądania, które pochodzą z określonych krajów. |
| [Kod DMA](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Identyfikuje żądania, które pochodzą z określonych linii metra (wyznaczonych obszarów rynkowych). |
| [Rodziny](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | Identyfikuje żądania, które pochodzą z określonego Latitudes. |
| [Długość geograficzna](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | Identyfikuje żądania, które pochodzą z określonych długości geograficznej. |
| [Kod Metro](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Identyfikuje żądania, które pochodzą z określonych linii metra (wyznaczonych obszarów rynkowych). |
| [Kod pocztowy](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Identyfikuje żądania, które pochodzą z określonych kodów pocztowych. |
| [Kod regionu](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | Identyfikuje żądania, które pochodzą z określonych regionów. |

> [!NOTE]
> **Czy należy używać kodu Metro lub kodu DMA?** <br>
Oba te warunki dopasowania zapewniają tę samą funkcję. Jednak zaleca się użycie warunku dopasowywania kodu Metro do identyfikowania żądań przez DMA.

**[Powrót do początku](#top)**

### <a name="origin"></a><a name="origin"></a>Źródł

Te warunki dopasowania są przeznaczone do identyfikowania żądań, które wskazują magazyn CDN lub serwer pochodzenia klienta.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Źródło CDN](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Identyfikuje żądania dotyczące zawartości przechowywanej w magazynie CDN. |
| [Pochodzenie klienta](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Identyfikuje żądania dotyczące zawartości przechowywanej na określonym serwerze źródłowym klienta. |

**[Powrót do początku](#top)**

### <a name="request"></a><a name="request"></a>Żądanie

Te warunki dopasowania są przeznaczone do identyfikowania żądań na podstawie ich właściwości.

| Nazwa              | Przeznaczenie                                                                |
|-------------------|------------------------------------------------------------------------|
| [Adres IP klienta](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Identyfikuje żądania, które pochodzą z określonego adresu IP. |
| Parametr cookie  | Identyfikuje żądanie, czy zawiera plik cookie zgodny z: <br> **-** Określona wartość ([literał parametru cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** Wyrażenie regularne ([regularne parametrów cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** Konkretny wzorzec ([symbol wieloznaczny parametru cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [Krawędź CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | Identyfikuje żądania wskazujące na określoną krawędź CNAME. |
| Odwołuje się do domeny | Identyfikuje żądanie, czy został on określony przez nazwę hosta zgodną z: <br> **-** Określona wartość ([odwołanie do literału domeny](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** Konkretny wzorzec ([odwołujący się do symbolu wieloznacznego domeny](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| Nagłówek żądania | Identyfikuje żądanie, czy zawiera nagłówek, który jest zgodny z: <br> **-** Określona wartość ([literał nagłówka żądania](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** Wyrażenia regularne (wyrażenie regularne[nagłówka żądania](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny nagłówka żądania](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Request, metoda](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Identyfikuje żądania według metody HTTP. |
| [Schemat żądania](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Identyfikuje żądania według protokołu HTTP. |

**[Powrót do początku](#top)**

### <a name="url"></a><a name="url"></a>Adres URL

| Nazwa              | Przeznaczenie                                                                |
|-------------------|------------------------------------------------------------------------|
| Ścieżka adresu URL | Identyfikuje żądania, czy ścieżka względna, w tym filename, pasuje do: <br> **-** Określona wartość ([literał ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** Wyrażenie regularne (wyrażenia regularne[ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| Katalog ścieżki URL | Identyfikuje żądania, określając, czy ich ścieżka względna pasuje do: <br> **-** Określona wartość ([literał katalogu ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** Konkretny wzorzec ([symbol zastępczy katalogu ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| Rozszerzenie ścieżki URL | Identyfikuje żądania według tego, czy rozszerzenie pliku pasuje do: <br> **-** Określona wartość ([literał rozszerzenia ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny rozszerzenia ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| Nazwa pliku ścieżki URL | Identyfikuje żądania, czy ich nazwa pliku jest zgodna z: <br> **-** Określona wartość ([literał nazwy ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny nazwy pliku ścieżki URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| Zapytanie URL | Identyfikuje żądania według tego, czy ich ciąg zapytania jest zgodny z: <br> **-** Określona wartość ([literał zapytania URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** Wyrażenia regularne (wyrażenie regularne[zapytania URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny zapytania URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| Parametr zapytania URL | Identyfikuje żądania według tego, czy zawierają one parametr ciągu zapytania ustawiony na wartość zgodną z: <br> **-** Określona wartość ([literał parametru zapytania URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** Konkretny wzorzec ([symbol wieloznaczny parametru zapytania URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[Powrót do początku](#top)**

Najnowsze warunki dopasowania można znaleźć w [dokumentacji aparatu reguł Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio).

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Content Delivery Network](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
