---
title: Konfigurowanie potoku ciągłej integracji/ciągłego wdrażania za pomocą zadania kompilacji emulatora Azure Cosmos DB
description: Samouczek dotyczący sposobu konfigurowania przepływu pracy kompilacji i wydawania w usłudze Azure DevOps przy użyciu zadania kompilacji emulatora usługi Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: c7246511a88e2d2756a8ef56c5adf51ddbfd3e58
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560535"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Konfigurowanie potoku ciągłej integracji/ciągłego wdrażania Azure Cosmos DB za pomocą zadania DevOps emulatora na platformie Azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Emulator usługi Azure Cosmos DB zapewnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów programistycznych. Emulator umożliwia lokalne opracowywanie i testowanie aplikacji bez konieczności tworzenia subskrypcji platformy Azure i ponoszenia kosztów. 

Zadanie kompilacji emulatora Azure Cosmos DB dla usługi Azure DevOps umożliwia takie samo w środowisku CI. Dzięki zadaniu kompilacji możesz uruchamiać testy z użyciem emulatora w ramach przepływów pracy kompilacji i wydania. Zadanie uruchamia kontener platformy Docker z uruchomionym emulatorem i dostarcza punkt końcowy, który może być używany przez pozostałą część definicji kompilacji. Możesz utworzyć i uruchomić dowolną liczbę wystąpień emulatora — każde w oddzielnym kontenerze. 

W tym artykule pokazano, jak skonfigurować potok ciągłej integracji w usłudze Azure DevOps dla aplikacji ASP.NET, w której wykorzystano zadanie kompilacji emulatora usługi Cosmos DB w celu wykonywania testów. Za pomocą podobnego podejścia można skonfigurować potok ciągłej integracji dla aplikacji Node.js lub Python. 

## <a name="install-the-emulator-build-task"></a>Instalowanie zadania kompilacji emulatora

Aby użyć zadania kompilacji, najpierw należy zainstalować je w ramach organizacji usługi Azure DevOps. Znajdź rozszerzenie **Azure Cosmos DB Emulator** w witrynie [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) i kliknij polecenie **Get it free** (Uzyskaj bezpłatnie).

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_1.png" alt-text="Znajdowanie i instalowanie zadania kompilacji emulatora usługi Azure Cosmos DB w witrynie Marketplace usługi Azure DevOps":::

Następnie wybierz organizację, w której chcesz zainstalować rozszerzenie. 

> [!NOTE]
> Aby zainstalować rozszerzenie w organizacji usługi Azure DevOps, musisz być właścicielem konta lub administratorem kolekcji projektów. Jeśli nie masz uprawnień, ale jesteś użytkownikiem konta, możesz w takiej sytuacji zażądać rozszerzenia. [Dowiedz się więcej.](/azure/devops/marketplace/faq-extensions)

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_2.png" alt-text="Wybierz organizację usługi Azure DevOps, w której chcesz zainstalować rozszerzenie":::

## <a name="create-a-build-definition"></a>Tworzenie definicji kompilacji

