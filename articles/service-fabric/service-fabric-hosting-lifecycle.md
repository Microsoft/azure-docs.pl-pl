---
title: Cykl życia aktywacji i dezaktywacji usługi Azure Service Fabric hosting
description: Dowiedz się więcej na temat cyklu życia aplikacji i pakietu servicepackage w węźle.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831826"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Cykl życia usługi Azure Service Fabric hosting

Ten artykuł zawiera omówienie zdarzeń, które pojawiają się w usłudze Azure Service Fabric w przypadku aktywowania aplikacji w węźle. Wyjaśniono różne konfiguracje klastra kontrolujące zachowanie.

Przed kontynuowaniem upewnij się, że rozumiesz koncepcje i relacje wyjaśnione w temacie [model a Application w Service Fabric][a1]. 

> [!NOTE]
> W tym artykule zastosowano pewną terminologię w specjalny sposób. O ile nie wskazano inaczej:
>
> - *Replika* odnosi się zarówno do repliki usługi stanowej, jak i wystąpienia usługi bezstanowej.
> - *CodePackage* jest traktowany jako równoważny z procesem ServiceHost, który rejestruje typ serviceType. Obsługuje ona repliki usług tego programu.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>Aktywuj ApplicationPackage lub servicepackage

Aby aktywować ApplicationPackage lub servicepackage:

1. Pobierz ApplicationPackage (na przykład *ApplicationManifest.xml*).
2. Skonfiguruj środowisko dla aplikacji. Kroki obejmują na przykład tworzenie użytkowników.
3. Rozpocznij śledzenie aplikacji do dezaktywacji.
4. Pobierz pakiet servicepackage (na przykład *ServiceManifest.xml*, kod, pliki konfiguracji i pakiety danych).
5. Skonfiguruj środowisko dla pakietu servicepackage. Kroki obejmują na przykład Konfigurowanie zapory i alokowanie portów dla punktów końcowych.
6. Rozpocznij śledzenie servicepackage do dezaktywacji.
7. Uruchom SetupEntryPoint dla CodePackages i poczekaj na jego zakończenie.
8. Uruchom MainEntryPoint dla CodePackages.

### <a name="servicetype-blocklisting"></a>Blocklisting ServiceType
`ServiceTypeDisableFailureThreshold` Określa liczbę dozwolonych niepowodzeń aktywacji, pobierania i CodePackage. Po osiągnięciu progu wartość ServiceType jest zaplanowana na blocklisting. Pierwszy błąd aktywacji, Niepowodzenie pobierania lub awaria CodePackage planuje blocklisting. 

`ServiceTypeDisableGraceInterval`Konfiguracja określa interwał prolongaty przed blocklisted na węźle. Ponieważ ten proces jest odtwarzany, aktywowanie, pobieranie i ponowne uruchamianie CodePackage są ponawiane równolegle. Ponawianie prób oznacza na przykład, że zaplanowano ponowne uruchomienie CodePackage po awarii lub Service Fabric spróbuje pobrać pakiety ponownie.

Gdy ServiceType jest blocklisted, zobaczysz błąd kondycji: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

Wartość ServiceType jest ponownie włączona w węźle, jeśli wystąpi dowolne z następujących zdarzeń:
- Aktywacja powiodła się. Lub jeśli to się nie powiedzie, osiągnie `ActivationMaxFailureCount` maksymalną liczbę ponownych prób.
- Pobieranie powiodło się. Lub jeśli to się nie powiedzie, osiągnie `DeploymentMaxFailureCount` maksymalną liczbę ponownych prób.
- Awaria CodePackage, która uległa awarii, i pomyślnie rejestruje serviceType.

`ActivationMaxFailureCount` i `DeploymentMaxFailureCount` to maksymalna liczba prób, które Service Fabric wykonać w celu aktywowania lub pobrania aplikacji w węźle. Po osiągnięciu maksymalnego poziomu, Service Fabric umożliwia ponowne aktywowanie elementu serviceType. 

Te progi zapewniają usłudze kolejną okazję do aktywacji. Jeśli aktywacja zakończy się pomyślnie, problem zostanie automatycznie zaleczony. 

