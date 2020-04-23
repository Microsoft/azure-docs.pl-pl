---
title: 'Samouczek: Tworzenie i uruchamianie obrazu niestandardowego'
description: Dowiedz się, jak utworzyć niestandardowy obraz systemu Linux, który można uruchomić w usłudze Azure App Service, wdrożyć go w rejestrach kontenerów platformy Azure i uruchomić go w usłudze App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 2609ff908b3c2f872cb63d3dcd7dcd481d316484
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085862"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Samouczek: Tworzenie obrazu niestandardowego i uruchamianie w usłudze App Service z rejestru prywatnego

[Usługa App Service](app-service-linux-intro.md) udostępnia wbudowane obrazy platformy Docker w systemie Linux z obsługą określonych wersji, takich jak PHP 7.3 i Node.js 10.14. Usługa App Service używa technologii kontenera platformy Docker do hostowania zarówno wbudowanych obrazów, jak i obrazów niestandardowych jako platformy jako usługi. W tym samouczku dowiesz się, jak utworzyć obraz niestandardowy i uruchomić go w usłudze App Service. Ten wzorzec jest przydatny, gdy wbudowane obrazy nie uwzględniają wybranego przez Ciebie języka lub gdy aplikacja wymaga określonej konfiguracji, której wbudowane obrazy nie obejmują.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie obrazu niestandardowego w rejestrze kontenerów prywatnych
> * Uruchamianie obrazu niestandardowego w usłudze aplikacji
> * Konfigurowanie zmiennych środowiskowych
> * Aktualizowanie i ponowne rozmieszczanie obrazu
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

