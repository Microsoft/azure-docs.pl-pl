---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl chaos
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do zarządzania chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f59eb3296c27e64eb6a4644b2f455e3704381f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260829"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Uruchamianie, zatrzymywanie i raportowanie w usłudze testowej chaos.

## <a name="subgroups"></a>Podgrupy
|Podgrupa|Opis|
| --- | --- |
| [rozkład](service-fabric-sfctl-chaos-schedule.md) | Pobierz i Ustaw harmonogram chaos. |
## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| zdarzenia | Pobiera następny segment zdarzeń chaos na podstawie tokenu kontynuacji lub zakresu czasu. |
| get | Pobierz stan chaos. |
| start | Uruchamia chaos w klastrze. |
| zatrzymanie | Program zatrzymuje chaos, jeśli jest uruchomiony w klastrze i umieszcza harmonogram chaos w stanie zatrzymanym. |

## <a name="sfctl-chaos-events"></a>zdarzenia chaos sfctl
Pobiera następny segment zdarzeń chaos na podstawie tokenu kontynuacji lub zakresu czasu.

Aby uzyskać następny segment zdarzeń chaos, możesz określić ContinuationToken. Aby rozpocząć nowy segment zdarzeń chaos, możesz określić zakres czasu za pomocą StartTimeUtc i EndTimeUtc. Nie można określić zarówno ContinuationToken, jak i zakresu czasu w tym samym wywołaniu. Jeśli istnieje więcej niż 100 zdarzeń chaos, zdarzenia chaos są zwracane w wielu segmentach, w których segment nie zawiera więcej niż 100 zdarzeń chaos, i aby uzyskać następny segment, należy wywołać ten interfejs API z tokenem kontynuacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --czas zakończenia — UTC | Czas pliku systemu Windows reprezentujący godzinę zakończenia zakresu czasu, dla którego ma zostać wygenerowany raport chaos. Aby uzyskać szczegółowe informacje, zapoznaj się z [metodą DateTime. ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) . |
| --Max-Results | Maksymalna liczba wyników do zwrócenia w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę wartości zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w komunikacie zgodnie z maksymalnymi ograniczeniami rozmiaru komunikatów zdefiniowanymi w konfiguracji. Jeśli ten parametr ma wartość zero lub nie zostanie określony, zapytanie stronicowane zawiera tyle wyników, ile to możliwe, które mieszczą się w komunikacie zwrotnym. |
| --czas rozpoczęcia — UTC | Czas pliku systemu Windows reprezentujący godzinę rozpoczęcia przedziału czasu, dla którego ma zostać wygenerowany raport chaos. Aby uzyskać szczegółowe informacje, zapoznaj się z [metodą DateTime. ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) . |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-get"></a>sfctl chaos Get
Pobierz stan chaos.

Pobierz stan chaos wskazujący, czy Chaos jest uruchomiony, chaos parametry używane do uruchamiania chaos oraz stan harmonogramu chaos.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-start"></a>sfctl chaos
Uruchamia chaos w klastrze.

