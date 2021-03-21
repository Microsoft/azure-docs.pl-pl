---
title: Skonfiguruj warunki zgodności zestawu reguł Standard/Premium platformy Azure
description: Ten artykuł zawiera listę różnych warunków dopasowania dostępnych w zestawie reguł Standard/Premium platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100041"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Warunki dopasowania zestawu reguł (wersja zapoznawcza) usługi Azure Front-Standard/Premium

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W tym samouczku pokazano, jak utworzyć zestaw reguł przy użyciu pierwszego zestawu reguł w Azure Portal. W [zestawie reguł](concept-rule-set.md)Standard/Premium platformy Azure w warstwie Standardowa reguła składa się z co najmniej zero warunków dopasowania i akcji. Ten artykuł zawiera szczegółowe opisy warunków dopasowania, których można użyć w zestawie reguł Standard/Premium platformy Azure.

Pierwszą częścią reguły jest warunek dopasowania lub zestaw warunków zgodności. Reguła może składać się z maksymalnie 10 warunków dopasowywania. Warunek dopasowania służy do identyfikowania określonych typów żądań, dla których wykonywane są określone akcje. Jeśli używasz wielu warunków dopasowywania, warunki dopasowania są pogrupowane przy użyciu i logiki. Dla wszystkich warunków dopasowania, które obsługują wiele wartości (zanotowanych jako "rozdzielone spacjami"), przyjęto operator "OR".

Można na przykład użyć warunku dopasowywania do:

* Filtrowanie żądań na podstawie określonego adresu IP, kraju lub regionu.
* Filtruj żądania według informacji nagłówka.
* Filtrowanie żądań z urządzeń przenośnych lub urządzeń stacjonarnych.
* Filtrowanie żądań z nazwy pliku żądania i rozszerzenia pliku.
* Filtrowanie żądań z adresu URL żądania, protokołu, ścieżki, ciągu zapytania, post args itd.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Następujące warunki dopasowania są dostępne do użycia w zestawie reguł Standard/Premium platformy Azure dla warstwy Standardowa:

## <a name="device-type"></a>Typ urządzenia

Identyfikuje żądania wysyłane z urządzenia przenośnego lub urządzenia stacjonarnego.  

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | Urządzenia przenośne, komputery stacjonarne

## <a name="post-argument"></a>Opublikuj argument

Identyfikuje żądania na podstawie argumentów zdefiniowanych dla metody POST Request, która jest używana w żądaniu.

#### <a name="required-fields"></a>Pola wymagane