Nowo umieszczona lub aktywowana replika może wyzwolić nową operację aktywacji lub pobierania. Ta akcja zakończy się pomyślnie lub ponownie wyzwoli wartość ServiceType blocklisting.

> [!NOTE]
> Awaria CodePackage, która nie rejestruje elementu ServiceType, nie ma wpływu na typ serviceType. Tylko awaria CodePackage, która obsługuje replikę, ma wpływ na typ serviceType.
>

### <a name="codepackage-crash"></a>Awaria CodePackage
W przypadku awarii CodePackage Service Fabric ponownie używa wycofywania, aby je uruchomić. Wycofywania jest stosowany bez względu na to, czy CodePackage zarejestrował typ w środowisku uruchomieniowym Service Fabric.

Wartość wycofywania to `Min(RetryTime, ActivationMaxRetryInterval)` . Wartość jest zwiększana do wartości stałej, liniowej lub wykładniczej na podstawie `ActivationRetryBackoffExponentiationBase` Ustawienia konfiguracji:

- **Stała**: if `ActivationRetryBackoffExponentiationBase == 0` , then `RetryTime = ActivationRetryBackoffInterval` .
- **Liniowy**: w przypadku  `ActivationRetryBackoffExponentiationBase == 0` , `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` gdy `ContinuousFailureCount` jest to liczba awarii CodePackage lub nie można jej aktywować.
- **Wykładniczy**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` .
    
Zachowanie można kontrolować, zmieniając wartości. Jeśli na przykład chcesz kilka prób ponownego uruchomienia, możesz użyć wartości liniowych przez ustawienie na wartość `ActivationRetryBackoffExponentiationBase` `0` i ustawienie `ActivationRetryBackoffInterval` `10` . Tak więc w przypadku awarii CodePackage Interwał uruchamiania będzie po 10 sekundach. Jeśli pakiet nadal ulega awarii, wycofywania zmienia się na 20 sekund, 30 sekund, 40 sekund itd., dopóki aktywacja CodePackage zakończy się powodzeniem lub CodePackage zostanie zdezaktywowana. 
    
Maksymalny czas, który Service Fabric wycofać (czyli czeka) po awarii podlega `ActivationMaxRetryInterval` .
    
Jeśli CodePackage ulegnie awarii i zostanie utworzona kopia zapasowa, musi ona zostać zaoferowana przez okres czasu określony przez `CodePackageContinuousExitFailureResetInterval` . Po tym czasie Service Fabric traktuje CodePackagee w dobrej kondycji. Service Fabric następnie zastępuje poprzedni raport kondycji błędów jako OK i resetuje `ContinuousFailureCount` .

### <a name="codepackage-not-registering-servicetype"></a>CodePackage nie zarejestrowano elementu ServiceType
Jeśli CodePackage się stanie i oczekuje się, że zarejestruje się ServiceType, ale nie, Service Fabric generuje raport kondycji ostrzegawczej po `ServiceTypeRegistrationTimeout` . Raport wskazuje, że typ ServiceType nie został zarejestrowany w oczekiwanym czasie.

### <a name="activation-failure"></a>Niepowodzenie aktywacji
Gdy Service Fabric odnajdzie błąd podczas aktywacji, zawsze korzysta z wycofywania liniowego, tak jak w przypadku awarii CodePackage. Operacja aktywacji naliczy się po ponownych próbach w następujących odstępach czasu: (0 + 10 + 20 + 30 + 40) = 100 sekund. (Pierwsza ponowna próba jest natychmiastowa). Po tej sekwencji aktywacja nie zostanie ponowiona.
    
Maksymalna wycofywania aktywacji może być większa `ActivationMaxRetryInterval` . Ponów próbę `ActivationMaxFailureCount` .

### <a name="download-failure"></a>Błąd pobierania
Service Fabric zawsze używa liniowej wycofywania w przypadku znalezienia błędu podczas pobierania. Operacja aktywacji naliczy się po ponownych próbach w następujących odstępach czasu: (0 + 10 + 20 + 30 + 40) = 100 sekund. (Pierwsza ponowna próba jest natychmiastowa). Po tej sekwencji pobieranie nie zostanie ponowione. 

Wycofywania liniowy do pobrania jest równa `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . Maksymalna wycofywania może być `DeploymentMaxRetryInterval` . Podobnie jak aktywacje, operacje pobierania mogą ponawiać próbę w celu uzyskania `ActivationMaxFailureCount` limitu.

