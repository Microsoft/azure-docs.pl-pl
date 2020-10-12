---
title: Platforma Azure Service Fabric Hostowanie i aktywacja cyklu życia
description: Wyjaśnia cykl życia aplikacji i pakietu servicepackage w węźle
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: a39aecf16d1c3303c0a590b389ba2aa69d4472f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87405130"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Cykl życia usługi Azure Service Fabric hosting
Ten artykuł zawiera omówienie zdarzeń, które pojawiają się, gdy aplikacja zostanie aktywowana w węźle i różne konfiguracje klastra używane do sterowania zachowaniem.

Przed kontynuowaniem upewnij się, że rozumiesz różne koncepcje i relacje wyjaśnione w temacie [model a Application w Service Fabric][a1]. 

> [!NOTE]
> W tym artykule, o ile nie określono inaczej jako znaczenia:
>
> - *Replika* odnosi się zarówno do repliki usługi stanowej, jak i wystąpienia usługi bezstanowej.
> - *CodePackage* jest traktowany jako równoważny z procesem *ServiceHost* , który rejestruje wartość *ServiceType*i obsługuje repliki usług tego *programu.*
>

## <a name="activation-of-applicationservicepackage"></a>Aktywacja aplikacji/pakietu servicepackage

Potok aktywacji jest następujący:

1. Pobierz ApplicationPackage. Na przykład: ApplicationManifest.xml itd.
2. Skonfiguruj środowisko dla aplikacji ex: Create users itp.
3. Rozpocznij śledzenie aplikacji do dezaktywacji.
4. Pobierz pakiet servicepackage. Na przykład: ServiceManifest.xml, kod, konfiguracja, pakiety danych itp.
5. Skonfiguruj środowisko dla pakietu usługi dla: Zapora konfiguracji, Przydziel porty dla punktów końcowych itp.
6. Rozpocznij śledzenie pakietu servicepackage do dezaktywacji.
7. Rozpocznij SetupEntryPoint of CodePackages i poczekaj na zakończenie.
8. Rozpocznij MainEntryPoint z CodePackages.

### <a name="servicetype-blocklisting"></a>Blocklisting ServiceType
**ServiceTypeDisableFailureThreshold** określa liczbę błędów (aktywacji, pobrania, niepowodzeń CodePackage), po których zaplanowano typ ServiceType dla blocklisting. W związku z tym podczas pierwszej aktywacji/pobierania lub CodePackage awaria należy wyzwolić harmonogram z ServiceType blocklisting. Konfiguracja **ServiceTypeDisableGraceInterval** określa interwał prolongaty, po którym typ ServiceType jest oznaczony jako blocklisted w tym węźle. Należy pamiętać, że w celu przeprowadzenia aktywacji/ponownego uruchomienia/CodePackage należy nadal w trybie ponawiania próbne i być śledzone przez podsystem hostingu. Ponawianie próby, oznacza na przykład: CodePackage zostanie zaplanowane do ponownego uruchomienia po awarii lub Service Fabric spróbuje pobrać pakiety ponownie.
Po blocklisted powinien zostać wyświetlony komunikat o błędzie "System. hosting" zgłosił błąd dla właściwości "ServiceTypeRegistration: ServiceType". Typ ServiceType został wyłączony w węźle ".

Wartość ServiceType zostanie włączona z powrotem w węźle 
- Jeśli operacja aktywacji zakończy się pomyślnie lub osiągnie **ActivationMaxFailureCount** ponownych prób w przypadku awarii.
- Jeśli operacja pobierania zakończy się pomyślnie lub osiągnie **DeploymentMaxFailureCount** ponownych prób w przypadku niepowodzenia.
- Jeśli CodePackage, który uległ awarii, uruchomi się i pomyślnie zarejestruje serviceType.

