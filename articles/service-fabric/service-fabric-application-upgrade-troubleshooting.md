---
title: Rozwiązywanie problemów z uaktualnieniami aplikacji
description: W tym artykule opisano niektóre typowe problemy związane z uaktualnianiem Service Fabric aplikacji i sposobami ich rozwiązywania.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009351"
---
# <a name="troubleshoot-application-upgrades"></a>Rozwiązywanie problemów z uaktualnieniami aplikacji

W tym artykule omówiono niektóre typowe problemy związane z uaktualnianiem aplikacji Service Fabric platformy Azure i sposobami ich rozwiązywania.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Rozwiązywanie problemów z uaktualnieniem aplikacji zakończonych niepowodzeniem

Gdy uaktualnienie nie powiedzie się, dane wyjściowe polecenia **Get-ServiceFabricApplicationUpgrade** zawierają dodatkowe informacje dotyczące debugowania błędu.  Poniższa lista określa, jak można używać dodatkowych informacji:

1. Zidentyfikuj typ błędu.
2. Zidentyfikuj przyczynę niepowodzenia.
3. Izolowanie co najmniej jednego składnika niemożliwego do dalszej analizy.

Te informacje są dostępne, gdy Service Fabric wykryje awarię bez względu na to, czy **FailureAction** ma wycofać lub wstrzymać uaktualnienie.

### <a name="identify-the-failure-type"></a>Zidentyfikuj typ błędu

W danych wyjściowych polecenia **Get-ServiceFabricApplicationUpgrade** **FailureTimestampUtc** identyfikuje sygnaturę czasową (w formacie UTC), w której wykryto Niepowodzenie uaktualnienia Service Fabric a **FailureAction** zostało wyzwolone. **Failurereason** identyfikuje jedną z trzech potencjalnych przyczyn niepowodzenia:

1. UpgradeDomainTimeout — wskazuje, że określona domena uaktualnienia trwała zbyt długo i **UpgradeDomainTimeout** wygasła.
2. OverallUpgradeTimeout — wskazuje, że całkowite uaktualnienie trwało zbyt długo i **UpgradeTimeout** wygasło.
3. HealthCheck — wskazuje, że po uaktualnieniu domeny aktualizacji aplikacja pozostawała w złej kondycji zgodnie z określonymi zasadami kondycji i **HealthCheckRetryTimeout** wygasła.

Te wpisy są wyświetlane tylko w danych wyjściowych, gdy uaktualnienie nie powiedzie się i rozpocznie wycofywanie. Dalsze informacje są wyświetlane w zależności od typu błędu.

### <a name="investigate-upgrade-timeouts"></a>Zbadaj limity czasu uaktualniania

Błędy przekroczenia limitu czasu uaktualniania są najczęściej spowodowane przez problemy z dostępnością usług. Dane wyjściowe poniższego akapitu są typowymi uaktualnieniami, w których nie można uruchomić replik usług lub wystąpień w nowej wersji kodu. Pole **UpgradeDomainProgressAtFailure** przechwytuje migawkę oczekujących uaktualnień w momencie wystąpienia błędu.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

W tym przykładzie uaktualnienie nie powiodło się po uaktualnieniu domeny *MYUD1* i dwie partycje (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* i *4b43f4d8-b26b-424e-9307-7a7a62e79750*) zostały zablokowane. Partycje zostały zablokowane, ponieważ środowisko uruchomieniowe nie mogło umieścić replik podstawowych (*WaitForPrimaryPlacement*) na węzłach docelowych *Węzeł1* i *Węzeł4*.

Polecenia **Get-ServiceFabricNode** można użyć do sprawdzenia, czy te dwa węzły znajdują się w uaktualnieniu domeny *MYUD1*. *UpgradePhase* mówi *PostUpgradeSafetyCheck*, co oznacza, że te kontrole bezpieczeństwa są wykonywane po zakończeniu uaktualniania wszystkich węzłów w domenie uaktualnienia. Wszystkie te informacje wskazują potencjalny problem z nową wersją kodu aplikacji. Najczęstsze problemy to błędy usługi w ramach otwartych lub podwyższania poziomu podstawowych ścieżek kodu.

*UpgradePhase* *PreUpgradeSafetyCheck* oznacza, że wystąpiły problemy podczas przygotowywania domeny uaktualnienia przed wykonaniem. Najczęstszymi problemami w tym przypadku są błędy usługi w pobliżu i obniżanie poziomu podstawowych ścieżek kodu.

Bieżącym **UpgradeStateem** jest *RollingBackCompleted*, dlatego oryginalne uaktualnienie musi zostać wykonane przy użyciu **FailureAction** wycofywania, które jest automatycznie wycofywane po awarii. Jeśli pierwotne uaktualnienie zostało wykonane z użyciem ręcznego **FailureAction**, uaktualnienie będzie miało miejsce w stanie wstrzymania, aby umożliwić debugowanie na żywo aplikacji.

