---
title: Konwertuj aplikacje platformy Azure Cloud Services na Service Fabric
description: Ten przewodnik porównuje Cloud Services ról Sieć Web i proces roboczy i Service Fabric usługi bezstanowe w celu ułatwienia migracji z Cloud Services do Service Fabric.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: cf593f793aabf2a0650684ed8d02fe02d756ec2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575741"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Przewodnik konwersji ról sieci Web i procesu roboczego w celu Service Fabric usług bezstanowych
W tym artykule opisano sposób migrowania ról Cloud Services sieci Web i procesu roboczego do Service Fabric usług bezstanowych. Jest to najprostsza ścieżka migracji z Cloud Services do Service Fabric dla aplikacji, których ogólna architektura ma być w przybliżeniu taka sama.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projekt usługi w chmurze do Service Fabric projektu aplikacji
 Projekt usługi w chmurze i projekt aplikacji Service Fabric mają podobną strukturę i obie reprezentują jednostkę wdrażania dla aplikacji — to oznacza, że każda z nich definiuje kompletny pakiet wdrożony w celu uruchomienia aplikacji. Projekt usługi w chmurze zawiera co najmniej jedną rolę sieci Web lub proces roboczy. Analogicznie, projekt aplikacji Service Fabric zawiera co najmniej jedną usługę. 

Różnica polega na tym, że projekt usługi w chmurze Couples wdrożenie aplikacji przy użyciu wdrożenia maszyny wirtualnej i w związku z tym zawiera ustawienia konfiguracji maszyny wirtualnej, podczas gdy projekt aplikacji Service Fabric definiuje tylko aplikację, która zostanie wdrożona w zestawie istniejących maszyn wirtualnych w klastrze Service Fabric. Sam klaster Service Fabric jest wdrażany tylko raz, za pomocą szablonu Menedżer zasobów lub przez Azure Portal i można do niego wdrożyć wiele aplikacji Service Fabric.

![Porównanie projektów Service Fabric i Cloud Services][3]

## <a name="worker-role-to-stateless-service"></a>Rola procesu roboczego do usługi bezstanowej
Koncepcyjnie rola proces roboczy reprezentuje bezstanowe obciążenie, co oznacza, że każde wystąpienie obciążenia jest identyczne, a żądania mogą być przesyłane do dowolnego wystąpienia w dowolnym momencie. Każde wystąpienie nie powinno zapamiętać poprzedniego żądania. Stan, w którym działa obciążenie, jest zarządzany przez zewnętrzny magazyn Stanów, taki jak Azure Table Storage lub Azure Cosmos DB. W Service Fabric tego typu obciążenie jest reprezentowane przez usługę bezstanową. Najprostszym podejściem do migrowania roli procesu roboczego do Service Fabric można dokonać, konwertując kod roli procesu roboczego na usługę bezstanową.

![Rola procesu roboczego do usługi bezstanowej][4]

## <a name="web-role-to-stateless-service"></a>Rola sieci Web do usługi bezstanowej
Podobnie jak w przypadku roli proces roboczy, rola sieci Web reprezentuje również obciążenie bezstanowe i dlatego można ją mapować do Service Fabric bezstanowej usługi. Jednak w przeciwieństwie do ról sieci Web Service Fabric nie obsługuje usług IIS. Aby przeprowadzić migrację aplikacji sieci Web z roli sieci Web do usługi bezstanowej, należy najpierw przenieść się do platformy sieci Web, która może być samodzielna, i nie zależy od usług IIS ani system. Web, takich jak ASP.NET Core 1.

| **Aplikacja** | **Obsługiwane** | **Ścieżka migracji** |
| --- | --- | --- |
| Formularze sieci Web ASP.NET |Nie |Konwertuj na ASP.NET Core 1 MVC |
| ASP.NET MVC |Z migracją |Uaktualnij do ASP.NET Core 1 MVC |
| ASP.NET Web API |Z migracją |Użyj samodzielnego serwera lub ASP.NET Core 1 |
| ASP.NET Core 1 |Tak |Nie dotyczy |

## <a name="entry-point-api-and-lifecycle"></a>Interfejs API punktu wejścia i cykl życia
Role proces roboczy i interfejsy API usługi Service Fabric oferują podobne punkty wejścia: 

| **Punkt wejścia** | **Rola procesu roboczego** | **Usługa Service Fabric** |
| --- | --- | --- |
| Przetwarzanie |`Run()` |`RunAsync()` |
| Uruchomienie maszyny wirtualnej |`OnStart()` |Nie dotyczy |
| Zatrzymanie maszyny wirtualnej |`OnStop()` |Nie dotyczy |
| Otwórz odbiornik dla żądań klientów |Nie dotyczy |<ul><li> `CreateServiceInstanceListener()` dla bezstanowego</li><li>`CreateServiceReplicaListener()` dla stanowych</li></ul> |

