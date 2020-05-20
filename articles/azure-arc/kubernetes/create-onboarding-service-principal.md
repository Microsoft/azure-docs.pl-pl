---
title: Tworzenie głównej nazwy usługi z funkcją Azure Arc — wersja zapoznawcza
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Tworzenie jednostki usługi do dołączania z funkcją Azure Arc '
keywords: Kubernetes, łuk, Azure, kontenery
ms.openlocfilehash: f9f750980d8a8b5d8190ba0b399fe068f1dd99c7
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680800"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Tworzenie głównej nazwy usługi z funkcją Azure Arc — wersja zapoznawcza

## <a name="overview"></a>Omówienie

Po dołączeniu klastra do platformy Azure Agenty działające w klastrze muszą uwierzytelniać się w Azure Resource Manager w ramach rejestracji. `connectedk8s`Rozszerzenie interfejsu wiersza polecenia platformy Azure ma zautomatyzowane tworzenie głównej nazwy usługi. Jednak może istnieć kilka scenariuszy, w których Automatyzacja interfejsu wiersza polecenia nie działa:

* Organizacja zazwyczaj ogranicza tworzenie jednostek usługi
* Użytkownik, który utworzył klaster, nie ma wystarczających uprawnień do tworzenia jednostek usługi

Zamiast tego Utwórz nazwę główną usługi poza pasmem, a następnie Przekaż podmiot zabezpieczeń do rozszerzenia interfejsu wiersza polecenia platformy Azure.

## <a name="create-a-new-service-principal"></a>Utwórz nową nazwę główną usługi

Utwórz nową nazwę główną usługi z niesformatowaną nazwą. Należy pamiętać, że ta nazwa musi być unikatowa dla dzierżawy Azure Active Directory:

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

Po utworzeniu nowej jednostki usługi Przypisz rolę "Azure Arc for Kubernetes Onboard" do nowo utworzonego podmiotu zabezpieczeń. Jest to wbudowana rola platformy Azure z ograniczonymi uprawnieniami, która umożliwia podmiotowi zabezpieczeń rejestrowanie klastrów na platformie Azure. Podmiot zabezpieczeń nie może aktualizować, usuwać ani modyfikować żadnych innych klastrów ani zasobów w ramach subskrypcji.

Mając na względzie ograniczone możliwości, klienci mogą z łatwością korzystać z tego podmiotu do dołączania wielu klastrów.

Uprawnienia mogą być dodatkowo ograniczone przez przekazanie odpowiednich `--scope` argumentów podczas przypisywania roli. Pozwala to klientom ograniczyć rejestrację klastra. Następujące scenariusze są obsługiwane przez różne `--scope` Parametry:

| Zasób  | Argument `scope`| Efekt |
| ------------- | ------------- | ------------- |
| Subskrypcja | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Jednostka usługi może zarejestrować dowolny klaster w istniejącej grupie zasobów w danej subskrypcji. |
| Grupa zasobów | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Nazwa główna usługi może rejestrować __tylko__ klastry w grupie zasobów`myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the apropriate scope
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

Odwołuje się do nowo utworzonej jednostki usługi:

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
