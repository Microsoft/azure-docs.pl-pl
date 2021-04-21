---
title: Wskazówki dotyczące ograniczania usługi Azure Key Vault
description: Key Vault ogranicza liczbę współbieżnych wywołań, aby zapobiec nadużytce zasobów.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7a215b53f673a7414f1b3662f519de5c26faaa9d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749539"
---
# <a name="azure-key-vault-throttling-guidance"></a>Wskazówki dotyczące ograniczania usługi Azure Key Vault

Ograniczanie przepustowości to inicjowany przez Ciebie proces, który ogranicza liczbę współbieżnych wywołań do usługi platformy Azure, aby zapobiec przekroczeniom zasobów. Azure Key Vault (AKV) jest przeznaczony do obsługi dużej liczby żądań. W przypadku wystąpienia ogromnej liczby żądań ograniczenie żądań klienta pomaga zachować optymalną wydajność i niezawodność usługi AKV.

Limity ograniczania różnią się w zależności od scenariusza. Jeśli na przykład wykonujesz dużą liczbę operacji zapisu, możliwość ograniczenia jest większa niż w przypadku wykonywania tylko operacji odczytu.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak Key Vault obsługuje swoje limity?

Limity usługi w Key Vault zapobiec niewłaściwemu użyciu zasobów i zapewnić jakość usługi dla wszystkich klientów Key Vault usługi. Po przekroczeniu progu usługi program Key Vault ogranicza wszelkie dalsze żądania od tego klienta przez określony czas, zwraca kod stanu HTTP 429 (zbyt wiele żądań), a żądanie kończy się niepowodzeniem. Żądania nieudane, które zwracają liczbę 429, nie są wliczane do limitów ograniczania śledzone przez Key Vault. 

Key Vault została pierwotnie zaprojektowana do przechowywania i pobierania wpisów tajnych w czasie wdrażania.  Świat ewoluował, a Key Vault są używane w czasie rzeczywistym do przechowywania i pobierania wpisów tajnych, a często aplikacje i usługi chcą używać Key Vault jak bazy danych.  Bieżące limity nie obsługują wysokiej przepływności.

Key Vault pierwotnie utworzono z limitami określonymi w limitach [Azure Key Vault usługi](service-limits.md).  Aby zmaksymalizować wydajność Key Vault użyciu stawek za pomocą put, poniżej znajdują się zalecane wskazówki/najlepsze rozwiązania dotyczące maksymalizowania przepływności:
1. Upewnij się, że masz ograniczenia przepustowości.  Klient musi honorować zasady wykładniczego odgałęzienia dla komputerów 429 i upewnić się, że ponowne próby są robisz zgodnie z poniższymi wskazówkami.
1. Podziel ruch Key Vault między wiele magazynów i różnych regionów.   Użyj oddzielnego magazynu dla każdej domeny zabezpieczeń/dostępności.   Jeśli masz pięć aplikacji, z których każda znajduje się w dwóch regionach, zalecamy po 10 magazynów zawierających wpisy tajne unikatowe dla aplikacji i regionu.  Limit dla wszystkich typów transakcji dla całej subskrypcji jest pięciokrotnie ograniczany dla poszczególnych magazynów kluczy. Na przykład transakcje modułu HSM na subskrypcję są ograniczone do 5000 transakcji w ciągu 10 sekund na subskrypcję. Rozważ buforowanie klucza tajnego w usłudze lub aplikacji, aby również zmniejszyć liczbę rps bezpośrednio do magazynu kluczy i/lub obsłużyć ruch oparty na skokach.  Możesz również podzielić ruch między różne regiony, aby zminimalizować opóźnienie i użyć innej subskrypcji/magazynu.  Nie wysyłaj więcej niż limit subskrypcji do usługi Key Vault w jednym regionie platformy Azure.
1. Buforowanie wpisów tajnych pobieranych z Azure Key Vault w pamięci i ponowne używanie ich z pamięci zawsze, gdy jest to możliwe.  Odczyt z kopii Azure Key Vault tylko wtedy, gdy kopia w pamięci podręcznej przestanie działać (np. ponieważ obraca się w źródle). 
1. Key Vault jest przeznaczony dla Twoich wpisów tajnych usług.   Jeśli przechowujesz wpisy tajne klientów (szczególnie w przypadku scenariuszy magazynu kluczy o wysokiej przepływności), rozważ umieszczenie kluczy w bazie danych lub koncie magazynu z szyfrowaniem i przechowywanie tylko klucza głównego w magazynie Azure Key Vault.
1. Szyfruj, opakuj i sprawdzaj, czy operacje na kluczach publicznych mogą być wykonywane bez dostępu do usługi Key Vault, co nie tylko zmniejsza ryzyko ograniczania przepustowości, ale także zwiększa niezawodność (o ile materiał klucza publicznego jest prawidłowo buforowany).
1. Jeśli używasz usługi Key Vault do przechowywania poświadczeń dla usługi, sprawdź, czy ta usługa obsługuje uwierzytelnianie usługi Azure AD w celu bezpośredniego uwierzytelnienia. Zmniejsza to obciążenie Key Vault, zwiększa niezawodność i upraszcza kod, ponieważ Key Vault teraz używać tokenu usługi Azure AD.  Wiele usług przeniosło się na usługę przy użyciu uwierzytelniania usługi Azure AD.  Zobacz bieżącą listę na stronie [Usługi, które obsługują tożsamości zarządzane dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)
1. Rozważ rozłożenie obciążenia/wdrożenia na dłuższy czas, aby utrzymać bieżące limity RPS.
1. Jeśli aplikacja składa się z wielu węzłów, które muszą odczytywać te same tajne dane, rozważ użycie wzorca fan out, w którym jedna jednostka odczytuje klucz tajny z magazynu Key Vault i przechyła do wszystkich węzłów.   Buforuj pobrane wpisy tajne tylko w pamięci.
Jeśli stwierdzisz, że powyższe informacje nadal nie spełniają Twoich potrzeb, wypełnij tabelę poniżej i skontaktuj się z nami, aby określić, jaka dodatkowa pojemność może zostać dodana (przykład przedstawiono poniżej tylko w celach ilustrujących).

