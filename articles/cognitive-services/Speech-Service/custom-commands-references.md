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
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561101"
---
# <a name="custom-commands-concepts-and-definitions"></a>Pojęcia i definicje poleceń niestandardowych

Ten artykuł służy jako dokumentacja dotycząca pojęć i definicji dla aplikacji poleceń niestandardowych.

## <a name="commands-configuration"></a>Konfiguracja poleceń
Polecenia są podstawowymi blokami konstrukcyjnymi aplikacji poleceń niestandardowych. Polecenie to zestaw konfiguracji wymagany do wykonania konkretnego zadania zdefiniowanego przez użytkownika.

### <a name="example-sentences"></a>Przykładowe zdania
Przykład wyrażenia długości to zestaw przykładów, które użytkownik może powiedzieć, aby wyzwolić konkretne polecenie. Musisz podać tylko przykład wyrażenia długości, a nie wyczerpującą listę. 

### <a name="parameters"></a>Parametry
Parametry są wymagane przez polecenia do wykonania zadania. W złożonych scenariuszach parametry można także używać do definiowania warunków wyzwalających akcje niestandardowe.

### <a name="completion-rules"></a>Reguły uzupełniania
Reguły ukończenia są serią reguł, które mają zostać wykonane po przygotowaniu polecenia, na przykład po spełnieniu wszystkich warunków reguł.

### <a name="interaction-rules"></a>Reguły interakcji
Reguły interakcji są dodatkowymi regułami, które obsługują bardziej szczegółowe lub złożone sytuacje. Możesz dodać dodatkowe walidacje lub skonfigurować zaawansowane funkcje, takie jak potwierdzenia lub korekty jednoetapowej. Możesz również utworzyć własne niestandardowe reguły interakcji.

## <a name="parameters-configuration"></a>Konfiguracja parametrów

Parametry są wymagane przez polecenia do wykonania zadania. W złożonych scenariuszach parametry można także używać do definiowania warunków wyzwalających akcje niestandardowe.

### <a name="name"></a>Nazwa
Parametr jest identyfikowany przez właściwość Name. Należy zawsze nadać opisową nazwę parametru. Parametr może być określony w różnych sekcjach, na przykład podczas konstruowania warunków, odpowiedzi na mowę lub innych akcji.
 
### <a name="isglobal"></a>IsGlobal
To pole wyboru wskazuje, czy zakres tego parametru jest współużytkowany przez wszystkie polecenia w aplikacji. Jeśli parametr jest globalny, jego wartość może być potencjalnie poświadczona z dowolnego zakresu poleceń. Po przypisaniu wartości można ją odwoływać z dowolnego polecenia. 

### <a name="required"></a>Wymagane
To pole wyboru wskazuje, czy wartość tego parametru jest wymagana do realizacji lub uzupełniania poleceń. Musisz skonfigurować odpowiedzi, aby monitować użytkownika o podanie wartości, jeśli parametr jest oznaczony jako wymagany.

Należy pamiętać, że jeśli **parametr wymagany** ma mieć **wartość domyślną** , system nadal będzie monitował o wartość parametru.

### <a name="type"></a>Typ
Polecenia niestandardowe obsługują następujące typy parametrów:

* Data i godzina
* Lokalizacja geograficzna
* Liczba
* String

Wszystkie te typy parametrów, z wyjątkiem lokalizacji geograficznej, obsługują konfigurację wartości domyślnych, którą można skonfigurować z poziomu portalu.

### <a name="configuration"></a>Konfiguracja
Konfiguracja jest właściwością parametru zdefiniowaną tylko dla ciągu typu. Obsługiwane są następujące wartości:

* **Brak**.
* **Zaakceptuj pełne dane wejściowe** : po włączeniu parametr akceptuje wszelkie wypowiedź wejściowe. Ta opcja jest przydatna, gdy użytkownik potrzebuje parametru z pełnym wypowiedź. Przykładem są adresy pocztowe.
* **Zaakceptuj wstępnie zdefiniowane wartości wejściowe z wykazu zewnętrznego** : Ta wartość jest używana do konfigurowania parametru, który może przyjmować różne wartości. Przykładem jest katalog sprzedaży. W takim przypadku katalog jest hostowany w zewnętrznym punkcie końcowym sieci Web i można go skonfigurować niezależnie.
* **Zaakceptuj wstępnie zdefiniowane wartości wejściowe z wykazu wewnętrznego** : Ta wartość jest używana do konfigurowania parametru, który może przyjmować kilka wartości. W takim przypadku należy skonfigurować wartości w programie Speech Studio.


### <a name="validation"></a>Walidacja
Walidacje to konstrukcje mające zastosowanie do niektórych typów parametrów, które umożliwiają konfigurowanie ograniczeń dotyczących wartości parametru. Obecnie polecenia niestandardowe obsługują walidacje następujących typów parametrów:

