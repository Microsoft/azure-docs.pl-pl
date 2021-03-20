---
title: 'Service Fabric zabezpieczenia klastra: role klienta'
description: W tym artykule opisano dwie role klienta i uprawnienia udostępniane role.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451896"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Kontrola dostępu oparta na rolach dla klientów Service Fabric
Usługa Azure Service Fabric obsługuje dwa różne typy kontroli dostępu dla klientów, którzy są połączeni z klastrem Service Fabric: administrator i użytkownik. Kontrola dostępu pozwala administratorowi klastra ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników, co sprawia, że klaster jest bezpieczniejszy.  

**Administratorzy** mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu i zapisu). Domyślnie **Użytkownicy** mają dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytania) i możliwość rozpoznawania aplikacji i usług.

Podczas tworzenia klastra należy określić dwie role klienta (administrator i klient), dostarczając oddzielne certyfikaty dla każdego z nich. Aby uzyskać szczegółowe informacje na temat konfigurowania bezpiecznego klastra Service Fabric, zobacz [Service Fabric zabezpieczenia klastra](service-fabric-cluster-security.md) .

## <a name="default-access-control-settings"></a>Domyślne ustawienia kontroli dostępu
Typ kontroli dostępu administratora ma pełny dostęp do wszystkich interfejsów API FabricClient. Może wykonywać wszelkie operacje odczytu i zapisu w klastrze Service Fabric, w tym następujące operacje:

### <a name="application-and-service-operations"></a>Operacje aplikacji i usług
* Moje **usługi**: Tworzenie usługi                             
* **CreateServiceFromTemplate**: Tworzenie usługi z szablonu                             
* **Elementu updateservice**: aktualizacje usługi                             
* **DeleteService**: Usuwanie usługi                             
* **ProvisionApplicationType**: Obsługa typu aplikacji                             
* Moja **aplikacja**: Tworzenie aplikacji                               
* **Operacji deleteapplication**: Usuwanie aplikacji                             
* **UpgradeApplication**: uruchamianie lub przerywanie uaktualnień aplikacji                             
* **UnprovisionApplicationType**: anulowanie aprowizacji typu aplikacji                             
* **MoveNextUpgradeDomain**: wznawianie uaktualnień aplikacji za pomocą jawnej domeny aktualizacji                             
* **ReportUpgradeHealth**: wznawianie uaktualnień aplikacji z aktualnym postępem uaktualniania                             
* **ReportHealth**: kondycja raportowania                             
* **PredeployPackageToNode**: interfejs API preinstalacji                            
* **CodePackageControl**: ponowne uruchamianie pakietów kodu                             
* **RecoverPartition**: odzyskiwanie partycji                             
* **RecoverPartitions**: odzyskiwanie partycji                             
* **RecoverServicePartitions**: odzyskiwanie partycji usługi                             
* **RecoverSystemPartitions**: odzyskiwanie partycji usługi systemowej                             

### <a name="cluster-operations"></a>Operacje klastra
* **ProvisionFabric**: Inicjowanie obsługi manifestu MSI i/lub klastra                             
* **UpgradeFabric**: uruchamianie uaktualnień klastra                             
* **UnprovisionFabric**: Inicjowanie obsługi administracyjnej manifestu MSI i/lub klastra                         
* **MoveNextFabricUpgradeDomain**: wznawianie uaktualnień klastra z jawną domeną aktualizacji                             
* **ReportFabricUpgradeHealth**: wznawianie uaktualnień klastra z bieżącym postępem uaktualniania                             
* **StartInfrastructureTask**: Uruchamianie zadań infrastruktury                             
* **FinishInfrastructureTask**: Kończenie zadań infrastruktury                             
* **InvokeInfrastructureCommand**: polecenia zarządzania zadaniami infrastruktury                              
* **ActivateNode**: aktywowanie węzła                             
* **Operacja deactivatenode**: dezaktywowanie węzła                             
* **DeactivateNodesBatch**: dezaktywowanie wielu węzłów                             
* **RemoveNodeDeactivations**: wycofywanie dezaktywacji na wielu węzłach                             
* **GetNodeDeactivationStatus**: Sprawdzanie stanu dezaktywacji                             
* **Elementu nodestateremoved**: Usunięto stan węzła raportowania                             
* **ReportFault**: błąd raportowania                             
* **FileContent**: transfer plików klienta magazynu obrazów (zewnętrzny do klastra)                             
* **FileDownload**: inicjacja pobrania pliku klienta magazynu obrazu (zewnętrzna dla klastra)                             
* **InternalList**: operacja listy plików klienta magazynu obrazów (wewnętrzna)                             
* **Usuwanie**: operacja usunięcia klienta magazynu obrazu                              
* **Przekazywanie**: operacja przekazania klienta magazynu obrazu                             
* **NodeControl**: uruchamianie, zatrzymywanie i ponowne uruchamianie węzłów                             
* **MoveReplicaControl**: przeniesienie replik z jednego węzła do innego                             

### <a name="miscellaneous-operations"></a>Różne operacje
* **Ping**: polecenie ping klienta                             
* **Zapytanie**: wszystkie zapytania są dozwolone
* **NameExists**: sprawdzanie nazw identyfikatorów URI                             

Typ kontroli dostępu użytkownika jest domyślnie ograniczony do następujących operacji: 

* **EnumerateSubnames**: Wyliczenie identyfikatorów URI nazw                             
* **EnumerateProperties**: Wyliczanie właściwości nazw                             
* **PropertyReadBatch**: nadawanie nazw operacji odczytu właściwości                             
* **GetServiceDescription**: powiadomienia o usłudze Long-sondowania i odczytywanie opisów usługi                             
* **ResolveService**: Rozpoznawanie usługi opartej na skardze                             
* **ResolveNameOwner**: Rozpoznawanie właściciela identyfikatora URI nazwy                             
* **ResolvePartition**: Rozpoznawanie usług systemowych                             
* **Servicenotifications**: powiadomienia dotyczące usługi oparte na zdarzeniach                             
* **GetUpgradeStatus**: sondowanie stanu uaktualnienia aplikacji                             
* **GetFabricUpgradeStatus**: sondowanie stanu uaktualnienia klastra                             
* **InvokeInfrastructureQuery**: wykonywanie zapytań dotyczących zadań infrastruktury                             
* **Lista**: operacja listy plików klienta magazynu obrazów                             
* **ResetPartitionLoad**: Resetowanie obciążenia dla jednostki trybu failover                             
* **ToggleVerboseServicePlacementHealthReporting**: przełączanie pełnych raportów o kondycji umieszczania usług                             

Kontrola dostępu administratora ma również dostęp do poprzednich operacji.

## <a name="changing-default-settings-for-client-roles"></a>Zmiana ustawień domyślnych dla ról klienta
W pliku manifestu klastra w razie potrzeby można udostępnić klientom funkcje administracyjne. Ustawienia domyślne można zmienić, przechodząc do opcji **Ustawienia sieci szkieletowej** podczas [tworzenia klastra](service-fabric-cluster-creation-via-portal.md)i podając powyższe ustawienia w polach **Nazwa**, **administrator**, **użytkownik** i **wartość** .

## <a name="next-steps"></a>Następne kroki
[Service Fabric zabezpieczenia klastra](service-fabric-cluster-security.md)

[Service Fabric tworzenia klastra](service-fabric-cluster-creation-via-portal.md)

