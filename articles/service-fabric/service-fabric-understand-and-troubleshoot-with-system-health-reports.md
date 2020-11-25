---
title: Rozwiązywanie problemów z raportami kondycji systemu
description: Opisuje raporty kondycji wysyłane przez składniki Service Fabric platformy Azure oraz ich użycie do rozwiązywania problemów z klastrami lub aplikacjami
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.openlocfilehash: 8e60ac5065c2f9543a641daf4f62299c00c61fc8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000661"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Używanie raportów kondycji systemu do rozwiązywania problemów
Składniki usługi Azure Service Fabric udostępniają raporty kondycji systemu na wszystkich jednostkach w klastrze, które są od razu do końca. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) tworzy i usuwa jednostki na podstawie raportów systemowych. Organizuje także je w hierarchii, która przechwytuje interakcje jednostek.

> [!NOTE]
> Aby zrozumieć koncepcje dotyczące kondycji, Przeczytaj więcej na temat [Service Fabric model kondycji](service-fabric-health-introduction.md).
> 
> 

Raporty kondycji systemu zapewniają wgląd w funkcje klastra i aplikacji oraz flagi problemów. W przypadku aplikacji i usług raporty kondycji systemu sprawdzają, czy jednostki są zaimplementowane i działają prawidłowo z perspektywy Service Fabric. Raporty nie zapewniają monitorowania kondycji logiki biznesowej usługi ani wykrywania procesów, które nie odpowiadają. Usługi użytkownika mogą wzbogacać dane dotyczące kondycji o informacje specyficzne dla ich logiki.

> [!NOTE]
> Raporty kondycji wysyłane przez liczniki użytkownika są widoczne dopiero *po* utworzeniu jednostki przez składniki systemowe. Po usunięciu jednostki magazyn kondycji automatycznie usuwa wszystkie skojarzone z nią raporty kondycji. To samo jest prawdziwe, gdy tworzone jest nowe wystąpienie obiektu. Przykładem jest utworzenie nowego stanowego trwałego wystąpienia repliki usługi. Wszystkie raporty skojarzone ze starym wystąpieniem zostaną usunięte i oczyszczone ze sklepu.
> 
> 

Raporty składników systemowych są identyfikowane przez źródło, które zaczyna się od "**system".** . Alarmy nie mogą używać tego samego prefiksu dla swoich źródeł, ponieważ raporty z nieprawidłowymi parametrami są odrzucane.

Przyjrzyjmy się niektórym raportom systemowym, aby zrozumieć, co je wyzwala, i aby dowiedzieć się, jak rozwiązać potencjalne problemy, które reprezentuje.

> [!NOTE]
> Service Fabric nadal dodawać raporty dotyczące warunków zainteresowania, które zwiększają wgląd w to, co dzieje się w klastrze i aplikacjach. Istniejące raporty można rozszerzyć, aby uzyskać więcej szczegółowych informacji ułatwiających szybsze rozwiązywanie problemów.
> 
> 

## <a name="cluster-system-health-reports"></a>Raporty kondycji systemu klastra
Jednostka kondycji klastra jest tworzona automatycznie w magazynie kondycji. Jeśli wszystko działa prawidłowo, nie ma raportu systemowego.

### <a name="neighborhood-loss"></a>Utrata klubu
**System. Federation** zgłasza błąd, gdy wykryje utratę klubu. Raport pochodzi z poszczególnych węzłów, a identyfikator węzła jest zawarty w nazwie właściwości. Jeśli jeden Klub zostanie utracony w całym pierścieniu Service Fabric, zazwyczaj oczekuje się, że dwa zdarzenia reprezentują obie strony raportu przerwy. Jeśli więcej klubów zostanie utraconych, istnieją więcej zdarzeń.

Ten raport określa limit czasu dla dzierżawy globalnej jako czas wygaśnięcia (TTL). Raport jest wysyłany ponownie co pół okresu ważności czasu wygaśnięcia przez czas, tak długo, jak warunek pozostaje aktywny. Zdarzenie zostanie automatycznie usunięte po jego wygaśnięciu. Zachowanie Usuń po wygaśnięciu gwarantuje, że raport jest czyszczony z magazynu kondycji prawidłowo, nawet jeśli węzeł raportowania nie działa.

* **SourceId**: System. Federation
* **Właściwość**: rozpoczyna się od **otoczenia** i zawiera informacje o węźle.
* **Następne kroki**: Zbadaj, dlaczego klub został utracony. Na przykład Sprawdź komunikację między węzłami klastra.

### <a name="rebuild"></a>Ponowne kompilowanie

