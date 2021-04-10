---
title: Skalowanie i zarządzanie niestandardowymi umiejętnościami
titleSuffix: Azure Cognitive Search
description: Poznaj narzędzia i techniki umożliwiające wydajne skalowanie niestandardowych umiejętności w celu zapewnienia maksymalnej przepływności. Niestandardowe umiejętności umożliwiają wywoływanie niestandardowych modeli AI lub logiki, które można dodać do potoku indeksowanego AI na platformie Azure Wyszukiwanie poznawcze.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 4fdc222fa20aef6639bf6d5d485f7dcf6b6ca535
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641135"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Wydajne skalowanie niestandardowych umiejętności

Niestandardowe umiejętności to interfejsy API sieci Web implementujące określony interfejs. Niestandardową umiejętność można zaimplementować przy użyciu dowolnego publicznego zasobu do adresowania. Najbardziej typowe implementacje niestandardowych umiejętności są następujące:
* Azure Functions niestandardowych umiejętności logiki
* Usługa Azure webapps dla prostych, kontenerowych umiejętności AI
* Usługa Azure Kubernetes dla bardziej złożonych lub większych umiejętności.

## <a name="prerequisites"></a>Wymagania wstępne

+ Zapoznaj się z [niestandardowym interfejsem umiejętności](cognitive-search-custom-skill-interface.md) w celu wprowadzenia do interfejsu wejścia/wyjścia, który powinien być zaimplementowany przez umiejętność niestandardową.

+ Skonfiguruj swoje środowisko. Możesz zacząć od [tego samouczka](/azure/azure-functions/create-first-function-vs-code-python) , aby skonfigurować funkcję platformy Azure bezserwerową przy użyciu rozszerzeń Visual Studio Code i Python.

## <a name="skillset-configuration"></a>Konfiguracja zestawu umiejętności

Skonfigurowanie niestandardowej umiejętności w celu zmaksymalizowania przepływności procesu indeksowania wymaga poznania umiejętności, konfiguracji indeksatora oraz sposobu, w jaki umiejętność odnosi się do każdego dokumentu. Na przykład liczba wywołań umiejętności dla dokumentu i oczekiwany czas trwania na wywołanie.

### <a name="skill-settings"></a>Ustawienia umiejętności

Na [niestandardowej umiejętności](cognitive-search-custom-skill-web-api.md) ustaw następujące parametry.

1. Zbiór `batchSize` niestandardowych umiejętności w celu skonfigurowania liczby rekordów wysyłanych do umiejętności w jednym wywołaniu umiejętności.

2. Ustaw wartość w polu `degreeOfParallelism` Aby skalibrować liczbę współbieżnych żądań, które indeksator wprowadzi do Twojej umiejętności.

3. Ustaw `timeout` wartość wystarczającą, aby umiejętność odpowiadała na prawidłową odpowiedź.

4. W `indexer` definicji Ustaw [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) liczbę dokumentów, które powinny być odczytywane ze źródła danych i wzbogacone współbieżnie.

### <a name="considerations"></a>Zagadnienia do rozważenia

Ustawienie tych zmiennych w celu zoptymalizowania wydajności indeksatorów wymaga określenia, czy umiejętność ma być lepsza dla wielu równoczesnych żądań lub mniej dużych żądań. Oto kilka pytań, które należy wziąć pod uwagę:

* Co to jest Kardynalność wywołania umiejętności? Czy umiejętność jest wykonywana jednokrotnie dla każdego dokumentu, na przykład umiejętności klasyfikacji dokumentu lub czy umiejętność została wykonana wiele razy na dokument, umiejętność klasyfikacji akapitu?

* Na średnim ile dokumentów jest odczytywanych ze źródła danych w celu wypełnienia żądania kwalifikacji na podstawie rozmiaru partii dla umiejętności? W idealnym przypadku powinna to być mniejsza niż rozmiar wsadu indeksatora. W przypadku partii o rozmiarach większych niż 1 umiejętność może odbierać rekordy z wielu dokumentów źródłowych. Jeśli na przykład liczba partii dla indeksatora to 5, a liczba partii kwalifikacji to 50, a każdy dokument generuje tylko pięć rekordów, indeksator będzie musiał wypełnić niestandardowe żądanie dotyczące umiejętności w wielu partiach indeksatora.

* Średnia liczba żądań, które może wygenerować partia indeksatora, zapewnia optymalne ustawienie stopni równoległości. Jeśli infrastruktura obsługująca umiejętność nie może obsługiwać tego poziomu współbieżności, należy rozważyć wybranie stopni równoległości. Najlepszym rozwiązaniem jest przetestowanie konfiguracji za pomocą kilku dokumentów w celu sprawdzenia poprawności wybranych parametrów.

