---
title: Omówienie usługi analizy błędów
description: W tym artykule opisano usługę analizy błędów w Service Fabric, aby wzbudzać błędy i uruchamiać scenariusze testów dla usług.
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 4e879b0b39d58f115b4661d47cf2ae564ba14e5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531381"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Wprowadzenie do usługi analizy błędów
Usługa błędów analizy jest przeznaczona do testowania usług, które są oparte na Microsoft Azure Service Fabric. Za pomocą usługi błędów analizy można wywołać znaczące błędy i uruchamiać kompletne scenariusze testów dla aplikacji. Te usterki i scenariusze wykonują działania oraz weryfikują liczne Stany i przejścia, które usługa będzie w całym okresie istnienia, w sposób kontrolowany, bezpieczny i spójny.

Akcje to indywidualne błędy ukierunkowane na usługę na potrzeby testowania. Deweloperzy usług mogą używać ich jako bloków konstrukcyjnych do pisania skomplikowanych scenariuszy. Na przykład:

* Uruchom ponownie węzeł, aby symulować dowolną liczbę sytuacji, w których maszyna lub maszyna wirtualna jest uruchamiana ponownym uruchomieniu.
* Przenieś replikę usługi stanowej w celu zasymulowania równoważenia obciążenia, pracy w trybie failover lub uaktualnienia aplikacji.
* Wywołaj utratę kworum w usłudze stanowej, aby utworzyć sytuację, w której nie można wykonać operacji zapisu, ponieważ nie ma wystarczającej liczby replik "kopia zapasowa" lub "pomocniczych", aby akceptować nowe dane.
* Wywołaj utratę danych w usłudze stanowej, aby utworzyć sytuację, w której cały stan w pamięci zostanie całkowicie wyczyszczony.

Scenariusze to złożone operacje składające się z co najmniej jednej akcji. Usługa analizy błędów oferuje dwa wbudowane scenariusze:

* Scenariusz chaos
* Scenariusz trybu failover

## <a name="testing-as-a-service"></a>Testowanie jako usługa
Usługa błędów analizy jest usługą systemową Service Fabric, która jest uruchamiana automatycznie z klastrem Service Fabric. Ta usługa działa jako host dla iniekcji błędów, wykonywania scenariusza testowego i analizy kondycji. 

![Usługa analizy błędów][0]

Po zainicjowaniu akcji błędu lub scenariusza testowego do usługi analizy błędów jest wysyłane polecenie, aby uruchomić akcję błędu lub scenariusz testu. Usługa analizy błędów jest stanowa, dzięki czemu może niezawodnie uruchamiać błędy i scenariusze oraz sprawdzać poprawność wyników. Na przykład długotrwały Scenariusz testowania może być niezawodnie wykonywany przez usługę analizy błędów. I ponieważ testy są wykonywane wewnątrz klastra, usługa może sprawdzać stan klastra i usług, aby uzyskać bardziej szczegółowe informacje na temat błędów.

## <a name="testing-distributed-systems"></a>Testowanie systemów rozproszonych
Service Fabric ułatwia znacznie łatwiejsze pisanie i zarządzanie rozproszonymi skalowalnymi aplikacjami. Usługa błędów analizy nie ułatwia przetestowania aplikacji rozproszonej. Istnieją trzy główne problemy, które należy rozwiązać podczas testowania:

1. Symulowanie/generowanie błędów, które mogą wystąpić w rzeczywistych scenariuszach: jednym z ważnych aspektów Service Fabric jest umożliwienie aplikacjom rozproszonym odzyskanie z różnych awarii. Jednak w celu przetestowania, że aplikacja może odzyskać się po tych błędach, musimy mieć mechanizm symulowania/wygenerowania tych rzeczywistych błędów w kontrolowanym środowisku testowym.
1. Możliwość generowania skorelowanych błędów: awarie podstawowe w systemie, takie jak awarie sieci i awarie maszyn, można łatwo utworzyć indywidualnie. Generowanie dużej liczby scenariuszy, które mogą wystąpić w świecie rzeczywistym w wyniku interakcji tych pojedynczych awarii, nie jest proste.
1. Ujednolicone środowisko programowania i wdrażania: istnieje wiele systemów iniekcji błędów, które mogą wykonywać różne typy awarii. Jednak podczas przechodzenia z jednokierunkowych scenariuszy programistycznych środowisko to może być niewystarczające, aby uruchomić te same testy w dużych środowiskach testowych, aby użyć ich do testów w środowisku produkcyjnym.

Chociaż istnieje wiele mechanizmów rozwiązywania tych problemów, system, który jest taki sam z wymaganymi gwarancjami — wszystko to w środowisku deweloperskim z jednym pudełkem, do testowania w klastrach produkcyjnych — brak. Usługa błędów analizy ułatwia deweloperom aplikacji skoncentrowanie się na testowaniu logiki biznesowej. Usługa analizy błędów zapewnia wszystkie możliwości wymagane do testowania interakcji usługi z podstawowym systemem rozproszonym.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Symulowanie/generowanie rzeczywistych scenariuszy błędów
Aby przetestować niezawodność systemu rozproszonego przed awariami, potrzebujemy mechanizmu generowania błędów. Chociaż jest to bardzo teoretyczne, generowanie niepowodzeń, podobnie jak węzeł w dół, rozpocznie działanie tego samego zestawu problemów ze spójnością, które Service Fabric próbuje rozwiązać. Na przykład jeśli chcemy zamknąć węzeł, wymagany przepływ pracy jest następujący:

