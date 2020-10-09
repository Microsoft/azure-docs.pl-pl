---
title: Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą procesu nauka danych w Azure Pipelines zespołowym
description: Utwórz potok ciągłej integracji i ciągłego dostarczania dla aplikacji sztucznej analizy (AI) przy użyciu platformy Docker i Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76721833"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Tworzenie potoków ciągłej integracji/ciągłego wdrażania dla aplikacji AI przy użyciu Azure Pipelines, Docker i Kubernetes

Sztuczna aplikacja (AI) jest kodem aplikacji osadzonym w modelu uczenia maszynowego. Zawsze istnieją dwa strumienie pracy dla aplikacji AI: analityki danych tworzą model ML, a deweloperzy aplikacji tworzą aplikację i uwidaczniają ją użytkownikom końcowym. W tym artykule opisano sposób implementacji potoku ciągłej integracji i ciągłego dostarczania (CI/CD) dla aplikacji AI, która osadza model ML w kodzie źródłowym aplikacji. Przykładowy kod i samouczek wykorzystują aplikację sieci Web z kolbą Python i pobierają przeddany model z prywatnego konta magazynu obiektów blob platformy Azure. Możesz również użyć konta magazynu AWS S3.

> [!NOTE]
> Poniższy proces to jeden z kilku sposobów na przeprowadzenie ciągłej integracji/ciągłego wdrażania. Istnieją alternatywy dla tego narzędzia i wymagania wstępne.

## <a name="source-code-tutorial-and-prerequisites"></a>Kod źródłowy, samouczek i wymagania wstępne

Możesz pobrać [kod źródłowy](https://github.com/Azure/DevOps-For-AI-Apps) i [szczegółowy samouczek](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) z usługi GitHub. Postępuj zgodnie z instrukcjami w samouczku, aby zaimplementować potok ciągłej integracji/ciągłego dostarczania dla własnej aplikacji.

Aby użyć pobranego kodu źródłowego i samouczka, potrzebne są następujące wymagania wstępne: 

- [Repozytorium kodu źródłowego](https://github.com/Azure/DevOps-For-AI-Apps) rozwidlenie na konto usługi GitHub
- [Organizacja usługi Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- [Klaster Azure Container Service for Kubernetes (AKS)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) do uruchamiania poleceń i konfiguracji pobierania z klastra AKS 
- [Konto Azure Container Registry (ACR)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Podsumowanie potoku ciągłej integracji/ciągłego wdrażania

Każde nowe zatwierdzenie git rozpoczyna się od potoku kompilacji. Kompilacja bezpiecznie pobiera najnowszy model ML z konta usługi BLOB Storage i umieszcza je w kodzie aplikacji w pojedynczym kontenerze. To oddzielenie workstreams projektowania aplikacji i nauki o danych gwarantuje, że aplikacja produkcyjna zawsze uruchamia najnowszy kod przy użyciu najnowszego modelu ML. Jeśli aplikacja przejdzie test, potok w bezpieczny sposób przechowuje obraz kompilacji w kontenerze platformy Docker w ACR. Potok wersji wdraża następnie kontener za pomocą AKS. 

## <a name="cicd-pipeline-steps"></a>Kroki potoku ciągłej integracji/ciągłego wdrażania

Poniższy diagram i kroki opisują architekturę potoku CI/CD:

![Architektura potoku ciągłej integracji/ciągłego wdrażania](./media/ci-cd-flask/architecture.png)

1. Deweloperzy pracują nad kodem aplikacji w wybranym przez siebie środowisku IDE.
2. Deweloperzy zatwierdzają kod do Azure Repos, GitHub lub innego dostawcy kontroli źródła git. 
3. Niezależnie od analityków danych pracują nad programowaniem modelu ML.
4. Badacze danych publikują gotowy model w repozytorium modelu, w tym przypadku konto magazynu obiektów BLOB. 
5. Azure Pipelines uruchamia kompilację w oparciu o zatwierdzenie git.
6. Potok kompilacji pobiera najnowszy model ML z magazynu obiektów blob i tworzy kontener.
7. Potok wypychanie obrazu kompilacji do repozytorium obrazów prywatnych w programie ACR.
8. Potok wydania rozpoczyna się w oparciu o pomyślną kompilację.
9. Potok ściąga najnowszy obraz z ACR i wdraża go w klastrze Kubernetes na AKS.
10. Żądania użytkowników dla aplikacji przechodzą przez serwer DNS.
11. Serwer DNS przekazuje żądania do modułu równoważenia obciążenia i wysyła odpowiedzi z powrotem do użytkowników.

## <a name="see-also"></a>Zobacz też

- [Proces nauki o danych zespołowych (przetwarzania TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Usługi Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes)
