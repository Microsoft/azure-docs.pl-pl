---
title: Schemat analizy ruchu na platformie Azure | Microsoft Docs
description: Informacje o schemacie Analiza ruchu do analizowania dzienników przepływu sieciowych grup zabezpieczeń platformy Azure.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 765a2728c5de49787a64fff723625e53e100e450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017786"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Agregacja schematu i danych w Analiza ruchu

Analiza ruchu to rozwiązanie oparte na chmurze, które zapewnia wgląd w aktywność użytkowników i aplikacji w sieciach w chmurze. Analiza ruchu analizuje dzienniki przepływu sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) Network Watcher, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Analiza ruchu umożliwia:

- Wizualizuj aktywność sieci w ramach subskrypcji platformy Azure i zidentyfikuj punkty aktywne.
- Zidentyfikuj zagrożenia bezpieczeństwa, a także Zabezpiecz sieć, korzystając z informacji takich jak Open-Ports, Applications próbujących uzyskać dostęp do Internetu oraz maszyn wirtualnych łączących się z nieautoryzowanymi sieciami.
- Zrozumienie wzorców przepływu ruchu w regionach platformy Azure oraz w Internecie w celu zoptymalizowania wdrożenia sieci pod kątem wydajności i pojemności.
- Lokalizowanie błędnych konfiguracji sieciowych prowadzących do nieudanych połączeń w sieci.
- Poznaj użycie sieci w bajtach, pakietach lub przepływach.

### <a name="data-aggregation"></a>Agregacja danych

1. Wszystkie dzienniki przepływów w sieciowej grupy zabezpieczeń między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t" są przechwytywane w ciągu jednej minuty na koncie magazynu jako obiekty blob przed przetworzeniem przez Analiza ruchu.
2. Domyślny interwał przetwarzania Analiza ruchu to 60 minut. Oznacza to, że co 60 minut Analiza ruchu wybiera obiekty blob z magazynu do agregacji. Jeśli wybrany interwał przetwarzania wynosi 10 minut, Analiza ruchu będą wybierać obiekty blob z konta magazynu po każdym 10 minutach.
3. Przepływy mające taki sam źródłowy adres IP, docelowy adres IP, port docelowy, nazwa sieciowej grupy zabezpieczeń, reguła sieciowej grupy zabezpieczeń, kierunek przepływu i protokół TLS (Transport Layer) (Uwaga: Port źródłowy jest wykluczony do agregacji) są uwzględniona do pojedynczego przepływu przez Analiza ruchu
4. Ten pojedynczy rekord jest dekoracyjny (szczegóły w sekcji poniżej) i pozyskany w Log Analytics przez Analiza ruchu. ten proces może potrwać maksymalnie 1 godzinę.
5. FlowStartTime_t pole wskazuje pierwsze wystąpienie tego zagregowanego przepływu (takie same cztery krotki) w czasie przetwarzania dziennika przepływu między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t".
6. Dla każdego zasobu w tym przepływy wskazane w interfejsie użytkownika są łącznymi przepływami widocznymi przez sieciowej grupy zabezpieczeń, ale w Log Analytics użytkownik zobaczy tylko jeden rekord z ograniczeniami. Aby wyświetlić wszystkie przepływy, użyj pola blob_id, do którego odwołuje się magazyn. Całkowita liczba przepływów dla tego rekordu będzie zgodna z pojedynczymi przepływami widocznymi w obiekcie blob.

Poniższe zapytanie pomaga sprawdzić wszystkie podsieci, które współpracują z publicznymi adresami IP spoza platformy Azure w ciągu ostatnich 30 dni.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
Aby wyświetlić ścieżkę obiektu BLOB dla przepływów w powyższym zapytaniu, użyj poniższego zapytania:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

Powyższe zapytanie konstruuje adres URL, aby uzyskać bezpośredni dostęp do obiektu BLOB. Adres URL z symbolami zastępczymi jest poniżej:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Pola używane w schemacie Analiza ruchu
  > [!IMPORTANT]
  > Schemat Analiza ruchu został zaktualizowany w dniu 22 sierpnia 2019. Nowy schemat zapewnia źródłowe i docelowe adresy IP osobno, co eliminuje konieczność przeanalizowania FlowDirectionych zapytań. </br>
  > FASchemaVersion_s zaktualizowane z 1 do 2. </br>
  > Przestarzałe pola: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Nowe pola: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Przestarzałe pola będą dostępne do 22 listopada, 2019.

Analiza ruchu jest oparta na Log Analytics, dzięki czemu można uruchamiać niestandardowe zapytania dotyczące danych dekoracyjnych przez Analiza ruchu i ustawiać dla nich alerty.