> [!NOTE]
> Przed zmianą tych ustawień należy pamiętać o następujących przykładach:
>
>* Jeśli CodePackage powoduje awarię i wykonywanie kopii zapasowej, wartość ServiceType zostanie wyłączona. Jeśli jednak konfiguracja aktywacji ma Szybkie ponowne uruchomienie, CodePackage może potrwać kilka razy przed rzeczywistą blocklisted. 
>
>    Załóżmy na przykład, że CodePackage się, rejestruje ServiceType w Service Fabric, a następnie ulega awarii. W takim przypadku, gdy hosting otrzyma rejestrację typu, `ServiceTypeDisableGraceInterval` okres zostanie anulowany. Ten proces może powtarzać się do momentu, gdy CodePackage się z powrotem do wartości większej niż `ServiceTypeDisableGraceInterval` okres. Następnie typ ServiceType będzie blocklisted w węźle. Blocklisting ServiceType może trwać dłużej niż oczekiwano.
>
>* W przypadku aktywacji, gdy system Service Fabric musi umieścić replikę w węźle, Agent rekonfiguracji poprosi podsystem hostingu o aktywację aplikacji. Ponawia próbę żądania aktywacji co 15 sekund. (Czas trwania podlega `RAPMessageRetryInterval` ustawieniu konfiguracji). Service Fabric nie może wiedzieć, że typ ServiceType został blocklisted, chyba że operacja aktywacji w hostingu jest przez dłuższy okres niż interwał ponawiania prób i `ServiceTypeDisableGraceInterval` . 
>
>    Załóżmy na przykład, że klaster `ActivationMaxFailureCount` ma ustawioną wartość 5 i `ActivationRetryBackoffInterval` jest ustawiony na 1 sekundę. W takim przypadku operacja aktywacji jest wykonywana po upływie interwału (0 + 1 + 2 + 3 + 4) = 10 sekund. (Pierwsza ponowna próba jest natychmiastowa). Po tej sekwencji Host podaje ponowną próbę. Operacja aktywacji kończy się niepo 15 sekundach. 
>
>    Service Fabric przekroczenia wszystkich dozwolonych ponownych prób w ciągu 15 sekund. W związku z tym każda ponowna próba od agenta konfiguracji powoduje utworzenie nowej operacji aktywacji w podsystemie hostingu, a wzorzec ciągle powtarza się. W związku z tym typ ServiceType nigdy nie będzie blocklisted w węźle. Ponieważ typ ServiceType nie będzie blocklisted w węźle, replika nie zostanie przeniesiona i podjęta w innym węźle.
> 

## <a name="deactivation"></a>Dezaktywacji

Gdy pakiet servicepackage jest aktywowany w węźle, jest śledzony do dezaktywacji. Dezaktywacja działa na dwa sposoby:

- **Okresowe dezaktywacja**: co więcej `DeactivationScanInterval` , system sprawdza pakiety usług, które *nigdy nie* obsługują repliki i oznacza je jako kandydatów do dezaktywacji.
- **Dezaktywacja ReplicaClose**: Jeśli replika jest ZAMKNIĘTA, deaktywatora otrzymuje `DecrementUsageCount` . Liczba jest równa 0, gdy pakiet servicepackage nie obsługuje żadnej repliki, więc pakiet servicepackage jest kandydatem do dezaktywacji.

Tryb aktywacji określa, kiedy kandydaci są zaplanowani do dezaktywacji. W trybie współdzielonym kandydaci do dezaktywacji są zaplanowani po `DeactivationGraceInterval` . W trybie wyłączności są one zaplanowane po `ExclusiveModeDeactivationGraceInterval` . Jeśli nowe rozmieszczenie replik zostanie odebrane między tymi godzinami, dezaktywacja zostanie anulowana. 