Teraz, gdy rozszerzenie jest zainstalowane, zaloguj się do organizacji usługi Azure DevOps i Znajdź swój projekt na pulpicie nawigacyjnym projekty. Do projektu możesz dodać [potok kompilacji](/azure/devops/pipelines/get-started-designer?preserve-view=true&tabs=new-nav) lub zmodyfikować istniejący potok kompilacji. Jeśli masz już potok kompilacji, możesz przejść od razu do sekcji [Dodawanie zadania kompilacji emulatora do definicji kompilacji](#addEmulatorBuildTaskToBuildDefinition).

1. Aby utworzyć nową definicję kompilacji, przejdź do karty **Builds** (Kompilacje) w usłudze Azure DevOps. Wybierz pozycję **+ Nowy.** \> **New build pipeline** (Nowy potok kompilacji)

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png" alt-text="Tworzenie nowego potoku kompilacji":::

2. Wybierz żądane **źródło** oraz odpowiednie opcje w polach **Team project** (Projekt zespołowy), **Repository** (Repozytorium) i **Default branch for manual and scheduled builds** (Domyślna gałąź dla kompilacji ręcznych i zaplanowanych). Po wybraniu wymaganych opcji wybierz przycisk **Continue** (Kontynuuj)

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png" alt-text="Wybieranie projektu zespołowego, repozytorium i gałęzi dla potoku kompilacji":::

3. Na koniec wybierz odpowiedni szablon dla potoku kompilacji. Do celów tego samouczka wybierzemy szablon **ASP.NET**. Teraz masz potok kompilacji, który można skonfigurować do korzystania z zadania kompilacji emulatora Azure Cosmos DB. 

> [!NOTE]
> Pula agentów wybrana dla tego elementu konfiguracji powinna mieć zainstalowaną Docker for Windows, chyba że instalacja zostanie wykonana ręcznie w poprzednim zadaniu jako część elementu konfiguracji. Aby uzyskać wybór pul agentów, zobacz artykuł dotyczący [agentów hostowanych firmy Microsoft](/azure/devops/pipelines/agents/hosted?tabs=yaml) . zalecamy rozpoczęcie od `Hosted VS2017` .

Emulator Azure Cosmos DB obecnie nie obsługuje hostowanej puli agentów VS2019. Jednak emulator jest już dostarczany z zainstalowanym VS2019 i jest używany przez uruchomienie emulatora przy użyciu następujących poleceń cmdlet programu PowerShell. Jeśli wystąpią problemy podczas korzystania z VS2019, skontaktuj się z zespołem [usługi Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) w celu uzyskania pomocy:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Dodawanie zadania do potoku kompilacji

1. Przed dodaniem zadania do potoku kompilacji należy dodać zadanie agenta. Przejdź do potoku kompilacji, wybierz pozycję **...** i wybierz polecenie **Add an agent job** (Dodaj zadanie agenta).

1. Następnie wybierz **+** symbol obok zadania agenta, aby dodać zadanie kompilacji emulatora. Wyszukaj ciąg **cosmos** w polu wyszukiwania, wybierz pozycję **Azure Cosmos DB Emulator** (Emulator usługi Azure Cosmos DB) i dodaj ten emulator do zadania agenta. Zadanie kompilacji będzie uruchamiać kontener, na którym już będzie uruchomione wystąpienie emulatora usługi Cosmos DB. Zadanie emulatora usługi Azure Cosmos DB powinno znajdować się przed wszystkimi innymi zadaniami, które oczekują, że emulator będzie działać.

   :::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_3.png" alt-text="Dodawanie zadania kompilacji emulatora do definicji kompilacji":::

W tym samouczku dodasz zadanie na początku, aby upewnić się, że emulator jest dostępny przed rozpoczęciem wykonywania naszych testów.

### <a name="add-the-task-using-yaml"></a>Dodawanie zadania przy użyciu YAML

Ten krok jest opcjonalny i jest wymagany tylko w przypadku konfigurowania potoku ciągłej integracji/ciągłego wdrażania przy użyciu zadania YAML. W takich przypadkach można zdefiniować zadanie YAML, jak pokazano w poniższym kodzie:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="configure-tests-to-use-the-emulator"></a>Konfigurowanie testów w celu używania emulatora

Teraz skonfigurujemy nasze testy tak, aby używały emulatora. Zadanie kompilacji emulatora eksportuje zmienną środowiskową „CosmosDbEmulator.Endpoint”, wobec której wszystkie zadania w dalszej części potoku kompilacji mogą wysyłać żądania. 

W tym samouczku użyjemy zadania [Visual Studio Test](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md), aby uruchomić testy jednostkowe skonfigurowane za pomocą pliku **.runsettings**. Aby dowiedzieć się więcej na temat konfigurowania testów jednostkowych, zapoznaj się z [dokumentacją](/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?preserve-view=true&view=vs-2017). Przykładowy kod aplikacji do wykonania, który jest używany w tym dokumencie, jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Poniżej przedstawiono przykładowy plik **.runsettings**, który definiuje parametry przekazywane do testów jednostkowych aplikacji. Należy pamiętać, że używana zmienna `authKey` to [dobrze znany klucz](./local-emulator.md#authenticate-requests) emulatora. Ta zmienna `authKey` to klucz oczekiwany przez zadanie kompilacji emulatora i powinna być zdefiniowana w pliku **.runsettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Jeśli konfigurujesz potok ciągłej integracji/ciągłego wdrażania dla aplikacji, która używa interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, to parametry połączenia bazy danych MongoDB domyślnie zawierają numer portu 10255. Jednak ten port nie jest obecnie otwarty. Jako alternatywny należy używać portu 10250 w celu ustanowienia połączenia. Parametry połączenia interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB pozostają takie same, z wyjątkiem obsługiwanego numeru portu: 10250 zamiast 10255.

Te parametry `TestRunParameters` są wywoływane za pośrednictwem właściwości `TestContext` w projekcie testowym aplikacji. Oto przykład testu wykonywanego względem usługi Cosmos DB.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Przejdź do opcji wykonywania w zadaniu Visual Studio Test. W opcji **Settings file** (Plik ustawień) określ, czy testy zostały skonfigurowane przy użyciu pliku **.runsettings**. W opcji **Override test run parameters** (Zastąp parametry przebiegu testu) dodaj element `-endpoint $(CosmosDbEmulator.Endpoint)`. W ten sposób skonfigurujesz zadanie Test tak, aby odwoływało się do punktu końcowego zadania kompilacji emulatora, a nie do punktu końcowego zdefiniowanego w pliku **.runsettings**.  

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_5.png" alt-text="Zastępowanie zmiennej punktu końcowego punktem końcowym zadania kompilacji emulatora":::

## <a name="run-the-build"></a>Uruchamianie kompilacji

Teraz zapisz kompilację i dodaj ją do kolejki, wybierając pozycję **Save and queue** (Zapisz i dodaj do kolejki). 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_1.png" alt-text="Zrzut ekranu pokazuje kompilację z zaznaczoną kolejką Zapisz &.":::

Po uruchomieniu kompilacji zwróć uwagę, że zadanie emulatora usługi Cosmos DB rozpoczęło ściąganie obrazu platformy Docker z zainstalowanym emulatorem. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_4.png" alt-text="Zrzut ekranu przedstawia ściągające zadanie emulatora Cosmos D B.":::

Po ukończeniu kompilacji zwróć uwagę, że testy uruchomione względem emulatora usługi Cosmos DB za pomocą zadania kompilacji zakończyły się powodzeniem.

:::image type="content" source="./media/tutorial-setup-ci-cd/buildComplete_1.png" alt-text="Zrzut ekranu przedstawia wartość postępu na karcie Podsumowanie.":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z emulatora do celów lokalnego programowania i testowania, zobacz [Programowanie i testowanie w środowisku lokalnym przy użyciu emulatora usługi Azure Cosmos DB](./local-emulator.md).

Aby wyeksportować certyfikaty emulatora TLS/SSL, zobacz [eksportowanie Azure Cosmos DB certyfikatów emulatora do użycia z Java, Python i Node.js](./local-emulator-export-ssl-certificates.md)