Poniżej wymieniono pola w schemacie i znaczenie

| Pole | Format | Komentarze |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela dla danych Analiza ruchu
| SubType_s | FlowLog | Podtyp dla dzienników przepływów. Używaj tylko "FlowLog", inne wartości SubType_s są dla wewnętrznej pracy produktu |
| FASchemaVersion_s |   2   | Wersja schematu. Nie odzwierciedla wersji dziennika przepływu sieciowej grupy zabezpieczeń |
| TimeProcessed_t   | Data i godzina w formacie UTC  | Godzina, o której Analiza ruchu przetworzył dzienniki nieprzetworzonych przepływów z konta magazynu |
| FlowIntervalStartTime_t | Data i godzina w formacie UTC |  Godzina rozpoczęcia interwału przetwarzania dziennika przepływu. Jest to czas, po jakim mierzony jest interwał przepływu |
| FlowIntervalEndTime_t | Data i godzina w formacie UTC | Godzina zakończenia interwału przetwarzania dziennika przepływu |
| FlowStartTime_t | Data i godzina w formacie UTC |  Pierwsze wystąpienie przepływu (które zostanie zagregowane) w przedziale czasu przetwarzania dziennika przepływu między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t". Ten przepływ jest agregowany na podstawie logiki agregacji |
| FlowEndTime_t | Data i godzina w formacie UTC | Ostatnie wystąpienie przepływu (które zostanie zagregowane) w interwale przetwarzania dziennika przepływu między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t". W obszarze Dziennik przepływu w wersji 2, to pole zawiera czas, w którym rozpoczęto ostatni przepływ z taką samą czterema krotką (oznaczono jako "B" w rekordzie nieprzetworzonych przepływów) |
| FlowType_s |  * IntraVNet <br> * Ramach <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Nieznany prywatny <br> * Nieznane | Definicja w notatkach poniżej tabeli |
| SrcIP_s | Źródłowy adres IP | Będzie puste w przypadku przepływów AzurePublic i ExternalPublic |
| DestIP_s | Docelowy adres IP | Będzie puste w przypadku przepływów AzurePublic i ExternalPublic |
| VMIP_s | Adres IP maszyny wirtualnej | Używane dla przepływów AzurePublic i ExternalPublic |
| PublicIP_s | Publiczne adresy IP | Używane dla przepływów AzurePublic i ExternalPublic |
| DestPort_d | Port docelowy | Port, na którym jest przychodzący ruch |
| L4Protocol_s  | * T <br> * U  | Protokół transportowy. T = TCP <br> U = UDP |
| L7Protocol_s  | Nazwa protokołu | Pochodny od portu docelowego |
| FlowDirection_s | * I = przychodzące<br> * O = wychodzące | Kierunek przepływu do/z sieciowej grupy zabezpieczeń jako dziennik dla przepływu |
| FlowStatus_s  | * A = dozwolone przez regułę sieciowej grupy zabezpieczeń <br> * D = odmowa przez regułę sieciowej grupy zabezpieczeń  | Stan przepływu dozwolony/nblocked przez sieciowej grupy zabezpieczeń zgodnie z dziennikiem przepływu |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) skojarzona z przepływem |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  Reguła sieciowej grupy zabezpieczeń, która zezwala lub nie odrzuciła tego przepływu |
| NSGRule_s | NSG_RULENAME |  Reguła sieciowej grupy zabezpieczeń, która zezwala lub nie odrzuciła tego przepływu |
| NSGRuleType_s | * Zdefiniowane przez użytkownika * domyślne |   Typ reguły sieciowej grupy zabezpieczeń używanej przez przepływ |
| MACAddress_s | Adres MAC | Adres MAC karty sieciowej, na której przechwycono przepływ |
| Subscription_s | Subskrypcja sieci wirtualnej platformy Azure/interfejsu sieciowego/maszyny wirtualnej jest wypełniana w tym polu | Dotyczy tylko typów przepływów typu Flow = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow i UnknownPrivate (typy przepływów, w których jest tylko jedna strona Azure) |
| Subscription1_s | Identyfikator subskrypcji | Identyfikator subskrypcji sieci wirtualnej/interfejsu sieciowego/maszyny wirtualnej, do której należy źródłowy adres IP w przepływie |
| Subscription2_s | Identyfikator subskrypcji | Identyfikator subskrypcji sieci wirtualnej/interfejsu sieciowego/maszyny wirtualnej, do której należy docelowy adres IP w przepływie |
| Region_s | Region świadczenia usługi Azure Virtual Network/interfejs sieciowy/maszyna wirtualna, do której należy adres IP w przepływie | Dotyczy tylko typów przepływów typu Flow = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow i UnknownPrivate (typy przepływów, w których jest tylko jedna strona Azure) |
| Region1_s | Region świadczenia usługi Azure | Region platformy Azure sieci wirtualnej/interfejsu sieciowego/maszyny wirtualnej, do której należy źródłowy adres IP w przepływie |
| Region2_s | Region świadczenia usługi Azure | Region platformy Azure sieci wirtualnej, do którego należy docelowy adres IP w przepływie |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  Karta sieciowa skojarzona z maszyną wirtualną wysyłająca lub odbierający ruch |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Karta sieciowa skojarzona ze źródłowym adresem IP w przepływie |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Karta sieciowa skojarzona z docelowym adresem IP w przepływie |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Maszyna wirtualna skojarzona z interfejsem sieciowym NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Maszyna wirtualna skojarzona ze źródłowym adresem IP w przepływie |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Maszyna wirtualna skojarzona z docelowym adresem IP w przepływie |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Podsieć skojarzona z NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Podsieć skojarzona ze źródłowym adresem IP w przepływie |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Podsieć skojarzona z docelowym adresem IP w przepływie |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Brama aplikacji skojarzona ze źródłowym adresem IP w przepływie |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Brama aplikacji skojarzona z docelowym adresem IP w przepływie |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Moduł równoważenia obciążenia skojarzony ze źródłowym adresem IP w przepływie |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Moduł równoważenia obciążenia skojarzony z docelowym adresem IP w przepływie |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brama sieci lokalnej skojarzona ze źródłowym adresem IP w przepływie |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brama sieci lokalnej skojarzona z docelowym adresem IP w przepływie |
| ConnectionType_s | Możliwe wartości to VNetPeering, bramy vpngateway i ExpressRoute |    Typ połączenia |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Nazwa połączenia. Dla elementu flowtype P2S zostanie on sformatowany jako <gateway name> _<VPN Client IP> |
| ConnectingVNets_s | Rozdzielana spacjami lista nazw sieci wirtualnych | W przypadku topologii gwiazdy i koncentratora sieci wirtualne będą umieszczane w tym miejscu |
| Country_s | Dwuliterowy kod kraju (ISO 3166-1 Alpha-2) | Wypełniono dla typu przepływu ExternalPublic. Wszystkie adresy IP w polu PublicIPs_s będą współużytkować ten sam kod kraju |
| AzureRegion_s | Lokalizacje regionów platformy Azure | Wypełniono dla typu przepływu AzurePublic. Wszystkie adresy IP w polu PublicIPs_s będą współużytkować region platformy Azure |
| AllowedInFlows_d | | Liczba dozwolonych przepływów przychodzących. Przedstawia liczbę przepływów, które współdzielą te same cztery kolekcje przychodzące do interfejsu sieciowego, na którym przechwycono przepływ |
| DeniedInFlows_d |  | Liczba odrzuconych przepływów przychodzących. (Ruch przychodzący do interfejsu sieciowego, na którym przechwycono przepływ) |
| AllowedOutFlows_d | | Liczba przepływów wychodzących, które były dozwolone (ruch wychodzący do interfejsu sieciowego, na którym przechwycono przepływ) |
| DeniedOutFlows_d  | | Liczba odrzuconych przepływów wychodzących (ruch wychodzący do interfejsu sieciowego, na którym przechwycono przepływ) |
| FlowCount_d | Przestarzałe. Całkowita liczba przepływów, które pasują do tych samych czterech krotek. W przypadku typów przepływów ExternalPublic i AzurePublic liczba będzie zawierać również przepływy z różnych adresów przywołującym element publicip.
| InboundPackets_d | Pakiety odebrane jako przechwycone w interfejsie sieciowym, w którym została zastosowana reguła sieciowej grupy zabezpieczeń | Jest to wypełniane tylko w wersji 2 schematu dziennika przepływu sieciowej grupy zabezpieczeń |
| OutboundPackets_d  | Pakiety wysłane jako przechwycone w interfejsie sieciowym, w którym zastosowano regułę sieciowej grupy zabezpieczeń | Jest to wypełniane tylko w wersji 2 schematu dziennika przepływu sieciowej grupy zabezpieczeń |
| InboundBytes_d |  Bajty odebrane jako przechwycone w interfejsie sieciowym, gdzie została zastosowana reguła sieciowej grupy zabezpieczeń | Jest to wypełniane tylko w wersji 2 schematu dziennika przepływu sieciowej grupy zabezpieczeń |
| OutboundBytes_d | Bajty wysłane jako przechwycone w interfejsie sieciowym, gdzie została zastosowana reguła sieciowej grupy zabezpieczeń | Jest to wypełniane tylko w wersji 2 schematu dziennika przepływu sieciowej grupy zabezpieczeń |
| CompletedFlows_d  |  | Ta wartość jest wypełniana wartością różną od zera tylko dla schematu dziennika przepływu sieciowej grupy zabezpieczeń w wersji 2 |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Wpisy rozdzielone słupkami |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Wpisy rozdzielone słupkami |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Wpisy rozdzielone słupkami |

