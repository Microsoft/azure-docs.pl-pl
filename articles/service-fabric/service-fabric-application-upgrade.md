---
title: Service Fabric uaktualniania aplikacji
description: Ten artykuł zawiera wprowadzenie do uaktualniania aplikacji Service Fabric, w tym wybierania trybów uaktualniania i przeprowadzania kontroli kondycji.
ms.topic: conceptual
ms.date: 8/5/2020
ms.openlocfilehash: f3fad8d0ede92004706d9a1f4e14353715361b63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98792018"
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric uaktualniania aplikacji
Aplikacja Service Fabric platformy Azure to zbiór usług. Podczas uaktualniania Service Fabric porównuje nowy [manifest aplikacji](service-fabric-application-and-service-manifests.md) z poprzednią wersją i określa, które usługi w aplikacji wymagają aktualizacji. Service Fabric porównuje numery wersji w manifestach usługi z numerami wersji w poprzedniej wersji. Jeśli usługa nie uległa zmianie, ta usługa nie zostanie uaktualniona.

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s nie są zachowywane podczas uaktualniania aplikacji. Aby zachować bieżące parametry aplikacji, użytkownik powinien najpierw pobrać parametry i przekazać je do wywołania interfejsu API uaktualniania, takiego jak poniżej:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="rolling-upgrades-overview"></a>Przegląd stopniowego uaktualniania
W przypadku stopniowego uaktualniania aplikacji uaktualnienie jest wykonywane w etapach. Na każdym etapie uaktualnienie jest stosowane do podzbioru węzłów w klastrze, zwanego domeną aktualizacji. W związku z tym aplikacja pozostaje dostępna podczas uaktualniania. Podczas uaktualniania klaster może zawierać kombinację starych i nowych wersji.

Z tego powodu te dwie wersje muszą mieć wartość Prześlij dalej i zgodność z poprzednimi wersjami. Jeśli nie są zgodne, administrator aplikacji jest odpowiedzialny za przygotowanie uaktualnienia wielofazowego w celu utrzymania dostępności. W przypadku uaktualniania z wieloma fazami pierwszy krok jest uaktualniany do pośredniej wersji aplikacji, która jest zgodna z poprzednią wersją. Drugim krokiem jest uaktualnienie końcowej wersji, która przerywa zgodność z wersją sprzed aktualizacji, ale jest zgodna z wersją pośrednią.

Domeny aktualizacji są określone w manifeście klastra podczas konfigurowania klastra. Domeny aktualizacji nie odbierają aktualizacji w określonej kolejności. Domena aktualizacji jest jednostką logiczną wdrożenia aplikacji. Aktualizowanie domen pozwala usługom zachować wysoką dostępność podczas uaktualniania.

Uaktualnienia inne niż stopniowe są możliwe, Jeśli uaktualnienie jest stosowane do wszystkich węzłów w klastrze, w przypadku gdy aplikacja ma tylko jedną domenę aktualizacji. Ta metoda nie jest zalecana, ponieważ usługa nie działa i jest niedostępna w momencie uaktualnienia. Ponadto platforma Azure nie zapewnia żadnych gwarancji, gdy klaster jest skonfigurowany tylko do jednej domeny aktualizacji.

Po zakończeniu uaktualniania wszystkie usługi i repliki (wystąpienia) byłyby w tej samej wersji — i. e. Jeśli uaktualnienie zakończy się pomyślnie, zostaną zaktualizowane do nowej wersji; Jeśli uaktualnienie nie powiedzie się i zostanie wycofane, zostałyby przywrócone do starej wersji.

## <a name="health-checks-during-upgrades"></a>Kontrole kondycji podczas uaktualnień
W przypadku uaktualnienia należy ustawić zasady kondycji (lub użyć wartości domyślnych). Uaktualnienie jest uznawane za pomyślne, gdy wszystkie domeny aktualizacji zostaną uaktualnione w określonym przedziale czasu, a wszystkie domeny aktualizacji są uważane za w dobrej kondycji.  Domena aktualizacji w dobrej kondycji oznacza, że domena aktualizacji przekazała wszystkie kontrole kondycji określone w zasadach dotyczących kondycji. Na przykład zasady dotyczące kondycji mogą spowodować, że wszystkie usługi w ramach wystąpienia aplikacji muszą mieć *dobrą kondycję*, ponieważ kondycja jest definiowana przez Service Fabric.

Zasady i kontrole kondycji podczas uaktualniania Service Fabric to usługi i aplikacje niezależny od. Oznacza to, że nie są wykonywane żadne testy specyficzne dla usługi.  Na przykład usługa może mieć wymaganą przepływność, ale Service Fabric nie ma informacji do sprawdzenia przepływności. Zapoznaj się z [artykułami dotyczącymi kondycji](service-fabric-health-introduction.md) , które są wykonywane. Sprawdzenia wykonywane podczas uaktualniania obejmują testy dotyczące tego, czy pakiet aplikacji został skopiowany prawidłowo, czy wystąpienie zostało uruchomione i tak dalej.

Kondycja aplikacji jest agregacją jednostek podrzędnych aplikacji. W skrócie Service Fabric szacuje kondycję aplikacji za pomocą kondycji raportowanej w aplikacji. Sprawdza również kondycję wszystkich usług dla aplikacji w ten sposób. Service Fabric dalszej ocenie kondycji usług aplikacji przez agregowanie kondycji ich elementów podrzędnych, takich jak replika usługi. Po spełnieniu zasad dotyczących kondycji aplikacji można przeprowadzić uaktualnienie. Jeśli zasady kondycji zostaną naruszone, uaktualnienie aplikacji zakończy się niepowodzeniem.