Powód ponownego włączenia tego programu po **ActivationMaxFailureCount** / **DeploymentMaxFailureCount** ponawiania prób to, że jest to maksymalna liczba Service Fabric prób, które zostaną wykonane w celu aktywowania/pobrania aplikacji w węźle. Jeśli to się nie powiedzie, bieżąca operacja nie zostanie ponowiona, ponieważ Service Fabric chce dać usłudze kolejną okazję do aktywacji, co może się powieść, powodując Automatyczne naprawianie problemu, jest ono powiązane z cyklem życia operacji aktywacji/pobierania. Nowa operacja aktywacji/pobierania wyzwalana przez rozmieszczenie repliki może ponownie wyzwolić blocklisting (ServiceType) lub może się powieść.

> [!NOTE]
> Jeśli CodePackage, w którym nie zarejestrowano elementu ServiceType, ulegnie awarii, nie ma to wpływu na typ serviceType. Tylko CodePackage hostujący replikę awarię będzie miało wpływ na typ serviceType.
>

### <a name="codepackage-crash"></a>Awaria CodePackage
W przypadku awarii CodePackage Service Fabric wycofać ją ponownie, a wycofanie jest niezależne od tego, czy pakiet kodu zarejestrował typ z nami, czy nie.

Wartość wycofania jest zawsze minimalna (RetryTime, **ActivationMaxRetryInterval**), a ta wartość może być stała, liniowa lub wykładnicza na podstawie konfiguracji **ActivationRetryBackoffExponentiationBase** .

- Stała: Jeśli **ActivationRetryBackoffExponentiationBase** = = 0, RetryTime = **ActivationRetryBackoffInterval**;
- Liniowy: Jeśli  **ActivationRetryBackoffExponentiationBase** = = 0 Then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** , gdzie ContinousFailureCount to liczba awarii programu CodePackage lub Niepowodzenie aktywacji.
- Wykładniczy: RetryTime = (**ActivationRetryBackoffInterval** w sekundach) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
Zachowanie można kontrolować, tak jak szybkie ponowne uruchamianie. Porozmawiamy o skali liniowej. Oznacza to, że jeśli CodePackage ulega awarii, interwał uruchamiania będzie po 10, 20, 30 40 sek., dopóki nie zostanie zdezaktywowany CodePackage. 
    
Maksymalny czas, który Service Fabric wycofać (czeka) po awarii podlega **ActivationMaxRetryInterval**
    
Jeśli CodePackage ulegnie awarii i zostanie utworzona kopia zapasowa, musi ona być w stanie **CodePackageContinuousExitFailureResetInterval** dla Service Fabric, aby wziąć pod uwagę, że będzie to miało miejsce w dobrej kondycji, w którym zostanie zastąpiony raport o kondycji jako OK i zresetowano ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage nie zarejestrowano elementu ServiceType
Jeśli CodePackage nadal działa i oczekuje się, że usługa ServiceType jest zarejestrowana, ale nigdy nie tak, w takim przypadku Service Fabric wygeneruje ostrzeżenie HealthReport po **ServiceTypeRegistrationTimeout** , że nie skonfigurowano tego elementu przed upływem limitu czasu.

### <a name="activation-failure"></a>Niepowodzenie aktywacji
Service Fabric zawsze korzysta z wycofywania liniowego (tak samo jak awaria CodePackage), jeśli podczas aktywacji wystąpi błąd. Oznacza to, że operacja aktywacji zostanie wystawiona po (0 + 10 + 20 + 30 + 40) = 100 s (pierwsza ponowna próba jest natychmiastowa). Po tej aktywacji nie zostanie podjęta ponowna próba.
    
Maksymalna wycofywania aktywacji może być **ActivationMaxRetryInterval** i ponów próbę **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Błąd pobierania
Service Fabric zawsze używa liniowego wycofywania, gdy napotka błąd podczas pobierania. Oznacza to, że operacja aktywacji zostanie wystawiona po (0 + 10 + 20 + 30 + 40) = 100 s (pierwsza ponowna próba jest natychmiastowa). Po pobraniu tego pobierania nie zostanie ponowiona. Liniowa kopia zapasowa do pobrania jest równa ContinuousFailureCount ***DeploymentRetryBackoffInterval** i może zostać wycofana do **DeploymentMaxRetryInterval**. Podobnie jak aktywacje, operacja pobierania może ponowić próbę **ActivationMaxFailureCount**.

