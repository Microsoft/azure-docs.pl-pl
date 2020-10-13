---
title: Wdrażanie aplikacji sieci Web przy użyciu szablonu Azure Cosmos DB
description: Dowiedz się, jak wdrożyć konto usługi Azure Cosmos, Web Apps Azure App Service i przykładową aplikację sieci Web przy użyciu szablonu Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 8e6a6d1c557a765e55152685f08e80ad54bbd903
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362014"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Wdrażanie Azure Cosmos DB i Azure App Service za pomocą aplikacji internetowej z usługi GitHub przy użyciu szablonu Azure Resource Manager

W tym samouczku pokazano, jak wykonać wdrożenie "No Touch" aplikacji sieci Web, która łączy się z Azure Cosmos DB przy pierwszym uruchomieniu bez konieczności wycinania i wklejania informacji o połączeniu z Azure Cosmos DB do `appsettings.json` i do ustawień aplikacji App Services platformy Azure w Azure Portal. Wszystkie te akcje są wykonywane przy użyciu szablonu Azure Resource Manager w ramach jednej operacji. W tym przykładzie zostanie wdrożony [przykładowy Azure Cosmos DB do zrobienia](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) z [samouczka aplikacji sieci Web](sql-api-dotnet-application.md).

Szablony Menedżer zasobów są dość elastyczne i umożliwiają tworzenie złożonych wdrożeń w ramach dowolnej usługi na platformie Azure. Obejmuje to zaawansowane zadania, takie jak wdrażanie aplikacji z usługi GitHub i wprowadzanie informacji o połączeniu do ustawień aplikacji Azure App Service w Azure Portal. W tym samouczku pokazano, jak wykonać następujące czynności przy użyciu jednego szablonu Menedżer zasobów.

* Wdróż konto usługi Azure Cosmos.
* Wdróż Azure App Service plan hostingu.
* Wdróż Azure App Service.
* Wsuń punkt końcowy i klucze z konta usługi Azure Cosmos do ustawień aplikacji App Service w Azure Portal.
* Wdróż aplikację sieci Web z repozytorium GitHub na App Service.

W wyniku wdrożenia jest w pełni funkcjonalna aplikacja sieci Web, która może łączyć się z Azure Cosmos DB bez konieczności wycinania i wklejania adresu URL punktu końcowego Azure Cosmos DB lub kluczy uwierzytelniania z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

> [!TIP]
> Chociaż w tym samouczku nie założono wcześniejszego doświadczenia z szablonami Azure Resource Manager lub JSON, należy zmodyfikować przywoływane szablony lub opcje wdrażania, a następnie trzeba znać każdy z tych obszarów.

## <a name="step-1-deploy-the-template"></a>Krok 1. Wdrażanie szablonu

