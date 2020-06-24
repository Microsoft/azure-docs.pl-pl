---
title: Pojęcia i definicje poleceń niestandardowych — usługa mowy
titleSuffix: Azure Cognitive Services
description: W tym artykule omówiono koncepcje i definicje dotyczące aplikacji poleceń niestandardowych.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5a5e3590e0e184633760f080dfd3402ed75bd48e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307674"
---
# <a name="custom-commands-concepts-and-definitions"></a>Pojęcia i definicje poleceń niestandardowych

Ten artykuł służy jako dokumentacja dotycząca pojęć i definicji dla aplikacji poleceń niestandardowych.

## <a name="commands-configuration"></a>Konfiguracja poleceń
Polecenia są podstawowymi blokami konstrukcyjnymi aplikacji poleceń niestandardowych. Polecenie jest zestawem konfiguracji wymaganych do wykonania określonego zadania zdefiniowanego przez użytkownika.

### <a name="example-sentences"></a>Przykładowe zdania
Przykład wyrażenia długości to zestaw przykładów, które użytkownik może powiedzieć, aby wyzwolić konkretne polecenie. Należy pamiętać, że należy podać tylko przykład wyrażenia długości, a nie wyczerpującą listę. 

### <a name="parameters"></a>Parametry
Parametry są wymagane przez polecenia do wykonania zadania. W scenariuszach złożonych parametry można także użyć do zdefiniowania warunków, które wyzwalają akcje niestandardowe.

### <a name="completion-rules"></a>Reguły uzupełniania
Seria reguł do wykonania, gdy polecenie jest gotowe do spełnienia, tj. po spełnieniu wszystkich warunków reguł.

### <a name="interaction-rules"></a>Reguły interakcji
Dodatkowe reguły do obsługi bardziej szczegółowych lub złożonych sytuacji. Możesz dodać dodatkowe walidacje lub skonfigurować zaawansowane funkcje, takie jak potwierdzenia lub korekty jednoetapowej. Możesz również utworzyć własne niestandardowe reguły interakcji.

## <a name="parameters-configuration"></a>Konfiguracja parametrów

Parametry są wymagane przez polecenia do wykonania zadania. W scenariuszach złożonych parametry można także użyć do zdefiniowania warunków, które wyzwalają akcje niestandardowe.

### <a name="name"></a>Nazwa
Parametr jest identyfikowany przez właściwość Name. Należy zawsze nadać opisową nazwę parametru. Parametr może być określony między różnymi sekcjami, takimi jak podczas konstruowania warunków, odpowiedzi na mowę lub innych akcjach.
 
### <a name="isglobal"></a>IsGlobal
Pole wyboru wskazujące, czy zakres tego parametru jest współużytkowany przez wszystkie polecenia w aplikacji. Jeśli parametr jest globalny, jego wartość może być potencjalnie dostarczana z dowolnego zakresu poleceń i po przypisaniu wartości — może być odwołuje się do któregokolwiek z poleceń. 

### <a name="required"></a>Wymagane
Pole wyboru wskazujące, czy wartość tego parametru jest wymagana do realizacji/uzupełniania poleceń. Musisz skonfigurować odpowiedzi, aby monitować użytkownika o podanie wartości, jeśli parametr jest oznaczony jako wymagany.

### <a name="type"></a>Typ
Polecenia niestandardowe obsługują następujące typy parametrów:


* DateTime
* Lokalizacja geograficzna
* Liczba
* Ciąg

Wszystkie te typy parametrów obsługują konfigurację wartości domyślnych, którą można skonfigurować z poziomu portalu.

### <a name="configuration"></a>Konfiguracja
Konfiguracja jest właściwością parametru zdefiniowaną tylko dla typu: String. Poniżej przedstawiono obsługiwane wartości
* Brak
* Zaakceptuj pełne dane wejściowe: włączenie tej opcji, parametr akceptuje wszelkie wypowiedź danych wejściowych, jest to przydatne, gdy użytkownik potrzebuje parametru z pełnym wypowiedź. na przykład adresy pocztowe.
* Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu zewnętrznego: służy do konfigurowania parametrów, które mogą przyjmować różne wartości. np. Sales Catalog. W takim przypadku katalog jest hostowany w zewnętrznym punkcie końcowym sieci Web i można go skonfigurować niezależnie.
* Akceptuj wstępnie zdefiniowane wartości wejściowe z wykazu wewnętrznego: służy do konfigurowania parametru, który może przyjmować kilka wartości. W tym przypadku należy skonfigurować wartości w programie Speech Studio.