> [!NOTE]
> Przed zmianą konfiguracji należy pamiętać o kilku przykładach.

* Jeśli CodePackage powoduje awarię i wyłączenie z powrotem, ServiceType zostanie wyłączona. Jeśli jednak konfiguracja aktywacji jest taka, że ma ona Szybkie ponowne uruchomienie, CodePackage może być dostępna przez kilka razy, zanim będzie mogła zobaczyć wyłączenie elementu serviceType. Na przykład: Załóżmy, że CodePackage się, rejestruje ServiceType z Service Fabric a następnie ulega awarii. W takim przypadku, gdy hosting otrzyma rejestrację typu, anulowano okres **ServiceTypeDisableGraceInterval** . Może to być powtarzane, dopóki nie CodePackage się z powrotem do wartości większej niż  **ServiceTypeDisableGraceInterval** , a następnie ServiceType zostanie wyłączona w węźle. Tak więc może być trochę czasu przed wyłączeniem ServiceType w węźle.

* W przypadku aktywacji, gdy Service Fabric system musi umieścić replikę w węźle, RA (ReconfigurationAgent) żąda podsystemu hostingu w celu aktywowania aplikacji i ponawiania prób żądania aktywacji co 15 sek. (**RAPMessageRetryInterval**). Aby dowiedzieć się, że usługa ServiceType została wyłączona, operacja aktywacji w hostingu musi być aktywna przez dłuższy okres niż interwał ponawiania prób i **ServiceTypeDisableGraceInterval**. Service Fabric Na przykład: pozwól, aby klaster miał konfigurację **ActivationMaxFailureCount** ustawioną na 5 i **ActivationRetryBackoffInterval** ustawioną na 1 sekundę. Oznacza to, że operacja aktywacji zostanie przekazana po (0 + 1 + 2 + 3 + 4) = 10 sekund (pierwsza ponowna próba jest natychmiast), a po tym, gdy host przeprowadzi ponowną próbę. W takim przypadku operacja aktywacji zostanie zakończona i nie zostanie ponowiona ponowna próba po 15 sekundach. Wystąpił, ponieważ Service Fabric wszystkie ponownych prób w ciągu 15 sekund. W związku z tym każda próba ponowienia z ReconfigurationAgent tworzy nową operację aktywacji w podsystemie hostingu, a wzorzec będzie utrzymywać powtarzanie, a typ ServiceType nigdy nie będzie wyłączony w węźle. Ponieważ typ ServiceType nie zostanie wyłączony w węźle, nie będzie można przenieść repliki do innego węzła.
> 

## <a name="deactivation"></a>Dezaktywacji

Gdy pakiet servicepackage jest aktywowany w węźle, jest śledzony do dezaktywacji. Deaktywatora to jednostka, która śledzi ją.
Rozróżnienie działa na dwa sposoby:

1.  Okresowo: w każdym **DeactivationScanInterval**sprawdza dla servicepackages, które nigdy nie obsługiwały repliki i oznacza je jako kandydatów do dezaktywacji.
2.  ReplicaClose: Jeśli replika jest ZAMKNIĘTA, polecenie deaktywatora pobiera DecrementUsageCount. Jeśli liczba jest równa 0, oznacza to, że pakiet servicepackage nie obsługuje żadnej repliki, więc jest kandydatem do dezaktywacji.

 W oparciu o tryb aktywacji [wyłączny/udostępniony][a2], kandydaci do dezaktywacji są zaplanowani po **DeactivationGraceInterval** dla elementu sharedmode/ **ExclusiveModeDeactivationGraceInterval** w trybie wyłączności. Jeśli w okresie między tym czasem nowe umieszczanie repliki zostanie nastąpić, dezaktywowanie zostanie anulowane.

