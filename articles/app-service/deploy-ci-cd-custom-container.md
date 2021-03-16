---
title: Ciągłej integracji/ciągłego dostarczania do kontenerów niestandardowych
description: Skonfiguruj ciągłe wdrażanie do niestandardowego kontenera systemu Windows lub Linux w Azure App Service.
keywords: Azure App Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: bc36325b55f049eebef823d836768fccc39a7615
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472177"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Ciągłe wdrażanie z kontenerami niestandardowymi w Azure App Service

W tym samouczku skonfigurujesz ciągłe wdrażanie niestandardowego obrazu kontenera z zarządzanych [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) repozytoriów lub usługi [Docker Hub](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. Przejdź do centrum wdrażania

W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania aplikacji App Service.

W menu po lewej stronie kliknij pozycję Ustawienia **centrum wdrażania**  >  . 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Wybierz źródło wdrożenia

**Wybierz** Źródło wdrożenia zależy od Twojego scenariusza:
- **Rejestr kontenerów** konfiguruje Ci/CD między rejestrem kontenerów a App Service.
- Opcja **działania** w serwisie GitHub jest dla Ciebie, Jeśli przechowujesz kod źródłowy obrazu kontenera w usłudze GitHub. Wyzwalane przez nowe zatwierdzenia w repozytorium GitHub, Akcja wdrożenia może zostać uruchomiona `docker build` i `docker push` bezpośrednio do rejestru kontenerów, a następnie zaktualizowana aplikacja App Service w celu uruchomienia nowego obrazu. Aby uzyskać więcej informacji, zobacz [jak działa usługa Ci/CD z akcjami usługi GitHub](#how-cicd-works-with-github-actions).
- Aby skonfigurować ciągłe i ciągłe dostarczanie za pomocą **Azure Pipelines**, zobacz [wdrażanie kontenera aplikacji sieci Web platformy Azure z Azure Pipelines](/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> W przypadku aplikacji Docker Compose wybierz pozycję **Container Registry**.

W przypadku wybrania akcji GitHub **kliknij pozycję** **Autoryzuj** i postępuj zgodnie z monitami o autoryzację. Jeśli wcześniej masz już autoryzację w usłudze GitHub, możesz wdrożyć ją z repozytorium innego użytkownika, klikając pozycję **Zmień konto**.

Po autoryzowaniu konta platformy Azure w usłudze GitHub **Wybierz** **organizację**, **repozytorium** i **gałąź** , z której chcesz wykonać wdrożenie.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Skonfiguruj ustawienia rejestru
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Skonfiguruj ustawienia rejestru

Aby wdrożyć aplikację wielokontenerową (Docker Compose), **Wybierz pozycję** **Docker Compose** w polu **typ kontenera**.

Jeśli nie widzisz listy rozwijanej **typ kontenera** , przewiń kopię zapasową do **źródła** i **Wybierz** **Container Registry**.
::: zone-end

W polu **Źródło rejestru** **Wybierz** miejsce, w którym znajduje się rejestr kontenerów. Jeśli nie jest on Azure Container Registry ani centrum Docker, **Wybierz pozycję** **Rejestr prywatny**.

::: zone pivot="container-linux"
> [!NOTE]
> Jeśli aplikacja z wieloma kontenerami (Docker Compose) korzysta z więcej niż jednego prywatnego obrazu, upewnij się, że obrazy prywatne znajdują się w tym samym rejestrze prywatnym i są dostępne z tymi samymi poświadczeniami użytkownika. Jeśli aplikacja obsługująca wiele kontenerów używa tylko obrazów publicznych, **Wybierz pozycję** **Docker Hub**, nawet jeśli niektóre obrazy nie znajdują się w usłudze Docker Hub.
::: zone-end  

Wykonaj kolejne kroki, wybierając kartę odpowiadającą wybranej opcji.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

Lista rozwijana **Rejestr** zawiera rejestry w tej samej subskrypcji, w której znajduje się aplikacja. **Wybierz** odpowiedni rejestr.

> [!NOTE]
> W celu wdrożenia z rejestru w innej subskrypcji **Wybierz** w zamian **Rejestr prywatny** w **źródle rejestru** .

::: zone pivot="container-windows"
**Wybierz** **obraz** i **tag** do wdrożenia. Jeśli chcesz, **wpisz** polecenie uruchamiania w **pliku startowym**. 
::: zone-end
::: zone pivot="container-linux"
Postępuj zgodnie z kolejnym krokiem w zależności od **typu kontenera**:
- W obszarze Docker Compose **Wybierz** rejestr dla prywatnych obrazów. **Kliknij pozycję** **Wybierz plik** , aby przekazać [plik Docker Compose](https://docs.docker.com/compose/compose-file/), lub po prostu **Wklej** zawartość pliku Docker Compose do **konfiguracji**.
- W przypadku **jednego kontenera** **Wybierz** **obraz** i **tag** do wdrożenia. Jeśli chcesz, **wpisz** polecenie uruchamiania w **pliku startowym**. 
::: zone-end

App Service dołącza ciąg w **pliku startowym** do [końca `docker run` polecenia (jako `[COMMAND] [ARG...]` Segment)](https://docs.docker.com/engine/reference/run/) podczas uruchamiania kontenera.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
W obszarze **dostęp do repozytorium** **Wybierz** , czy obraz do wdrożenia ma być publiczny, czy prywatny.
::: zone-end
::: zone pivot="container-linux"
W obszarze **dostęp do repozytorium** **Wybierz** , czy obraz do wdrożenia ma być publiczny, czy prywatny. W przypadku aplikacji Docker Compose z co najmniej jednym prywatnym obrazem **Wybierz pozycję** **prywatny**.
::: zone-end

W przypadku wybrania obrazu prywatnego należy **określić** nazwę **logowania** (username) i **hasło** konta platformy Docker.

::: zone pivot="container-windows"
**Podaj** nazwę obrazu i tagu w polu **pełna nazwa obrazu i tag**, oddzielone znakiem `:` (na przykład `nginx:latest` ). Jeśli chcesz, **wpisz** polecenie uruchamiania w **pliku startowym**. 
::: zone-end
::: zone pivot="container-linux"
Postępuj zgodnie z kolejnym krokiem w zależności od **typu kontenera**:
- W obszarze Docker Compose **Wybierz** rejestr dla prywatnych obrazów. **Kliknij pozycję** **Wybierz plik** , aby przekazać [plik Docker Compose](https://docs.docker.com/compose/compose-file/), lub po prostu **Wklej** zawartość pliku Docker Compose do **konfiguracji**.
- W przypadku **jednego kontenera** **Podaj** nazwę obrazu i tagu w polu **pełna nazwa obrazu i tag**, oddzielone znakiem `:` (na przykład `nginx:latest` ). Jeśli chcesz, **wpisz** polecenie uruchamiania w **pliku startowym**. 
::: zone-end

App Service dołącza ciąg w **pliku startowym** do [końca `docker run` polecenia (jako `[COMMAND] [ARG...]` Segment)](https://docs.docker.com/engine/reference/run/) podczas uruchamiania kontenera.

# <a name="private-registry"></a>[Rejestr prywatny](#tab/private)

W polu **adres URL serwera** **wpisz** adres URL serwera, zaczynając od **https://**.

W polu **Nazwa logowania** i **hasło** **wpisz** poświadczenia logowania do rejestru prywatnego.

::: zone pivot="container-windows"
**Podaj** nazwę obrazu i tagu w polu **pełna nazwa obrazu i tag**, oddzielone znakiem `:` (na przykład `nginx:latest` ). Jeśli chcesz, **wpisz** polecenie uruchamiania w **pliku startowym**. 
::: zone-end
::: zone pivot="container-linux"
Postępuj zgodnie z kolejnym krokiem w zależności od **typu kontenera**:
- W obszarze Docker Compose **Wybierz** rejestr dla prywatnych obrazów. **Kliknij pozycję** **Wybierz plik** , aby przekazać [plik Docker Compose](https://docs.docker.com/compose/compose-file/), lub po prostu **Wklej** zawartość pliku Docker Compose do **konfiguracji**.
- W przypadku **jednego kontenera** **Podaj** nazwę obrazu i tagu w polu **pełna nazwa obrazu i tag**, oddzielone znakiem `:` (na przykład `nginx:latest` ). Jeśli chcesz, **wpisz** polecenie uruchamiania w **pliku startowym**. 
::: zone-end

App Service dołącza ciąg w **pliku startowym** do [końca `docker run` polecenia (jako `[COMMAND] [ARG...]` Segment)](https://docs.docker.com/engine/reference/run/) podczas uruchamiania kontenera.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. Włącz CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. Włącz CI/CD
::: zone-end

App Service obsługuje integrację ciągłej integracji/ciągłego wdrażania z usługami Azure Container Registry i Docker Hub. Aby włączyć tę funkcję, **Wybierz pozycję** **włączone** w obszarze **ciągłe wdrażanie**.

::: zone pivot="container-linux"
> [!NOTE]
> Jeśli wybierzesz **Akcje usługi GitHub** w obszarze **Źródło**, nie uzyskasz tej opcji, ponieważ usługa Ci/CD jest obsługiwana bezpośrednio przez akcje usługi GitHub. Zamiast tego zostanie wyświetlona sekcja **konfiguracji przepływu pracy** , w której można **kliknąć pozycję** **Podgląd pliku** , aby sprawdzić plik przepływu pracy. Platforma Azure zatwierdzi ten plik w wybranym repozytorium źródłowym usługi GitHub w celu obsługi zadań kompilowania i wdrażania. Aby uzyskać więcej informacji, zobacz [jak działa usługa Ci/CD z akcjami usługi GitHub](#how-cicd-works-with-github-actions).
::: zone-end

Po włączeniu tej opcji App Service dodaje element webhook do repozytorium w Azure Container Registry lub Docker Hub. Repozytorium zapisuje do tego elementu webhook za każdym razem, gdy wybrany obraz zostanie zaktualizowany przy użyciu programu `docker push` . Element webhook powoduje, że aplikacja App Service będzie ponownie uruchamiana i uruchamiana `docker pull` w celu uzyskania zaktualizowanego obrazu.

W **przypadku innych rejestrów prywatnych** można opublikować element webhook ręcznie lub jako krok w potoku ciągłej integracji/ciągłego wdrażania. W polu **adres URL elementu webhook** **kliknij** przycisk **Kopiuj** , aby uzyskać adres URL elementu webhook.

::: zone pivot="container-linux"
> [!NOTE]
> Obsługa aplikacji wielokontenerowych (Docker Compose) jest ograniczona: 
> - W przypadku Azure Container Registry App Service tworzy element webhook w wybranym rejestrze z rejestrem jako zakres. `docker push`Do dowolnego repozytorium w rejestrze (w tym tych, do których nie odwołuje się plik Docker Compose), wyzwala ponowne uruchomienie aplikacji. Możesz chcieć [zmodyfikować element webhook](../container-registry/container-registry-webhook.md) do węższego zakresu.
> - Centrum platformy Docker nie obsługuje elementów webhook na poziomie rejestru. Elementy webhook należy **dodać** ręcznie do obrazów określonych w pliku Docker Compose.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Zapisz ustawienia
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Zapisz ustawienia
::: zone-end

**Kliknij przycisk** **Zapisz**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Jak współpracuje CI/CD z akcjami GitHub

Jeśli wybierzesz **Akcje usługi GitHub** w obszarze **Źródło** (zobacz [wybieranie źródła wdrożenia](#2-choose-deployment-source)), App Service konfiguruje Ci/CD w następujący sposób:

- Umieszcza w repozytorium GitHub plik przepływu pracy akcji usługi GitHub w celu obsługi kompilowania i wdrażania zadań do App Service.
- Dodaje poświadczenia dla prywatnego rejestru jako wpisy tajne usługi GitHub. Wygenerowany plik przepływu pracy wykonuje akcję [Azure/Docker-login](https://github.com/Azure/docker-login) , aby zalogować się za pomocą prywatnego rejestru, a następnie zostanie uruchomiony `docker push` do wdrożenia.
- Dodaje profil publikowania aplikacji jako wpis tajny usługi GitHub. Wygenerowany plik przepływu pracy używa tego klucza tajnego do uwierzytelniania za pomocą App Service, a następnie uruchamia akcję [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) w celu skonfigurowania zaktualizowanego obrazu, który wyzwala ponowne uruchomienie aplikacji w celu ściągnięcia zaktualizowanego obrazu.
- Przechwytuje informacje z [dzienników przebiegów przepływu pracy](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) i wyświetla je na karcie **dzienniki** w **centrum wdrażania** aplikacji.

Dostawcę kompilacji akcji usługi GitHub można dostosować w następujący sposób:

- Dostosuj plik przepływu pracy po jego wygenerowaniu w repozytorium GitHub. Aby uzyskać więcej informacji, zobacz [składnia przepływu pracy dla akcji usługi GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Po prostu upewnij się, że przepływ pracy jest zakończony przez akcję [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) , aby wyzwolić ponowne uruchomienie aplikacji.
- Jeśli wybrana gałąź jest chroniona, nadal można wyświetlić podgląd pliku przepływu pracy bez zapisywania konfiguracji, a następnie dodać go i wymagane wpisy tajne serwisu GitHub do repozytorium ręcznie. Ta metoda nie zapewnia integracji dzienników z Azure Portal.
- Zamiast profilu publikowania należy wdrożyć przy użyciu [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) w Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Uwierzytelnianie za pomocą nazwy głównej usługi

Ta opcjonalna konfiguracja zastępuje domyślne uwierzytelnianie z profilami publikowania w wygenerowanym pliku przepływu pracy.

**Wygeneruj** jednostkę usługi za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) w [interfejsie CLI platformy Azure](/cli/azure/). W poniższym przykładzie Zastąp *\<subscription-id>* wartości, *\<group-name>* i *\<app-name>* własnymi wartościami. **Zapisz** wszystkie dane wyjściowe JSON dla następnego kroku, w tym najwyższego poziomu `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> W celu zapewnienia bezpieczeństwa należy przyznać minimalny wymagany dostęp do nazwy głównej usługi. Zakres w poprzednim przykładzie jest ograniczony do konkretnej aplikacji App Service, a nie całej grupy zasobów.

W [](https://github.com/)witrynie GitHub **Przejdź** do repozytorium, a następnie **Wybierz pozycję** **Ustawienia > wpisy tajne > Dodaj nowe hasło**. **Wklej** wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. **Nadaj** wpisowi tajnemu nazwę, taką jak `AZURE_CREDENTIALS` .

W pliku przepływu pracy wygenerowanego przez **centrum wdrażania** **Popraw** `azure/webapps-deploy` krok przy użyciu kodu, takiego jak Poniższy przykład:

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatyzowanie przy użyciu interfejsu wiersza polecenia

Aby skonfigurować rejestr kontenerów i obraz platformy Docker, **Uruchom** polecenie [AZ webapp config Container Set](/cli/azure/webapp/config/container#az-webapp-config-container-set).

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Rejestr prywatny](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Aby skonfigurować aplikację wielokontenerową (Docker Compose), **Przygotuj** plik Docker Compose lokalnie, a następnie **Uruchom** polecenie [AZ webapp config Container Set](/cli/azure/webapp/config/container#az-webapp-config-container-set) z `--multicontainer-config-file` parametrem. Jeśli plik Docker Compose zawiera prywatne obrazy, **Dodaj** `--docker-registry-server-*` Parametry, jak pokazano w poprzednim przykładzie.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Aby skonfigurować CI/CD z rejestru kontenerów dla aplikacji, **Uruchom** polecenie [AZ webapp Deployment Container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) z `--enable-cd` parametrem. Polecenie wyświetla adres URL elementu webhook, ale należy ręcznie utworzyć element webhook w rejestrze w osobnym kroku. Poniższy przykład umożliwia włączenie CI/CD w aplikacji, a następnie użycie adresu URL elementu webhook w danych wyjściowych w celu utworzenia elementu webhook w Azure Container Registry.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Więcej zasobów

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](quickstart-dotnetcore.md)
* [Szybki Start: uruchamianie niestandardowego kontenera na App Service](quickstart-custom-container.md)
* [Często zadawane pytania dotyczące usługi App Service w systemie Linux](faq-app-service-linux.md)
* [Konfigurowanie kontenerów niestandardowych](configure-custom-container.md)
* [Przepływy pracy akcji do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)
