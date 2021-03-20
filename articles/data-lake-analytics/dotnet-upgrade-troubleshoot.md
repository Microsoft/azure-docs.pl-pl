---
title: Jak rozwiązywać problemy związane z błędami Azure Data Lake Analytics zadania U-SQL z powodu .NET Framework uaktualnienia 4.7.2
description: Rozwiązywanie błędów zadań U-SQL z powodu uaktualnienia do .NET Framework 4.7.2.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217681"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics jest uaktualniana do .NET Framework v 4.7.2

Azure Data Lake Analytics domyślne środowisko uruchomieniowe uaktualnia .NET Framework v 4.5.2 do .NET Framework v 4.7.2. Ta zmiana wprowadza niewielkie ryzyko istotnej zmiany, jeśli kod U-SQL używa zestawów niestandardowych, a te zestawy niestandardowe korzystają z bibliotek platformy .NET.

To uaktualnienie z .NET Framework 4.5.2 do wersji 4.7.2 oznacza, że .NET Framework wdrożone w środowisku uruchomieniowym U-SQL (domyślne środowisko uruchomieniowe) będzie teraz zawsze 4.7.2. Nie ma opcji obok siebie dla wersji .NET Framework.

Po zakończeniu tego uaktualnienia do .NET Framework 4.7.2, kod zarządzany przez system będzie uruchamiany jako wersja 4.7.2, biblioteki udostępnione przez użytkownika, takie jak niestandardowe zestawy U-SQL, będą działać w trybie zgodności z poprzednimi wersjami odpowiednim dla wersji, dla której Wygenerowano zestaw.

- Jeśli biblioteki DLL zestawu są generowane dla wersji 4.5.2, wdrożone środowisko będzie traktować je jako 4.5.2 biblioteki, dostarczając (z kilkoma wyjątkami) semantykę.
- Można teraz używać niestandardowych zestawów U-SQL, które korzystają z funkcji 4.7.2 w wersji, jeśli są przeznaczone dla .NET Framework 4.7.2.

W związku z tym uaktualnieniem do .NET Framework 4.7.2 istnieje możliwość wprowadzenia istotnych zmian w zadaniach U-SQL, które używają niestandardowych zestawów platformy .NET. Zalecamy sprawdzenie w poszukiwaniu problemów ze zgodnością z poprzednimi wersjami przy użyciu poniższej procedury.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Jak wyszukiwać problemy ze zgodnością z poprzednimi wersjami

Sprawdź, czy występują problemy z uszkodzeniem zgodności z poprzednimi wersjami, uruchamiając sprawdzanie zgodności programu .NET w kodzie platformy .NET w niestandardowych zestawach U-SQL.

> [!Note]
> Narzędzie nie wykrywa faktycznych zmian powodujących istotność. jest on identyfikowany tylko jako interfejsy API platformy .NET, które mogą (w przypadku niektórych danych wejściowych) powodować problemy. Jeśli otrzymasz powiadomienie o problemie, kod może nadal być prawidłowy, jednak należy zaewidencjonować więcej szczegółów.

