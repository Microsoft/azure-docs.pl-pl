---
title: Utwórz nazwę główną usługi dołączania dla usługi Azure Arc Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Tworzenie jednostki usługi do dołączania z funkcją Azure Arc '
keywords: Kubernetes, łuk, Azure, kontenery
ms.openlocfilehash: bda088bdae5c866493718db94c9a2da89cada8c9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650350"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Utwórz nazwę główną usługi dołączania dla usługi Azure Arc Kubernetes

## <a name="overview"></a>Omówienie

Klastry Kubernetes można łączyć z usługą Azure ARC przy użyciu jednostek usługi z ograniczonymi przypisaniami ról. Ta funkcja jest przydatna w przypadku potoków ciągłej integracji i ciągłego wdrażania (CI/CD), takich jak Azure Pipelines i akcje GitHub.

Wykonaj następujące kroki, aby dowiedzieć się, jak używać jednostek usługi do łączenia klastrów Kubernetes z usługą Azure Arc.

## <a name="create-a-new-service-principal"></a>Utwórz nową nazwę główną usługi

Utwórz nową nazwę główną usługi o nazwie, która jest unikatowa dla dzierżawy Azure Active Directory.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Rozdzielczości**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Przypisywanie uprawnień

Przypisz rolę "Kubernetes klaster-Azure Arc dołączania" do nowo utworzonej nazwy głównej usługi. Ta wbudowana rola platformy Azure z ograniczonymi uprawnieniami zezwala tylko podmiotowi zabezpieczeń na rejestrowanie klastrów na platformie Azure. Podmiot zabezpieczeń z tą przypisaną rolą nie może aktualizować, usuwać ani modyfikować żadnych innych klastrów ani zasobów w ramach subskrypcji.

Mając na względzie ograniczone możliwości, klienci mogą z łatwością korzystać z tego podmiotu do dołączania wielu klastrów.

Można ograniczyć uprawnienia, przekazując odpowiedni `--scope` argument podczas przypisywania roli. Dzięki temu administratorzy mogą ograniczyć rejestrację klastra do zakresu subskrypcji lub grupy zasobów. Następujące scenariusze są obsługiwane przez różne `--scope` Parametry:

| Zasób  | Argument `scope`| Efekt |
| ------------- | ------------- | ------------- |
| Subskrypcja | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Nazwa główna usługi może rejestrować klaster w dowolnej grupie zasobów w ramach tej subskrypcji. |
| Grupa zasobów | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Nazwa główna usługi może rejestrować __tylko__ klastry w grupie zasobów `myGroup` . |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Rozdzielczości**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Korzystanie z jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure

Odwołuje się do nowo utworzonej jednostki usługi przy użyciu następujących poleceń:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
