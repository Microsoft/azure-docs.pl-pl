---
title: Monitorowanie wydajności usługi Azure App Services | Microsoft Docs
description: Monitorowanie wydajności aplikacji dla usług Azure App Services. Załaduj wykres i czas odpowiedzi, informacje o zależnościach i ustaw alerty wydajności.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: e326f9764147b882a5009c53b9f13a3c3bd0bfc1
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875612"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorowanie wydajności usługi Azure App Service

Włączenie monitorowania na ASP.NET i opartych na ASP.NET Core aplikacjach sieci Web działających na [platformie Azure App Services](../../app-service/index.yml) jest teraz łatwiejsze niż kiedykolwiek wcześniej. Wcześniej trzeba było ręcznie zainstalować rozszerzenie witryny, dlatego najnowsze rozszerzenie/Agent jest teraz domyślnie wbudowane w obraz usługi App Service. W tym artykule opisano Włączanie monitorowania Application Insights oraz zamieszczono wstępne wskazówki dotyczące automatyzowania procesu wdrażania na dużą skalę.

> [!NOTE]
> Ręczne dodawanie rozszerzenia witryny Application Insights za pomocą **rozszerzeń narzędzi programistycznych**  >  **Extensions** jest przestarzałe. Ta metoda instalacji rozszerzenia była zależna od aktualizacji ręcznych dla każdej nowej wersji. Najnowsza stabilna wersja rozszerzenia jest teraz  [wstępnie zainstalowana](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako część obrazu App Service. Pliki znajdują się w `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` systemie i są automatycznie aktualizowane z każdą stabilną wersją. W przypadku korzystania z instrukcji opartych na agencie w celu włączenia monitorowania poniżej zostanie automatycznie usunięte przestarzałe rozszerzenie.

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Istnieją dwa sposoby włączania monitorowania aplikacji na platformie Azure App Services hostowanych aplikacjach:

* **Monitorowanie aplikacji oparte na agentach** (ApplicationInsightsAgent).  
    * Ta metoda jest najłatwiejsza do włączenia i nie jest wymagana żadna Konfiguracja zaawansowana. Jest on często określany jako "środowisko uruchomieniowe". W przypadku platformy Azure App Services zalecamy co najmniej włączenie tego poziomu monitorowania, a następnie na podstawie określonego scenariusza można sprawdzić, czy jest potrzebne bardziej zaawansowane monitorowanie za pomocą Instrumentacji ręcznej.

* **Ręczne Instrumentacja aplikacji za pomocą kodu** przez zainstalowanie zestawu SDK Application Insights.

    * Takie podejście jest znacznie bardziej dostosowywalne, ale wymaga [dodania zależności do pakietów NuGet zestawu Application Insights SDK](./asp-net.md). Ta metoda oznacza również, że konieczne jest zarządzanie aktualizacjami do najnowszej wersji pakietów.

    * Jeśli konieczne jest wykonywanie niestandardowych wywołań interfejsu API w celu śledzenia zdarzeń/zależności, które nie są przechwytywane domyślnie przy użyciu monitorowania opartego na agentach, należy użyć tej metody. Zapoznaj się z [interfejsem API dla niestandardowych zdarzeń i metryk](./api-custom-events-metrics.md) , aby dowiedzieć się więcej. Jest to również obecnie jedyną obsługiwaną opcją dla obciążeń opartych na systemie Linux.

> [!NOTE]
> W przypadku wykrycia zarówno usługi opartej na agencie, jak i ręcznie Instrumentacji opartej na zestawie SDK zostaną uznane tylko ustawienia Instrumentacji ręcznej. Ma to na celu uniemożliwienie wysyłania zduplikowanych danych. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [sekcją rozwiązywania problemów](#troubleshooting) poniżej.

## <a name="enable-agent-based-monitoring"></a>Włącz monitorowanie oparte na agentach

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Kombinacja APPINSIGHTS_JAVASCRIPT_ENABLED i urlCompression nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz wyjaśnienie w [sekcji Rozwiązywanie problemów](#troubleshooting).


1. **Wybierz pozycję Application Insights** w panelu sterowania platformy Azure dla usługi App Service.

    ![W obszarze Ustawienia wybierz pozycję Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Wybierz opcję utworzenia nowego zasobu, chyba że już skonfigurowano zasób Application Insights dla tej aplikacji. 

     > [!NOTE]
     > Po kliknięciu przycisku **OK** w celu utworzenia nowego zasobu zostanie wyświetlony monit o **zastosowanie ustawień monitorowania**. Wybranie pozycji **Kontynuuj** spowoduje połączenie nowego zasobu Application Insights z usługą App Service. spowoduje to również **ponowne uruchomienie usługi App Service**. 

     ![Instrumentacja aplikacji internetowej](./media/azure-web-apps/create-resource-01.png)

2. Po określeniu zasobu, który ma być używany, możesz wybrać, w jaki sposób aplikacja Application Insights ma zbierać dane na platformę dla aplikacji. Monitorowanie aplikacji ASP.NET jest domyślnie włączone z dwoma różnymi poziomami kolekcji.

    ![Zrzut ekranu przedstawia stronę Application Insights rozszerzeń witryny z wybraną opcją Utwórz nowy zasób.](./media/azure-web-apps/choose-options-new.png)
 
 Poniżej znajduje się podsumowanie danych zbieranych dla każdej trasy:
        
| Dane | Podstawowa kolekcja .NET | Zalecana kolekcja .NET |
| --- | --- | --- |
| Dodanie trendów użycia procesora, pamięci i operacji we/wy |Tak |Tak |
| Zbieranie trendów użycia z możliwością korelacji wyników dostępności z transakcjami | Tak |Tak |
| Zbieranie wyjątków nieobsłużonych przez proces hosta | Tak |Tak |
| Zwiększenie dokładności metryki APM pod obciążeniem, gdy jest używane próbkowanie | Tak |Tak |
| Korelowanie mikrousług ponad granicami żądań/zależności | Nie (tylko możliwości APM o pojedynczym wystąpieniu) |Tak |

3. Aby skonfigurować ustawienia, takie jak próbkowanie, które można wcześniej kontrolować za pomocą pliku applicationinsights.config, można teraz współdziałać z tymi samymi ustawieniami za pośrednictwem ustawień aplikacji z odpowiednim prefiksem. 

    * Na przykład aby zmienić procent początkowej próbki, można utworzyć ustawienie aplikacji: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` i wartość `100` .

    * Aby zapoznać się z listą obsługiwanych ustawień procesora telemetrii do próbkowania, możesz zajrzeć do [kodu](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) i [powiązanej dokumentacji](./sampling.md).

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Obsługiwane są następujące wersje programu .NET Core: ASP.NET Core 2,1, ASP.NET Core 2,2, ASP.NET Core 3,0, ASP.NET Core 3,1

Kierowanie całego środowiska z platformy .NET Core, samodzielnego wdrażania i aplikacji opartych na systemie Linux nie jest obecnie **obsługiwane** w przypadku monitorowania opartego na agentach/rozszerzeniach. ([Instrumentacja ręczna](./asp-net-core.md) za pośrednictwem kodu będzie działała we wszystkich poprzednich scenariuszach).

1. **Wybierz pozycję Application Insights** w panelu sterowania platformy Azure dla usługi App Service.

    ![W obszarze Ustawienia wybierz pozycję Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Wybierz opcję utworzenia nowego zasobu, chyba że już skonfigurowano zasób Application Insights dla tej aplikacji. 

     > [!NOTE]
     > Po kliknięciu przycisku **OK** w celu utworzenia nowego zasobu zostanie wyświetlony monit o **zastosowanie ustawień monitorowania**. Wybranie pozycji **Kontynuuj** spowoduje połączenie nowego zasobu Application Insights z usługą App Service. spowoduje to również **ponowne uruchomienie usługi App Service**. 

     ![Instrumentacja aplikacji internetowej](./media/azure-web-apps/create-resource-01.png)

2. Po określeniu zasobu, który ma być używany, można wybrać, w jaki sposób Application Insights zbierać dane na platformę dla aplikacji. Program .NET Core oferuje **zalecaną kolekcję** lub **wyłączony** dla ASP.NET Core 2,1, 2,2, 3,0 i 3,1.

    ![Wybierz opcje na platformę](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Z poziomu aplikacji internetowej App Service w obszarze **Ustawienia**  >  **Wybierz pozycję Application Insights**  >  **Włącz**. Monitorowanie oparte na agencie Node.js jest obecnie dostępne w wersji zapoznawczej.

# <a name="java"></a>[Java](#tab/java)

Aplikacje sieci Web oparte na App Service Java nie obsługują obecnie automatycznego monitorowania opartego na agentach i rozszerzeniach. Aby włączyć monitorowanie aplikacji Java, należy [ręcznie Instrumentacja aplikacji](./java-get-started.md).

# <a name="python"></a>[Python](#tab/python)

Aplikacje sieci Web oparte na App Service Python nie obsługują obecnie automatycznego monitorowania opartego na agentach i rozszerzeniach. Aby włączyć monitorowanie aplikacji w języku Python, musisz [ręcznie Instrumentacja aplikacji](./opencensus-python.md).

---

## <a name="enable-client-side-monitoring"></a>Włączanie monitorowania po stronie klienta

# <a name="net"></a>[.NET](#tab/net)

Monitorowanie po stronie klienta jest zgodą na ASP.NET. Aby włączyć monitorowanie po stronie klienta:

* **Ustawienia** **>** **Konfiguracja**
   * W obszarze Ustawienia aplikacji Utwórz **nowe ustawienie aplikacji**:

     Nazwa: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wartość: `true`

   * **Zapisz** ustawienia i **ponownie uruchom** aplikację.

Aby wyłączyć monitorowanie po stronie klienta, Usuń skojarzoną parę klucz wartość z ustawień aplikacji lub ustaw wartość na false.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Monitorowanie po stronie klienta jest **domyślnie włączone** w przypadku aplikacji .NET Core z **zalecaną kolekcją**, niezależnie od tego, czy jest obecne ustawienie aplikacji "APPINSIGHTS_JAVASCRIPT_ENABLED".

Jeśli z jakiegoś powodu chcesz wyłączyć monitorowanie po stronie klienta:

* **Ustawienia** **>** **Konfiguracja**
   * W obszarze Ustawienia aplikacji Utwórz **nowe ustawienie aplikacji**:

     Nazwij `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wartość: `false`

   * **Zapisz** ustawienia i **ponownie uruchom** aplikację.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Aby włączyć monitorowanie po stronie klienta dla aplikacji Node.js, musisz [ręcznie dodać zestaw JavaScript SDK po stronie klienta do swojej aplikacji](./javascript.md).

# <a name="java"></a>[Java](#tab/java)

Aby włączyć monitorowanie po stronie klienta dla aplikacji Java, musisz [ręcznie dodać zestaw JavaScript SDK po stronie klienta do swojej aplikacji](./javascript.md).

# <a name="python"></a>[Python](#tab/python)

Aby włączyć monitorowanie po stronie klienta dla aplikacji w języku Python, musisz [ręcznie dodać zestaw SDK JavaScript po stronie klienta do swojej aplikacji](./javascript.md).

---

## <a name="automate-monitoring"></a>Automatyzowanie monitorowania

Aby można było włączyć zbieranie danych telemetrycznych z Application Insights, należy ustawić tylko ustawienia aplikacji:

   ![App Service ustawienia aplikacji z dostępnymi ustawieniami Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definicje ustawień aplikacji

|Nazwa ustawienia aplikacji |  Definicja | Wartość |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Główne rozszerzenie, które kontroluje monitorowanie środowiska uruchomieniowego. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  W trybie domyślnym tylko funkcje podstawowe są włączane w celu zapewnienia optymalnej wydajności. | `default` lub `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Kontroluje, czy aparat zapisywania binarnego `InstrumentationEngine` zostanie włączony. To ustawienie ma wpływ na wydajność i wpływa na zimny czas uruchamiania/uruchamiania. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Określa, czy tekst tabeli programu SQL & Azure zostanie przechwycony wraz z wywołaniami zależności. Ostrzeżenie o wydajności: wpłynie to na zimny czas uruchamiania aplikacji. To ustawienie wymaga `InstrumentationEngine` . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>App Service ustawienia aplikacji z Azure Resource Manager

Ustawienia aplikacji dla App Services mogą być zarządzane i konfigurowane przy użyciu [szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Tej metody można użyć podczas wdrażania nowych zasobów App Service z automatyzacją Azure Resource Manager lub do modyfikowania ustawień istniejących zasobów.

Podstawowa struktura JSON ustawień aplikacji dla usługi App Service jest niższa:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Przykład szablonu Azure Resource Manager z ustawieniami aplikacji skonfigurowanymi dla Application Insights, ten [szablon](https://github.com/Andrew-MSFT/BasicImageGallery) może być przydatny, w odniesieniu do sekcji zaczynającej się od [wiersza 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatyzuj tworzenie zasobu Application Insights i Połącz się z nowo utworzonym App Service.

Aby utworzyć szablon Azure Resource Manager ze wszystkimi skonfigurowanymi domyślnymi ustawieniami Application Insights, Rozpocznij proces tak, jakby utworzyć nową aplikację internetową z włączonym Application Insightsem.

Wybieranie **opcji automatyzacji**

   ![App Service menu tworzenia aplikacji sieci Web](./media/azure-web-apps/create-web-app.png)

Ta opcja generuje najnowszy szablon Azure Resource Manager ze skonfigurowanymi wszystkimi wymaganymi ustawieniami.

  ![Szablon aplikacji sieci Web App Service](./media/azure-web-apps/arm-template.png)

Poniżej znajduje się przykład, Zamień wszystkie wystąpienia z  `AppMonitoredSite` nazwą swojej witryny:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Włączanie przy użyciu programu PowerShell

Aby można było włączyć monitorowanie aplikacji za pomocą programu PowerShell, należy zmienić tylko podstawowe ustawienia aplikacji. Poniżej znajduje się przykład, który umożliwia monitorowanie aplikacji w witrynie sieci Web o nazwie "AppMonitoredSite" w grupie zasobów "AppMonitoredRG" i konfiguruje dane do wysłania do klucza Instrumentacji "012345678-ABCD-EF01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Rozszerzenie/Agent monitorowania uaktualnienia

### <a name="upgrading-from-versions-289-and-up"></a>Uaktualnianie z wersji 2.8.9 i up

Uaktualnianie z wersji 2.8.9 odbywa się automatycznie, bez żadnych dodatkowych akcji. Nowe bity monitorowania są dostarczane w tle do docelowej usługi App Service, a po ponownym uruchomieniu aplikacji zostaną one pobrane.

Aby sprawdzić, która wersja rozszerzenia jest uruchomiona, odwiedź stronę `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Zrzut ekranu przedstawiający ścieżkę URL http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Uaktualnianie z wersji 1.0.0-2.6.5

Począwszy od wersji 2.8.9 jest używane wstępnie zainstalowane rozszerzenie witryny. Jeśli jesteś wcześniejszą wersją, możesz ją zaktualizować, korzystając z jednego z dwóch sposobów:

* [Uaktualnij przez włączenie za pośrednictwem portalu](#enable-application-insights). (Nawet jeśli masz rozszerzenie Application Insights dla Azure App Service zainstalowanych, interfejs użytkownika pokazuje tylko przycisk **Włącz** . W tle rozszerzenie starego witryny prywatnej zostanie usunięte.

* [Uaktualnianie przy użyciu programu PowerShell](#enabling-through-powershell):

    1. Ustaw ustawienia aplikacji, aby włączyć wstępnie zainstalowane rozszerzenie witryny ApplicationInsightsAgent. Zobacz [Włączanie przy użyciu programu PowerShell](#enabling-through-powershell).
    2. Ręcznie usuń rozszerzenie witryny prywatnej o nazwie Application Insights rozszerzenie dla Azure App Service.

Jeśli uaktualnienie zostało wykonane z wersji wcześniejszej niż 2.5.1, sprawdź, czy biblioteki DLL ApplicationInsigths są usuwane z folderu bin aplikacji, [Zobacz kroki rozwiązywania problemów](#troubleshooting).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono Przewodnik rozwiązywania problemów krok po kroku dotyczący monitorowania na podstawie rozszerzeń i programu .NET Core w przypadku aplikacji opartych na App Services platformie .NET.

> [!NOTE]
> Aplikacje Java są obsługiwane tylko na platformie Azure App Services za pośrednictwem ręcznie Instrumentacji opartej na zestawie SDK i dlatego poniższe kroki nie mają zastosowania do tych scenariuszy.

1. Sprawdź, czy aplikacja jest monitorowana za pośrednictwem programu `ApplicationInsightsAgent` .
    * Sprawdź, czy `ApplicationInsightsAgent_EXTENSION_VERSION` ustawienie aplikacji ma ustawioną wartość "~ 2".
2. Upewnij się, że aplikacja spełnia wymagania, które mają być monitorowane.
    * Przejdź do `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Zrzut ekranu przedstawiający https://yoursitename.scm.azurewebsites/applicationinsights stronę wyników](./media/azure-web-apps/app-insights-sdk-status.png)

    * Upewnij się, że `Application Insights Extension Status` jest `Pre-Installed Site Extension, version 2.8.12.1527, is running.` 
    * Jeśli nie jest uruchomiona, postępuj zgodnie z [instrukcjami dotyczącymi włączania monitorowania Application Insights](#enable-application-insights)

    * Upewnij się, że źródło stanu istnieje i wygląda następująco: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Jeśli podobna wartość nie jest obecna, oznacza to, że aplikacja nie jest aktualnie uruchomiona lub nie jest obsługiwana. Aby upewnić się, że aplikacja jest uruchomiona, spróbuj ręcznie odwiedzać adresy URL i punkty końcowe aplikacji, co umożliwi udostępnienie informacji o środowisku uruchomieniowym.

    * Potwierdź, że `IKeyExists` jest `true`
        * Jeśli tak jest `false` , Dodaj `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING` Identyfikator GUID iKey i skontaktuj się z nim w ustawieniach aplikacji.

    * Upewnij się, że nie ma żadnych wpisów dla `AppAlreadyInstrumented` , `AppContainsDiagnosticSourceAssembly` i `AppContainsAspNetTelemetryCorrelationAssembly` .
        * Jeśli którykolwiek z tych wpisów istnieje, Usuń następujące pakiety z aplikacji: `Microsoft.ApplicationInsights` , `System.Diagnostics.DiagnosticSource` , i `Microsoft.AspNet.TelemetryCorrelation` .

Poniższa tabela zawiera bardziej szczegółowy opis znaczenia tych wartości, ich podstawowych przyczyn i zalecanych poprawek:

|Wartość problemu|Objaśnienie|Wprowadzanie poprawek
|---- |----|---|
| `AppAlreadyInstrumented:true` | Ta wartość wskazuje, że rozszerzenie wykryło, że jakiś aspekt zestawu SDK jest już obecny w aplikacji i zostanie wycofany. Może to być spowodowane odwołaniem do `System.Diagnostics.DiagnosticSource` ,  `Microsoft.AspNet.TelemetryCorrelation` lub `Microsoft.ApplicationInsights`  | Usuń odwołania. Niektóre z tych odwołań są domyślnie dodawane z niektórych szablonów programu Visual Studio, a starsze wersje programu Visual Studio mogą dodawać odwołania do programu `Microsoft.ApplicationInsights` .
|`AppAlreadyInstrumented:true` | Jeśli aplikacja jest przeznaczona dla platformy .NET Core 2,1 lub 2,2 i odwołuje się do [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-Package, to jest Application Insights, a rozszerzenie zostanie wycofane. | Klienci korzystający z programu .NET Core 2.1, 2.2 [zalecają](https://github.com/aspnet/Announcements/issues/287) zamiast tego użycie metadanych Microsoft. AspNetCore. app.|
|`AppAlreadyInstrumented:true` | Ta wartość może być również spowodowana obecnością powyższych bibliotek DLL w folderze aplikacji z poprzedniego wdrożenia. | Wyczyść folder aplikacji, aby upewnić się, że te biblioteki DLL zostały usunięte. Sprawdź katalog bin aplikacji lokalnej oraz katalog wwwroot w App Service. (Aby sprawdzić katalog wwwroot aplikacji internetowej App Service: Narzędzia zaawansowane (kudu) > konsoli debugowania > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Ta wartość wskazuje, że rozszerzenie wykryło odwołania do `Microsoft.AspNet.TelemetryCorrelation` w aplikacji i zostanie wycofana. | Usuń odwołanie.
|`AppContainsDiagnosticSourceAssembly**:true`|Ta wartość wskazuje, że rozszerzenie wykryło odwołania do `System.Diagnostics.DiagnosticSource` w aplikacji i zostanie wycofana.| Usuń odwołanie.
|`IKeyExists:false`|Ta wartość wskazuje, że klucz instrumentacji nie występuje w element appSetting, `APPINSIGHTS_INSTRUMENTATIONKEY` . Możliwe przyczyny: te wartości mogły zostać przypadkowo usunięte, nie można ustawić wartości w skrypcie automatyzacji itd. | Upewnij się, że ustawienie jest obecne w ustawieniach aplikacji App Service.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED i urlCompression nie są obsługiwane

Jeśli używasz APPINSIGHTS_JAVASCRIPT_ENABLED = true w przypadkach, w których zawartość jest zakodowana, mogą wystąpić błędy takie jak: 

- błąd ponownego zapisywania adresu URL 500
- 500,53: błąd modułu ponownego zapisywania adresu URL z regułami ponownego zapisywania wychodzących komunikatów, gdy zawartość odpowiedzi HTTP jest zakodowana ("gzip"). 

Jest to spowodowane tym, że ustawienie aplikacji APPINSIGHTS_JAVASCRIPT_ENABLED ma wartość true, a Kodowanie zawartości jest obecne w tym samym czasie. Ten scenariusz nie jest jeszcze obsługiwany. Obejście polega na usunięciu APPINSIGHTS_JAVASCRIPT_ENABLED z ustawień aplikacji. Niestety, to oznacza, że jeśli Instrumentacja JavaScript klienta/przeglądarki jest nadal wymagana, ręczne odwołania do zestawu SDK są wymagane dla stron sieci Web. Postępuj zgodnie z [instrukcjami](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) dotyczącymi ręcznego Instrumentacji przy użyciu zestawu JavaScript SDK.

Aby uzyskać najnowsze informacje na temat Application Insights agenta/rozszerzenia, zapoznaj się z [informacjami o wersji](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Domyślna witryna internetowa wdrożona przy użyciu usługi Web Apps nie obsługuje automatycznego monitorowania po stronie klienta

Podczas tworzenia aplikacji sieci Web za pomocą programu `ASP.NET` lub `.NET Core` środowiska uruchomieniowego na platformie Azure App Services wdrażana jest pojedyncza statyczna strona HTML jako początkowa witryna sieci Web. Statyczna Strona sieci Web ładuje również zarządzane składniki sieci .NET w usługach IIS. Pozwala to na testowanie bezkodowego monitorowania po stronie serwera, ale nie obsługuje automatycznego monitorowania po stronie klienta.

Jeśli chcesz przetestować monitorowanie po stronie serwera i klienta dla ASP.NET lub ASP.NET Core w aplikacji sieci Web App Services platformy Azure, zalecamy wykonanie oficjalnych przewodników związanych z [tworzeniem aplikacji internetowej ASP.NET Core](../../app-service/quickstart-dotnetcore.md) i [Tworzenie aplikacji sieci Web ASP.NET Framework](../../app-service/quickstart-dotnet-framework.md) , a następnie użyj instrukcji w bieżącym artykule, aby włączyć monitorowanie.

### <a name="php-and-wordpress-are-not-supported"></a>PHP i WordPress nie są obsługiwane

Witryny PHP i WordPress nie są obsługiwane. Obecnie nie ma oficjalnie obsługiwanego zestawu SDK/agenta dla monitorowania po stronie serwera tych obciążeń. Jednak ręczne Instrumentacja transakcji po stronie klienta w środowisku PHP lub witrynie WordPress przez dodanie kodu JavaScript po stronie klienta do stron sieci Web można wykonać przy użyciu [zestawu SDK języka JavaScript](./javascript.md).

### <a name="connection-string-and-instrumentation-key"></a>Parametry połączenia i klucz Instrumentacji

Gdy jest używane monitorowanie bez kodu, wymagane są tylko parametry połączenia. Mimo że nadal zalecamy ustawienie klucza Instrumentacji w celu zachowania zgodności z poprzednimi wersjami w przypadku wykonywania Instrumentacji ręcznej.

## <a name="release-notes"></a>Informacje o wersji

Aby zapoznać się z najnowszymi aktualizacjami i poprawkami błędów [, zapoznaj się z informacjami o wersji](./web-app-extension-release-notes.md).

## <a name="next-steps"></a>Następne kroki
* [Uruchom profilera aplikacji na żywo](./profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) — monitorowanie usługi Azure Functions za pomocą usługi Application Insights
* [Włącz diagnostykę platformy Azure](../platform/diagnostics-extension-to-application-insights.md), która ma być wysyłana do usługi Application Insights.
* [Monitoruj metryki kondycji usługi](../platform/data-platform.md), aby upewnić się, że usługa jest dostępna i szybko reaguje.
* [Odbieraj powiadomienia o alertach](../platform/alerts-overview.md) zawsze, gdy wystąpią zdarzenia operacyjne lub metryki przekroczą próg.
* Użyj pozycji [Usługa Application Insights dla aplikacji JavaScript i stron sieci Web](javascript.md), aby pobrać telemetrię klienta z przeglądarek, w których odwiedzono stronę sieci Web.
* [Konfiguruj testy sieci Web dostępności](monitor-web-app-availability.md), aby otrzymywać alerty, gdy witryna nie działa.

