---
title: Dodawanie obsługi Key Vault do projektu ASP.NET przy użyciu programu Visual Studio — Azure Key Vault | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak dodać obsługę Key Vault do aplikacji sieci Web ASP.NET lub ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: af0065db087595167ca71bb79b968cc4ad339acd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82116846"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Dodawanie Key Vault do aplikacji sieci Web przy użyciu usług połączonych programu Visual Studio

W tym samouczku dowiesz się, jak łatwo dodać wszystko, czego potrzebujesz, aby rozpocząć korzystanie z Azure Key Vault do zarządzania wpisami tajnymi dla projektów sieci Web w programie Visual Studio, niezależnie od tego, czy używasz ASP.NET Core czy dowolnego typu projektu ASP.NET. Korzystając z funkcji usługi połączone w programie Visual Studio, można automatycznie dodać wszystkie pakiety NuGet i ustawienia konfiguracji, które są wymagane do nawiązania połączenia z Key Vault na platformie Azure.

Aby uzyskać szczegółowe informacje o zmianach, które są połączone przez usługi w projekcie w celu umożliwienia Key Vault, zobacz [Key Vault połączonej usługi — co się stało z moim projektem ASP.NET 4.7.1](#how-your-aspnet-framework-project-is-modified) lub [Key Vault połączonej usługi — co się stało z moim projektem ASP.NET Core](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, zarejestruj się w celu uzyskania [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- **Program Visual studio 2019 w wersji 16,3** lub nowszej albo **program Visual Studio 2017 w wersji 15,7** z zainstalowanym obciążeniem **programowaniem w sieci Web** . [Pobierz go teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- W przypadku ASP.NET (nierdzeń) z programem Visual Studio 2017 potrzebne są narzędzia deweloperskie .NET Framework 4.7.1 lub nowsze, które nie są instalowane domyślnie. Aby je zainstalować, uruchom Instalator programu Visual Studio, wybierz opcję **Modyfikuj**, a następnie wybierz **poszczególne składniki**, następnie po prawej stronie rozwiń węzeł **ASP.NET i programowanie w sieci Web**, a następnie wybierz pozycję **.NET Framework 4.7.1 Development Tools**.
- Projekt sieci Web ASP.NET 4.7.1 lub nowszy lub ASP.NET Core 2,0 lub nowszy.

## <a name="add-key-vault-support-to-your-project"></a>Dodawanie obsługi Key Vault do projektu

Przed rozpoczęciem upewnij się, że jesteś zalogowany w programie Visual Studio. Zaloguj się przy użyciu tego samego konta, które jest używane w ramach subskrypcji platformy Azure. Następnie otwórz ASP.NET 4.7.1 lub nowszy lub ASP.NET Core projekt sieci Web 2,0 i wykonaj następujące czynności:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, do którego chcesz dodać obsługę Key Vault, a następnie wybierz polecenie **Dodaj**  >  **podłączoną usługę**.
   Zostanie wyświetlona strona Usługa połączona zawierająca usługi, które możesz dodać do projektu.
1. W menu dostępne usługi wybierz pozycję **bezpieczne klucze tajne z Azure Key Vault**.

   ![Wybieranie opcji "Zabezpiecz klucze tajne Azure Key Vault"](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nową lub istniejącą Key Vault. W przypadku wybrania nowej Key Vault zostanie wyświetlone łącze **Edytuj** . Wybierz go, aby skonfigurować nowy Key Vault.

   ![Wybieranie subskrypcji](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. W obszarze **edytuj Azure Key Vault**wprowadź nazwę, która ma być używana dla Key Vault.

1. Wybierz istniejącą **grupę zasobów**lub Utwórz nową, automatycznie wygenerowaną unikatową nazwę.  Jeśli chcesz utworzyć nową grupę o innej nazwie, możesz użyć [Azure Portal](https://portal.azure.com), a następnie zamknąć stronę i ponownie uruchomić, aby ponownie załadować listę grup zasobów.
1. Wybierz **lokalizację** , w której chcesz utworzyć Key Vault. Jeśli aplikacja sieci Web jest hostowana na platformie Azure, wybierz region, który obsługuje aplikację sieci Web w celu uzyskania optymalnej wydajności.
1. Wybierz **warstwę cenową**. Aby uzyskać szczegółowe informacje, zobacz [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Wybierz **przycisk OK** , aby zaakceptować opcje konfiguracji.
1. Po wybraniu istniejącego Key Vault lub skonfigurowaniu nowej Key Vault na karcie **Azure Key Vault** programu Visual Studio wybierz pozycję **Dodaj** , aby dodać podłączoną usługę.
1. Wybierz łącze **Zarządzaj wpisami tajnymi przechowywanymi w tym Key Vault** , aby otworzyć stronę wpisy **tajne** dla Key Vault. Jeśli zamknięto stronę lub projekt, możesz przejść do niego w [Azure Portal](https://portal.azure.com) , wybierając pozycję **wszystkie usługi** i w obszarze **zabezpieczenia**wybierz pozycję **Key Vault**, a następnie Key Vault.
1. W Key Vault sekcji dla utworzonego Key Vault wybierz pozycję wpisy **tajne**, a następnie pozycję **Generuj/Importuj**.

   ![Generowanie/Importowanie klucza tajnego](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Wprowadź klucz tajny, taki jak *hasło* i nadaj mu dowolną wartość ciągu jako test, a następnie wybierz przycisk **Utwórz** .

   ![Utwórz klucz tajny](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. obowiązkowe Wprowadź inny klucz tajny, ale ten czas należy umieścić w kategorii przez nadanie jej *tajemnicy*. Ta składnia Określa kategorię "tajemnice", która zawiera wpis tajny "Secret".

Teraz możesz uzyskać dostęp do wpisów tajnych w kodzie. Następne kroki są różne w zależności od tego, czy używasz ASP.NET 4.7.1 czy ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Dostęp do wpisów tajnych w kodzie (ASP.NET Core)

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Znajdź i zainstaluj te dwa pakiety NuGet: [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i .NET Core 2, Dodaj [Microsoft. Azure. kluczowe magazyn](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) lub .NET Core 3, Dodaj[Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)kluczing. Core.

1. W przypadku platformy .NET Core 2 Wybierz `Program.cs` kartę i Zmień `BuildWebHost` definicję w klasie program na następującą:

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

   W przypadku platformy .NET Core 3 Użyj poniższego kodu.

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

1. Następnie otwórz jeden z plików stronicowania, takich jak *index.cshtml.cs* , i Napisz następujący kod:
   1. Dołącz odwołanie do `Microsoft.Extensions.Configuration` tej dyrektywy using:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Dodaj zmienną konfiguracyjną.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Dodaj ten Konstruktor lub Zastąp istniejący Konstruktor tym:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Zaktualizuj metodę `OnGet`. Zaktualizuj wartość symbolu zastępczego w tym miejscu przy użyciu nazwy wpisu tajnego utworzonego w powyższych poleceniach.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Aby potwierdzić wartość w czasie wykonywania, Dodaj kod do wyświetlenia `ViewData["Message"]` do pliku *. cshtml* , aby wyświetlić wpis tajny w komunikacie.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Aplikację można uruchomić lokalnie, aby sprawdzić, czy klucz tajny został pomyślnie pobrany z Key Vault.

## <a name="access-your-secrets-aspnet"></a>Dostęp do wpisów tajnych (ASP.NET)

Konfigurację można skonfigurować tak, aby plik web.config miał wartość fikcyjną w `appSettings` elemencie, który jest zastępowany przez wartość true w czasie wykonywania. Następnie można uzyskać do niego dostęp za pośrednictwem `ConfigurationManager.AppSettings` struktury danych.

1. Edytuj plik web.config.  Znajdź tag appSettings, Dodaj atrybut `configBuilders="AzureKeyVault"` i Dodaj wiersz:

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
1. Uruchom aplikację lokalnie w debugerze, przejdź do karty **informacje** i sprawdź, czy jest wyświetlana wartość z Key Vault.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie jest już potrzebna, usuń ją. Spowoduje to usunięcie Key Vault i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **wpisz nazwę grupy zasobów:** wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli Key Vault jest uruchomiony na innym konto Microsoft niż ten, który jest zalogowany do programu Visual Studio (na przykład, Key Vault jest uruchomiony na koncie służbowym, ale program Visual Studio używa Twojego konta prywatnego), w pliku Program.cs zostanie wyświetlony komunikat o błędzie, że program Visual Studio nie będzie mógł uzyskać dostępu do Key Vault. Aby rozwiązać ten problem:

1. Przejdź do [Azure Portal](https://portal.azure.com) i Otwórz Key Vault.

1. Wybierz pozycję **zasady dostępu**, a następnie **Dodaj zasady dostępu**i wybierz konto, za pomocą którego użytkownik jest zalogowany jako podmiot zabezpieczeń.

1. W programie Visual Studio wybierz **File**pozycję  >  **Ustawienia konta**pliku.
Wybierz pozycję **Dodaj konto** z sekcji **wszystkie konta** . Zaloguj się przy użyciu konta wybranego jako podmiot zabezpieczeń zasad dostępu.

1. Wybierz pozycję **Narzędzia**  >  **Opcje**i Wyszukaj pozycję **uwierzytelnianie usługi platformy Azure**. Następnie wybierz konto, które właśnie zostało dodane do programu Visual Studio.

Teraz podczas debugowania aplikacji program Visual Studio nawiązuje połączenie z kontem, na którym znajduje się Key Vault.

## <a name="how-your-aspnet-core-project-is-modified"></a>Jak projekt ASP.NET Core jest modyfikowany

W tej sekcji przedstawiono dokładne zmiany wprowadzone w projekcie ASP.NET podczas dodawania połączonej usługi Key Vault przy użyciu programu Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Dodano odwołania dla ASP.NET Core

Ma wpływ na odwołania do pliku projektu i odwołania do pakietu NuGet.

| Typ | Odwołanie |
| --- | --- |
| NuGet | Microsoft. AspNetCore. AzureKeyVault. HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Dodano pliki dla ASP.NET Core

- `ConnectedService.json`dodano, który rejestruje pewne informacje o dostawcy usługi połączonej, wersji i linku dokumentacji.

### <a name="project-file-changes-for-aspnet-core"></a>Zmiany plików projektu dla ASP.NET Core

- Dodano element i plik usługi połączonych usług `ConnectedServices.json` .

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.jszmian ASP.NET Core

- Dodano następujące wpisy zmiennych środowiskowych do profilu IIS Express i profilu zgodnego z nazwą projektu sieci Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Zmiany na platformie Azure dla ASP.NET Core

- Utworzono grupę zasobów (lub użyto istniejącej).
- Utworzono Key Vault w określonej grupie zasobów.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Jak jest modyfikowany projekt platformy ASP.NET Framework

W tej sekcji przedstawiono dokładne zmiany wprowadzone w projekcie ASP.NET podczas dodawania połączonej usługi Key Vault przy użyciu programu Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Dodano odwołania dla platformy ASP.NET Framework

Ma wpływ na odwołania do pliku projektu i `packages.config` (odwołania NuGet).

| Typ | Odwołanie |
| --- | --- |
| Waga NuGet | Microsoft.Azure.KeyVault |
| Waga NuGet | Microsoft. Azure. WebKey magazynu. |
| Waga NuGet | Microsoft.Rest.ClientRuntime |
| Waga NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Dodano pliki dla platformy ASP.NET Framework

- `ConnectedService.json`dodano, który rejestruje pewne informacje o dostawcy usługi połączonej, wersji i linku do dokumentacji.

### <a name="project-file-changes-for-aspnet-framework"></a>Zmiany plików projektu dla platformy ASP.NET Framework

- Dodano element Items i ConnectedServices.jsw pliku połączonych usług.
- Odwołania do zestawów .NET opisanych w sekcji [dodane odwołania](#added-references-for-aspnet-framework) .

### <a name="webconfig-or-appconfig-changes"></a>Zmiany web.config lub app.config

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Zmiany na platformie Azure dla platformy ASP.NET Framework

- Utworzono grupę zasobów (lub użyto istniejącej).
- Utworzono Key Vault w określonej grupie zasobów.

## <a name="next-steps"></a>Następne kroki

Jeśli wykonano ten samouczek, Twoje uprawnienia Key Vault są skonfigurowane do uruchamiania w ramach własnej subskrypcji platformy Azure, ale mogą nie być pożądane w scenariuszu produkcyjnym. Możesz utworzyć zarządzaną tożsamość, aby zarządzać dostępem Key Vault do aplikacji. Zobacz [udostępnianie uwierzytelniania Key Vault przy użyciu tożsamości zarządzanej](/azure/key-vault/managed-identity).

Dowiedz się więcej na temat opracowywania Key Vault, odczytując [przewodnik dewelopera Key Vault](developers-guide.md).