Usługa Menedżer trybu failover (FM) zarządza informacjami o węzłach klastra. Gdy radio utraci swoje dane i przejdzie do utraty danych, nie może zagwarantować, że zawiera ona najbardziej zaktualizowane informacje o węzłach klastra. W takim przypadku system przechodzi przez ponowną kompilację, a System.FM zbiera dane ze wszystkich węzłów w klastrze w celu odbudowania jego stanu. Czasami, ze względu na problemy z siecią lub węzłem, ponowna kompilacja może zostać zablokowana lub zawiesić. Taka sytuacja może wystąpić w przypadku usługi Menedżer trybu failover Master (FMM). FMM to bezstanowa usługa systemowa, która śledzi, gdzie wszystkie FMs znajdują się w klastrze. Podstawowy FMM jest zawsze węzłem o IDENTYFIKATORze najbliższym 0. Jeśli ten węzeł zostanie porzucony, zostanie wyzwolona ponowna kompilacja.
Gdy wystąpi jeden z powyższych warunków, **System.fm** lub **System. FMM** flaguje go za pomocą raportu o błędach. Ponowne kompilowanie może być zablokowane w jednej z dwóch faz:

* **Oczekiwanie na emisję**: Radio/FMM czeka na odpowiedź komunikatu emisji z innych węzłów.

  * **Następne kroki**: Sprawdź, czy występuje problem z połączeniem sieciowym między węzłami.
* **Oczekiwanie na węzły**: Radio/FMM otrzymało odpowiedź emisyjną od innych węzłów i oczekuje na odpowiedź z określonych węzłów. Raport kondycji zawiera listę węzłów, dla których element FM/FMM oczekuje na odpowiedź.
   * **Następne kroki**: Zbadaj połączenie sieciowe między FM/FMM i wymienionymi węzłami. Zbadaj każdy z wymienionych węzłów, aby uzyskać inne możliwe problemy.

* **SourceId**: System.FM lub system. FMM
* **Właściwość**: Kompiluj ponownie.
* **Następne kroki**: Zbadaj połączenie sieciowe między węzłami, a także stan wszystkich określonych węzłów, które są wymienione na liście raportu kondycji.

### <a name="seed-node-status"></a>Stan węzła inicjatora
**System.fm** zgłasza ostrzeżenie na poziomie klastra, jeśli niektóre węzły inicjatora są w złej kondycji. Węzły inicjatora są węzłami, które utrzymują dostępność bazowego klastra. Te węzły pomagają zapewnić, że klaster pozostanie w ramach ustanowienia dzierżaw z innymi węzłami i służy jako tiebreakers w przypadku niektórych rodzajów awarii sieci. Jeśli większość węzłów inicjatora znajduje się w klastrze i nie zostaną przywrócone, klaster zostanie automatycznie zamknięty. 

Węzeł inicjatora jest w złej kondycji, jeśli jego stan węzła nie działa, został usunięty lub nieznany.
Raport ostrzegawczy stanu węzła inicjatora wyświetli listę wszystkich węzłów inicjatora w złej kondycji ze szczegółowymi informacjami.

* **SourceId**: System.fm
* **Właściwość**: SeedNodeStatus
* **Następne kroki**: Jeśli to ostrzeżenie jest wyświetlane w klastrze, postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać ten problem: w przypadku klastra z systemem Service Fabric w wersji 6,5 lub nowszej: w przypadku klastra Service Fabric na platformie Azure po przekroczeniu węzła inicjatora Service Fabric spróbuje zmienić go na węzeł niebędący inicjatorem. Aby to osiągnąć, upewnij się, że liczba węzłów innych niż inicjator w typie podstawowym nie jest większa lub równa liczbie węzłów wypełniania. W razie potrzeby Dodaj więcej węzłów do typu węzła podstawowego, aby to osiągnąć.
W zależności od stanu klastra może upłynąć trochę czasu, aby rozwiązać ten problem. Po wykonaniu tej czynności raport ostrzegawczy zostanie automatycznie wyczyszczony.

Aby wyczyścić Raport z ostrzeżeniem dla Service Fabric klastra autonomicznego, wszystkie węzły inicjatora muszą stać się w dobrej kondycji. W zależności od tego, dlaczego węzły inicjatora są w złej kondycji, należy wykonać różne akcje: Jeśli węzeł inicjatora nie działa, użytkownicy muszą przenieść ten węzeł inicjatora; Jeśli węzeł inicjatora został usunięty lub nieznany, ten węzeł inicjatora [musi zostać usunięty z klastra](./service-fabric-cluster-windows-server-add-remove-nodes.md).
Raport ostrzegawczy jest automatycznie czyszczony, gdy wszystkie węzły inicjatora staną się w dobrej kondycji.

