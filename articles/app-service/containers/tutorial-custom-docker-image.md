---
title: 'Samouczek: kompilowanie i uruchamianie niestandardowego obrazu w Azure App Service'
description: Przewodnik krok po kroku dotyczący tworzenia niestandardowego obrazu systemu Linux, wypychania obrazu do Azure Container Registry, a następnie wdrożenia tego obrazu do Azure App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
ms.custom: mvc, seodec18, tracking-python
ms.openlocfilehash: bfe1e9fd2532e308c474aee6983615e28a8081f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506851"
---
# <a name="tutorial-run-a-custom-docker-image-in-app-service"></a>Samouczek: uruchamianie niestandardowego obrazu platformy Docker w App Service

Azure App Service używa technologii kontenera Docker do hostowania zarówno wbudowanych obrazów, jak i obrazów niestandardowych. Aby wyświetlić listę wbudowanych obrazów, uruchom polecenie interfejsu wiersza polecenia platformy Azure, ["AZ webapp list-Runtimes--Linux"](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes). Jeśli te obrazy nie spełniają Twoich potrzeb, można skompilować i wdrożyć niestandardowy obraz.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie obrazu niestandardowego, jeśli żaden wbudowany obraz nie spełnia Twoich potrzeb
> * Wypychanie obrazu niestandardowego do prywatnego rejestru kontenerów na platformie Azure
> * Uruchamianie obrazu niestandardowego w App Service
> * Konfigurowanie zmiennych środowiskowych
> * Aktualizowanie i ponowne wdrażanie obrazu
> * Uzyskiwanie dostępu do dzienników diagnostycznych
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

Ukończenie tego samouczka wiąże się z niewielką opłatą w Twoim koncie platformy Azure dla rejestru kontenerów i może pociągnąć za sobą dodatkowe koszty obsługi kontenera dłużej niż miesiąc.

## <a name="set-up-your-initial-environment"></a>Konfigurowanie środowiska początkowego