Najpierw wybierz poniższy przycisk **Wdróż na platformie Azure** , aby otworzyć Azure Portal, aby utworzyć wdrożenie niestandardowe. Możesz również wyświetlić szablon zarządzanie zasobami platformy Azure w [galerii szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdrażanie na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Raz w Azure Portal wybierz subskrypcję do wdrożenia, a następnie wybierz lub Utwórz nową grupę zasobów. Następnie Wypełnij poniższe wartości.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Wdrażanie na platformie Azure":::

* **Region** — jest to wymagane przez Menedżer zasobów. Wprowadź ten sam region, w którym znajdują się zasoby.
* **Nazwa aplikacji** — ta nazwa jest używana przez wszystkie zasoby dla tego wdrożenia. Upewnij się, że wybrano unikatową nazwę, aby uniknąć konfliktów z istniejącymi Azure Cosmos DB i App Service kontami.
* **Lokalizacja** — region, w którym są wdrażane zasoby.
* App Service warstwy cenowej planu **App Service** .
* **Wystąpienia planu App Service** — liczba procesów roboczych dla planu usługi App Service.
* **Adres URL repozytorium** — repozytorium aplikacji sieci Web w witrynie GitHub.
* **Gałąź** — gałąź dla repozytorium GitHub.
* **Nazwa bazy danych** — Nazwa bazy danych usługi Azure Cosmos.
* **Nazwa kontenera** — nazwa kontenera usługi Azure Cosmos.

Po wypełnieniu wartości wybierz przycisk **Utwórz** , aby rozpocząć wdrażanie. Wykonanie tego kroku powinno potrwać od 5 do 10 minut.

> [!TIP]
> Szablon nie sprawdza, czy nazwa Azure App Service i nazwa konta usługi Azure Cosmos wprowadzone w szablonie są prawidłowe i dostępne. Zdecydowanie zaleca się sprawdzenie dostępności nazw planowanych do dostarczenia przed przesłaniem wdrożenia.


## <a name="step-2-explore-the-resources"></a>Krok 2. Eksplorowanie zasobów

### <a name="view-the-deployed-resources"></a>Wyświetlanie wdrożonych zasobów

Po wdrożeniu zasobów przez szablon można teraz zobaczyć każdy z nich w grupie zasobów.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Wdrażanie na platformie Azure":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Wyświetl Cosmos DB punkt końcowy i klucze

Następnie otwórz konto usługi Azure Cosmos w portalu. Poniższy zrzut ekranu przedstawia punkt końcowy i klucze dla konta usługi Azure Cosmos.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Wdrażanie na platformie Azure":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Wyświetl klucze Azure Cosmos DB w ustawieniach aplikacji

Następnie przejdź do Azure App Service w grupie zasobów. Kliknij kartę Konfiguracja, aby wyświetlić ustawienia aplikacji dla App Service. Ustawienia aplikacji zawierają wartości konta Cosmos DB i klucza podstawowego niezbędne do nawiązania połączenia z Cosmos DB, a także nazwy baz danych i kontenerów, które zostały przesłane z wdrożenia szablonu.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Wdrażanie na platformie Azure":::

### <a name="view-web-app-in-deployment-center"></a>Wyświetl aplikację sieci Web w centrum wdrażania

Przejdź do centrum wdrażania App Service. Tutaj zobaczysz punkty repozytorium do repozytorium GitHub przesłane do szablonu. Ponadto stan poniżej wskazuje powodzenie (aktywne), co oznacza, że aplikacja została pomyślnie wdrożona i uruchomiona.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Wdrażanie na platformie Azure":::

### <a name="run-the-web-application"></a>Uruchamianie aplikacji internetowej

Kliknij przycisk **Przeglądaj** w górnej części centrum wdrażania, aby otworzyć aplikację sieci Web. Aplikacja sieci Web zostanie otwarta na ekranie głównym. Kliknij pozycję **Utwórz nową** i wprowadź dane do pól, a następnie kliknij przycisk Zapisz. Ekran wynikowy pokazuje dane zapisane w Cosmos DB.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Wdrażanie na platformie Azure":::

## <a name="step-3-how-does-it-work"></a>Krok 3. jak to działa

Aby to działało, należy wykonać trzy elementy.

### <a name="reading-app-settings-at-runtime"></a>Odczytywanie ustawień aplikacji w czasie wykonywania

Najpierw aplikacja musi zażądać punktu końcowego Cosmos DB i klucza w `Startup` klasie w aplikacji sieci web ASP.NET MVC. [Cosmos DB do wykonania przykład](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) może działać lokalnie, gdzie można wprowadzać informacje o połączeniu do appsettings.js. Jednak po wdrożeniu ten plik jest wdrażany razem z aplikacją. Jeśli te linie w kolorze czerwonym nie mogą uzyskać dostępu do ustawień z appsettings.jsna, spróbuje użyć ustawień aplikacji w Azure App Service.

:::image type="content" source="./media/create-website/startup.png" alt-text="Wdrażanie na platformie Azure":::

### <a name="using-special-azure-resource-management-functions"></a>Używanie specjalnych funkcji zarządzania zasobami platformy Azure

Aby te wartości były dostępne dla aplikacji po wdrożeniu, szablon Azure Resource Manager może zażądać tych wartości z konta Cosmos DB przy użyciu specjalnych funkcji zarządzania zasobami platformy Azure, w tym [odwołań](../azure-resource-manager/templates/template-functions-resource.md#reference) i [listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys) , które ponoszą wartości z konta Cosmos DB, i wstawiaj je do wartości ustawień aplikacji z nazwami kluczy, które są zgodne z ustawieniami używanymi w aplikacji powyżej w formacie "{Section: Key}". Na przykład `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Wdrażanie na platformie Azure":::

### <a name="deploying-web-apps-from-github"></a>Wdrażanie aplikacji internetowych z usługi GitHub

Na koniec należy wdrożyć aplikację sieci Web z usługi GitHub w App Service. W tym celu należy użyć poniższego kodu JSON. W przypadku tego zasobu należy zachować ostrożność. `"type": "sourcecontrols"` `"name": "web"` Wartości właściwości i są na stałe kodowane i nie powinny być zmieniane.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Wdrażanie na platformie Azure":::

## <a name="next-steps"></a>Następne kroki

Gratulacje! Wdrożono Azure Cosmos DB, Azure App Service i przykładową aplikację sieci Web, która automatycznie ma informacje o połączeniu niezbędne do nawiązania połączenia z Cosmos DB, a wszystko to w jednej operacji, bez konieczności wycinania i wklejania poufnych informacji. Korzystając z tego szablonu jako punktu wyjścia, można go zmodyfikować, aby wdrażać własne aplikacje sieci Web w taki sam sposób.

* Aby zapoznać się z szablonem Azure Resource Manager tego przykładu przejdź do [galerii szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* W polu kod źródłowy przykładowej aplikacji przejdź do [Cosmos DB do aplikacji w witrynie GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
