---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — węzeł sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń zarządzania węzłami klastra.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f2cf1011db37892f71bdd06f409cec1a76463507
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257149"
---
# <a name="sfctl-node"></a>sfctl node
Zarządzaj węzłami, które tworzą klaster.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Add-Configuration-Parameter-Overrides | Dodaje listę zastąpień konfiguracji w określonym węźle. |
| Wyłącz | Dezaktywuj węzeł klastra Service Fabric z określonym zamiarem dezaktywacji. |
| Włącz | Aktywuj Service Fabric węzeł klastra, który jest obecnie dezaktywowany. |
| Get-Configuration — zastąpienia | Pobiera listę zastąpień konfiguracji w określonym węźle. |
| kondycji | Pobiera kondycję węzła Service Fabric. |
| informacje o | Pobiera informacje o określonym węźle w klastrze Service Fabric. |
| list | Pobiera listę węzłów w klastrze Service Fabric. |
| ładowanie | Pobiera informacje o ładowaniu węzła Service Fabric. |
| Usuń konfigurację — zastąpienia | Usuwa zastąpienia konfiguracji w określonym węźle. |
| Usuń stan | Powiadamia Service Fabric, że trwały stan w węźle został trwale usunięty lub utracony. |
| Raport — kondycja | Wysyła raport o kondycji w węźle Service Fabric. |
| restart | Uruchamia ponownie węzeł klastra Service Fabric. |
| przejście | Uruchamia lub wstrzymuje węzeł klastra. |
| przejście — stan | Pobiera postęp operacji rozpoczętej przy użyciu StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl węzła Add-Configuration-Parameter-Overrides
Dodaje listę zastąpień konfiguracji w określonym węźle.

Ten interfejs API umożliwia dodawanie wszystkich istniejących zastąpień konfiguracji w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --config-Parameter-override-list [wymagane] | Opis dodawania listy zastąpień konfiguracji. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Wymuś | Wymuś Dodawanie zastąpień konfiguracji do określonych węzłów. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-disable"></a>Wyłącz węzeł sfctl
Dezaktywuj węzeł klastra Service Fabric z określonym zamiarem dezaktywacji.

