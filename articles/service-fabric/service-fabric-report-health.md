---
title: Dodawanie niestandardowych raportów o kondycji Service Fabric
description: Opisuje sposób wysyłania niestandardowych raportów o kondycji do jednostek usługi Azure Service Fabric Health. Zawiera zalecenia dotyczące projektowania i implementowania raportów dotyczących kondycji jakości.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: 6df434610a8f595ecca7f16e31f8a302373b02f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001868"
---
# <a name="add-custom-service-fabric-health-reports"></a>Dodawanie niestandardowych raportów o kondycji Service Fabric
Na platformie Azure Service Fabric wprowadzono [model kondycji](service-fabric-health-introduction.md) umożliwiający Flagowanie klastra w złej kondycji i warunków aplikacji na określonych jednostkach. Model kondycji używa **raportów kondycji** (składniki systemowe i alarmy). Celem jest łatwe i Szybkie diagnozowanie i naprawa. Moduły zapisujące usługi muszą myśleć o kondycji. Każdy warunek, który może mieć wpływ na kondycję, powinien być raportowany w szczególności, jeśli może pomóc w oflagowaniu problemów blisko poziomu głównego. Informacje o kondycji mogą zaoszczędzić czas i wysiłku związane z debugowaniem i badaniem. Użyteczność jest szczególnie oczywista, gdy usługa jest uruchomiona i działa w dużej skali w chmurze (prywatnej lub na platformie Azure).

Monitorowanie Service Fabric raporty określiło warunki zainteresowania. Składają raporty dotyczące tych warunków w zależności od ich widoku lokalnego. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) agreguje dane o kondycji wysyłane przez wszystkich raportów, aby określić, czy jednostki są w dobrej kondycji. Model ma być bogaty, elastyczny i łatwy w użyciu. Jakość raportów o kondycji określa dokładność widoku kondycji klastra. Fałszywie dodatnie, które źle wyświetlają problemy w złej kondycji, mogą mieć negatywny wpływ na uaktualnienia lub inne usługi korzystające z danych o kondycji. Przykładami takich usług są naprawy usługi i mechanizmy powiadamiania o alertach. W związku z tym niektóre zagadnienia są potrzebne do dostarczania raportów, które przechwytują warunki zainteresowania w najlepszym możliwym przypadku.

Aby zaprojektować i zaimplementować Raportowanie kondycji, licznik alarmów i składniki systemowe muszą:

