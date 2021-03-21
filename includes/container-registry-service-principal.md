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
ms.openlocfilehash: f2d2b655e80f5b9694fb1948b136aac918312ca9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244825"
---
## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby utworzyć jednostkę usługi z dostępem do rejestru kontenerów, uruchom następujący skrypt w [Azure Cloud Shell](../articles/cloud-shell/overview.md) lub lokalnej instalacji [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Skrypt jest sformatowany dla powłoki bash.

Przed uruchomieniem skryptu należy zaktualizować `ACR_NAME` zmienną za pomocą nazwy rejestru kontenerów. `SERVICE_PRINCIPAL_NAME`Wartość musi być unikatowa w ramach dzierżawy Azure Active Directory. Jeśli zostanie wyświetlony błąd " `'http://acr-service-principal' already exists.` ", określ inną nazwę dla jednostki usługi.

Opcjonalnie możesz zmodyfikować `--role` wartość w polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create-for-rbac] , jeśli chcesz przyznać różne uprawnienia. Aby uzyskać pełną listę ról, zobacz [ACR ról i uprawnień](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Po uruchomieniu skryptu Zanotuj **Identyfikator** i **hasło** jednostki usługi. Po uzyskaniu poświadczeń można skonfigurować aplikacje i usługi do uwierzytelniania w rejestrze kontenerów jako nazwę główną usługi.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Użyj istniejącej nazwy głównej usługi

Aby udzielić dostępu do rejestru do istniejącej nazwy głównej usługi, należy przypisać nową rolę do jednostki usługi. Podobnie jak w przypadku tworzenia nowej jednostki usługi, można przyznać dostęp do ściągania, wypychania i ściągania oraz dostępu właściciela, między innymi.

Poniższy skrypt używa polecenia [AZ role przypisanie Create][az-role-assignment-create] , aby przyznać uprawnienia *ściągnięcia* do jednostki usługi określonej w `SERVICE_PRINCIPAL_ID` zmiennej. Dostosuj `--role` wartość, jeśli chcesz udzielić innego poziomu dostępu.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
