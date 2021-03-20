---
title: Wdrażanie bramy samohostowanej w usłudze Azure Kubernetes Service | Microsoft Docs
description: Dowiedz się, jak wdrożyć składnik bramy samoobsługowej usługi Azure API Management w usłudze Azure Kubernetes Service
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015225"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Wdrażanie w usłudze Azure Kubernetes Service

W tym artykule przedstawiono procedurę wdrażania składnika bramy samoobsługowego API Management platformy Azure w [usłudze Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/). Aby wdrożyć bramę samoobsługową do klastra Kubernetes, zobacz ten[dokument](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md)
- [Tworzenie klastra usługi Azure Kubernetes](../aks/kubernetes-walkthrough-portal.md)
- [Zainicjuj obsługę administracyjną zasobu bramy w wystąpieniu API Management](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Wdrażanie bramy samohostowanej w usłudze AKS

1. Wybierz pozycję **bramy** z sekcji **wdrażanie i infrastruktura**.
2. Wybierz zasób bramy samoobsługowej, który ma zostać wdrożony.
3. Wybierz pozycję **wdrożenie**.
4. Należy zauważyć, że nowy token w polu tekstowym **token** został wygenerowany automatycznie przy użyciu domyślnych wartości kluczy **wygaśnięcia** i **tajnych** . Dostosuj jedną lub obie w razie potrzeby, a następnie wybierz pozycję **Generuj** , aby utworzyć nowy token.
5. Upewnij się, że wybrano **Kubernetes** w obszarze **Skrypty wdrażania**.
6. Wybierz pozycję **<Gateway-name>. yml** **, aby pobrać** plik.
7. W razie konieczności dostosuj mapowania portów i nazwę kontenera w pliku YML.
8. W zależności od danego scenariusza może zajść potrzeba zmiany [typu usługi](../aks/concepts-network.md#services). Wartość domyślna to `NodePort`.
9. Wybierz ikonę **kopiowania** znajdującą się po prawej stronie pola tekstowego **Wdróż** , aby zapisać `kubectl` polecenie do Schowka.
10. Wklej polecenie do okna terminalu (lub polecenia). Należy pamiętać, że polecenie oczekuje, że pobrany plik środowiska będzie obecny w bieżącym katalogu.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Wykonaj polecenie. Polecenie instruuje klaster AKS, aby uruchamiał kontener przy użyciu wstępnie hostowanego obrazu bramy pobranego z Container Registry firmy Microsoft i skonfigurować kontener do udostępniania portów HTTP (8080) i HTTPS (443).
12. Uruchom poniższe polecenie, aby sprawdzić, czy brama pod jest uruchomiona. Należy pamiętać, że nazwa użytkownika jest inna.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Uruchom poniższe polecenie, aby sprawdzić, czy Usługa bramy jest uruchomiona. Należy pamiętać, że nazwa usługi i adresy IP będą się różnić.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Wróć do Azure Portal i upewnij się, że wdrożony węzeł bramy jest w stanie dobra kondycja.

> [!TIP]
> Użyj <code>kubectl logs <gateway-pod-name></code> polecenia, aby wyświetlić migawkę dziennika bramy samohostowanej.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz temat [usługa Azure API Management](self-hosted-gateway-overview.md) samodzielna Brama — Omówienie
* Dowiedz się więcej o [usłudze Azure Kubernetes Service](../aks/intro-kubernetes.md)
* Informacje [na temat konfigurowania i utrwalania dzienników w chmurze](how-to-configure-cloud-metrics-logs.md)
* * Informacje [na temat konfigurowania i utrwalania dzienników lokalnie](how-to-configure-local-metrics-logs.md)
