---
title: Uruchamianie kontenerów inicjujących
description: Uruchom kontenery init w Azure Container Instances, aby wykonać zadania instalacyjne w grupie kontenerów przed uruchomieniem kontenerów aplikacji.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85954285"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Uruchamianie kontenera init na potrzeby zadań instalacyjnych w grupie kontenerów

Azure Container Instances obsługuje *kontenery init* w grupie kontenerów. Kontenery inicjowania działają do zakończenia przed rozpoczęciem kontenera lub kontenerów aplikacji. Podobnie jak w przypadku [kontenerów init Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/), Użyj co najmniej jednego kontenera init do wykonania logiki inicjalizacji dla kontenerów aplikacji, takich jak Ustawianie kont, uruchamianie skryptów Instalatora lub Konfigurowanie baz danych.

W tym artykule pokazano, jak za pomocą szablonu Azure Resource Manager skonfigurować grupę kontenerów za pomocą kontenera init.

## <a name="things-to-know"></a>Co należy wiedzieć

* **Wersja interfejsu API** — aby wdrożyć kontenery init, musisz mieć co najmniej Azure Container Instances interfejs API w wersji 2019-12-01. Wdróż przy użyciu `initContainers` właściwości w [pliku YAML](container-instances-multi-container-yaml.md) lub [szablonie Menedżer zasobów](container-instances-multi-container-group.md).
* **Porządek wykonywania** — kontenery init są wykonywane w kolejności określonej w szablonie oraz przed innymi kontenerami. Domyślnie można określić maksymalnie 59 kontenerów init dla każdej grupy kontenerów. Co najmniej jeden kontener, który nie jest init, musi znajdować się w grupie.
* **Środowisko hosta** — kontenery init są uruchamiane na tym samym sprzęcie co pozostałe kontenery w grupie.
* **Zasoby** — nie można określać zasobów dla kontenerów init. Są przyznawane całkowite zasoby, takie jak procesory CPU i pamięć dostępna dla grupy kontenerów. Podczas uruchamiania kontenera init w grupie nie są uruchamiane żadne inne kontenery.
* **Obsługiwane właściwości** — kontenery init mogą używać właściwości grupy, takich jak woluminy, wpisy tajne i zarządzane tożsamości. Nie mogą jednak używać portów ani adresów IP, jeśli są skonfigurowane dla grupy kontenerów. 
* **Zasady ponownego uruchamiania** — poszczególne kontenery init muszą zakończyć się pomyślnie przed rozpoczęciem następnego kontenera w grupie. Jeśli kontener init nie zakończy się pomyślnie, jego akcja ponownego uruchomienia zależy od [zasad ponownego uruchamiania](container-instances-restart-policy.md) skonfigurowanych dla grupy:

    |Zasady w grupie  |Zasady w init  |
    |---------|---------|
    |Always (Zawsze)     |OnFailure (W razie niepowodzenia)         |
    |OnFailure (W razie niepowodzenia)     |OnFailure (W razie niepowodzenia)         |
    |Nigdy     |Nigdy         |
* **Opłaty** — Grupa kontenerów wiąże się z pierwszym wdrożeniem kontenera init.

## <a name="resource-manager-template-example"></a>Przykład szablonu Menedżer zasobów

Zacznij od skopiowania następującego kodu JSON do nowego pliku o nazwie `azuredeploy.json` . Szablon konfiguruje grupę kontenerów za pomocą jednego kontenera init i dwóch kontenerów aplikacji:

* Kontener *init1* uruchamia obraz [BUSYBOX](https://hub.docker.com/_/busybox) z usługi Docker Hub. Uśpienie przez 60 sekund, a następnie zapisanie ciągu wiersza polecenia do pliku w [woluminie emptyDir](container-instances-volume-emptydir.md).
* Oba kontenery aplikacji uruchamiają `aci-wordcount` obraz kontenera firmy Microsoft:
    * Kontener *Hamlet* uruchamia aplikację WORDCOUNT w konfiguracji domyślnej, licząc częstotliwości wyrazów w *Hamlet* Play Szekspira.
    * Kontener aplikacji *Juliet* odczytuje ciąg wiersza polecenia z woluminu emptDir, aby uruchomić aplikację WORDCOUNT zamiast Szekspira *Romeo i Juliet*.

Aby uzyskać więcej informacji i przykładów przy użyciu `aci-wordcount` obrazu, zobacz [Ustawianie zmiennych środowiskowych w wystąpieniach kontenerów](container-instances-environment-variables.md).

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

Wdróż szablon za pomocą polecenia [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

W grupie z kontenerem init czas wdrożenia zostaje zwiększony ze względu na czas trwania kontenera init lub kontenerów.


## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

Aby sprawdzić, czy kontener inicjowania został pomyślnie uruchomiony, Wyświetl dane wyjściowe dzienników kontenerów aplikacji za pomocą polecenia [AZ Container Logs][az-container-logs] . `--container-name`Argument określa kontener, z którego mają być ściągane dzienniki. W tym przykładzie należy ściągnąć dzienniki kontenerów *Hamlet* i *Juliet* , które pokazują różne dane wyjściowe polecenia:

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

Kontenery inicjowania ułatwiają wykonywanie zadań instalacyjnych i inicjalizacji dla kontenerów aplikacji. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie zadań kontenera z zasadami ponownego uruchamiania](container-instances-restart-policy.md).

Azure Container Instances oferuje inne opcje modyfikacji zachowania kontenerów aplikacji. Przykłady obejmują:

* [Ustawianie zmiennych środowiskowych w wystąpieniach kontenerów](container-instances-environment-variables.md)
* [Ustaw wiersz polecenia w wystąpieniu kontenera w celu zastąpienia domyślnej operacji wiersza polecenia](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
