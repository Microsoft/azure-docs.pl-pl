---
title: Dodawanie obsługi usługi Key Vault do projektu ASP.NET przy użyciu programu Visual Studio — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Ten samouczek pomoże Ci dowiedzieć się, jak dodać obsługę usługi Key Vault do ASP.NET lub ASP.NET podstawowej aplikacji sieci web.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 68f8607db25b70ea5e6bfe21a7d6e002aeb127d6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429774"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Dodawanie magazynu kluczy do aplikacji sieci web przy użyciu usług połączonych programu Visual Studio

W tym samouczku dowiesz się, jak łatwo dodać wszystko, czego potrzebujesz, aby rozpocząć korzystanie z usługi Azure Key Vault do zarządzania wpisami tajnymi dla projektów sieci Web w programie Visual Studio, niezależnie od tego, czy używasz ASP.NET Core, czy dowolnego typu ASP.NET projektu. Korzystając z funkcji Połączone usługi w programie Visual Studio, program Visual Studio może automatycznie dodawać wszystkie pakiety NuGet i ustawienia konfiguracji potrzebne do połączenia z usługą Key Vault na platformie Azure.

Aby uzyskać szczegółowe informacje na temat zmian wprowadzanych przez usługi połączone w projekcie w celu włączenia usługi Key Vault, zobacz [Key Vault Connected Service - Co się stało z moim projektem ASP.NET 4.7.1](#how-your-aspnet-framework-project-is-modified) lub [Key Vault Connected Service - Co się stało z moim projektem ASP.NET Core](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, zarejestruj się na [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 w wersji 16.3** lub nowszej lub **Visual Studio 2017 w wersji 15.7** z zainstalowanym obciążeniem **web development.** [Pobierz go teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- W przypadku ASP.NET (nie Core) w programie Visual Studio 2017 potrzebne są narzędzia programistyczne .NET Framework 4.7.1 lub nowsze, które nie są domyślnie zainstalowane. Aby je zainstalować, uruchom Instalator programu Visual Studio, wybierz polecenie **Modyfikuj**, a następnie wybierz **pozycję Poszczególne składniki**, a następnie po prawej stronie, rozwiń ASP.NET i tworzenie stron **internetowych**oraz wybierz opcję **.NET Framework 4.7.1 Development Tools**.
- ASP.NET 4.7.1 lub nowszym lub ASP.NET projekt internetowy Core 2.0 lub nowszy.

## <a name="add-key-vault-support-to-your-project"></a>Dodawanie obsługi usługi Key Vault do projektu

Przed rozpoczęciem upewnij się, że zalogowano się do programu Visual Studio. Zaloguj się przy użyciu tego samego konta, którego używasz w ramach subskrypcji platformy Azure. Następnie otwórz ASP.NET 4.7.1 lub nowszym lub ASP.NET projektu sieci Web Core 2.0 i wykonaj następujące czynności:

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt, do którego chcesz dodać obsługę usługi Key Vault, a następnie wybierz pozycję **Dodaj** > **połączoną usługę**.
   Zostanie wyświetlona strona Usługa połączona zawierająca usługi, które możesz dodać do projektu.
1. W menu dostępnych usług wybierz polecenie **Bezpieczne wpisy tajne z usługą Azure Key Vault**.

   ![Wybierz opcję "Bezpieczne wpisy tajne z magazynem kluczy platformy Azure"](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nową lub istniejącą przechowalnię kluczy. Jeśli wybierzesz nową przechowalnię kluczy, zostanie wyświetlene łącze **Edytuj.** Wybierz go, aby skonfigurować nowy magazyn kluczy.

   ![Wybierz swoją subskrypcję](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. W **obszarze Edytuj usługę Azure Key Vault**wprowadź nazwę, której chcesz użyć w magazynie kluczy.

1. Wybierz istniejącą **grupę zasobów**lub wybierz nową o automatycznie wygenerowanej unikatowej nazwie.  Jeśli chcesz utworzyć nową grupę o innej nazwie, możesz użyć [portalu Azure](https://portal.azure.com), a następnie zamknąć stronę i ponownie uruchomić, aby ponownie załadować listę grup zasobów.
1. Wybierz **lokalizację,** w której ma być utworzony przechowalnia kluczy. Jeśli aplikacja sieci web jest hostowana na platformie Azure, wybierz region, w którym znajduje się aplikacja sieci web, aby uzyskać optymalną wydajność.
1. Wybierz **warstwę cenową**. Aby uzyskać szczegółowe informacje, zobacz [Cennik skarbca kluczy](https://azure.microsoft.com/pricing/details/key-vault/).
1. Wybierz **przycisk OK,** aby zaakceptować opcje konfiguracji.
1. Po wybraniu istniejącego magazynu kluczy lub skonfigurowaniu nowego magazynu kluczy na karcie **Usługa Azure Key Vault** w programie Visual Studio wybierz pozycję **Dodaj,** aby dodać usługę połączone.
1. Wybierz **łącze Zarządzaj wpisami tajnymi przechowywanymi w tym łączu magazynu** **kluczy,** aby otworzyć stronę Wpisy tajne dla magazynu kluczy. Jeśli strona lub projekt zostały zamknięte, można przejść do niej w [witrynie Azure portal,](https://portal.azure.com) wybierając **pozycję Wszystkie usługi** i w obszarze **Zabezpieczenia**, wybierając pozycję **Magazyn kluczy**, a następnie wybierz przechowalnię kluczy.
1. W sekcji Przechowalnia kluczy dla utworzonego magazynu kluczy wybierz pozycję **Wpisy tajne**, a następnie **pozycję Generuj/Importuj**.

   ![Generowanie/importowanie klucza tajnego](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Wprowadź klucz tajny, taki jak *MySecret* i nadaj mu dowolną wartość ciągu jako test, a następnie wybierz przycisk **Utwórz.**

   ![Utwórz klucz tajny](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (opcjonalnie) Wprowadź inny sekret, ale tym razem umieścić go w kategorii, nazywając go *Secrets - MySecret*. Ta składnia określa kategorię "Wpisy tajne", która zawiera klucz tajny "MySecret".

Teraz możesz uzyskać dostęp do swoich wpisów tajnych w kodzie. Następne kroki różnią się w zależności od tego, czy używasz ASP.NET 4.7.1 lub ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Dostęp do wpisów tajnych w kodzie (ASP.NET Core)

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** znajdź i zainstaluj te dwa pakiety NuGet: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i .NET Core 2, dodaj [microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) lub .NET Core 3, dodaj[microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

1. W przypadku programu .NET `Program.cs` Core 2 `BuildWebHost` wybierz kartę i zmień definicję w klasie Program na następującą:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   W przypadku programu .NET Core 3 użyj następującego kodu.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Następnie otwórz jeden z plików stron, takich jak *Index.cshtml.cs* i napisz następujący kod:
   1. Dołącz odwołanie `Microsoft.Extensions.Configuration` do tego przy użyciu dyrektywy:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Dodaj zmienną konfigurację.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Dodaj ten konstruktor lub zastąp istniejący konstruktor tym:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Zaktualizuj metodę `OnGet`. Zaktualizuj wartość symbolu zastępczego wyświetlaną w tym miejscu za pomocą tajnej nazwy utworzonej w powyższych poleceniach.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Aby potwierdzić wartość w czasie wykonywania, dodaj kod do wyświetlenia `ViewData["Message"]` w pliku *cshtml,* aby wyświetlić klucz tajny w wiadomości.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Aplikację można uruchomić lokalnie, aby sprawdzić, czy klucz tajny został pomyślnie uzyskany z magazynu kluczy.

## <a name="access-your-secrets-aspnet"></a>Dostęp do swoich sekretów (ASP.NET)

Konfigurację można skonfigurować w taki sposób, aby plik web.config miał wartość manekina w `appSettings` elemencie, który jest zastępowany wartością rzeczywistą w czasie wykonywania. Następnie można uzyskać do `ConfigurationManager.AppSettings` niego dostęp za pośrednictwem struktury danych.

1. Edytuj plik web.config.  Znajdź tag appSettings, dodaj `configBuilders="AzureKeyVault"`atrybut i dodaj wiersz:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Edytuj `About` metodę w *HomeController.cs*, aby wyświetlić wartość potwierdzenia.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Uruchom aplikację lokalnie pod debugerem, przełącz się na kartę **Informacje** i sprawdź, czy jest wyświetlana wartość z magazynu kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie jest już potrzebna, usuń ją. Spowoduje to usunięcie magazynu kluczy i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **TYP NAZWA GRUPY ZASOBÓW wprowadź** nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli usługa Key Vault jest uruchomiona na innym koncie Microsoft niż to, które jest zalogowane do programu Visual Studio (na przykład usługa Key Vault jest uruchomiona na koncie służbowym, ale program Visual Studio używa konta prywatnego), w pliku Program.cs pojawia się błąd, program Visual Studio nie może uzyskać dostępu do usługi Key Vault. Aby rozwiązać ten problem:

1. Przejdź do [witryny Azure portal](https://portal.azure.com) i otwórz magazyn kluczy.

1. Wybierz **pozycję Zasady programu Access**, a następnie dodaj zasady **dostępu**i wybierz konto, na które użytkownik jest zalogowany jako podmiot zabezpieczeń.

1. W programie Visual Studio wybierz pozycję**Ustawienia konta** **plików** > .
Wybierz **pozycję Dodaj konto** w sekcji Wszystkie **konta.** Zaloguj się przy tym za pomocą konta wybranego jako główny zobowiązany do przestrzegania zasad dostępu.

1. Wybierz pozycję**Opcje** **narzędzi** > i poszukaj **opcji uwierzytelniania usługi Azure**. Następnie wybierz konto, które właśnie dodano do programu Visual Studio.

Teraz po debugowaniu aplikacji program Visual Studio łączy się z kontem, na którym znajduje się usługa Key Vault.

## <a name="how-your-aspnet-core-project-is-modified"></a>Jak modyfikowany jest projekt ASP.NET Core

W tej sekcji identyfikowane dokładne zmiany wprowadzone do projektu ASP.NET podczas dodawania usługi połączonej usługi Usługi Usługi Key Vault przy użyciu programu Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Dodano referencje dla ASP.NET Core

Wpływa na odwołania do pliku projektu .NET i odwołania do pakietu NuGet.

| Typ | Dokumentacja |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Dodano pliki dla ASP.NET Core

- `ConnectedService.json`informacje o dostawcy Usługi Connected, wersja i link do dokumentacji.

### <a name="project-file-changes-for-aspnet-core"></a>Zmiany w pliku projektu dla ASP.NET Core

- Dodano grupę elementów `ConnectedServices.json` usług połączonych i plik.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.json zmiany dla ASP.NET Core

- Dodano następujące wpisy zmiennych środowiskowych zarówno do profilu IIS Express, jak i profilu, który pasuje do nazwy projektu sieci Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Zmiany na platformie Azure dla ASP.NET Core

- Utworzono grupę zasobów (lub utworzono istniejącą).
- Utworzono magazyn kluczy w określonej grupie zasobów.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Jak modyfikowany jest projekt ASP.NET Framework

W tej sekcji identyfikowane dokładne zmiany wprowadzone do projektu ASP.NET podczas dodawania usługi połączonej usługi Usługi Usługi Key Vault przy użyciu programu Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Dodano odwołania do ASP.NET Framework

Wpływa na odwołania do pliku `packages.config` projektu .NET i (Odwołania NuGet).

| Typ | Dokumentacja |
| --- | --- |
| .NET; Nuget | Microsoft.Azure.KeyVault |
| .NET; Nuget | Narzędzie Microsoft.Azure.KeyVault.WebKey |
| .NET; Nuget | Microsoft.Rest.ClientRuntime |
| .NET; Nuget | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Dodano pliki dla ASP.NET Framework

- `ConnectedService.json`informacje o dostawcy Usługi Connected, wersja i link do dokumentacji.

### <a name="project-file-changes-for-aspnet-framework"></a>Zmiany w pliku projektu dla ASP.NET Framework

- Dodano plik Element Connected Services i ConnectedServices.json.
- Odwołania do zestawów .NET opisane w sekcji [Dodane odwołania.](#added-references-for-aspnet-framework)

### <a name="webconfig-or-appconfig-changes"></a>web.config lub app.config zmiany

- Dodano następujące wpisy konfiguracji:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Zmiany na platformie Azure dla ASP.NET Framework

- Utworzono grupę zasobów (lub utworzono istniejącą).
- Utworzono magazyn kluczy w określonej grupie zasobów.

## <a name="next-steps"></a>Następne kroki

Jeśli po tym samouczku uprawnienia usługi Key Vault są skonfigurowane do uruchamiania z własną subskrypcją platformy Azure, ale może to nie być pożądane w scenariuszu produkcyjnym. Możesz utworzyć tożsamość zarządzaną, aby zarządzać dostępem do usługi Key Vault dla aplikacji. Zobacz: [Zapewnianie uwierzytelniania magazynu kluczy z tożsamością zarządzaną](/azure/key-vault/managed-identity).

Dowiedz się więcej o rozwoju usługi Key Vault, czytając [przewodnik dla deweloperów magazynu kluczy.](developers-guide.md)
