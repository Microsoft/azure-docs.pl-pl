---
title: Zarządzanie wpisami tajnymi aplikacji usługi Azure Service Fabric siatka
description: Zarządzanie wpisami tajnymi aplikacji umożliwia bezpieczne tworzenie i wdrażanie aplikacji usługi Service Fabric Mesh.
ms.date: 4/2/2019
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3be0c2b21c3405f4f42b2ff4d02ca95c78956de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626964"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Zarządzanie wpisami tajnymi aplikacji usługi Service Fabric Mesh

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Usługa Service Fabric Mesh obsługuje wpisy tajne jako zasoby platformy Azure. Wpis tajny usługi Service Fabric Mesh może być dowolnym poufnym tekstem, takim jak parametry połączenia magazynu, hasła lub inne wartości, które powinny być przechowywane i przesyłane w bezpieczny sposób. W tym artykule pokazano, jak za pomocą usługi bezpiecznego magazynu usługi Service Fabric można wdrażać wpisy tajne i konserwować je.

Wpis tajny aplikacji usługi Mesh składa się z następujących elementów:
* Zasób **Wpisy tajne**, który jest kontenerem przechowującym tekstowe wpisy tajne. Wpisy tajne zawarte w zasobie **Wpisy tajne** są przechowywane i przesyłane w bezpieczny sposób.
* Co najmniej jeden zasób **Wpisy tajne/wartości**, który jest przechowywany w kontenerze zasobów **Wpisy tajne**. Zasoby **Wpisy tajne/wartości** rozróżnia się na podstawie numeru wersji. Nie można zmodyfikować wersji zasobu **Wpisy tajne/wartości**. Można jedynie dołączyć nową wersję.

Aby zarządzać wpisami tajnymi, wykonaj następujące kroki:
1. Zadeklaruj zasób siatki **kluczy tajnych** w pliku YAML lub JSON modelu zasobów platformy Azure przy użyciu definicji typu InlinedValue i SecretsStoreRef.
2. Zadeklaruj zasoby dotyczące **wartości tajnych/** zasobów siatki w pliku YAML lub JSON modelu zasobów platformy Azure, który będzie przechowywany w zasobie **tajnym** (z kroku 1).
3. Zmodyfikuj aplikację usługi Mesh tak, aby przywoływała wartości wpisów tajnych usługi Mesh.
4. Przeprowadź wdrożenie lub uaktualnienie stopniowe aplikacji usługi Mesh, aby skorzystać z wartości wpisów tajnych.
5. Użyj poleceń „az” interfejsu wiersza polecenia platformy Azure do zarządzania cyklem życia usługi bezpiecznego magazynu.

## <a name="declare-a-mesh-secrets-resource"></a>Deklarowanie zasobu Wpisy tajne usługi Mesh
Zasób z pustą siatką jest zadeklarowany w pliku JSON lub YAML modelu zasobów platformy Azure przy użyciu definicji typu inlinedValue. Zasób Wpisy tajne usługi Mesh obsługuje wpisy tajne pochodzące z usługi bezpiecznego magazynu. 
>
Poniżej przedstawiono przykładową deklarację zasobów Wpisy tajne usługi Mesh w pliku JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
Poniżej przedstawiono przykładową deklarację zasobów Wpisy tajne usługi Mesh w pliku YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Deklarowanie zasobów Wpisy tajne/wartości
Zasoby Wpisy tajne/wartości usługi Mesh zależą od zasobów Wpisy tajne usługi Mesh zdefiniowanych w poprzednim kroku.

Relacja między polami „value:” i „name:” w sekcji „resources” jest następująca: druga część ciągu „name:” oddzielona dwukropkiem to numer wersji używany dla wpisu tajnego, a nazwa przed dwukropkiem musi być zgodna z wartością wpisu tajnego usługi Mesh, od którego zależy. Na przykład dla elementu ```name: mysecret:1.0``` numer wersji to 1.0, a nazwa ```mysecret``` musi być zgodna z uprzednio zdefiniowaną wartością ```"value": "mysecret"```.

>
Poniżej przedstawiono przykładową deklarację zasobów Wpisy tajne/wartości usługi Mesh w pliku JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
Poniżej przedstawiono przykładową deklarację zasobów Wpisy tajne/wartości usługi Mesh w pliku YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Modyfikowanie aplikacji usługi Mesh pod kątem przywoływania wartości wpisów tajnych usługi Mesh
Aplikacje usługi Service Fabric Mesh muszą obsługiwać następujące dwa ciągi, aby korzystać z wartości wpisów tajnych w usłudze bezpiecznego magazynu:
1. Element Microsoft.ServiceFabricMesh/Secrets.name zawiera nazwę pliku i będzie zawierać wartość zasobu Wpisy tajne w postaci zwykłego tekstu.
2. Zmienna środowiskowa „Fabric_SettingPath” systemu Windows lub Linux zawiera ścieżkę katalogu, w którym będą dostępne pliki zawierające wartości zasobu Wpisy tajne usługi bezpiecznego magazynu. Te ścieżki to „C:\Settings” i „/var/settings” dla aplikacji usługi Mesh hostowanych odpowiednio w systemie Windows i Linux.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Wdrażanie lub uaktualnianie stopniowe aplikacji usługi Mesh w celu korzystania z wartości wpisów tajnych
Tworzenie zasobów Wpisy tajne i/lub wersjonowanych zasobów Wpisy tajne/wartości jest ograniczone do wdrożeń z zadeklarowanym modelem zasobów. Jedynym sposobem utworzenia tych zasobów jest przekazanie pliku JSON lub YAML modelu zasobów za pomocą polecenia **az mesh deployment** w następujący sposób:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Polecenia interfejsu wiersza polecenia platformy Azure do zarządzania cyklem życia usługi bezpiecznego magazynu

### <a name="create-a-new-secrets-resource"></a>Tworzenie nowego zasobu Wpisy tajne
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Przekaż parametr **template-file** lub **template-uri** (lecz nie oba).

Na przykład:
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- AZ siatki Deployment Create--https: \/ /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Wyświetlanie wpisu tajnego
Zwraca opis wpisu tajnego (lecz nie wartość).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Usuwanie klucza tajnego

- Nie można usunąć wpisu tajnego, gdy jest przywoływany przez aplikację usługi Mesh.
- Usunięcie zasobu Wpisy tajne powoduje usunięcie wszystkich wersji zasobu Wpisy tajne.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Wyświetlanie wpisów tajnych w subskrypcji
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Wyświetlanie wpisów tajnych w grupie zasobów
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Wyświetlanie wszystkich wersji wpisu tajnego
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Wyświetlanie wartości wersji wpisu tajnego
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Usuwanie wartości wersji wpisu tajnego
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Następne kroki 
Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md)
