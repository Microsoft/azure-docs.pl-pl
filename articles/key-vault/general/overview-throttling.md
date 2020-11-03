---
title: Wskazówki dotyczące ograniczania usługi Azure Key Vault
description: Funkcja ograniczania Key Vault ogranicza liczbę współbieżnych wywołań, aby zapobiec nadmiernemu użyciu zasobów.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 5b60f290f6d3ca184e25edd2984ad5b2d1ff2bdf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289680"
---
# <a name="azure-key-vault-throttling-guidance"></a>Wskazówki dotyczące ograniczania usługi Azure Key Vault

Ograniczanie to proces inicjowany, który ogranicza liczbę współbieżnych wywołań do usługi platformy Azure, aby zapobiec nadmiernemu wykorzystaniu zasobów. Azure Key Vault (AKV) został zaprojektowany z myślą o obsłudze dużej liczby żądań. W przypadku przeprowadzenia przeciążonej liczby żądań ograniczanie żądań klientów pomaga zapewnić optymalną wydajność i niezawodność usługi AKV.

Limity ograniczania są różne w zależności od scenariusza. Na przykład jeśli wykonujesz dużą liczbę operacji zapisu, możliwość ograniczania przepustowości jest wyższa niż w przypadku wykonywania operacji odczytu.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak Key Vault obsługiwać limity?

Limity usługi w Key Vault uniemożliwiają niewłaściwe użycie zasobów i zapewniają jakość usługi dla wszystkich klientów Key Vault. W przypadku przekroczenia progu usługi Key Vault ogranicza wszelkie dalsze żądania od tego klienta przez pewien czas, zwraca kod stanu HTTP 429 (zbyt wiele żądań) i żądanie kończy się niepowodzeniem. Żądania zakończone niepowodzeniem, które zwracają liczbę 429 do limitów dławienia śledzonych przez Key Vault. 

Key Vault pierwotnie zaprojektowano, aby służyć do przechowywania i pobierania wpisów tajnych w czasie wdrażania.  Świat został rozwijający, a Key Vault jest używany w czasie wykonywania w celu przechowywania i pobierania wpisów tajnych, a często aplikacje i usługi chcą używać Key Vault, takich jak baza danych.  Bieżące limity nie obsługują stawek o dużej przepływności.

