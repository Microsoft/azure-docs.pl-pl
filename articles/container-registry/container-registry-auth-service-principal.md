---
title: Uwierzytelnianie za pomocą jednostki usługi
description: Zapewnianie dostępu do obrazów w prywatnym rejestrze kontenera przy użyciu nazwy głównej usługi Azure Active Directory.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: a32538e5fc5354427bafc5098634becdcedd1239
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285539"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Uwierzytelnianie Azure Container Registry przy użyciu jednostek usługi

Możesz użyć jednostki usługi Azure Active Directory (Azure AD), aby zapewnić wypychanie, ściąganie lub inny dostęp do rejestru kontenerów. Za pomocą nazwy głównej usługi można zapewnić dostęp do "bezobsługowego" usług i aplikacji.

## <a name="what-is-a-service-principal"></a>Co to jest jednostka usługi?

Jednostki *usługi* Azure AD zapewniają dostęp do zasobów platformy Azure w ramach Twojej subskrypcji. Nazwę główną usługi można traktować jako tożsamość użytkownika usługi, gdzie "usługa" to dowolna aplikacja, usługa lub platforma, która musi uzyskać dostęp do zasobów. Można skonfigurować jednostkę usługi z prawami dostępu w zakresie tylko do określonych zasobów. Następnie skonfiguruj aplikację lub usługę tak, aby korzystała z poświadczeń jednostki usługi w celu uzyskania dostępu do tych zasobów.