Jeśli chaos nie jest już uruchomiona w klastrze, zaczyna chaos z przekazaniem parametrów chaos. Jeśli Chaos jest już uruchomiona podczas tego wywołania, wywołanie kończy się niepowodzeniem z kodem błędu FABRIC_E_CHAOS_ALREADY_RUNNING. Aby uzyskać więcej informacji, zapoznaj się z artykułem dotyczącym [kontrolowanego chaos w klastrach Service Fabric](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) .

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Type-Health-Policy-map | Zakodowana w formacie JSON tablica wpisów słownika (klucz/wartość) z maksymalną wartością procentową aplikacji w złej kondycji dla określonych typów aplikacji. Każdy wpis słownika określa jako klucz nazwę typu aplikacji oraz liczbę całkowitą dla wartości reprezentującej procent MaxPercentUnhealthyApplications używany do szacowania aplikacji określonego typu aplikacji. <br><br> Definiuje mapę z maksymalną wartością procentową aplikacji w złej kondycji dla określonych typów aplikacji. Mapa zasad kondycji typu aplikacji może być używana podczas oceny kondycji klastra do opisywania poszczególnych typów aplikacji. Typy aplikacji uwzględnione w mapie są oceniane względem wartości procentowej określonej w mapie, a nie dla globalnych MaxPercentUnhealthyApplications zdefiniowanych w zasadach dotyczących kondycji klastra. Aplikacje typów aplikacji określone w mapie nie są zliczane względem globalnej puli aplikacji. Jeśli na przykład niektóre aplikacje typu mają krytyczne znaczenie, administrator klastra może dodać wpis do mapy dla tego typu aplikacji i przypisać mu wartość 0% (nie można tolerować żadnych błędów). Wszystkie inne aplikacje można ocenić przy użyciu MaxPercentUnhealthyApplications o wartości 20%, aby tolerować błędy z tysięcy wystąpień aplikacji. Mapa zasad kondycji typu aplikacji jest używana tylko wtedy, gdy manifest klastra umożliwia ocenę kondycji typu aplikacji przy użyciu wpisu ConfigurationManager/EnableApplicationTypeHealthEvaluation. <br><br> Przykładowy zakodowany ciąg JSON: [{ \" Key \" : \" Fabric:/głosu \" , \" wartość \" : \" 0 \" }] |
| --Chaos-Target-Filter | Zakodowany słownik JSON z dwoma kluczami typu String. Dwa klucze to NodeTypeInclusionList i ApplicationInclusionList. Oba te klucze są wartościami typu String. chaos_target_filter definiuje wszystkie filtry dla dokierowanych błędów chaos, na przykład błędy tylko niektórych typów węzłów lub błędy tylko dla niektórych aplikacji. <br><br> Jeśli chaos_target_filter nie jest używany, chaos błędów wszystkich jednostek klastra. Jeśli chaos_target_filter jest używany, chaos błędy tylko jednostki, które spełniają wymagania chaos_target_filter. NodeTypeInclusionList i ApplicationInclusionList zezwalają tylko na semantykę Unii. Nie można określić przecięcia z NodeTypeInclusionList i ApplicationInclusionList. Na przykład nie jest możliwe określenie "błąd tej aplikacji tylko wtedy, gdy znajduje się ona w danym typie węzła". Gdy jednostka jest uwzględniona w NodeTypeInclusionList lub ApplicationInclusionList, ta jednostka nie może zostać wykluczona przy użyciu ChaosTargetFilter. Nawet jeśli applicationX nie pojawia się w ApplicationInclusionList, w niektórych chaos iteracji applicationX może być uszkodzona, ponieważ występuje w węźle nodeTypeY, który jest zawarty w NodeTypeInclusionList. Jeśli zarówno NodeTypeInclusionList, jak i ApplicationInclusionList są puste, zostanie zgłoszony ArgumentException. Wszystkie typy błędów (węzeł Uruchom ponownie, uruchom ponownie pakiet kodu, Usuń replikę, replika ponownego uruchomienia, Przenieś podstawowy i Przenieś pomocniczy) są włączone dla węzłów tych typów węzłów. Jeśli typ węzła (Powiedz NodeTypeX) nie jest wyświetlany w NodeTypeInclusionList, wówczas błędy na poziomie węzła (takie jak NodeRestart) nigdy nie będą włączone dla węzłów NodeTypeX, ale pakiet kodu i błędy replik nadal mogą być włączone dla NodeTypeX, jeśli aplikacja w ApplicationInclusionList ma się znajdować w węźle NodeTypeX. Na tej liście można umieścić maksymalnie 100 nazw typów węzłów, aby zwiększyć tę liczbę, do konfiguracji MaxNumberOfNodeTypesInChaosEntityFilter jest wymagane uaktualnienie konfiguracji. Wszystkie repliki należące do usług tych aplikacji są gotowe do błędów replik (ponownie uruchom replikę, Usuń replikę, Przenieś podstawową i Przenieś pomocniczą) przez chaos. Chaos może ponownie uruchomić pakiet kodu tylko wtedy, gdy pakiet kodu hostuje repliki tych aplikacji. Jeśli aplikacja nie jest wyświetlana na tej liście, nadal może być uszkodzona w pewnej iteracji chaos, jeśli aplikacja zostanie zakończona w węźle typu węzła, który jest zawarty w NodeTypeInclusionList. Jeśli jednak applicationX jest powiązany z nodeTypeY za pośrednictwem ograniczeń umieszczania, a applicationX nie jest obecny w ApplicationInclusionList i nodeTypeY nie jest w NodeTypeInclusionList, wówczas applicationX nie będzie nigdy występować. Na tej liście można umieścić maksymalnie 1000 nazw aplikacji, aby zwiększyć tę liczbę, do konfiguracji MaxNumberOfApplicationsInChaosEntityFilter jest wymagane uaktualnienie konfiguracji. |
| --kontekst | Zakodowana w formacie JSON Tablica par klucz-wartość (String, String). Mapa może służyć do rejestrowania informacji o przebiegu chaos. Nie może być więcej niż 100 takich par, a każdy ciąg (klucz lub wartość) może mieć maksymalnie 4095 znaków. Ta mapa jest ustawiana przez Starter chaos Run, aby opcjonalnie przechowywać kontekst o określonym przebiegu. |
| --Disable-rereplica-faults | Wyłącza dodatkowe błędy przenoszenia podstawowego i przenoszenia. |
| -— maks. — stabilizacja klastra | Maksymalny czas oczekiwania, aż wszystkie jednostki klastra staną się stabilne i w dobrej kondycji.  Wartość domyślna \: 60. <br><br> Chaos wykonuje iteracje i na początku każdej iteracji sprawdza kondycję jednostek klastra. Podczas sprawdzania poprawności, jeśli jednostka klastra nie jest stabilna i w dobrej kondycji w ramach MaxClusterStabilizationTimeoutInSeconds, chaos generuje zdarzenie niepowodzenia walidacji. |
| --Max-współbieżne-błędy | Maksymalna liczba współbieżnych błędów wywołanych na iterację. Chaos wykonuje w iteracjach, a dwie kolejne iteracje są oddzielone etapami walidacji. Im wyższy poziom współbieżności, tym trudniejsze jest iniekcja błędów — wywoływanie bardziej złożonej serii Stanów do odkrywania usterek. Zalecenie ma zacząć się od wartości 2 lub 3 i aby zachować ostrożność podczas przesuwania.  Wartość domyślna \: 1. |
| --maks-% — zła kondycja — aplikacje | Podczas oceny kondycji klastra podczas chaos, maksymalna dozwolona wartość procentowa aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. Na przykład, aby umożliwić złej kondycji 10% aplikacji, ta wartość będzie równa 10. Wartość procentowa reprezentuje maksymalny dopuszczalny procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest szacowana jako ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na łączną liczbę wystąpień aplikacji w klastrze, z wyłączeniem aplikacji typów aplikacji, które są uwzględnione w ApplicationTypeHealthPolicyMap. Obliczenia są zaokrąglane w górę, aby tolerować jeden błąd w niewielkiej liczbie aplikacji. Wartość domyślna to zero. |
| --maks-procent — zła kondycja — węzły | Podczas oceny kondycji klastra podczas chaos Maksymalna dozwolona wartość procentowa węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Maksymalny dozwolony procent węzłów w złej kondycji przed zgłoszeniem błędu. Na przykład aby umożliwić złej kondycji 10% węzłów, ta wartość będzie równa 10. Wartość procentowa reprezentuje maksymalny dopuszczalny procent węzłów, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycja jest szacowana jako ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji na łączną liczbę węzłów w klastrze. Obliczenia są zaokrąglane w górę, aby tolerować jeden błąd w niewielkiej liczbie węzłów. Wartość domyślna to zero. W dużych klastrach niektóre węzły będą zawsze wyłączane lub wychodzące w przypadku napraw, więc ta wartość procentowa powinna być skonfigurowana w taki sposób, że. |
| --czas do uruchomienia | Łączny czas (w sekundach), przez który chaos będzie uruchamiany przed automatycznym zatrzymaniem. Maksymalna dozwolona wartość to 4 294 967 295 (System. UInt32. MaxValue).  Wartość domyślna \: 4294967295. |
| --timeout-t | Wartość domyślna \: 60. |
| --czas oczekiwania-między awariami | Czas oczekiwania (w sekundach) między kolejnymi błędami w ramach jednej iteracji.  Domyślnie \: 20. <br><br> Im większa wartość, tym mniejsza liczba błędów i prostsze sekwencje przejść między Stanami, które klaster przejdzie. Zaleca się, aby zacząć od wartości od 1 do 5 i zachować ostrożność podczas przesuwania. |
| --czas oczekiwania-między iteracjami | Separacja czasu (w sekundach) między dwoma kolejnymi iteracjami chaos. Im większa wartość, tym mniejsza liczba iniekcji błędów.  Wartość domyślna to \: 30. |
| --Ostrzeżenie-jako błąd | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos
Program zatrzymuje chaos, jeśli jest uruchomiony w klastrze i umieszcza harmonogram chaos w stanie zatrzymanym.

Uniemożliwia chaos wykonywania nowych błędów. Błędy w locie będą nadal wykonywane, dopóki nie zostaną ukończone. Bieżący harmonogram chaos został przesunięty w stan zatrzymania. Po zatrzymaniu harmonogramu pozostanie on w stanie zatrzymania i nie będzie używany do chaos harmonogramu nowych przebiegów chaos. Aby wznowić planowanie, należy ustawić nowy harmonogram chaos.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](./scripts/sfctl-upgrade-application.md).