### <a name="validation"></a>Walidacja
Walidacje to konstrukcje mające zastosowanie do niektórych typów parametrów, które umożliwiają konfigurowanie ograniczeń dotyczących wartości parametru. Obecnie polecenia niestandardowe obsługują walidacje następujących typów parametrów:
* DateTime
* Liczba

## <a name="rules-configuration"></a>Konfiguracja reguł
Reguła w poleceniach niestandardowych jest definiowana przez zestaw **warunków**, które po spełnieniu spowoduje wykonanie zestawu **akcji**. Reguła umożliwia również skonfigurowanie **stanu po wykonaniu** i **oczekiwania** na następny ruch.

### <a name="types"></a>Typy
Polecenia niestandardowe obsługują następujące kategorie reguł:
* Reguły uzupełniania

    Lista reguł do wykonania przy realizacji polecenia. Wszystkie reguły skonfigurowane w tej sekcji, dla których warunki są spełnione, zostaną wykonane.
    
* Reguły interakcji

    Reguły interakcji mogą służyć do konfigurowania dodatkowych niestandardowych walidacji, potwierdzeń, korekty jednoetapowej lub do wykonywania innych niestandardowych logiki okna dialogowego. Te reguły są oceniane przy każdym włączeniu przetwarzania i mogą być używane do wyzwalania reguł uzupełniania.

Różne akcje skonfigurowane w ramach reguły są wykonywane w kolejności, w jakiej są wyświetlane w portalu tworzenia.
    
### <a name="conditions"></a>Warunki
Zestaw warunków, które muszą zostać spełnione, aby reguła została wykonana. Warunkiem reguł mogą być następujące typy:
* Wartość parametru Equals: wartość skonfigurowanego parametru jest równa określonej wartości.
* Brak wartości parametru: skonfigurowane parametry nie powinny mieć żadnej wartości.
* Wymagane parametry: skonfigurowany parametr ma wartość.
* Wszystkie wymagane parametry: wszystkie parametry, które zostały oznaczone jako wymagane, mają wartość.
* Zaktualizowane parametry: co najmniej jedna wartość parametru została zaktualizowana w wyniku przetwarzania bieżących danych wejściowych (wypowiedź/Activity).
* Potwierdzenie zakończyło się pomyślnie: dane wejściowe wypowiedź/Activity zostały pomyślnie potwierdzone (tak).
* Odmowa potwierdzenia: dane wejściowe wypowiedź/Activity były pomyślnym potwierdzeniem (No).
* Należy zaktualizować poprzednie polecenie: ten warunek jest używany w wystąpieniach, jeśli chcesz przechwytywać zaprzeczenie potwierdzenia wraz z aktualizacją. W tle jest skonfigurowany do momentu, w którym aparat okna dialogowego wykrywa negatywne potwierdzenie, w przypadku którego zamiar jest taki sam jak poprzedni, a użytkownik odpowiedział z aktualizacją.

### <a name="actions"></a>Akcje
* Wyślij odpowiedź na mowę: Wyślij odpowiedź na mowę z powrotem do klienta.
* Zaktualizuj wartość parametru: zaktualizuj wartość parametru polecenia do określonej wartości.
* Wyczyść wartość parametru Wyczyść wartość parametru polecenia.
* Wywołaj punkt końcowy sieci Web: nawiązać połączenie z punktem końcowym sieci Web.
* Wyślij działanie do klienta: Wyślij do klienta niestandardowe działanie.

### <a name="expectations"></a>Oczekiwania
Oczekiwania są używane do konfigurowania wskazówek dotyczących przetwarzania następnych danych wejściowych użytkownika. Obsługiwane są następujące typy:
* Oczekiwano potwierdzenia od użytkownika: określa, że aplikacja oczekuje potwierdzenia (tak/nie) dla następnego danych wejściowych użytkownika.
* Oczekiwano parametrów wejściowych od użytkownika: Określ co najmniej jeden parametr polecenia, którego aplikacja oczekuje od danych wejściowych użytkownika.

### <a name="post-execution-state"></a>Stan po wykonaniu
Stan okna dialogowego po przetworzeniu bieżącego danych wejściowych (wypowiedź/Activity). Są to następujące typy:
* Polecenie zostało wykonane: Ukończ polecenie i nie zostaną przetworzone żadne dodatkowe reguły polecenia.
* Wykonaj reguły uzupełniania: wykonaj wszystkie prawidłowe reguły uzupełniania.
* Zaczekaj na dane wejściowe użytkownika: Poczekaj na następne dane wejściowe użytkownika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zobacz przykłady w witrynie GitHub](https://aka.ms/speech/cc-samples)