### <a name="worker-role"></a>Rola procesu roboczego
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Service Fabric bezstanowa usługa
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Oba mają podstawowe przesłonięcia "Run", w którym rozpocznie się przetwarzanie. Service Fabric Services łączą, `Run` `Start` i `Stop` w jeden punkt wejścia, `RunAsync` . Usługa powinna zacząć działać po `RunAsync` uruchomieniu i powinna przestać działać, gdy `RunAsync` CancellationToken metody jest sygnalizowane. 

Istnieje kilka najważniejszych różnic między cyklem życia a okresem istnienia ról procesów roboczych i usług Service Fabric:

* **Cykl życia:** Największą różnicą jest to, że rola proces roboczy jest maszyną wirtualną i dlatego jej cykl życia jest powiązany z maszyną wirtualną, która obejmuje zdarzenia dotyczące uruchamiania i zatrzymywania maszyny wirtualnej. Usługa Service Fabric ma cykl życia, który jest oddzielony od cyklu życia maszyny wirtualnej, dlatego nie obejmuje zdarzeń dla momentu uruchomienia i zatrzymania maszyny wirtualnej hosta, ponieważ nie są ze sobą powiązane.
* **Okres istnienia:** Wystąpienie roli procesu roboczego zostanie odtworzone, jeśli `Run` Metoda zostanie zakończona. `RunAsync`Metoda w Service Fabric może jednak działać do zakończenia, a wystąpienie usługi pozostanie niezmienione. 

Service Fabric udostępnia opcjonalny punkt wejścia konfiguracji komunikacji dla usług nasłuchujących żądań klientów. Zarówno RunAsync, jak i punkt wejścia komunikacji są opcjonalnymi zastąpieniami w ramach usług Service Fabricch — usługa może zdecydować się na nasłuchiwanie żądań klientów, lub uruchomić tylko pętlę przetwarzania, lub obie, co oznacza, że metoda RunAsync może wyjść bez ponownego uruchamiania wystąpienia usługi, ponieważ nadal mogą nasłuchiwać żądań klientów.

## <a name="application-api-and-environment"></a>Interfejs API i środowisko aplikacji
Interfejs API środowiska Cloud Services udostępnia informacje i funkcje dla bieżącego wystąpienia maszyny wirtualnej oraz informacje o innych wystąpieniach roli maszyny wirtualnej. Service Fabric zawiera informacje związane ze środowiskiem uruchomieniowym i niektóre informacje o węźle, w którym usługa jest aktualnie uruchomiona. 

