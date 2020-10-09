---
title: Samouczek — wypychanie aktualizacji do rejestru z replikacją geograficzną
description: Wypchnij zaktualizowany obraz Docker do rejestru kontenerów platformy Azure z replikacją geograficzną, a następnie zobacz zmiany automatycznie wdrożone w aplikacjach sieci Web działających w wielu regionach. Trzecia część trzyczęściowej serii.
ms.topic: tutorial
ms.date: 04/30/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 9222ac31e067cba6a0ffa71143c90f906ba6ff7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74454688"
---
# <a name="tutorial-push-an-updated-container-image-to-a-geo-replicated-container-registry-for-regional-web-app-deployments"></a>Samouczek: wypychanie zaktualizowanego obrazu kontenera do rejestru kontenerów z replikacją geograficzną na potrzeby wdrożeń regionalnych aplikacji sieci Web

Niniejszy samouczek jest trzecią częścią trzyczęściowej serii. W [poprzednim samouczku](container-registry-tutorial-deploy-app.md) skonfigurowano replikację geograficzną dla dwóch różnych regionalnych wdrożeń aplikacji internetowej. W tym samouczku najpierw zmodyfikujesz aplikację, a następnie utworzysz nowy obraz kontenera i wypchniesz go do rejestru replikowanego geograficznie. Na koniec wyświetlisz zmianę, wdrożoną automatycznie przez elementy webhook usługi Azure Container Registry w obu wystąpieniach aplikacji internetowej.

Ten samouczek, będący ostatnią częścią serii, obejmuje:

> [!div class="checklist"]
> * Modyfikowanie kodu HTML aplikacji internetowej
> * Budowanie i tagowanie obrazu platformy Docker
> * Wypychanie zmiany do usługi Azure Container Registry
> * Wyświetlanie zaktualizowanej aplikacji w dwóch różnych regionach

Jeśli jeszcze nie skonfigurowano dwóch regionalnych wdrożeń usługi *Web App for Containers*, wróć do poprzedniego samouczka z tej serii — [Wdrażanie aplikacji internetowej z usługi Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Modyfikowanie aplikacji internetowej

W tym kroku należy wprowadzić w aplikacji internetowej zmianę, która będzie dobrze widoczna po wypchnięciu zaktualizowanego obrazu kontenera do usługi Azure Container Registry.

Znajdź plik `AcrHelloworld/Views/Home/Index.cshtml` w źródle aplikacji [sklonowanym z usługi GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) w poprzednim samouczku i otwórz go w edytorze tekstu. Dodaj następujący wiersz poniżej istniejącego wiersza `<h1>`:

```html
<h1>MODIFIED</h1>
```

Zmodyfikowany plik `Index.cshtml` powinien wyglądać podobnie do poniższego:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Ponowne tworzenie obrazu

Teraz, po zaktualizowaniu aplikacji internetowej, należy ponownie utworzyć obraz jej kontenera. Tak jak wcześniej, użyj w pełni kwalifikowanej nazwy obrazu zawierającej w pełni kwalifikowaną nazwę domeny (FQDN) serwera logowania dla tagu:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Wypychanie obrazu do usługi Azure Container Registry

Następnie wypchnij zaktualizowany kontener obrazu *acr-helloworld* do rejestru replikowanego geograficznie. W tym miejscu wykonujesz jedno polecenie `docker push`, aby wdrożyć zaktualizowany obraz w replikach rejestru w regionach *Zachodnie stany USA* i *Wschodnie stany USA*.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Dane wyjściowe `docker push` będą podobne do następujących:

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Wyświetlanie dzienników elementów webhook

Gdy obraz jest replikowany, możesz zobaczyć wyzwalane elementy webhook usługi Azure Container Registry.

Aby wyświetlić regionalne elementy webhook utworzone podczas wdrażania kontenera w usłudze *Web App for Containers* w poprzednim samouczku, przejdź do usługi Container Registry w witrynie Azure Portal, a następnie wybierz pozycję **Elementy webhook** w obszarze **USŁUGI**.

![Elementy webhook usługi Container Registry w witrynie Azure Portal][tutorial-portal-01]

Wybierz każdy element webhook, aby wyświetlić historię jego wywołań i odpowiedzi. W dziennikach obu elementów webhook powinien być wyświetlony wiersz akcji **push** (wypychanie). Tutaj dziennik dla elementu webhook w regionie *Zachodnie stany USA* wyświetla akcję **push** wyzwoloną przez polecenie `docker push` w poprzednim kroku:

![Dziennik elementów webhook usługi Container Registry w witrynie Azure Portal (Zachodnie stany USA)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Wyświetlanie zaktualizowanej aplikacji internetowej

Elementy webhook powiadamiają usługę Web Apps o wypchnięciu nowego obrazu do rejestru, co automatycznie powoduje wdrożenie zaktualizowanego kontenera w dwóch regionalnych aplikacjach internetowych.

Sprawdź, czy aplikacja została zaktualizowana w obu wdrożeniach, przechodząc do obu regionalnych wdrożeń aplikacji internetowej w przeglądarce internetowej. Przypominamy, że adres URL wdrożonej aplikacji internetowej można znaleźć w prawym górnym rogu każdej karty Przegląd usługi App Service.

![Przegląd usługi App Service w witrynie Azure Portal][tutorial-portal-03]

Aby wyświetlić zaktualizowaną aplikację, wybierz link w obszarze Przegląd usługi App Service. Oto przykładowy widok aplikacji uruchomionej w regionie *Zachodnie stany USA*:

![Wyświetlany w przeglądarce widok zmodyfikowanej aplikacji internetowej uruchomionej w regionie Zachodnie stany USA][deployed-app-westus-modified]

Sprawdź, czy zaktualizowany obraz kontenera został również wdrożony we wdrożeniu dla regionu *Wschodnie stany USA*, wyświetlając go w przeglądarce.

![Wyświetlany w przeglądarce widok zmodyfikowanej aplikacji internetowej uruchomionej w regionie Wschodnie stany USA][deployed-app-eastus-modified]

Za pomocą jednej aplikacji `docker push` została automatycznie zaktualizowana aplikacja internetowa działająca w obu regionalnych wdrożeniach aplikacji internetowej. Usługa Azure Container Registry przesłała do kontenera obrazy z repozytoriów znajdujących się najbliżej poszczególnych wdrożeń.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z samouczkiem utworzono i wypchnięto nową wersję kontenera aplikacji internetowej do rejestru replikowanego geograficznie. Elementy webhook w usłudze Azure Container Registry powiadomiły o aktualizacji usługę Web Apps for Containers, która wyzwoliła lokalne ściągnięcie z najbliższej repliki rejestru.

### <a name="acr-build-automated-image-build-and-patch"></a>Kompilacja ACR: zautomatyzowana kompilacja obrazu i poprawka

Oprócz replikacji geograficznej kompilacja ACR jest kolejną funkcją usługi Azure Container Registry, która może ułatwić optymalizację potoku wdrożenia kontenera. Rozpocznij od przeglądu kompilacji ACR, aby poznać jej możliwości:

[Automatyzacja systemu operacyjnego i poprawianie struktury przy użyciu kompilacji ACR](container-registry-tasks-overview.md)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png