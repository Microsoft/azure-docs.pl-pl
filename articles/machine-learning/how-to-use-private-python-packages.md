---
title: Używanie prywatnych pakietów języka Python
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak bezpiecznie współpracować z prywatnymi pakietami języka Python w środowiskach Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: b922c25561843d140f1e2b8221f62fad89ea00c8
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520901"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Używanie prywatnych pakietów języka Python z Azure Machine Learning


W tym artykule dowiesz się, jak bezpiecznie używać prywatnych pakietów języka Python w programie Azure Machine Learning. Przypadki użycia dla prywatnych pakietów języka Python obejmują:

 * Opracowano prywatny pakiet, który nie powinien być udostępniany publicznie.
 * Chcesz użyć zanadzorowanego repozytorium pakietów przechowywanych w zaporze przedsiębiorstwa.

Zalecane podejście zależy od tego, czy masz kilka pakietów dla jednego obszaru roboczego Azure Machine Learning, czy też całe repozytorium pakietów dla wszystkich obszarów roboczych w organizacji.

Pakiety prywatne są używane przez klasę [środowiska](/python/api/azureml-core/azureml.core.environment.environment) . W środowisku należy zadeklarować pakiety języka Python, które mają być używane, w tym prywatne. Aby uzyskać ogólne informacje na temat środowiska w Azure Machine Learning, zobacz [jak korzystać z środowisk](how-to-use-environments.md). 

## <a name="prerequisites"></a>Wymagania wstępne

 * [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install)
 * [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>Korzystanie z niewielkiej liczby pakietów na potrzeby programowania i testowania

W przypadku niewielkiej liczby pakietów prywatnych dla jednego obszaru roboczego Użyj metody statycznej [`Environment.add_private_pip_wheel()`](/python/api/azureml-core/azureml.core.environment.environment#add-private-pip-wheel-workspace--file-path--exist-ok-false-) . Takie podejście umożliwia szybkie dodanie pakietu prywatnego do obszaru roboczego i jest odpowiednie dla celów deweloperskich i testowych.

Wskaż wartość argumentu ścieżka do pliku koła lokalnego i uruchom ```add_private_pip_wheel``` polecenie. Polecenie zwraca adres URL służący do śledzenia lokalizacji pakietu w obszarze roboczym. Przechwyć adres URL magazynu i przekaż go do `add_pip_package()` metody.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Wewnętrznie usługa Azure Machine Learning zastępuje adres URL za pomocą bezpiecznego adresu URL, więc plik kółka będzie prywatny i bezpieczny.

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Korzystanie z repozytorium pakietów z poziomu źródła danych Azure DevOps

Jeśli aktywnie opracowujesz pakiety języka Python dla aplikacji Machine Learning, możesz hostować je w repozytorium usługi Azure DevOps jako artefakty i publikować je jako źródło danych. Takie podejście umożliwia integrację przepływu pracy DevOps na potrzeby kompilowania pakietów przy użyciu Obszar roboczy usługi Azure Machine Learning. Aby dowiedzieć się, jak skonfigurować kanały informacyjne języka Python przy użyciu usługi Azure DevOps, przeczytaj artykuł Wprowadzenie [do pakietów języka Python w Azure Artifacts](/azure/devops/artifacts/quickstarts/python-packages?preserve-view=true&view=azure-devops)

To podejście używa osobistego tokenu dostępu do uwierzytelniania w repozytorium. Takie samo podejście ma zastosowanie do innych repozytoriów z uwierzytelnianiem opartym na tokenach, takich jak prywatne repozytoria GitHub. 

 1. [Utwórz osobisty token dostępu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?preserve-view=true&tabs=preview-page&view=azure-devops#create-a-pat) dla wystąpienia usługi Azure DevOps. Ustaw zakres tokenu do __spakowania > odczytać__. 

 2. Dodaj adres URL i DevOps platformy Azure jako właściwości obszaru roboczego przy użyciu metody [Workspace.set_connection](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-) .

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Utwórz środowisko Azure Machine Learning i Dodaj pakiety języka Python ze źródła danych.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

Środowisko jest teraz gotowe do użycia w ramach wdrożeń lub punktów końcowych usługi sieci Web. Podczas kompilowania środowiska Azure Machine Learning usługa używa źródła do uwierzytelniania w kanale informacyjnym przy użyciu zgodnego podstawowego adresu URL.

## <a name="use-a-repository-of-packages-from-private-storage"></a>Używanie repozytorium pakietów z magazynu prywatnego

Możesz korzystać z pakietów z konta usługi Azure Storage w ramach zapory w organizacji. Konto magazynu może zawierać nadzorowany zestaw pakietów lub wewnętrzny duplikat pakietów dostępnych publicznie.

Aby skonfigurować taki magazyn prywatny, zobacz temat [Zabezpieczanie obszaru roboczego Azure Machine Learning i skojarzonych zasobów](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints). Należy również [umieścić Azure Container Registry (ACR) za siecią wirtualną](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr).

> [!IMPORTANT]
> Należy wykonać ten krok, aby móc nauczyć lub wdrożyć modele przy użyciu repozytorium pakietu prywatnego.

Po zakończeniu tych konfiguracji można odwoływać się do pakietów w definicji środowiska Azure Machine Learning za pomocą pełnego adresu URL w usłudze Azure Blob Storage.

## <a name="next-steps"></a>Następne kroki

 * Dowiedz się więcej o [zabezpieczeniach korporacyjnych w Azure Machine Learning](concept-enterprise-security.md)