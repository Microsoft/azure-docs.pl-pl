---
title: Wskazówki dotyczące ograniczania przepustowości usługi Azure Key Vault
description: Ograniczanie przepustowości usługi Key Vault ogranicza liczbę równoczesnych wywołań, aby zapobiec nadużywaniu zasobów.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f32a988ec0d75ca8d8eca04e69edd7226bf283b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432088"
---
# <a name="azure-key-vault-throttling-guidance"></a>Wskazówki dotyczące ograniczania przepustowości usługi Azure Key Vault

Ograniczanie jest procesem, który inicjujesz, który ogranicza liczbę równoczesnych wywołań usługi Azure, aby zapobiec nadużywaniu zasobów. Usługa Azure Key Vault (AKV) została zaprojektowana do obsługi dużej liczby żądań. Jeśli wystąpi przytłaczająca liczba żądań, ograniczanie żądań klienta pomaga utrzymać optymalną wydajność i niezawodność usługi AKV.

Limity ograniczania różnią się w zależności od scenariusza. Na przykład jeśli wykonujesz dużą ilość zapisów, możliwość ograniczania przepustowości jest wyższa niż w przypadku wykonywania tylko odczytów.

## <a name="how-does-key-vault-handle-its-limits"></a>W jaki sposób usługa Key Vault radzi sobie z jego ograniczeniami?

Limity usług w usłudze Key Vault zapobiegają niewłaściwemu wykorzystaniu zasobów i zapewniają jakość usług dla wszystkich klientów usługi Key Vault. Po przekroczeniu progu usługi usługa, Usługa Key Vault ogranicza wszelkie dalsze żądania od tego klienta przez pewien czas, zwraca kod stanu HTTP 429 (zbyt wiele żądań), a żądanie kończy się niepowodzeniem. Nieudane żądania zwracające 429 liczników do limitów przepustnicy śledzonych przez magazyn kluczy. 

Usługa Key Vault została pierwotnie zaprojektowana do przechowywania i pobierania wpisów tajnych w czasie wdrażania.  Świat ewoluował, a usługa Key Vault jest używana w czasie wykonywania do przechowywania i pobierania wpisów tajnych, a często aplikacje i usługi chcą używać usługi Key Vault jak bazy danych.  Bieżące limity nie obsługują wysokich przepływności.

