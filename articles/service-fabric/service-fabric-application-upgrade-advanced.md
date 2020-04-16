---
title: Zaawansowane tematy uaktualniania aplikacji
description: W tym artykule omówiono niektóre zaawansowane tematy dotyczące uaktualniania aplikacji sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: a12d2ec55bda95c1c61d4a73c76f4a777f4237f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414502"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uaktualnianie aplikacji sieci szkieletowej usług: zaawansowane tematy

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Dodawanie lub usuwanie typów usług podczas uaktualniania aplikacji

Jeśli nowy typ usługi zostanie dodany do opublikowanej aplikacji w ramach uaktualnienia, nowy typ usługi zostanie dodany do wdrożonej aplikacji. Takie uaktualnienie nie ma wpływu na żadne z wystąpień usługi, które były już częścią aplikacji, ale wystąpienie typu usługi, który został dodany musi zostać utworzony dla nowego typu usługi, aby być aktywne (zobacz [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Podobnie typy usług można usunąć z aplikacji w ramach uaktualnienia. Jednak wszystkie wystąpienia usługi typu usługi do usunięcia muszą zostać usunięte przed przystąpieniem do uaktualnienia (zobacz [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Unikanie spadków połączeń podczas planowanych przestojów usługi bezstanowej

W przypadku planowanych przestojów wystąpienia bezstanowego, takich jak uaktualnienie aplikacji/klastra lub dezaktywacja węzłów, połączenia mogą zostać przerwane z powodu dziennego punktu końcowego jest usuwany po wystąpieniu ulegnie upadkowi, co powoduje wymuszone zamknięcie połączenia.

Aby tego uniknąć, należy skonfigurować *requestdrain* (wersja zapoznawcza) funkcja, dodając *czas trwania opóźnienia zamknięcia wystąpienia* w konfiguracji usługi, aby umożliwić drenażu podczas odbierania żądań z innych usług w klastrze i używają reverse proxy lub przy użyciu interfejsu API rozpoznawania z modelu powiadomień do aktualizowania punktów końcowych. Gwarantuje to, że punkt końcowy anonsowany przez wystąpienie bezstanowe jest usuwany *przed* rozpoczęciem opóźnienia przed zamknięciem wystąpienia. To opóźnienie umożliwia istniejących żądań do drenażu bezpiecznie przed wystąpienie faktycznie idzie w dół. Klienci są powiadamiani o zmianie punktu końcowego przez funkcję wywołania zwrotnego w momencie uruchamiania opóźnienia, dzięki czemu mogą ponownie rozwiązać punkt końcowy i uniknąć wysyłania nowych żądań do wystąpienia, które przechodzi w dół.

### <a name="service-configuration"></a>Konfiguracja usługi

Istnieje kilka sposobów konfigurowania opóźnienia po stronie usługi.

 * **Podczas tworzenia nowej usługi**należy `-InstanceCloseDelayDuration`określić :

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **Podczas definiowania usługi w sekcji domyślne w manifeście aplikacji**przypisz `InstanceCloseDelayDurationSeconds` właściwość:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Podczas aktualizowania istniejącej**usługi `-InstanceCloseDelayDuration`należy określić:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>Konfiguracja klientów

Aby otrzymywać powiadomienia o zmianie punktu końcowego, klienci powinni zarejestrować wywołanie zwrotne, zobacz [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
Powiadomienie o zmianie jest wskazanie, że punkty końcowe uległy zmianie, klient powinien ponownie rozwiązać punkty końcowe i nie używać punktów końcowych, które nie są już anonsowane, ponieważ wkrótce slegają.

### <a name="optional-upgrade-overrides"></a>Opcjonalne zastąpienia uaktualnień

Oprócz ustawienia domyślnych czasów trwania opóźnienia na usługę, można również zastąpić opóźnienie podczas`InstanceCloseDelayDurationSec`uaktualniania aplikacji/klastra przy użyciu tej samej opcji ( ):

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

Czas trwania opóźnienia dotyczy tylko wywoływane wystąpienie uaktualnienia i w przeciwnym razie nie zmienia poszczególnych konfiguracji opóźnienia usługi. Na przykład można użyć tego, aby `0` określić opóźnienie w celu pominięcia wszelkich wstępnie skonfigurowanych opóźnień uaktualnienia.

> [!NOTE]
> Ustawienie opróżniania żądań nie jest honorowane dla żądań z modułu Azure Load balancer. Ustawienie nie są honorowane, jeśli usługa wywołująca używa rozwiązania opartego na reklamacji.
>
>

> [!NOTE]
> Tę funkcję można skonfigurować w istniejących usługach przy użyciu polecenia cmdlet Update-ServiceFabricService, jak wspomniano powyżej, gdy wersja kodu klastra jest 7.1.XXX lub wyższa.
>
>

## <a name="manual-upgrade-mode"></a>Tryb ręcznego uaktualniania

> [!NOTE]
> Tryb *Monitored* monitorowania uaktualnienia jest zalecany dla wszystkich uaktualnień sieci szkieletowej usług.
> Tryb uaktualniania *UnmonitoredManual* należy wziąć pod uwagę tylko w przypadku uaktualnień, które nie powiodły się lub zawieszone. 
>
>

W trybie *monitorowanym* sieci szkieletowej usług stosuje zasady kondycji, aby upewnić się, że aplikacja jest w dobrej kondycji w miarę postępu uaktualniania. Jeśli zasady dotyczące kondycji zostaną naruszone, uaktualnienie zostanie zawieszone lub automatycznie wycofane w zależności od określonego *failureAction*.

W *trybie niemonitorowanymarządzenie* administrator aplikacji ma całkowitą kontrolę nad postępem uaktualnienia. Ten tryb jest przydatny podczas stosowania niestandardowych zasad oceny kondycji lub wykonywania niekonwencjonalnych uaktualnień w celu całkowitego obejścia monitorowania kondycji (np. aplikacja jest już w utracie danych). Uaktualnienie uruchomione w tym trybie zawiesza się po zakończeniu każdego UD i musi zostać jawnie wznowione za pomocą [Resume-ServiceFabricApplicationUpligrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Gdy uaktualnienie zostanie zawieszone i gotowe do wznowienia przez użytkownika, jego stan uaktualnienia pokaże *RollforwardPending* (zobacz [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Na koniec tryb *UnmonitoredAuto* jest przydatny do wykonywania iteracji szybkiego uaktualniania podczas tworzenia lub testowania usługi, ponieważ nie jest wymagane żadne dane wejściowe użytkownika i nie są oceniane żadne zasady kondycji aplikacji.

## <a name="upgrade-with-a-diff-package"></a>Uaktualnij za pomocą pakietu różnicowego

Zamiast inicjowania obsługi administracyjnej kompletny pakiet aplikacji, uaktualnienia mogą być również wykonywane przez inicjowanie obsługi administracyjnej pakietów różnicowych, które zawierają tylko zaktualizowane pakiety kodu/konfiguracji/danych wraz z manifestem pełnej aplikacji i manifestami usługi complete. Kompletne pakiety aplikacji są wymagane tylko do początkowej instalacji aplikacji w klastrze. Kolejne uaktualnienia mogą pochodzić z kompletnych pakietów aplikacji lub pakietów różnicowych.  

Wszelkie odwołania w manifestu aplikacji lub manifestów usługi pakietu różnicowego, który nie można znaleźć w pakiecie aplikacji jest automatycznie zastępowany aktualnie aprowizowana wersja.

Scenariusze dotyczące używania pakietu różnicowego są:

* Jeśli masz duży pakiet aplikacji, który odwołuje się do kilku plików manifestu usługi i/lub kilku pakietów kodu, pakietów konfiguracyjnych lub pakietów danych.
* Gdy masz system wdrażania, który generuje układ kompilacji bezpośrednio z procesu kompilacji aplikacji. W takim przypadku, mimo że kod nie został zmieniony, nowo utworzone zestawy uzyskać inną sumę kontrolną. Przy użyciu pełnego pakietu aplikacji wymagałoby aktualizacji wersji na wszystkich pakietów kodu. Za pomocą pakietu różnicowego, należy podać tylko pliki, które uległy zmianie i pliki manifestu, w którym wersja została zmieniona.

Gdy aplikacja jest uaktualniany przy użyciu programu Visual Studio, pakiet różnicowy jest publikowany automatycznie. Aby ręcznie utworzyć pakiet różnicowy, manifest aplikacji i manifesty usługi muszą zostać zaktualizowane, ale tylko zmienione pakiety powinny być uwzględnione w pakiecie aplikacji końcowej.

Na przykład zacznijmy od następującej aplikacji (numery wersji przewidziane dla ułatwienia zrozumienia):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Załóżmy, że chcesz zaktualizować tylko pakiet kodu service1 przy użyciu pakietu różnicowego. Zaktualizowana aplikacja ma następujące zmiany wersji:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

W takim przypadku należy zaktualizować manifest aplikacji do 2.0.0 i manifest usługi dla service1, aby odzwierciedlić aktualizację pakietu kodu. Folder pakietu aplikacji będzie miał następującą strukturę:

```text
app1/
  service1/
    code/
```

Innymi słowy, należy utworzyć kompletny pakiet aplikacji normalnie, a następnie usunąć wszelkie code/config/data package foldery, dla których wersja nie uległa zmianie.

## <a name="upgrade-application-parameters-independently-of-version"></a>Uaktualnianie parametrów aplikacji niezależnie od wersji

Czasami jest pożądane, aby zmienić parametry aplikacji sieci szkieletowej usług bez zmiany wersji manifestu. Można to zrobić wygodnie przy użyciu **-ApplicationParameter** flagi z **Start-ServiceFabricApplicationUplicationUpgrade** Azure Service Fabric PowerShell polecenia cmdlet. Załóżmy, że aplikacja sieci szkieletowej usług z następującymi właściwościami:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Teraz uaktualnić aplikację przy użyciu polecenia cmdlet **Start-ServiceFabricApplicationUplication.** W tym przykładzie pokazano monitorowane uaktualnienie, ale można również użyć uaktualnienia niemonitorowanego. Aby zobaczyć pełny opis flag akceptowanych przez to polecenie cmdlet, zobacz [odwołanie do modułu powershell sieci szkieletowej usług Azure, zobacz odwołanie do modułu usługi Azure Service Fabric PowerShell](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Po uaktualnieniu upewnij się, że aplikacja ma zaktualizowane parametry i tę samą wersję:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Wycofywanie uaktualnień aplikacji

Podczas gdy aktualizacje mogą być przewijane do przodu w jednym z trzech trybów *(Monitored,* *UnmonitoredAuto*lub *UnmonitoredManual),* mogą być przywracane tylko w trybie *UnmonitoredAuto* lub *UnmonitorManual.* Wycofywanie w trybie *UnmonitoredAuto* działa tak samo jak toczenia do przodu z wyjątkiem, że domyślna wartość *UpgradeReplicaSetCheckTimeout* jest inna - zobacz [Parametry uaktualnienia aplikacji](service-fabric-application-upgrade-parameters.md). Wycofywanie w *trybie UnmonitoredManual* działa tak samo jak toczenia do przodu — wycofywanie zawiesi się po zakończeniu każdego UD i musi być jawnie wznowione przy użyciu [Resume-ServiceFabricApplicationUpgrade,](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) aby kontynuować wycofywanie.

Wycofywanie może być wyzwalane automatycznie po naruszeniu zasad kondycji uaktualnienia w trybie *monitorowanym* z *awariąAk* *wycofywania* (zobacz [Parametry uaktualnienia aplikacji)](service-fabric-application-upgrade-parameters.md)lub jawnie przy użyciu [funkcji Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Podczas wycofywania wartość *UpgradeReplicaSetCheckTimeout* i tryb można jeszcze zmienić w dowolnym momencie za pomocą [Update-ServiceFabricApplicationUpligrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Upewnij się, że uaktualnienia aplikacji są zgodne, ucząc się, jak korzystać z [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Rozwiąż typowe problemy w uaktualnieniach aplikacji, odwołując się do kroków [rozwiązywania problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