## <a name="upgrade-modes"></a>Tryby uaktualniania
Tryb, który zalecamy w przypadku uaktualniania aplikacji jest trybem monitorowanym, który jest najczęściej używanym trybem. Tryb monitorowany wykonuje uaktualnienie w jednej domenie aktualizacji, a jeśli wszystkie testy kondycji są przekazywane (zgodnie z określonymi zasadami), automatycznie przechodzi do następnej domeny aktualizacji.  Jeśli sprawdzanie kondycji zakończy się niepowodzeniem i/lub przekroczenia limitu czasu, uaktualnienie jest wycofywane dla domeny aktualizacji lub tryb zostanie zmieniony na niemonitorowany. Uaktualnienie można skonfigurować tak, aby wybrać jeden z tych dwóch trybów dla nieudanych uaktualnień. 

Niemonitorowany tryb ręczny wymaga ręcznej interwencji po każdym uaktualnieniu w domenie aktualizacji, aby rozpocząć uaktualnianie do następnej domeny aktualizacji. Nie są wykonywane żadne kontrole kondycji Service Fabric. Administrator przeprowadza Sprawdzanie kondycji lub stanu przed rozpoczęciem uaktualniania w następnej domenie aktualizacji.

## <a name="upgrade-default-services"></a>Uaktualnij domyślne usługi
Niektóre domyślne parametry usługi zdefiniowane w [manifeście aplikacji](service-fabric-application-and-service-manifests.md) mogą być również uaktualniane w ramach uaktualnienia aplikacji. Tylko parametry usługi, które obsługują zmiany za pomocą [Update-ServiceFabricService](/powershell/module/servicefabric/update-servicefabricservice) , mogą zostać zmienione w ramach uaktualnienia. Zachowanie zmian domyślnych usług podczas uaktualniania aplikacji jest następujące:

1. Tworzone są domyślne usługi w manifeście nowej aplikacji, które nie istnieją jeszcze w klastrze.
2. Domyślne usługi, które znajdują się w poprzednich i nowych manifestach aplikacji, są aktualizowane. Parametry usługi domyślnej w nowym manifeście aplikacji zastępują parametry istniejącej usługi. Uaktualnienie aplikacji zostanie automatycznie wycofane, jeśli aktualizacja usługi domyślnej nie powiedzie się.
3. Usługi domyślne, które nie istnieją w nowym manifeście aplikacji, są usuwane, jeśli istnieją w klastrze. **Należy pamiętać, że usunięcie usługi domyślnej spowoduje usunięcie jej stanu i nie można jej cofnąć.**

Po wycofaniu uaktualnienia aplikacji domyślne parametry usługi są przywracane do starych wartości przed rozpoczęciem uaktualniania, ale nie można ponownie utworzyć usuniętych usług przy użyciu starego stanu.

> [!TIP]
> Ustawienie konfiguracji klastra [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) musi mieć *wartość true* , aby włączyć reguły 2) i 3) powyżej (domyślna aktualizacja i usuwanie usługi). Ta funkcja jest obsługiwana w Service Fabric wersja 5,5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Uaktualnianie wielu aplikacji za pomocą punktów końcowych HTTPS
Należy zachować ostrożność, aby nie używać tego **samego portu** dla różnych wystąpień tej samej aplikacji podczas korzystania z protokołu HTTP **S**. Przyczyną jest to, że Service Fabric nie będzie można uaktualnić certyfikatu dla jednego z wystąpień aplikacji. Na przykład jeśli aplikacja 1 lub aplikacja 2 chcą uaktualnić certyfikat 1 do certyfikatu 2. Po uaktualnieniu program Service Fabric mógł oczyścić rejestrację certyfikatu 1 z http.sys nawet wtedy, gdy nadal używa jej inna aplikacja. Aby temu zapobiec, Service Fabric wykryje, że na porcie jest już zarejestrowane inne wystąpienie aplikacji z certyfikatem (ze względu na http.sys) i operacja zakończy się niepowodzeniem.

Dlatego Service Fabric nie obsługuje uaktualniania dwóch różnych usług przy użyciu tego **samego portu** w różnych wystąpieniach aplikacji. Innymi słowy, nie można używać tego samego certyfikatu w różnych usługach na tym samym porcie. Jeśli musisz mieć współużytkowany certyfikat na tym samym porcie, musisz się upewnić, że usługi są umieszczone na różnych komputerach z ograniczeniami umieszczania. Lub Rozważ użycie Service Fabric portów dynamicznych, jeśli jest to możliwe dla każdej usługi w każdym wystąpieniu aplikacji. 

Jeśli zobaczysz, że uaktualnienie nie powiedzie się, zostanie wyświetlone ostrzeżenie informujące o błędzie "interfejs API serwera HTTP systemu Windows nie obsługuje wielu certyfikatów dla aplikacji, które współużytkują port".

## <a name="application-upgrade-flowchart"></a>Schemat blokowy uaktualniania aplikacji
Schemat blokowy następujący po tym akapicie może pomóc w zrozumieniu procesu uaktualniania aplikacji Service Fabric. W szczególności przepływ opisuje sposób, w jaki przekroczenia limitu czasu, w tym *HealthCheckStableDuration*, *HealthCheckRetryTimeout* i *UpgradeHealthCheckInterval*, kontrolują, kiedy uaktualnienie w jednej domenie aktualizacji jest uznawane za powodzenie lub awarię.

![Proces uaktualniania dla aplikacji Service Fabric][image]

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Aby uaktualnić aplikacje, należy się upewnić, jak używać [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [zaawansowanych tematów](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów dotyczących uaktualnień aplikacji, w odniesieniu do kroków w [temacie Troubleshooting Upgrades Applications](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