* Zdefiniuj żądany warunek, sposób jego monitorowania oraz wpływ na funkcjonalność klastra lub aplikacji. Na podstawie tych informacji należy określić właściwość raportu kondycji i stan kondycji.
* Określ [jednostkę](service-fabric-health-introduction.md#health-entities-and-hierarchy) , do której odnosi się raport.
* Określ, w jaki sposób ma być wykonywane raportowanie, z poziomu usługi lub z wewnętrznego lub zewnętrznego licznika alarmowego.
* Zdefiniuj źródło używane do identyfikowania raportu.
* Wybierz strategię raportowania (okresowo lub przy przejściach). Zalecany sposób są okresowo, ponieważ wymaga prostszy kod i jest mniej podatny na błędy.
* Określ, jak długo raport o złej kondycji powinien pozostać w magazynie kondycji i jak powinien zostać wyczyszczony. Korzystając z tych informacji, należy zdecydować o czasie trwania i usunięciu czasu wygaśnięcia raportu.

Jak wspomniano wcześniej, można wykonywać raportowanie z:

* Replika monitorowanej usługi Service Fabric.
* Wewnętrzne alarmy wdrożone jako usługa Service Fabric (na przykład Service Fabric usługa bezstanowa, która monitoruje warunki i raporty problemów). Licznik Alarms można wdrożyć wszystkie węzły lub można ją koligacji z monitorowaną usługą.
* Wewnętrzne alarmy, które działają w węzłach Service Fabric, ale *nie* są implementowane jako usługi Service Fabric.
* Zewnętrzne liczniki alarmowe, które sonduje zasób *spoza* klastra Service Fabric (na przykład usługa monitorowania, taka jak Gomez).

> [!NOTE]
> Poza tym klaster jest wypełniany raportami kondycji wysyłanymi przez składniki systemowe. Więcej informacji można znaleźć w artykule dotyczącym [rozwiązywania problemów przy użyciu raportów kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Raporty użytkownika muszą być wysyłane w [jednostkach kondycji](service-fabric-health-introduction.md#health-entities-and-hierarchy) , które zostały już utworzone przez system.
> 
> 

Po wyczyszczeniu projektu raportowania kondycji można łatwo wysyłać raporty kondycji. Programu [FabricClient](/dotnet/api/system.fabric.fabricclient) można użyć do raportowania kondycji, Jeśli klaster nie jest [zabezpieczony](service-fabric-cluster-security.md) lub jeśli klient sieci szkieletowej ma uprawnienia administratora. Raportowanie można przeprowadzić za pośrednictwem interfejsu API za pomocą [FabricClient. HealthManager. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), za pośrednictwem programu PowerShell lub za pośrednictwem protokołu REST. Pokrętła konfiguracji przedstawia raporty wsadowe w celu zwiększenia wydajności.

> [!NOTE]
> Kondycja raportu jest synchroniczna i reprezentuje tylko sprawdzenie poprawności działania po stronie klienta. Fakt, że raport jest akceptowany przez klienta kondycji lub `Partition` obiekty lub `CodePackageActivationContext` nie oznacza, że są stosowane w sklepie. Jest on wysyłany asynchronicznie i prawdopodobnie przetwarzany z innymi raportami. Przetwarzanie na serwerze może nadal zakończyć się niepowodzeniem: numer sekwencyjny może być nieodświeżony, jednostka, na której należy zastosować raport, został usunięty itp.
> 
> 

## <a name="health-client"></a>Klient kondycji
Raporty kondycji są wysyłane do Menedżera kondycji za pomocą klienta kondycji, który znajduje się wewnątrz klienta sieci szkieletowej. Menedżer kondycji zapisuje raporty w magazynie kondycji. Klienta kondycji można skonfigurować przy użyciu następujących ustawień:

* **HealthReportSendInterval**: opóźnienie między momentem, gdy raport zostanie dodany do klienta programu i czas, który jest wysyłany do Menedżera kondycji. Służy do tworzenia wsadowych raportów w jednej wiadomości, zamiast wysyłania jednej wiadomości dla każdego raportu. Przetwarzanie wsadowe poprawia wydajność. Wartość domyślna: 30 sekund.
* **HealthReportRetrySendInterval**: interwał, w którym klient kondycji ponownie wysyła skumulowane raporty kondycji do Menedżera kondycji. Wartość domyślna: 30 sekund, minimum: 1 sekunda.
* **HealthOperationTimeout**: przekroczenie limitu czasu dla wiadomości raportu wysyłanej do Menedżera kondycji. W przypadku upływu limitu czasu klient kondycji ponowi próbę, dopóki raport nie zostanie przetworzony. Wartość domyślna: dwie minuty.

> [!NOTE]
> Gdy raporty są przetwarzane partiami, klient sieci szkieletowej musi być aktywny dla co najmniej HealthReportSendInterval, aby upewnić się, że są wysyłane. Jeśli komunikat zostanie utracony lub Menedżer kondycji nie będzie mógł zastosować ich ze względu na błędy przejściowe, klient sieci szkieletowej musi być w stanie dłużej działać, aby umożliwić mu ponowną próbę.
> 
> 

Buforowanie na kliencie wymaga unikatowej wartości raportów. Na przykład jeśli konkretny nieprawidłowy raport zgłasza 100 raportów na sekundę na tej samej właściwości tej samej jednostki, raporty są zastępowane ostatnią wersją. W kolejce klienta istnieje co najwyżej jeden raport. W przypadku skonfigurowania przetwarzania wsadowego liczba raportów wysyłanych do Menedżera kondycji jest tylko jeden na interwał wysyłania. Ten raport jest ostatnim dodanym raportem, który odzwierciedla najbardziej aktualny stan jednostki.
Określ parametry konfiguracji `FabricClient` , gdy jest tworzony przez przekazanie [FabricClientSettings](/dotnet/api/system.fabric.fabricclientsettings) z żądanymi wartościami dla wpisów związanych z kondycją.

Poniższy przykład tworzy klienta sieci szkieletowej i określa, że raporty mają być wysyłane po ich dodaniu. W przypadku przekroczenia limitu czasu i błędów, które mogą być ponawiane, ponowne próby odbywają się co 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Zalecamy pozostawienie domyślnych ustawień klienta sieci szkieletowej, które są ustawione `HealthReportSendInterval` na 30 sekund. To ustawienie zapewnia optymalną wydajność z powodu przetwarzania wsadowego. W przypadku raportów o kluczowym znaczeniu, które muszą być wysyłane najszybciej, jak to możliwe, użyj `HealthReportSendOptions` natychmiastowego `true` interfejsu API [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) . Natychmiastowe raporty pomijają interwał przetwarzania wsadowego. Używaj tej flagi z opieką; Jeśli to możliwe, chcemy skorzystać z partii klientów kondycji. Natychmiastowe wysyłanie jest również przydatne w przypadku zamykania klienta sieci szkieletowej (na przykład proces ustalił nieprawidłowy stan i musi zostać zamknięty, aby zapobiec efektom ubocznym). Zapewnia to optymalny sposób wysyłania raportów skumulowanych. Po dodaniu jednego raportu do natychmiastowej flagi klient kondycji zlicza wszystkie skumulowane raporty od ostatniego wysłania.

Te same parametry można określić podczas tworzenia połączenia z klastrem za pomocą programu PowerShell. Poniższy przykład uruchamia połączenie z klastrem lokalnym:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Podobnie jak w przypadku interfejsu API, można wysyłać raporty przy użyciu `-Immediate` przełącznika, który ma być wysyłany natychmiast, niezależnie od `HealthReportSendInterval` wartości.

W przypadku protokołu REST raporty są wysyłane do bramy Service Fabric, która ma wewnętrznego klienta sieci szkieletowej. Domyślnie ten klient jest skonfigurowany do wysyłania raportów wsadowych co 30 sekund. Możesz zmienić interwał wsadowy przy użyciu ustawienia konfiguracji klastra `HttpGatewayHealthReportSendInterval` `HttpGateway` . Jak wspomniano, lepszym rozwiązaniem jest wysyłanie raportów z `Immediate` wartością true. 

> [!NOTE]
> Aby upewnić się, że nieautoryzowane usługi nie mogą zgłosić kondycji dla jednostek w klastrze, skonfiguruj serwer tak, aby akceptował żądania tylko od zabezpieczonych klientów. Aby `FabricClient` można było komunikować się z klastrem (na przykład z uwierzytelnianiem Kerberos lub certyfikatem), używany na potrzeby raportowania musi mieć włączoną funkcję zabezpieczenia. Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Raportowanie z poziomu usług z niskimi uprawnieniami
Jeśli usługi Service Fabric nie mają dostępu administratora do klastra, można raportować kondycję jednostek z bieżącego kontekstu za pośrednictwem `Partition` lub `CodePackageActivationContext` .

* W przypadku usług bezstanowych Użyj [IStatelessServicePartition. ReportInstanceHealth](/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) , aby zgłosić bieżące wystąpienie usługi.
* W przypadku usług stanowych Użyj [IStatefulServicePartition. ReportReplicaHealth](/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) , aby zgłosić bieżącą replikę.
* Użyj [IServicePartition. ReportPartitionHealth](/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) , aby zgłosić bieżącą jednostkę partycji.
* Użyj [CodePackageActivationContext. ReportApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) , aby zgłosić bieżącą aplikację.
* Użyj [CodePackageActivationContext. ReportDeployedApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) , aby zgłosić bieżącą aplikację wdrożoną w bieżącym węźle.
* Użyj [CodePackageActivationContext. ReportDeployedServicePackageHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) , aby zgłosić pakiet usługi dla aplikacji wdrożonej w bieżącym węźle.

> [!NOTE]
> Wewnętrznie, `Partition` i `CodePackageActivationContext` utrzymuje klienta kondycji skonfigurowany z ustawieniami domyślnymi. Zgodnie z wyjaśnieniem dla [klienta kondycji](service-fabric-report-health.md#health-client)raporty są wsadowe i wysyłane na czasomierzu. Aby można było wysłać raport, należy utrzymać możliwość utrzymania tego obiektu.
> 
> 

Możesz określić `HealthReportSendOptions` czas wysyłania raportów `Partition` i `CodePackageActivationContext` interfejsów API kondycji. Jeśli masz raporty krytyczne, które muszą być wysyłane najszybciej, jak to możliwe, użyj `HealthReportSendOptions` z natychmiast `true` . Natychmiastowe raporty pomijają interwał wsadowy wewnętrznego klienta kondycji. Jak wspomniano wcześniej, Użyj tej flagi z opieką; Jeśli to możliwe, chcemy skorzystać z partii klientów kondycji.

## <a name="design-health-reporting"></a>Projektowanie raportowania kondycji
Pierwszym krokiem w generowaniu raportów wysokiej jakości jest zidentyfikowanie warunków, które mogą mieć wpływ na kondycję usługi. Dowolny warunek, który może pomóc w oflagowaniu problemów z usługą lub klastrem, gdy zostanie on uruchomiony lub jeszcze lepszy, zanim wystąpi problem — może potencjalnie zaoszczędzić miliardy dolarów. Korzyści obejmują mniej czasu, krótsze godziny spędzone na badaniu i naprawianiu problemów oraz lepsze zadowolenie klientów.

Po zidentyfikowaniu warunków moduły zapisujące alarmy muszą ustalić najlepszy sposób monitorowania ich pod kątem równowagi między obciążeniem i użytecznością. Rozważmy na przykład usługę, która wykonuje skomplikowane obliczenia, które używają niektórych plików tymczasowych w udziale. Licznik Alarm może monitorować udział w celu zapewnienia, że dostępna jest wystarczająca ilość miejsca. Może nasłuchiwać powiadomień o zmianach plików lub katalogów. Może zgłosić ostrzeżenie, gdy zostanie osiągnięty próg z góry, i zgłosić błąd, jeśli udział jest pełny. Na ostrzeżenie system naprawy może rozpocząć czyszczenie starszych plików w udziale. Po błędzie system naprawy może przenieść replikę usługi do innego węzła. Zwróć uwagę na to, jak Stany stanu są opisane w obszarze kondycji: stan warunku, który może być uznawany za zdrowy (ok) lub w złej kondycji (ostrzeżenie lub błąd).

Po ustawieniu szczegółów monitorowania moduł zapisujący licznika alarmowego musi ustalić, jak zaimplementować licznik alarmowy. Jeśli warunki można ustalić z poziomu usługi, licznik Alarm może być częścią monitorowanej usługi. Na przykład kod usługi może sprawdzić użycie udziału, a następnie zgłosić raport za każdym razem, gdy próbuje zapisać plik. Zaletą tego podejścia jest to, że raportowanie jest proste. Należy zachować ostrożność, aby zapobiec wpływowi błędów licznika alarmowego na działanie usługi.

Raportowanie z poziomu monitorowanej usługi nie zawsze jest opcją. Licznik alarmowy w ramach usługi może nie być w stanie wykryć warunków. Może nie mieć logiki lub danych, aby dokonać ustalenia. Obciążenie związane z monitorowaniem warunków może być wysokie. Warunki te mogą również nie być specyficzne dla usługi, ale mają wpływ na interakcje między usługami. Kolejną opcją jest posiadanie liczników alarmów w klastrze jako oddzielnych procesów. Licznik alarmy monitoruje warunki i Raport bez wpływu na główne usługi. Na przykład te alarmy mogą być implementowane jako usługi bezstanowe w tej samej aplikacji, wdrożone we wszystkich węzłach lub w tych samych węzłach co usługa.

Czasami licznik alarm działający w klastrze nie jest opcją. Jeśli monitorowany warunek to dostępność lub funkcjonalność usługi, która jest widoczna dla użytkowników, najlepiej jest mieć te alarmy w tym samym miejscu co komputery klienckie. W tym miejscu mogą testować operacje w taki sam sposób, w jaki użytkownicy je wywołują. Na przykład można mieć licznik alarmowy, który znajduje się poza klastrem, wysyła żądania do usługi i sprawdza opóźnienia i poprawność wyniku. (Na przykład w przypadku usługi kalkulatora program wykonuje 2 + 2 zwrócenie 4 w rozsądnym czasie?)

Po sfinalizowaniu szczegółów licznika alarmowego należy określić identyfikator źródła, który jednoznacznie identyfikuje go. Jeśli wiele licznika alarmów tego samego typu są żyjące w klastrze, muszą one zgłosić różne jednostki lub, jeśli są zgłaszane w tej samej jednostce, użyć innego identyfikatora źródła lub właściwości. W ten sposób ich raporty mogą współistnieć. Właściwość raportu kondycji powinna przechwytywać monitorowany warunek. (W powyższym przykładzie właściwość może być **ShareSize**). Jeśli wiele raportów ma zastosowanie do tego samego warunku, właściwość powinna zawierać pewne informacje dynamiczne, które umożliwiają współistnienie raportów. Na przykład jeśli trzeba monitorować wiele udziałów, nazwa właściwości może być **ShareSize-ShareName**.

> [!NOTE]
> *Nie* należy używać magazynu kondycji do przechowywania informacji o stanie. Tylko informacje dotyczące kondycji powinny być zgłaszane jako kondycja, ponieważ te informacje mają wpływ na ocenę kondycji jednostki. Magazyn kondycji nie został zaprojektowany jako magazyn ogólnego przeznaczenia. Używa logiki oceny kondycji do agregowania wszystkich danych do stanu kondycji. Wysyłanie informacji niezwiązanych z kondycją (na przykład stanu raportowania ze stanem kondycji OK) nie ma wpływu na zagregowany stan kondycji, ale może mieć negatywny wpływ na wydajność magazynu kondycji.
> 
> 

Następny punkt decyzyjny to jednostka do raportowania. W większości przypadków warunek jasno identyfikuje jednostkę. Wybierz jednostkę z najlepszymi możliwymi poziomami szczegółowości. Jeśli warunek ma wpływ na wszystkie repliki w partycji, raport na partycji, a nie w usłudze. Istnieją przypadki narożne, w przypadku których jest wymagana większa liczba myśli. Jeśli warunek ma wpływ na jednostkę, na przykład replikę, ale wolisz mieć warunek oflagowany przez więcej niż okres istnienia repliki, powinien on być raportowany na partycji. W przeciwnym razie po usunięciu repliki magazyn kondycji czyści wszystkie raporty. Moduły zapisujące alarmy muszą myśleć o okresach istnienia jednostki i raportu. Musi być jasne, gdy raport powinien zostać oczyszczony z magazynu (na przykład gdy błąd raportowany przez jednostkę nie ma już zastosowania).

Przyjrzyjmy się przykładowi łączącemu podane przeze mnie punkty. Rozważmy Service Fabric aplikację składającą się z głównej, stanowej usługi trwałej i dodatkowych usług bezstanowych wdrożonych na wszystkich węzłach (jeden typ usługi pomocniczej dla każdego typu zadania). Wzorzec zawiera kolejkę przetwarzania, która zawiera polecenia, które mają być wykonywane przez pomocnicze. Serwery pomocnicze wykonują żądania przychodzące i wysyłają sygnały potwierdzania zwrotnego. Jeden warunek, który może być monitorowany, to długość głównej kolejki przetwarzania. Jeśli długość kolejki głównej osiągnie wartość progową, zostanie zgłoszone ostrzeżenie. Ostrzeżenie wskazuje, że serwery pomocnicze nie mogą obsłużyć obciążenia. Jeśli kolejka osiągnie maksymalną długość i polecenia są porzucane, zgłaszany jest błąd, ponieważ nie można odzyskać usługi. Raporty mogą znajdować się we właściwości **QueueStatus**. Licznik alarm znajduje się w usłudze i jest okresowo wysyłany w głównej replice podstawowej. Czas wygaśnięcia wynosi dwie minuty, a okres próbny jest wysyłany co 30 sekund. Jeśli podstawowy przejdzie w dół, raport zostanie oczyszczony automatycznie ze sklepu. Jeśli replika usługi jest w stanie, ale jest zakleszczony lub występują inne problemy, raport wygasa w magazynie kondycji. W takim przypadku jednostką ocenia się przy błędzie.

Innym warunkiem, który może być monitorowany, jest czas wykonywania zadania. Główny serwer dystrybuuje zadania do serwerów pomocniczych w oparciu o typ zadania. W zależności od projektu wzorzec może sondować serwery pomocnicze dla stanu zadania. Może to również poczekać, aż serwery pomocnicze będą wysyłać sygnały potwierdzania zwrotnego po ich zakończeniu. W drugim przypadku należy zwrócić uwagę, aby wykrywać sytuacje, w których serwery lub komunikaty są tracone. Jedną z opcji jest to, aby wzorzec wysłał żądanie ping do tego samego elementu pomocniczego, co spowoduje wysłanie jego stanu. Jeśli stan nie zostanie odebrany, wzorzec traktuje go jako błąd i ponownie planuje zadanie. To zachowanie zakłada, że zadania są idempotentne.

Monitorowany warunek można przetłumaczyć jako ostrzeżenie, jeśli zadanie nie zostało wykonane w określonym czasie (**T1**, na przykład 10 minut). Jeśli zadanie nie zostało ukończone w czasie (**T2**, na przykład 20 minut), monitorowany warunek może być przetłumaczony jako błąd. Takie Raportowanie można wykonać na wiele sposobów:

* Główna replika podstawowa raportuje się okresowo. Dla wszystkich oczekujących zadań w kolejce można mieć jedną właściwość. Jeśli co najmniej jedno zadanie trwa dłużej, stan raportu w właściwości **PendingTasks** jest odpowiednio ostrzeżeniem lub błędem. Jeśli nie ma oczekujących zadań lub wszystkie zadania rozpoczęły wykonywanie, raport ma stan OK. Zadania są trwałe. Jeśli podstawowy przestanie być określony, nowo podwyższona wartość podstawowa będzie mogła kontynuować raportowanie.
* Inny proces licznika alarmowego (w chmurze lub zewnętrzny) sprawdza zadania (z zewnątrz, w oparciu o żądany wynik zadania), aby zobaczyć, czy zostały ukończone. Jeśli nie respektują progów, raport jest wysyłany w usłudze głównej. Raport jest również wysyłany do każdego zadania zawierającego identyfikator zadania, taki jak **PendingTask + taskId**. Raporty powinny być wysyłane tylko w Stanach złej kondycji. Skonfiguruj czas wygaśnięcia w ciągu kilku minut i Oznacz raporty do usunięcia po wygaśnięciu, aby upewnić się, że jest to możliwe.
* Pomocnicza, która wykonuje zadanie raportu, gdy jego uruchomienie trwa dłużej niż oczekiwano. Raport jest raportowany w wystąpieniu usługi we właściwości **PendingTasks**. Raport wykrywa wystąpienie usługi, które ma problemy, ale nie przechwytuje sytuacji, w której wystąpienie jest. Raporty są czyszczone. Może on zgłosić usługę pomocniczą. Jeśli pomocnicze ukończy zadanie, wystąpienie pomocnicze czyści raport ze sklepu. W raporcie nie są przechwytywane sytuacje, w których komunikat potwierdzający zostaje utracony, a zadanie nie zostało zakończone z punktu widzenia głównego.

Jednak raportowanie jest wykonywane w przypadkach opisanych powyżej, raporty są przechwytywane w usłudze Application Health w przypadku oceny kondycji.

## <a name="report-periodically-vs-on-transition"></a>Okresowe raportowanie w porównaniu do przejścia
Przy użyciu modelu raportowania kondycji alarmy mogą wysyłać raporty okresowo lub w przejściach. Zalecany sposób raportowania przy użyciu licznika alarmowego jest okresowo, ponieważ kod jest znacznie prostszy i mniej podatny na błędy. Alarmy muszą być tak proste, jak to możliwe, aby uniknąć błędów wyzwalających nieprawidłowe raporty. Niepoprawne raporty w *złej kondycji* wpływają na oceny kondycji i scenariusze na podstawie kondycji, w tym uaktualnień. Błędne raporty w *dobrej kondycji* ukrywają problemy w klastrze, co nie jest wymagane.

W przypadku raportowania okresowego licznik alarm można zaimplementować przy użyciu czasomierza. W przypadku wywołania zwrotnego czasomierza licznik alarmowy może sprawdzić stan i wysłać raport w oparciu o bieżący stan. Nie ma potrzeby, aby zobaczyć, który raport został wcześniej wysłany, lub dokonać optymalizacji pod kątem komunikatów. Klient kondycji ma logikę wsadową w celu zapewnienia wydajności. Gdy klient kondycji jest aktywny, ponawia próbę wewnętrznie do momentu potwierdzenia raportu przez magazyn kondycji lub licznik alarm generuje nowszy Raport z tą samą jednostką, właściwością i źródłem.

Raportowanie dotyczące przejść wymaga starannej obsługi stanu. Licznik alarm monitoruje pewne warunki i raporty tylko wtedy, gdy zmieniają się warunki. Do tego podejścia jest wymagana mniejsza liczba raportów. Minusem polega na tym, że logika licznika alarm jest złożona. Licznik alarm musi zachować warunki lub raporty, aby można je było sprawdzić, aby określić zmiany stanu. W przypadku przejścia w tryb failover należy zachować ostrożność w przypadku raportów, które zostały dodane, ale nie zostały jeszcze wysłane do magazynu kondycji. Numer sekwencyjny musi być coraz większy. W przeciwnym razie raporty są odrzucane jako przestarzałe. W rzadkich przypadkach, gdy nastąpi utrata danych, synchronizacja może być wymagana między stanem raportu a stanem magazynu kondycji.

Raportowanie dotyczące przejść ma sens dla usług, które są raportowane samodzielnie przez `Partition` lub `CodePackageActivationContext` . Po usunięciu obiektu lokalnego (repliki lub wdrożonego pakietu usługi/wdrożonej aplikacji) wszystkie jego raporty również zostaną usunięte. To automatyczne czyszczenie osłabi potrzebę synchronizacji między programem reporter a magazynem kondycji. Jeśli raport dotyczy partycji nadrzędnej lub aplikacji nadrzędnej, należy zachować ostrożność w przypadku przejścia w tryb failover, aby uniknąć starych raportów w magazynie kondycji. Należy dodać logikę, aby zachować poprawny stan, a następnie wyczyścić raport ze sklepu, gdy nie jest już wymagany.

## <a name="implement-health-reporting"></a>Implementowanie raportowania kondycji
Gdy szczegóły jednostki i raportu zostaną wyczyszczone, wysyłanie raportów kondycji można wykonać za pomocą interfejsu API, programu PowerShell lub REST.

### <a name="api"></a>Interfejs API
Aby zgłosić za pomocą interfejsu API, należy utworzyć raport kondycji specyficzny dla typu jednostki, w którym mają być zgłaszane. Przekaż raport klientowi kondycji. Alternatywnie możesz utworzyć informacje o kondycji i przekazać je, aby skorygować metody raportowania w programie `Partition` lub `CodePackageActivationContext` do raportów dotyczących bieżących jednostek.

Poniższy przykład przedstawia raportowanie okresowe z licznika alarmowego w klastrze. Licznik alarm sprawdza, czy można uzyskać dostęp do zasobu zewnętrznego z poziomu węzła. Zasób jest wymagany przez manifest usługi w aplikacji. Jeśli zasób jest niedostępny, inne usługi w aplikacji mogą nadal działać prawidłowo. W związku z tym raport jest wysyłany w ramach wdrożonego pakietu usługi co 30 sekund.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Wyślij raporty kondycji za pomocą elementu ***EntityType*-servicefabric HealthReport**.

Poniższy przykład pokazuje okresowe raportowanie wartości procesora CPU w węźle. Raporty powinny być wysyłane co 30 sekund, a ich czas trwania wynosi dwie minuty. Jeśli wygasną, zgłaszany jest problem, więc węzeł zostanie oceniony z błędem. Gdy CPU przekracza wartość progową, raport ma stan kondycji ostrzeżenie. Gdy procesor CPU pozostanie powyżej progu przez czas dłuższy niż skonfigurowany, jest raportowany jako błąd. W przeciwnym razie program reporter wyśle stan kondycji OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Poniższy przykład raportuje ostrzeżenie przejściowe na replice. Najpierw pobiera identyfikator partycji, a następnie identyfikator repliki dla interesującej Cię usługi. Następnie wysyła raport z **PowershellWatcher** na **ResourceDependency** właściwości. Raport jest interesujący tylko przez dwie minuty i jest automatycznie usuwany ze sklepu.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Wysyłać raporty kondycji przy użyciu protokołu REST z żądaniami POST, które przechodzą do żądanej jednostki i zawierają treść raportu kondycji. Na przykład Zobacz jak wysyłać [raporty kondycji klastra](/rest/api/servicefabric/report-the-health-of-a-cluster) REST lub [raporty kondycji usług](/rest/api/servicefabric/report-the-health-of-a-service). Wszystkie jednostki są obsługiwane.

## <a name="next-steps"></a>Następne kroki
W oparciu o dane dotyczące kondycji, moduły zapisujące usługi i Administratorzy klastrów/aplikacji mogą traktować metody korzystania z tych informacji. Można na przykład skonfigurować alerty na podstawie stanu kondycji, aby przechwytywać poważne problemy przed ich wystąpieniem. Administratorzy mogą również skonfigurować systemy naprawcze w celu automatycznego rozwiązywania problemów.

[Wprowadzenie do monitorowania kondycji Service Fabric](service-fabric-health-introduction.md)

[Wyświetlanie raportów o kondycji Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric uaktualniania aplikacji](service-fabric-application-upgrade.md)
