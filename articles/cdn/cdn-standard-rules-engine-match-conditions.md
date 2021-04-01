---
title: Dopasuj warunki w aparacie reguł standardowych dla Azure CDN | Microsoft Docs
description: Dokumentacja referencyjna dotycząca warunków dopasowania w aparacie standardowych reguł dla platformy Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81259934"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Dopasuj warunki w aparacie reguł standardowych dla Azure CDN

W [aparacie reguł standardowych](cdn-standard-rules-engine.md) dla platformy Azure Content Delivery Network (Azure CDN) reguła zawiera co najmniej jeden warunek dopasowania i akcję. Ten artykuł zawiera szczegółowe opisy warunków dopasowania, których można użyć w aparacie reguł standardowych dla Azure CDN.

Pierwszą częścią reguły jest warunek dopasowania lub zestaw warunków zgodności. W aparacie reguł standardowych dla Azure CDN Każda reguła może mieć maksymalnie cztery warunki dopasowywania. Warunek dopasowania służy do identyfikowania określonych typów żądań, dla których wykonywane są określone akcje. Jeśli używasz wielu warunków dopasowywania, warunki dopasowania są pogrupowane przy użyciu i logiki.

Można na przykład użyć warunku dopasowywania do:

- Filtrowanie żądań na podstawie określonego adresu IP, kraju lub regionu.
- Filtruj żądania według informacji nagłówka.
- Filtrowanie żądań z urządzeń przenośnych lub urządzeń stacjonarnych.

## <a name="match-conditions"></a>Warunki dopasowania

Poniższe warunki dopasowania są dostępne do użycia w aparacie reguł standardowych dla Azure CDN. 

### <a name="device-type"></a>Typ urządzenia 

Identyfikuje żądania wysyłane z urządzenia przenośnego lub urządzenia stacjonarnego.  

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | Urządzenia przenośne, komputery stacjonarne

### <a name="http-version"></a>Wersja protokołu HTTP

Identyfikuje żądania na podstawie wersji protokołu HTTP żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | 2,0, 1,1, 1,0, 0,9, wszystkie

### <a name="request-cookies"></a>Pliki cookie żądania

Identyfikuje żądania na podstawie informacji o pliku cookie w żądaniu przychodzącym.

#### <a name="required-fields"></a>Pola wymagane