Aby uzyskać więcej informacji, zobacz [tryb wyłącznooci i tryb udostępniania][a2].

### <a name="periodic-deactivation"></a>Okresowe dezaktywowanie
Poniżej przedstawiono kilka przykładów okresowej aktywacji:

* **Przykład 1**: Załóżmy, że deaktywator rozpocznie skanowanie w czasie T ( `DeactivationScanInterval` ). Następne skanowanie będzie miało 2T. Przyjęto założenie, że aktywacja pakietu servicepackage zaszło w T + 1. Ten pakiet servicepackage nie obsługuje repliki, więc należy ją dezaktywować. 

    Aby być kandydatem do dezaktywacji, pakiet servicepackage musi hostować brak repliki przez co najmniej T. Będzie ona uprawniona do dezaktywacji w 2T + 1. W związku z tym skanowanie pod adresem 2T nie będzie identyfikować tego elementu servicepackage jako kandydata do dezaktywacji. 

    Następnym cyklem dezaktywacji, 3T, Zaplanuj ten pakiet servicepackage do dezaktywacji, ponieważ teraz pakiet był w stanie No-Replica dla czasu T.  

* **Przykład 2**: Załóżmy, że pakiet servicepackage jest aktywowany w czasie T-1, a podaktywator rozpocznie skanowanie w T. Pakiet servicepackage nie obsługuje repliki. Podczas następnego skanowania 2T pakiet servicepackage zostanie zidentyfikowany jako kandydat do dezaktywacji, więc zostanie zaplanowana do dezaktywacji.  

* **Przykład 3**: Załóżmy, że pakiet servicepackage zostanie aktywowany z t – 1, a podaktywator rozpocznie skanowanie w t. Pakiet servicepackage nie obsługuje jeszcze repliki. Teraz w usłudze T + 1 zostanie umieszczona replika. To znaczy, hosting pobiera `IncrementUsageCount` , co oznacza, że tworzona jest replika. 

    W 2T ten pakiet servicepackage nie zostanie zaplanowany do dezaktywacji. Ponieważ pakiet zawiera replikę, dezaktywacja będzie zgodna z logiką ReplicaClose, jak opisano w następnej sekcji tego artykułu.

* **Przykład 4**: Załóżmy, że pakiet servicepackage ma 10 GB. Ponieważ pakiet jest duży, pobieranie w węźle trwa dłużej. Po aktywowaniu aplikacji deaktywator śledzi cykl życia. Jeśli `DeactivationScanInterval` ustawiono małą wartość, pakiet servicepackage może nie mieć czasu na aktywację w węźle z powodu czasu, który zajęło pobranie. Aby rozwiązać ten problem, możesz [pobrać pakiet servicepackage z wyprzedzeniem w węźle][p1] lub zwiększyć `DeactivationScanInterval` . 

> [!NOTE]
> Pakiet servicepackage może zostać zdezaktywowany w dowolnym miejscu między ( `DeactivationScanInterval` do 2 * `DeactivationScanInterval` ) + `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>Dezaktywacja ReplicaClose

> [!NOTE]
> Ta sekcja dotyczy następujących ustawień konfiguracji:
> - **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: czas nadany usłudze servicepackage do hostowania innej repliki, jeśli już ją obsługiwała. 
> - **DeactivationScanInterval**: minimalny czas przyznany pakietowi servicepackage do hostowania repliki, jeśli *nigdy* nie jest hostowana żadna replika, czyli jeśli nie została ona użyta.
>

System przechowuje liczbę replik, które przechowuje pakiet servicepackage. Jeśli pakiet servicepackage utrzymuje replikę, a replika jest ZAMKNIĘTA lub nie działa, Host otrzymuje `DecrementUsageCount` . Gdy replika jest otwarta, hosting pobiera `IncrementUsageCount` . 

Zmniejszenie wskazuje, że liczba replik, które obsługuje pakiet servicepackage, została zmniejszona przez jedną replikę. Gdy liczba spadnie do 0, pakiet servicepackage zostanie zaplanowany do dezaktywacji. Czas, po którym zostanie zdezaktywowana, to `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 