| Nazwa magazynu | Region magazynu | Typ obiektu (wpis tajny, klucz lub certyfikat) | Operation(s)* | Typ klucza | Długość klucza lub krzywa | Klucz HSM?| Wymagany czas rps stanu stałego | Potrzebna szczytowa wartość RPS |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Klucz | Znak | EC | P-256 | Nie | 200 | 1000 |

\* Aby uzyskać pełną listę możliwych wartości, zobacz [Azure Key Vault operacji](/rest/api/keyvault/key-operations).

Jeśli dodatkowa pojemność zostanie zatwierdzona, należy pamiętać o następujących związku ze wzrostem pojemności:
1. Zmiany modelu spójności danych. Gdy magazyn zostanie wymieniony na liście z dodatkową pojemnością przepływności, spójność danych usługi Key Vault gwarantuje zmiany (niezbędne do spełnienia wymagań dotyczących większej liczby rps, ponieważ podstawowa usługa Azure Storage nie nadączy za zmianami).  W skrócie:
  1. **Bez zezwalania na** wyświetlanie: Key Vault będzie odzwierciedlać wyniki operacji zapisu (np. SecretSet, CreateKey) natychmiast w kolejnych wywołaniach (np. SecretGet, KeySign).
  1. **Z listą zezwalania:** usługa Key Vault będzie odzwierciedlać wyniki operacji zapisu (np. SecretSet, CreateKey) w ciągu 60 sekund w kolejnych wywołaniach (np. SecretGet, KeySign).
1. Kod klienta musi honorować zasady powrotu dla 429 ponownych prób. Kod klienta wywołujący usługę Key Vault nie może natychmiast ponawiać Key Vault żądań po otrzymaniu kodu odpowiedzi 429.  Wskazówki Azure Key Vault ograniczania przepływności opublikowane w tym miejscu zalecają stosowanie wykładniczego odejmowania podczas odbierania kodu odpowiedzi HTTP 429.

Jeśli masz prawidłowy przypadek biznesowy dla wyższych limitów ograniczania przepustowości, skontaktuj się z nami.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Jak ograniczać aplikację w odpowiedzi na limity usługi

Poniżej przedstawiono **najlepsze rozwiązania, które** należy zaimplementować, gdy usługa jest ograniczona:
- Zmniejsz liczbę operacji na żądanie.
- Zmniejsz częstotliwość żądań.
- Unikaj natychmiastowych ponownych prób. 
    - Wszystkie żądania są naliczane względem limitów użycia.

Podczas implementowania obsługi błędów aplikacji użyj kodu błędu HTTP 429, aby wykryć potrzebę ograniczania przepustowości po stronie klienta. Jeśli żądanie ponownie zakończy się niepowodzeniem z kodem błędu HTTP 429, nadal napotykasz limit usług platformy Azure. Kontynuuj korzystanie z zalecanej metody ograniczania przepustowości po stronie klienta, ponawiając żądanie do momentu jego powodzenia.

Poniżej przedstawiono kod, który implementuje wykładniczego odgałęzienie. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Użycie tego kodu w aplikacji klienta w języku C# jest proste. 

### <a name="recommended-client-side-throttling-method"></a>Zalecana metoda ograniczania przepustowości po stronie klienta

W kodzie błędu HTTP 429 rozpocznij ograniczanie klienta przy użyciu metody wykładniczego odgałęzienia:

1. Poczekaj 1 sekundę, ponów żądanie
2. Jeśli nadal ograniczenie przepływu oczekiwania trwa 2 sekundy, spróbuj ponownie wykonać żądanie
3. Jeśli nadal ograniczono czas oczekiwania przez 4 sekundy, spróbuj ponownie wykonać żądanie
4. Jeśli nadal ograniczono czas oczekiwania przez 8 sekund, spróbuj ponownie wykonać żądanie
5. Jeśli nadal ograniczenie przepływu oczekiwania trwa 16 sekund, spróbuj ponownie wykonać żądanie

W tym momencie nie powinny być dostępne kody odpowiedzi HTTP 429.

## <a name="see-also"></a>Zobacz też

Aby uzyskać bardziej szczegółowe informacje na temat ograniczania przepustowości w chmurze firmy Microsoft, zobacz [Throttling Pattern (Wzorzec ograniczania przepustowości).](/azure/architecture/patterns/throttling)
