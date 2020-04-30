---
title: 'Samouczek: kompilowanie i uruchamianie obrazu niestandardowego'
description: Dowiedz się, jak utworzyć niestandardowy obraz systemu Linux, który można uruchomić na Azure App Service, wdrożyć go w usłudze Azure Container Rejestrs i uruchomić w App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 2609ff908b3c2f872cb63d3dcd7dcd481d316484
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085862"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Samouczek: Tworzenie niestandardowego obrazu i uruchamianie go w App Service z rejestru prywatnego

[App Service](app-service-linux-intro.md) udostępnia wbudowane obrazy platformy Docker w systemie Linux z obsługą określonych wersji, takich jak php 7,3 i Node. js 10,14. App Service używa technologii kontenera Docker do hostowania zarówno wbudowanych obrazów, jak i obrazów niestandardowych jako platformy jako usługi. W tym samouczku dowiesz się, jak utworzyć obraz niestandardowy i uruchomić go w App Service. Ten wzorzec jest przydatny, gdy wbudowane obrazy nie uwzględniają wybranego przez Ciebie języka lub gdy aplikacja wymaga określonej konfiguracji, której wbudowane obrazy nie obejmują.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie obrazu niestandardowego w rejestrze kontenera prywatnego
> * Uruchamianie obrazu niestandardowego w App Service
> * Konfigurowanie zmiennych środowiskowych
> * Aktualizowanie i ponowne wdrażanie obrazu
> * Uzyskiwanie dostępu do dzienników diagnostycznych
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminalu uruchom następujące polecenie, aby sklonować repozytorium przykładowej aplikacji na maszynę lokalną, a następnie przejść do katalogu zawierającego przykładowy kod.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Tworzenie obrazu na podstawie pliku platformy Docker

