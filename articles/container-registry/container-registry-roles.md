---
title: Role i uprawnienia platformy Azure
description: Użyj kontroli dostępu opartej na rolach (Azure RBAC) i zarządzania tożsamościami i dostępem (IAM), aby zapewnić szczegółowe uprawnienia do zasobów w usłudze Azure Container Registry.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: b8562d3e33cd49082d4ba4d8567d5f0c816070b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661388"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry ról i uprawnień

Usługa Azure Container Registry obsługuje zestaw [wbudowanych ról platformy Azure](../role-based-access-control/built-in-roles.md) , które zapewniają różne poziomy uprawnień do usługi Azure Container Registry. Za pomocą [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/index.yml) można przypisywać określone uprawnienia użytkownikom, podmiotom usług lub innym tożsamościom, które muszą korzystać z rejestru. Można także definiować [role niestandardowe](#custom-roles) z szczegółowymi uprawnieniami do rejestru dla różnych operacji.

| Rola/uprawnienie       | [Menedżer zasobów dostępu](#access-resource-manager) | [Utwórz/usuń rejestr](#create-and-delete-registry) | [Obraz wypychany](#push-image) | [Obraz ściągania](#pull-image) | [Usuń dane obrazu](#delete-image-data) | [Zmień zasady](#change-policies) |   [Podpisz obrazy](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Właściciel | X | X | X | X | X | X |  |  
| Współautor | X | X | X |  X | X | X |  |  
| Czytelnik | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Odróżnianie użytkowników i usług

Wszystkie uprawnienia są stosowane, najlepszym rozwiązaniem jest zapewnienie najbardziej ograniczonego zestawu uprawnień dla osoby lub usługi, w celu wykonania zadania. Następujące zbiory uprawnień reprezentują zestaw możliwości, które mogą być używane przez ludzi i usługi bezobsługowe.

### <a name="cicd-solutions"></a>Rozwiązania CI/CD

W przypadku automatyzowania poleceń z rozwiązań ciągłej integracji/ciągłego wdrażania `docker build` wymagane są `docker push` funkcje. W przypadku tych scenariuszy usługi bezobsługowe sugerujemy przypisanie roli **AcrPush** . Ta rola, w przeciwieństwie do szerszej roli **współautor** , uniemożliwia kontu wykonywanie innych operacji rejestru lub uzyskiwanie dostępu do Azure Resource Manager.

### <a name="container-host-nodes"></a>Węzły hosta kontenera

Podobnie węzły korzystające z kontenerów wymagają roli **AcrPull** , ale nie wymagają możliwości **czytnika** .

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code rozszerzenie platformy Docker

Dla narzędzi, takich jak Visual Studio Code [Docker Extension](https://code.visualstudio.com/docs/azure/docker), wymagany jest dodatkowy dostęp dostawcy zasobów, aby wyświetlić listę dostępnych rejestrów kontenerów platformy Azure. W takim przypadku należy zapewnić użytkownikom dostęp do roli **czytelnik** lub **współautor** . Role te umożliwiają `docker pull` , `docker push` , `az acr list` , `az acr build` i inne możliwości. 

## <a name="access-resource-manager"></a>Menedżer zasobów dostępu

Do zarządzania Azure Portal i rejestrem za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/)jest wymagany dostęp Azure Resource Manager. Aby na przykład uzyskać listę rejestrów przy użyciu `az acr list` polecenia, musisz mieć ten zestaw uprawnień. 

## <a name="create-and-delete-registry"></a>Tworzenie i usuwanie rejestru

Możliwość tworzenia i usuwania rejestrów kontenerów platformy Azure.

## <a name="push-image"></a>Obraz wypychany

Możliwość `docker push` obrazu lub wypchnięcie innego [obsługiwanego artefaktu](container-registry-image-formats.md) , takiego jak wykres Helm, do rejestru. Wymaga [uwierzytelniania](container-registry-authentication.md) z rejestrem przy użyciu autoryzowanej tożsamości. 

## <a name="pull-image"></a>Obraz ściągania

Możliwość `docker pull` niepoddania kwarantannie obrazu lub ściągnięcia innego [obsługiwanego artefaktu](container-registry-image-formats.md) , takiego jak wykres Helm, z rejestru. Wymaga [uwierzytelniania](container-registry-authentication.md) z rejestrem przy użyciu autoryzowanej tożsamości.

## <a name="delete-image-data"></a>Usuń dane obrazu

Możliwość [usuwania obrazów kontenera](container-registry-delete.md)lub usuwania innych [obsługiwanych artefaktów](container-registry-image-formats.md) , takich jak wykresy Helm, z rejestru.

## <a name="change-policies"></a>Zmień zasady

Możliwość konfigurowania zasad w rejestrze. Zasady obejmują przeczyszczanie obrazu, Włączanie kwarantanny i podpisywanie obrazu.

## <a name="sign-images"></a>Podpisz obrazy

Możliwość podpisywania obrazów, zwykle przypisywanych do zautomatyzowanego procesu, który mógłby korzystać z nazwy głównej usługi. To uprawnienie jest zwykle połączone z [obrazem push](#push-image) , aby umożliwić wypychanie zaufanego obrazu do rejestru. Aby uzyskać szczegółowe informacje, zobacz [zaufanie zawartości w Azure Container Registry](container-registry-content-trust.md).

## <a name="custom-roles"></a>Role niestandardowe

Podobnie jak w przypadku innych zasobów platformy Azure, można utworzyć [role niestandardowe](../role-based-access-control/custom-roles.md) z szczegółowymi uprawnieniami do Azure Container Registry. Następnie przypisz niestandardowe role do użytkowników, podmiotów usługi lub innych tożsamości, które muszą korzystać z rejestru. 

Aby określić, które uprawnienia mają być stosowane do roli niestandardowej, zobacz listę [akcji](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)Microsoft. ContainerRegistry, przejrzyj dozwolone akcje [wbudowanych ról ACR](../role-based-access-control/built-in-roles.md)lub uruchom następujące polecenie:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Aby zdefiniować rolę niestandardową, zapoznaj [się z instrukcjami tworzenia roli niestandardowej](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> W roli niestandardowej Azure Container Registry nie obsługuje obecnie symboli wieloznacznych, takich jak `Microsoft.ContainerRegistry/*` lub `Microsoft.ContainerRegistry/registries/*` udzielających dostępu do wszystkich akcji dopasowywania. Określ dowolną akcję wymaganą pojedynczo w roli.

### <a name="example-custom-role-to-import-images"></a>Przykład: rola niestandardowa do importowania obrazów

Na przykład poniższy kod JSON definiuje minimalne akcje dla roli niestandardowej, które zezwalają na [Importowanie obrazów](container-registry-import-images.md) do rejestru.

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

Aby utworzyć lub zaktualizować rolę niestandardową przy użyciu opisu JSON, użyj [interfejsu wiersza polecenia platformy Azure](../role-based-access-control/custom-roles-cli.md), [szablonu Azure Resource Manager](../role-based-access-control/custom-roles-template.md), [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)lub innych narzędzi platformy Azure. Dodawanie lub usuwanie przypisań ról niestandardowych w taki sam sposób, w jaki zarządzasz przypisaniami ról dla wbudowanych ról platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat przypisywania ról platformy Azure do tożsamości platformy Azure przy użyciu [Azure Portal](../role-based-access-control/role-assignments-portal.md), [interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)lub innych narzędzi platformy Azure.

* Dowiedz się więcej na temat [opcji uwierzytelniania](container-registry-authentication.md) dla Azure Container Registry.

* Dowiedz się więcej na temat włączania [uprawnień do zakresu repozytorium](container-registry-repository-scoped-permissions.md) (wersja zapoznawcza) w rejestrze kontenerów.