* Data i godzina
* Liczba

## <a name="rules-configuration"></a>Konfiguracja reguł
Reguła w poleceniach niestandardowych jest definiowana przez zestaw *warunków* , które po spełnieniu, wykonywania zestawu *akcji*. Reguły umożliwiają również Konfigurowanie *stanu po wykonaniu* i *oczekiwań* przy następnym włączeniu.

### <a name="types"></a>Typy
Polecenia niestandardowe obsługują następujące kategorie reguł:

* **Reguły uzupełniania** : te reguły muszą zostać wykonane przy realizacji polecenia. Wszystkie reguły skonfigurowane w tej sekcji, dla których warunki są spełnione, zostaną wykonane. 
* **Reguły interakcji** : te reguły mogą służyć do konfigurowania dodatkowych niestandardowych walidacji, potwierdzeń i korekty jednoetapowej lub do wykonywania jakichkolwiek innych niestandardowych logiki okna dialogowego. Reguły interakcji są oceniane przy każdym włączeniu przetwarzania i mogą być używane do wyzwalania reguł uzupełniania.

Różne akcje skonfigurowane w ramach reguły są wykonywane w kolejności, w jakiej są wyświetlane w portalu tworzenia.

### <a name="conditions"></a>Warunki
Warunki są wymagania, które muszą zostać spełnione, aby reguła mogła zostać wykonana. Warunkiem reguł mogą być następujące typy:

* **Wartość parametru Equals** : wartość skonfigurowanego parametru jest równa określonej wartości.
* **Brak wartości parametru** : skonfigurowane parametry nie powinny mieć żadnej wartości.
* **Wymagane parametry** : skonfigurowany parametr ma wartość.
* **Wszystkie wymagane parametry** : wszystkie parametry, które zostały oznaczone jako wymagane, mają wartość.
* **Zaktualizowane parametry** : co najmniej jedna wartość parametru została zaktualizowana w wyniku przetwarzania bieżących danych wejściowych (wypowiedź lub Activity).
* **Potwierdzenie zakończyło się pomyślnie** : dane wejściowe wypowiedź lub działanie były pomyślne potwierdzeniem (tak).
* **Odrzucono potwierdzenie** : dane wejściowe wypowiedź lub działanie nie było pomyślnym potwierdzeniem (No).
* Należy **zaktualizować poprzednie polecenie** : ten warunek jest używany w wystąpieniach, jeśli chcesz przechwytywać zaprzeczenie potwierdzenia wraz z aktualizacją. W tle ten warunek jest skonfigurowany do momentu wykrycia negatywnego potwierdzenia przez aparat okna dialogowego, w którym zamiar jest taka sama jak w poprzednim, a użytkownik odpowiedział z aktualizacją.

### <a name="actions"></a>Akcje
* **Wyślij odpowiedź na mowę** : Wyślij odpowiedź na mowę z powrotem do klienta.
* **Zaktualizuj wartość parametru** : zaktualizuj wartość parametru polecenia do określonej wartości.
* **Wyczyść wartość parametru** : Wyczyść wartość parametru polecenia.
* **Wywołaj punkt końcowy sieci Web** : nawiązać połączenie z punktem końcowym sieci Web.
* **Wyślij działanie do klienta** : Wyślij do klienta niestandardowe działanie.

### <a name="expectations"></a>Oczekiwania
Oczekiwania są używane do konfigurowania wskazówek dotyczących przetwarzania następnego danych wejściowych użytkownika. Obsługiwane są następujące typy:

* **Oczekiwano potwierdzenia od użytkownika** : to oczekiwanie określa, że aplikacja oczekuje potwierdzenia (tak/nie) dla następnego danych wejściowych użytkownika.
* **Oczekiwano danych wejściowych z użytkownika** : to oczekiwanie określa co najmniej jeden parametr polecenia, którego aplikacja oczekuje od danych wejściowych użytkownika.

### <a name="post-execution-state"></a>Stan po wykonaniu
Stan po wykonaniu to stan okna dialogowego po przetworzeniu bieżącego danych wejściowych (wypowiedź lub Activity). Są to następujące typy:

* **Zachowaj bieżący stan** : Zachowaj tylko bieżący stan.
* **Ukończ polecenie** : Ukończ polecenie i nie zostaną przetworzone żadne dodatkowe reguły polecenia.
* **Wykonaj reguły uzupełniania** : wykonaj wszystkie prawidłowe reguły uzupełniania.
* **Zaczekaj na dane wejściowe użytkownika** : Poczekaj na następne dane wejściowe użytkownika.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zobacz przykłady w witrynie GitHub](https://aka.ms/speech/cc-samples)