W repozytorium Git zapoznaj się z zawartością pliku _Dockerfile_. W tym pliku opisano środowisko Python wymagane do uruchomienia aplikacji. Ponadto obraz skonfiguruje serwer [SSH](https://www.ssh.com/ssh/protocol/) na potrzeby bezpiecznej komunikacji między kontenerem a hostem. Ostatni wiersz w _pliku Dockerfile_, `ENTRYPOINT ["init.sh"]`wywołuje `init.sh` uruchomienie usługi SSH i serwera Python.

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

Tworzenie obrazu platformy Docker za `docker build` pomocą polecenia.

```bash
docker build --tag mydockerimage .
```

Sprawdź, czy kompilacja działa, uruchamiając kontener platformy Docker. Wydaj [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) polecenie i przekaż jej nazwę i znacznik obrazu. Pamiętaj o określeniu portu przy użyciu argumentu `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Upewnij się, że aplikacja internetowa i kontener działają prawidłowo, przechodząc do adresu `http://localhost:8000`.

![Lokalne testowanie aplikacji internetowych](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

Aby utworzyć aplikację, która używa obrazu, który właśnie utworzono, należy uruchomić polecenia interfejsu wiersza polecenia platformy Azure, które tworzą grupę zasobów, wypycha obraz, a następnie tworzy aplikację sieci web plan usługi App Service, aby go uruchomić.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

W aplikacji Cloud Shell [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) użyj polecenia, aby utworzyć rejestr kontenerów platformy Azure.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Logowanie się do rejestru kontenerów platformy Azure

Aby wypchnąć obraz do rejestru, należy uwierzytelnić się w rejestrze prywatnym. W usłudze Cloud [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) Shell użyj polecenia, aby pobrać poświadczenia z utworzonego rejestru.

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

W oknie terminala lokalnego zaloguj się do `docker login` rejestru kontenerów platformy Azure przy użyciu polecenia, jak pokazano w poniższym przykładzie. Zastąp * \<>nazwę rejestru azure* i * \<>nazwy rejestru* wartościami dla rejestru. Po wyświetleniu monitu wpisz jedno z haseł z poprzedniego kroku.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Upewnij się, że logowanie powiedzie się.

### <a name="push-image-to-azure-container-registry"></a>Wypychanie obrazu do usługi Azure Container Registry

Oznaczanie obrazu lokalnego dla rejestru kontenerów platformy Azure. Przykład:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Wypchnij obraz przy użyciu polecenia `docker push`. Otaguj obraz nazwą rejestru, a następnie nazwą i tagiem obrazu.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Powrót w Cloud Shell, sprawdź, czy wypychanie zakończyło się pomyślnie.

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

W aplikacji Cloud Shell utwórz [aplikację internetową](app-service-linux-intro.md) w planie usługi `myAppServicePlan` app service za [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) pomocą polecenia. Zastąp _ \<>nazwy aplikacji_ unikatową nazwą aplikacji i _ \<nazwą rejestru azure-container->_ nazwą rejestru.

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

Aby usługa App Service pobierała obraz prywatny, potrzebuje informacji o rejestrze i obrazie. W powłoki chmury, podaj im [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) polecenie. Zastąp * \<>nazwy aplikacji, * * \<>>, * _ \<>nazwy rejestru _i _ \<hasła>_.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> W przypadku korzystania z rejestru `--docker-registry-server-url` innego niż Docker Hub, muszą być sformatowane zgodnie `https://` z w pełni kwalifikowaną nazwą domeny rejestru.
>

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Większość obrazów platformy Docker używa niestandardowych zmiennych środowiskowych, takich jak port inny niż 80. Powiesz usłudze App Service o porcie `WEBSITES_PORT` używanym przez obraz przy użyciu ustawienia aplikacji. Na stronie usługi GitHub dotyczącej [przykładowego kodu w języku Python w tym samouczku](https://github.com/Azure-Samples/docker-django-webapp-linux) przedstawiono, co jest potrzebne, aby ustawić opcję `WEBSITES_PORT` na wartość _8000_.

Aby ustawić ustawienia aplikacji, użyj [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w aplikacji Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Testowanie aplikacji internetowej

Upewnij się, że aplikacja internetowa działa, przechodząc do niej (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Przy pierwszym dostępie do aplikacji może upłynąć trochę czasu, ponieważ usługa App Service musi wyciągnąć cały obraz. Jeśli przesiewnie się przekreślenie czasu przeglądarki, wystarczy odświeżyć stronę.

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

Po zmodyfikowaniu pliku języka Python i zapisaniu go należy ponownie utworzyć i wypchnąć obraz platformy Docker. Następnie uruchom ponownie aplikację internetową, aby zmiany zaczęły obowiązywać. Użyj tych samych poleceń, których już wcześniej używano w tym samouczku. Można odwołać się do [tworzenia obrazu z pliku platformy Docker](#build-the-image-from-the-docker-file) i obrazu [wypychania do rejestru kontenerów platformy Azure](#push-image-to-azure-container-registry). Przetestuj aplikację internetową, postępując zgodnie z instrukcjami w sekcji [Testowanie aplikacji internetowej](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Włączanie połączeń SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby włączyć połączenie SSH do kontenera, należy skonfigurować obraz niestandardowy dla niego. Przyjrzyjmy się przykładowego repozytorium, które ma już niezbędną konfigurację.

* W pliku [Dockerfile poniższy](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)kod instaluje serwer SSH, a także ustawia poświadczenia logowania.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH jest dostępny tylko za pośrednictwem witryny Kudu/SCM. Witryna Kudu/SCM jest uwierzytelniona za pomocą konta platformy Azure.

* [Plik Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) kopiuje plik [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) w repozytorium do katalogu */etc/ssh/.*

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) udostępnia port 2222 w kontenerze. Jest to port wewnętrzny dostępny tylko dla kontenerów w sieci mostkowanej prywatnej sieci wirtualnej. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [Skrypt wpisu](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) uruchamia serwer SSH.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Otwieranie połączenia SSH z kontenerem

Połączenie SSH jest dostępne tylko za pośrednictwem strony `https://<app-name>.scm.azurewebsites.net`Kudu, która jest dostępna pod adresem .

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

Gratulacje! Skonfigurowano niestandardowy kontener systemu Linux w usłudze App Service.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Wdrażanie obrazu niestandardowego w rejestrze kontenerów prywatnych
> * Uruchamianie obrazu niestandardowego w usłudze aplikacji
> * Konfigurowanie zmiennych środowiskowych
> * Aktualizowanie i ponowne rozmieszczanie obrazu
> * Uzyskiwanie dostępu do dzienników diagnostycznych
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

Przejdź do następnego samouczka, aby dowiedzieć się, jak zamapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: Mapowanie niestandardowej nazwy DNS do aplikacji](../app-service-web-tutorial-custom-domain.md)

Możesz też zapoznać się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Samouczek: Wieloobiektowa aplikacja WordPress](tutorial-multi-container-app.md)