Dezaktywuj węzeł klastra Service Fabric z określonym zamiarem dezaktywacji. Po zakończeniu dezaktywacji można zwiększyć cel dezaktywacji, ale nie został on obniżony (na przykład węzeł, który jest dezaktywowany z intencją wstrzymania, może zostać zdezaktywowany ponownie, ale nie w inny sposób. Węzły mogą być ponownie uaktywniane przy użyciu operacji aktywowania węzła w dowolnym momencie po jej zdezaktywowaniu. Jeśli dezaktywacja nie zostanie zakończona, spowoduje to anulowanie dezaktywacji. Węzeł, który przejdzie i połączy się, gdy dezaktywowany będzie nadal musiał być ponownie aktywowany, zanim usługi zostaną umieszczone w tym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Dezaktywacja — zamiar | Opisuje intencję lub przyczynę dezaktywowania węzła. Możliwe wartości są następujące. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-enable"></a>Włączanie węzła sfctl
Aktywuj Service Fabric węzeł klastra, który jest obecnie dezaktywowany.

Aktywuje Service Fabric węzeł klastra, który jest obecnie dezaktywowany. Po aktywowaniu węzeł będzie ponownie działać jako obiekt docelowy do umieszczania nowych replik, a wszystkie zdezaktywowane repliki pozostałe w węźle zostaną uaktywnione ponownie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl węzła Get-Configuration — zastąpień
Pobiera listę zastąpień konfiguracji w określonym węźle.

Ten interfejs API umożliwia pobieranie wszystkich istniejących zastąpień konfiguracji w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-health"></a>Kondycja węzła sfctl
Pobiera kondycję węzła Service Fabric.

Pobiera kondycję węzła Service Fabric. Użyj EventsHealthStateFilter, aby odfiltrować kolekcję zdarzeń kondycji zgłoszonych w węźle na podstawie stanu kondycji. Jeśli węzeł określony przez nazwę nie istnieje w magazynie kondycji, spowoduje to zwrócenie błędu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Events-kondycja-filtr | Umożliwia filtrowanie kolekcji obiektów HealthEvent zwracanych na podstawie stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia zgodne z filtrem. Wszystkie zdarzenia są używane do oszacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, zwracane są wszystkie zdarzenia z wartością HealthState równą OK (2) i ostrzeżenie (4).  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-info"></a>Informacje o węźle sfctl
Pobiera informacje o określonym węźle w klastrze Service Fabric.

Odpowiedź obejmuje nazwę, stan, identyfikator, kondycję, czas przestoju i inne szczegóły dotyczące węzła.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-list"></a>Lista węzłów sfctl
Pobiera listę węzłów w klastrze Service Fabric.

Odpowiedź obejmuje nazwę, stan, identyfikator, kondycję, czas przestoju i inne szczegóły dotyczące węzłów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --Max-Results | Maksymalna liczba wyników do zwrócenia w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę wartości zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w komunikacie zgodnie z maksymalnymi ograniczeniami rozmiaru komunikatów zdefiniowanymi w konfiguracji. Jeśli ten parametr ma wartość zero lub nie zostanie określony, zapytanie stronicowane zawiera tyle wyników, ile to możliwe, które mieszczą się w komunikacie zwrotnym. |
| --Node-status-Filter | Umożliwia filtrowanie węzłów na podstawie NodeStatus. Zostaną zwrócone tylko węzły pasujące do określonej wartości filtru. Wartość filtru może być jedną z następujących wartości.  Domyślna wartość domyślna \: . |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-load"></a>Ładowanie węzłów sfctl
Pobiera informacje o ładowaniu węzła Service Fabric.

Pobiera informacje o ładowaniu Service Fabric węzła dla wszystkich metryk, dla których zdefiniowano obciążenie lub pojemność.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-remove-configuration-overrides"></a>węzeł sfctl Usuń konfigurację — zastąpienia
Usuwa zastąpienia konfiguracji w określonym węźle.

Ten interfejs API umożliwia usunięcie wszystkich istniejących zastąpień konfiguracji w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-remove-state"></a>sfctl węzła usuwania stanu
Powiadamia Service Fabric, że trwały stan w węźle został trwale usunięty lub utracony.

Oznacza to, że nie jest możliwe odzyskanie utrwalonego stanu tego węzła. Zwykle dzieje się tak, jeśli dysk twardy został oczyszczony z czyszczeniem lub jeśli dysk twardy ulegnie awarii. Aby ta operacja zakończyła się pomyślnie, węzeł musi być wyłączony. Ta operacja umożliwia Service Fabric informacji o tym, że repliki w tym węźle już nie istnieją, a Service Fabric powinny przestać czekać na kopie zapasowe tych replik. Nie uruchamiaj tego polecenia cmdlet, jeśli stan węzła nie został usunięty, a węzeł może utworzyć kopię zapasową w stanie niezmienionym. Począwszy od Service Fabric 6,5, aby użyć tego interfejsu API dla węzłów inicjatora, Zmień węzły inicjatora na regularne (nie będące inicjatorami), a następnie Wywołaj ten interfejs API w celu usunięcia stanu węzła. Jeśli klaster działa na platformie Azure, po awarii węzła inicjatora Service Fabric spróbuje zmienić go na węzeł niebędący inicjatorem. Aby to osiągnąć, upewnij się, że liczba węzłów niebędących inicjatorami w typie podstawowym węzła nie jest mniejsza niż liczba węzłów początkowych. W razie potrzeby Dodaj więcej węzłów do typu węzła podstawowego, aby to osiągnąć. W przypadku klastra autonomicznego, jeśli nie ma potrzeby tworzenia kopii zapasowej węzła inicjatora w dół w stanie nienaruszonym, Usuń węzeł z klastra, a następnie wybierz pozycję https \: //docs.Microsoft.com/en-us/Azure/Service-Fabric/Service-Fabric-Cluster-Windows-Server-Add-Remove-nodes.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-report-health"></a>Raport węzła sfctl — kondycja
Wysyła raport o kondycji w węźle Service Fabric.

Informuje o stanie kondycji określonego węzła Service Fabric. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raportowany. Raport jest wysyłany do węzła bramy Service Fabric, który przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak stary numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Health-Property [wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem i nie jest stałym wyliczeniem, aby umożliwić elastyczność programu Reporter na kategoryzowanie warunku stanu, który wyzwala raport. Na przykład, Raport z identyfikatorem SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może raportować Właściwość "AvailableDisk" w tym węźle. Ten sam raport może monitorować łączność z węzłami, dzięki czemu może zgłosić Właściwość "łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia dotyczące kondycji dla określonego węzła. Wraz z identyfikatorem SourceId Właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --kondycja — stan [wymagane] | Możliwe wartości to \: "nieprawidłowe", "OK", "ostrzeżenie", "błąd", "nieznany". |
| --Node-Name [wymagane] | Nazwa węzła. |
| --source-ID [wymagane] | Nazwa źródłowa, która identyfikuje składnik klienta/alarm/składnika systemu, który wygenerował informacje o kondycji. |
| --Description | Opis informacji o kondycji. <br><br> Reprezentuje on bezpłatny tekst używany do dodawania informacji ludzkich o raporcie. Maksymalna długość ciągu dla opisu to 4096 znaków. Jeśli podany ciąg jest dłuższy, zostanie automatycznie obcięty. Po obcięciu ostatnie znaki opisu zawierają znacznik "[obcięty]", a całkowity rozmiar ciągu to 4096 znaków. Obecność znacznika wskazuje użytkownikom, że wystąpiło obcinanie. Należy pamiętać, że w przypadku obcinania opis ma mniej niż 4096 znaków od oryginalnego ciągu. |
| --natychmiastowe | Flaga wskazująca, czy raport ma być wysyłany od razu. <br><br> Raport o kondycji jest wysyłany do aplikacji bramy Service Fabric, która przekazuje do magazynu kondycji. Jeśli jest ustawiona wartość true, raport jest wysyłany natychmiast z bramy HTTP do magazynu kondycji, niezależnie od ustawień klienta sieci szkieletowej używanej przez aplikację bramy HTTP. Jest to przydatne w przypadku raportów o kluczowym znaczeniu, które powinny być wysyłane najszybciej, jak to możliwe. W zależności od chronometrażu i innych warunków wysyłanie raportu może nadal zakończyć się niepowodzeniem, na przykład jeśli Brama HTTP jest ZAMKNIĘTA lub komunikat nie dociera do bramy. Jeśli wartość jest ustawiona na wartość FAŁSZ, raport jest wysyłany na podstawie ustawień klienta kondycji z bramy HTTP. W związku z tym będzie on przetwarzany wsadowo zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie, ponieważ pozwala klientowi kondycji zoptymalizować komunikaty raportowania kondycji do magazynu kondycji, a także przetwarzania raportów kondycji. Domyślnie raporty nie są wysyłane od razu. |
| --Usuń po wygaśnięciu | Wartość wskazująca, czy raport został usunięty z magazynu kondycji po jego wygaśnięciu. <br><br> W przypadku ustawienia wartości true raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli zostanie ustawiona na wartość false, raport jest traktowany jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie fałszywa. Gdy klienci raportują okresowo, powinni ustawić RemoveWhenExpired false (domyślnie). W ten sposób, to program reporter ma problemy (np. zakleszczenie) i nie może zgłosić, gdy Raport kondycji wygaśnie. Oznacza to, że jednostka jest w stanie błędu kondycji. |
| --Sequence-Number | Numer sekwencyjny dla tego raportu kondycji jako ciąg liczbowy. <br><br> Numer sekwencyjny raportu jest używany przez magazyn kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer sekwencyjny jest generowany automatycznie przez klienta kondycji, gdy raport zostanie dodany. |
| --timeout-t | Wartość domyślna \: 60. |
| --TTL | Czas trwania okresu ważności tego raportu kondycji. To pole używa formatu ISO8601 do określenia czasu trwania. <br><br> Gdy klienci raportują okresowo, powinni wysyłać raporty o wyższej częstotliwości niż czas wygaśnięcia. Jeśli klienci raportują przejście, mogą ustawić czas wygaśnięcia na wartość nieskończoną. Po wygaśnięciu wygaśnięcia zdarzenia dotyczącego kondycji, które zawiera informacje o kondycji, zostaną usunięte z magazynu kondycji, jeśli RemoveWhenExpired ma wartość true lub jest oceniane w przypadku błędu, jeśli RemoveWhenExpired false. Jeśli nie zostanie określony, wartość czasu wygaśnięcia będzie równa wartości nieskończonej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-restart"></a>ponowne uruchomienie węzła sfctl
Uruchamia ponownie węzeł klastra Service Fabric.

Uruchamia ponownie Service Fabric węzeł klastra, który jest już uruchomiony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Create-Fabric-dump | Określ wartość true, aby utworzyć zrzut procesu węzła sieci szkieletowej. Wielkość liter nie jest rozróżniana.  Wartość domyślna to \: false. |
| --Node-instance-ID | Identyfikator wystąpienia węzła docelowego. Jeśli określono identyfikator wystąpienia, węzeł jest uruchamiany ponownie tylko wtedy, gdy jest zgodny z bieżącym wystąpieniem węzła. Wartość domyślna "0" będzie zgodna z dowolnym IDENTYFIKATORem wystąpienia. Identyfikator wystąpienia można uzyskać przy użyciu zapytania Get Node.  Wartość domyślna to \: 0. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-transition"></a>sfctl przejście węzła
Uruchamia lub wstrzymuje węzeł klastra.

Uruchamia lub wstrzymuje węzeł klastra.  Węzeł klastra to proces, a nie sam wystąpienie systemu operacyjnego.  Aby uruchomić węzeł, należy przekazać "Start" dla parametru NodeTransitionType. Aby zatrzymać węzeł, należy przekazać wartość "Stop" dla parametru NodeTransitionType. Ten interfejs API uruchamia operację — gdy interfejs API zwróci węzeł, być może nie zakończył jeszcze kończenia przejścia. Wywołaj GetNodeTransitionProgress z tym samym OperationId, aby uzyskać postęp operacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-instance-ID [wymagane] | Identyfikator wystąpienia węzła docelowego. Można to ustalić za poorednictwem interfejsu API GetNodeInfo. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Node-Transition-Type [wymagane] | Wskazuje typ przejścia do wykonania.  NodeTransitionType. Start uruchomi zatrzymany węzeł. NodeTransitionType. Stop zatrzymuje węzeł, który jest w stanie up. |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
| --Stop-Duration-in-Seconds [Required] | Czas zatrzymania węzła (w sekundach).  Wartość minimalna to 600, maksimum to 14400.  Po upływie tego czasu w węźle zostanie automatycznie utworzona kopia zapasowa. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-node-transition-status"></a>przejście węzła sfctl — stan
Pobiera postęp operacji rozpoczętej przy użyciu StartNodeTransition.

Pobiera postęp operacji rozpoczętej z StartNodeTransition przy użyciu podanej OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
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