### <a name="periodically"></a>Pewien
Przykład 1: Załóżmy, że deaktywator wykonuje skanowanie w czasie T (**DeactivationScanInterval**). Następne skanowanie będzie miało 2T. Przyjęto założenie, że aktywacja pakietu servicepackage zaszło w T + 1. Ten pakiet servicepackage nie obsługuje repliki, dlatego musi zostać zdezaktywowany. Aby pakiet servicepackage miał być kandydatem do dezaktywacji, musi on być w stanie braku repliki przez czas T. Oznacza to, że będzie on uprawniony do dezaktywacji w 2T + 1. W związku z tym skanowanie pod adresem 2T nie znajdzie tego elementu servicepackage jako kandydata do dezaktywacji. W następnym cyklu deaktywacji 3T Zaplanuj ten pakiet servicepackage do dezaktywacji, ponieważ teraz nie ma stanu repliki dla czasu T.  

Przykład 2: Załóżmy, że servicepackage jest aktywowany w czasie T-1 i deaktywator wykonuje skanowanie w T. Pakiet servicepackage nie obsługuje repliki. Następnie przy następnym skanowaniu 2T ten pakiet servicepackage zostanie znaleziony jako kandydat do dezaktywacji, dlatego zostanie zaplanowany do dezaktywacji.  

Przykład 3: Załóżmy, że pakiet servicepackage zostanie aktywowany z T – 1, a deaktywator wykonuje skanowanie w T. Pakiet servicepackage nie obsługuje jeszcze repliki. Teraz w usłudze T + 1 replika jest umieszczana, tj. Hosting pobiera IncrementUsageCount, co oznacza, że tworzona jest replika. Teraz o godzinie 2T ten pakiet servicepackage nie zostanie zaplanowany do dezaktywacji. Teraz dezaktywacja przejdzie do logiki ReplicaClose opisanej poniżej.

Przykład 4: Załóżmy, że pakiet servicepackage jest duży, taki jak 10 GB, a następnie pobieranie w węźle może zająć trochę czasu. Po aktywowaniu aplikacji deaktywator śledzi jej cykl życia. Teraz, jeśli masz niewielką **DeactivationScanIntervalą** konfigurację, możesz napotkać problemy, w przypadku których pakiet servicepackage nie otrzymuje czasu na aktywację w węźle, ponieważ cały czas poszło do pobrania. Aby rozwiązać ten problem, możesz [wstępnie pobrać pakiet servicepackage w węźle][p1]. 

> [!NOTE]
> Aby pakiet servicepackage mógł zostać zdezaktywowany w dowolnym miejscu (**DeactivationScanInterval** – 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>Zamknięto replikę:
Deaktywator przechowuje liczbę replik, które przechowuje pakiet servicepackage. Jeśli pakiet servicepackage utrzymuje replikę, a replika jest ZAMKNIĘTA/wyłączona, Host pobiera DecrementUsageCount. Gdy replika jest otwarta, hosting pobiera IncrementUsageCount. Zmniejszenie oznacza, że pakiet servicepackage obsługuje teraz jedną mniejszą replikę, a liczba spadnie do 0, a następnie jest zaplanowana do dezaktywacji, a czas, po którym zostanie zdezaktywowana, to **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Na przykład: Załóżmy, że zmniejszenie występuje w T i servicepackage zaplanowano na dezaktywację w 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**). Jeśli w tym czasie hosting pobiera IncrementUsage oznacza, że zostanie utworzona replika, dezaktywowanie zostanie anulowane.

> [!NOTE]
>Co oznacza, że te konfiguracje są zasadniczo: **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: czas przyznany pakietowi servicepackage do hostowania kolejnej repliki po jej przeprowadzeniu. 
**DeactivationScanInterval**: minimalny czas przyznany pakietowi servicepackage do hostowania repliki, jeśli nigdy nie obsługiwał żadnej repliki, tj. Jeśli nie jest używany.
>

