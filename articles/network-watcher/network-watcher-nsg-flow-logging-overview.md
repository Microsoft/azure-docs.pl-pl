---
title: Wprowadzenie do rejestrowania przepływu dla sieciowych sieciowych sieciowych sieci
titleSuffix: Azure Network Watcher
description: W tym artykule wyjaśniono, jak używać funkcji dzienników przepływu sieciowej Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f737be68a28f95ab5402ba5ea08e85fcf1b04d37
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565903"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń

## <a name="introduction"></a>Wprowadzenie

[Dzienniki przepływu sieciowej](../virtual-network/network-security-groups-overview.md#security-rules) grupy zabezpieczeń to funkcja usługi Azure Network Watcher, która umożliwia rejestrowanie informacji o ruchu IP przepływacym przez sieciową grupę zabezpieczeń. Dane przepływu są wysyłane na konta usługi Azure Storage, z których można uzyskać do nich dostęp, a także eksportować je do dowolnego narzędzia do wizualizacji, rozwiązania SIEM lub identyfikatorów.

![omówienie dzienników przepływu](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Dlaczego warto używać dzienników przepływu?

Ważne jest, aby monitorować i znać własną sieć oraz zarządzać nimi w celu zapewnienia bezkompromisowych zabezpieczeń, zgodności i wydajności. Znajomość własnego środowiska ma ogromne znaczenie dla ochrony i optymalizacji. Często musisz znać bieżący stan sieci, to kto się łączy, skąd się łączy, z których portów jest otwarty dla Internetu, oczekiwane zachowanie sieci, nieregularne zachowanie sieci i nagły wzrost ruchu.

Dzienniki przepływu są źródłem prawdy dla wszystkich działań sieciowych w środowisku chmury. Niezależnie od tego, czy zbliżasz się do startupu, który próbuje zoptymalizować zasoby, czy też próbujesz wykryć włamanie w dużym przedsiębiorstwie, najlepszym rozwiązaniem są dzienniki usługi Flow. Można jej używać do optymalizowania przepływów sieci, monitorowania przepływności, weryfikowania zgodności, wykrywania włamań i nie tylko.

## <a name="common-use-cases"></a>Typowe przypadki użycia

**Monitorowanie sieci:** zidentyfikuj nieznany lub niepożądany ruch. Monitoruj poziomy ruchu i użycie przepustowości. Filtrowanie dzienników przepływu według adresu IP i portu w celu zrozumienia zachowania aplikacji. Eksportuj dzienniki przepływu do narzędzi do analizy i wizualizacji, aby skonfigurować pulpity nawigacyjne monitorowania.

**Monitorowanie i optymalizacja użycia:** Zidentyfikuj najważniejsze osoby w sieci. Połącz z danymi GeoIP, aby identyfikować ruch między regionami. Zrozumienie wzrostu ruchu w celu prognozowania pojemności. Użyj danych, aby usunąć zbyt restrykcyjne reguły ruchu.

**Zgodność:** Użyj danych przepływu, aby zweryfikować izolację sieci i zgodność z regułami dostępu przedsiębiorstwa

**Analiza śledczych & zabezpieczeń:** analizowanie przepływów sieciowych na podstawie naruszonych ip i interfejsów sieciowych. Wyeksportuj dzienniki przepływu do dowolnego wybranego narzędzia SIEM lub IDS.

## <a name="how-logging-works"></a>Jak działa rejestrowanie

**Właściwości klucza**

- Dzienniki przepływu działają w [warstwie 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) i rejestrują wszystkie przepływy adresów IP przechodzące do i z sieciowej sieciowej organizacji zabezpieczeń
- Dzienniki są zbierane za pośrednictwem platformy Azure **co 1 minuty** i w żaden sposób nie wpływają na zasoby klienta ani wydajność sieci.
- Dzienniki są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące na podstawie reguł sieciowej sieciowej organizacji zabezpieczeń.
- Każdy rekord dziennika zawiera interfejs sieciowy (KARTę sieciową), do których ma zastosowanie przepływ, informacje o krotce z 5-krotnością, informacje o przepływności & (tylko wersja 2). Aby _uzyskać szczegółowe informacje,_ zobacz Format dziennika poniżej.
- Dzienniki przepływu mają funkcję przechowywania, która umożliwia automatyczne usuwanie dzienników do roku po ich utworzeniu. 

> [!NOTE]
> Przechowywanie jest dostępne tylko w przypadku kont [magazynu ogólnego przeznaczenia w wersji 2 (GPv2).](../storage/common/storage-account-overview.md#types-of-storage-accounts) 

**Podstawowe pojęcia**

- Sieci zdefiniowane programowo są zorganizowane wokół sieci wirtualnych (VNET) i podsieci. Zabezpieczeniami tych sieci wirtualnych i podsieci można zarządzać przy użyciu sieciowej organizacji zabezpieczeń.
- Sieciowa grupa zabezpieczeń zawiera listę reguł  zabezpieczeń, które zezwalają na ruch sieciowy lub go odrzucają w zasobach, z które jest połączona. Sieciowe sieciowe sieciowe skojarzenia można skojarzyć z podsieciami, poszczególnymi maszynami wirtualnym lub poszczególnymi interfejsami sieciowym (NIC) dołączonymi do maszyn wirtualnych (Resource Manager). Aby uzyskać więcej informacji, zobacz [Omówienie sieciowych grup zabezpieczeń.](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- Wszystkie przepływy ruchu w sieci są oceniane przy użyciu reguł w odpowiednich sieciowych sieciowych sieciowych sieciach.
- Wynikiem tych ocen są dzienniki przepływu sieciowej organizacji zabezpieczeń. Dzienniki przepływu są zbierane za pośrednictwem platformy Azure i nie wymagają żadnych zmian w zasobach klienta.
- Uwaga: Reguły mają dwa typy — przerywanie &, z których każda ma różne zachowania rejestrowania.
- - Reguły odmowy sieciowej organizacji sieciowej są przerywane. Sieciowa żadna z tych sieci nie zezwala na ruch, będzie rejestrować go w dziennikach usługi Flow, a przetwarzanie w tym przypadku zostanie zatrzymane po odmowie ruchu przez dowolną sieciną mowę. 
- - Reguły zezwalania sieciowej sieci nie przerywają, co oznacza, że nawet jeśli zezwala na to jedna sieciowa sieciowa żadna z tych sieci, przetwarzanie będzie kontynuowane do następnej sieciowej sieci. Ostatnia sieciowa sieciowa żadna z sieci zezwala na ruch będzie rejestrować ruch w dziennikach usługi Flow.
- Dzienniki przepływu sieciowych organizacji zabezpieczeń są zapisywane na kontach magazynu, z których można uzyskać do nich dostęp.
- Dzienniki przepływu można eksportować, przetwarzać, analizować i wizualizować przy użyciu narzędzi, takich jak TA, Splunk, Grafana, Stealthwatch itp.

## <a name="log-format"></a>Format dziennika

Dzienniki przepływu obejmują następujące właściwości:

* **time** — czas, o którym zdarzenie zostało zarejestrowane
* **systemId** — identyfikator systemu sieciowej grupy zabezpieczeń.
* **category** — kategoria zdarzenia. Kategoria to zawsze **NetworkSecurityGroupFlowEvent**
* **resourceid** — identyfikator zasobu sieciowej grupy zasobów
* **operationName** — zawsze NetworkSecurityGroupFlowEvents
* **properties** — kolekcja właściwości przepływu
    * **Wersja** — numer wersji schematu zdarzeń dziennika przepływu
    * **flows** — kolekcja przepływów. Ta właściwość ma wiele wpisów dla różnych reguł
        * **reguła** — reguła, dla której są wyświetlane przepływy
            * **flows** — kolekcja przepływów
                * **mac** — adres MAC karty sieciowej maszyny wirtualnej, na której został zebrany przepływ
                * **flowTuples** — ciąg zawierający wiele właściwości krotki przepływu w formacie rozdzielanym przecinkami
                    * **Sygnatura** czasowa — ta wartość jest sygnaturą czasową, kiedy przepływ wystąpił w formacie epoki systemu UNIX
                    * **Źródłowy adres IP** — źródłowy adres IP
                    * **Docelowy adres IP** — docelowy adres IP
                    * **Port źródłowy** — port źródłowy
                    * **Port docelowy** — port docelowy
                    * **Protokół** — protokół przepływu. Prawidłowe wartości to **T dla** TCP i **U dla** UDP
                    * **Przepływ ruchu** — kierunek przepływu ruchu. Prawidłowe wartości to **I** dla ruchu przychodzącego i **O** dla ruchu wychodzącego.
                    * **Decyzja o ruchu** — określa, czy ruch był dozwolony, czy odrzucony. Prawidłowe wartości to **A** dla dozwolonych i **D** w przypadku odmowy.
                    * **Stan przepływu — tylko wersja 2** — przechwytuje stan przepływu. Możliwe stany to **B**: początek — gdy zostanie utworzony przepływ. Statystyki nie są podawane. **C**: kontynuacja — dotyczy ciągłego przepływu. Statystyki są podawane w 5-minutowych odstępach. **E**: koniec — gdy przepływ zostanie zakończony. Statystyki są podawane.
                    * **Pakiety — ze źródła do miejsca docelowego — tylko wersja 2** Całkowita liczba pakietów TCP wysłanych ze źródła do miejsca docelowego od ostatniej aktualizacji.
                    * **Wysłane bajty — ze źródła do miejsca docelowego — tylko wersja 2** Całkowita liczba bajtów pakietów TCP wysłanych ze źródła do miejsca docelowego od ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.
                    * **Pakiety — od miejsca docelowego do źródła — tylko wersja 2** Całkowita liczba pakietów TCP wysłanych z miejsca docelowego do źródła od czasu ostatniej aktualizacji.
                    * **Wysłane bajty — miejsce docelowe do źródła — tylko wersja 2** Całkowita liczba bajtów pakietów TCP wysłanych z miejsca docelowego do źródła od czasu ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.


**Dzienniki przepływu sieciowej organizacji zabezpieczeń w wersji 2 (a w wersji 1)** 

Wersja 2 dzienników wprowadza koncepcję stanu przepływu. Możesz skonfigurować wersję obierania dzienników przepływu.

Stan _przepływu B_ jest rejestrowany po zainicjowaniu przepływu. Stan _przepływu C_ i _stan przepływu E_ to stany, które odpowiednio oznaczają kontynuację przepływu i zakończenia przepływu. Zarówno _stany C,_ _jak i E_ zawierają informacje o przepustowości ruchu.

### <a name="sample-log-records"></a>Przykładowe rekordy dziennika

Tekst poniżej jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które są zgodne z listą właściwości opisaną w poprzedniej sekcji.

> [!NOTE]
> Wartości we właściwości *flowTuples* są listą rozdzieloną przecinkami.
 
**Przykładowy format dziennika przepływu sieciowej sieciowej organizacji zabezpieczeń w wersji 1**
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
**Przykładowy format dziennika przepływu sieciowej sieciowej organizacji zabezpieczeń w wersji 2**
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
**Krotka dziennika objaśniona**

![krotka dzienników przepływu](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Przykładowe obliczenie przepustowości**

Krotki przepływu z konwersacji TCP między 185.170.185.105:35370 i 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

W przypadku _stanów przepływu_ kontynuacji C i _E_ liczba bajtów i pakietów to zagregowane liczby z czasu poprzedniego rekordu krotki przepływu. Odwołując się do poprzedniej przykładowej konwersacji, łączna liczba przeniesionych pakietów to 1021+52+8005+47 = 9125. Łączna liczba przeniesionych bajtów to 588096+29952+4610880+27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Włączanie dzienników przepływu sieciowej grupy zabezpieczeń

Skorzystaj z poniższego linku, aby uzyskać przewodniki dotyczące włączania dzienników przepływu.

- [Witryna Azure Portal](./network-watcher-nsg-flow-logging-portal.md)
- [Program PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [Interfejs wiersza polecenia](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Aktualizowanie parametrów

**Witryna Azure Portal**

Na stronie Azure Portal przejdź do sekcji Dzienniki przepływu sieciowych Network Watcher. Następnie kliknij nazwę sieciowej grupy danych. Spowoduje to okno ustawień dziennika usługi Flow. Zmień wymagane parametry i naciśnij przycisk **Zapisz,** aby wdrożyć zmiany.

**PS/CLI/REST/ARM**

Aby zaktualizować parametry za pomocą narzędzi wiersza polecenia, użyj tego samego polecenia, które jest używane do włączania dzienników przepływu (powyżej), ale ze zaktualizowanymi parametrami, które chcesz zmienić.

## <a name="working-with-flow-logs"></a>Praca z dziennikami przepływu

*Odczytywanie i eksportowanie dzienników przepływu*

- [Pobieranie &amp; dzienników przepływu widoku z portalu](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Odczytywanie dzienników przepływu przy użyciu funkcji programu PowerShell](./network-watcher-read-nsg-flow-logs.md)
- [Eksportowanie dzienników przepływu sieciowej organizacji zabezpieczeń do splunku](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Podczas gdy dzienniki przepływu są kierowane do sieciowych grup zabezpieczeń, nie są one wyświetlane tak samo jak inne dzienniki. Dzienniki przepływu są przechowywane tylko w ramach konta magazynu i postępuj zgodnie ze ścieżką rejestrowania pokazaną w poniższym przykładzie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Wizualizowanie dzienników przepływu*

- [Analiza ruchu na platformie Azure](./traffic-analytics.md) to natywna usługa platformy Azure do przetwarzania dzienników przepływów, wyodrębniania szczegółowych informacji i wizualizowania dzienników przepływów. 
- [[Samouczek] Wizualizowanie dzienników przepływu sieciowych Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [[Samouczek] Wizualizowanie dzienników przepływu sieciowej organizacji zabezpieczeń za pomocą usługi Elastic Stack](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [[Samouczek] Zarządzanie dziennikami przepływu sieciowych zasad zabezpieczeń i ich analizowanie przy użyciu narzędzia Grafana](./network-watcher-nsg-grafana.md)
- [[Samouczek] Zarządzanie dziennikami przepływu sieciowych zasad zabezpieczeń i ich analizowanie przy użyciu oprogramowania Graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)

*Wyłączanie dzienników przepływu*

Po wyłączeniu dziennika przepływu rejestrowanie przepływu dla skojarzonej sieciowej sieci jest zatrzymywane. Jednak dziennik przepływu jako zasób nadal istnieje ze wszystkimi jego ustawieniami i skojarzeniami. Można ją włączyć w dowolnym momencie, aby rozpocząć rejestrowanie przepływu dla skonfigurowanej sieciowej sieci. Kroki umożliwiające wyłączenie/włączenie dzienników przepływu można znaleźć w [tym przewodniku.](./network-watcher-nsg-flow-logging-powershell.md)  

*Usuwanie dzienników przepływu*

Po usunięciu dziennika przepływu nie tylko rejestrowanie przepływu dla skojarzonej sieciowej grupy zabezpieczeń zostanie zatrzymane, ale także zasób dziennika przepływu zostanie usunięty wraz z jego ustawieniami i skojarzeniami. Aby ponownie rozpocząć rejestrowanie przepływu, należy utworzyć nowy zasób dziennika przepływu dla tej sieciowej grupy zabezpieczeń. Dziennik przepływu można usunąć przy użyciu programu [PowerShell,](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkwatcherflowlog)interfejsu [wiersza polecenia](https://docs.microsoft.com/cli/azure/network/watcher/flow-log#az_network_watcher_flow_log_delete) lub [interfejsu API REST.](https://docs.microsoft.com/rest/api/network-watcher/flowlogs/delete) Obsługa usuwania dzienników przepływu z Azure Portal jest w potoku.    

Ponadto po usunięciu sieciowej grupy zabezpieczeń domyślnie jest usuwany skojarzony zasób dziennika przepływu.

> [!NOTE]
> Aby przenieść sieciową grupę zabezpieczeń do innej grupy zasobów lub subskrypcji, należy usunąć skojarzone dzienniki przepływu. Wystarczy, że wyłączenie dzienników przepływu nie będzie działać. Po migracji sieciowej grupy zabezpieczeń należy ponownie utworzyć dzienniki przepływu, aby włączyć w nich rejestrowanie przepływów.  

## <a name="nsg-flow-logging-considerations"></a>Zagadnienia dotyczące rejestrowania przepływu sieciowej organizacji sieciowej

**Zagadnienia dotyczące konta magazynu:** 

- Lokalizacja: używane konto magazynu musi znajdować się w tym samym regionie, w którym znajduje się sieciowa żadna z tych sieciowych sieci.
- Warstwa wydajności: obecnie obsługiwane są tylko konta magazynu w warstwie Standardowa.
- Samodzielne zarządzanie rotacją kluczy: w przypadku zmiany/rotacji kluczy dostępu na koncie magazynu dzienniki przepływu sieciowej grupy zabezpieczeń przestaną działać. Aby rozwiązać ten problem, należy wyłączyć, a następnie ponownie włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń.

**Koszty rejestrowania przepływu:** rejestrowanie przepływu sieciowej organizacji zabezpieczeń jest rozliczane na podstawie liczby wytwarzanych dzienników. Duże natężenie ruchu może spowodować duże liczby dzienników przepływów i powiązane koszty. Ceny dzienników przepływu sieciowych organizacji zabezpieczeń nie obejmują podstawowych kosztów magazynu. Używanie funkcji zasad przechowywania z rejestrowaniem przepływu sieciowej grupy zabezpieczeń oznacza naliczanie oddzielnych kosztów magazynowania przez dłuższy czas. Jeśli chcesz zachować dane na zawsze i nie chcesz stosować żadnych zasad przechowywania, ustaw okres przechowywania (dni) na wartość 0. Aby uzyskać więcej informacji, [zobacz Network Watcher Pricing](https://azure.microsoft.com/pricing/details/network-watcher/) (Cennik usługi Azure Storage) i Azure Storage Pricing (Cennik usługi Azure [Storage).](https://azure.microsoft.com/pricing/details/storage/)

Problemy ze zdefiniowanymi przez użytkownika regułami **ruchu przychodzącego protokołu TCP:** [sieciowe](../virtual-network/network-security-groups-overview.md) grupy zabezpieczeń są implementowane jako [stanowa zapora.](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true) Jednak ze względu na bieżące ograniczenia platformy reguły zdefiniowane przez użytkownika, które mają wpływ na przychodzące przepływy TCP, są implementowane w sposób bez stanowy. W związku z tym przepływy, na które wpływają reguły ruchu przychodzącego zdefiniowane przez użytkownika, stają się niepowiązywające się z zakończeniem. Ponadto liczba bajtów i pakietów nie jest rejestrowana dla tych przepływów. W związku z tym liczba bajtów i pakietów zgłoszonych w dziennikach przepływu sieciowych Analiza ruchu) może różnić się od liczby rzeczywistej. Flaga zgody, która rozwiązuje te problemy, ma być dostępna do najnowszej wersji z marca 2021 r. W międzyczasie klienci, u których występuje poważny problem z powodu tego zachowania, mogą poprosić o zgodę za pośrednictwem pomocy technicznej. Zgłoś wniosek o pomoc techniczną w obszarze Network Watcher > przepływu sieciowej Network Watcher > sieciowej organizacji zabezpieczeń.  

Przepływy przychodzące rejestrowane z internetowych adresów IP do maszyn wirtualnych bez publicznych adresów **IP:** maszyny wirtualne, które nie mają publicznego adresu IP przypisanego za pośrednictwem publicznego adresu IP skojarzonego z kartą sieciową jako publiczny adres IP na poziomie wystąpienia lub które są częścią puli podstawowej usługi równoważenia obciążenia, używają domyślnej funkcji [SNAT](../load-balancer/load-balancer-outbound-connections.md) i mają adres IP przypisany przez platformę Azure w celu ułatwienia łączności wychodzącej. W związku z tym wpisy dziennika przepływu mogą być dla przepływów z internetowych adresów IP, jeśli przepływ jest przeznaczony do portu w zakresie portów przypisanych do SNAT. Mimo że platforma Azure nie zezwoli na te przepływy do maszyny wirtualnej, ta próba jest rejestrowana i domyślnie pojawia się Network Watcher w dzienniku przepływu sieciowej Network Watcher sieciowej organizacji. Zalecamy jawne blokowanie niepożądanego przychodzącego ruchu internetowego za pomocą sieciowej sieciowej sieciowej sieci.

**Problem z sieciową Application Gateway** sieciową domyślną podsiecią w wersji 2: Rejestrowanie przepływu w sieciowej podsieci bramy aplikacji w wersji 2 [nie jest obecnie](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) obsługiwane. Ten problem nie ma wpływu na Application Gateway wersji 1.

**Niezgodne usługi:** ze względu na bieżące ograniczenia platformy mały zestaw usług platformy Azure nie jest obsługiwany przez dzienniki przepływu sieciowej organizacji zabezpieczeń. Bieżąca lista niezgodnych usług to
- [Usługi Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Najlepsze rozwiązania

**Włącz w krytycznych** sieciach wirtualnych/podsieciach: dzienniki przepływu powinny być włączone na wszystkich krytycznych sieciach wirtualnych/podsieciach w ramach subskrypcji jako najlepsze rozwiązanie w zakresie inspekcji i zabezpieczeń. 

**Włącz rejestrowanie przepływu sieciowej** grupy zarządzania dla wszystkich sieciowych grup sieciowych dołączonych do zasobu: Rejestrowanie przepływu na platformie Azure jest skonfigurowane dla zasobu sieciowej grupy zasobów. Przepływ zostanie skojarzony tylko z jedną regułą sieciowej sieciowej sieciowej sieciowej sieci. W scenariuszach, w których jest używanych wiele sieciowych grup zabezpieczeń, zalecamy włączenie dzienników przepływów sieciowej grupy zabezpieczeń we wszystkich sieciowych grupach zabezpieczeń zastosowanych w podsieci lub interfejsie sieciowym zasobu, aby upewnić się, że cały ruch jest rejestrowany. Aby uzyskać więcej informacji, [zobacz, jak jest oceniany ruch w](../virtual-network/network-security-group-how-it-works.md) sieciowych grupach zabezpieczeń. 

Kilka typowych scenariuszy:
1. **Wiele karty sieciowe na maszynie wirtualnej:** jeśli wiele karty sieciowe są dołączone do maszyny wirtualnej, rejestrowanie przepływu musi być włączone na wszystkich z nich
1. **Sieciowa** grupa danych ma zarówno poziom karty sieciowej, jak i podsieci: w przypadku skonfigurowania sieciowej oraz na poziomie podsieci należy włączyć rejestrowanie przepływu w obu sieciowych sieciach. 

**Aprowizowanie magazynu:** magazyn należy aprowizować zgodnie z oczekiwanym woluminem dziennika przepływu.

**Nazewnictwo:** nazwa sieciowej grupy danych musi zawierać maksymalnie 80 znaków, a nazwy reguł sieciowej grupy danych mogą zawierać maksymalnie 65 znaków. Jeśli nazwy przekraczają limit znaków, mogą zostać obcięte podczas rejestrowania.

## <a name="troubleshooting-common-issues"></a>Rozwiązywanie typowych problemów

**Nie mogę włączyć dzienników przepływu sieciowych grup zabezpieczeń**

- **Dostawca zasobów Microsoft.Insights** nie jest zarejestrowany

Jeśli wystąpił błąd _AuthorizationFailed_ lub _GatewayAuthenticationFailed_, być może nie włączono dostawcy zasobów Microsoft Insights w subskrypcji. [Postępuj zgodnie z instrukcjami,](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) aby włączyć dostawcę usługi Microsoft Insights.

**Dzienniki przepływu sieciowych grup zabezpieczeń zostały włączone, ale nie widzę danych na moim koncie magazynu**

- **Godzina konfiguracji**

Dzienniki przepływu sieciowej grupy zabezpieczeń mogą zostać wyświetlone na koncie magazynu po 5 minutach (jeśli zostały poprawnie skonfigurowane). Zostanie wyświetlony plik PT1H.JSON, do którego można uzyskać dostęp [zgodnie z tymi instrukcjami](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Brak ruchu w sieciowych grupach zabezpieczeń**

Czasami dzienniki nie są wyświetlane, ponieważ maszyny wirtualne nie są aktywne lub istnieją filtry nadrzędne w bramie aplikacji lub innych urządzeniach, które blokują ruch do sieciowych grup zabezpieczeń.

**Chcę zautomatyzować dzienniki przepływu sieciowej grupy zabezpieczeń**

Obsługa automatyzacji za pomocą szablonów ARM nie jest obecnie dostępna dla dzienników przepływów sieciowych grup zabezpieczeń. Przeczytaj [zapowiedź funkcji](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/), aby uzyskać więcej informacji.

## <a name="faq"></a>Często zadawane pytania

**Co robią dzienniki przepływu sieciowej organizacji zabezpieczeń?**

Zasoby sieciowe platformy Azure można łączyć i zarządzać nimi za pośrednictwem sieciowych grup [zabezpieczeń](../virtual-network/network-security-groups-overview.md). Dzienniki przepływu sieciowych organizacji zabezpieczeń umożliwiają rejestrowanie informacji o przepływie krotki z 5-krotkami dotyczących całego ruchu w sieciowych sieciach zabezpieczeń. Nieprzetworzone dzienniki przepływu są zapisywane na koncie usługi Azure Storage, z którego można je dalej przetwarzać, analizować, wyszukiwać lub eksportować zgodnie z potrzebami.

**Czy korzystanie z dzienników przepływu ma wpływ na moje opóźnienie sieci lub wydajność?**

Dane dzienników przepływu są zbierane poza ścieżką ruchu sieciowego i dlatego nie wpływają na przepływność ani opóźnienie sieci. Dzienniki przepływów można tworzyć i usuwać bez ryzyka wpływu na wydajność sieci.

**Jak mogę używać dzienników przepływu sieciowej organizacji zabezpieczeń z kontem magazynu za zaporą?**

Aby użyć konta usługi Storage za zaporą, musisz podać wyjątek dla zaufanych usług firmy Microsoft, aby uzyskać dostęp do konta magazynu:

- Przejdź do konta magazynu, wpisując nazwę konta magazynu w witrynie wyszukiwanie globalne w portalu lub na [stronie Konta magazynu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- W sekcji  **USTAWIENIA**  wybierz pozycję  **Zapory i sieci wirtualne**
- W **jęz.** Zezwalaj na dostęp z programu **wybierz pozycję Wybrane sieci.** Następnie w  **obszarze Wyjątki** zaznacz pole wyboru obok **Zezwalaj zaufanym usługi firmy Microsoft na dostęp do tego konta magazynu**
- Jeśli jest ona już zaznaczona, nie trzeba wprowadzać żadnych zmian.
- Znajdź docelową sieciową grupy zabezpieczeń na [stronie](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) przeglądu dzienników przepływu sieciowych grup zabezpieczeń i włącz dzienniki przepływu sieciowej grupy zabezpieczeń z wybranym powyższym kontem magazynu.

Możesz sprawdzić dzienniki magazynu po kilku minutach — powinna być widoczna zaktualizowana sygnatura czasowa lub utworzony nowy plik JSON.

**Jak mogę używać dzienników przepływu sieciowej organizacji zabezpieczeń z kontem magazynu za punktem końcowym usługi?**

Dzienniki przepływu sieciowych organizacji zabezpieczeń są zgodne z punktami końcowymi usługi bez konieczności dodatkowej konfiguracji. Zobacz [samouczek dotyczący włączania punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) w sieci wirtualnej.

**Jaka jest różnica między dziennikami przepływu w wersji 1 & 2?**

Dzienniki przepływu w wersji 2 wprowadzają koncepcję stanu przepływu, & przechowuje informacje o bajtach i przesyłanych pakietach.  [Dowiedz się więcej](#log-format)

## <a name="pricing"></a>Ceny

Opłaty za dzienniki przepływu sieciowych organizacji zabezpieczeń są naliczane za GB zebranych dzienników i są dostępne w warstwie Bezpłatna 5 GB/miesiąc na subskrypcję. Aby uzyskać informacje o bieżących cenach w Twoim regionie, zobacz [Network Watcher cennika](https://azure.microsoft.com/pricing/details/network-watcher/).

Opłaty za magazyn dzienników są naliczane osobno. Odpowiednie ceny można znaleźć na stronie z cennikiem blokowych obiektów blob usługi [Azure Storage.](https://azure.microsoft.com/pricing/details/storage/blobs/)