W kontekście Azure Container Registry można utworzyć jednostkę usługi Azure AD za pomocą ściągania, wypychania i ściągania lub innych uprawnień do rejestru prywatnego na platformie Azure. Aby uzyskać pełną listę, zobacz [Azure Container Registry ról i uprawnień](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Dlaczego warto używać nazwy głównej usługi?

Za pomocą nazwy głównej usługi Azure AD można zapewnić dostęp z zakresu do prywatnego rejestru kontenerów. Utwórz różne jednostki usługi dla każdej aplikacji lub usług, z których każdy może mieć dostosowane prawa dostępu do rejestru. Ponadto, ponieważ możesz uniknąć udostępniania poświadczeń między usługami i aplikacjami, możesz obrócić poświadczenia lub odwołać dostęp tylko do nazwy głównej usługi (i w związku z tym aplikacji).

Na przykład skonfiguruj aplikację sieci Web tak, aby korzystała z jednostki usługi, która udostępnia ją `pull` tylko z dostępem do obrazu, podczas gdy system kompilacji używa jednostki usługi, która udostępnia ją jednocześnie `push` i `pull` dostęp. Jeśli rozwój aplikacji zmienia się, możesz obrócić jej poświadczenia głównej usługi bez wpływu na system kompilacji.

## <a name="when-to-use-a-service-principal"></a>Kiedy używać nazwy głównej usługi

Aby zapewnić dostęp do rejestru w **scenariuszach bezobsługowym**, należy użyć nazwy głównej usługi. Oznacza to, że dowolna aplikacja, usługa lub skrypt, który musi wypchnąć lub ściągnąć obrazy kontenerów w sposób zautomatyzowany lub w inny sposób nienadzorowany. Na przykład:

  * *Ściąganie*: wdrażanie kontenerów z rejestru w systemach aranżacji, takich jak KUBERNETES, DC/OS i Docker Swarm. Możesz również ściągnąć z rejestrów kontenerów do powiązanych usług platformy Azure, takich jak [Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml)i innych.

  * *Wypychanie*: Tworzenie obrazów kontenerów i wypychanie ich do rejestru przy użyciu rozwiązań ciągłej integracji i wdrażania, takich jak Azure Pipelines lub Jenkins.

W przypadku indywidualnego dostępu do rejestru, takiego jak ręczne ściąganie obrazu kontenera do stacji roboczej deweloperskiej, zalecamy użycie własnej [tożsamości usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) zamiast dostępu do rejestru (na przykład za pomocą polecenia [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Przykładowe skrypty

Powyższe przykładowe skrypty dla interfejsu wiersza polecenia platformy Azure można znaleźć w witrynie GitHub, a także wersje dla Azure PowerShell:

* [Interfejs wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Uwierzytelnianie za pomocą nazwy głównej usługi

Gdy masz nazwę główną usługi, której udzielono dostępu do rejestru kontenerów, możesz skonfigurować jej poświadczenia w celu uzyskania dostępu do "bezobsługowego" usług i aplikacji albo wprowadzić je za pomocą `docker login` polecenia. Użyj następujących wartości:

* **Nazwa użytkownika** — identyfikator aplikacji głównej usługi (NAZYWANY także *identyfikatorem klienta*)
* **Hasło** — hasło główne usługi (nazywane także *kluczem tajnym klienta*)

Każda wartość ma format `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . 

> [!TIP]
> Możesz ponownie wygenerować hasło jednostki usługi, uruchamiając polecenie [AZ AD Sp Reset-Credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Używanie poświadczeń z usługami platformy Azure

Poświadczeń jednostki usługi można użyć z dowolnej usługi platformy Azure, która jest uwierzytelniana w usłudze Azure Container Registry.  Użyj poświadczeń nazwy głównej usługi zamiast poświadczeń administratora rejestru dla różnych scenariuszy.

Na przykład Użyj poświadczeń w celu ściągnięcia obrazu z rejestru kontenerów platformy Azure w celu [Azure Container Instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Użyj z logowaniem Docker

Można uruchomić `docker login` za pomocą nazwy głównej usługi. W poniższym przykładzie identyfikator aplikacji głównej usługi jest przekazaniem w zmiennej środowiskowej `$SP_APP_ID` i hasłem w zmiennej `$SP_PASSWD` . Zalecane praktyki dotyczące zarządzania poświadczeniami platformy Docker można znaleźć w dokumentacji polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Po zalogowaniu się program Docker buforuje poświadczenia.

### <a name="use-with-certificate"></a>Użyj z certyfikatem

Jeśli dodano certyfikat do nazwy głównej usługi, możesz zalogować się do interfejsu wiersza polecenia platformy Azure przy użyciu uwierzytelniania opartego na certyfikatach, a następnie użyć [AZ ACR login][az-acr-login] , aby uzyskać dostęp do rejestru. Użycie certyfikatu jako wpisu tajnego zamiast hasła zapewnia dodatkowe zabezpieczenia podczas korzystania z interfejsu wiersza polecenia. 

Certyfikat z podpisem własnym można utworzyć podczas [tworzenia nazwy głównej usługi](/cli/azure/create-an-azure-service-principal-azure-cli). Lub Dodaj co najmniej jeden certyfikat do istniejącej nazwy głównej usługi. Jeśli na przykład używasz jednego ze skryptów w tym artykule, aby utworzyć lub zaktualizować jednostkę usługi z uprawnieniami do ściągania lub wypychania obrazów z rejestru, Dodaj certyfikat za pomocą polecenia [AZ AD Sp Credential Reset][az-ad-sp-credential-reset] .

Aby można było [zalogować się do interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)przy użyciu nazwy głównej usługi, certyfikat musi być w formacie PEM i zawierać klucz prywatny. Jeśli certyfikat nie jest w wymaganym formacie, użyj narzędzia takiego jak `openssl` konwertowanie. Po uruchomieniu polecenia [AZ login][az-login] , aby zalogować się w interfejsie wiersza polecenia przy użyciu nazwy głównej usługi, podaj również identyfikator aplikacji jednostki usługi i identyfikator dzierżawy Active Directory. Poniższy przykład przedstawia te wartości jako zmienne środowiskowe:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Następnie uruchom polecenie [AZ ACR login][az-acr-login] , aby uwierzytelnić się w rejestrze:

```azurecli
az acr login --name myregistry
```

Interfejs wiersza polecenia używa tokenu utworzonego podczas przeprowadzania `az login` uwierzytelniania sesji z rejestrem.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [omówieniem uwierzytelniania](container-registry-authentication.md) , aby poznać inne scenariusze uwierzytelniania przy użyciu usługi Azure Container Registry.

* Przykład użycia magazynu kluczy Azure do przechowywania i pobierania poświadczeń jednostki usługi dla rejestru kontenerów można znaleźć w samouczku, aby [skompilować i wdrożyć obraz kontenera za pomocą zadań ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