W rzadkich przypadkach pole **UpgradeDomainProgressAtFailure** może być puste, jeśli ogólne uaktualnienie kończy się w przypadku zakończenia całego działania systemu dla bieżącej domeny uaktualnienia. W takim przypadku spróbuj zwiększyć wartości parametrów uaktualnienia **UpgradeTimeout** i **UpgradeDomainTimeout** i ponów próbę uaktualnienia.

### <a name="investigate-health-check-failures"></a>Zbadaj błędy sprawdzania kondycji

Błędy sprawdzania kondycji mogą być wyzwalane przez różne problemy, które mogą wystąpić po zakończeniu uaktualniania wszystkich węzłów w domenie uaktualnienia i przekazaniu wszystkich kontroli bezpieczeństwa. Dane wyjściowe poniższego akapitu są typowymi błędami uaktualniania z powodu niepowodzeń kontroli kondycji. Pole **UnhealthyEvaluations** przechwytuje migawkę kontroli kondycji, które zakończyły się niepowodzeniem w czasie uaktualniania zgodnie z określonymi [zasadami kondycji](service-fabric-health-introduction.md).

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Badanie błędów sprawdzania kondycji najpierw wymaga poznania modelu kondycji Service Fabric. Jednak nawet bez dokładnej wiedzy można zobaczyć, że dwie usługi są w złej kondycji: *Sieć szkieletowa:/DemoApp/Svc3* i *Sieć szkieletowa:/DemoApp/Svc2*, wraz z raportami kondycji błędów ("InjectedFault" w tym przypadku). W tym przykładzie dwa z czterech usług są w złej kondycji, które są poniżej domyślnego obiektu docelowego o wartości 0% w złej kondycji (*MaxPercentUnhealthyServices*).

Uaktualnienie zostało wstrzymane w przypadku niepowodzenia, określając **FailureAction** ręcznie podczas uruchamiania uaktualnienia. Ten tryb umożliwia zbadanie systemu na żywo w stanie niepowodzenia przed podjęciem dalszych działań.

### <a name="recover-from-a-suspended-upgrade"></a>Odzyskaj po wstrzymanym uaktualnieniu

W przypadku wycofania **FailureAction** nie jest wymagana odzyskanie, ponieważ uaktualnienie jest automatycznie wycofywane po awarii. W przypadku ręcznego **FailureAction** istnieje kilka opcji odzyskiwania:

1.  Wyzwalanie wycofywania
2. Wykonaj pozostałą część uaktualnienia ręcznie
3. Wznów monitorowane uaktualnienie

Polecenie **Start-ServiceFabricApplicationRollback** może być używane w dowolnym momencie do uruchomienia wycofywania aplikacji. Gdy polecenie zwróci się pomyślnie, żądanie wycofania zostało zarejestrowane w systemie i rozpocznie się wkrótce.

Polecenie **Resume-ServiceFabricApplicationUpgrade** może służyć do przechodzenia przez pozostałą część uaktualnienia ręcznie, jednej domeny uaktualnienia w danym momencie. W tym trybie tylko kontrole bezpieczeństwa są wykonywane przez system. Nie przeprowadzono więcej kontroli kondycji. Tego polecenia można używać tylko wtedy, gdy *UpgradeState* pokazuje *RollingForwardPending*, co oznacza, że bieżąca domena uaktualnienia zakończyła się uaktualnieniem, ale Następna nie została uruchomiona (oczekująca).

Polecenie **Update-ServiceFabricApplicationUpgrade** może służyć do wznowienia monitorowanego uaktualnienia z wykonywanymi kontrolami bezpieczeństwa i kondycji.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Uaktualnienie jest kontynuowane z domeny uaktualnienia, w której został on ostatnio wstrzymany, i Użyj tych samych parametrów uaktualnienia i zasad dotyczących kondycji co poprzednio. W razie konieczności wszystkie parametry uaktualnienia i zasady dotyczące kondycji pokazane w powyższych danych wyjściowych można zmienić w tym samym poleceniu po wznowieniu uaktualniania. W tym przykładzie uaktualnienie zostało wznowione w trybie monitorowania z parametrami i zasadami kondycji, które nie uległy zmianie.

## <a name="further-troubleshooting"></a>Dalsze Rozwiązywanie problemów

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric nie jest zgodna z określonymi zasadami kondycji

Możliwa przyczyna 1:

