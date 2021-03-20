---
title: Samouczek — Tworzenie rejestru z replikacją geograficzną
description: Tworzenie rejestru kontenerów platformy Azure, konfigurowanie replikacji geograficznej, przygotowanie obrazu platformy Docker i wdrożenie go w rejestrze. Pierwsza część trzyczęściowej serii.
ms.topic: tutorial
ms.date: 06/30/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 6abf1b7a524bc7dd28f1704a362749ac84de2389
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97826078"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Samouczek: przygotowywanie rejestru kontenerów platformy Azure z replikacją geograficzną

Rejestr kontenerów platformy Azure to prywatny rejestr platformy Docker wdrożony na platformie Azure, który można zlokalizować w sieci w pobliżu własnych wdrożeń. Ten zestaw trzech samouczków przedstawia metodę użycia replikacji geograficznej do wdrożenia aplikacji internetowej platformy ASP.NET Core uruchomionej w kontenerze systemu Linux do dwóch wystąpień funkcji [Web Apps for Containers](../app-service/index.yml). Dowiesz się, jak platforma Azure automatycznie wdraża obraz do każdego wystąpienia aplikacji internetowej z najbliższego repozytorium z replikacją geograficzną.

Ten samouczek jest pierwszą częścią trzyczęściowej serii:

> [!div class="checklist"]
> * Tworzenie rejestru kontenerów platformy Azure z replikacją geograficzną
> * Klonowanie kodu źródłowego aplikacji z usługi GitHub
> * Tworzenie obrazu kontenera platformy Docker ze źródła aplikacji
> * Wypchnięcie obrazu kontenera do rejestru

W kolejnych samouczkach kontener zostanie wdrożony z prywatnego rejestru do aplikacji internetowej uruchomionej w dwóch regionach platformy Azure. Następnie zostanie zaktualizowany kod w aplikacji, a oba wystąpienia aplikacji internetowej zostaną zaktualizowane za pomocą pojedynczego polecenia `docker push` uruchomionego względem rejestru.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Dla tego samouczka wymagana jest lokalna instalacja interfejsu wiersza polecenia platformy Azure (w wersji 2.0.31 lub nowszej). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