W repozytorium Git zapoznaj się z zawartością pliku _Dockerfile_. W tym pliku opisano środowisko Python wymagane do uruchomienia aplikacji. Ponadto obraz skonfiguruje serwer [SSH](https://www.ssh.com/ssh/protocol/) na potrzeby bezpiecznej komunikacji między kontenerem a hostem. Ostatni wiersz w _pliku dockerfile_, `ENTRYPOINT ["init.sh"]`, wywoła `init.sh` , aby uruchomić usługę SSH i serwer Python.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
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

#service SSH start
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Utwórz obraz platformy Docker za pomocą `docker build` polecenia.

```bash
docker build --tag mydockerimage .
```

Sprawdź, czy kompilacja działa, uruchamiając kontener platformy Docker. Wydaj [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) polecenie i Przekaż do niego nazwę oraz tag obrazu. Pamiętaj o określeniu portu przy użyciu argumentu `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Upewnij się, że aplikacja internetowa i kontener działają prawidłowo, przechodząc do adresu `http://localhost:8000`.

![Lokalne testowanie aplikacji internetowych](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

Aby utworzyć aplikację, która używa właśnie utworzonego obrazu, należy uruchomić polecenia interfejsu CLI platformy Azure, które tworzą grupę zasobów, wypchnięcie obrazu, a następnie utworzenie aplikacji sieci Web App Service plan w celu jej uruchomienia.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

W Cloud Shell Użyj [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) polecenia, aby utworzyć Azure Container Registry.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Zaloguj się do Azure Container Registry

Aby wypchnąć obraz do rejestru, należy uwierzytelnić się w rejestrze prywatnym. W Cloud Shell Użyj [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) polecenia, aby pobrać poświadczenia z utworzonego rejestru.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Dane wyjściowe ujawniają dwa hasła wraz z nazwą użytkownika.

<pre>
{
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "&lt;registry-username&gt;"
}
</pre>

W lokalnym oknie terminala Zaloguj się do Azure Container Registry za pomocą `docker login` polecenia, jak pokazano w poniższym przykładzie. Zastąp * \<ciąg Azure-Container-Registry-Name>* i * \<Registry-username>* wartościami dla rejestru. Po wyświetleniu monitu wpisz jedno z haseł z poprzedniego kroku.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Upewnij się, że logowanie powiedzie się.

### <a name="push-image-to-azure-container-registry"></a>Wypychanie obrazu do usługi Azure Container Registry

Oznacz obraz lokalny dla Azure Container Registry. Przykład:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Wypchnij obraz przy użyciu polecenia `docker push`. Otaguj obraz nazwą rejestru, a następnie nazwą i tagiem obrazu.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Wróć do Cloud Shell, aby sprawdzić, czy wypychanie zakończyło się pomyślnie.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Należy uzyskać następujące dane wyjściowe.

<pre>
[
  "mydockerimage"
]
</pre>

### <a name="create-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Tworzenie aplikacji internetowej

W Cloud Shell Utwórz [aplikację sieci Web](app-service-linux-intro.md) w planie `myAppServicePlan` App Service za pomocą [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) polecenia. Zastąp _ \<>App-Name nazwą_ unikatowej nazwy aplikacji, a _ \<Nazwa rejestru Azure-Container-Registry-Name>_ .

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

<pre>
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="configure-registry-credentials-in-web-app"></a>Konfigurowanie poświadczeń rejestru w aplikacji sieci Web

Aby uzyskać App Service do ściągania obrazu prywatnego, potrzebne są informacje o rejestrze i obrazie. W Cloud Shell podaj je za pomocą [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) polecenia. Zastąp * \<ciąg App-Name>*, * \<Azure-Container-Registry-Name>*, _ \<Registry-username>_ i _ \<>hasła _.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> W przypadku korzystania z rejestru innego niż centrum Docker `--docker-registry-server-url` należy sformatować jako `https://` , a następnie w pełni kwalifikowaną nazwę domeny rejestru.
>

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Większość obrazów platformy Docker używa niestandardowych zmiennych środowiskowych, takich jak port inny niż 80. Poinformuj App Service o porcie używanym przez obraz przy użyciu ustawienia `WEBSITES_PORT` aplikacji. Na stronie usługi GitHub dotyczącej [przykładowego kodu w języku Python w tym samouczku](https://github.com/Azure-Samples/docker-django-webapp-linux) przedstawiono, co jest potrzebne, aby ustawić opcję `WEBSITES_PORT` na wartość _8000_.

Aby ustawić ustawienia aplikacji, użyj [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Testowanie aplikacji internetowej

Upewnij się, że aplikacja internetowa działa, przechodząc do niej (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Gdy uzyskujesz dostęp do aplikacji po raz pierwszy, może to potrwać pewien czas, ponieważ App Service muszą ściągnąć cały obraz. Jeśli przeglądarka przeprowadzi limit czasu, należy odświeżyć stronę.

![Testowanie konfiguracji portu aplikacji internetowej](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Zmienianie aplikacji internetowej i ponowne wdrażanie

W lokalnym repozytorium Git otwórz plik app/templates/app/index.html. Znajdź pierwszy element HTML i zmień go na:

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Po zmodyfikowaniu pliku języka Python i zapisaniu go należy ponownie utworzyć i wypchnąć obraz platformy Docker. Następnie uruchom ponownie aplikację internetową, aby zmiany zaczęły obowiązywać. Użyj tych samych poleceń, których już wcześniej używano w tym samouczku. Można odwołać się do [kompilowania obrazu z pliku Docker](#build-the-image-from-the-docker-file) i [wypychania obrazu do Azure Container Registry](#push-image-to-azure-container-registry). Przetestuj aplikację internetową, postępując zgodnie z instrukcjami w sekcji [Testowanie aplikacji internetowej](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Włącz połączenia SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby włączyć połączenie SSH z kontenerem, należy go skonfigurować dla obrazu niestandardowego. Przyjrzyjmy się przykładowym repozytorium, które ma już niezbędną konfigurację.

* W [pliku dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)Poniższy kod instaluje serwer SSH, a także ustawia poświadczenia logowania.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH jest dostępny tylko za pośrednictwem witryny Kudu/SCM. Witryna kudu/SCM jest uwierzytelniana przy użyciu konta platformy Azure.

* [Pliku dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) kopiuje plik [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) w repozytorium do katalogu */etc/ssh/* .

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Pliku dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) uwidacznia port 2222 w kontenerze. Jest to port wewnętrzny dostępny tylko dla kontenerów w sieci mostkowanej prywatnej sieci wirtualnej. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [Skrypt wejścia](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) uruchamia serwer SSH.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Otwieranie połączenia SSH z kontenerem

Połączenie SSH jest dostępne tylko za pośrednictwem witryny kudu, która jest dostępna `https://<app-name>.scm.azurewebsites.net`pod adresem.

Aby nawiązać połączenie, przejdź pod adres `https://<app-name>.scm.azurewebsites.net/webssh/host` i zaloguj się przy użyciu konta platformy Azure.

Nastąpi wtedy przekierowanie do strony z wyświetloną interaktywną konsolą.

Możesz sprawdzić, czy w kontenerze są uruchomione określone aplikacje. Aby sprawdzić kontener i zweryfikować uruchomione procesy, uruchom polecenie `top` w wierszu polecenia.

```bash
top
```

Polecenie `top` uwidacznia wszystkie uruchomione procesy w kontenerze.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Gratulacje! W App Service został skonfigurowany niestandardowy kontener systemu Linux.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Wdrażanie obrazu niestandardowego w rejestrze kontenera prywatnego
> * Uruchamianie obrazu niestandardowego w App Service
> * Konfigurowanie zmiennych środowiskowych
> * Aktualizowanie i ponowne wdrażanie obrazu
> * Uzyskiwanie dostępu do dzienników diagnostycznych
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

Przejdź do następnego samouczka, aby dowiedzieć się, jak zamapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](../app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Samouczek: wielokontenerowa aplikacja WordPress](tutorial-multi-container-app.md)
