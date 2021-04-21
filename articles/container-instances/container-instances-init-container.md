---
title: Uruchamianie kontenerów init
description: Uruchom kontenery init w Azure Container Instances, aby wykonać zadania konfiguracji w grupie kontenerów przed uruchomieniem kontenerów aplikacji.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 9ccaf1a67d6ca3bcff422acb591b528cc72a9608
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763941"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Uruchamianie kontenera init dla zadań konfiguracji w grupie kontenerów

Azure Container Instances obsługuje *kontenery init w* grupie kontenerów. Kontenery init są uruchamiane do ukończenia przed uruchomieniem kontenera aplikacji lub kontenerów. Podobnie jak w przypadku kontenerów init usługi [Kubernetes,](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)użyj co najmniej jednego kontenera init, aby wykonać logikę inicjowania dla kontenerów aplikacji, taką jak ustawianie kont, uruchamianie skryptów konfiguracji lub konfigurowanie baz danych.

W tym artykule pokazano, jak za pomocą szablonu Azure Resource Manager skonfigurować grupę kontenerów z kontenerem init.

## <a name="things-to-know"></a>Co należy wiedzieć

* **Wersja interfejsu API** — do wdrożenia kontenerów init jest Azure Container Instances api w wersji 2019-12-01. Wdrażanie przy użyciu `initContainers` właściwości w pliku [YAML lub](container-instances-multi-container-yaml.md) Resource Manager [szablonu](container-instances-multi-container-group.md).
* **Kolejność wykonywania —** kontenery init są wykonywane w kolejności określonej w szablonie i przed innymi kontenerami. Domyślnie można określić maksymalnie 59 kontenerów init na grupę kontenerów. W grupie musi znajdować się co najmniej jeden kontener bez init.
* **Środowisko hosta** — kontenery init działają na tym samym sprzęcie co pozostałe kontenery w grupie.
* **Zasoby** — nie określa się zasobów dla kontenerów init. Przyznano im łączną ilość zasobów, takich jak procesory CPU i pamięć dostępne dla grupy kontenerów. Podczas uruchamiania kontenera init żadne inne kontenery nie są uruchamiane w grupie.
* **Obsługiwane właściwości** — kontenery init mogą używać właściwości grupy, takich jak woluminy, wpisy tajne i tożsamości zarządzane. Nie mogą jednak używać portów ani adresów IP, jeśli zostały skonfigurowane dla grupy kontenerów. 
* **Zasady ponownego** uruchamiania — każdy kontener init musi zakończyć się pomyślnie przed uruchomieniem następnego kontenera w grupie. Jeśli kontener init nie zakończy się pomyślnie, jego akcja ponownego uruchomienia zależy od [zasad](container-instances-restart-policy.md) ponownego uruchamiania skonfigurowanych dla grupy:

    |Zasady w grupie  |Zasady w init  |
    |---------|---------|
    |Always (Zawsze)     |OnFailure (W razie niepowodzenia)         |
    |OnFailure (W razie niepowodzenia)     |OnFailure (W razie niepowodzenia)         |
    |Nigdy     |Nigdy         |
* **Opłaty** — za grupę kontenerów są naliczane opłaty z pierwszego wdrożenia kontenera init.

## <a name="resource-manager-template-example"></a>Resource Manager przykład szablonu

Zacznij od skopiowania następującego pliku JSON do nowego pliku o nazwie `azuredeploy.json` . Szablon konfiguruje grupę kontenerów z jednym kontenerem init i dwoma kontenerami aplikacji:

* Kontener *init1* uruchamia obraz [busybox](https://hub.docker.com/_/busybox) z Docker Hub. Jest uśpięty przez 60 sekund, a następnie zapisuje ciąg wiersza polecenia do pliku w [woluminie emptyDir](container-instances-volume-emptydir.md).
* Oba kontenery aplikacji uruchamiają obraz kontenera `aci-wordcount` firmy Microsoft:
    * Kontener *hamletów* uruchamia aplikację wordcount w konfiguracji domyślnej, zliczać częstotliwości wyrazów w odtwarzaniu *Hamleta* Trzmyka.
    * Kontener *aplikacji juliet* odczytuje ciąg wiersza polecenia z woluminu emptDir, aby uruchomić aplikację wordcount, a nie na ekranie Potrząsanie *i Julia.*

Aby uzyskać więcej informacji i przykładów dotyczących korzystania z `aci-wordcount` obrazu, zobacz Set environment variables in container instances (Ustawianie [zmiennych środowiskowych w wystąpieniach kontenerów).](container-instances-environment-variables.md)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Wd wdrażaj szablon za pomocą [polecenia az deployment group create.][az-deployment-group-create]

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

W grupie z kontenerem init czas wdrożenia jest zwiększany z powodu czasu, który zajmuje ukończenie kontenera init lub kontenerów.


## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

Aby sprawdzić, czy kontener init został pomyślnie zatrzymany, wyświetl dane wyjściowe dziennika kontenerów aplikacji przy użyciu [polecenia az container logs.][az-container-logs] Argument `--container-name` określa kontener, z którego mają być ściągane dzienniki. W tym przykładzie ściągnij dzienniki dla kontenerów *hamlet* i *juliet,* które pokazują różne dane wyjściowe polecenia:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Dane wyjściowe:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Dane wyjściowe:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Następne kroki

Kontenery init ułatwiają wykonywanie zadań konfiguracji i inicjowania dla kontenerów aplikacji. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie konteneryzowanych zadań przy użyciu zasad ponownego uruchamiania.](container-instances-restart-policy.md)

Azure Container Instances udostępnia inne opcje modyfikowania zachowania kontenerów aplikacji. Przykłady obejmują:

* [Ustawianie zmiennych środowiskowych w wystąpieniach kontenerów](container-instances-environment-variables.md)
* [Ustawianie wiersza polecenia w wystąpieniu kontenera w celu zastąpienia domyślnej operacji wiersza polecenia](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs
