---
title: 'Szybki Start: uruchamianie niestandardowego kontenera na App Service'
description: Rozpocznij pracę z kontenerami na Azure App Service przez wdrożenie pierwszego kontenera niestandardowego.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 4c95e345255b28ba43e474087cdb80fcab493394
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91356421"
---
# <a name="run-a-custom-container-in-azure"></a>Uruchamianie niestandardowego kontenera na platformie Azure

::: zone pivot="container-windows"
[Usługa Azure App Service](overview.md) udostępnia wstępnie zdefiniowane stosy aplikacji w systemie Windows, takie jak ASP.NET lub Node.js, działające w usługach IIS. Wstępnie skonfigurowane środowisko kontenera systemu Windows (wersja zapoznawcza) blokuje system operacyjny z poziomu dostępu administracyjnego, instalacji oprogramowania, zmian w globalnej pamięci podręcznej zestawów itd. Aby uzyskać więcej informacji, zobacz [Funkcja systemu operacyjnego na Azure App Service](operating-system-functionality.md). Jeśli aplikacja wymaga szerszego dostępu niż ten, na który pozwala wstępnie skonfigurowane środowisko, możesz wdrożyć niestandardowy kontener systemu Windows.

Ten przewodnik Szybki Start przedstawia sposób wdrażania aplikacji ASP.NET w obrazie systemu Windows w usłudze [Docker Hub](https://hub.docker.com/) z programu Visual Studio. Aplikacja jest uruchamiana w kontenerze niestandardowym w Azure App Service.

> [!NOTE]
> App Service w kontenerach systemu Windows jest w wersji zapoznawczej.
>

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- <a href="https://hub.docker.com/" target="_blank">Załóż konto usługi Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Zainstaluj program Docker for Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Przełącz platformę Docker na potrzeby uruchamiania kontenerów systemu Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Zainstaluj program Visual Studio 2019</a> przy użyciu obciążeń **ASP.NET oraz tworzenia aplikacji sieci Web** i **platformy Azure** . Jeśli zainstalowano już program Visual Studio 2019:

    - Zainstaluj najnowsze aktualizacje programu Visual Studio, wybierając pozycję **Pomoc**  >  **Sprawdź dostępność aktualizacji**.
    - Dodaj obciążenia w programie Visual Studio, wybierając kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**.

## <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji internetowej platformy ASP.NET

Utwórz aplikację internetową ASP.NET, wykonując następujące czynności:

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt**Znajdź i wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)** dla języka C#, a następnie wybierz pozycję **dalej**.

1. W obszarze **Konfigurowanie nowego projektu**Nadaj nazwę aplikacji _myfirstazurewebapp_, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej platformy ASP.NET. Na potrzeby tego przewodnika Szybki Start wybierz szablon **MVC** .

