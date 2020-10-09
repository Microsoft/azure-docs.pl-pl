---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — zdarzenia sfctl
description: Opisuje Service Fabric polecenia zdarzeń sfctl CLI.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76906095"
---
# <a name="sfctl-events"></a>zdarzenia sfctl
Pobierz zdarzenia ze sklepu Events (Jeśli usługa EventStore jest już zainstalowana).

Usługę systemową EventStore można dodać w ramach uaktualnienia konfiguracji do dowolnego klastra SFRP z systemem >= 6,4. Sprawdź następujący adres URL \: https \: //docs.Microsoft.com/Azure/Service-Fabric/Service-Fabric-Diagnostics-eventstore.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| wszystkie-aplikacje-lista | Pobiera wszystkie zdarzenia związane z aplikacjami. |
| wszystkie węzły — lista | Pobiera wszystkie zdarzenia związane z węzłami. |
| wszystkie partycje — lista | Pobiera wszystkie zdarzenia związane z partycjami. |
| wszystkie-usługi-list | Pobiera wszystkie zdarzenia związane z usługami. |
| Lista aplikacji | Pobiera zdarzenia dotyczące aplikacji. |
| Lista klastrów | Pobiera wszystkie zdarzenia związane z klastrem. |
| Lista węzłów | Pobiera zdarzenia związane z węzłem. |
| Partition-All-Replicas-list | Pobiera wszystkie zdarzenia związane z replikami dla partycji. |
| Lista partycji | Pobiera zdarzenia związane z partycją. |
| Partition-Replica-list | Pobiera zdarzenia związane z repliką partycji. |
| Lista usług | Pobiera zdarzenia dotyczące usługi. |

## <a name="sfctl-events-all-applications-list"></a>wszystkie zdarzenia sfctl — lista aplikacji
Pobiera wszystkie zdarzenia związane z aplikacjami.

Odpowiedź jest listą obiektów ApplicationEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-all-nodes-list"></a>Lista zdarzeń sfctl — wszystkie węzły
Pobiera wszystkie zdarzenia związane z węzłami.

Odpowiedź jest listą obiektów NodeEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-all-partitions-list"></a>wszystkie zdarzenia sfctl — lista partycji
Pobiera wszystkie zdarzenia związane z partycjami.

Odpowiedź jest listą obiektów PartitionEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-all-services-list"></a>wszystkie usługi — lista zdarzeń sfctl
Pobiera wszystkie zdarzenia związane z usługami.

Odpowiedź jest listą obiektów serviceevents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-application-list"></a>Lista zdarzeń sfctl
Pobiera zdarzenia dotyczące aplikacji.

Odpowiedź jest listą obiektów ApplicationEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-cluster-list"></a>Lista klastrów zdarzeń sfctl
Pobiera wszystkie zdarzenia związane z klastrem.

Odpowiedź jest listą obiektów ClusterEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-node-list"></a>węzeł Lista zdarzeń sfctl
Pobiera zdarzenia związane z węzłem.

Odpowiedź jest listą obiektów NodeEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-partition-all-replicas-list"></a>partycja zdarzeń sfctl — wszystkie repliki — lista
Pobiera wszystkie zdarzenia związane z replikami dla partycji.

Odpowiedź jest listą obiektów ReplicaEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-partition-list"></a>partycja zdarzeń sfctl — lista
Pobiera zdarzenia związane z partycją.

Odpowiedź jest listą obiektów PartitionEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-partition-replica-list"></a>partycja zdarzeń sfctl-Replica-list
Pobiera zdarzenia związane z repliką partycji.

Odpowiedź jest listą obiektów ReplicaEvent.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Replica-ID [wymagane] | Identyfikator repliki. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-events-service-list"></a>Usługa zdarzeń sfctl — lista
Pobiera zdarzenia dotyczące usługi.

Odpowiedź jest listą obiektów serviceevents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --czas zakończenia — UTC [wymagane] | Godzina zakończenia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --Start-Time-UTC [wymagane] | Godzina rozpoczęcia zapytania wyszukiwania w formacie ISO UTC RRRR-MM-DDTgg \: mm \: SSS. |
| --Events-Types-Filter | Jest to ciąg rozdzielony przecinkami określający typy FabricEvents, które powinny być uwzględniane tylko w odpowiedzi. |
| --Exclude-Analysis-Events | Ten parametr param wyłącza pobieranie elementu AnalysisEvents, jeśli wartość jest równa true. |
| --Skip-korelacja | Ten parametr param wyłącza wyszukiwanie informacji CorrelatedEvents w przypadku, gdy wartość jest równa true. w przeciwnym razie pole CorrelationEvents przetworzono i HasCorrelatedEvents w każdym FabricEvent zostanie wypełnione. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