Key Vault został pierwotnie utworzony z limitami określonymi w [limitach usługi Azure Key Vault](service-limits.md).  Aby zmaksymalizować Key Vault dzięki stawkom za użycie usługi Put, poniżej przedstawiono kilka zalecanych wytycznych/najlepszych rozwiązań dotyczących maksymalizowania przepływności:
1. Upewnij się, że nastąpiło ograniczenie wydajności.  Klient musi przestrzegać zasad wycofywania wykładniczego dla 429 i upewnić się, że wykonujesz ponowną próbę zgodnie z poniższymi wskazówkami.
1. Podziel ruch Key Vault między wiele magazynów a różnymi regionami.   Użyj oddzielnego magazynu dla każdej domeny zabezpieczeń/dostępności.   Jeśli masz pięć aplikacji, z których każda znajduje się w dwóch regionach, zalecamy 10 magazynów zawierających wpisy tajne unikatowe dla aplikacji i regionu.  Limit całej subskrypcji dla wszystkich typów transakcji jest pięciu razy większy niż pojedynczy limit magazynu kluczy. Na przykład usługi HSM — inne transakcje na subskrypcję są ograniczone do 5 000 transakcji w ciągu 10 sekund na subskrypcję. Rozważ buforowanie wpisu tajnego w ramach usługi lub aplikacji, aby zmniejszyć RPS pliku bezpośrednio do magazynu kluczy i/lub obsłużyć ruch na podstawie serii.  Możesz również podzielić ruch między różnymi regionami, aby zminimalizować opóźnienia i korzystać z innej subskrypcji/magazynu.  Nie wysyłaj więcej niż limitu subskrypcji do usługi Key Vault w jednym regionie platformy Azure.
1. Buforowanie kluczy tajnych pobieranych z Azure Key Vault w pamięci i wielokrotnego użytku z pamięci, gdy jest to możliwe.  Odczytaj Azure Key Vault tylko wtedy, gdy buforowana kopia przestanie działać (na przykład, ponieważ została obrócona na źródło). 
1. Key Vault jest zaprojektowana dla własnych wpisów tajnych usług.   Jeśli przechowujesz wpisy tajne klientów (szczególnie w przypadku scenariuszy magazynu kluczy o wysokiej przepływności), Rozważ umieszczenie kluczy w bazie danych lub koncie magazynu przy użyciu szyfrowania i przechowywanie tylko klucza głównego w Azure Key Vault.
1. Szyfrowanie, zawijanie i weryfikowanie operacji w kluczu publicznym może być wykonywane bez dostępu do Key Vault, co zmniejsza ryzyko ograniczania przepustowości, ale również zwiększa niezawodność (o ile materiał klucza publicznego jest prawidłowo buforowany).
1. Jeśli używasz Key Vault do przechowywania poświadczeń dla usługi, sprawdź, czy usługa obsługuje uwierzytelnianie usługi Azure AD w celu bezpośredniego uwierzytelniania. Zmniejsza to obciążenie Key Vault, zwiększa niezawodność i upraszcza kod, ponieważ Key Vault może teraz używać tokenu usługi Azure AD.  Wiele usług zostało przeniesionych do korzystania z uwierzytelniania usługi Azure AD.  Zapoznaj się z bieżącą listą [usług, która obsługuje zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Rozważ rozłożenie obciążenia/wdrożenia w dłuższym czasie, aby zachować w ramach bieżących limitów RPS pliku.
1. Jeśli aplikacja składa się z wielu węzłów, które muszą odczytywać te same tajne dane, należy rozważyć użycie wzorca wentylatorów, gdzie jedna jednostka odczytuje wpis tajny z Key Vault, a następnie wymusić między wszystkimi węzłami.   Buforuj pobrane wpisy tajne tylko w pamięci.
Jeśli okaże się, że powyższy kod nadal nie spełnia Twoich potrzeb, Wypełnij poniższą tabelę i skontaktuj się z nami, aby określić, jakie dodatkowe możliwości można dodać (przykład poniżej w celach ilustracyjnych).

| Nazwa magazynu | Region magazynu | Typ obiektu (klucz tajny, klucz lub certyfikat) | Operacje: * | Typ klucza | Długość lub krzywa klucza | Klucz HSM?| Wymagany RPS pliku stanu | Wymagany RPS pliku szczytu |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Klucz | Znak | EC | P-256 | Nie | 200 | 1000 |

\* Aby uzyskać pełną listę możliwych wartości, zobacz [Azure Key Vault operacji](/rest/api/keyvault/key-operations).

W przypadku zatwierdzenia dodatkowej pojemności należy zwrócić uwagę na następujące kwestie w wyniku wzrostu pojemności:
1. Zmiany modelu spójności danych. Gdy magazyn jest dozwolony na liście z dodatkową przepływność, usługa Key Vault gwarancja spójności danych usług (niezbędna do spełnienia wyższego RPS pliku woluminu, ponieważ nie można zatrzymać podstawowej usługi magazynu platformy Azure).  W Nutshell:
  1. **Bez zezwolenia na listę** : usługa Key Vault będzie odzwierciedlała wyniki operacji zapisu (np. SecretSet, CreateKey) natychmiast w kolejnych wywołaniach (np. SecretGet, znak.
  1. **Z opcją Zezwalaj na listę** : usługa Key Vault będzie odzwierciedlała wyniki operacji zapisu (np. SecretSet, CreateKey) w ciągu 60 sekund w kolejnych wywołaniach (np. SecretGet, znak.
1. Kod klienta musi przestrzegać zasad wycofywania dla 429 ponownych prób. Kod klienta wywołujący usługę Key Vault nie może natychmiast ponowić próby Key Vault żądań, gdy odbierze kod odpowiedzi 429.  Wskazówki dotyczące ograniczania przepustowości Azure Key Vault opublikowane w tym miejscu zalecają zastosowanie wykładniczej wycofywania podczas otrzymywania kodu odpowiedzi HTTP 429.

Jeśli masz prawidłowy przypadek biznesowy dla wyższych limitów ograniczania przepustowości, skontaktuj się z nami.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Jak ograniczyć swoją aplikację w odpowiedzi na limity usług

Poniżej przedstawiono **najlepsze rozwiązania** , które należy zaimplementować w przypadku dławienia usługi:
- Zmniejsz liczbę operacji na żądanie.
- Zmniejsz częstotliwość żądań.
- Unikaj natychmiastowych ponownych prób. 
    - Wszystkie żądania są naliczane względem limitów użycia.

Podczas implementowania obsługi błędów aplikacji należy użyć kodu błędu HTTP 429 w celu wykrycia potrzeby ograniczenia przepustowości po stronie klienta. Jeśli żądanie kończy się niepowodzeniem z kodem błędu HTTP 429, nadal występuje limit usługi platformy Azure. Kontynuuj używanie zalecanej metody ograniczania przepustowości po stronie klienta, ponawianie próby żądania do momentu jego pomyślnego przeprowadzenia.

Kod implementujący wykładniczy wycofywania jest przedstawiony poniżej. 
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


Używanie tego kodu w aplikacji klienckiej w języku C# jest proste. 

### <a name="recommended-client-side-throttling-method"></a>Zalecana metoda ograniczania przepustowości po stronie klienta

W przypadku błędu HTTP o kodzie 429 Rozpocznij ograniczanie klienta przy użyciu podejścia wykładniczego wycofywania:

1. Oczekiwanie 1 sekundy, żądanie ponowienia próby
2. Jeśli nadal ograniczono limit czasu oczekiwania na 2 sekundy, ponów próbę
3. Jeśli nadal ograniczono limit czasu oczekiwania na 4 sekundy, żądanie ponowienia próby
4. Jeśli nadal ograniczono limit czasu, zaczekaj 8 sekund, a następnie ponów próbę
5. Jeśli nadal ograniczono limit czasu, odczekaj 16 sekund, żądanie ponowienia próby

W tym momencie nie należy otrzymywać kodów odpowiedzi HTTP 429.

## <a name="see-also"></a>Zobacz także

Aby uzyskać bardziej szczegółowe ukierunkowanie ograniczania przepływności na Microsoft Cloud, zobacz [Ograniczanie poziomu wzorca](/azure/architecture/patterns/throttling).