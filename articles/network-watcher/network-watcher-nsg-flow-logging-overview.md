---
title: Wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń
titleSuffix: Azure Network Watcher
description: W tym artykule wyjaśniono, jak korzystać z funkcji sieciowej grupy zabezpieczeń Logs dzienników przepływu pracy w usłudze Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 792908236c4f240db64bd3899474d779d5b0570c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018107"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń

## <a name="introduction"></a>Wprowadzenie

Dzienniki przepływu [sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md#security-rules) (sieciowej grupy zabezpieczeń) to funkcja platformy Azure Network Watcher, która umożliwia rejestrowanie informacji o ruchu IP przepływających przez sieciowej grupy zabezpieczeń. Dane przepływu są wysyłane do kont usługi Azure Storage z lokalizacji, w której można uzyskać do nich dostęp, a także wyeksportować je do dowolnego narzędzia wizualizacji, SIEM lub wybranych przez siebie identyfikatorów.

![Dzienniki przepływów — Omówienie](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Dlaczego warto używać dzienników przepływów?

Niezbędne jest monitorowanie własnej sieci i zarządzanie nią w celu naruszenia bezpieczeństwa, zgodności i wydajności. Znajomość własnego środowiska ma najważniejsze znaczenie dla ochrony i optymalizacji. Często trzeba znać bieżący stan sieci, który nawiązuje połączenie, z którym są nawiązywane połączenia, które porty są otwarte dla Internetu, oczekiwano zachowania sieci, nieregularne zachowanie sieci i nagłe wzrost ruchu.

Dzienniki przepływu są źródłem prawdy dla całej aktywności sieciowej w środowisku chmury. Bez względu na to, czy korzystasz z nadchodzącego uruchomienia podczas próby zoptymalizowania zasobów lub dużego przedsiębiorstwa próbującego wykryć wtargnięcie, dzienniki przepływów są najlepszym trafieniem. Służy do optymalizowania przepływów sieci, monitorowania przepływności, sprawdzania zgodności, wykrywania nieautoryzowanych i innych operacji.

## <a name="common-use-cases"></a>Typowe przypadki użycia

**Monitorowanie sieci**: Identyfikowanie nieznanego lub niepożądanego ruchu. Monitoruj poziomy ruchu i użycie przepustowości. Filtrowanie dzienników przepływów według adresów IP i portów w celu zrozumienia zachowania aplikacji. Eksportuj dzienniki przepływów do wybranych narzędzi do analizy i wizualizacji, aby skonfigurować pulpity nawigacyjne monitorowania.

**Monitorowanie użycia i optymalizacja:** Identyfikuj najpopularniejsze rozmowy w sieci. Połącz z danymi GeoIP, aby zidentyfikować ruch między regionami. Zrozumienie wzrostu ruchu w przypadku prognozowania pojemności. Użyj danych, aby usunąć overtly restrykcyjne reguły ruchu.

**Zgodność**: Użyj danych przepływu, aby zweryfikować izolację sieci i zgodność z regułami dostępu przedsiębiorstwa

**Network dowodowych & analiza zabezpieczeń**: analizowanie przepływów sieci ze złamanych adresów IP i interfejsów sieciowych. Eksportuj dzienniki przepływów do dowolnego wybranego narzędzia SIEM lub identyfikatora.

## <a name="how-logging-works"></a>Jak działa rejestrowanie

**Właściwości klucza**

- Dzienniki przepływów działają w [warstwie 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) i nagrywają wszystkie PRZEPŁYWy IP wychodzące i wychodzące z sieciowej grupy zabezpieczeń
- Dzienniki są zbierane za pomocą platformy Azure i nie mają wpływu na zasoby klientów ani wydajność sieci.
- Dzienniki są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące dla każdej reguły sieciowej grupy zabezpieczeń.
- Każdy rekord dziennika zawiera interfejs sieciowy (karta sieciowa), do którego odnosi się ten przepływ, do 5 informacji o spójnej kolekcji, & decyzja o ruchu (tylko wersja 2). Szczegółowe informacje znajdują się w poniższym _formacie dziennika_ .
- Dzienniki przepływu mają funkcję przechowywania, która umożliwia automatyczne usuwanie dzienników do roku po ich utworzeniu. 

> [!NOTE]
> Przechowywanie jest dostępne tylko w przypadku korzystania z [kont magazynu ogólnego przeznaczenia w wersji 2 (GPv2)](../storage/common/storage-account-overview.md#types-of-storage-accounts). 

**Podstawowe pojęcia**

- Sieci zdefiniowane w oprogramowaniu są zorganizowane wokół sieci wirtualnych (sieci wirtualnych) i podsieci. Zabezpieczeniami tych sieci wirtualnych i podsieci można zarządzać przy użyciu sieciowej grupy zabezpieczeń.
- Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) zawiera listę _reguł zabezpieczeń_ , które zezwalają na ruch sieciowy w zasobach, do których jest podłączony. Sieciowych grup zabezpieczeń można kojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi lub interfejsami sieciowymi (NIC) podłączonymi do maszyn wirtualnych (Menedżer zasobów). Aby uzyskać więcej informacji, zobacz [Omówienie grup zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md?toc=%252fazure%252fnetwork-watcher%252ftoc.json).
- Wszystkie przepływy ruchu w sieci są oceniane przy użyciu reguł w odpowiednich sieciowej grupy zabezpieczeń.
- Wynikiem tych ocen są dzienniki przepływu sieciowej grupy zabezpieczeń. Dzienniki przepływów są zbierane za pomocą platformy Azure i nie wymagają żadnej zmiany w zasobach klienta.
- Uwaga: reguły są dwa typy — kończąc & niekończący, z których każdy ma inne zachowanie rejestrowania.
- - Reguły odmowy sieciowej grupy zabezpieczeń są przerywane. SIECIOWEJ grupy zabezpieczeń odmowy ruchu spowoduje zarejestrowanie go w dziennikach przepływów, a przetwarzanie w tym przypadku zostanie zatrzymane po jakimkolwiek sieciowej grupy zabezpieczeń odmówiono ruchu. 
- - Reguły zezwalania sieciowej grupy zabezpieczeń są niekończące się, co oznacza, że nawet jeśli jeden sieciowej grupy zabezpieczeń ją umożliwia, przetwarzanie będzie kontynuowane do następnego sieciowej grupy zabezpieczeń. Ostatnie sieciowej grupy zabezpieczeń zezwalające na ruch rejestruje ruch do dzienników przepływów.
- Dzienniki przepływu sieciowej grupy zabezpieczeń są zapisywane na kontach magazynu z lokalizacji, w której można uzyskać do nich dostęp.
- Dzienniki przepływu można eksportować, przetwarzać, analizować i wizualizować przy użyciu narzędzi takich jak TA, Splunk, Grafana, Stealthwatch itd.

## <a name="log-format"></a>Format dziennika

Dzienniki przepływów obejmują następujące właściwości:

* **czas,** w którym zdarzenie zostało zarejestrowane
* **systemId** — identyfikator systemu grupy zabezpieczeń sieci.
* **Kategoria** — Kategoria zdarzenia. Kategoria jest zawsze **NetworkSecurityGroupFlowEvent**
* **ResourceID** — identyfikator zasobu sieciowej grupy zabezpieczeń
* **OperationName** — zawsze NetworkSecurityGroupFlowEvents
* **Właściwości** — kolekcja właściwości przepływu
    * Numer **wersji w** schemacie zdarzeń dziennika przepływu
    * **przepływy** — kolekcja przepływów. Ta właściwość ma wiele wpisów dla różnych reguł
        * **reguła** — reguła, dla której są wyświetlane przepływy
            * **przepływy** — kolekcja przepływów
                * **Mac** — adres MAC karty sieciowej dla maszyny wirtualnej, w której zebrano przepływ
                * **flowTuples** — ciąg zawierający wiele właściwości krotki przepływu w formacie rozdzielonym przecinkami
                    * **Sygnatura czasowa** — ta wartość jest sygnaturą czasową, gdy przepływ wystąpił w formacie epoki systemu UNIX
                    * **Źródłowy adres IP** — źródłowy adres IP
                    * **Docelowy adres IP** — docelowy adres IP
                    * **Port źródłowy** — port źródłowy
                    * **Port docelowy** — port docelowy
                    * **Protokół** — protokół przepływu. Prawidłowe wartości to **T** dla protokołów TCP i **U** dla protokołu UDP
                    * **Przepływ ruchu** — kierunek przepływu ruchu. Prawidłowe **wartości to dla** ruchu przychodzącego i **o** dla ruchu wychodzącego.
                    * **Decyzja o ruchu** — czy ruch był dozwolony lub zabroniony. Prawidłowe **wartości to dozwolone** i **D** na odmowa.
                    * **Stan przepływu — tylko wersja 2** — przechwytuje stan przepływu. Możliwe stany to **B**: początek — gdy zostanie utworzony przepływ. Statystyki nie są podawane. **C**: kontynuacja — dotyczy ciągłego przepływu. Statystyki są podawane w 5-minutowych odstępach. **E**: koniec — gdy przepływ zostanie zakończony. Statystyki są podawane.
                    * **Pakiety-tylko do miejsca docelowego — wersja 2** Całkowita liczba pakietów TCP lub UDP wysłanych ze źródła do miejsca docelowego od momentu ostatniej aktualizacji.
                    * **Wysłane bajty — tylko źródło do wersji 2** Całkowita liczba bajtów pakietów TCP lub UDP wysłanych z lokalizacji źródłowej do docelowej od ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.
                    * **Pakiety — tylko wersja docelowa do wersji 2** Całkowita liczba pakietów TCP lub UDP wysłanych z lokalizacji docelowej do źródła od momentu ostatniej aktualizacji.
                    * **Bajty wysłane — miejsce docelowe do wersji 2** Całkowita liczba bajtów pakietów TCP i UDP wysłanych z lokalizacji docelowej do źródła od ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.


**Dzienniki przepływu sieciowej grupy zabezpieczeń w wersji 2 (vs wersja 1)** 

W wersji 2 dzienników wprowadzono koncepcję stanu przepływu. Można skonfigurować, która wersja odbieranych dzienników przepływu.

Stan przepływu _B_ jest rejestrowany po zainicjowaniu przepływu. Stan przepływu _C_ i stan przepływu _E_ są stanami, które oznaczają kontynuację przepływu i zakończenie przepływu. Stany _C_ i _E_ zawierają informacje o przepustowości ruchu.

### <a name="sample-log-records"></a>Przykładowe rekordy dziennika

Poniższy tekst jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które są zgodne z listą właściwości opisaną w poprzedniej sekcji.

> [!NOTE]
> Wartości we właściwości *flowTuples* są rozdzielaną przecinkami listą.
 
**Przykład formatu dziennika przepływu sieciowej grupy zabezpieczeń w wersji 1**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**Przykład formatu dziennika przepływu sieciowej grupy zabezpieczeń w wersji 2**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**Objaśniono krotkę dziennika**

![Kolekcje dzienników przepływów](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Przykładowe Obliczanie przepustowości**

Kolekcje przepływu z konwersacji TCP między 185.170.185.105:35370 i 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4,

W przypadku Stanów kontynuacja _C_ i End _E_ , liczba bajtów i pakietów to liczba zagregowana od czasu poprzedniego rekordu krotki przepływu. Odwołując się do poprzedniej przykładowej konwersacji, Łączna liczba przesłanych pakietów to 1021 + 52 + 8005 + 47 = 9125. Łączna liczba przesłanych bajtów to 588096 + 29952 + 4610880 + 27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Włączanie dzienników przepływu sieciowej grupy zabezpieczeń

Użyj poniższego linku poniżej, aby zapoznać się z przewodnikami dotyczącymi włączania dzienników przepływów.

- [Witryna Azure Portal](./network-watcher-nsg-flow-logging-portal.md)
- [Program PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [Interfejs wiersza polecenia](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Aktualizowanie parametrów

**Witryna Azure Portal**

Na Azure Portal przejdź do sekcji Dzienniki przepływu sieciowej grupy zabezpieczeń w programie Network Watcher. Następnie kliknij nazwę sieciowej grupy zabezpieczeń. Spowoduje to wyświetlenie okienka ustawień dziennika przepływu. Zmień żądane parametry i naciśnij przycisk **Zapisz** , aby wdrożyć zmiany.

**PS/CLI/REST/ARM**

Aby zaktualizować parametry za pomocą narzędzi wiersza polecenia, użyj tego samego polecenia, które służy do włączania dzienników przepływów (powyżej), ale z zaktualizowanymi parametrami, które chcesz zmienić.

## <a name="working-with-flow-logs"></a>Praca z dziennikami przepływów

*Odczyt i eksportowanie dzienników przepływów*

- [Pobieranie &amp; dzienników przepływu wyświetlania z portalu](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Odczytywanie dzienników przepływu przy użyciu funkcji programu PowerShell](./network-watcher-read-nsg-flow-logs.md)
- [Eksportuj dzienniki przepływu sieciowej grupy zabezpieczeń do Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Podczas gdy dzienniki przepływów są docelowe sieciowych grup zabezpieczeń, nie są wyświetlane w taki sam sposób, jak w przypadku innych dzienników. Dzienniki przepływów są przechowywane tylko w ramach konta magazynu i są zgodne ze ścieżką rejestrowania pokazaną w poniższym przykładzie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Wizualizacja dzienników przepływu*

- [Analiza ruchu platformy Azure](./traffic-analytics.md) to natywna usługa platformy Azure do przetwarzania dzienników przepływów, wyodrębniania szczegółowych informacji i wizualizacji dzienników przepływów. 
- [Ręczny Wizualizowanie dzienników przepływów sieciowej grupy zabezpieczeń za pomocą Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Ręczny Wizualizacja dzienników przepływu sieciowej grupy zabezpieczeń z elastycznym stosem](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Ręczny Zarządzanie dziennikami przepływu sieciowej grupy zabezpieczeń i analizowanie ich przy użyciu Grafana](./network-watcher-nsg-grafana.md)
- [Ręczny Zarządzanie dziennikami przepływu sieciowej grupy zabezpieczeń i analizowanie ich przy użyciu Z narzędzia graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)


## <a name="nsg-flow-logging-considerations"></a>Zagadnienia dotyczące rejestrowania przepływu sieciowej grupy zabezpieczeń

**Zagadnienia dotyczące konta magazynu**: 

- Lokalizacja: używane konto magazynu musi znajdować się w tym samym regionie co sieciowej grupy zabezpieczeń.
- Samozarządzanie rotacją kluczy: Jeśli zmienisz/przeniesiesz klucze dostępu do konta magazynu, dzienniki przepływu sieciowej grupy zabezpieczeń przestaną działać. Aby rozwiązać ten problem, należy wyłączyć i ponownie włączyć dzienniki przepływu sieciowej grupy zabezpieczeń.

**Koszty rejestrowania** w usłudze Flow: w przypadku rejestrowania przepływu sieciowej grupy zabezpieczeń są naliczane opłaty za ilość generowanych dzienników. Duże natężenie ruchu może skutkować dużym woluminem dziennika przepływu i powiązanymi kosztami. Cennik dziennika przepływu sieciowej grupy zabezpieczeń nie obejmuje podstawowych kosztów magazynu. Korzystanie z funkcji zasad przechowywania z rejestrowaniem przepływu sieciowej grupy zabezpieczeń oznacza, że są to różne koszty magazynowania przez dłuższy czas. Jeśli nie jest wymagana funkcja zasad przechowywania, zalecamy ustawienie wartości 0. Aby uzyskać więcej informacji, zobacz [cennik Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/) i [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) , aby uzyskać dodatkowe informacje.

**Problemy ze zdefiniowanymi przez użytkownika regułami protokołu TCP dla ruchu przychodzącego**: [sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/network-security-groups-overview.md) są implementowane jako [zapory stanowe](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Jednak ze względu na bieżące ograniczenia platformy reguły zdefiniowane przez użytkownika, które mają wpływ na przychodzące przepływy TCP, są implementowane w sposób bezstanowy. Z tego powodu przepływy, na które wpływają reguły ruchu przychodzącego zdefiniowane przez użytkownika, stają się niekończące. Ponadto liczby bajtów i pakietów nie są rejestrowane dla tych przepływów. W związku z tym liczba bajtów i pakietów raportowanych w dziennikach przepływu sieciowej grupy zabezpieczeń (i Analiza ruchu) może różnić się od liczby rzeczywistej. Flaga wyboru, która rozwiązuje te problemy, jest zaplanowana do udostępnienia w grudniu 2020 najnowszych. W przypadku klientów z poważnymi problemami związanymi z tym zachowaniem może zażądać rezygnacji z pomocy technicznej, Zgłoś żądanie pomocy technicznej w obszarze Network Watcher > dzienniki przepływów sieciowej grupy zabezpieczeń.  

**Przepływy przychodzące zarejestrowane z Internetu adresów IP na maszynach wirtualnych bez publicznych** adresach IP: maszyny wirtualne, które nie mają publicznego adresu do sieci, są przypisane za pośrednictwem publicznego adresu IP SKOJARZONEGO z kartą sieciową jako publiczny adres IPv4 na poziomie wystąpienia lub które są częścią puli zaplecza usługi równoważenia obciążenia, użyj [domyślnego](../load-balancer/load-balancer-outbound-connections.md) , a także adresu IP przypisanego przez platformę Azure. W związku z tym mogą pojawić się wpisy dziennika przepływu dla przepływów z internetowych adresów IP, jeśli przepływ jest przeznaczony do portu w zakresie portów przypisanych do tego elementu. Mimo że platforma Azure nie zezwala na te przepływy na maszynę wirtualną, próba zostanie zarejestrowana i zostanie wyświetlona Network Watcher w dzienniku przepływu sieciowej grupy zabezpieczeń przez zaprojektowanie. Zalecamy, aby niepożądane przychodzące ruch internetowy został jawnie zablokowany przy użyciu sieciowej grupy zabezpieczeń.

**Niezgodne usługi**: ze względu na bieżące ograniczenia platformy, mały zestaw usług platformy Azure nie jest obsługiwany przez dzienniki przepływów sieciowej grupy zabezpieczeń. Bieżąca lista niezgodnych usług jest
- [Usługi Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Najlepsze rozwiązania

**Włącz w przypadku krytycznych sieci wirtualnych/podsieci**: dzienniki przepływów powinny być włączone na wszystkich krytycznych sieci wirtualnych/podsieciach w ramach subskrypcji jako najlepsze rozwiązanie w zakresie inspekcji i zabezpieczeń. 

**Włącz rejestrowanie przepływu sieciowej grupy zabezpieczeń na wszystkich sieciowych grup zabezpieczeńach dołączonych do zasobu**: rejestrowanie przepływu na platformie Azure jest konfigurowane na zasobie sieciowej grupy zabezpieczeń. Przepływ zostanie skojarzony tylko z jedną regułą sieciowej grupy zabezpieczeń. W scenariuszach, w których jest używany wiele sieciowych grup zabezpieczeń, zalecamy włączenie dzienników przepływów sieciowej grupy zabezpieczeń na wszystkich sieciowych grup zabezpieczeń zastosowały podsieć lub interfejs sieciowy zasobu, aby upewnić się, że cały ruch jest zarejestrowany. Aby uzyskać więcej informacji, zobacz [jak oceniany jest ruch](../virtual-network/network-security-group-how-it-works.md) w sieciowych grupach zabezpieczeń.

**Inicjowanie obsługi administracyjnej magazynu**: zainicjowanie obsługi magazynu należy zamieścić z oczekiwanym woluminem dziennika przepływów.

## <a name="troubleshooting-common-issues"></a>Rozwiązywanie typowych problemów

**Nie mogę włączyć dzienników przepływu sieciowych grup zabezpieczeń**

- Dostawca zasobów **Microsoft. Insights** nie jest zarejestrowany

Jeśli wystąpił błąd _AuthorizationFailed_ lub _GatewayAuthenticationFailed_, być może nie włączono dostawcy zasobów Microsoft Insights w subskrypcji. [Postępuj zgodnie z instrukcjami](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) , aby włączyć dostawcę usługi Microsoft Insights.

**Dzienniki przepływu sieciowych grup zabezpieczeń zostały włączone, ale nie widzę danych na moim koncie magazynu**

- **Godzina konfiguracji**

Dzienniki przepływu sieciowej grupy zabezpieczeń mogą zostać wyświetlone na koncie magazynu po 5 minutach (jeśli zostały poprawnie skonfigurowane). Zostanie wyświetlony plik PT1H.JSON, do którego można uzyskać dostęp [zgodnie z tymi instrukcjami](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Brak ruchu w sieciowych grupach zabezpieczeń**

Czasami dzienniki nie są wyświetlane, ponieważ maszyny wirtualne nie są aktywne lub istnieją filtry nadrzędne w bramie aplikacji lub innych urządzeniach, które blokują ruch do sieciowych grup zabezpieczeń.

**Chcę zautomatyzować dzienniki przepływu sieciowej grupy zabezpieczeń**

Obsługa automatyzacji za pomocą szablonów ARM nie jest obecnie dostępna dla dzienników przepływów sieciowych grup zabezpieczeń. Przeczytaj [anons funkcji](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) , aby uzyskać więcej informacji.

## <a name="faq"></a>Często zadawane pytania

**Co robią dzienniki przepływu sieciowej grupy zabezpieczeń?**

Zasoby sieciowe platformy Azure można łączyć i zarządzać nimi za pomocą [sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/network-security-groups-overview.md). Dzienniki przepływu sieciowej grupy zabezpieczeń umożliwiają rejestrowanie 5-informacje o przepływie krotki na cały ruch przez sieciowych grup zabezpieczeń. Dzienniki nieprzetworzonych przepływów są zapisywane na koncie usługi Azure Storage, z poziomu którego mogą być przetwarzane, analizowane, badane lub eksportowane zgodnie z wymaganiami.

**Czy korzystanie z dzienników przepływów ma wpływ na opóźnienie sieci lub wydajność?**

Dane dzienników przepływów są zbierane poza ścieżką ruchu sieciowego i w związku z tym nie wpływają na przepływność lub opóźnienia sieci. Można tworzyć i usuwać dzienniki przepływów bez ryzyka związanego z wydajnością sieci.

**Jak mogę użyć dzienników przepływu sieciowej grupy zabezpieczeń z kontem magazynu za zaporą?**

Aby użyć konta magazynu za zaporą, musisz podać wyjątek dla zaufanych usług firmy Microsoft, aby uzyskać dostęp do konta magazynu:

- Przejdź do konta magazynu, wpisując nazwę konta magazynu w wyszukiwaniu globalnym w portalu lub na [stronie konta magazynu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) .
- W sekcji **Ustawienia** wybierz pozycję **zapory i sieci wirtualne** .
- W obszarze **Zezwalaj na dostęp z** wybierz pozycję  **wybrane sieci**. Następnie w obszarze  **wyjątki** zaznacz pole wyboru obok pozycji * * * * Zezwól zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu * * * *
- Jeśli jest ona już zaznaczona, nie trzeba wprowadzać żadnych zmian.
- Znajdź swój docelowy sieciowej grupy zabezpieczeń na [stronie Przegląd dzienników przepływów sieciowej grupy zabezpieczeń](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) i Włącz dzienniki przepływu sieciowej grupy zabezpieczeń z wybranym powyższym kontem magazynu.

Możesz sprawdzić dzienniki magazynu po kilku minutach — powinna być widoczna zaktualizowana sygnatura czasowa lub utworzony nowy plik JSON.

**Jak mogę użyć dzienników przepływu sieciowej grupy zabezpieczeń z kontem magazynu za punktem końcowym usługi?**

Dzienniki przepływu sieciowej grupy zabezpieczeń są zgodne z punktami końcowymi usługi, bez konieczności dodatkowej konfiguracji. Zapoznaj się z [samouczkiem dotyczącym włączania punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) w sieci wirtualnej.

**Jaka jest różnica między dziennikami przepływów w wersjach 1 & 2?**

Dzienniki przepływów w wersji 2 wprowadzają koncepcję _stanu przepływu_ & przechowuje informacje o transmitowanych bajtach i pakietach. [Dowiedz się więcej](#log-format)

## <a name="pricing"></a>Cennik

Dzienniki przepływu sieciowej grupy zabezpieczeń są naliczone za GB dzienników zebranych i mające bezpłatną warstwę 5 GB/miesiąc na subskrypcję. Aktualne ceny w regionie znajdują się na [stronie cennika Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/).

Opłaty za przechowywanie dzienników są naliczane osobno. zobacz [stronę cennika usługi Azure Storage Block BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) dla odpowiednich cen.
