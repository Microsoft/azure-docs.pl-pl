---
title: 'Samouczek: kompilowanie i uruchamianie obrazu niestandardowego w Azure App Service'
description: Przewodnik krok po kroku dotyczący tworzenia niestandardowego obrazu systemu Linux lub Windows, wypychania obrazu do Azure Container Registry, a następnie wdrażania go w Azure App Service. Dowiedz się, jak przeprowadzić migrację wdrożenia niestandardowego oprogramowania App Service w kontenerze niestandardowym.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: azure app service, web app, linux, windows, docker, container
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 0770b46a60f497d3a3da772e7be13ece0526eca0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765705"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Migrowanie niestandardowego oprogramowania do Azure App Service przy użyciu kontenera niestandardowego

::: zone pivot="container-windows"  

[Usługa Azure App Service](overview.md) udostępnia wstępnie zdefiniowane stosy aplikacji w systemie Windows, takie jak ASP.NET lub Node.js, działające w usługach IIS. Wstępnie skonfigurowane środowisko systemu Windows blokuje możliwość dostępu administracyjnego, instalacji oprogramowania, zmian w globalnej pamięci podręcznej zestawów itd. w systemie operacyjnym. Zobacz [Operating system functionality on Azure App Service](operating-system-functionality.md) (Funkcjonalność systemu operacyjnego w usłudze Azure App Service). Korzystając jednak z niestandardowego kontenera systemu Windows w usłudze App Service, można dokonać wymaganych przez aplikację zmian w systemie operacyjnym. Ułatwia to przeprowadzenie migracji lokalnych aplikacji, które wymagają niestandardowych konfiguracji systemu operacyjnego i oprogramowania. W tym samouczku pokazano, jak przeprowadzić migrację do usługi App Service aplikacji ASP.NET, która korzysta z niestandardowych czcionek zainstalowanych w bibliotece czcionek systemu Windows. Polega to na wdrożeniu skonfigurowanego w sposób niestandardowy obrazu systemu Windows z programu Visual Studio do [usługi Azure Container Registry](../container-registry/index.yml), a następnie uruchomieniu go w usłudze App Service.