1. Z poziomu klienta wydaj żądanie wyłączenia węzła.
1. Wyślij żądanie do prawego węzła.
   
    a. Jeśli węzeł nie zostanie znaleziony, powinien się nie powieść.
   
    b. Jeśli węzeł zostanie znaleziony, powinien zwrócić tylko wtedy, gdy węzeł jest wyłączony.

Aby sprawdzić niepowodzenie z perspektywy testowej, test musi wiedzieć, że w przypadku wystąpienia tego błędu faktycznie wystąpił błąd. Gwarancją, którą zapewnia Service Fabric, jest to, że węzeł zostanie wyłączony lub już wyłączony, gdy polecenie osiągnie węzeł. W każdym przypadku test powinien być w stanie prawidłowo przyczynić się do stanu i pomyślnie lub działać nieprawidłowo w jego weryfikacji. System wdrożony poza Service Fabric w celu wykonania tego samego zestawu błędów może obtrafiać wiele problemów dotyczących sieci, sprzętu i oprogramowania, co uniemożliwi mu dostarczenie powyższych gwarancji. W obecności powyższych problemów Service Fabric ponownie skonfiguruje stan klastra w celu obejścia problemów, a tym samym usługa analizy błędów nadal będzie mogła dać właściwy zestaw gwarancji.

### <a name="generating-required-events-and-scenarios"></a>Generowanie wymaganych zdarzeń i scenariuszy
Chociaż symulowanie rzeczywistego błędu w sposób ciągły jest trudne do uruchomienia, możliwość generowania skorelowanych błędów jest jeszcze trudniejsza. Na przykład utrata danych odbywa się w trwałej usłudze, gdy wystąpią następujące kwestie:

1. Tylko kworum zapisu replik są przechwytywane podczas replikacji. Wszystkie repliki pomocnicze są opóźnione w stosunku do podstawowego.
1. Kworum zapisu jest wyłączone ze względu na przechodzenie między replikami (ze względu na pakiet kodu lub węzeł).
1. Nie można wykonać kopii zapasowej kworum zapisu, ponieważ dane dla replik zostały utracone (ze względu na uszkodzenie dysku lub odtwarzanie maszynowe).

Te skorelowane błędy odbywają się w świecie rzeczywistym, ale nie tak często, jak poszczególne awarie. Możliwość testowania pod kątem tych scenariuszy, zanim wystąpią w środowisku produkcyjnym, ma kluczowe znaczenie. Jeszcze ważniejsze jest możliwość symulowania tych scenariuszy przy użyciu obciążeń produkcyjnych w kontrolowanych okolicznościach (w połowie dnia i wszystkich inżynierów na pokładzie). Jest to znacznie lepsze niż nastąpi po raz pierwszy w środowisku produkcyjnym o godzinie 2:00

### <a name="unified-experience-across-different-environments"></a>Ujednolicone środowisko w różnych środowiskach
Tradycyjnie opracowano trzy różne zestawy środowisk, jeden dla środowiska programistycznego, jeden dla testów i jeden do produkcji. Model:

1. W środowisku programistycznym program tworzy przejścia stanu, które zezwalają na testy jednostkowe poszczególnych metod.
1. W środowisku testowym program tworzy błędy, aby umożliwić kompleksowe testy, które wykonują różne scenariusze awarii.
1. Zachowaj Pristine środowiska produkcyjnego, aby zapobiec wszelkim nienaturalnym awariom i upewnić się, że występuje niezwykle szybka odpowiedź człowieka na awarię.

W Service Fabric za pośrednictwem usługi analizy błędów proponujemy jej włączenie i użycie tej samej metodologii ze środowiska deweloperskiego do produkcji. Istnieją dwa sposoby osiągnięcia tego celu:

1. Aby wywołać kontrolowane awarie, należy użyć interfejsów API usługi analizy błędów ze środowiska jednokrotnego w całym celu w przypadku klastrów produkcyjnych.
1. Aby nadać klastrowi pomór, która powoduje automatyczne zawinięcie błędów, Użyj usługi analizy błędów w celu wygenerowania błędów automatycznych. Kontrolowanie współczynnika błędów za pomocą konfiguracji umożliwia przetestowanie tej samej usługi w różny sposób w różnych środowiskach.

W Service Fabric, chociaż Skala błędów będzie różna w różnych środowiskach, rzeczywiste mechanizmy byłyby identyczne. Pozwala to na wiele szybszych potoków kodu i wdrożenia oraz możliwość testowania usług w ramach rzeczywistych obciążeń.

## <a name="using-the-fault-analysis-service"></a>Korzystanie z usługi analizy błędów
**C#**

Funkcje usługi analizy błędów znajdują się w przestrzeni nazw System. Fabric w pakiecie NuGet Microsoft. servicefabric. Aby korzystać z funkcji usługi analizy błędów, należy uwzględnić pakiet NuGet jako odwołanie w projekcie.

**Program PowerShell**

Aby użyć programu PowerShell, należy zainstalować zestaw SDK Service Fabric. Po zainstalowaniu zestawu SDK moduł servicefabric PowerShell jest ładowany do użycia.

## <a name="next-steps"></a>Następne kroki
Aby utworzyć prawdziwie usługi w skali chmury, należy upewnić się, że zarówno przed, jak i po wdrożeniu, usługi mogą wytrzymywać rzeczywiste awarie. Obecnie w świecie usług można szybko wprowadzać innowacje i szybko przenosić kod do produkcji. Usługa Fault Analysis ułatwia deweloperom usług precyzyjne wykonywanie tych czynności.

Zacznij testować aplikacje i usługi przy użyciu wbudowanych [scenariuszy testowych](service-fabric-testability-scenarios.md)lub twórz własne scenariusze testowe przy użyciu [działań błędów](service-fabric-testability-actions.md) udostępnianych przez usługę analizy błędów.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