### <a name="ctrlc"></a>Ctrl+C
Gdy pakiet servicepackage przejdzie do **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** i nadal nie obsługuje repliki, dezaktywacja nie zostanie anulowana. CodePackage są wystawiane przez program obsługi klawiszy CTRL + C, co oznacza, że teraz potok dezaktywacji musi przejść, aby przełączyć proces w dół. W tym czasie, jeśli Nowa replika dla tego samego pakietu servicepackage próbuje zostać umieszczona, nie powiedzie się, ponieważ nie można przejść od dezaktywacji do aktywacji.

## <a name="cluster-configs"></a>Konfiguracje klastrów

Konfiguracje z wartościami domyślnymi wpływającymi na aktywację/decativation.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: domyślny 1. Próg liczby niepowodzeń po tym, że FM (trybu failover) jest powiadamiany o wyłączeniu typu usługi w tym węźle i próba umieszczania w innym węźle.
**ServiceTypeDisableGraceInterval**: domyślnie 30 sek. Przedział czasu, po którym można wyłączyć typ usługi.
**ServiceTypeRegistrationTimeout**: domyślnie 300 sek. Limit czasu rejestrowania dla ServiceType w Service Fabric.

### <a name="activation"></a>Uaktywnienie
**ActivationRetryBackoffInterval**: wartość domyślna to 10 sekund. Wycofywania interwał dla każdej awarii aktywacji.
**ActivationMaxFailureCount**: domyślnie 20. Maksymalna liczba, dla której system podejmie ponowną próbę niepowodzenia aktywacji przed pokazaniem. 
**ActivationRetryBackoffExponentiationBase**: domyślnie 1,5.
**ActivationMaxRetryInterval**: domyślnie 3600 sek. Maksymalna liczba wycofywania dla aktywacji w przypadku awarii.
**CodePackageContinuousExitFailureResetInterval**: domyślnie 300 sek. Limit czasu resetowania liczby niepowodzeń ciągłego zamykania dla CodePackage.

### <a name="download"></a>Pobierz
**DeploymentRetryBackoffInterval**: domyślnie 10. Interwał wycofywania dla błędu wdrożenia.
**DeploymentMaxRetryInterval**: domyślnie 3600 sek. Maksymalna liczba wycofywania dla wdrożenia w przypadku awarii.
**DeploymentMaxFailureCount**: domyślnie 20. Ponowna próba wdrożenia aplikacji zostanie ponowiona dla DeploymentMaxFailureCount czasów przed niepowodzeniem wdrożenia tej aplikacji w węźle.

### <a name="deactivation"></a>Dezaktywacji
**DeactivationScanInterval**: domyślnie 600 sek. Minimalny czas, który ma być obsługiwany przez pakiet servicepackage do hostowania repliki, jeśli nigdy nie obsługiwał żadnej repliki, tj. Jeśli nie jest używany.
**DeactivationGraceInterval**: domyślnie 60 sek. Czas, który ma być obsługiwany przez pakiet servicepackage, będzie obsługiwał ponownie inną replikę po przeprowadzeniu przez nią repliki w przypadku modelu procesu **udostępnionego** .
**ExclusiveModeDeactivationGraceInterval**: domyślnie 1 s. Czas, który ma być obsługiwany przez pakiet servicepackage, będzie obsługiwał ponownie inną replikę, gdy przekroczy ona replikę w przypadku modelu procesu **wyłącznego** .

## <a name="next-steps"></a>Następne kroki
Utwórz [pakiet aplikacji][a3] i przygotuj go do wdrożenia.

[Wdrażaj i usuwaj aplikacje][a4]. W tym artykule opisano sposób użycia programu PowerShell do zarządzania wystąpieniami aplikacji.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