* Posiadanie konta platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Zainstaluj [platformę Docker](https://docs.docker.com/get-started/#setup), która służy do tworzenia obrazów platformy Docker. Zainstalowanie platformy Docker może wymagać ponownego uruchomienia komputera.
* Zainstaluj <a href="/cli/azure/install-azure-cli" target="_blank">interfejs wiersza polecenia platformy Azure</a> 2.0.80 lub nowszy, za pomocą którego uruchamiasz polecenia w dowolnej powłoce, aby udostępnić i skonfigurować zasoby platformy Azure.

Po zainstalowaniu rozwiązania Docker i interfejsu wiersza polecenia platformy Azure Otwórz okno terminalu i sprawdź, czy zainstalowano platformę Docker:

```bash
docker --version
```

Sprawdź również, czy wersja interfejsu wiersza polecenia platformy Azure to 2.0.80 lub nowszy:

```azurecli
az --version
```

Następnie zaloguj się do platformy Azure za pomocą interfejsu wiersza polecenia:

```azurecli
az login
```

`az login`Polecenie spowoduje otwarcie przeglądarki w celu zebrania poświadczeń. Po zakończeniu wykonywania polecenia są wyświetlane dane wyjściowe JSON zawierające informacje o Twoich subskrypcjach.

Po zalogowaniu możesz uruchamiać polecenia platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, aby pracować z zasobami w ramach subskrypcji.

## <a name="clone-or-download-the-sample-app"></a>Klonowanie lub pobieranie przykładowej aplikacji

Możesz uzyskać przykład dla tego samouczka za pośrednictwem klonowania lub pobierania usługi git.

### <a name="clone-with-git"></a>Klonowanie za pomocą usługi git

Klonuj repozytorium przykładowe:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Pamiętaj, aby uwzględnić `--config core.autocrlf=input` argument w celu zagwarantowania prawidłowych końców wierszy w plikach, które są używane wewnątrz kontenera systemu Linux:

Następnie przejdź do tego folderu:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Pobieranie z witryny GitHub

Zamiast używać klonowania git, można odwiedzać [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) , wybrać opcję **klonowanie**, a następnie wybrać pozycję **Pobierz plik zip**. 

Rozpakuj plik ZIP do folderu o nazwie *Docker-Django-webapp-Linux*. 

Następnie otwórz okno terminalu w folderze *Docker-Django-webapp-Linux* .

## <a name="optional-examine-the-docker-file"></a>Obowiązkowe Badanie pliku platformy Docker

Plik w przykładzie o nazwie _pliku dockerfile_ , który zawiera opis obrazu platformy Docker i zawiera instrukcje dotyczące konfiguracji:

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
* Druga grupa poleceń tworzy serwer [SSH](https://www.ssh.com/ssh/protocol/) do bezpiecznej komunikacji między kontenerem a hostem.
* Ostatni wiersz, `ENTRYPOINT ["init.sh"]` , wywołuje, `init.sh` Aby uruchomić usługę SSH i serwer Python.

## <a name="build-and-test-the-image-locally"></a>Tworzenie i testowanie obrazu lokalnie

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
    > Jeśli używasz programu w systemie Windows i widzisz błąd, *standard_init_linux. go: 211: exec proces użytkownika spowodował "Brak takiego pliku lub katalogu"*, plik *init.sh* zawiera końce wiersza CR-LF zamiast oczekiwanych końców LF. Ten błąd występuje, jeśli używasz narzędzia Git do klonowania przykładowego repozytorium, ale pominięto `--config core.autocrlf=input` parametr. W takim przypadku należy ponownie sklonować repozytorium z argumentem "--config". Ten błąd może również zostać wyświetlony, Jeśli edytowano *init.sh* i zapisano go z zakończeniami CRLF. W takim przypadku Zapisz ponownie plik z znakami LF.

1. Przejdź do `http://localhost:8000` strony, aby sprawdzić, czy aplikacja sieci Web i kontener działają prawidłowo.

    ![Lokalne testowanie aplikacji internetowych](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W tej sekcji i tych, które obserwujesz, udostępniamy zasoby na platformie Azure, do których należy wypchnąć obraz, a następnie wdrożyć kontener do Azure App Service. Zacznij od utworzenia grupy zasobów, w której mają być zbierane wszystkie te zasoby.

Uruchom polecenie [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) , aby utworzyć grupę zasobów:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Możesz zmienić wartość, `--location` Aby określić region w sąsiedztwie.

## <a name="push-the-image-to-azure-container-registry"></a>Wypchnij obraz do Azure Container Registry

W tej sekcji popchnij obraz do Azure Container Registry, z którego App Service mogą go wdrożyć.

1. Uruchom [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) polecenie, aby utworzyć Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Zamień na `<registry-name>` odpowiednią nazwę rejestru. Nazwa może zawierać tylko litery i cyfry i musi być unikatowa we wszystkich na platformie Azure.

1. Uruchom [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) polecenie, aby pobrać poświadczenia dla rejestru:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    Dane wyjściowe JSON tego polecenia zawierają dwa hasła wraz z nazwą użytkownika rejestru.
    
1. Użyj `docker login` polecenia, aby zalogować się do rejestru kontenerów:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Zastąp `<registry-name>` `<registry-username>` wartości i wartościami z poprzednich kroków. Po wyświetleniu monitu wpisz jedno z haseł z poprzedniego kroku.

    Ta sama nazwa rejestru jest używana we wszystkich pozostałych krokach tej sekcji.

1. Gdy logowanie powiedzie się, Oznacz lokalny obraz platformy Docker jako rejestr:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Użyj `docker push` polecenia, aby wypchnąć obraz do rejestru:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Przekazywanie obrazu po raz pierwszy może potrwać kilka minut, ponieważ zawiera obraz podstawowy. Kolejne operacje przekazywania są zwykle szybsze.

    Podczas oczekiwania możesz wykonać kroki opisane w następnej sekcji, aby skonfigurować App Service do wdrożenia z rejestru.

1. Użyj `az acr repository list` polecenia, aby sprawdzić, czy wypychanie zakończyło się pomyślnie:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    Dane wyjściowe powinny zawierać nazwę obrazu.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Konfigurowanie App Service do wdrożenia obrazu z rejestru

Aby wdrożyć kontener do Azure App Service, należy najpierw utworzyć aplikację sieci Web na App Service, a następnie połączyć aplikację sieci Web z rejestrem kontenerów. Gdy aplikacja sieci Web zostanie uruchomiona, App Service automatycznie pobiera obraz z rejestru.

1. Utwórz plan App Service przy użyciu [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) polecenia:

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Plan App Service odpowiada maszynie wirtualnej, która hostuje aplikację sieci Web. Domyślnie poprzednie polecenie używa niedrogiej [warstwy cenowej B1](https://azure.microsoft.com/pricing/details/app-service/linux/) , która jest bezpłatna przez pierwszy miesiąc. Warstwę można kontrolować za pomocą `--sku` parametru.

1. Utwórz aplikację internetową za pomocą [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) polecenia:

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Zamień na `<app-name>` nazwę aplikacji sieci Web, która musi być unikatowa dla wszystkich platform Azure. Zastąp także `<registry-name>` nazwą rejestru z poprzedniej sekcji.

1. Użyj, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) Aby ustawić `WEBSITES_PORT` zmienną środowiskową zgodnie z oczekiwaniami w kodzie aplikacji: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Zamień `<app-name>` na nazwę użytą w poprzednim kroku.
    
    Aby uzyskać więcej informacji na temat tej zmiennej środowiskowej, zobacz [plik Readme w repozytorium GitHub przykładu](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Włącz [zarządzaną tożsamość](/azure/app-service/overview-managed-identity) aplikacji sieci Web za pomocą [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) polecenia:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Zamień `<app-name>` na nazwę użytą w poprzednim kroku. Dane wyjściowe polecenia (filtrowane przez `--query` `--output` argumenty i) to nazwa główna usługi przypisanej tożsamości, która jest używana wkrótce.

    Tożsamość zarządzana umożliwia udzielenie uprawnień aplikacji sieci Web w celu uzyskania dostępu do innych zasobów platformy Azure bez konieczności ponoszenia określonych poświadczeń.

1. Pobierz swój identyfikator subskrypcji za pomocą [`az account show`](/cli/azure/account?view=azure-cli-latest#az-account-show) polecenia, którego potrzebujesz w następnym kroku:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Przyznaj aplikacji sieci Web uprawnienia dostępu do rejestru kontenerów:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Zastąp następujące wartości:
    - `<principal-id>`z IDENTYFIKATORem jednostki usługi z `az webapp identity assign` polecenia
    - `<registry-name>`Nazwa rejestru kontenerów
    - `<subscription-id>`z IDENTYFIKATORem subskrypcji pobranym z `az account show` polecenia

Aby uzyskać więcej informacji o tych uprawnieniach, zobacz [co to jest kontrola dostępu oparta na rolach na platformie Azure](/azure/role-based-access-control/overview) i 

## <a name="deploy-the-image-and-test-the-app"></a>Wdrażanie obrazu i testowanie aplikacji

Te kroki można wykonać po wypchnięciu obrazu do rejestru kontenerów, a App Service jest w pełni inicjowane.

1. Użyj [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) polecenia, aby określić rejestr kontenerów i obraz do wdrożenia dla aplikacji sieci Web:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Zastąp ciąg `<app_name>` nazwą aplikacji sieci Web i Zastąp ciąg `<registry-name>` w dwóch miejscach nazwą rejestru. 

    - W przypadku korzystania z rejestru innego niż centrum platformy Docker (jak pokazano w tym przykładzie), `--docker-registry-server-url` musi być sformatowany w `https://` pełni kwalifikowana nazwa domeny rejestru.
    - Komunikat "nie podano poświadczeń do dostępu Azure Container Registry. Trwa próba wyszukania... " informuje o tym, że platforma Azure korzysta z tożsamości zarządzanej aplikacji do uwierzytelniania za pomocą rejestru kontenerów, a nie monituje o podanie nazwy użytkownika i hasła.
    - Jeśli wystąpi błąd, obiekt "AttributeError:" NoneType "nie ma atrybutu" Reserved ", upewnij się, że `<app-name>` jest poprawny.

    > [!TIP]
    > Ustawienia kontenera aplikacji sieci Web można pobrać w dowolnym momencie za pomocą polecenia `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . Obraz jest określony we właściwości `DOCKER_CUSTOM_IMAGE_NAME` . Gdy aplikacja sieci Web jest wdrażana za pomocą szablonów usługi Azure DevOps lub Azure Resource Manager, obraz może być również wyświetlany we właściwości o nazwie `LinuxFxVersion` . Obie właściwości obsługują ten sam cel. Jeśli obie są obecne w konfiguracji aplikacji sieci Web, `LinuxFxVersion` ma pierwszeństwo.

1. Po `az webapp config container set` zakończeniu wykonywania polecenia aplikacja sieci Web powinna być uruchomiona w kontenerze na App Service.

    Aby przetestować aplikację, przejdź do `http://<app-name>.azurewebsites.net` , zastępując ją `<app-name>` nazwą aplikacji sieci Web. Przy pierwszym dostępie aplikacja może zająć trochę czasu, ponieważ App Service musi ściągnąć cały obraz z rejestru. Jeśli przeglądarka przeprowadzi limit czasu, należy odświeżyć stronę. Gdy początkowy obraz zostanie pobrany, kolejne testy będą wykonywane znacznie szybciej.

    ![Pomyślne przetestowanie aplikacji sieci Web na platformie Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Zmodyfikuj kod aplikacji i Wdróż ponownie

W tej sekcji wprowadzisz zmiany w kodzie aplikacji sieci Web, odbudujesz kontener, a następnie wypchnij kontener do rejestru. App Service następnie automatycznie pobiera zaktualizowany obraz z rejestru w celu zaktualizowania uruchomionej aplikacji sieci Web.

1. W lokalnym folderze *Docker-Django-webapp-Linux* Otwórz plik *App/templates/App/index.html*.

1. Zmień pierwszy element HTML w taki sposób, aby był zgodny z poniższym kodem.

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

1. Przejdź do folderu *Docker-Django-webapp-Linux* i Skompiluj ponownie obraz:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Zaktualizuj numer wersji w tagu obrazu do 1.0.1 v:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Zastąp ciąg `<registry-name>` nazwą rejestru.

1. Wypchnij obraz do rejestru:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Uruchom ponownie aplikację sieci Web:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Zastąp ciąg `<app_name>` nazwą swojej aplikacji internetowej. Po ponownym uruchomieniu App Service pobiera zaktualizowany obraz z rejestru kontenerów.

1. Sprawdź, czy aktualizacja została wdrożona, przechodząc do witryny `http://<app-name>.azurewebsites.net` .

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

    Możesz również sprawdzić pliki dziennika z przeglądarki pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

1. Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie, wpisz **Ctrl** + **C**.

## <a name="connect-to-the-container-using-ssh"></a>Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby włączyć połączenie SSH z kontenerem, należy go skonfigurować dla obrazu niestandardowego. Po uruchomieniu kontenera można otworzyć połączenie SSH.

### <a name="configure-the-container-for-ssh"></a>Konfigurowanie kontenera dla protokołu SSH

Przykładowa aplikacja używana w tym samouczku ma już niezbędną konfigurację w *pliku dockerfile*, która instaluje serwer SSH, a także ustawia poświadczenia logowania. Ta sekcja ma tylko informacje informacyjne. Aby nawiązać połączenie z kontenerem, przejdź do następnej sekcji

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

*Pliku dockerfile* kopiuje także plik *sshd_config* do folderu */etc/ssh/* i uwidacznia port 2222 w kontenerze:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Port 2222 jest portem wewnętrznym dostępnym tylko przez kontenery w sieci mostkowej prywatnej sieci wirtualnej. 

Na koniec skrypt wejścia, *init.sh*, uruchamia serwer SSH.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Otwieranie połączenia SSH z kontenerem

1. Przeglądaj `https://<app-name>.scm.azurewebsites.net/webssh/host` i zaloguj się przy użyciu konta platformy Azure. Zastąp ciąg `<app-name>` nazwą swojej aplikacji internetowej.

1. Po zalogowaniu nastąpi przekierowanie do strony informacyjnej aplikacji sieci Web. Wybierz pozycję **SSH** w górnej części strony, aby otworzyć powłokę i użyć poleceń.

    Na przykład można przejrzeć procesy działające w ramach tego `top` polecenia.
    
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby utworzone w tym artykule mogą wiązać się z bieżącymi kosztami. Aby wyczyścić zasoby, należy usunąć tylko grupę zasobów, która ją zawiera:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Wdrażanie obrazu niestandardowego w rejestrze kontenera prywatnego
> * Wdrażanie i obraz niestandardowy w App Service
> * Aktualizowanie i ponowne wdrażanie obrazu
> * Uzyskiwanie dostępu do dzienników diagnostycznych
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

W następnym samouczku dowiesz się, jak zmapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](../app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Samouczek: wielokontenerowa aplikacja WordPress](tutorial-multi-container-app.md)
