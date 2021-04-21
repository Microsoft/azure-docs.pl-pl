---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 592f62eaf1627733f8cf20460b57e3f474f17963
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800128"
---
## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby utworzyć jednostkę usługi z dostępem do rejestru kontenerów, uruchom następujący skrypt w Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli) [](../articles/cloud-shell/overview.md) Skrypt jest sformatowany dla powłoki Bash.

Przed uruchomieniem skryptu zaktualizuj `ACR_NAME` zmienną przy użyciu nazwy rejestru kontenerów. Wartość `SERVICE_PRINCIPAL_NAME` musi być unikatowa w ramach dzierżawy Azure Active Directory dzierżawy. Jeśli zostanie wyświetlony błąd `'http://acr-service-principal' already exists.` "", określ inną nazwę jednostki usługi.

Opcjonalnie możesz zmodyfikować wartość w poleceniu `--role` [az ad sp create-for-rbac,][az-ad-sp-create-for-rbac] jeśli chcesz przyznać różne uprawnienia. Aby uzyskać pełną listę ról, zobacz [Role i uprawnienia usługi ACR.](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md)

Po uruchomieniu skryptu zanotuj identyfikator i hasło **jednostki** **usługi**. Po poświadczeniu możesz skonfigurować aplikacje i usługi do uwierzytelniania w rejestrze kontenerów jako jednostkę usługi.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Używanie istniejącej jednostki usługi

Aby udzielić dostępu rejestru do istniejącej jednostki usługi, musisz przypisać nową rolę do jednostki usługi. Podobnie jak w przypadku tworzenia nowej jednostki usługi, możesz między innymi udzielić dostępu do ściągania, wypychania i ściągania oraz do właściciela.

W poniższym skrypcie użyto polecenia  [az role assignment create,][az-role-assignment-create] aby udzielić uprawnień do ściągania do jednostki usługi określisz w `SERVICE_PRINCIPAL_ID` zmiennej . Dostosuj `--role` wartość, jeśli chcesz przyznać inny poziom dostępu.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