![Pokazuje aplikację internetową uruchamianą w kontenerze systemu Windows.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- <a href="https://hub.docker.com/" target="_blank">Załóż konto usługi Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Zainstaluj program Docker for Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Przełącz platformę Docker na potrzeby uruchamiania kontenerów systemu Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Zainstaluj Visual Studio 2019</a> r. przy **użyciu ASP.NET tworzenie aplikacji internetowych** i tworzenie aplikacji na platformie **Azure.** Jeśli zainstalowano już program Visual Studio 2019:
    - Zainstaluj najnowsze aktualizacje w programie Visual Studio klikając **pozycję Help** Check for  >  **Updates (Sprawdź pomoc w przypadku aktualizacji).**
    - Dodaj obciążenia w programie Visual Studio klikając pozycję **Narzędzia Pobierz** narzędzia  >  **i funkcje.**

## <a name="set-up-the-app-locally"></a>Lokalne konfigurowanie aplikacji

### <a name="download-the-sample"></a>Pobieranie przykładu

Ten krok umożliwia skonfigurowanie lokalnego projektu platformy .NET.

- [Pobierz przykładowy projekt](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Wyodrębnij (rozpakuj) plik *custom-font-win-container.zip*.

Przykładowy projekt zawiera prostą aplikację platformy ASP.NET, która używa niestandardowej czcionki zainstalowanej w bibliotece czcionek systemu Windows. Instalowanie czcionek nie jest konieczne, ale jest to przykład aplikacji, która jest zintegrowana z bazowym systemem operacyjnym. Aby przeprowadzić migrację takiej aplikacji do usługi App Service, należy zmienić architekturę jej kodu w celu usunięcia integracji lub zmigrować ją w takim stanie, w jakim jest, korzystając z niestandardowego kontenera systemu Windows.

### <a name="install-the-font"></a>Instalowanie czcionki

W Eksploratorze Windows przejdź do folderu _custom-font-win-container-master/CustomFontSample_, kliknij prawym przyciskiem myszy plik _FrederickatheGreat Regular.ttf_ i wybierz pozycję **Zainstaluj**.

Ta czcionka jest dostępna publicznie w serwisie [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Uruchamianie aplikacji

Otwórz plik *custom-font-win-container/CustomFontSample.sln* w programie Visual Studio. 

Naciśnij klawisze `Ctrl+F5`, aby uruchomić aplikację bez debugowania. Aplikacja zostanie wyświetlona w domyślnej przeglądarce. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Zrzut ekranu przedstawiający aplikację wyświetlaną w domyślnej przeglądarce.":::

Ponieważ ta aplikacja używa zainstalowanej czcionki, nie może ona działać w piaskownicy usługi App Service. Aplikację tę można jednak wdrożyć za pomocą kontenera systemu Windows, ponieważ można w nim zainstalować wymaganą czcionkę.

### <a name="configure-windows-container"></a>Konfigurowanie kontenera systemu Windows

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **CustomFontSample** i wybierz pozycję **Dodaj** > **Obsługa aranżacji kontenerów**.

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Zrzut ekranu przedstawiający Eksplorator rozwiązań menu Projekt CustomFontSample, Dodawanie i Obsługa orkiestratora kontenerów wybrane elementy menu.":::

Wybierz **Docker Compose**  >  **OK.**

Projekt jest teraz skonfigurowany do uruchamiania w kontenerze systemu Windows. Plik _Dockerfile_ został dodany do projektu **CustomFontSample**, a projekt **docker-compose** został dodany do rozwiązania. 

W Eksploratorze rozwiązań otwórz plik **Dockerfile**.

Konieczne będzie użycie [obsługiwanego obrazu nadrzędnego](configure-custom-container.md#supported-parent-images). Zmień obraz nadrzędny, zastępując wiersz `FROM` następującym kodem:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Na końcu pliku dodaj następujący wiersz, a następnie zapisz plik:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

W projekcie **CustomFontSample** znajduje się plik _InstallFont.ps1_. Jest to prosty skrypt, który instaluje czcionkę. Bardziej złożoną wersję tego skryptu można znaleźć w witrynie [Script Center](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Aby przetestować kontener systemu Windows lokalnie, upewnij się, że na komputerze lokalnym jest uruchomiona aplikacja Docker.
>

## <a name="publish-to-azure-container-registry"></a>Publikowanie w usłudze Azure Container Registry

Usługa [Azure Container Registry](../container-registry/index.yml) może przechowywać obrazy na potrzeby wdrożeń kontenerów. Usługę App Service można tak skonfigurować, aby używała obrazów hostowanych w usłudze Azure Container Registry.

### <a name="open-publish-wizard"></a>Otwieranie kreatora publikacji

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **CustomFontSample** i wybierz polecenie **Publikuj**.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Zrzut ekranu Eksplorator rozwiązań przedstawiający projekt CustomFontSample i wybraną opcję Publikuj.":::

### <a name="create-registry-and-publish"></a>Tworzenie rejestru i publikowanie

W kreatorze publikowania wybierz pozycję **Container Registry**  >  **Utwórz nowy Azure Container Registry**  >  **Publikuj.**

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Zrzut ekranu przedstawiający kreatora publikowania Container Registry, utwórz nowy Azure Container Registry i wybrany przycisk Publikuj.":::

### <a name="sign-in-with-azure-account"></a>Logowanie się za pomocą konta platformy Azure

W oknie dialogowym **Utwórz nowy rejestr Azure Container Registry** wybierz pozycję **Dodaj konto** i zaloguj się do swojej subskrypcji platformy Azure. Po zalogowaniu wybierz z listy rozwijanej konto zawierające odpowiednią subskrypcję.

![Logowanie do platformy Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Konfigurowanie rejestru

Skonfiguruj nowy rejestr kontenerów, korzystając z sugerowanych wartości z poniższej tabeli. Po zakończeniu kliknij pozycję **Gotowe**.

| Ustawienie  | Sugerowana wartość | Więcej informacji |
| ----------------- | ------------ | ----|
|**Prefiks DNS**| Zachowaj wygenerowaną nazwę rejestru lub zmień ją na inną unikatową nazwę. |  |
|**Grupa zasobów**| Kliknij pozycję **Nowy**, wpisz **myResourceGroup** i kliknij przycisk **OK**. |  |
|**SKU**| Podstawowa | [Warstwy cenowe](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Lokalizacja rejestru**| West Europe | |

![Konfigurowanie rejestru kontenerów platformy Azure](./media/tutorial-custom-container/configure-registry.png)

Zostanie otwarte okno terminalu z wyświetlonym postępem wdrażania obrazu. Zaczekaj na zakończenie wdrażania.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W menu po lewej stronie wybierz **pozycję Utwórz zasób** w sieci  >  **Web**  >  **Web App for Containers**.

### <a name="configure-app-basics"></a>Konfigurowanie podstawowych ustawień aplikacji

Na karcie **Podstawowe skonfiguruj** ustawienia zgodnie z następującą tabelą, a następnie kliknij przycisk **Dalej: Docker**.

| Ustawienie  | Sugerowana wartość | Więcej informacji |
| ----------------- | ------------ | ----|
|**Subskrypcja**| Upewnij się, że wybrano poprawną subskrypcję. |  |
|**Grupa zasobów**| Wybierz **pozycję Utwórz nową,** wpisz **myResourceGroup** i kliknij przycisk **OK.** |  |
|**Nazwa**| Wpisz unikatową nazwę. | Adres URL aplikacji internetowej to `http://<app-name>.azurewebsites.net`, gdzie `<app-name>` to nazwa aplikacji. |
|**Publikowanie**| Kontener platformy Docker | |
|**System operacyjny**| Windows | |
|**Region**| West Europe | |
|**Plan systemu Windows**| Wybierz **pozycję Utwórz nowy,** wpisz **myAppServicePlan** i kliknij przycisk **OK.** | |

Karta **Podstawy powinna** wyglądać tak:

![Przedstawia kartę Podstawy używaną do konfigurowania aplikacji internetowej.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Konfigurowanie kontenera systemu Windows

Na karcie **Docker skonfiguruj** niestandardowy kontener systemu Windows, jak pokazano w poniższej tabeli, a następnie wybierz pozycję **Przejrzyj i utwórz.**

| Ustawienie  | Sugerowana wartość |
| ----------------- | ------------ |
|**Źródło obrazu**| Azure Container Register |
|**Rejestr**| Wybierz [rejestr utworzony wcześniej.](#publish-to-azure-container-registry) |
|**Obraz**| customfontsample |
|**Tag**| latest |

### <a name="complete-app-creation"></a>Kończenie tworzenia aplikacji

Kliknij przycisk **Utwórz** i poczekaj na utworzenie wymaganych zasobów przez platformę Azure.

## <a name="browse-to-the-web-app"></a>Przechodzenie do aplikacji internetowej

Po zakończeniu operacji platformy Azure zostanie wyświetlone okno powiadomienia.

![Pokazuje, że operacja platformy Azure została ukończona.](media/tutorial-custom-container/portal-create-finished.png)

1. Kliknij opcję **Przejdź do zasobu**.

2. Na stronie aplikacji kliknij link w obszarze **Adres URL**.

W przeglądarce zostanie otwarta następująca strona:

![Wyświetla nową stronę przeglądarki dla aplikacji internetowej.](media/tutorial-custom-container/app-starting.png)

Poczekaj kilka minut i spróbuj ponownie. Powinna zostać wyświetlona strona główna z oczekiwaną piękną czcionką:

![Wyświetla stronę główną ze skonfigurowaną czcionką.](media/tutorial-custom-container/app-running.png)

**Gratulacje!** Przeprowadzono migrację aplikacji ASP.NET do usługi Azure App Service w kontenerze systemu Windows.

## <a name="see-container-start-up-logs"></a>Wyświetlanie dzienników uruchamiania kontenera

Może upłynąć trochę czasu, zanim kontener systemu Windows zostanie załadowany. Aby wyświetlić postęp, przejdź do następującego adresu URL, zastępując nazwą *\<app-name>* swojej aplikacji.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Przesyłane strumieniowo dzienniki wyglądają następująco:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service używa technologii kontenera platformy Docker do hostowania zarówno obrazów wbudowanych, jak i niestandardowych. Aby wyświetlić listę wbudowanych obrazów, uruchom polecenie interfejsu wiersza polecenia platformy Azure ["az webapp list-runtimes --linux".](/cli/azure/webapp#az_webapp_list_runtimes) Jeśli te obrazy nie spełniają Twoich potrzeb, możesz skompilować i wdrożyć obraz niestandardowy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie obrazu niestandardowego, jeśli żaden wbudowany obraz nie spełnia Twoich potrzeb
> * Wypychanie obrazu niestandardowego do prywatnego rejestru kontenerów na platformie Azure
> * Uruchamianie obrazu niestandardowego w programie App Service
> * Konfigurowanie zmiennych środowiskowych
> * Aktualizowanie i ponowne wdychaj obraz
> * Uzyskiwanie dostępu do dzienników diagnostycznych
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

Wykonanie instrukcji w tym samouczku wiąże się z naliczeniem niewielkiej opłaty na koncie platformy Azure dla rejestru kontenerów i może ponieść dodatkowe koszty hostowania kontenera przez okres dłuższy niż miesiąc.

## <a name="set-up-your-initial-environment"></a>Konfigurowanie początkowego środowiska

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Zainstaluj [program Docker](https://docs.docker.com/get-started/#setup), który umożliwia kompilowanie obrazów platformy Docker. Zainstalowanie platformy Docker może wymagać ponownego uruchomienia komputera.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.80 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

Po zainstalowaniu platformy Docker lub uruchomieniu Azure Cloud Shell otwórz okno terminalu i sprawdź, czy jest zainstalowana:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>Klonowanie lub pobieranie przykładowej aplikacji

Przykład dla tego samouczka można uzyskać za pośrednictwem narzędzia git clone lub download.

### <a name="clone-with-git"></a>Klonowanie za pomocą narzędzia git

Sklonuj przykładowe repozytorium:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Pamiętaj, aby dołączyć argument , aby zagwarantować prawidłowe zakończenia wiersza w `--config core.autocrlf=input` plikach, które są używane wewnątrz kontenera systemu Linux:

Następnie przejdź do tego folderu:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Pobieranie z witryny GitHub

Zamiast korzystać z polecenia git clone, możesz odwiedzić stronę [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) , wybrać pozycję Clone (Sklonuj), a następnie wybrać pozycję Download ZIP **(Pobierz plik ZIP).**  

Rozpakuj plik ZIP do folderu o nazwie *docker-django-webapp-linux.* 

Następnie otwórz okno terminalu w tym *folderze docker-django-webapp-linux.*

## <a name="optional-examine-the-docker-file"></a>(Opcjonalnie) Badanie pliku platformy Docker

Plik w przykładzie o nazwie _Dockerfile,_ który opisuje obraz platformy Docker i zawiera instrukcje konfiguracji:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* Pierwsza grupa poleceń instaluje wymagania aplikacji w środowisku.
* Druga grupa poleceń tworzy serwer [SSH](https://www.ssh.com/ssh/protocol/) na celu bezpieczną komunikację między kontenerem a hostem.
* Ostatni wiersz, `ENTRYPOINT ["init.sh"]` , wywołuje , aby uruchomić `init.sh` usługę SSH i serwer Python.

## <a name="build-and-test-the-image-locally"></a>Lokalne kompilowanie i testowanie obrazu

> [!NOTE]
> Docker Hub przydziały dotyczące liczby anonimowych ściągań na adres IP i liczby uwierzytelnionych ściągnięć na użytkownika bezpłatnego [(zobacz **Transfer danych**)](https://www.docker.com/pricing). Jeśli zauważysz, że ściąganie z Docker Hub jest ograniczone, spróbuj, jeśli jeszcze się `docker login` nie zalogowano.
> 

1. Uruchom następujące polecenie, aby skompilować obraz:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Sprawdź, czy kompilacja działa, uruchamiając kontener platformy Docker lokalnie:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    To [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) polecenie określa port z `-p` argumentem, po którym następuje nazwa obrazu. 
    
    > [!TIP]
    > Jeśli w systemie Windows jest wyświetlany *błąd, standard_init_linux.go:211:* proces użytkownika exec spowodował "brak takiego pliku lub katalogu", *plik init.sh* zawiera zakończenia wiersza CR-LF zamiast oczekiwanych zakończeń LF. Ten błąd występuje, jeśli do sklonowania przykładowego repozytorium używasz narzędzia git, ale pominięto `--config core.autocrlf=input` parametr . W takim przypadku sklonuj repozytorium ponownie przy użyciu argumentu "--config". Ten błąd może również zostać wyświetlony, jeśli init.sh *i* zapisany z zakończeniami CRLF. W takim przypadku ponownie zapisz plik tylko z zakończeniami LF.

1. Przejdź do `http://localhost:8000` strony , aby sprawdzić, czy aplikacja internetowa i kontener działają prawidłowo.

    ![Lokalne testowanie aplikacji internetowych](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W tej sekcji i tych, które są poniżej, aprowizujesz zasoby na platformie Azure, do których wypychasz obraz, a następnie wdrażasz kontener w Azure App Service. Zacznij od utworzenia grupy zasobów, w której będą zbierane wszystkie te zasoby.

Uruchom polecenie [az group create,](/cli/azure/group#az_group_create) aby utworzyć grupę zasobów:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Możesz zmienić `--location` wartość, aby określić region w pobliżu.

## <a name="push-the-image-to-azure-container-registry"></a>Wypychanie obrazu do Azure Container Registry

W tej sekcji wypchniesz obraz do Azure Container Registry, z App Service można go wdrożyć.

1. Uruchom polecenie [`az acr create`](/cli/azure/acr#az_acr_create) , aby utworzyć Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Zastąp `<registry-name>` wartość odpowiednią nazwą rejestru. Nazwa musi zawierać tylko litery i cyfry i musi być unikatowa na całej platformie Azure.

1. Uruchom polecenie [`az acr show`](/cli/azure/acr#az_acr_show) , aby pobrać poświadczenia dla rejestru:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    Dane wyjściowe JSON tego polecenia zapewniają dwa hasła wraz z nazwą użytkownika rejestru.
    
1. Użyj polecenia `docker login` , aby zalogować się do rejestru kontenerów:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Zastąp `<registry-name>` wartości `<registry-username>` i wartościami z poprzednich kroków. Po wyświetleniu monitu wpisz jedno z haseł z poprzedniego kroku.

    Użyj tej samej nazwy rejestru we wszystkich pozostałych krokach tej sekcji.

1. Po zakończeniu logowania otaguj lokalny obraz platformy Docker dla rejestru:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Użyj polecenia `docker push` , aby wypchnąć obraz do rejestru:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Przekazywanie obrazu po raz pierwszy może potrwać kilka minut, ponieważ zawiera obraz podstawowy. Kolejne przekazywanie jest zwykle szybsze.

    Podczas oczekiwania możesz wykonać kroki opisane w następnej sekcji, aby skonfigurować App Service do wdrożenia z rejestru.

1. Użyj polecenia `az acr repository list` , aby sprawdzić, czy wypychanie powiodło się:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    Dane wyjściowe powinny pokazywać nazwę obrazu.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Konfigurowanie App Service wdrażania obrazu z rejestru

Aby wdrożyć kontener na Azure App Service, najpierw utwórz aplikację internetową w witrynie App Service, a następnie połącz aplikację internetową z rejestrem kontenerów. Gdy aplikacja internetowa jest uruchamiana, App Service automatycznie ściąga obraz z rejestru.

1. Utwórz plan App Service za pomocą [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) polecenia :

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Plan App Service odpowiada maszynie wirtualnej, która hostuje aplikację internetową. Domyślnie poprzednie polecenie używa niedrogiej warstwy cenowej [B1,](https://azure.microsoft.com/pricing/details/app-service/linux/) która jest bezpłatna przez pierwszy miesiąc. Możesz kontrolować warstwę za pomocą `--sku` parametru .

1. Utwórz aplikację internetową za pomocą [`az webpp create`](/cli/azure/webapp#az_webapp_create) polecenia :

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Zastąp nazwą aplikacji internetowej, która `<app-name>` musi być unikatowa na całej platformie Azure. Zastąp `<registry-name>` również nazwą rejestru z poprzedniej sekcji.

1. Użyj [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) , aby ustawić `WEBSITES_PORT` zmienną środowiskową zgodnie z oczekiwaniami w kodzie aplikacji: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Zastąp `<app-name>` nazwą u używaną w poprzednim kroku.
    
    Aby uzyskać więcej informacji na temat tej zmiennej środowiskowej, zobacz [readme w repozytorium GitHub przykładu](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Włącz [tożsamość zarządzaną](./overview-managed-identity.md) dla aplikacji internetowej za pomocą [`az webapp identity assign`](/cli/azure/webapp/identity#az_webapp_identity-assign) polecenia :

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Zastąp `<app-name>` nazwą u używaną w poprzednim kroku. Dane wyjściowe polecenia (filtrowane według argumentów i) są jednostką usługi przypisanej `--query` `--output` tożsamości, której wkrótce użyjemy.

    Tożsamość zarządzana umożliwia przyznawanie aplikacji internetowej uprawnień dostępu do innych zasobów platformy Azure bez konieczności poświadczeń.

1. Pobierz identyfikator subskrypcji za pomocą [`az account show`](/cli/azure/account#az_account_show) polecenia , które będzie potrzebne w następnym kroku:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Przyznaj aplikacji internetowej uprawnienia dostępu do rejestru kontenerów:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Zastąp następujące wartości:
    - `<principal-id>` z identyfikatorem jednostki usługi z `az webapp identity assign` polecenia
    - `<registry-name>` z nazwą rejestru kontenerów
    - `<subscription-id>` z identyfikatorem subskrypcji pobranym z `az account show` polecenia

Aby uzyskać więcej informacji na temat tych uprawnień, zobacz Co to jest kontrola dostępu oparta na rolach [na platformie Azure](../role-based-access-control/overview.md) i 

## <a name="deploy-the-image-and-test-the-app"></a>Wdrażanie obrazu i testowanie aplikacji

Te kroki można wykonać, gdy obraz zostanie wypchnięty do rejestru kontenerów, a App Service zostanie w pełni zaaprowizowana.

1. Użyj polecenia , aby określić rejestr kontenerów i obraz [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set) do wdrożenia dla aplikacji internetowej:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Zastąp wartość nazwą aplikacji internetowej, a w dwóch miejscach zastąp `<app_name>` `<registry-name>` nazwą rejestru. 

    - W przypadku korzystania z rejestru innego Docker Hub (jak pokazano w tym przykładzie) musi być sformatowany zgodnie z w pełni kwalifikowaną nazwą `--docker-registry-server-url` `https://` domeny rejestru.
    - Komunikat "Nie podano poświadczeń w celu uzyskania dostępu do Azure Container Registry. Próba wyszukiwania..." informuje o tym, że platforma Azure używa tożsamości zarządzanej aplikacji do uwierzytelniania w rejestrze kontenerów, zamiast prosić o nazwę użytkownika i hasło.
    - Jeśli wystąpi błąd "AttributeError: 'NoneType' object has no attribute 'reserved'" (Obiekt AttributeError: 'NoneType' nie ma atrybutu 'reserved'), upewnij się, że `<app-name>` jest poprawny.

    > [!TIP]
    > Ustawienia kontenera aplikacji internetowej można pobrać w dowolnym momencie za pomocą polecenia `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . Obraz jest określony we właściwości `DOCKER_CUSTOM_IMAGE_NAME` . Gdy aplikacja internetowa jest wdrażana za pomocą Azure DevOps lub szablonów Azure Resource Manager, obraz może również pojawić się we właściwości o nazwie `LinuxFxVersion` . Obie właściwości służą do tego samego celu. Jeśli obie opcje są obecne w konfiguracji aplikacji internetowej, `LinuxFxVersion` ma pierwszeństwo.

1. Po `az webapp config container set` zakończeniu działania polecenia aplikacja internetowa powinna być uruchomiona w kontenerze na App Service.

    Aby przetestować aplikację, przejdź do strony , zastępując `http://<app-name>.azurewebsites.net` pozycję nazwą swojej aplikacji `<app-name>` internetowej. Przy pierwszym dostępie odpowiedź aplikacji może zająć trochę czasu, ponieważ App Service ściągnięcie całego obrazu z rejestru. Jeśli przeglądarka zostanie przeoczana, wystarczy odświeżyć stronę. Po ściągniętiu obrazu początkowego kolejne testy będą uruchamiane znacznie szybciej.

    ![Pomyślny test aplikacji internetowej na platformie Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Modyfikowanie kodu aplikacji i ponowne jej wdychaj

W tej sekcji zmienisz kod aplikacji internetowej, ponownie skompilowasz kontener, a następnie wypchniesz kontener do rejestru. App Service następnie automatycznie ściąga zaktualizowany obraz z rejestru w celu zaktualizowania uruchomionej aplikacji internetowej.

1. W lokalnym *folderze docker-django-webapp-linux* otwórz plik *app/templates/app/index.html*.

1. Zmień pierwszy element HTML, aby był zgodne z poniższym kodem.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Zapisz zmiany.

1. Zmień folder *na docker-django-webapp-linux* i ponownie sbuduj obraz:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Zaktualizuj numer wersji w tagu obrazu do wersji 1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Zastąp ciąg `<registry-name>` nazwą rejestru.

1. Wypchnie obraz do rejestru:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Uruchom ponownie aplikację internetową:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Zastąp ciąg `<app_name>` nazwą swojej aplikacji internetowej. Po ponownym uruchomieniu App Service zaktualizowany obraz z rejestru kontenerów.

1. Sprawdź, czy aktualizacja została wdrożona, przeglądając adres `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

1. Włącz rejestrowanie kontenerów:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Włącz strumień dziennika:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

    Pliki dzienników można także sprawdzać w przeglądarce pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie, wpisz **Ctrl** + **C**.

## <a name="connect-to-the-container-using-ssh"></a>Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby włączyć połączenie SSH z kontenerem, należy skonfigurować dla niego obraz niestandardowy. Po uruchomieniu kontenera możesz otworzyć połączenie SSH.

### <a name="configure-the-container-for-ssh"></a>Konfigurowanie kontenera dla SSH

Przykładowa aplikacja używana w tym samouczku ma już niezbędną konfigurację w pliku *Dockerfile,* który instaluje serwer SSH, a także ustawia poświadczenia logowania. Ta sekcja zawiera tylko informacje. Aby nawiązać połączenie z kontenerem, przejdź do następnej sekcji

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH jest dostępny tylko za pośrednictwem witryny Kudu/SCM. Witryna Kudu/SCM jest uwierzytelniana przy użyciu konta platformy Azure.

Plik *Dockerfile* kopiuje również *sshd_config* do folderu */etc/ssh/* i uwidacznia port 2222 w kontenerze:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Port 2222 jest portem wewnętrznym dostępnym tylko dla kontenerów w sieci mostka prywatnej sieci wirtualnej. 

Na koniec skrypt wejściowy, *init.sh*, uruchamia serwer SSH.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Otwieranie połączenia SSH z kontenerem

1. Przejdź do `https://<app-name>.scm.azurewebsites.net/webssh/host` strony i zaloguj się przy użyciu konta platformy Azure. Zastąp ciąg `<app-name>` nazwą swojej aplikacji internetowej.

1. Po zalogowaniu nastąpi przekierowanie do strony informacyjnej aplikacji internetowej. Wybierz **pozycję SSH** w górnej części strony, aby otworzyć powłokę i użyć poleceń.

    Na przykład możesz zbadać działające w nim procesy przy użyciu `top` polecenia .
    
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby utworzone w tym artykule mogą wiązać się z bieżącymi kosztami. Aby wyczyścić zasoby, wystarczy usunąć tylko grupę zasobów, która je zawiera:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Wdrażanie obrazu niestandardowego w prywatnym rejestrze kontenerów
> * Wdrażanie i obraz niestandardowy w App Service
::: zone pivot="container-linux"
> * Aktualizowanie i ponowne wdychaj obraz
::: zone-end
> * Uzyskiwanie dostępu do dzienników diagnostycznych
::: zone pivot="container-linux"
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH
::: zone-end

Z następnego samouczka dowiesz się, jak zamapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](app-service-web-tutorial-custom-domain.md)

Możesz też sprawdzić inne zasoby:

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Samouczek: aplikacja WordPress z wieloma kontenerami](tutorial-multi-container-app.md)
::: zone-end