### <a name="notes"></a>Uwagi

1. W przypadku przepływów AzurePublic i ExternalPublic adres IP maszyny wirtualnej platformy Azure należący do klienta jest wypełniany VMIP_s polu, podczas gdy publiczne adresy IP są wypełniane w polu PublicIPs_s. Dla tych dwóch typów przepływów należy używać VMIP_s i PublicIPs_s zamiast SrcIP_s i DestIP_s pól. W przypadku adresów AzurePublic i ExternalPublicIP należy dalej agregować, aby liczba rekordów pozyskanych do obszaru roboczego usługi log Analytics klienta była minimalna. (To pole zostanie wkrótce wycofane i będziemy używać SrcIP_ i DestIP_s w zależności od tego, czy maszyna wirtualna platformy Azure była źródłem lub miejscem docelowym w przepływie)
1. Szczegóły dotyczące typów przepływów: na podstawie adresów IP związanych z przepływem klasyfikujemy przepływy do następujących typów przepływu:
1. IntraVNet — adresy IP znajdujące się w przepływie znajdują się w tym samym Virtual Network platformy Azure.
1. Ramach — adresy IP w przepływie znajdują się w dwóch różnych sieciach wirtualnych platformy Azure.
1. S2S — (lokacja z lokacją) jeden z adresów IP należy do usługi Azure Virtual Network, podczas gdy drugi adres IP należy do sieci klienta (lokacja) połączonej z usługą Azure Virtual Network za pośrednictwem bramy sieci VPN lub usługi Express Route.
1. P2S — (wskaż lokację) jeden z adresów IP należy do platformy Azure Virtual Network podczas gdy drugi adres IP należy do sieci klienta (lokacja) połączonej z usługą Azure Virtual Network za pośrednictwem bramy sieci VPN.
1. AzurePublic — jeden z adresów IP należy do usługi Azure Virtual Network, podczas gdy drugi adres IP należy do wewnętrznych publicznych adresów IP platformy Azure należących do firmy Microsoft. Publiczne adresy IP należące do klienta nie będą częścią tego typu przepływu. Na przykład każdy klient wysyłający maszynę wirtualną do usługi platformy Azure (punkt końcowy magazynu) zostanie skategoryzowany w ramach tego typu przepływu.
1. ExternalPublic — jeden z adresów IP należy do usługi Azure Virtual Network, podczas gdy drugi adres IP jest publicznym adresem IP, który nie znajduje się na platformie Azure, nie jest raportowany jako złośliwy w źródłach ASC, które Analiza ruchu zużywać dla interwału przetwarzania między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t".
1. MaliciousFlow — jeden z adresów IP należy do usługi Azure Virtual Network, a drugi adres IP jest publicznym adresem IP, który nie znajduje się na platformie Azure i jest raportowany jako złośliwy w źródłach ASC, które Analiza ruchu zużywać dla interwału przetwarzania między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t".
1. UnknownPrivate — jeden z adresów IP należy do usługi Azure Virtual Network, podczas gdy drugi adres IP należy do zakresu prywatnych adresów IP zgodnie z definicją w dokumencie RFC 1918 i nie można go zmapować Analiza ruchu do witryny należącej do klienta lub Virtual Network platformy Azure.
1. Nieznane — nie można zamapować jednego z adresów IP w przepływie na platformę Azure, a także lokalnie (lokacja).
1. Niektóre nazwy pól są dołączane do \_ s lub \_ d. Nie oznaczają one źródła i miejsca docelowego, ale wskazują odpowiednio ciąg typów danych i wartość dziesiętną.

### <a name="next-steps"></a>Następne kroki
Aby uzyskać odpowiedzi na często zadawane pytania, zobacz temat [Analiza ruchu — często zadawane](traffic-analytics-faq.md) pytania, aby zobaczyć szczegóły dotyczące funkcjonalności, zobacz [Dokumentacja usługi Traffic Analytics](traffic-analytics.md)
