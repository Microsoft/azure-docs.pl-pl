---
title: Aparaty reguł dla drzwi frontonu platformy Azure spełniają warunki
description: Ten artykuł zawiera listę różnych warunków dopasowania dostępnych w aparacie reguł dla drzwi platformy Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 0e874ae3d29f4143a4f8a9275d5ffcde48d08e6d
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569768"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Warunki dopasowania aparatu do usługi Azure Front drzwiczk

W [aparacie reguł AFD](front-door-rules-engine.md)reguła składa się z zero lub więcej warunków dopasowania i akcji. Ten artykuł zawiera szczegółowe opisy warunków dopasowania, których można użyć w aparacie reguł AFD.

Pierwszą częścią reguły jest warunek dopasowania lub zestaw warunków zgodności. Reguła może składać się z maksymalnie 10 warunków dopasowywania. Warunek dopasowania służy do identyfikowania określonych typów żądań, dla których wykonywane są określone akcje. Jeśli używasz wielu warunków dopasowywania, warunki dopasowania są pogrupowane przy użyciu i logiki. Dla wszystkich warunków dopasowania, które obsługują wiele wartości (zanotowanych jako "rozdzielone spacjami"), przyjęto operator "OR".

Można na przykład użyć warunku dopasowywania do:

- Filtrowanie żądań na podstawie określonego adresu IP, kraju lub regionu.
- Filtruj żądania według informacji nagłówka.
- Filtrowanie żądań z urządzeń przenośnych lub urządzeń stacjonarnych.

Poniższe warunki dopasowania są dostępne do użycia w aparacie reguł dla drzwi platformy Azure.  

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
Ciąg | [Standardowa lista operatorów](#standard-operator-list) | String, int | Małe litery, wielkie litery, przycinanie, usuwanie białych znaków, kodowanie URL, dekodowanie adresu URL

## <a name="query-string"></a>Ciąg zapytania

Identyfikuje żądania zawierające określony parametr ciągu zapytania. Ten parametr jest ustawiony na wartość zgodną z określonym wzorcem. Parametry ciągu zapytania (na przykład **parametr = wartość**) w adresie URL żądania określają, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje parametr ciągu zapytania według jego nazwy i akceptuje co najmniej jedną wartość wartości parametru.

#### <a name="required-fields"></a>Pola wymagane

Operator | Ciąg zapytania | Przekształcanie wielkości liter
---------|--------------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Małe litery, wielkie litery, przycinanie, usuwanie białych znaków, kodowanie URL, dekodowanie adresu URL

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

- Użyj notacji CIDR.
- Aby określić wiele adresów IP i bloków adresów IP, należy użyć pojedynczej spacji między wartościami:
  - **Przykład IPv4**: *1.2.3.4 10.20.30.40* dopasowuje wszystkie żądania, które dotarły do adresu 1.2.3.4 lub 10.20.30.40.
  - **Przykład IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* dopasowuje wszystkie żądania, które nadeszły z adresu 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
- Składnia bloku adresów IP to podstawowy adres IP, po którym następuje ukośnik i rozmiar prefiksu. Na przykład:
  - **Przykład IPv4**: *5.5.5.64/26* dopasowuje wszystkie żądania odbierane z adresów 5.5.5.64 przez 5.5.5.127.
  - **Przykład IPv6**: *1:2:3:/48* dopasowuje wszystkie żądania, które nadeszły z adresów 1:2:3:0:0:0:0:0 do 1:2:3: FFFF: FFFF: FFFF: FFFF: FFFF.

## <a name="request-body"></a>Treść żądania

Identyfikuje żądania na podstawie określonego tekstu, który pojawia się w treści żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Treść żądania | Przekształcanie wielkości liter
---------|--------------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Małe litery, wielkie litery, przycinanie, usuwanie białych znaków, kodowanie URL, dekodowanie adresu URL

## <a name="request-header"></a>Nagłówek żądania

Identyfikuje żądania, które używają określonego nagłówka w żądaniu.

#### <a name="required-fields"></a>Pola wymagane

Nazwa nagłówka | Operator | Wartość nagłówka | Przekształcanie wielkości liter
------------|----------|--------------|---------------
Ciąg | [Standardowa lista operatorów](#standard-operator-list) | String, int | Małe litery, wielkie litery, przycinanie, usuwanie białych znaków, kodowanie URL, dekodowanie adresu URL

## <a name="request-method"></a>Metoda żądania

Identyfikuje żądania wykorzystujące określoną metodę żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Obsługiwane wartości
---------|----------------
Równa się, nie równa się | GET, POST, PUT, DELETE, GŁOWY, OPTIONS, TRACE

#### <a name="key-information"></a>Informacje o kluczu

- Tylko Metoda GET Request może generować zawartość z pamięci podręcznej w ramach zewnętrznych drzwi platformy Azure. Wszystkie pozostałe metody żądań są przekazywane za pomocą sieci. 

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
[Standardowa lista operatorów](#standard-operator-list) | String, int | Małe litery, wielkie litery, przycinanie, usuwanie białych znaków, kodowanie URL, dekodowanie adresu URL

#### <a name="key-information"></a>Informacje o kluczu

- Jeśli używasz tego warunku reguły, pamiętaj o uwzględnieniu informacji o protokole. Na przykład: * https://www . \<yourdomain\> . Model com*.

## <a name="request-file-extension"></a>Rozszerzenie pliku żądania

Identyfikuje żądania, które zawierają określone rozszerzenie pliku w nazwie pliku w żądającym adresie URL.

#### <a name="required-fields"></a>Pola wymagane

Operator | Wewnętrzny | Przekształcanie wielkości liter
---------|-----------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Małe litery, wielkie litery, przycinanie, usuwanie białych znaków, kodowanie URL, dekodowanie adresu URL

#### <a name="key-information"></a>Informacje o kluczu

- W przypadku rozszerzenia nie należy umieszczać wiodących kropek; na przykład użyj *HTML* zamiast *. html*.

## <a name="request-file-name"></a>Nazwa pliku żądania

Identyfikuje żądania, które zawierają określoną nazwę pliku w adresie URL żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Nazwa pliku | Przekształcanie wielkości liter
---------|-----------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Małe litery, wielkie litery, przycinanie, usuwanie białych znaków, kodowanie URL, dekodowanie adresu URL

#### <a name="key-information"></a>Informacje o kluczu

- Aby określić wiele nazw plików, oddziel poszczególne nazwy plików przez naciśnięcie klawisza ENTER. 

## <a name="request-path"></a>Ścieżka żądania

Identyfikuje żądania, które zawierają określoną ścieżkę w adresie URL żądania.

#### <a name="required-fields"></a>Pola wymagane

Operator | Wartość | Przekształcanie wielkości liter
---------|-------|---------------
[Standardowa lista operatorów](#standard-operator-list) | String, int | Małe litery, wielkie litery, przycinanie, usuwanie białych znaków, kodowanie URL, dekodowanie adresu URL

## <a name="standard-operator-list"></a>Standardowa lista operatorów

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

W przypadku operatorów liczbowych, takich jak *mniejsza niż* i *większa niż lub równa*, użyte porównanie jest zależne od długości. Wartość w warunku dopasowywania powinna być liczbą całkowitą, która jest równa długości, którą chcesz porównać. 


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować pierwszy [aparat reguł](front-door-tutorial-rules-engine.md). 
- Dowiedz się więcej na temat [akcji aparatu reguł](front-door-rules-engine-actions.md)
- Dowiedz się więcej o [aparacie reguł dla drzwi platformy Azure](front-door-rules-engine.md)