Załóżmy na przykład, że zmniejszenie występuje w T, a pakiet servicepackage został zaplanowany do dezaktywowania w 2T + X ( `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` ). W tym czasie, Jeśli host pobiera `IncrementUsage` , ponieważ replika jest tworzona, dezaktywacja zostanie anulowana.

### <a name="ctrl--c"></a>Ctrl + C
Jeśli pakiet servicepackage przeszedł `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` i nadal nie obsługuje repliki, nie można anulować dezaktywacji. CodePackages Odbierz procedurę obsługi Ctrl + C. Teraz potok dezaktywacji musi zakończyć się, aby przełączyć proces w dół. 

W tym czasie, jeśli Nowa replika dla tego samego pakietu servicepackage próbuje zostać umieszczona, nie powiedzie się, ponieważ proces nie może przejść z dezaktywacji na aktywację.

## <a name="cluster-configurations"></a>Konfiguracje klastrów

W tej sekcji wymieniono konfiguracje, które mają wpływ na aktywację i dezaktywację.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: wartość domyślna: 1. Próg liczby niepowodzeń; po osiągnięciu tego progu zostanie powiadomiony o wyłączeniu typu usługi w węźle i wypróbowania innego węzła.
- **ServiceTypeDisableGraceInterval**: wartość domyślna: 30 sekund. Przedział czasu, po którym można wyłączyć typ usługi.
- **ServiceTypeRegistrationTimeout**: wartość domyślna: 300 sekund. Limit czasu rejestrowania dla elementu ServiceType w Service Fabric.

### <a name="activation"></a>Uaktywnienie
- **ActivationRetryBackoffInterval**: wartość domyślna: 10 sekund. Wycofywania interwał dla każdej awarii aktywacji.
- **ActivationMaxFailureCount**: wartość domyślna: 20. Maksymalna liczba, dla której system podejmie ponowną próbę aktywacji zakończonej niepowodzeniem przed pokazaniem. 
- **ActivationRetryBackoffExponentiationBase**: wartość domyślna: 1,5.
- **ActivationMaxRetryInterval**: wartość domyślna: 3 600 sekund. Maksymalny interwał ponawiania prób wycofywania dla aktywacji po błędach.
- **CodePackageContinuousExitFailureResetInterval**: wartość domyślna: 300 sekund. Interwał limitu czasu służący do resetowania liczby błędów wyjścia ciągłego dla CodePackage.

### <a name="download"></a>Pobierz
- **DeploymentRetryBackoffInterval**: wartość domyślna: 10. Interwał wycofywaniau dla niepowodzenia wdrożenia.
- **DeploymentMaxRetryInterval**: wartość domyślna: 3 600 sekund. Maksymalny interwał wycofywania wdrożenia po wystąpieniu błędów.
- **DeploymentMaxFailureCount**: wartość domyślna: 20. Ponawianie próby wdrożenia aplikacji w węźle zostanie ponowione `DeploymentMaxFailureCount` .

### <a name="deactivation"></a>Dezaktywacji
- **DeactivationScanInterval**: wartość domyślna: 600 sekund. Minimalny czas nadawany przez pakiet servicepackage do hostowania repliki, jeśli nigdy nie jest hostowana żadna replika (to oznacza, jeśli nie jest używana).
- **DeactivationGraceInterval**: wartość domyślna: 60 sekund. W modelu procesu *współużytkowanego* , czas przyznany pakietowi servicepackage do ponownego hostowania kolejnej repliki, gdy już jest ona hostowana.
- **ExclusiveModeDeactivationGraceInterval**: wartość domyślna: 1 sekunda. W modelu procesu *wyłącznego* , czas nadany pakietowi servicepackage do ponownego hostowania innej repliki, po której już jest hostowana dowolna replika.

## <a name="next-steps"></a>Następne kroki

- Utwórz [pakiet aplikacji][a3] i przygotuj go do wdrożenia.
- [Wdrażaj i usuwaj aplikacje][a4] w programie PowerShell.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