Nazwa pliku cookie | Operator | Wartość cookie | Przekształcanie wielkości liter
------------|----------|--------------|---------------
Ciąg | [Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

#### <a name="key-information"></a>Informacje o kluczu

- W przypadku określenia nazwy pliku cookie nie można używać symboli wieloznacznych (w tym gwiazdek \* ). należy użyć dokładnej nazwy pliku cookie.
- Dla każdego wystąpienia tego warunku dopasowywania można określić tylko jedną nazwę pliku cookie.
- Porównania nazw plików cookie nie uwzględniają wielkości liter.
- Aby określić wiele wartości plików cookie, należy użyć pojedynczej spacji między wartościami plików cookie. 
- Wartości plików cookie mogą korzystać z symboli wieloznacznych.
- Jeśli nie określono wartości symbolu wieloznacznego, tylko dokładne dopasowanie spełnia ten warunek dopasowania. Na przykład "wartość" będzie pasować do wartości "value", ale nie "wartość1". 

### <a name="post-argument"></a>Opublikuj argument

Identyfikuje żądania na podstawie argumentów zdefiniowanych dla metody POST Request, która jest używana w żądaniu. 

#### <a name="required-fields"></a>Pola wymagane

Nazwa argumentu | Operator | Wartość argumentu | Przekształcanie wielkości liter
--------------|----------|----------------|---------------
Ciąg | [Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

### <a name="query-string"></a>Ciąg zapytania

Identyfikuje żądania zawierające określony parametr ciągu zapytania. Ten parametr jest ustawiony na wartość zgodną z określonym wzorcem. Parametry ciągu zapytania (na przykład **parametr = wartość**) w adresie URL żądania określają, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje parametr ciągu zapytania według jego nazwy i akceptuje co najmniej jedną wartość wartości parametru.

#### <a name="required-fields"></a>Pola wymagane

Operator | Ciąg zapytania | Przekształcanie wielkości liter
---------|--------------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

### <a name="remote-address"></a>Adres zdalny

Identyfikuje żądania na podstawie lokalizacji lub adresu IP osoby żądającej.

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|-----------------
Dowolne | Nie dotyczy
Dopasowanie geograficzne | Numer kierunkowy kraju
Dopasowanie adresu IP | Adres IP (rozdzielone spacjami)
Nie wszystkie | Nie dotyczy
Brak dopasowania geograficznego | Numer kierunkowy kraju
Niezgodność adresów IP | Adres IP (rozdzielone spacjami)

#### <a name="key-information"></a>Informacje o kluczu

- Użyj notacji CIDR.
- Aby określić wiele adresów IP i bloków adresów IP, należy użyć pojedynczej spacji między wartościami:
  - **Przykład IPv4**: *1.2.3.4 10.20.30.40* dopasowuje wszystkie żądania, które dotarły do adresu 1.2.3.4 lub 10.20.30.40.
  - **Przykład IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* dopasowuje wszystkie żądania, które nadeszły z adresu 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
- Składnia bloku adresów IP to podstawowy adres IP, po którym następuje ukośnik i rozmiar prefiksu. Na przykład:
  - **Przykład IPv4**: *5.5.5.64/26* dopasowuje wszystkie żądania odbierane z adresów 5.5.5.64 przez 5.5.5.127.
  - **Przykład IPv6**: *1:2:3:/48* dopasowuje wszystkie żądania odbierane z adresów 1:2:3:0:0:0:0:0 do 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.

### <a name="request-body"></a>Treść żądania

Identyfikuje żądania na podstawie określonego tekstu, który pojawia się w treści żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Treść żądania | Przekształcanie wielkości liter
---------|--------------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

### <a name="request-header"></a>Nagłówek żądania

Identyfikuje żądania, które używają określonego nagłówka w żądaniu.

#### <a name="required-fields"></a>Pola wymagane

Nazwa nagłówka | Operator | Wartość nagłówka | Przekształcanie wielkości liter
------------|----------|--------------|---------------
Ciąg | [Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

### <a name="request-method"></a>Metoda żądania

Identyfikuje żądania wykorzystujące określoną metodę żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | GET, POST, PUT, DELETE, GŁOWY, OPTIONS, TRACE

#### <a name="key-information"></a>Informacje o kluczu

- Tylko Metoda GET Request może generować zawartość buforowaną w Azure CDN. Wszystkie pozostałe metody żądań są przekazywane za pomocą sieci. 

### <a name="request-protocol"></a>Protokół żądania

Identyfikuje żądania używające określonego używanego protokołu.

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | HTTP, HTTPS

### <a name="request-url"></a>Adres URL żądania

Identyfikuje żądania zgodne z określonym adresem URL.

#### <a name="required-fields"></a>Pola wymagane

Operator | Adres URL żądania | Przekształcanie wielkości liter
---------|-------------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

#### <a name="key-information"></a>Informacje o kluczu

- Jeśli używasz tego warunku reguły, pamiętaj o uwzględnieniu informacji o protokole. Na przykład: *https://www . \<yourdomain\> . Model com*.

### <a name="url-file-extension"></a>Rozszerzenie pliku adresu URL

Identyfikuje żądania, które zawierają określone rozszerzenie pliku w nazwie pliku w żądającym adresie URL.

#### <a name="required-fields"></a>Pola wymagane

Operator | Wewnętrzny | Przekształcanie wielkości liter
---------|-----------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

#### <a name="key-information"></a>Informacje o kluczu

- W przypadku rozszerzenia nie należy umieszczać wiodących kropek; na przykład użyj *HTML* zamiast *. html*.

### <a name="url-file-name"></a>Nazwa pliku adresu URL

Identyfikuje żądania, które zawierają określoną nazwę pliku w adresie URL żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Nazwa pliku | Przekształcanie wielkości liter
---------|-----------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

#### <a name="key-information"></a>Informacje o kluczu

- Aby określić wiele nazw plików, oddziel poszczególne nazwy plików pojedynczą spacją. 

### <a name="url-path"></a>Ścieżka adresu URL

Identyfikuje żądania, które zawierają określoną ścieżkę w adresie URL żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Wartość | Przekształcanie wielkości liter
---------|-------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Nie Przekształć, na wielkie, małe litery

#### <a name="key-information"></a>Informacje o kluczu

- Wartość nazwy pliku może korzystać z symboli wieloznacznych. Na przykład każdy wzorzec nazwy pliku może składać się z co najmniej jednej gwiazdki (*), gdzie każda Gwiazdka dopasowuje sekwencję składającą się z co najmniej jednego znaku.

## <a name="reference-for-rules-engine-match-conditions"></a>Dokumentacja warunków dopasowania aparatu reguł

### <a name="standard-operator-list"></a>Standardowa lista operatorów

Dla reguł, które akceptują wartości z standardowej listy operatorów, są prawidłowe następujące operatory:

- Dowolne
- Równa się 
- Contains 
- Zaczyna się od 
- Kończący się na 
- Mniejsze niż
- Mniejsze niż lub równe
- Większe niż
- Większe niż lub równe
- Nie wszystkie
- Nie zawiera
- Nie zaczyna się od 
- Nie kończący się na 
- Nie mniejsze niż
- Nie mniejsze niż lub równe
- Nie większe niż
- Nie większe niż lub równe

W przypadku operatorów liczbowych, takich jak *mniejsza niż* i *większa niż lub równa*, użyte porównanie jest zależne od długości. W takim przypadku wartość w warunku dopasowania powinna być liczbą całkowitą, która jest równa długości, którą chcesz porównać. 

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł standardowych](cdn-standard-rules-engine-reference.md)
- [Akcje w aparacie reguł standardowych](cdn-standard-rules-engine-actions.md)
- [Wymuszanie protokołu HTTPS za pomocą aparatu reguł standardowych](cdn-standard-rules-engine.md)