Service Fabric tłumaczy wszystkie wartości procentowe na rzeczywistą liczbę jednostek (na przykład repliki, partycje i usługi) na potrzeby oceny kondycji i zawsze zaokrągla do całych jednostek. Jeśli na przykład maksymalna *MaxPercentUnhealthyReplicasPerPartition* wynosi 21%, a istnieje pięć replik, Service Fabric może mieć do dwóch replik w złej kondycji (to oznacza, `Math.Ceiling (5*0.21)` ). W związku z tym należy odpowiednio ustawić zasady kondycji.

Możliwa przyczyna 2:

Zasady dotyczące kondycji są określane w postaci wartości procentowej łącznej liczby usług i nie określonych wystąpień usługi. Na przykład przed uaktualnieniem, jeśli aplikacja ma cztery wystąpienia usługi A, B, C i D, gdzie usługa D jest w złej kondycji, ale ma niewielki wpływ na aplikację. Podczas uaktualniania chcemy zignorować znaną usługę w złej kondycji i ustawić parametr *MaxPercentUnhealthyServices* na 25%, przy założeniu, że tylko a, B i C muszą być w dobrej kondycji.

Jednak podczas uaktualniania D może stać się w dobrej kondycji, a C staje się zła. Uaktualnienie nadal powiedzie się, ponieważ tylko 25% usług jest w złej kondycji. Jednak może to spowodować nieoczekiwane błędy z powodu nieprawidłowej kondycji C, a nie D. W takiej sytuacji D należy modelować jako inny typ usługi z, B i C. Ponieważ zasady dotyczące kondycji są określone dla każdego typu usługi, do różnych usług można zastosować różne progi procentu w złej kondycji. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Nie określono zasad dotyczących kondycji na potrzeby uaktualniania aplikacji, ale uaktualnienie nadal kończy się niepowodzeniem przez niektóre przekroczenia limitu czasu, które nigdy nie zostały określone

Gdy zasady dotyczące kondycji nie są dostarczane do żądania uaktualnienia, są pobierane z *ApplicationManifest.xml* bieżącej wersji aplikacji. Na przykład Jeśli uaktualniasz aplikację X z wersji 1,0 do wersji 2,0, używane są zasady dotyczące kondycji aplikacji określone dla programu w wersji 1,0. Jeśli dla uaktualnienia należy zastosować różne zasady dotyczące kondycji, należy określić zasady w ramach wywołania interfejsu API uaktualniania aplikacji. Zasady określone jako część wywołania interfejsu API są stosowane tylko podczas uaktualniania. Po zakończeniu uaktualniania zostaną użyte zasady określone w *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Określono nieprawidłowe limity czasu

Być może zastanawiasz się, co się stanie, gdy limity czasu są ustawione niespójnie. Na przykład może być *UpgradeTimeout* , który jest mniejszy niż *UpgradeDomainTimeout*. Odpowiedź polega na tym, że zwracany jest błąd. Błędy są zwracane, jeśli wartość *UpgradeDomainTimeout* jest mniejsza niż suma wartości *HealthCheckWaitDuration* i *HealthCheckRetryTimeout*, lub jeśli *UpgradeDomainTimeout* jest mniejsza niż suma wartości *HealthCheckWaitDuration* i *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Moje uaktualnienia zajmują zbyt dużo czasu

Czas na zakończenie uaktualniania zależy od sprawdzania kondycji i określonych limitów czasu. Sprawdzanie kondycji i limity czasu są zależne od tego, jak długo trwa kopiowanie, wdrażanie i stabilizację aplikacji. Zbyt agresywne w przypadku przekroczenia limitu czasu może oznaczać więcej nieudanych uaktualnień, dlatego zalecamy rozpoczęcie bardziej ostrożnie z dłuższymi przekroczeniem limitu czasu.

Poniżej znajduje się szybki odświeżacz, w jaki sposób czas pracy z czasem uaktualniania zależy od czasu.

Uaktualnienia dla domeny uaktualnienia nie mogą zakończyć się krócej niż *HealthCheckWaitDuration*  +  *HealthCheckStableDuration*.

Niepowodzenie uaktualniania nie może wystąpić szybciej niż *HealthCheckWaitDuration*  +  *HealthCheckRetryTimeout*.

Czas uaktualnienia dla domeny uaktualnienia jest ograniczony przez *UpgradeDomainTimeout*.  Jeśli *HealthCheckRetryTimeout* i *HealthCheckStableDuration* są zarówno niezerowe, jak i kondycja aplikacji nadal przełączają się z powrotem i do przodu, uaktualnienie jest ostatecznie przemnożone na *UpgradeDomainTimeout*. *UpgradeDomainTimeout* rozpoczyna zliczanie w dół po rozpoczęciu uaktualniania bieżącej domeny uaktualnienia.

## <a name="next-steps"></a>Następne kroki

[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Aby uaktualnić aplikacje, należy się upewnić, jak używać [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [zaawansowanych tematów](service-fabric-application-upgrade-advanced.md).