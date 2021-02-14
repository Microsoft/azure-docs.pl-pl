---
title: Tworzenie głównej nazwy usługi Azure ARC, która umożliwia dołączanie (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Tworzenie jednostki usługi do dołączania z funkcją Azure Arc '
keywords: Kubernetes, łuk, Azure, kontenery
ms.openlocfilehash: 8772cf7634d9a833af120784e3e7868b41d202c4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390491"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Tworzenie głównej nazwy usługi Azure ARC, która umożliwia dołączanie (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Klastry Kubernetes można dołączać do usługi Azure ARC przy użyciu jednostek usługi z ograniczonymi przypisaniami ról uprawnień. Ta funkcja jest przydatna w przypadku potoków ciągłej integracji i ciągłego wdrażania (CI/CD), takich jak Azure Pipelines i akcje GitHub.

Wykonaj następujące kroki, aby dowiedzieć się, jak używać jednostek usługi do dołączania klastrów Kubernetes do usługi Azure Arc.

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

Można ograniczyć uprawnienia, przekazując odpowiedni `--scope` argument podczas przypisywania roli. Pozwala to klientom ograniczyć rejestrację klastra. Następujące scenariusze są obsługiwane przez różne `--scope` Parametry:

| Zasób  | Argument `scope`| Efekt |
| ------------- | ------------- | ------------- |
| Subskrypcja | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Jednostka usługi może zarejestrować dowolny klaster w istniejącej grupie zasobów w danej subskrypcji. |
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