1. Uruchom narzędzie sprawdzania zgodności z poprzednimi wersjami w bibliotekach DLL platformy .NET przez
   1. Używanie rozszerzenia programu Visual Studio na [rozszerzeniu programu Visual Studio analizatora przenośności](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Pobieranie i używanie autonomicznego narzędzia z usługi [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Instrukcje dotyczące uruchamiania narzędzia autonomicznego znajdują się w witrynie [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md) .
   1. Dla 4.7.2. zgodność, `read isRetargeting == True` identyfikuje możliwe problemy.
2. Jeśli narzędzie wskazuje na to, że na kod może mieć wpływ którykolwiek z możliwych do odróżnienia niezgodności (niektóre typowe przykłady niezgodności są wymienione poniżej), można kontynuować sprawdzanie według
   1. Analizowanie kodu i określanie, czy kod przekazuje wartości do interfejsów API, których to dotyczy
   1. Wykonaj sprawdzenie środowiska uruchomieniowego. Wdrożenie środowiska uruchomieniowego nie jest wykonywane obok siebie w ADLA. Przed uaktualnieniem można wykonać sprawdzanie środowiska uruchomieniowego, używając lokalnego VisualStudio z lokalną .NET Framework 4.7.2 względem reprezentatywnego zestawu danych.
3. Jeśli naprawdę chcesz mieć negatywny wpływ na zgodność z poprzednimi wersjami, wykonaj niezbędne kroki, aby rozwiązać ten problem (na przykład naprawianie danych lub logiki kodu).

W większości przypadków nie ma to wpływu na zgodność z poprzednimi wersjami.

## <a name="timeline"></a>Oś czasu

Możesz sprawdzić wdrożenie nowego środowiska uruchomieniowego tutaj [Rozwiązywanie problemów w środowisku uruchomieniowym](runtime-troubleshoot.md), sprawdzając każde wcześniejsze pomyślne zadanie.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Co zrobić, jeśli nie mogę uzyskać przeglądu kodu w czasie

Możesz przesłać zadanie do starej wersji środowiska uruchomieniowego (która jest skompilowana jako przeznaczoną do 4.5.2), jednak z powodu braku .NET Framework funkcji obok siebie, będzie nadal działać tylko w trybie zgodności 4.5.2. W związku z tym zachowaniem nadal mogą wystąpić problemy ze zgodnością z poprzednimi wersjami.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Jakie są najczęstsze problemy ze zgodnością z poprzednimi wersjami, które mogą wystąpić

Najbardziej typowe niezgodności z poprzednimi wersjami, które mogą identyfikować moduł sprawdzania, to (Ta lista została wygenerowana przez uruchomienie narzędzia sprawdzania w ramach własnych wewnętrznych zadań ADLA), które biblioteki mają wpływ (Uwaga: że można wywołać biblioteki tylko pośrednio), dlatego ważne jest, aby wykonać wymaganą akcję #1, aby sprawdzić, czy wpływ na zadania) i możliwe działania zaradcze. Uwaga: w prawie wszystkie przypadki związane z własnymi zadaniami ostrzeżenia zostały wyłączone, aby były fałszywie dodatnie ze względu na wąski charakter najnowszych zmian.

- Właściwość IAsyncResult. CompletedSynchronously musi być poprawna, aby wynikowe zadanie zostało ukończone
  - Podczas wywoływania metody TaskFactory. wywołaniach metody FromAsync implementacja właściwości IAsyncResult. CompletedSynchronously musi być poprawna, aby wynikowe zadanie zostało ukończone. Oznacza to, że właściwość musi zwracać wartość true, jeśli i tylko wtedy, gdy implementacja została ukończona synchronicznie. Uprzednio ta właściwość nie była sprawdzana.
  - Biblioteki, których dotyczy problem: mscorlib, system. Threading. Tasks
  - Sugerowana akcja: Upewnij się, że TaskFactory. wywołaniach metody FromAsync zwraca wartość PRAWDA prawidłowo

- Obiekt DataObject. GetData teraz pobiera dane jako UTF-8
  - W przypadku aplikacji przeznaczonych dla .NET Framework 4 lub uruchomionych w .NET Framework 4.5.1 lub wcześniejszych wersjach element DataObject. GetData pobiera dane sformatowane w formacie HTML jako ciąg ASCII. W związku z tym znaki inne niż ASCII (znaki, których kody ASCII są większe niż 0x7F) są reprezentowane przez dwa losowe znaki. #N # #N#For aplikacji przeznaczonych dla .NET Framework 4,5 lub nowszych i wykonywanych w .NET Framework 4.5.2, `DataObject.GetData` Pobiera dane sformatowane w formacie HTML jako UTF-8, co oznacza, że znaki większe niż 0x7F są poprawne.
  - Biblioteki, których dotyczy problem: GLO
  - Sugerowana akcja: Upewnij się, że pobrane dane są w żądanym formacie

- Element XmlWriter zwraca dla nieprawidłowych par surogatów
  - W przypadku aplikacji, które są przeznaczone dla .NET Framework 4.5.2 lub poprzednich wersji, zapisanie nieprawidłowej pary dwuskładnikowej przy użyciu obsługi rezerwy wyjątku nie zawsze zgłasza wyjątek. W przypadku aplikacji przeznaczonych dla .NET Framework 4,6 Próba zapisania nieprawidłowej pary dwuskładnikowej zgłasza `ArgumentException` .
  - Biblioteki, których dotyczy problem: System.Xml, System.Xml. ReaderWriter
  - Sugerowana akcja: Upewnij się, że nie zapisujesz nieprawidłowej pary dwuskładnikowej, która spowoduje wyjątek argumentu

- HtmlTextWriter nie renderuje `<br/>` poprawnie elementu
  - Począwszy od .NET Framework 4,6, wywołanie `HtmlTextWriter.RenderBeginTag()` i `HtmlTextWriter.RenderEndTag()` z `<BR />` elementem spowoduje poprawne wstawienie tylko jednego `<BR />` (zamiast dwóch)
  - Biblioteki, których dotyczy problem: System. Web
  - Sugerowana akcja: Upewnij się, że wstawiasz ilość `<BR />` oczekiwaną przez użytkownika, więc w zadaniu produkcyjnym nie jest widoczne żadne przypadkowe zachowanie

- Wywołanie CreateDefaultAuthorizationContext z pustym argumentem zostało zmienione
  - Implementacja AuthorizationContext zwrócona przez wywołanie elementu `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` z argumentem authorizationPolicies o wartości null zmieniła swoją implementację w .NET Framework 4,6.
  - Biblioteki, których dotyczy problem: System. IdentityModel
  - Sugerowana akcja: Upewnij się, że obsługujesz nowe oczekiwane zachowanie w przypadku, gdy zasady autoryzacji mają wartość null
  
- RSACng teraz prawidłowo ładuje klucze RSA o niestandardowym rozmiarze klucza
  - W wersjach .NET Framework wcześniejszych niż 4.6.2 klienci z niestandardowymi rozmiarami kluczy dla certyfikatów RSA nie mogą uzyskać dostępu do tych kluczy za `GetRSAPublicKey()` pomocą `GetRSAPrivateKey()` metod rozszerzenia i. `CryptographicException`Komunikat o błędzie "żądany rozmiar klucza nie jest obsługiwany" jest generowany. W .NET Framework 4.6.2 ten problem został rozwiązany. Podobnie `RSA.ImportParameters()` i `RSACng.ImportParameters()` teraz pracujesz z niestandardowymi rozmiarami kluczy bez wyrzucania `CryptographicException` .
  - Biblioteki, których dotyczy problem: mscorlib, system. Core
  - Sugerowana akcja: Upewnij się, że klucze RSA działają zgodnie z oczekiwaniami

- Sprawdzanie dwukropek ścieżki jest bardziej rygorystyczne
  - W .NET Framework 4.6.2 wprowadzono wiele zmian do obsługi wcześniej nieobsługiwanych ścieżek (zarówno w formacie długości, jak i formatu). Sprawdza, czy składnia separatora dysku (dwukropek) była bardziej poprawna, co spowodowało zablokowanie niektórych ścieżek identyfikatorów URI w kilku interfejsach API do wybierania ścieżek, gdzie są one używane do tolerowania.
  - Biblioteki, których dotyczy problem: mscorlib, system. Runtime. Extensions
  - Sugerowana akcja:

- Wywołania konstruktorów identyfikator oświadczenia
  - Począwszy od .NET Framework 4.6.2 istnieje zmiana sposobu, w jaki `T:System.Security.Claims.ClaimsIdentity` konstruktory z `T:System.Security.Principal.IIdentity` parametrem ustawiają `P:System.Security.Claims.ClaimsIdentify.Actor` Właściwość. Jeśli `T:System.Security.Principal.IIdentity` argument jest `T:System.Security.Claims.ClaimsIdentity` obiekt i `P:System.Security.Claims.ClaimsIdentify.Actor` Właściwość tego `T:System.Security.Claims.ClaimsIdentity` obiektu nie jest `null` , `P:System.Security.Claims.ClaimsIdentify.Actor` Właściwość jest dołączona przy użyciu `M:System.Security.Claims.ClaimsIdentity.Clone` metody. W Framework 4.6.1 i starszych wersji `P:System.Security.Claims.ClaimsIdentify.Actor` Właściwość jest dołączana jako istniejące odwołanie. Ze względu na tę zmianę, rozpoczynając od .NET Framework 4.6.2, `P:System.Security.Claims.ClaimsIdentify.Actor` Właściwość nowego `T:System.Security.Claims.ClaimsIdentity` obiektu nie jest równa `P:System.Security.Claims.ClaimsIdentify.Actor` właściwości `T:System.Security.Principal.IIdentity` argumentu konstruktora. W .NET Framework 4.6.1 i starszych wersji jest równa.
  - Biblioteki, których dotyczy problem: mscorlib
  - Sugerowana akcja: Upewnij się, że identyfikator oświadczenia działa zgodnie z oczekiwaniami w nowym środowisku uruchomieniowym

- Serializacja znaków sterujących za pomocą Klasa DataContractJsonSerializer jest teraz zgodna z ECMAScript V6 i V8
  - W programie .NET Framework 4.6.2 i starszych wersjach Klasa DataContractJsonSerializer nie serializować niektórych specjalnych znaków kontroli, takich jak \b, \f i \t, w sposób zgodny z standardami ECMAScript V6 i V8. Począwszy od .NET Framework 4,7, serializacja tych znaków kontrolnych jest zgodna z ECMAScript V6 i V8.
  - Biblioteki, których dotyczy problem: System.Runtime.Serialization.Jswłączone
  - Sugerowana akcja: Zapewnij takie samo zachowanie przy użyciu Klasa DataContractJsonSerializer