W przypadku klastra z systemem Service Fabric w wersji starszej niż 6,5: w tym przypadku raport ostrzegawczy musi zostać wyczyszczony ręcznie. **Przed wyczyszczeniem raportu użytkownicy powinni upewnić się, że wszystkie węzły inicjatora staną się w dobrej kondycji**: Jeśli węzeł inicjatora nie działa, użytkownicy muszą przenieść ten węzeł inicjatora. Jeśli węzeł inicjatora zostanie usunięty lub nieznany, ten węzeł inicjatora musi zostać usunięty z klastra.
Gdy wszystkie węzły inicjatora staną się w dobrej kondycji, użyj następującego polecenia programu PowerShell, aby [wyczyścić raport ostrzegawczy](/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Wygaśnięcie certyfikatu
**System. FabricNode** zgłasza ostrzeżenie, gdy certyfikaty używane przez węzeł są bliskie wygaśnięcia. Istnieją trzy certyfikaty na węzeł: **Certificate_cluster**, **Certificate_server** i **Certificate_default_client**. Gdy okres ważności wynosi co najmniej dwa tygodnie, stan kondycji raportu jest prawidłowy. Gdy wygaśnięcie ma wartość w ciągu dwóch tygodni, typ raportu jest ostrzeżeniem. Czas wygaśnięcia tych zdarzeń jest nieskończony i jest usuwany, gdy węzeł opuszcza klaster.

* **SourceId**: System. FabricNode
* **Właściwość**: rozpoczyna się od **certyfikatu** i zawiera więcej informacji na temat typu certyfikatu.
* **Następne kroki**: zaktualizuj certyfikaty, jeśli wkrótce wygasną.

### <a name="load-capacity-violation"></a>Naruszenie pojemności obciążenia
Service Fabric Load Balancer zgłasza ostrzeżenie po wykryciu naruszenia pojemności węzła.

* **SourceId**: System. PLB
* **Właściwość**: zaczyna się od **pojemności**.
* **Następne kroki**: Sprawdź podane metryki i Wyświetl bieżącą pojemność w węźle.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Niezgodność pojemności węzła dla metryk ładu zasobów
System. host zgłasza ostrzeżenie, jeśli zdefiniowane pojemności węzłów w manifeście klastra są większe niż rzeczywiste pojemności węzła dla metryk ładu zasobów (pamięć i rdzenie procesora CPU). Raport o kondycji jest wyświetlany, gdy pierwszy pakiet usługi, który używa rejestrów [ładu zasobów](service-fabric-resource-governance.md) w określonym węźle.

* **SourceId**: System. hosting
* **Właściwość**: **ResourceGovernance**.
* **Następne kroki**: ten problem może być problemem, ponieważ zarządzanie pakietami usług nie jest wymuszane zgodnie z oczekiwaniami, a [Zarządzanie zasobami](service-fabric-resource-governance.md) nie działa prawidłowo. Zaktualizuj manifest klastra o prawidłowej pojemności węzła dla tych metryk lub nie określaj go i pozwól Service Fabric automatycznie wykrywać dostępne zasoby.

## <a name="application-system-health-reports"></a>Raporty kondycji systemu aplikacji
System.CM, który reprezentuje usługę Menedżera klastra, jest urzędem zarządzającym informacjami o aplikacji.

### <a name="state"></a>Stan
System.CM raporty jako poprawne po utworzeniu lub zaktualizowaniu aplikacji. Informuje magazyn kondycji o usunięciu aplikacji, dzięki czemu można go usunąć ze sklepu.

* **SourceId**: System.cm
* **Właściwość**: State.
* **Następne kroki**: Jeśli aplikacja została utworzona lub zaktualizowana, powinna zawierać Raport kondycji Menedżera klastra. W przeciwnym razie Sprawdź stan aplikacji, wydając zapytanie. Na przykład użyj polecenia cmdlet programu PowerShell **Get-ServiceFabricApplication-ApplicationName** *ApplicationName*.

Poniższy przykład pokazuje zdarzenie State w **sieci szkieletowej:/WORDCOUNT** :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Raporty kondycji systemu usługi
System.FM, który reprezentuje usługę Menedżer trybu failover, jest urzędem zarządzającym informacjami o usługach.

### <a name="state"></a>Stan
System.FM raporty jako poprawne, gdy usługa została utworzona. Usuwa jednostkę z magazynu kondycji, gdy usługa zostanie usunięta.

* **SourceId**: System.fm
* **Właściwość**: State.

Poniższy przykład przedstawia zdarzenie stanu w usłudze Service **Fabric:/WORDCOUNT/usługi wordcountwebservice**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Błąd korelacji usługi
**System. PLB** zgłasza błąd, gdy wykryje, że aktualizacja usługi jest skorelowane z inną usługą, która tworzy łańcuch koligacji. Raport zostanie wyczyszczony po pomyślnej aktualizacji.

* **SourceId**: System. PLB
* **Właściwość**: **ServiceDescription**.
* **Następne kroki**: Sprawdź opis skorelowanych usług.

## <a name="partition-system-health-reports"></a>Partycjonowanie raportów kondycji systemu
System.FM, który reprezentuje usługę Menedżer trybu failover, jest urzędem zarządzającym informacjami o partycjach usługi.

### <a name="state"></a>Stan
System.FM raporty jako poprawne, gdy partycja została utworzona i jest w dobrej kondycji. Usuwa jednostkę z magazynu kondycji, gdy partycja zostanie usunięta.

Jeśli partycja jest mniejsza niż minimalna liczba replik, zgłosi błąd. Jeśli partycja nie jest mniejsza niż minimalna liczba replik, ale znajduje się poniżej docelowej liczby replik, zgłosi ostrzeżenie. Jeśli partycja jest w utracie kworum, System.FM zgłasza błąd.

Inne istotne zdarzenia obejmują ostrzeżenie, gdy ponowna konfiguracja trwa dłużej niż oczekiwano i gdy kompilacja trwa dłużej niż oczekiwano. Oczekiwany czas na kompilację i ponowną konfigurację można skonfigurować na podstawie scenariuszy usługi. Jeśli na przykład usługa ma terabajt stanu, na przykład Azure SQL Database, kompilacja trwa dłużej niż w przypadku usługi z niewielką ilością stanu.

* **SourceId**: System.fm
* **Właściwość**: State.
* **Następne kroki**: Jeśli kondycja nie jest prawidłowa, istnieje możliwość, że niektóre repliki nie zostały prawidłowo utworzone, otwarte lub podwyższone do podstawowej lub pomocniczej. 

Jeśli opis zawiera opis utraty kworum, zbadasz szczegółowy raport o kondycji dla replik, które nie działa, i połączysz ich kopię zapasową, aby przywrócić partycję w trybie online.

Jeśli opis opisuje zatrzymaną ponownie [konfigurację](service-fabric-concepts-reconfiguration.md)partycji, Raport kondycji w replice podstawowej zawiera dodatkowe informacje.

W przypadku innych raportów kondycji System.FM raporty dotyczące replik lub partycji lub usługi mogą być dostępne w innych składnikach systemu. 

W poniższych przykładach opisano niektóre z tych raportów. 

W poniższym przykładzie przedstawiono partycję w dobrej kondycji:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Poniższy przykład przedstawia kondycję partycji, która znajduje się poniżej docelowej liczby replik. Następnym krokiem jest uzyskanie opisu partycji, który pokazuje, jak jest skonfigurowany: **MinReplicaSetSize** to trzy, a **wartość targetreplicasetsize** to siedem. Następnie uzyskaj liczbę węzłów w klastrze, które w tym przypadku są pięć. Tak więc w tym przypadku nie można umieścić dwóch replik, ponieważ docelowa liczba replik jest większa niż liczba dostępnych węzłów.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

W poniższym przykładzie przedstawiono kondycję partycji, która została zablokowana w ramach ponownej konfiguracji, ponieważ użytkownik nie honoruje tokenu anulowania w metodzie **RunAsync** . Badanie raportu kondycji każdej repliki oznaczonej jako podstawowa (P) może pomóc w dokładniejszym przejściu do problemu.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Ten raport kondycji przedstawia stan replik partycji do ponownej konfiguracji: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

W przypadku każdej repliki Raport kondycji zawiera następujące pozycje:
- Poprzednia rola konfiguracji
- Bieżąca rola konfiguracji
- [Stan repliki](service-fabric-concepts-replica-lifecycle.md)
- Węzeł, na którym jest uruchomiona replika
- IDENTYFIKATOR repliki

W przypadku, podobnie jak w przypadku przykładu, należy wykonać dalsze badania. Sprawdź kondycję poszczególnych replik, rozpoczynając od replik oznaczonych jako `Primary` i `Secondary` (131482789658160654 i 131482789688598467) w poprzednim przykładzie.

### <a name="replica-constraint-violation"></a>Naruszenie ograniczenia repliki
**System. PLB** zgłasza ostrzeżenie, jeśli wykryje naruszenie ograniczenia repliki i nie może umieścić wszystkich replik partycji. Szczegóły raportu zawierają informacje o ograniczeniach i właściwościach uniemożliwiające rozmieszczenie replik.

* **SourceId**: System. PLB
* **Właściwość**: rozpoczyna się od **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Raporty kondycji systemu repliki
**System. RA**, który reprezentuje składnik Agent rekonfiguracji, jest urzędem dla stanu repliki.

### <a name="state"></a>Stan
Raporty system. RA są prawidłowe, gdy replika została utworzona.

* **SourceId**: System. RA
* **Właściwość**: State.

W poniższym przykładzie przedstawiono replikę w dobrej kondycji:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Ta właściwość jest używana do wskazania ostrzeżeń lub błędów podczas próby otwarcia repliki, zamknięcia repliki lub przejścia repliki z jednej roli do innej. Aby uzyskać więcej informacji, zobacz [cykl życia repliki](service-fabric-concepts-replica-lifecycle.md). Błędy mogą być wyjątkami zgłoszonymi przez wywołania interfejsu API lub awarie procesu hosta usługi w tym czasie. W przypadku niepowodzeń z powodu wywołań interfejsu API z kodu C# Service Fabric dodaje do raportu kondycji wyjątek i ślad stosu.

Te ostrzeżenia dotyczące kondycji są wywoływane po ponowieniu próby wykonania akcji lokalnie przez wiele razy (w zależności od zasad). Service Fabric ponawianie próby wykonania akcji do maksymalnej wartości progowej. Po osiągnięciu maksymalnego progu może spróbować rozwiązać problem. Ta próba może spowodować wyczyszczenie tych ostrzeżeń, ponieważ zawiera ona akcję w tym węźle. Na przykład jeśli nie można otworzyć repliki w węźle, Service Fabric zgłasza ostrzeżenie dotyczące kondycji. Jeśli nie można otworzyć repliki w dalszym ciągu, Service Fabric działa do samonaprawiania. Ta akcja może wiązać się z próbą wykonania tej samej operacji w innym węźle. Spowoduje to wyczyszczenie ostrzeżenia wygenerowanego przez tę replikę. 

* **SourceId**: System. RA
* **Właściwość**: **ReplicaOpenStatus**, **ReplicaCloseStatus** i **ReplicaChangeRoleStatus**.
* **Następne kroki**: Zbadaj kod usługi lub Zrzuty awaryjne w celu zidentyfikowania przyczyny niepowodzenia operacji.

W poniższym przykładzie przedstawiono kondycję repliki, która jest wyrzucana `TargetInvocationException` z metody Open. Opis zawiera punkt awarii, **IStatefulServiceReplica. Open**, typ wyjątku **TargetInvocationException —** i ślad stosu.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

W poniższym przykładzie przedstawiono replikę, która ciągle ulega awarii podczas zamykania:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Ponowne konfigurowanie
Ta właściwość służy do wskazywania, gdy replika przeprowadzająca [ponowną konfigurację](service-fabric-concepts-reconfiguration.md) wykryje, że Ponowna konfiguracja jest wstrzymana lub zablokowana. Ten raport kondycji może znajdować się w replice, której bieżąca rola jest podstawowa, z wyjątkiem przypadków ponownej konfiguracji wymiany, w której może znajdować się w replice, która jest obniżana od podstawowej do aktywnej pomocniczej.

Ponowna konfiguracja może być zablokowana z jednego z następujących powodów:

- Akcja w replice lokalnej, taka sama replika, co w przypadku przeprowadzania ponownej konfiguracji, nie kończy się. W takim przypadku badanie raportów kondycji w tej replice z innych składników, system. Rd lub System.RE, może zawierać dodatkowe informacje.

- Akcja nie została ukończona w replice zdalnej. Repliki, dla których oczekujące akcje są wymienione w raporcie o kondycji. W przypadku tych replik zdalnych należy przeprowadzić dalsze badanie raportów kondycji. Mogą być również problemy z komunikacją między tym węzłem i węzłem zdalnym.

W rzadkich przypadkach ponowna konfiguracja może być zablokowana z powodu komunikacji lub innych problemów między tym węzłem a usługą Menedżer trybu failover.

* **SourceId**: System. RA
* **Właściwość**: Ponowna konfiguracja.
* **Następne kroki**: Zbadaj repliki lokalne lub zdalne w zależności od opisu raportu kondycji.

W poniższym przykładzie przedstawiono Raport kondycji, w którym jest zablokowana ponowna konfiguracja repliki lokalnej. W tym przykładzie jest to spowodowane tym, że usługa nie honoruje tokenu anulowania.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

W poniższym przykładzie przedstawiono Raport kondycji, w którym zablokowanie ponownej konfiguracji oczekuje na odpowiedź z dwóch replik zdalnych. W tym przykładzie istnieją trzy repliki w partycji, w tym bieżący podstawowy. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Ten raport kondycji pokazuje, że Ponowna konfiguracja jest zablokowana podczas oczekiwania na odpowiedź z dwóch replik: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Dla każdej repliki są określone następujące informacje:
- Poprzednia rola konfiguracji
- Bieżąca rola konfiguracji
- [Stan repliki](service-fabric-concepts-replica-lifecycle.md)
- IDENTYFIKATOR węzła
- IDENTYFIKATOR repliki

Aby odblokować ponowną konfigurację:
- Repliki **w dół** powinny zostać przesunięte. 
- Repliki **inbuild** powinny zakończyć kompilację i przejście do gotowe.

### <a name="slow-service-api-call"></a>Wywołanie interfejsu API powolnej usługi
**System. Rd** i **System. Replikator** zgłasza ostrzeżenie, jeśli wywołanie kodu usługi użytkownika trwa dłużej niż skonfigurowany czas. Ostrzeżenie jest wyczyszczone po zakończeniu wywołania.

* **SourceId**: System. Rd lub system. Replikator
* **Właściwość**: Nazwa powolnego interfejsu API. Opis zawiera więcej szczegółów o czasie oczekiwania interfejsu API.
* **Następne kroki**: Sprawdź, dlaczego połączenie trwa dłużej niż oczekiwano.

W poniższym przykładzie pokazano zdarzenie kondycji z elementu System. Rd dla niezawodnej usługi, która nie honoruje tokenu anulowania w **RunAsync**:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

Właściwość i tekst wskazują, który interfejs API został zablokowany. Kolejne kroki, które należy wykonać w przypadku różnych zablokowanych interfejsów API, są różne. Każdy interfejs API na *IStatefulServiceReplica* lub *IStatelessServiceInstance* jest zwykle usterką w kodzie usługi. W poniższej sekcji opisano sposób tłumaczenia na [model Reliable Services](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica. Open**: to ostrzeżenie wskazuje, że wywołanie elementu `CreateServiceInstanceListeners` , `ICommunicationListener.OpenAsync` lub, jeśli zostało zastąpione, `OnOpenAsync` jest zablokowane.

- **IStatefulServiceReplica. Close** i **IStatefulServiceReplica. Abort**: najbardziej typowym przypadkiem jest usługa, która nie honoruje tokenu anulowania przekazaną do `RunAsync` . Może to być również `ICommunicationListener.CloseAsync` w przypadku, gdy jest on zasłonięty `OnCloseAsync` .

- **IStatefulServiceReplica. ChangeRole** i **IStatefulServiceReplica. ChangeRole (N)**: najczęściej spotykanym przypadkiem jest usługa, która nie honoruje tokenu anulowania przesłanego do `RunAsync` . Najlepszym rozwiązaniem jest ponowne uruchomienie repliki w tym scenariuszu.

- **IStatefulServiceReplica. ChangeRole (P)**: najbardziej typowym przypadkiem jest to, że usługa nie zwróciła zadania z `RunAsync` .

Inne wywołania interfejsu API, które mogą zostać zablokowane, znajdują się w interfejsie **IReplicator** . Na przykład:

- **IReplicator. CatchupReplicaSet**: to ostrzeżenie wskazuje jedną z dwóch rzeczy. Istnieją niewystarczające repliki. Aby sprawdzić, czy tak jest, sprawdź stan repliki replik w partycji lub raporcie o kondycji System.FM dla zablokowanej ponownej konfiguracji. Lub repliki nie potwierdzają operacji. Polecenia cmdlet programu PowerShell `Get-ServiceFabricDeployedReplicaDetail` można użyć do określenia postępu wszystkich replik. Problem polega na replikach `LastAppliedReplicationSequenceNumber` , których wartość znajduje się za `CommittedSequenceNumber` wartością podstawową.

- **IReplicator. BuildReplica ( \<Remote ReplicaId> )**: to ostrzeżenie wskazuje na problem w procesie kompilacji. Aby uzyskać więcej informacji, zobacz [cykl życia repliki](service-fabric-concepts-replica-lifecycle.md). Może to być spowodowane nieprawidłową konfiguracją adresu replikatora. Aby uzyskać więcej informacji, zobacz [Konfigurowanie stanowego Reliable Services](service-fabric-reliable-services-configuration.md) i [określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md). Może być również problemem w węźle zdalnym.

### <a name="replicator-system-health-reports"></a>Raportowanie raportów o kondycji systemu
**Pełna Kolejka replikacji:** 
 **System. Replikator** zgłasza ostrzeżenie, gdy kolejka replikacji jest pełna. Na serwerze podstawowym Kolejka replikacji zwykle zostaje zapełniona, ponieważ co najmniej jedna replika pomocnicza jest powolna do potwierdzenia operacji. Na serwerze pomocniczym zwykle zdarza się to, gdy usługa jest powolna do zastosowania operacji. Ostrzeżenie jest wyczyszczone, gdy kolejka nie jest już pełna.

* **SourceId**: System. Replikator
* **Właściwość**: **PrimaryReplicationQueueStatus** lub **SecondaryReplicationQueueStatus**, w zależności od roli repliki.
* **Następne kroki**: Jeśli raport znajduje się na serwerze podstawowym, sprawdź połączenie między węzłami w klastrze. Jeśli wszystkie połączenia są w dobrej kondycji, może się zdarzyć, że co najmniej jedna powolna dodatkowa z opóźnieniem dysku do zastosowania operacji. Jeśli raport znajduje się na serwerze pomocniczym, należy najpierw sprawdzić użycie dysku i wydajność w węźle. Następnie sprawdź połączenie wychodzące z wolnego węzła do podstawowego.

**RemoteReplicatorConnectionStatus:** 
 **System. Replikator** w replice podstawowej raportuje ostrzeżenie, gdy połączenie z serwerem pomocniczym (zdalnym) nie jest w dobrej kondycji. Adres zdalnego replikatora jest wyświetlany w komunikacie raportu, co sprawia, że jest wygodniejszy do wykrywania, czy nieprawidłowa konfiguracja została przekazana lub czy występują problemy z siecią między replikatorami.

* **SourceId**: System. Replikator
* **Właściwość**: **RemoteReplicatorConnectionStatus**.
* **Następne kroki**: Sprawdź komunikat o błędzie i upewnij się, że adres zdalnego replikatora jest prawidłowo skonfigurowany. Na przykład, jeśli Replikator zdalny jest otwarty z adresem nasłuchiwania "localhost", nie jest dostępny z zewnątrz. Jeśli adres jest poprawny, sprawdź połączenie między węzłem podstawowym a adresem zdalnym, aby znaleźć potencjalne problemy z siecią.

### <a name="replication-queue-full"></a>Zapełnienie kolejki replikacji
**System. Replikator** zgłasza ostrzeżenie, gdy kolejka replikacji jest pełna. Na serwerze podstawowym Kolejka replikacji zwykle zostaje zapełniona, ponieważ co najmniej jedna replika pomocnicza jest powolna do potwierdzenia operacji. Na serwerze pomocniczym zwykle zdarza się to, gdy usługa jest powolna do zastosowania operacji. Ostrzeżenie jest wyczyszczone, gdy kolejka nie jest już pełna.

* **SourceId**: System. Replikator
* **Właściwość**: **PrimaryReplicationQueueStatus** lub **SecondaryReplicationQueueStatus**, w zależności od roli repliki.

### <a name="slow-naming-operations"></a>Wolne operacje nazewnictwa
**System. NamingService** raportuje kondycję repliki podstawowej, gdy operacja nazewnictwa trwa dłużej niż akceptowalne. Przykłady operacji nazewnictwa to [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) lub [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Więcej metod można znaleźć w obszarze FabricClient. Na przykład można je znaleźć w obszarze [metody zarządzania usługami](/dotnet/api/system.fabric.fabricclient.servicemanagementclient) lub [metody zarządzania właściwościami](/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Usługa nazewnictwa rozwiązuje nazwy usług do lokalizacji w klastrze. Użytkownicy mogą używać go do zarządzania nazwami i właściwościami usług. Jest to Service Fabric podzielona na partycje. Jedna z partycji reprezentuje *właściciela urzędu*, który zawiera metadane dotyczące wszystkich Service Fabric nazw i usług. Nazwy Service Fabric są mapowane na różne partycje o nazwie partycje *właściciela nazwy* , więc usługa jest rozszerzalna. Przeczytaj więcej na temat [usługi nazewnictwa](service-fabric-architecture.md).
> 
> 

Gdy operacja nazewnictwa trwa dłużej niż oczekiwano, operacja jest oflagowana z ostrzeżeniem w ramach podstawowej repliki partycji usługi nazewnictwa, która służy do wykonywania tej operacji. Jeśli operacja zakończy się pomyślnie, ostrzeżenie jest wyczyszczone. Jeśli operacja zakończy się błędem, raport o kondycji zawiera szczegółowe informacje o błędzie.

* **SourceId**: System. NamingService
* **Właściwość**: zaczyna się od prefiksu "**Duration_**" i identyfikuje powolne działanie oraz nazwę Service Fabric, na której jest stosowana operacja. Jeśli na przykład usługa Create Service w nazwie **Fabric:/MojaApl/WebService** trwa zbyt długo, właściwość ma **Duration_AOCreateService. Fabric:/MojaApl/moje usługi**. "AO" wskazuje rolę partycji nazewnictwa dla tej nazwy i operacji.
* **Następne kroki**: Sprawdź, dlaczego operacja nazewnictwa nie powiedzie się. Każda operacja może mieć różne przyczyny główne. Na przykład usługa usuwania może być zablokowana. Usługa może być zablokowana, ponieważ Host aplikacji utrzymuje awarię w węźle z powodu błędu użytkownika w kodzie usługi.

Poniższy przykład przedstawia operację tworzenia usługi. Operacja trwała dłużej niż skonfigurowany czas trwania. "AO" ponawia próbę i wysyła do "NO". Wartość "nie" zakończyła ostatnią operację z przekroczeniem limitu czasu. W takim przypadku ta sama replika jest podstawowa dla ról "AO" i "NO".

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication raporty kondycji systemu
**System. hosting** jest urzędem dla wdrożonych jednostek.

### <a name="activation"></a>Uaktywnienie
W przypadku pomyślnego aktywowania aplikacji w węźle system. hostuje raporty jako poprawne. W przeciwnym razie zgłasza błąd.

* **SourceId**: System. hosting
* **Właściwość**: **Aktywacja**, w tym wersja wdrożenia.
* **Następne kroki**: Jeśli aplikacja jest w złej kondycji, sprawdź, dlaczego aktywacja nie powiodła się.

W poniższym przykładzie przedstawiono pomyślną aktywację:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Pobierz
System. host zgłasza błąd, jeśli pobieranie pakietu aplikacji nie powiedzie się.

* **SourceId**: System. hosting
* **Właściwość**: **Download**, w tym wersja wdrożenia.
* **Następne kroki**: Zbadaj, Dlaczego pobieranie nie powiodło się w węźle.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage raporty kondycji systemu
**System. hosting** jest urzędem dla wdrożonych jednostek.

### <a name="service-package-activation"></a>Aktywacja pakietu usługi
Jeśli aktywacja pakietu usługi w węźle zakończyła się pomyślnie, system. hostuje raporty jako poprawne. W przeciwnym razie zgłasza błąd.

* **SourceId**: System. hosting
* **Właściwość**: aktywacja.
* **Następne kroki**: Zbadaj, dlaczego aktywacja nie powiodła się.

### <a name="code-package-activation"></a>Aktywacja pakietu kodu
W przypadku pomyślnego zakończenia aktywacji raporty system. hostowanie są prawidłowe dla każdego pakietu kodu. Jeśli aktywacja nie powiedzie się, zgłasza ostrzeżenie zgodnie z konfiguracją. Jeśli **CodePackage** nie można uaktywnić lub zakończyć z błędem większym niż skonfigurowany **CodePackageHealthErrorThreshold**, hostowanie zgłosi błąd. Jeśli pakiet usługi zawiera wiele pakietów kodu, raport aktywacji jest generowany dla każdego z nich.

* **SourceId**: System. hosting
* **Property**: używa prefiksu **CodePackageActivation** i zawiera nazwę pakietu kodu oraz punkt wejścia jako *CodePackageActivation: CodePackageName: SetupEntryPoint/EntryPoint*. Na przykład **CodePackageActivation: code: SetupEntryPoint**.

### <a name="service-type-registration"></a>Rejestracja typu usługi
Jeśli typ usługi został pomyślnie zarejestrowany, system. hostuje raporty jako poprawne. Zgłasza błąd, jeśli rejestracja nie została ukończona w czasie, zgodnie z konfiguracją przy użyciu **ServiceTypeRegistrationTimeout**. Jeśli środowisko uruchomieniowe jest zamknięte, typ usługi jest wyrejestrowany z węzła i hostowanie raportuje ostrzeżenie.

* **SourceId**: System. hosting
* **Property**: używa prefiksu **ServiceTypeRegistration** i zawiera nazwę typu usługi. Na przykład **ServiceTypeRegistration: FileStoreServiceType**.

W poniższym przykładzie przedstawiono pakiet usługi wdrożony w dobrej kondycji:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Pobierz
System. host zgłasza błąd, jeśli pobranie pakietu usługi nie powiedzie się.

* **SourceId**: System. hosting
* **Właściwość**: **Download**, w tym wersja wdrożenia.
* **Następne kroki**: Zbadaj, Dlaczego pobieranie nie powiodło się w węźle.

### <a name="upgrade-validation"></a>Weryfikacja uaktualnienia
System. host zgłasza błąd, jeśli Walidacja w trakcie uaktualniania nie powiedzie się lub uaktualnienie nie powiedzie się w węźle.

* **SourceId**: System. hosting
* **Właściwość**: używa prefiksu **FabricUpgradeValidation** i zawiera wersję uaktualnienia.
* **Opis**: wskazuje, że wystąpił błąd.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Niezdefiniowana pojemność węzła dla metryk ładu zasobów
System. host zgłasza ostrzeżenie, jeśli pojemności węzła nie są zdefiniowane w manifeście klastra, a konfiguracja automatycznego wykrywania jest wyłączona. Service Fabric zgłasza kondycję za każdym razem, gdy pakiet usługi używa rejestrów [ładu zasobów](service-fabric-resource-governance.md) w określonym węźle.

* **SourceId**: System. hosting
* **Właściwość**: **ResourceGovernance**.
* **Następne kroki**: preferowany sposób rozwiązania tego problemu polega na zmodyfikowaniu manifestu klastra w celu włączenia automatycznego wykrywania dostępnych zasobów. Innym sposobem jest zaktualizowanie manifestu klastra przy użyciu poprawnie określonych pojemności węzłów dla tych metryk.

## <a name="next-steps"></a>Następne kroki
* [Wyświetlanie raportów o kondycji Service Fabric](service-fabric-view-entities-aggregated-health.md)

* [Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Service Fabric uaktualniania aplikacji](service-fabric-application-upgrade.md)