| **Zadanie środowiska** | **Usługi w chmurze** | **Service Fabric** |
| --- | --- | --- |
| Ustawienia konfiguracji i powiadomienia o zmianie |`RoleEnvironment` |`CodePackageActivationContext` |
| Magazyn lokalny |`RoleEnvironment` |`CodePackageActivationContext` |
| Informacje o punkcie końcowym |`RoleInstance` <ul><li>Bieżące wystąpienie: `RoleEnvironment.CurrentRoleInstance`</li><li>Inne role i wystąpienia: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` dla bieżącego adresu węzła</li><li>`FabricClient` i `ServicePartitionResolver` dla odnajdywania punktu końcowego usługi</li> |
| Emulacja środowiska |`RoleEnvironment.IsEmulated` |Nie dotyczy |
| Jednoczesne zdarzenie zmiany |`RoleEnvironment` |Nie dotyczy |

## <a name="configuration-settings"></a>Ustawienia konfiguracji
Ustawienia konfiguracji w Cloud Services są ustawione dla roli maszyny wirtualnej i mają zastosowanie do wszystkich wystąpień tej roli maszyny wirtualnej. Te ustawienia to pary klucz-wartość ustawione w plikach ServiceConfiguration. *. cscfg i można uzyskać do nich dostęp bezpośrednio za poorednictwem RoleEnvironment. W Service Fabric ustawienia są stosowane indywidualnie dla każdej usługi i do poszczególnych aplikacji, a nie do maszyny wirtualnej, ponieważ maszyna wirtualna może obsługiwać wiele usług i aplikacji. Usługa składa się z trzech pakietów:

* **Kod:** zawiera pliki wykonywalne usługi, pliki binarne, biblioteki DLL i inne pliki, których usługa musi zostać uruchomiona.
* **Config:** wszystkie pliki konfiguracji i ustawienia dla usługi.
* **Dane:** statyczne pliki danych skojarzone z usługą.

Każdy z tych pakietów może mieć niezależnie wersja i uaktualnienie. Podobnie jak w przypadku Cloud Services, do pakietu konfiguracyjnego można uzyskać dostęp programowo za pomocą interfejsu API i dostępne są zdarzenia umożliwiające powiadomienie usługi o zmianie pakietu konfiguracji. Plik Settings.xml może służyć do konfiguracji wartości klucza i dostępu programowego podobnej do sekcji Ustawienia aplikacji w pliku App.config. Jednak w przeciwieństwie do Cloud Services pakiet konfiguracji Service Fabric może zawierać dowolne pliki konfiguracji w dowolnym formacie, niezależnie od tego, czy jest to plik XML, JSON, YAML czy niestandardowy format binarny. 

### <a name="accessing-configuration"></a>Uzyskiwanie dostępu do konfiguracji
#### <a name="cloud-services"></a>Cloud Services
Do ustawień konfiguracji z pliku ServiceConfiguration. *. cscfg można uzyskać dostęp za pośrednictwem programu `RoleEnvironment` . Te ustawienia są dostępne globalnie dla wszystkich wystąpień roli w ramach tego samego wdrożenia usługi w chmurze.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Każda usługa ma własny pojedynczy pakiet konfiguracji. Nie ma wbudowanego mechanizmu dla globalnych ustawień konfiguracji dostępnych dla wszystkich aplikacji w klastrze. W przypadku korzystania z Service Fabric specjalnego Settings.xml pliku konfiguracji w ramach pakietu konfiguracyjnego wartości w Settings.xml mogą być zastępowane na poziomie aplikacji, co umożliwia ustawienie konfiguracji na poziomie aplikacji.

Ustawienia konfiguracji są dostępne w ramach każdego wystąpienia usługi za pomocą usługi `CodePackageActivationContext` .

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Zdarzenia aktualizacji konfiguracji
#### <a name="cloud-services"></a>Cloud Services
To `RoleEnvironment.Changed` zdarzenie służy do powiadamiania wszystkich wystąpień roli w przypadku wystąpienia zmiany w środowisku, na przykład zmiany konfiguracji. Służy do korzystania z aktualizacji konfiguracji bez konieczności odtwarzania wystąpień roli lub ponownego uruchamiania procesu roboczego.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Każdy z trzech typów pakietów w kodzie usługi, konfiguracji i danych — zawiera zdarzenia powiadamiające wystąpienie usługi o aktualizacji, dodawaniu lub usunięciu pakietu. Usługa może zawierać wiele pakietów każdego typu. Na przykład usługa może mieć wiele pakietów konfiguracyjnych, niezależnie od wersji i uaktualniania. 

Te zdarzenia są dostępne do korzystania ze zmian w pakietach usług bez ponownego uruchamiania wystąpienia usługi.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Zadania uruchamiania
Zadania uruchamiania to akcje podejmowane przed uruchomieniem aplikacji. Zadanie uruchamiania jest zwykle używane do uruchamiania skryptów Instalatora przy użyciu podwyższonego poziomu uprawnień. Oba Cloud Services i Service Fabric obsługują zadania uruchamiania. Główną różnicą jest to, że w Cloud Services zadanie uruchamiania jest powiązane z maszyną wirtualną, ponieważ jest częścią wystąpienia roli, podczas gdy w Service Fabric zadanie uruchamiania jest powiązane z usługą, która nie jest powiązana z żadną określoną maszyną wirtualną.

| Service Fabric | Cloud Services |
| --- | --- |
| Lokalizacja konfiguracji |ServiceDefinition. csdef |
| Uprawnienia |"Limited" lub "podwyższony poziom" |
| Sekwencjonowanie |"proste", "tło", "pierwszy plan" |

### <a name="cloud-services"></a>Cloud Services
W Cloud Services dla roli w elemencie ServiceDefinition. csdef zostanie skonfigurowany punkt wejścia uruchamiania. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
W Service Fabric w ServiceManifest.xml skonfigurowano punkt wejścia uruchamiania dla usługi:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Uwaga dotycząca środowiska deweloperskiego
Zarówno Cloud Services, jak i Service Fabric są zintegrowane z programem Visual Studio z szablonami projektów i obsługują debugowanie, Konfigurowanie i wdrażanie zarówno lokalnie, jak i na platformie Azure. Zarówno Cloud Services, jak i Service Fabric udostępniają także lokalne środowisko środowiska uruchomieniowego. Różnica polega na tym, że podczas gdy środowisko uruchomieniowe usług w chmurze emuluje środowisko platformy Azure, w którym działa, Service Fabric nie korzysta z emulatora — używa kompletnego środowiska uruchomieniowego Service Fabric. Środowisko Service Fabric uruchamiane na lokalnym komputerze deweloperskim jest tym samym środowiskiem, które działa w środowisku produkcyjnym.

## <a name="next-steps"></a>Następne kroki
Przeczytaj więcej na temat Service Fabric Reliable Services i podstawowych różnic między architekturą aplikacji Cloud Services i Service Fabric, aby zrozumieć, jak korzystać z pełnego zestawu funkcji Service Fabric.

* [Wprowadzenie do Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Przewodnik koncepcyjny dotyczący różnic między Cloud Services i Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
