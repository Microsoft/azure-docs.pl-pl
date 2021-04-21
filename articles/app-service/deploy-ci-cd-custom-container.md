---
title: Ci/CD to custom containers (Ci/CD to custom containers)
description: Skonfiguruj ciągłe wdrażanie w niestandardowym kontenerze systemu Windows lub Linux w Azure App Service.
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 6519f3fe7335ed41f4d5ef67771aaa738a33e4a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782607"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Ciągłe wdrażanie przy użyciu kontenerów niestandardowych w Azure App Service

W tym samouczku skonfigurujesz ciągłe wdrażanie dla niestandardowego obrazu kontenera z zarządzanych [repozytoriów Azure Container Registry](https://azure.microsoft.com/services/container-registry/) lub [Docker Hub](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. Przejdź do Centrum wdrażania

W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania dla swojej App Service aplikacji.

W menu po lewej stronie kliknij pozycję **Ustawienia Centrum**  >  **wdrażania.** 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Wybierz źródło wdrożenia

**Wybór** źródła wdrożenia zależy od scenariusza:
- **Rejestr kontenerów konfiguruje** ci/cd między rejestrem kontenerów a App Service.
- Opcja **GitHub Actions** jest dostępna w przypadku obsługi kodu źródłowego obrazu kontenera w usłudze GitHub. Wyzwalane przez nowe zatwierdzenia do repozytorium GitHub, akcję wdrażania można uruchomić i bezpośrednio w rejestrze kontenerów, a następnie zaktualizować aplikację App Service, aby uruchomić `docker build` `docker push` nowy obraz. Aby uzyskać więcej informacji, zobacz [How CI/CD works with GitHub Actions](#how-cicd-works-with-github-actions)(Jak ci/CD współpracuje z GitHub Actions ).
- Aby skonfigurować usługę CI/CD przy **użyciu Azure Pipelines,** zobacz [Deploy an Azure Web App Container from Azure Pipelines](/azure/devops/pipelines/targets/webapp-on-container-linux)(Wdrażanie kontenera aplikacji internetowej platformy Azure z Azure Pipelines ).

> [!NOTE]
> W przypadku Docker Compose wybierz pozycję **Container Registry**.

Jeśli wybierzesz opcję GitHub Actions  **autoryzuj** i postępuj zgodnie z monitami autoryzacji. Jeśli masz już autoryzację w usłudze GitHub, możesz wdrożyć ją z repozytorium innego użytkownika, klikając pozycję **Zmień konto.**

Po autoryzacji konta platformy Azure w usłudze GitHub **wybierz** pozycję **Organizacja,** **Repozytorium** i **Gałąź** do wdrożenia.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Konfigurowanie ustawień rejestru
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Konfigurowanie ustawień rejestru

Aby wdrożyć aplikację z wieloma kontenerami **(Docker Compose),** wybierz pozycję **Docker Compose** **typ kontenera.**

Jeśli nie widzisz listy rozwijanej **Typ** kontenera,  przewiń  z powrotem do źródła i wybierz **pozycję Container Registry**.
::: zone-end

W **źródle rejestru** **wybierz,** gdzie znajduje się rejestr kontenerów. Jeśli nie jest to pole Azure Container Registry ani Docker Hub, **wybierz pozycję** **Rejestr prywatny.**

::: zone pivot="container-linux"
> [!NOTE]
> Jeśli aplikacja z wieloma kontenerami (Docker Compose) używa więcej niż jednego obrazu prywatnego, upewnij się, że obrazy prywatne znajdują się w tym samym rejestrze prywatnym i są dostępne przy użyciu tych samych poświadczeń użytkownika. Jeśli aplikacja z wieloma kontenerami  używa tylko obrazów publicznych, wybierz pozycję **Docker Hub**, nawet jeśli niektóre obrazy nie znajdują się Docker Hub.
::: zone-end  

Wykonaj następne kroki, wybierając kartę, która odpowiada wybranej opcji.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

Na **liście rozwijanej** Rejestr są wyświetlane rejestry w tej samej subskrypcji co aplikacja. **Wybierz** rejestr, którego potrzebujesz.

> [!NOTE]
> Aby wdrożyć z rejestru w innej **subskrypcji,** zamiast tego wybierz pozycję **Rejestr** prywatny w **źródle** rejestru.

::: zone pivot="container-windows"
**Wybierz** pozycję **Obraz i** **Tag do** wdrożenia. Jeśli chcesz, **wpisz polecenie** uruchamiania w pliku **startowym**. 
::: zone-end
::: zone pivot="container-linux"
Wykonaj następny krok w zależności od **typu kontenera:**
- Aby **Docker Compose**, **wybierz** rejestr obrazów prywatnych. **Kliknij** **pozycję Wybierz plik,** aby przekazać Docker Compose  [pliku](https://docs.docker.com/compose/compose-file/)lub po prostu wklej zawartość pliku Docker Compose do pliku **konfiguracji**.
- W **przypadku pojedynczego kontenera** wybierz **obraz** **i** **tag do** wdrożenia. Jeśli chcesz, **wpisz polecenie** uruchamiania w pliku **startowym**. 
::: zone-end

App Service dołącza ciąg w pliku **startowym** na końcu polecenia [ `docker run` (jako `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) podczas uruchamiania kontenera.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
W **dostępie do repozytorium** **wybierz,** czy obraz do wdrożenia jest publiczny, czy prywatny.
::: zone-end
::: zone pivot="container-linux"
W **dostępie do repozytorium** **wybierz,** czy obraz do wdrożenia jest publiczny, czy prywatny. W przypadku aplikacji Docker Compose z co najmniej jednym obrazem prywatnym wybierz **pozycję** **Prywatny.**
::: zone-end

W przypadku wybrania obrazu prywatnego określ **wartości** Login (username) **(Nazwa** użytkownika) **i Password** (Hasło) konta platformy Docker.

::: zone pivot="container-windows"
**W** polach Full Image Name (Pełna nazwa obrazu) i **Tag (Tag)** należy podać nazwę obrazu i tagu, oddzielając `:` je znakami (na przykład `nginx:latest` ). Jeśli chcesz, **wpisz polecenie** uruchamiania w pliku **startowym**. 
::: zone-end
::: zone pivot="container-linux"
Wykonaj następny krok w zależności od **typu kontenera:**
- Aby **Docker Compose**, **wybierz** rejestr obrazów prywatnych. **Kliknij** **pozycję Wybierz plik,** aby Docker Compose plik  [lub](https://docs.docker.com/compose/compose-file/)po prostu wklej zawartość pliku Docker Compose do pliku **konfiguracji**.
- W **przypadku pojedynczego kontenera** należy podać **nazwę** obrazu i tagu w polach Pełna nazwa obrazu i **Tag**, oddzielając `:` je znakami (na przykład `nginx:latest` ). Jeśli chcesz, **wpisz polecenie** uruchamiania w pliku **startowym**. 
::: zone-end

App Service dołącza ciąg w pliku **startowym** na końcu polecenia [ `docker run` (jako `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) podczas uruchamiania kontenera.

# <a name="private-registry"></a>[Rejestr prywatny](#tab/private)

W **polu Adres URL** serwera **wpisz** adres URL serwera, zaczynając od **https://**.

W **ustawieniach** **Logowanie** **i Hasło** wpisz swoje poświadczenia logowania dla rejestru prywatnego.

::: zone pivot="container-windows"
**W** polach Full Image Name (Pełna nazwa obrazu) i **Tag (Tag)** należy podać nazwę obrazu i tagu, oddzielając `:` je znakami (na przykład `nginx:latest` ). Jeśli chcesz, **wpisz polecenie** uruchamiania w pliku **startowym**. 
::: zone-end
::: zone pivot="container-linux"
Wykonaj następny krok w zależności od **typu kontenera:**
- Aby **Docker Compose**, **wybierz** rejestr obrazów prywatnych. **Kliknij** **pozycję Wybierz plik,** aby Docker Compose plik  [lub](https://docs.docker.com/compose/compose-file/)po prostu wklej zawartość pliku Docker Compose do pliku **konfiguracji**.
- Dla **pola Single Container** **(Pojedynczy kontener)** należy podać nazwę obrazu i tagu w polach Full Image Name (Pełna nazwa obrazu) i **Tag (tag),** oddzielając je `:` znakami (na przykład `nginx:latest` ). Jeśli chcesz, **wpisz polecenie** uruchamiania w pliku **startowym**. 
::: zone-end

App Service dołącza ciąg w pliku **startowym** na końcu polecenia [ `docker run` (jako `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) podczas uruchamiania kontenera.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. Włączanie ciasnych/cd
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. Włączanie funkcji CI/CD
::: zone-end

App Service obsługuje integrację ci/CD z Azure Container Registry i Docker Hub. Aby ją włączyć, wybierz pozycję **Wł. w**  **ciągłego wdrażania.**

::: zone pivot="container-linux"
> [!NOTE]
> Jeśli **wybierzesz** GitHub Actions **w** źródle , nie otrzymasz tej opcji, ponieważ ci/cd są obsługiwane GitHub Actions bezpośrednio. Zamiast tego zobaczysz sekcję **Konfiguracja przepływu** pracy, w której możesz kliknąć **pozycję** **Podgląd pliku,** aby sprawdzić plik przepływu pracy. Platforma Azure zatwierdza ten plik w wybranym repozytorium źródłowym GitHub w celu obsługi zadań kompilacji i wdrażania. Aby uzyskać więcej informacji, zobacz [Jak ci/CD współpracuje z GitHub Actions](#how-cicd-works-with-github-actions).
::: zone-end

Po włączeniu tej opcji program App Service webhook do repozytorium w Azure Container Registry lub Docker Hub. Repozytorium publikuje w tym elementie webhook za każdym razem, gdy wybrany obraz jest aktualizowany za pomocą . `docker push` Ten webhook powoduje ponowne uruchomienie App Service aplikacji internetowej i uruchomienie jej w `docker pull` celu uzyskania zaktualizowanego obrazu.

**W przypadku innych rejestrów** prywatnych można publikować w tym celu ręcznie lub jako krok w potoku ci/CD. W **polu Adres URL linku webhook** **kliknij** przycisk **Kopiuj,** aby uzyskać adres URL tego linku.

::: zone pivot="container-linux"
> [!NOTE]
> Obsługa aplikacji wielo kontenerów (Docker Compose) jest ograniczona: 
> - Na Azure Container Registry App Service element webhook w wybranym rejestrze z rejestrem jako zakresem. Element w dowolnym repozytorium w rejestrze (w tym repozytorium, do których nie odwołuje się plik Docker Compose) wyzwala `docker push` ponowne uruchomienie aplikacji. Możesz zmodyfikować [ten webhook](../container-registry/container-registry-webhook.md) do węższego zakresu.
> - Docker Hub nie obsługuje webhook na poziomie rejestru. Należy ręcznie **dodać** webhook do obrazów określonych w pliku Docker Compose webhook.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Zapisywanie ustawień
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Zapisywanie ustawień
::: zone-end

**Kliknij pozycję** **Zapisz.**

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Jak ci/cd współpracuje z GitHub Actions

Jeśli wybierzesz opcję GitHub Actions **w** źródle (zobacz Wybieranie źródła [wdrożenia),](#2-choose-deployment-source)App Service ci/CD konfiguruje się w następujący sposób: 

- Deponuje plik GitHub Actions przepływu pracy w repozytorium GitHub w celu obsługi zadań kompilacji i wdrażania w App Service.
- Dodaje poświadczenia dla rejestru prywatnego jako wpisy tajne usługi GitHub. Wygenerowany plik przepływu pracy uruchamia [akcję Azure/docker-login,](https://github.com/Azure/docker-login) aby zalogować się do rejestru prywatnego, a następnie jest uruchamiany w celu `docker push` wdrożenia w nim.
- Dodaje profil publikowania dla aplikacji jako klucz tajny usługi GitHub. Wygenerowany plik przepływu pracy używa tego tajnych danych do uwierzytelniania w usłudze App Service, a następnie uruchamia akcję [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) w celu skonfigurowania zaktualizowanego obrazu, co wyzwala ponowne uruchomienie aplikacji w celu ściągnięcia zaktualizowanego obrazu.
- Przechwytuje informacje z [dzienników przebiegów przepływu](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) pracy i wyświetla je na karcie **Dzienniki** w Centrum **wdrażania aplikacji.**

Dostawcę kompilacji GitHub Actions można dostosować w następujący sposób:

- Dostosuj plik przepływu pracy po jego wygenerowaniu w repozytorium GitHub. Aby uzyskać więcej informacji, zobacz [Składnia przepływu pracy dla GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Upewnij się, że przepływ pracy kończy się akcją [Azure/webapps-deploy,](https://github.com/Azure/webapps-deploy) aby wyzwolić ponowne uruchomienie aplikacji.
- Jeśli wybrana gałąź jest chroniona, nadal możesz wyświetlić podgląd pliku przepływu pracy bez zapisywania konfiguracji, a następnie ręcznie dodać go i wymagane wpisy tajne usługi GitHub do repozytorium. Ta metoda nie umożliwia integracji dziennika z Azure Portal.
- Zamiast profilu publikowania należy wdrożyć program przy użyciu [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) w Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Uwierzytelnianie za pomocą jednostki usługi

Ta opcjonalna konfiguracja zastępuje domyślne uwierzytelnianie profilami publikowania w wygenerowanym pliku przepływu pracy.

**Wygeneruj** jednostkę usługi za pomocą polecenia [az ad sp create-for-rbac w](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) interfejsie wiersza polecenia platformy [Azure.](/cli/azure/) W poniższym przykładzie zastąp *\<subscription-id>* wartości *\<group-name>* , i *\<app-name>* własnymi wartościami. **Zapisz** wszystkie dane wyjściowe JSON do następnego kroku, w tym plik najwyższego poziomu `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Ze względów bezpieczeństwa przyznaj minimalny wymagany dostęp do jednostki usługi. Zakres w poprzednim przykładzie jest ograniczony do określonej App Service, a nie całej grupy zasobów.

W [usłudze GitHub](https://github.com/) **przejdź do** repozytorium, a następnie wybierz pozycję > wpisy tajne > Dodaj nowy wpis  **tajny.** **Wklej** całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości we kluczu tajnym. **Nadaj** kluczowi tajnego nazwę, na przykład `AZURE_CREDENTIALS` .

W pliku przepływu pracy wygenerowanym  przez **Centrum wdrażania** popraw krok przy użyciu `azure/webapps-deploy` kodu podobnego do poniższego przykładu:

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

## <a name="automate-with-cli"></a>Automatyzacja przy użyciu interfejsu wiersza polecenia

Aby skonfigurować rejestr kontenerów i obraz platformy Docker, **uruchom polecenie** [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set).

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
Aby skonfigurować aplikację z wieloma kontenerami (Docker Compose), przygotuj lokalnie plik  Docker Compose, a następnie uruchom polecenie [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) z  `--multicontainer-config-file` parametrem . Jeśli plik Docker Compose zawiera obrazy prywatne, **dodaj** `--docker-registry-server-*` parametry, jak pokazano w poprzednim przykładzie.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Aby skonfigurować konfigurację ci/CD z rejestru kontenerów do **aplikacji,** uruchom [polecenie az webapp deployment container config z](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config) `--enable-cd` parametrem . Polecenie wyprowadza adres URL webhook, ale należy ręcznie utworzyć ten webhook w rejestrze w osobnym kroku. Poniższy przykład włącza w aplikacji usługę CI/CD, a następnie używa adresu URL webhook w danych wyjściowych do utworzenia tego Azure Container Registry.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Więcej zasobów

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](quickstart-dotnetcore.md)
* [Szybki start: uruchamianie niestandardowego kontenera na platformie App Service](quickstart-custom-container.md)
* [Często zadawane pytania dotyczące usługi App Service w systemie Linux](faq-app-service-linux.md)
* [Konfigurowanie kontenerów niestandardowych](configure-custom-container.md)
* [Przepływy pracy akcji do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)