Nazwa argumentu | Operator | Wartość argumentu | Przekształcanie wielkości liter
--------------|----------|----------------|---------------
Ciąg | [Lista operatorów](#operator-list) | String, int | Małe litery, wielkie litery

## <a name="query-string"></a>Ciąg zapytania

Identyfikuje żądania zawierające określony parametr ciągu zapytania. Ten parametr jest ustawiony na wartość zgodną z określonym wzorcem. Parametry ciągu zapytania (na przykład **parametr = wartość**) w adresie URL żądania określają, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje parametr ciągu zapytania według jego nazwy i akceptuje co najmniej jedną wartość wartości parametru.

#### <a name="required-fields"></a>Pola wymagane

Operator | Ciąg zapytania | Przekształcanie wielkości liter
---------|--------------|---------------
[Lista operatorów](#operator-list) | String, int | Małe litery, wielkie litery

## <a name="remote-address"></a>Adres zdalny

Identyfikuje żądania na podstawie lokalizacji lub adresu IP osoby żądającej.

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|-----------------
Dopasowanie geograficzne | Numer kierunkowy kraju
Dopasowanie adresu IP | Adres IP (rozdzielone spacjami)
Brak dopasowania geograficznego | Numer kierunkowy kraju
Niezgodność adresów IP | Adres IP (rozdzielone spacjami)

#### <a name="key-information"></a>Informacje o kluczu

* Użyj notacji CIDR.
* W przypadku wielu adresów IP i bloków adresów IP jest obsługiwana logika "OR".
    * **Przykład IPv4**: Jeśli dodasz dwa adresy IP *1.2.3.4* i *10.20.30.40*, warunek jest dopasowywany, jeśli jakieś żądania, które dotarły do adresu 1.2.3.4 lub 10.20.30.40.
    * **Przykład IPv6**: Jeśli dodasz dwa adresy IP *1:2:3:4:5:6:7:8* i *10:20:30:40:50:60:70:80*, warunek jest dopasowywany, jeśli jakieś żądania, które dotarły do adresu 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
* Składnia bloku adresów IP to podstawowy adres IP, po którym następuje ukośnik i rozmiar prefiksu. Na przykład:
    * **Przykład IPv4**: *5.5.5.64/26* dopasowuje wszystkie żądania odbierane z adresów 5.5.5.64 przez 5.5.5.127.
    * **Przykład IPv6**: *1:2:3:/48* dopasowuje wszystkie żądania, które nadeszły z adresów 1:2:3:0:0:0:0:0 do 1:2:3: FFFF: FFFF: FFFF: FFFF: FFFF.

## <a name="request-body"></a>Treść żądania

Identyfikuje żądania na podstawie określonego tekstu, który pojawia się w treści żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Treść żądania | Przekształcanie wielkości liter
---------|--------------|---------------
[Lista operatorów](#operator-list) | String, int | Małe litery, wielkie litery

## <a name="request-header"></a>Nagłówek żądania

Identyfikuje żądania, które używają określonego nagłówka w żądaniu.

#### <a name="required-fields"></a>Pola wymagane

Nazwa nagłówka | Operator | Wartość nagłówka | Przekształcanie wielkości liter
------------|----------|--------------|---------------
Ciąg | [Lista operatorów](#operator-list) | String, int | Małe litery, wielkie litery

## <a name="request-method"></a>Metoda żądania

Identyfikuje żądania wykorzystujące określoną metodę żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | GET, POST, PUT, DELETE, GŁOWY, OPTIONS, TRACE

#### <a name="key-information"></a>Informacje o kluczu

Tylko Metoda GET Request może generować zawartość z pamięci podręcznej w ramach zewnętrznych drzwi platformy Azure. Wszystkie pozostałe metody żądań są przekazywane za pomocą sieci.

## <a name="request-protocol"></a>Protokół żądania

Identyfikuje żądania używające określonego używanego protokołu.

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | HTTP, HTTPS

## <a name="request-url"></a>Adres URL żądania

Identyfikuje żądania zgodne z określonym adresem URL.

#### <a name="required-fields"></a>Pola wymagane

Operator | Adres URL żądania | Przekształcanie wielkości liter
---------|-------------|---------------
[Lista operatorów](#operator-list) | String, int | Małe litery, wielkie litery

#### <a name="key-information"></a>Informacje o kluczu

Jeśli używasz tego warunku reguły, pamiętaj o uwzględnieniu informacji o protokole. Na przykład: *https://www . \<yourdomain\> . Model com*.

## <a name="request-file-extension"></a>Rozszerzenie pliku żądania

Identyfikuje żądania, które zawierają określone rozszerzenie pliku w nazwie pliku w żądającym adresie URL.

#### <a name="required-fields"></a>Pola wymagane

Operator | Wewnętrzny | Przekształcanie wielkości liter
---------|-----------|---------------
[Lista operatorów](#operator-list)  | String, int | Małe litery, wielkie litery

#### <a name="key-information"></a>Informacje o kluczu

W przypadku rozszerzenia nie należy umieszczać wiodących kropek; na przykład użyj *HTML* zamiast *. html*.

## <a name="request-file-name"></a>Nazwa pliku żądania

Identyfikuje żądania, które zawierają określoną nazwę pliku w adresie URL żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Nazwa pliku | Przekształcanie wielkości liter
---------|-----------|---------------
[Lista operatorów](#operator-list)| String, int | Małe litery, wielkie litery

## <a name="request-path"></a>Ścieżka żądania

Identyfikuje żądania, które zawierają określoną ścieżkę w adresie URL żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Wartość | Przekształcanie wielkości liter
---------|-------|---------------
[Lista operatorów](#operator-list) | String, int | Małe litery, wielkie litery

## <a name="operator-list"></a><a name = "operator-list"></a>Lista operatorów

Dla reguł, które akceptują wartości z standardowej listy operatorów, są prawidłowe następujące operatory:

* Dowolne
* Równa się
* Contains
* Zaczyna się od
* Kończący się na
* Mniejsze niż
* Mniejsze niż lub równe
* Większe niż
* Większe niż lub równe
* Nie wszystkie
* Nie zawiera
* Nie zaczyna się od
* Nie kończący się na
* Nie mniejsze niż
* Nie mniejsze niż lub równe
* Nie większe niż
* Nie większe niż lub równe
* Wyrażenie regularne

W przypadku operatorów liczbowych, takich jak *mniejsza niż* i *większa niż lub równa*, użyte porównanie jest zależne od długości. Wartość w warunku dopasowywania powinna być liczbą całkowitą, która jest równa długości, którą chcesz porównać.

## <a name="regular-expression"></a>Wyrażenie regularne

Wyrażenie regularne nie obsługuje następujących operacji:

* Odwołania wsteczne i przechwytywanie podwyrażeń
* Dowolne potwierdzenia o zerowej szerokości
* Odwołania podprocedury i wzorce cykliczne
* Wzorce warunkowe
* Zlecenia kontroli wycofywania
* \C jednobajtowa dyrektywa
* Dyrektywa dopasowania nowego wiersza
* \K początek dyrektywy resetowania dopasowania
* Objaśnienia i kod osadzony
* Grupowanie niepodzielne i Kwantyfikatory Possessive

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zestawie reguł](concept-rule-set.md).
* Dowiedz się, jak [skonfigurować swój pierwszy zestaw reguł](how-to-configure-rule-set.md).
* Dowiedz się więcej o [akcjach zestawu reguł](concept-rule-set-actions.md).
