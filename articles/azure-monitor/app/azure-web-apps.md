---
title: Monitorowanie wydajności usług aplikacji platformy Azure | Dokumenty firmy Microsoft
description: Monitorowanie wydajności aplikacji dla usług aplikacji platformy Azure. Czas ładowania wykresu i odpowiedzi, informacje o zależnościach i ustawianie alertów dotyczących wydajności.
ms.topic: conceptual
ms.date: 12/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: dd0d3be6ed7e5185183618cc2bdeff5ee8d749f3
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729805"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorowanie wydajności usługi Azure App Service

Włączenie monitorowania aplikacji sieci web opartych na ASP.NET i ASP.NET Core uruchomionych w [usługach Azure App Services](https://docs.microsoft.com/azure/app-service/) jest teraz łatwiejsze niż kiedykolwiek. Podczas gdy wcześniej trzeba było ręcznie zainstalować rozszerzenie witryny, najnowsze rozszerzenie/agent jest teraz domyślnie wbudowany w obraz usługi aplikacji. W tym artykule zostanie przewodnik po włączaniu monitorowania usługi Application Insights, a także zapewni wstępne wskazówki dotyczące automatyzacji procesu wdrożeń na dużą skalę.

> [!NOTE]
> Ręczne dodawanie rozszerzenia witryny usługi Application Insights za pośrednictwem > **rozszerzeń** **narzędzi programistycznych**jest przestarzałe. Ta metoda instalacji rozszerzenia była zależna od ręcznych aktualizacji dla każdej nowej wersji. Najnowsza stabilna wersja rozszerzenia jest teraz [preinstalowana](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako część obrazu usługi App Service. Pliki znajdują się `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` w i są automatycznie aktualizowane z każdym stabilnym wydaniu. Jeśli zastosujesz się do instrukcji opartych na agencie, aby włączyć monitorowanie poniżej, automatycznie usunie przestarzałe rozszerzenie dla Ciebie.

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Istnieją dwa sposoby włączania monitorowania aplikacji dla aplikacji hostowanych usługi Azure App Services:

* **Monitorowanie aplikacji oparte na agentach** (ApplicationInsightsAgent).  
    * Ta metoda jest najłatwiejsza do włączenia i nie jest wymagana żadna zaawansowana konfiguracja. Jest często określane jako monitorowanie "środowiska uruchomieniowego". W przypadku usług Azure App Services zaleca się co najmniej włączenie tego poziomu monitorowania, a następnie na podstawie określonego scenariusza można ocenić, czy bardziej zaawansowane monitorowanie za pomocą instrumentacji ręcznej jest potrzebne.

* **Ręczne instrumentowanie aplikacji za pomocą kodu** przez zainstalowanie zestawu SDK usługi Application Insights.

    * Takie podejście jest znacznie bardziej konfigurowalne, ale wymaga [dodania zależności od pakietów NuGet SDK SDK aplikacji.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) Ta metoda oznacza również, że musisz samodzielnie zarządzać aktualizacjami do najnowszej wersji pakietów.

    * Jeśli musisz wykonać niestandardowe wywołania interfejsu API do śledzenia zdarzeń/zależności nie przechwycone domyślnie z monitorowania opartego na agentach, należy użyć tej metody. Zapoznaj się z [interfejsem API dla zdarzeń niestandardowych i metryki artykuł,](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) aby dowiedzieć się więcej. Jest to również obecnie jedyna obsługiwana opcja dla obciążeń opartych na systemie Linux.

> [!NOTE]
> Jeśli zostanie wykryte zarówno monitorowanie oparte na agentach, jak i ręczne instrumentamencie oparte na zestawie SDK, honorowane będą tylko ustawienia instrumentacji ręcznej. Ma to zapobiec wysyłaniu zduplikowanych danych. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [poniższą sekcją rozwiązywania problemów.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)

## <a name="enable-agent-based-monitoring"></a>Włączanie monitorowania opartego na agentach

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Połączenie APPINSIGHTS_JAVASCRIPT_ENABLED i urlCompression nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz wyjaśnienie w [sekcji rozwiązywania problemów](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Wybierz usługę Application Insights** w panelu sterowania platformy Azure dla usługi aplikacji.

    ![W obszarze Ustawienia wybierz pozycję Usługa Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Wybierz, aby utworzyć nowy zasób, chyba że już skonfigurować zasób usługi Application Insights dla tej aplikacji. 

     > [!NOTE]
     > Po kliknięciu przycisku **OK** w celu utworzenia nowego zasobu zostanie wyświetlony monit o **zastosowanie ustawień monitorowania**. Wybranie opcji **Kontynuuj** spowoduje połączenie nowego zasobu usługi Application Insights z usługą aplikacji, co spowoduje również **ponowne uruchomienie usługi aplikacji.** 

     ![Instrumentacja aplikacji internetowej](./media/azure-web-apps/create-resource-01.png)

2. Po określeniu zasobu, który ma być używany, można wybrać sposób, w jaki wgląd w aplikacje ma zbierać dane na platformę dla aplikacji. ASP.NET monitorowanie aplikacji jest domyślnie włączone z dwoma różnymi poziomami kolekcji.

    ![Wybierz opcje na platformę](./media/azure-web-apps/choose-options-new.png)
 
 Poniżej znajduje się podsumowanie danych zebranych dla każdej trasy:
        
|  | Kolekcja podstawowa platformy .NET | .NET Zalecana kolekcja |
| --- | --- | --- |
| Dodanie trendów użycia procesora, pamięci i operacji we/wy |Tak |Tak |
| Zbieranie trendów użycia z możliwością korelacji wyników dostępności z transakcjami | Tak |Tak |
| Zbieranie wyjątków nieobsłużonych przez proces hosta | Tak |Tak |
| Zwiększenie dokładności metryki APM pod obciążeniem, gdy jest używane próbkowanie | Tak |Tak |
| Korelowanie mikrousług ponad granicami żądań/zależności | Nie (tylko możliwości APM z pojedynczym wystąpieniem) |Tak |

3. Aby skonfigurować ustawienia, takie jak próbkowanie, które można wcześniej kontrolować za pośrednictwem pliku applicationinsights.config, można teraz wchodzić w interakcje z tymi samymi ustawieniami za pomocą ustawień aplikacji z odpowiednim prefiksem. 

    * Na przykład, aby zmienić początkową wartość procentową `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` próbkowania, `100`można utworzyć ustawienie aplikacji: i wartość .

    * Aby uzyskać listę obsługiwanych adaptacyjnych ustawień procesora telemetrii próbkowania, można zapoznać się z [kodem](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) i [powiązaną dokumentacją](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Obsługiwane są następujące wersje programu .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

Kierowanie pełnej struktury z platformy .NET Core, samodzielne wdrażanie i aplikacje oparte na systemie Linux nie są obecnie **obsługiwane za** pomocą monitorowania opartego na agentach/rozszerzeniach. [(Ręczne oprzyrządowanie](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) za pomocą kodu będzie działać we wszystkich poprzednich scenariuszach.)

1. **Wybierz usługę Application Insights** w panelu sterowania platformy Azure dla usługi aplikacji.

    ![W obszarze Ustawienia wybierz pozycję Usługa Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Wybierz, aby utworzyć nowy zasób, chyba że już skonfigurować zasób usługi Application Insights dla tej aplikacji. 

     > [!NOTE]
     > Po kliknięciu przycisku **OK** w celu utworzenia nowego zasobu zostanie wyświetlony monit o **zastosowanie ustawień monitorowania**. Wybranie opcji **Kontynuuj** spowoduje połączenie nowego zasobu usługi Application Insights z usługą aplikacji, co spowoduje również **ponowne uruchomienie usługi aplikacji.** 

     ![Instrumentacja aplikacji internetowej](./media/azure-web-apps/create-resource-01.png)

2. Po określeniu zasobu, który ma być używany, można wybrać sposób, w jaki usługa Application Insights ma zbierać dane na platformę dla aplikacji. .NET Core oferuje **zalecane zbieranie** lub **wyłączone** dla .NET Core 2.0, 2.1, 2.2 i 3.0.

    ![Wybierz opcje na platformę](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Z poziomu aplikacji sieci Web usługi App Service w obszarze **Ustawienia** > **wybierz pozycję Application Insights** > **Enable**. Monitorowanie oparte na agencie node.js jest obecnie w wersji zapoznawczej.

# <a name="java"></a>[Java](#tab/java)

Aplikacje internetowe oparte na usłudze Java App Service nie obsługują obecnie automatycznego monitorowania opartego na agentach/rozszerzeniach. Aby włączyć monitorowanie aplikacji Java, należy [ręcznie przyrządzać aplikację](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started).

# <a name="python"></a>[Python](#tab/python)

Aplikacje internetowe oparte na usłudze Python App Service nie obsługują obecnie automatycznego monitorowania opartego na agentach/rozszerzeniach. Aby włączyć monitorowanie aplikacji Języka Python, należy [ręcznie przyrządzać aplikację](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

---

## <a name="enable-client-side-monitoring"></a>Włączanie monitorowania po stronie klienta

# <a name="net"></a>[.NET](#tab/net)

Monitorowanie po stronie klienta jest opt-in dla ASP.NET. Aby włączyć monitorowanie po stronie klienta:

* Wybierz **ustawienia** >** **Ustawienia aplikacji****
   * W obszarze Ustawienia aplikacji dodaj nową nazwę i **wartość** **ustawienia aplikacji:**

     Nazwa:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wartość:`true`

   * **Zapisz** ustawienia i **ponownie uruchom** aplikację.

![Zrzut ekranu przedstawiający interfejs użytkownika ustawień aplikacji](./media/azure-web-apps/appinsights-javascript-enabled.png)

Aby wyłączyć monitorowanie po stronie klienta albo usunąć parę skojarzonych wartości klucza z ustawień aplikacji lub ustawić wartość false.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Monitorowanie po stronie klienta jest **domyślnie włączone** dla aplikacji .NET Core z **zalecaną kolekcją**, niezależnie od tego, czy ustawienie aplikacji "APPINSIGHTS_JAVASCRIPT_ENABLED" jest obecne.

Jeśli z jakiegoś powodu chcesz wyłączyć monitorowanie po stronie klienta:

* Wybieranie **ustawień** > **ustawień aplikacji**
   * W obszarze Ustawienia aplikacji dodaj nową nazwę i **wartość** **ustawienia aplikacji:**

     Nazwa:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wartość:`false`

   * **Zapisz** ustawienia i **ponownie uruchom** aplikację.

![Zrzut ekranu przedstawiający interfejs użytkownika ustawień aplikacji](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Aby włączyć monitorowanie po stronie klienta aplikacji Node.js, należy [ręcznie dodać sdk JavaScript po stronie klienta do aplikacji](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="java"></a>[Java](#tab/java)

Aby włączyć monitorowanie po stronie klienta dla aplikacji Java, należy [ręcznie dodać sdk JavaScript po stronie klienta do aplikacji](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="python"></a>[Python](#tab/python)

Aby włączyć monitorowanie po stronie klienta dla aplikacji Języka Python, należy [ręcznie dodać sdk JavaScript po stronie klienta do aplikacji](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

---

## <a name="automate-monitoring"></a>Automatyzacja monitorowania

Aby włączyć zbieranie danych telemetrycznych za pomocą usługi Application Insights, należy ustawić tylko ustawienia aplikacji:

   ![Ustawienia aplikacji usługi App Service z dostępnymi ustawieniami usługi Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definicje ustawień aplikacji

|Nazwa ustawienia aplikacji |  Definicja | Wartość |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Główne rozszerzenie, które kontroluje monitorowanie środowiska uruchomieniowego. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Tylko w trybie domyślnym podstawowe funkcje są włączone w celu zapewnienia optymalnej wydajności. | `default` lub `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Określa, czy aparat `InstrumentationEngine` ponownego przepisywania binarnego zostanie włączony. To ustawienie ma wpływ na wydajność i wpływa na czas zimnego rozruchu/uruchamiania. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Określa, czy tekst tabeli sql & Azure zostanie przechwycony wraz z wywołaniami zależności. Ostrzeżenie o wydajności: wpłynie to na czas rozruchu na zimno aplikacji. To ustawienie `InstrumentationEngine`wymaga pliku . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Ustawienia aplikacji usługi App Service w usłudze Azure Resource Manager

Ustawieniami aplikacji dla usług App Services można zarządzać i konfigurować za pomocą [szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Tej metody można użyć podczas wdrażania nowych zasobów usługi App Service za pomocą automatyzacji usługi Azure Resource Manager lub do modyfikowania ustawień istniejących zasobów.

Podstawowa struktura ustawień aplikacji JSON dla usługi aplikacji znajduje się poniżej:

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

Na przykład szablonu usługi Azure Resource Manager z ustawieniami aplikacji skonfigurowanymi dla usługi Application Insights, ten [szablon](https://github.com/Andrew-MSFT/BasicImageGallery) może być pomocny, w szczególności sekcja rozpoczynająca się od [wiersza 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Zautomatyzuj tworzenie zasobu usługi Application Insights i łącze do nowo utworzonej usługi app service.

Aby utworzyć szablon usługi Azure Resource Manager ze wszystkimi domyślnymi ustawieniami usługi Application Insights skonfigurowane, należy rozpocząć proces tak, jakby zamierzał utworzyć nową aplikację sieci Web z włączoną usługą Application Insights.

Wybierz **opcje automatyzacji**

   ![Menu tworzenia aplikacji sieci Web usługi App Service](./media/azure-web-apps/create-web-app.png)

Ta opcja generuje najnowszy szablon usługi Azure Resource Manager ze wszystkimi wymaganymi ustawieniami skonfigurowane.

  ![Szablon aplikacji sieci Web usługi App Service](./media/azure-web-apps/arm-template.png)

Poniżej znajduje się przykład, `AppMonitoredSite` zastąp wszystkie wystąpienia nazwą witryny:

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

### <a name="enabling-through-powershell"></a>Włączanie za pomocą programu PowerShell

Aby umożliwić monitorowanie aplikacji za pośrednictwem programu PowerShell, należy zmienić tylko podstawowe ustawienia aplikacji. Poniżej znajduje się przykład, który umożliwia monitorowanie aplikacji dla strony internetowej o nazwie "AppMonitoredSite" w grupie zasobów "AppMonitoredRG", i konfiguruje dane, które mają być wysyłane do "012345678-abcd-ef01-2345-6789abcd" klucz instrumentacji.

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

## <a name="upgrade-monitoring-extensionagent"></a>Rozszerzenie/agent monitorowania uaktualnienia

### <a name="upgrading-from-versions-289-and-up"></a>Uaktualnianie z wersji 2.8.9 i nowszej

Uaktualnienie z wersji 2.8.9 odbywa się automatycznie, bez żadnych dodatkowych działań. Nowe bity monitorowania są dostarczane w tle do usługi aplikacji docelowej, a po ponownym uruchomieniu aplikacji zostaną pobrane.

Aby sprawdzić, która wersja rozszerzenia jest uruchomiona, odwiedź`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Zrzut ekranu przedstawiający ścieżkę adresu URLhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Uaktualnienie z wersji 1.0.0 - 2.6.5

Począwszy od wersji 2.8.9, używane jest fabrycznie zainstalowane rozszerzenie lokacji. Jeśli jesteś starszą wersją, możesz zaktualizować za pomocą jednego z dwóch sposobów:

* [Uaktualnij, włączając za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Nawet jeśli masz zainstalowane rozszerzenie usługi Application Insights dla usługi Azure App Service, interfejs użytkownika pokazuje tylko przycisk **Włącz.** Za kulisami stare prywatne rozszerzenie witryny zostanie usunięte.)

* [Uaktualnij za pomocą programu PowerShell:](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)

    1. Ustaw ustawienia aplikacji, aby włączyć wstępnie zainstalowane rozszerzenie witryny ApplicationInsightsAgent. Zobacz [Włączanie za pomocą programu PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Ręcznie usuń rozszerzenie lokacji prywatnej o nazwie Application Insights extension for Azure App Service.

Jeśli uaktualnienie odbywa się z wersji wcześniejszej niż 2.5.1, sprawdź, czy biblioteki DLL ApplicationInsigths są usuwane z folderu pojemnika [aplikacji, zobacz kroki rozwiązywania problemów](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej znajduje się nasz przewodnik po rozwiązywaniu problemów krok po kroku dla monitorowania opartego na rozszerzeniu/agencie dla aplikacji opartych na platformie .NET i .NET Core uruchomionych w usługach Azure App Services.

> [!NOTE]
> Aplikacje Java są obsługiwane tylko w usłudze Azure App Services za pomocą ręcznego instrumentacji opartej na zestawie SDK i dlatego poniższe kroki nie mają zastosowania do tych scenariuszy.

1. Sprawdź, czy aplikacja jest `ApplicationInsightsAgent`monitorowana za pośrednictwem .
    * Sprawdź, `ApplicationInsightsAgent_EXTENSION_VERSION` czy ustawienie aplikacji jest ustawione na wartość "~2".
2. Upewnij się, że aplikacja spełnia wymagania, które mają być monitorowane.
    * Przejdź na stronę `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Zrzut https://yoursitename.scm.azurewebsites/applicationinsights ekranu przedstawiający stronę wyników](./media/azure-web-apps/app-insights-sdk-status.png)

    * Upewnij się, `Application Insights Extension Status` że jest`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Jeśli nie jest uruchomiona, postępuj zgodnie z [instrukcjami monitorowania usługi Enable Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Upewnij się, że źródło stanu istnieje i wygląda następująco:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Jeśli podobna wartość nie jest obecny, oznacza to, że aplikacja nie jest aktualnie uruchomiona lub nie jest obsługiwana. Aby upewnić się, że aplikacja jest uruchomiona, spróbuj ręcznie odwiedzić punkty końcowe adresu URL/aplikacji aplikacji, co umożliwi udostępnienie informacji o czasie wykonywania.

    * Potwierdź, że `IKeyExists` jest`true`
        * Jeśli tak `false`jest `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING` , dodaj i z identyfikatorem ikey do ustawień aplikacji.

    * Potwierdź, że nie `AppAlreadyInstrumented` `AppContainsDiagnosticSourceAssembly`ma `AppContainsAspNetTelemetryCorrelationAssembly`żadnych wpisów dla , i .
        * Jeśli którykolwiek z tych wpisów istnieje, usuń `Microsoft.ApplicationInsights` `System.Diagnostics.DiagnosticSource`z `Microsoft.AspNet.TelemetryCorrelation`aplikacji następujące pakiety: , , i .

Poniższa tabela zawiera bardziej szczegółowe wyjaśnienie, co oznaczają te wartości, ich przyczyny i zalecane poprawki:

|Wartość problemu|Wyjaśnienie|Poprawka
|---- |----|---|
| `AppAlreadyInstrumented:true` | Ta wartość wskazuje, że rozszerzenie wykryło, że niektóre aspekty SDK jest już obecny w aplikacji i będzie wycofywać. Może to być spowodowane `System.Diagnostics.DiagnosticSource`odniesieniem do , `Microsoft.AspNet.TelemetryCorrelation`lub`Microsoft.ApplicationInsights`  | Usuń odwołania. Niektóre z tych odwołań są domyślnie dodawane z niektórych szablonów programu `Microsoft.ApplicationInsights`Visual Studio, a starsze wersje programu Visual Studio mogą dodawać odwołania do programu .
|`AppAlreadyInstrumented:true` | Jeśli aplikacja jest kierowana na .NET Core 2.1 lub 2.2 i odwołuje się do [microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-pakiet, a następnie przynosi w aplikacji Insights i rozszerzenie zostanie wycofane. | Klienci korzystający z platformy .NET Core 2.1,2.2 [zaleca się](https://github.com/aspnet/Announcements/issues/287) używanie metapakiety Microsoft.AspNetCore.App.|
|`AppAlreadyInstrumented:true` | Ta wartość może być również spowodowane przez obecność powyższych bibliotek dll w folderze aplikacji z poprzedniego wdrożenia. | Wyczyść folder aplikacji, aby upewnić się, że te biblioteki DLL są usuwane. Sprawdź zarówno katalog pojemników aplikacji lokalnej, jak i katalog wwwroot w usłudze App Service. (Aby sprawdzić katalog wwwroot aplikacji sieci Web usługi App Service: Advanced Tools (Kudu) > konsoli debugowania > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Ta wartość wskazuje, że rozszerzenie `Microsoft.AspNet.TelemetryCorrelation` wykryte odwołania do w aplikacji i będzie wycofywać. | Usuń odwołanie.
|`AppContainsDiagnosticSourceAssembly**:true`|Ta wartość wskazuje, że rozszerzenie `System.Diagnostics.DiagnosticSource` wykryte odwołania do w aplikacji i będzie wycofywać.| Usuń odwołanie.
|`IKeyExists:false`|Ta wartość wskazuje, że klucz instrumentacji nie jest obecny `APPINSIGHTS_INSTRUMENTATIONKEY`w AppSetting, . Możliwe przyczyny: Wartości mogły zostać przypadkowo usunięte, zapomniałem ustawić wartości w skrypcie automatyzacji itp. | Upewnij się, że ustawienie jest obecne w ustawieniach aplikacji usługi App Service.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED i urlCompression nie jest obsługiwany

Jeśli używasz APPINSIGHTS_JAVASCRIPT_ENABLED = true w przypadkach, gdy zawartość jest zakodowana, mogą pojawić się błędy, takie jak: 

- Błąd ponownego zapisu adresu URL 500
- 500.53 Błąd modułu ponownego zapisu adresu URL z komunikatem Reguły przepisywania wychodzącego nie mogą być stosowane, gdy zawartość odpowiedzi HTTP jest zakodowana ("gzip"). 

Wynika to z APPINSIGHTS_JAVASCRIPT_ENABLED ustawienie aplikacji jest ustawiona na true i kodowania zawartości jest obecny w tym samym czasie. Ten scenariusz nie jest jeszcze obsługiwany. Obejście polega na usunięciu APPINSIGHTS_JAVASCRIPT_ENABLED z ustawień aplikacji. Niestety oznacza to, że jeśli instrumentacja JavaScript po stronie klienta/przeglądarki jest nadal wymagana, wymagane są ręczne odwołania do zestawów SDK dla stron internetowych. Postępuj zgodnie z [instrukcjami](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) dotyczącymi ręcznego oprzyrządowania za pomocą zestawu JavaScript SDK.

Aby uzyskać najnowsze informacje na temat agenta/rozszerzenia usługi Application Insights, zapoznaj się z [informacjami o wersji](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>PHP i WordPress nie są obsługiwane

Witryny PHP i WordPress nie są obsługiwane. Obecnie nie ma oficjalnie obsługiwanego SDK/agenta do monitorowania tych obciążeń po stronie serwera. Jednak ręczne instrumentowanie transakcji po stronie klienta w witrynie PHP lub WordPress przez dodanie javascript po stronie klienta do stron internetowych można osiągnąć za pomocą [javascript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript). 

## <a name="next-steps"></a>Następne kroki
* [Uruchom profilera aplikacji na żywo](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) — monitorowanie usługi Azure Functions za pomocą usługi Application Insights
* [Włącz diagnostykę platformy Azure](../platform/diagnostics-extension-to-application-insights.md), która ma być wysyłana do usługi Application Insights.
* [Monitoruj metryki kondycji usługi](../platform/data-platform.md), aby upewnić się, że usługa jest dostępna i szybko reaguje.
* [Odbieraj powiadomienia o alertach](../platform/alerts-overview.md) zawsze, gdy wystąpią zdarzenia operacyjne lub metryki przekroczą próg.
* Użyj pozycji [Usługa Application Insights dla aplikacji JavaScript i stron sieci Web](javascript.md), aby pobrać telemetrię klienta z przeglądarek, w których odwiedzono stronę sieci Web.
* [Konfiguruj testy sieci Web dostępności](monitor-web-app-availability.md), aby otrzymywać alerty, gdy witryna nie działa.