1. Wybierz opcję **Obsługa platformy Docker**i upewnij się, że uwierzytelnianie jest ustawione na wartość **bez uwierzytelniania**. Wybierz przycisk **Utwórz**.

   ![Tworzenie aplikacji sieci Web ASP.NET](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Jeśli plik _Dockerfile_ nie zostanie automatycznie otwarty, otwórz go w **Eksploratorze rozwiązań**.

1. Potrzebujesz [obsługiwanego obrazu nadrzędnego](configure-custom-container.md#supported-parent-images). Zmień obraz nadrzędny, zastępując wiersz `FROM` następującym kodem i zapisując plik:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację internetową lokalnie.

   ![Uruchamianie aplikacji lokalnie](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publikowanie w usłudze Docker Hub

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **myfirstazurewebapp** i wybierz polecenie **Publikuj**.

1. Wybierz **App Service** a następnie wybierz pozycję **Publikuj**.

1. W obszarze Wybieranie **elementu docelowego publikowania**wybierz pozycję **Container Registry** i usługa **Docker Hub**, a następnie kliknij pozycję **Publikuj**.

   ![Publikowanie ze strony przeglądu projektu](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Podaj poświadczenia konta centrum platformy Docker i wybierz pozycję **Zapisz**.

   Zaczekaj na zakończenie wdrażania. Na stronie **Publikuj** jest teraz wyświetlana nazwa repozytorium do użycia później.

   ![Publikowanie ze strony przeglądu projektu](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Skopiuj tę nazwę repozytorium do późniejszego użycia.

## <a name="create-a-windows-container-app"></a>Tworzenie aplikacji kontenera systemu Windows

1. Zaloguj się w witrynie [Azure Portal]( https://portal.azure.com).

1. W lewym górnym rogu okna witryny Azure Portal wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania powyżej listy zasobów portalu Azure Marketplace Wyszukaj pozycję **Web App for Containers**i wybierz pozycję **Utwórz**.

1. W obszarze **Tworzenie aplikacji sieci Web**wybierz subskrypcję i **grupę zasobów**. W razie konieczności można utworzyć nową grupę zasobów.

1. Podaj nazwę aplikacji, taką jak *win-Container-Demonstracja* , i wybierz **system Windows** dla **systemu operacyjnego**. Wybierz pozycję **Next (dalej): Docker** , aby kontynuować.

   ![Tworzenie Web App for Containers](media/quickstart-custom-container/create-web-app-continer.png)

1. W polu **Źródło obrazu**wybierz pozycję **centrum Docker** i pozycję **obraz i tag**, a następnie wprowadź nazwę repozytorium skopiowaną w obszarze publikowanie w [usłudze Docker Hub](#publish-to-docker-hub).

   ![Skonfiguruj Web App for Containers](media/quickstart-custom-container/configure-web-app-continer.png)

    Jeśli masz obraz niestandardowy dla aplikacji internetowej w innym miejscu, na przykład w [usłudze Azure Container Registry](../container-registry/index.yml) lub w innym prywatnym repozytorium, możesz skonfigurować go tutaj.

1. Wybierz pozycję **Przejrzyj i Utwórz** , a następnie **Utwórz** i poczekaj na platformę Azure, aby utworzyć wymagane zasoby.

## <a name="browse-to-the-container-app"></a>Przechodzenie do aplikacji kontenera

Po zakończeniu operacji platformy Azure zostanie wyświetlone okno powiadomienia.

![Wdrożenie powiodło się](media/quickstart-custom-container/portal-create-finished.png)

1. Kliknij opcję **Przejdź do zasobu**.

1. W obszarze przegląd tego zasobu Użyj linku obok pozycji **adres URL**.

Zostanie otwarta nowa strona przeglądarki na następującej stronie:

![Uruchamianie aplikacji kontenera systemu Windows](media/quickstart-custom-container/app-starting.png)

Poczekaj kilka minut i spróbuj ponownie, dopóki nie zostanie wyświetlona domyślna strona główna aplikacji ASP.NET:

![Uruchomiona aplikacja kontenera systemu Windows](media/quickstart-custom-container/app-running-vs.png)

**Gratulacje!** Uruchamiasz swój pierwszy niestandardowy kontener systemu Windows w usłudze Azure App Service.

## <a name="see-container-start-up-logs"></a>Wyświetlanie dzienników uruchamiania kontenera

Może upłynąć trochę czasu, zanim kontener systemu Windows zostanie załadowany. Aby zobaczyć postęp, przejdź do poniższego adresu URL, zastępując *\<app_name>* nazwę aplikacji.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

Przesyłane strumieniowo dzienniki wyglądają następująco:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Lokalne aktualizowanie i ponowne wdrażanie

1. W programie Visual Studio w **Eksplorator rozwiązań**Otwórz **Widok**  >  **główny**  >  **index. cshtml**.

1. Znajdź tag HTML `<div class="jumbotron">` u góry i zastąp cały element następującym kodem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby ponownie wdrożyć na platformie Azure, kliknij prawym przyciskiem myszy projekt **myfirstazurewebapp** w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.

1. Na stronie publikowania wybierz pozycję **Publikuj** i poczekaj na ukończenie procesu publikowania.

1. Aby nakazać usłudze App Service ściągnięcie obrazu z usługi Docker Hub, uruchom ponownie aplikację. Wróć do strony aplikacji w portalu, a następnie kliknij przycisk **Uruchom ponownie**  >  **tak**.

   ![Ponowne uruchamianie aplikacji internetowej na platformie Azure](./media/quickstart-custom-container/portal-restart-app.png)

Ponownie [przejdź do aplikacji kontenera](#browse-to-the-container-app). Po odświeżeniu strony internetowej najpierw powinna być widoczna strona „Uruchamianie”, a następnie po kilku minutach powinna zostać wyświetlona zaktualizowana strona internetowa.

![Zaktualizowana aplikacja internetowa na platformie Azure](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie do kontenera systemu Windows na platformie Azure](tutorial-custom-container.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
App Service w systemie Linux udostępnia wstępnie zdefiniowane stosy aplikacji w systemie Linux z obsługą języków takich jak .NET, PHP, Node.js i innych. Można także użyć niestandardowego obrazu platformy Docker, aby uruchamiać aplikację internetową na stosie aplikacji, który nie jest zdefiniowany na platformie Azure. W tym przewodniku szybki start przedstawiono sposób wdrażania obrazu z [Azure Container Registry](../container-registry/index.yml) (ACR) do App Service.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto platformy Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure App Service rozszerzenie vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). To rozszerzenie służy do tworzenia i wdrażania Web Apps systemu Linux na platformie Azure jako usługi (PaaS) oraz zarządzania nimi.
* [Rozszerzenie platformy Docker dla vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). To rozszerzenie służy do uproszczenia zarządzania lokalnymi obrazami i poleceniami platformy Docker oraz do wdrażania wbudowanych obrazów aplikacji na platformie Azure.

## <a name="create-an-image"></a>Tworzenie obrazu

Aby ukończyć ten przewodnik Szybki Start, potrzebny będzie odpowiedni obraz aplikacji sieci Web przechowywany w [Azure Container Registry](../container-registry/index.yml). Postępuj zgodnie z instrukcjami w [przewodniku szybki start: Tworzenie prywatnego rejestru kontenerów przy użyciu Azure Portal](../container-registry/container-registry-get-started-portal.md), ale `mcr.microsoft.com/azuredocs/go` zamiast `hello-world` obrazu Użyj obrazu. Aby uzyskać odwołanie, [przykład pliku dockerfile znajduje się w repozytorium przykładów platformy Azure](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Należy pamiętać o ustawieniu opcji **użytkownik administrator** , aby **włączyć** ją podczas tworzenia rejestru kontenerów. Można również ustawić ją z sekcji **klucze dostępu** na stronie rejestru w Azure Portal. To ustawienie jest wymagane do App Service dostępu.

## <a name="sign-in"></a>Zaloguj się

Następnie uruchom VS Code i zaloguj się do konta platformy Azure przy użyciu rozszerzenia App Service. W tym celu wybierz logo platformy Azure na pasku działania, przejdź do Eksploratora **usługi App Service** , a następnie wybierz pozycję **Zaloguj się do platformy Azure** i postępuj zgodnie z instrukcjami.

![Zaloguj się do platformy Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Sprawdzanie wymagań wstępnych

Teraz możesz sprawdzić, czy zostały zainstalowane i prawidłowo skonfigurowane wszystkie wymagania wstępne.

W VS Code adres e-mail platformy Azure powinien zostać wyświetlony na pasku stanu i w ramach subskrypcji w Eksploratorze **usługi App Service** .

Następnie sprawdź, czy zainstalowano i uruchomiono platformę Docker. Następujące polecenie spowoduje wyświetlenie wersji platformy Docker, jeśli jest uruchomiona.

```bash
docker --version
```

Na koniec upewnij się, że Azure Container Registry jest połączona. Aby to zrobić, wybierz logo platformy Docker na pasku działania, a następnie przejdź do **rejestrów**.

![Zrzut ekranu przedstawia wartość rejestrów z rozszerzoną platformą Azure i plikiem z rozszerzeniem kropka i o filename.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Wdróż obraz do Azure App Service

Teraz, gdy wszystko jest skonfigurowane, możesz wdrożyć obraz do [Azure App Service](https://azure.microsoft.com/services/app-service/) bezpośrednio z Eksploratora rozszerzeń platformy Docker.

Znajdź obraz w węźle **rejestry** w Eksploratorze **platformy Docker** i rozwiń go, aby wyświetlić jego Tagi. Kliknij prawym przyciskiem myszy tag, a następnie wybierz polecenie **Wdróż obraz do Azure App Service**.

W tym miejscu postępuj zgodnie z monitami, aby wybrać subskrypcję, globalnie unikatową nazwę aplikacji, grupę zasobów i plan App Service. Wybierz pozycję **B1 podstawowa** dla warstwy cenowej i regionu.

Po wdrożeniu aplikacja jest dostępna pod adresem `http://<app name>.azurewebsites.net` .

**Grupa zasobów** to nazwana Kolekcja wszystkich zasobów aplikacji na platformie Azure. Na przykład grupa zasobów może zawierać odwołanie do witryny sieci Web, bazy danych i funkcji platformy Azure.

**Plan App Service** definiuje zasoby fizyczne, które będą używane do hostowania witryny sieci Web. Ten przewodnik Szybki Start korzysta z **podstawowego** planu hostingu w infrastrukturze systemu **Linux** , co oznacza, że witryna będzie hostowana na komputerze z systemem Linux oraz w innych witrynach sieci Web. Jeśli zaczniesz od planu **Basic** , możesz użyć Azure Portal do skalowania w górę, aby Twoja witryna była jedyną lokacją uruchomioną na komputerze.

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Panel **dane wyjściowe** zostanie otwarty podczas wdrażania, aby wskazać stan operacji. Po zakończeniu operacji Znajdź aplikację utworzoną w Eksploratorze **usługi App Service** , kliknij ją prawym przyciskiem myszy, a następnie wybierz pozycję **Przeglądaj witrynę sieci Web** , aby otworzyć witrynę w przeglądarce.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Następne kroki

Gratulacje, udało Ci się ukończyć ten przewodnik Szybki Start.

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Lub Pobierz je wszystkie, instalując pakiet rozszerzeń [narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .

Zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

::: zone-end