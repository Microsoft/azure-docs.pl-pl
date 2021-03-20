---
title: 'Uaktualnianie aplikacji: parametry uaktualnienia'
description: Opisuje parametry związane z uaktualnianiem aplikacji Service Fabric, w tym kontrole kondycji do wykonania i zasady automatycznego cofania uaktualnienia.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 6b6116bf1188fcf191b2d672e6c698bb3c050e6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018481"
---
# <a name="application-upgrade-parameters"></a>Parametry uaktualniania aplikacji
W tym artykule opisano różne parametry, które są stosowane podczas uaktualniania aplikacji Service Fabric platformy Azure. Parametry uaktualniania aplikacji kontrolują limity czasu i kontroli kondycji, które są stosowane podczas uaktualniania i określają zasady, które należy zastosować w przypadku niepowodzenia uaktualnienia. Parametry aplikacji dotyczą uaktualnień przy użyciu:
- PowerShell
- Visual Studio
- SFCTL
- [REST](/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Uaktualnienia aplikacji są inicjowane za pośrednictwem jednego z trzech trybów uaktualniania wybieranych przez użytkownika. Każdy tryb ma swój własny zestaw parametrów aplikacji:
- Kontrolowana
- Niemonitorowane
- Niemonitorowany ręczny

Odpowiednie wymagane i opcjonalne parametry są opisane w każdej sekcji w następujący sposób.

## <a name="visual-studio-and-powershell-parameters"></a>Parametry programu Visual Studio i programu PowerShell

Service Fabric uaktualnień aplikacji przy użyciu programu PowerShell, użyj polecenia [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) . Tryb uaktualniania jest wybierany przez przekazanie parametru **monitorowane**, **UnmonitoredAuto** lub **UnmonitoredManual** do opcji [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Parametry uaktualniania aplikacji Service Fabric programu Visual Studio są ustawiane za pomocą okna dialogowego ustawień uaktualniania programu Visual Studio. Tryb uaktualniania programu Visual Studio jest wybierany za pomocą pola listy rozwijanej **tryb uaktualniania** do **monitorowania**, **UnmonitoredAuto** lub **UnmonitoredManual**. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uaktualnienia aplikacji Service Fabric w programie Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Wymagane parametry
(PS = PowerShell, VS = Visual Studio)

| Parametr | Dotyczy: | Opis |
| --- | --- | --- |
ApplicationName |PS| Nazwa aplikacji, która jest uaktualniana. Przykłady: Sieć szkieletowa:/VisualObjects, Sieć szkieletowa:/ClusterMonitor. |
ApplicationTypeVersion|PS|Wersja typu aplikacji, która jest przeznaczona do uaktualnienia. |
FailureAction |PS, VS|Dozwolone wartości to **wycofywanie**, **Ręczne** i **nieprawidłowe**. Akcja kompensowania do wykonania, gdy *monitorowane* uaktualnienie napotyka zasad monitorowania lub naruszeń zasad dotyczących kondycji. <br>**Wycofanie** oznacza, że uaktualnienie zostanie automatycznie przywrócone do wersji sprzed uaktualnienia. <br>**Ręczna** oznacza, że uaktualnienie zostanie przełączone do trybu uaktualnienia *UnmonitoredManual* . <br>**Nieprawidłowy** wskazuje, że akcja błędu jest nieprawidłowa.|
Kontrolowana |PS|Wskazuje, że jest monitorowany tryb uaktualniania. Gdy polecenie cmdlet zakończy uaktualnienie dla domeny uaktualnienia, a kondycja domeny uaktualnienia i klastra spełnia zdefiniowane przez Ciebie zasady kondycji, Service Fabric uaktualnia następną domenę uaktualnienia. Jeśli domena uaktualnienia lub klaster nie spełni zasad dotyczących kondycji, uaktualnienie nie powiedzie się, Service Fabric przywraca uaktualnienie dla domeny uaktualnienia lub przywraca tryb ręczny zgodnie z określonymi zasadami. Jest to zalecany tryb uaktualniania aplikacji w środowisku produkcyjnym. |
UpgradeMode | VS | Dozwolone wartości to **monitorowane** (ustawienie domyślne), **UnmonitoredAuto** lub **UnmonitoredManual**. Aby uzyskać szczegółowe informacje, zobacz parametry programu PowerShell dla każdego trybu w tym artykule. |
UnmonitoredAuto | PS | Wskazuje, że tryb uaktualniania jest niemonitorowany automatycznie. Po Service Fabric uaktualnia domenę uaktualnienia, Service Fabric uaktualnia następną domenę uaktualnienia niezależnie od stanu kondycji aplikacji. Ten tryb nie jest zalecany w środowisku produkcyjnym i jest przydatny tylko podczas opracowywania aplikacji. |
UnmonitoredManual | PS | Wskazuje, że tryb uaktualniania jest niemonitorowany. Po Service Fabric uaktualnia domenę uaktualnienia, czeka na uaktualnienie następnej domeny uaktualnienia przy użyciu polecenia cmdlet *Resume-ServiceFabricApplicationUpgrade* . |

### <a name="optional-parameters"></a>Parametry opcjonalne

Parametry oceny kondycji są opcjonalne. Jeśli nie zostaną określone kryteria oceny kondycji, gdy zostanie uruchomione uaktualnienie, Service Fabric korzysta z zasad dotyczących kondycji aplikacji określonych w ApplicationManifest.xml wystąpienia aplikacji.

> [!div class="mx-tdBreakAll"]
> | Parametr | Dotyczy: | Opis |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Określa zastąpienia parametrów aplikacji.<br>Parametry aplikacji programu PowerShell są określane jako pary nazwa/wartość obiektu Hashtable. Na przykład @ {"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>Parametry aplikacji programu Visual Studio można określić w oknie dialogowym Publikowanie Service Fabric aplikacji w polu **plik parametrów aplikacji** .
> | Confirm |PS| Dozwolone wartości to **true** i **false**. Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet. |
> | ConsiderWarningAsError |PS, VS |Dozwolone wartości to **true** i **false**. Wartość domyślna to **false**. Traktuj ostrzeżenia o zdarzeniach dotyczących kondycji aplikacji jako błędy podczas oceny kondycji aplikacji podczas uaktualniania. Domyślnie Service Fabric nie oblicza zdarzeń kondycji ostrzeżenia w przypadku błędów (błędów), dzięki czemu uaktualnienie może być kontynuowane, nawet jeśli istnieją zdarzenia ostrzegawcze. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Określa zasady kondycji dla domyślnego typu usługi, który ma być używany na potrzeby monitorowanego uaktualnienia w formacie MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Na przykład 5, 10, 15 wskazuje następujące wartości: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Dozwolone wartości to **true** i **false**. Wskazuje, że proces uaktualniania pomija komunikat ostrzegawczy i wymusza uaktualnienie nawet wtedy, gdy numer wersji nie został zmieniony. Jest to przydatne w przypadku testowania lokalnego, ale nie jest to zalecane do użycia w środowisku produkcyjnym, ponieważ wymaga usunięcia istniejącego wdrożenia, które powoduje zmniejszenie i utratę danych. |
> | ForceRestart |PS, VS |Jeśli zaktualizujesz konfigurację lub pakiet danych bez aktualizowania kodu usługi, usługa zostanie uruchomiona ponownie tylko wtedy, gdy właściwość ForceRestart ma **wartość true**. Po zakończeniu aktualizacji Service Fabric powiadamia usługę, że jest dostępny nowy pakiet konfiguracji lub pakiet danych. Usługa jest odpowiedzialna za zastosowanie zmian. W razie potrzeby usługa może się ponownie uruchomić. |
> | HealthCheckRetryTimeoutSec |PS, VS |Czas (w sekundach), który Service Fabric nadal przeprowadzał ocenę kondycji, przed zadeklarowaniem uaktualnienia jako zakończonym niepowodzeniem. Wartość domyślna to 600 sekund. Ten czas trwania rozpoczyna się po osiągnięciu *HealthCheckWaitDurationSec* . W tym *HealthCheckRetryTimeout* Service Fabric mogą wykonywać wiele sprawdzeń kondycji aplikacji. Wartość domyślna to 10 minut i powinna być odpowiednio dostosowana dla aplikacji. |
> | HealthCheckStableDurationSec |PS, VS |Czas trwania (w sekundach), aby sprawdzić, czy aplikacja jest stabilna przed przejściem do następnej domeny uaktualnienia lub zakończeniu uaktualniania. Ten czas oczekiwania jest używany do zapobiegania niewykrywalnym zmianom kondycji bezpośrednio po przeprowadzeniu kontroli kondycji. Wartość domyślna to 120 sekund i powinna być odpowiednio dostosowana dla aplikacji. |
> | HealthCheckWaitDurationSec |PS, VS | Czas oczekiwania (w sekundach) po zakończeniu uaktualniania w domenie uaktualnienia przed Service Fabric oceni kondycję aplikacji. Ten czas trwania może być również traktowany jako czas, w którym aplikacja powinna działać, zanim będzie mogła zostać uznana za w dobrej kondycji. Jeśli sprawdzanie kondycji zakończy się, proces uaktualniania przechodzi do następnej domeny uaktualnienia.  Jeśli sprawdzanie kondycji zakończy się niepowodzeniem, Service Fabric czeka na [UpgradeHealthCheckInterval](./service-fabric-cluster-fabric-settings.md#clustermanager) przed ponowną próbą sprawdzenia kondycji, dopóki nie zostanie osiągnięty *HealthCheckRetryTimeoutSec* . Wartość domyślna i zalecana to 0 sekund. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |Wartość domyślna i zalecana to 0. Określ maksymalną liczbę wdrożonych aplikacji (zobacz [sekcję kondycja](service-fabric-health-introduction.md)), która może być w złej kondycji, zanim aplikacja zostanie uznana za niezdrowe i uaktualnienie zakończy się niepowodzeniem. Ten parametr definiuje kondycję aplikacji w węźle i pomaga wykrywać problemy podczas uaktualniania. Zazwyczaj repliki aplikacji mają zrównoważone obciążenie z innym węzłem, dzięki czemu aplikacja może być wyświetlana w dobrej kondycji, co pozwala na przeprowadzenie uaktualnienia. Określając ścisłą kondycję *MaxPercentUnhealthyDeployedApplications* , Service Fabric może szybko wykryć problem z pakietem aplikacji i pomóc w szybkim uaktualnieniu. |
> | MaxPercentUnhealthyServices |PS, VS |Parametr do *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Wartość domyślna i zalecana to 0. Określ maksymalną liczbę usług w wystąpieniu aplikacji, która może być zła w złej kondycji, zanim aplikacja zostanie uznana za niezdrowe i uaktualnienie zakończy się niepowodzeniem. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Parametr do *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Wartość domyślna i zalecana to 0. Określ maksymalną liczbę partycji w usłudze, które mogą być w złej kondycji, zanim usługa zostanie uznana za złą. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Parametr do *DefaultServiceTypeHealthPolicy* i *ServiceTypeHealthPolicyMap*. Wartość domyślna i zalecana to 0. Określ maksymalną liczbę replik w partycji, które mogą być w złej kondycji, zanim partycja zostanie uznana za złą. |
> | ServiceTypeHealthPolicyMap | PS, VS | Reprezentuje zasady kondycji używane do oszacowania kondycji usług należących do typu usługi. Pobiera dane wejściowe tabeli skrótów w następującym formacie: @ {"ServiceTypeName": "MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices"} na przykład: @ {"ServiceTypeName01" = "5, 10, 5"; "ServiceTypeName02" = "5, 5, 5"} |
> | TimeoutSec | PS, VS | Określa limit czasu (w sekundach) dla operacji. |
> | UpgradeDomainTimeoutSec |PS, VS |Maksymalny czas (w sekundach) uaktualniania pojedynczej domeny uaktualnienia. Jeśli limit czasu zostanie osiągnięty, uaktualnienie zostanie zatrzymane i będzie działać na podstawie ustawienia dla *FailureAction*. Wartość domyślna to nigdy (nieskończoność) i powinna być odpowiednio dostosowana dla aplikacji. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Mierzony w sekundach.<br>**Usługa bezstanowa**— w ramach jednej domeny uaktualnienia Service Fabric podejmuje próbę zapewnienia dostępności dodatkowych wystąpień usługi. Jeśli docelowa liczba wystąpień ma więcej niż jeden, Service Fabric czeka na dostępność więcej niż jednego wystąpienia, do maksymalnej wartości limitu czasu. Ten limit czasu jest określany za pomocą właściwości *UpgradeReplicaSetCheckTimeoutSec* . Jeśli limit czasu wygaśnie, Service Fabric kontynuuje uaktualnianie, niezależnie od liczby wystąpień usługi. Jeśli liczba wystąpień docelowych jest taka, Service Fabric nie czeka i natychmiast kontynuuje proces uaktualniania.<br><br>**Usługa stanowa**— w ramach jednej domeny uaktualnienia Service Fabric próbuje upewnić się, że zestaw replik ma kworum. Service Fabric czeka na dostępność kworum, do maksymalnej wartości limitu czasu (określonej przez właściwość *UpgradeReplicaSetCheckTimeoutSec* ). Jeśli limit czasu wygaśnie, Service Fabric kontynuuje uaktualnianie, niezależnie od kworum. To ustawienie jest ustawione jako nigdy (nieograniczone) podczas przewracania do przodu i 1200 sekund podczas wycofywania. |
> | UpgradeTimeoutSec |PS, VS |Limit czasu (w sekundach) dotyczący całego uaktualnienia. Po osiągnięciu limitu czasu uaktualnienie zostaje zatrzymane i *FailureAction* zostanie wyzwolone. Wartość domyślna to nigdy (nieskończoność) i powinna być odpowiednio dostosowana dla aplikacji. |
> | Instrukcja WhatIf | PS | Dozwolone wartości to **true** i **false**. Pokazuje, co się stanie po uruchomieniu polecenia cmdlet. Polecenie cmdlet nie zostało uruchomione. |

Kryteria *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* i *MaxPercentUnhealthyReplicasPerPartition* można określić dla typu usługi dla wystąpienia aplikacji. Ustawienie tych parametrów dla poszczególnych usług pozwala aplikacji zawierać różne typy usług z różnymi zasadami oceny. Na przykład typ usługi bramy bezstanowej może mieć *MaxPercentUnhealthyPartitionsPerService* , który jest inny niż typ usługi silnika stanowego dla konkretnego wystąpienia aplikacji.

## <a name="sfctl-parameters"></a>Parametry SFCTL

Service Fabric uaktualnień aplikacji przy użyciu interfejsu wiersza polecenia Service Fabric użycie [sfctl uaktualniania aplikacji](./service-fabric-sfctl-application.md#sfctl-application-upgrade) wraz z następującymi wymaganymi i opcjonalnymi parametrami.

### <a name="required-parameters"></a>Wymagane parametry

| Parametr | Opis |
| --- | --- |
| Identyfikator aplikacji  |Identyfikator aplikacji, która jest uaktualniana. <br> Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa:". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa aplikacji to "Fabric:/MojaApl/APP1", tożsamość aplikacji powinna mieć wartość "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach.|
Aplikacja — wersja |Wersja typu aplikacji, która jest przeznaczona do uaktualnienia.|
parameters  |Zakodowana w formacie JSON lista przesłonięć parametrów aplikacji do zastosowania podczas uaktualniania aplikacji.|

### <a name="optional-parameters"></a>Parametry opcjonalne

| Parametr | Opis |
| --- | --- |
domyślne-Service-Health-Policy | Zakodowana w formacie [JSON](/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) Specyfikacja zasad kondycji używana domyślnie do oszacowania kondycji typu usługi. Mapa jest domyślnie pusta. |
Niepowodzenie — akcja | Dozwolone wartości to **wycofywanie**, **Ręczne** i **nieprawidłowe**. Akcja kompensowania do wykonania, gdy *monitorowane* uaktualnienie napotyka zasad monitorowania lub naruszeń zasad dotyczących kondycji. <br>**Wycofanie** oznacza, że uaktualnienie zostanie automatycznie przywrócone do wersji sprzed uaktualnienia. <br>**Ręczna** oznacza, że uaktualnienie zostanie przełączone do trybu uaktualnienia *UnmonitoredManual* . <br>**Nieprawidłowy** wskazuje, że akcja błędu jest nieprawidłowa.|
Wymuś ponowne uruchomienie | Jeśli zaktualizujesz konfigurację lub pakiet danych bez aktualizowania kodu usługi, usługa zostanie uruchomiona ponownie tylko wtedy, gdy właściwość ForceRestart ma **wartość true**. Po zakończeniu aktualizacji Service Fabric powiadamia usługę, że jest dostępny nowy pakiet konfiguracji lub pakiet danych. Usługa jest odpowiedzialna za zastosowanie zmian. W razie potrzeby usługa może się ponownie uruchomić. |
Kondycja — Sprawdź ponowienie — limit czasu | Czas, przez który należy ponowić próbę oceny kondycji, gdy aplikacja lub klaster jest w złej kondycji przed wykonaniem *FailureAction* . Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. Wartość domyślna: PT0H10M0S. |
Kondycja — sprawdzanie stabilne — czas trwania | Ilość czasu, przez jaką aplikacja lub klaster muszą pozostawać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. Wartość domyślna: PT0H2M0S. |
Kondycja — sprawdzanie — czas trwania | Czas oczekiwania po zakończeniu domeny uaktualnienia przed zastosowaniem zasad dotyczących kondycji. Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. Wartość domyślna: 0.|
maks. — zła kondycja — aplikacje | Wartość domyślna i zalecana to 0. Określ maksymalną liczbę wdrożonych aplikacji (zobacz [sekcję kondycja](service-fabric-health-introduction.md)), która może być w złej kondycji, zanim aplikacja zostanie uznana za niezdrowe i uaktualnienie zakończy się niepowodzeniem. Ten parametr definiuje kondycję aplikacji w węźle i pomaga wykrywać problemy podczas uaktualniania. Zazwyczaj repliki aplikacji mają zrównoważone obciążenie z innym węzłem, dzięki czemu aplikacja może być wyświetlana w dobrej kondycji, co pozwala na przeprowadzenie uaktualnienia. Określając ścisłą kondycję z *maksymalną kondycją aplikacji* , Service Fabric może szybko wykryć problem z pakietem aplikacji i pomóc w szybkim wykorzystaniu szybkiego uaktualnienia. Reprezentowane jako liczba z zakresu od 0 do 100. |
tryb | Dozwolone wartości to **monitorowanie**, **upgrademode**, **UnmonitoredAuto**, **UnmonitoredManual**. Wartość domyślna to **UnmonitoredAuto**. Opisy tych wartości można znaleźć w sekcji Visual Studio i PowerShell *Required Parameters* .|
Replika — limit czasu sprawdzania |Mierzony w sekundach. <br>**Usługa bezstanowa**— w ramach jednej domeny uaktualnienia Service Fabric podejmuje próbę zapewnienia dostępności dodatkowych wystąpień usługi. Jeśli docelowa liczba wystąpień ma więcej niż jeden, Service Fabric czeka na dostępność więcej niż jednego wystąpienia, do maksymalnej wartości limitu czasu. Ten limit czasu jest określany za pomocą właściwości *Replica-Set-Check-timeout* . Jeśli limit czasu wygaśnie, Service Fabric kontynuuje uaktualnianie, niezależnie od liczby wystąpień usługi. Jeśli liczba wystąpień docelowych jest taka, Service Fabric nie czeka i natychmiast kontynuuje proces uaktualniania.<br><br>**Usługa stanowa**— w ramach jednej domeny uaktualnienia Service Fabric próbuje upewnić się, że zestaw replik ma kworum. Service Fabric czeka na udostępnienie kworum, do maksymalnej wartości limitu czasu (określonej przez właściwość *Replica-Set-Check-timeout* ). Jeśli limit czasu wygaśnie, Service Fabric kontynuuje uaktualnianie, niezależnie od kworum. To ustawienie jest ustawione jako nigdy (nieograniczone) podczas przewracania do przodu i 1200 sekund podczas wycofywania. |
Usługa-kondycja — zasady | Zakodowana w notacji JSON zasada kondycji typu usługi dla nazwy typu usługi. Mapa jest pusta. [Format JSON parametru.](/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). Plik JSON dla części "value" zawiera **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService** i **MaxPercentUnhealthyReplicasPerPartition**. Opisy tych parametrów można znaleźć w sekcji opcjonalne parametry programu Visual Studio i programu PowerShell.
timeout | Określa limit czasu (w sekundach) dla operacji. Wartość domyślna: 60. |
Upgrade-Domain-timeout | Czas, przez jaki każda domena uaktualnienia musi zakończyć pracę przed wykonaniem *FailureAction* . Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. Wartość domyślna to nigdy (nieskończoność) i powinna być odpowiednio dostosowana dla aplikacji. Wartość domyślna: P10675199DT02H48M 05.4775807 S. |
uaktualnienie — limit czasu | Czas, przez jaki każda domena uaktualnienia musi zakończyć pracę przed wykonaniem *FailureAction* . Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. Wartość domyślna to nigdy (nieskończoność) i powinna być odpowiednio dostosowana dla aplikacji. Wartość domyślna: P10675199DT02H48M 05.4775807 S.|
Ostrzeżenie jako błąd | Dozwolone wartości to **true** i **false**. Wartość domyślna to **false**. Można przesłać jako flagę. Traktuj ostrzeżenia o zdarzeniach dotyczących kondycji aplikacji jako błędy podczas oceny kondycji aplikacji podczas uaktualniania. Domyślnie Service Fabric nie oblicza zdarzeń kondycji ostrzeżenia w przypadku błędów (błędów), dzięki czemu uaktualnienie może być kontynuowane, nawet jeśli istnieją zdarzenia ostrzegawcze. |

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu PowerShell.

[Uaktualnianie aplikacji przy użyciu interfejsu wiersza polecenia Service Fabric w systemie Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu interfejsu wiersza polecenia Service Fabric.

[Uaktualnianie aplikacji przy użyciu wtyczki Service Fabric zaćmienie](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Aby uaktualnić aplikacje, należy się upewnić, jak używać [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [zaawansowanych tematów](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów dotyczących uaktualnień aplikacji, w odniesieniu do kroków w [temacie Troubleshooting Upgrades Applications](service-fabric-application-upgrade-troubleshooting.md).
