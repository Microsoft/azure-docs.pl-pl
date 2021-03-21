---
title: Używanie Azure Container Registry z usługą Azure Red Hat OpenShift
description: Dowiedz się, jak ściągać i uruchamiać kontener z Azure Container Registry w klastrze Red Hat OpenShift platformy Azure.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634417"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Używanie Azure Container Registry z usługą Azure Red Hat OpenShift (ARO)

Azure Container Registry (ACR) to zarządzana usługa rejestru kontenerów, która służy do przechowywania prywatnych obrazów kontenerów platformy Docker z możliwościami przedsiębiorstwa, takimi jak replikacja geograficzna. Aby uzyskać dostęp do ACR z klastra ARO, klaster może być uwierzytelniany przy użyciu ACR przez przechowywanie poświadczeń logowania platformy Docker w Kubernetes wpisie tajnym.  Podobnie klaster ARO może użyć imagePullSecret w specyfikacji pod, aby uwierzytelnić się w rejestrze podczas ściągania obrazu. W tym artykule dowiesz się, jak skonfigurować Azure Container Registry przy użyciu klastra Red Hat OpenShift na platformie Azure w celu przechowywania i ściągania prywatnych obrazów kontenerów platformy Docker.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku przyjęto założenie, że masz istniejący Azure Container Registry. Jeśli tego nie zrobisz, użyj [instrukcji interfejsu wiersza polecenia Azure Portal lub platformy Azure](../container-registry/container-registry-get-started-azure-cli.md) , aby utworzyć rejestr kontenerów.

W tym artykule przyjęto również założenie, że masz istniejący klaster systemu Azure Red Hat OpenShift i `oc` zainstalowano interfejs wiersza polecenia. Jeśli nie, postępuj zgodnie z instrukcjami w [samouczku Tworzenie klastra](tutorial-create-cluster.md)z wypełnieniem.

## <a name="get-a-pull-secret"></a>Pobierz klucz tajny ściągania

Do uzyskania dostępu do rejestru z poziomu klastra.

Aby uzyskać poświadczenia ściągania hasła, możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure.

Jeśli używasz Azure Portal, przejdź do wystąpienia usługi ACR i wybierz pozycję **klucze dostępu**.  `docker-username`Jest to nazwa rejestru kontenerów, Użyj hasła lub password2 dla `docker-password` .

![Klucze dostępu](./media/acr-access-keys.png)

Zamiast tego można użyć interfejsu wiersza polecenia platformy Azure, aby uzyskać następujące poświadczenia:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Tworzenie klucza tajnego Kubernetes

Teraz użyjemy tych poświadczeń, aby utworzyć wpis tajny Kubernetes. Wykonaj następujące polecenie z poświadczeniami programu ACR:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Ten wpis tajny będzie przechowywany w bieżącym projekcie OpenShift (Kubernetes Namespace) i będzie można do niego odwoływać się tylko według zasobników utworzonych w tym projekcie.  Zapoznaj się z tym [dokumentem](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) , aby uzyskać dalsze instrukcje dotyczące tworzenia szerokiego klucza tajnego klastra.

## <a name="create-a-pod-using-a-private-registry-image"></a>Utwórz pod za pomocą prywatnego obrazu rejestru

Teraz, gdy klaster ARO został połączony z ACR, pobierzemy obraz z ACR, aby utworzyć element pod.

Zacznij od podSpec i określ wpis tajny utworzony jako imagePullSecret:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Aby sprawdzić, czy Twoje urządzenie jest uruchomione, wykonaj to polecenie i poczekaj na **uruchomienie** stanu:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Następne kroki

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Szybki Start: Tworzenie prywatnego rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md)
