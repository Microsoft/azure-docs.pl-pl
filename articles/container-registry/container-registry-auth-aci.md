---
title: Dostęp z Container Instances
description: Dowiedz się, jak zapewnić dostęp do obrazów w rejestrze kontenerów prywatnych z Azure Container Instances przy użyciu jednostki usługi Azure Active Directory.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74456506"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Uwierzytelnianie za pomocą Azure Container Registry z Azure Container Instances

Za pomocą nazwy głównej usługi Azure Active Directory (Azure AD) można zapewnić dostęp do prywatnych rejestrów kontenerów w Azure Container Registry.

Ten artykuł zawiera informacje na temat tworzenia i konfigurowania jednostki usługi Azure AD z uprawnieniami *ściągania* do rejestru. Następnie należy uruchomić kontener w Azure Container Instances (ACI), który pobiera obraz z prywatnego rejestru przy użyciu nazwy głównej usługi do uwierzytelniania.

## <a name="when-to-use-a-service-principal"></a>Kiedy używać nazwy głównej usługi

Należy używać nazwy głównej usługi do uwierzytelniania z ACI w **scenariuszach bezobsługowych**, takich jak aplikacje lub usługi, które tworzą wystąpienia kontenerów w sposób zautomatyzowany lub w inny sposób nienadzorowany.

Na przykład jeśli masz zautomatyzowany skrypt, który działa nocnie i tworzy [wystąpienie kontenera opartego na zadaniach](../container-instances/container-instances-restart-policy.md) , aby przetworzyć niektóre dane, można użyć jednostki usługi z uprawnieniami tylko do ściągania w celu uwierzytelnienia w rejestrze. Następnie można obrócić poświadczenia jednostki usługi lub całkowicie odwołać swój dostęp bez wpływu na inne usługi i aplikacje.

Nazwy główne usług należy również stosować, gdy [użytkownik administrator](container-registry-authentication.md#admin-account) rejestru jest wyłączony.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Uwierzytelnianie przy użyciu nazwy głównej usługi

Aby uruchomić kontener w Azure Container Instances przy użyciu nazwy głównej usługi, określ jej identyfikator `--registry-username` i hasło dla `--registry-password` .

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Przykładowe skrypty

Powyższe przykładowe skrypty dla interfejsu wiersza polecenia platformy Azure można znaleźć w witrynie GitHub, jak również wersje Azure PowerShell:

* [Interfejs wiersza polecenia platformy Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Następne kroki

Poniższe artykuły zawierają dodatkowe informacje na temat pracy z jednostkami usługi i ACR:

* [Uwierzytelnianie Azure Container Registry przy użyciu jednostek usługi](container-registry-auth-service-principal.md)
* [Uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