Należy posiadać wiedzę na temat bazowych koncepcji usługi Docker, takich jak kontenery, obrazy kontenerów i podstawowe polecenia interfejsu wiersza polecenia usługi Docker. Aby uzyskać podstawowe informacje na temat kontenerów, zapoznaj się z tematem [Get started with Docker (Rozpoczynanie pracy z platformą Docker)]( https://docs.docker.com/get-started/).

Do ukończenia tego samouczka potrzebna jest lokalna instalacja usługi Docker. Platforma Docker dostarcza instrukcje na temat instalacji w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Usługa Azure Cloud Shell nie zawiera składników platformy Docker wymaganych do ukończenia każdego kroku w tym samouczku. Dlatego zalecamy użycie lokalnej instalacji interfejsu wiersza polecenia platformy Azure i środowiska deweloperskiego platformy Docker.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

W tym samouczku potrzebny jest rejestr kontenerów platformy Azure w warstwie usługi Premium. Aby utworzyć nowy rejestr kontenerów platformy Azure, wykonaj kroki opisane w tej sekcji.

> [!TIP]
> Jeśli wcześniej utworzono rejestr i musisz przeprowadzić uaktualnienie, zobacz [Zmiana warstw](container-registry-skus.md#changing-tiers). 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

Wybierz pozycję **Utwórz**  >  **kontenery** zasobów  >  **Azure Container Registry**.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-01.png" alt-text="Tworzenie rejestru kontenerów w witrynie Azure Portal":::

Skonfiguruj nowy rejestr przy użyciu następujących ustawień. Na karcie **podstawowe** :

* **Nazwa rejestru**: utwórz nazwę rejestru, która jest globalnie unikatowa w obrębie platformy Azure i składa się z od 5 do 50 znaków alfanumerycznych
* **Grupa zasobów**: **Utwórz nową** > `myResourceGroup`
* **Lokalizacja**: `West US`
* **Jednostka SKU**: `Premium` (wymagana na potrzeby replikacji geograficznej)

Wybierz pozycję **Przegląd + Utwórz** , a następnie **Utwórz** , aby utworzyć wystąpienie rejestru.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-02.png" alt-text="Konfigurowanie rejestru kontenerów w Azure Portal":::

W pozostałej części tego samouczka użyjemy `<acrName>` jako symbolu zastępczego dla wybranej **nazwy rejestru** kontenerów.

> [!TIP]
> Ponieważ rejestry kontenerów platformy Azure to zwykle zasoby o długim czasie życia używane na wielu hostach kontenerów, zalecamy utworzenie rejestru w jego własnej grupie zasobów. Podczas konfigurowania rejestrów z replikacją geograficzną i elementów webhook te dodatkowe zasoby są umieszczane w tej samej grupie zasobów.

## <a name="configure-geo-replication"></a>Konfigurowanie replikacji geograficznej

Teraz, gdy masz rejestr w warstwie Premium, możesz skonfigurować replikację geograficzną. Aplikacja internetowa, którą skonfigurujesz w następnym samouczku pod kątem uruchamiania w dwóch regionach, będzie wtedy mogła ściągać obrazy kontenera z najbliższego rejestru.

Przejdź do nowego rejestru kontenerów w Azure Portal a następnie wybierz pozycję **replikacje** w obszarze **usługi**:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-03.png" alt-text="Replikacje w interfejsie użytkownika rejestru kontenerów w witrynie Azure Portal":::

Zostanie wyświetlona mapa z zielonymi sześciokątami reprezentującymi regiony platformy Azure dostępne dla replikacji geograficznej:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-map-01.png" alt-text="Mapa regionów w witrynie Azure Portal":::

Zreplikuj rejestr do regionu Wschodnie stany USA, wybierając jego zielony sześciokąt, a następnie wybierz polecenie **Utwórz** w obszarze **Utwórz replikację**:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-04.png" alt-text="Tworzenie interfejsu użytkownika replikacji w witrynie Azure Portal":::

Po zakończeniu replikacji w portalu będzie wyświetlany stan *Gotowe* dla obu regionów. Użyj przycisku **Odśwież**, aby odświeżyć stan replikacji. Utworzenie i zsynchronizowanie replik może trwać około minuty.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-05.png" alt-text="Interfejs użytkownika stanu replikacji w witrynie Azure Portal":::


## <a name="enable-admin-account"></a>Włącz konto administratora

W kolejnych samouczkach można wdrożyć obraz kontenera z rejestru bezpośrednio do Web App for Containers. Aby włączyć tę funkcję, należy również włączyć [konto administratora](container-registry-authentication.md#admin-account)rejestru.

Przejdź do nowego rejestru kontenerów w Azure Portal i wybierz pozycję **klucze dostępu** w obszarze **Ustawienia**. W obszarze **Administrator** wybierz pozycję **Włącz**.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-06.png" alt-text="Włącz konto administratora w Azure Portal":::


## <a name="container-registry-login"></a>Logowanie do rejestru Container Registry

Po skonfigurowaniu replikacji geograficznej utwórz obraz kontenera i wypchnij go do rejestru. Musisz najpierw zalogować się do rejestru przed wypchnięciem do niego obrazów.

Użyj polecenia [az acr login](/cli/azure/acr#az-acr-login), aby przeprowadzić uwierzytelnianie i zapisać poświadczenia dla rejestru w pamięci podręcznej. Zastąp ciąg `<acrName>` nazwą wcześniej utworzonego rejestru.

```azurecli
az acr login --name <acrName>
```

Polecenie zwraca ciąg `Login Succeeded` po zakończeniu.

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

Przykład podany w tym samouczku obejmuje małą aplikację internetową utworzoną za pomocą platformy [ASP.NET Core][aspnet-core]. Aplikacja udostępnia stronę HTML wyświetlającą region, z którego obraz został wdrożony przez usługę Azure Container Registry.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-app-01.png" alt-text="Samouczek aplikacji wyświetlony w przeglądarce":::

Użyj narzędzia git, aby pobrać przykład do katalogu lokalnego, a następnie użyj polecenia `cd`, aby przejść do tego katalogu:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Jeśli nie masz zainstalowanego narzędzia `git`, możesz [pobrać archiwum ZIP][acr-helloworld-zip] bezpośrednio z usługi GitHub.

## <a name="update-dockerfile"></a>Aktualizacja pliku Dockerfile

Plik Dockerfile dołączony do przykładu przedstawia sposób tworzenia kontenera. Zaczyna się od oficjalnego obrazu środowiska uruchomieniowego ASP.NET Core, kopiuje pliki aplikacji do kontenera, instaluje zależności, kompiluje dane wyjściowe przy użyciu oficjalnego zestaw .NET Core SDK obrazu, a wreszcie kompiluje zoptymalizowany obraz aspnetcore.

Plik [Dockerfile][dockerfile] znajduje się w lokalizacji `./AcrHelloworld/Dockerfile` w sklonowanym źródle.

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

Aplikacja w obrazie *acr-helloworld* próbuje określić region, z którego wdrożono jej kontener, odpytując usługę DNS o informacje dotyczące serwera logowania rejestru. Musisz określić w pełni kwalifikowaną nazwę domeny (FQDN, fully qualified domain name) serwera logowania rejestru za pomocą zmiennej środowiskowej `DOCKER_REGISTRY` w pliku Dockerfile.

Najpierw pobierz serwer logowania rejestru za pomocą polecenia `az acr show`. Zastąp ciąg `<acrName>` nazwą rejestru utworzonego w poprzednich krokach.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Dane wyjściowe:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Następnie zaktualizuj wiersz `ENV DOCKER_REGISTRY` przy użyciu nazwy FQDN serwera logowania rejestru. W tym przykładzie użyto przykładowej nazwy rejestru *uniqueregistryname*:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Tworzenie obrazu kontenera

Po zaktualizowaniu pliku Dockerfile za pomocą nazwy FQDN serwera logowania rejestru możesz użyć polecenia `docker build` do utworzenia obrazu kontenera. Uruchom następujące polecenie, aby utworzyć obraz i oznaczyć go za pomocą adresu URL prywatnego rejestru, ponownie zastępując ciąg `<acrName>` nazwą rejestru:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Podczas tworzenia obrazu platformy Docker jest wyświetlanych kilka wierszy danych wyjściowych (w tym miejscu są one obcięte):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
2.2: Pulling from mcr.microsoft.com/dotnet/core/aspnet
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Użyj polecenia `docker images`, aby wyświetlić kompilację i oznakowany obraz:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Wypychanie obrazu do usługi Azure Container Registry

Następnie użyj polecenia `docker push`, aby wypchnąć obraz *acr-helloworld* do rejestru. Zastąp ciąg `<acrName>` nazwą rejestru.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Ponieważ rejestr skonfigurowano pod kątem replikacji geograficznej, obraz jest automatycznie replikowany do regionów *Zachodnie stany USA* i *Wschodnie stany USA* po użyciu pojedynczego polecenia `docker push`.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka utworzono prywatny, replikowany geograficznie rejestr kontenerów, utworzono obraz kontenera, a następnie wypchnięto obraz do rejestru.

Przejdź do następnego samouczka, aby wdrożyć kontener do wielu wystąpień funkcji Web Apps for Containers za pomocą replikacji geograficznej w celu udostępniania obrazów lokalnie.

> [!div class="nextstepaction"]
> [Deploy web app from Azure Container Registry (Wdrażanie aplikacji internetowej z usługi Azure Container Registry)](container-registry-tutorial-deploy-app.md)

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile
