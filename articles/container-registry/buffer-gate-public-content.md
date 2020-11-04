---
title: Zarządzanie zawartością publiczną w rejestrze kontenera prywatnego
description: Praktyki i przepływy pracy w Azure Container Registry do zarządzania zależnościami na publicznych obrazach z usługi Docker Hub i innej zawartości publicznej
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 10/29/2020
ms.openlocfilehash: def1c3a9b8a1086f453c7e71d766ab0dd89b0c2d
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347526"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Zarządzanie zawartością publiczną za pomocą Azure Container Registry

Ten artykuł zawiera omówienie praktyk i przepływów pracy służących do korzystania z rejestru lokalnego, takiego jak [usługa Azure Container Registry](container-registry-intro.md) , aby zachować kopie publicznej zawartości, takie jak obrazy kontenerów w usłudze Docker Hub. 


## <a name="risks-with-public-content"></a>Ryzyko związane z zawartością publiczną

Środowisko może być zależne od zawartości publicznej, takiej jak obrazy kontenerów publicznych, [wykresy Helme](https://helm.sh/), zasady [Open Policy Agent](https://www.openpolicyagent.org/) (nieprzez) lub inne artefakty. Na przykład można uruchomić [Nginx](https://hub.docker.com/_/nginx) do routingu usług lub `docker build FROM alpine` przez ściąganie obrazów bezpośrednio z usługi Docker Hub lub innego rejestru publicznego. 

Bez odpowiednich kontroli, posiadanie zależności od zawartości rejestru publicznego może stanowić zagrożenie dla przepływów pracy tworzenia obrazów i wdrażania. W celu ograniczenia ryzyka należy zachować lokalne kopie publicznej zawartości, jeśli jest to możliwe. Aby uzyskać szczegółowe informacje, zobacz Blog dotyczący [otwartej inicjatywy](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Uwierzytelnianie za pomocą narzędzia Docker Hub

Pierwszy krok, jeśli obecnie pobierasz Obrazy publiczne z usługi Docker Hub w ramach przepływu pracy kompilowania lub wdrażania, zalecamy [uwierzytelnienie przy użyciu konta usługi Docker Hub](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) zamiast anonimowego żądania ściągnięcia.

> [!NOTE]
> Od 2 listopada 2020 [limity szybkości pobierania](https://docs.docker.com/docker-hub/download-rate-limit) dotyczą żądań anonimowych i uwierzytelnionych do usługi Docker Hub z kont planów bezpłatnych platformy Docker i są wymuszane odpowiednio przy użyciu adresu IP i identyfikatora platformy Docker. 
>
> Podczas szacowania liczby żądań ściągnięcia należy wziąć pod uwagę, że w przypadku korzystania z usług dostawcy w chmurze lub pracy za firmową translacją adresów sieciowych do usługi Docker Hub w agregacji jako podzbiór adresów IP zostanie wyświetlonych wiele użytkowników. Dodanie funkcji uwierzytelniania płatnego konta usługi Docker do żądań wysyłanych do centrum platformy Docker spowoduje uniknięcie potencjalnych przerw w świadczeniu usług z powodu ograniczenia szybkości ograniczania.
>
> Aby uzyskać szczegółowe informacje, zobacz [Cennik platformy Docker i subskrypcje](https://www.docker.com/pricing) oraz [warunki użytkowania usługi Docker](https://www.docker.com/legal/docker-terms-service).

### <a name="docker-hub-access-token"></a>Token dostępu do centrum platformy Docker

Centrum platformy Docker obsługuje [osobiste tokeny dostępu](https://docs.docker.com/docker-hub/access-tokens/) jako alternatywy dla hasła Docker podczas uwierzytelniania w usłudze Docker Hub. Tokeny są zalecane dla zautomatyzowanych usług, które pobierają obrazy z usługi Docker Hub. Można wygenerować wiele tokenów dla różnych użytkowników lub usług i odwołać tokeny, gdy nie są już potrzebne.

Aby uwierzytelnić się przy `docker login` użyciu tokenu, Pomiń hasło w wierszu polecenia. Po wyświetleniu monitu o podanie hasła wprowadź zamiast niego token. Jeśli włączono uwierzytelnianie dwuskładnikowe dla konta usługi Docker Hub, podczas logowania się z interfejsu wiersza polecenia platformy Docker należy używać osobistego tokenu dostępu.

### <a name="authenticate-from-azure-services"></a>Uwierzytelnianie za pomocą usług platformy Azure

Kilka usług platformy Azure, w tym App Service i Azure Container Instances obsługują ściąganie obrazów z rejestrów publicznych, takich jak usługa Docker Hub na potrzeby wdrożeń kontenerów. Jeśli zachodzi potrzeba wdrożenia obrazu z usługi Docker Hub, zalecamy skonfigurowanie ustawień do uwierzytelniania przy użyciu konta usługi Docker Hub. Przykłady:

**App Service**

* **Źródło obrazu** : centrum platformy Docker
* **Dostęp do repozytorium** : prywatny
* **Logowanie** : \<Docker Hub username>
* **Hasło** : \<Docker Hub token>

Aby uzyskać szczegółowe informacje, zobacz [uwierzytelnione ściągania usługi Docker Hub na App Service](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Azure Container Instances**

* **Źródło obrazu** : centrum platformy Docker lub inny rejestr
* **Typ obrazu** : prywatny
* **Serwer logowania rejestru obrazów** : Docker.IO
* **Nazwa użytkownika rejestru obrazu** : \<Docker Hub username>
* **Hasło rejestru obrazu** : \<Docker Hub token>
* **Obraz** : Docker.IO/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Importowanie obrazów do usługi Azure Container Registry
 
Aby rozpocząć zarządzanie kopiami obrazów publicznych, możesz utworzyć rejestr kontenerów platformy Azure, jeśli go jeszcze nie masz. Tworzenie rejestru przy użyciu [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)lub innych narzędzi. 

Jako zalecany krok jednorazowy należy [zaimportować](container-registry-import-images.md) obrazy podstawowe i inną zawartość publiczną do usługi Azure Container Registry. Polecenie [AZ ACR import](/cli/azure/acr#az_acr_import) w interfejsie wiersza polecenia platformy Azure obsługuje importowanie obrazów z publicznych rejestrów, takich jak Docker Hub i Microsoft Container Registry i innych prywatnych rejestrów kontenerów. 

`az acr import` nie wymaga lokalnej instalacji platformy Docker. Można uruchomić ją z lokalną instalacją interfejsu wiersza polecenia platformy Azure lub bezpośrednio w Azure Cloud Shell. Obsługuje obrazy dowolnego typu systemu operacyjnego, obrazów z wieloarchitekturą lub artefaktów OCI, takich jak wykresy Helm.

Przykład:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

W zależności od potrzeb organizacji można importować do dedykowanego rejestru lub repozytorium w rejestrze udostępnionym.

## <a name="automate-application-image-updates"></a>Automatyzowanie aktualizacji obrazu aplikacji

Deweloperzy obrazów aplikacji powinni upewnić się, że ich kod odwołuje się do zawartości lokalnej pod kontrolą. Na przykład `Docker FROM` instrukcja w pliku dockerfile powinna odwoływać się do obrazu w prywatnym rejestrze obrazu podstawowego zamiast do rejestru publicznego. 

Rozszerzanie podczas importowania obrazu, skonfiguruj [zadanie Azure Container Registry](container-registry-tasks-overview.md) , aby zautomatyzować kompilacje obrazu aplikacji po zaktualizowaniu obrazów podstawowych. Zadanie zautomatyzowanej kompilacji może śledzić [aktualizacje obrazu podstawowego](container-registry-tasks-base-images.md) i [aktualizacje kodu źródłowego](container-registry-tasks-overview.md#trigger-task-on-source-code-update).

Aby zapoznać się z szczegółowym przykładem, zobacz [jak używać i konserwowania zawartości publicznej za pomocą zadań Azure Container Registry](tasks-consume-public-content.md). 

> [!NOTE]
> Jedno wstępnie skonfigurowane zadanie może automatycznie odbudować każdy obraz aplikacji, który odwołuje się do zależnego obrazu podstawowego. 
 
## <a name="next-steps"></a>Następne kroki
 
* Dowiedz się więcej o [zadaniach ACR](container-registry-tasks-overview.md) do kompilowania, uruchamiania, wypychania i poprawiania obrazów kontenerów na platformie Azure.
* Zapoznaj się z tematem [jak używać i konserwować zawartość publiczną z Azure Container Registry zadaniami](tasks-consume-public-content.md) dla zautomatyzowanego przepływu pracy kontroli, aby aktualizować obrazy podstawowe do środowiska. 
* Zapoznaj się z [samouczkami dotyczącymi zadań ACR](container-registry-tutorial-quick-task.md) , aby uzyskać więcej przykładów automatyzowania kompilacji i aktualizacji obrazów.