Usługa Key Vault została pierwotnie utworzona z limitami określonymi w [limitach usług Usługi Azure Key Vault](service-limits.md).  Aby zmaksymalizować wydajność usługi Key Vault za pomocą współczynników put, oto kilka zalecanych wskazówek/najlepszych rozwiązań dotyczących maksymalizacji przepustowości:
1. Upewnij się, że masz dławienie w miejscu.  Klient musi honorować wykładnicze zasady wycofywania dla 429 i upewnij się, że robisz ponownych prób, zgodnie z poniższymi wskazówkami.
1. Podziel ruch w magazynie kluczy między wiele magazynów i różnych regionów.   Użyj oddzielnego magazynu dla każdej domeny zabezpieczeń/dostępności.   Jeśli masz pięć aplikacji, każdy w dwóch regionach, a następnie zalecamy 10 magazynów, z których każda zawiera wpisy tajne unikatowe dla aplikacji i regionu.  Limit dla wszystkich typów transakcji dla wszystkich typów transakcji jest pięciokrotnie limit indywidualnego magazynu kluczy. Na przykład hsm innych transakcji na subskrypcję są ograniczone do 5000 transakcji w 10 sekund na subskrypcję. Należy rozważyć buforowanie klucza tajnego w ramach usługi lub aplikacji, aby również zmniejszyć RPS bezpośrednio do magazynu kluczy i/lub obsługi ruchu opartego na serii.  Można również podzielić ruch między różne regiony, aby zminimalizować opóźnienia i użyć innej subskrypcji/przechowalni.  Nie wysyłaj więcej niż limit subskrypcji do usługi Usługi Usługi Usługi Key Vault w jednym regionie platformy Azure.
1. Buforuj wpisy tajne pobierane z usługi Azure Key Vault w pamięci i ponownie z pamięci, gdy tylko jest to możliwe.  Ponowne odczytanie z usługi Azure Key Vault tylko wtedy, gdy kopia w pamięci podręcznej przestaje działać (np. ponieważ została obrócona u źródła). 
1. Usługa Key Vault została zaprojektowana z myślą o twoich sekretach usług.   Jeśli przechowujesz wpisy tajne klientów (szczególnie w przypadku scenariuszy magazynu kluczy o wysokiej przepływności), należy rozważyć umieszczenie kluczy w bazie danych lub konta magazynu z szyfrowaniem i przechowywanie tylko klucza głównego w usłudze Azure Key Vault.
1. Szyfruj, zawijaj i weryfikuj operacje klucza publicznego mogą być wykonywane bez dostępu do usługi Key Vault, co nie tylko zmniejsza ryzyko ograniczania przepustowości, ale także zwiększa niezawodność (o ile prawidłowo buforujesz materiał klucza publicznego).
1. Jeśli używasz Usługi Key Vault do przechowywania poświadczeń dla usługi, sprawdź, czy ta usługa obsługuje uwierzytelnianie usługi Azure AD do uwierzytelniania bezpośrednio. Zmniejsza to obciążenie usługi Key Vault, zwiększa niezawodność i upraszcza kod, ponieważ usługa Key Vault może teraz używać tokenu usługi Azure AD.  Wiele usług zostało przeniesionych do korzystania z usługi Azure AD Auth.  Zobacz bieżącą listę w [usługach, które obsługują tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Należy rozważyć rozłożenie obciążenia/wdrożenia przez dłuższy okres czasu, aby pozostać w ramach bieżących limitów RPS.
1. Jeśli aplikacja składa się z wielu węzłów, które muszą odczytać te same klucze tajne, należy rozważyć użycie wzorca wyjścia wentylatora, gdzie jedna jednostka odczytuje klucz tajny z usługi Key Vault i wentylatory do wszystkich węzłów.   Buforuj pobrane wpisy tajne tylko w pamięci.
Jeśli okaże się, że powyższe nadal nie spełnia Twoich potrzeb, wypełnij poniższą tabelę i skontaktuj się z nami, aby ustalić, jakie dodatkowe pojemność można dodać (przykład poniżej tylko w celach ilustracyjnych).

| Nazwa przechowalni | Obszar przechowalni | Typ obiektu (klucz tajny, klucz lub certyfikat) | Działanie(-a) * | Typ klucza | Długość lub krzywa klawisza | klucz HSM?| Potrzebny rps w stanie stacjonarnym | Peak RPS potrzebne |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Klucz | Znak | EC | P-256 | Nie | 200 | 1000 |

\*Aby uzyskać pełną listę możliwych wartości, zobacz [Operacje usługi Azure Key Vault](/rest/api/keyvault/key-operations).

W przypadku zatwierdzenia dodatkowej pojemności należy zwrócić uwagę na następujące informacje w wyniku zwiększenia przepustowości:
1. Zmiany modelu spójności danych. Gdy magazyn jest dozwolone wymienione z dodatkową przepustowość pojemności, usługi usługi usługi Key Vault spójności gwarancji zmian (niezbędne do spełnienia większej ilości RPS, ponieważ podstawowej usługi Azure Storage nie może nadążyć).  W skrócie:
  1. **Bez zezwolenia na wystawienie:** Usługa Key Vault będzie odzwierciedlać wyniki operacji zapisu (np. SecretSet, CreateKey) natychmiast w kolejnych wywołaniach (np. SecretGet, KeySign).
  1. **Z dopuszczeniem aukcji:** Usługa Key Vault będzie odzwierciedlać wyniki operacji zapisu (np. SecretSet, CreateKey) w ciągu 60 sekund w kolejnych połączeniach (np. SecretGet, KeySign).
1. Kod klienta musi honorować zasady wycofywania dla 429 ponownych prób. The client code calling the Key Vault service must not immediately retry Key Vault requests when it receives a 429 response code.  Opublikowane w tym miejscu wskazówki dotyczące ograniczania przepustowości usługi Azure Key Vault zaleca stosowanie wykładniczego wycofywania podczas odbierania kodu odpowiedzi http 429.

Jeśli masz ważne uzasadnienie biznesowe dla wyższych limitów przepustnicy, skontaktuj się z nami.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Jak ograniczyć aplikację w odpowiedzi na limity usług

Oto **najlepsze rozwiązania, które** należy zaimplementować, gdy usługa jest ograniczona:
- Zmniejsz liczbę operacji na żądanie.
- Zmniejsz częstotliwość żądań.
- Unikaj natychmiastowych ponownych prób. 
    - Wszystkie żądania są naliczane w granicach użycia.

Podczas implementowania obsługi błędów aplikacji, użyj kodu błędu HTTP 429, aby wykryć potrzebę ograniczania przepustowości po stronie klienta. Jeśli żądanie nie powiedzie się ponownie z kodem błędu HTTP 429, nadal występują limit usługi platformy Azure. Kontynuuj używanie zalecanej metody ograniczania przepustowości po stronie klienta, ponowiając próbę żądania, dopóki nie zakończy się pomyślnie.

Kod, który implementuje wykładnicze wycofywanie pokazano poniżej. 
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
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Korzystanie z tego kodu w aplikacji języka C klienta jest proste. 

### <a name="recommended-client-side-throttling-method"></a>Zalecana metoda ograniczania przepustowości po stronie klienta

W przypadku kodu błędu HTTP 429 rozpocznij ograniczanie obsługi klienta przy użyciu wykładniczego podejścia wycofywania:

1. Poczekaj 1 sekundę, ponów próbę żądania
2. Jeśli nadal jest ograniczona do 2 sekund, ponowić żądanie
3. Jeśli nadal jest ograniczona do 4 sekund, ponowić żądanie
4. Jeśli nadal jest ograniczona do 8 sekund, ponowić żądanie
5. Jeśli nadal jest ograniczona do 16 sekund, ponowić żądanie

W tym momencie nie powinno być coraz HTTP 429 kody odpowiedzi.

## <a name="see-also"></a>Zobacz też

Aby uzyskać głębszą orientację ograniczania przepustowości w chmurze firmy Microsoft, zobacz [Ograniczanie wzorca](https://docs.microsoft.com/azure/architecture/patterns/throttling).