* Testowanie przy użyciu mniejszej próbki dokumentów pozwala oszacować czas wykonywania swojej umiejętności na łączny czas potrzebny do przetworzenia podzbioru dokumentów. Czy indeksator poświęca więcej czasu na tworzenie partii lub oczekiwanie na odpowiedź z Twojej umiejętności? 

* Należy wziąć pod uwagę skutki nadrzędności równoległości. Jeśli dane wejściowe do niestandardowej umiejętności są danymi wyjściowymi z wcześniejszej umiejętności, czy wszystkie umiejętności z zestawu umiejętności skalowane w praktyce w celu zminimalizowania opóźnień?

## <a name="error-handling-in-the-custom-skill"></a>Obsługa błędów w niestandardowej kwalifikacji

Umiejętności niestandardowe powinny zwracać kod stanu sukcesu HTTP 200, gdy umiejętność zakończy się pomyślnie. Jeśli co najmniej jeden rekord w partii powoduje błędy, należy rozważyć zwrócenie kodu o wielu stanach 207. Lista błędy lub ostrzeżenia dla rekordu powinna zawierać odpowiedni komunikat.

Wszelkie elementy w partii, których błędy spowodują, że odpowiadające im dokumenty zakończą się niepowodzeniem. Jeśli konieczne jest pomyślne wykonanie dokumentu, zwróć ostrzeżenie.

Wszystkie kody stanu powyżej 299 są oceniane jako błąd, a wszystkie wzbogacania nie powiodły się, co spowodowało niepowodzenie dokumentu. 

### <a name="common-error-messages"></a>Typowe komunikaty o błędach

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Ustaw parametr Timeout dla umiejętności, aby zezwolić na dłuższy czas wykonywania.

* `Could not execute skill because Web Api skill response is invalid.` Indykatywny dla umiejętności nie zwraca komunikatu w niestandardowym formacie odpowiedzi na umiejętności. Może to być wynikiem nieprzechwyconego wyjątku w kwalifikacji.

* `Could not execute skill because the Web Api request failed.` Najprawdopodobniej jest to spowodowane błędami lub wyjątkami autoryzacji.

* `Could not execute skill.` Zwykle wynik odpowiedzi na umiejętności jest mapowany na istniejącą właściwość w hierarchii dokumentów.

## <a name="testing-custom-skills"></a>Testowanie umiejętności niestandardowych

Zacznij od testowania niestandardowej umiejętności przy użyciu klienta interfejsu API REST, aby sprawdzić poprawność:

* Umiejętność implementuje niestandardowy interfejs umiejętności dla żądań i odpowiedzi

* Umiejętność zwraca prawidłowy kod JSON z `application/JSON` typem MIME

* Zwraca prawidłowy kod stanu HTTP

Utwórz [sesję debugowania](cognitive-search-debug-session.md) , aby dodać swoją umiejętność do zestawu umiejętności i upewnić się, że daje ona prawidłowe wzbogacanie. Sesja debugowania nie pozwala na dostosowanie wydajności umiejętności, dzięki czemu można upewnić się, że umiejętność jest skonfigurowana z prawidłowymi wartościami i zwraca oczekiwane obiekty wzbogacone.

## <a name="best-practices"></a>Najlepsze rozwiązania

* Chociaż umiejętności mogą akceptować i zwracać większe ładunki, należy rozważyć ograniczenie odpowiedzi do 150 MB lub mniejszej przy zwracaniu kodu JSON.

* Rozważ ustawienie rozmiaru wsadu na indeksatorze i umiejętności, aby upewnić się, że każda partia źródła danych generuje pełny ładunek dla umiejętności.

* W przypadku długotrwałych zadań należy ustawić limit czasu na wystarczająco wysoką wartość, aby upewnić się, że indeksator nie wystąpił błąd podczas przetwarzania dokumentów współbieżnie.

* Zoptymalizuj rozmiar wsadu indeksatora, rozmiar wsadu zdolności i umiejętności równoległości w celu wygenerowania wzorca obciążenia, którego oczekuje Twoje umiejętności, mniejsze żądania lub wiele małych żądań.

* Monitoruj niestandardowe umiejętności z szczegółowymi dziennikami błędów, ponieważ możesz mieć scenariusze, w których określone żądania są spójne w wyniku zmienności danych.


## <a name="next-steps"></a>Następne kroki
Gratulacje! Twoje niestandardowe umiejętności są teraz skalowane bezpośrednio w celu zmaksymalizowania przepływności na indeksatorze. 

+ [Umiejętności dotyczące oszczędzania mocy: repozytorium umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Dodaj niestandardową umiejętność do potoku wzbogacania AI](cognitive-search-custom-skill-interface.md)
+ [Dodaj umiejętność Azure Machine Learning](./cognitive-search-aml-skill.md)
+ [Użyj sesji debugowania do testowania zmian](./cognitive-search-debug-session.md)