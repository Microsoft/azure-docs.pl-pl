---
title: Używanie prywatnych pakietów języka Python
titleSuffix: Azure Machine Learning
description: Bezpiecznie Uzyskuj dostęp do prywatnych pakietów języka Python ze środowisk Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 314f6a45bf688125e79f0b8ce0099a8326b339dc
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958154"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Używanie prywatnych pakietów języka Python z Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak bezpiecznie używać prywatnych pakietów języka Python w programie Azure Machine Learning. Przypadki użycia dla prywatnych pakietów języka Python obejmują:

 * Opracowano prywatny pakiet, który nie powinien być udostępniany publicznie.
 * Chcesz użyć zanadzorowanego repozytorium pakietów przechowywanych w zaporze przedsiębiorstwa.

Zalecane podejście zależy od tego, czy masz kilka pakietów dla jednego obszaru roboczego Azure Machine Learning, czy też całe repozytorium pakietów dla wszystkich obszarów roboczych w organizacji.

Pakiety prywatne są używane przez klasę [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) . W środowisku należy zadeklarować pakiety języka Python, które mają być używane, w tym prywatne. Aby uzyskać ogólne informacje na temat środowiska w Azure Machine Learning, zobacz [jak korzystać z środowisk](how-to-use-environments.md). 

## <a name="prerequisites"></a>Wymagania wstępne

 * [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>Korzystanie z niewielkiej liczby pakietów na potrzeby programowania i testowania

W przypadku niewielkiej liczby pakietów prywatnych dla jednego obszaru roboczego Użyj metody statycznej [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) . Takie podejście umożliwia szybkie dodanie pakietu prywatnego do obszaru roboczego i jest odpowiednie dla celów deweloperskich i testowych.

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

Jeśli aktywnie opracowujesz pakiety języka Python dla aplikacji Machine Learning, możesz hostować je w repozytorium usługi Azure DevOps jako artefakty i publikować je jako źródło danych. Takie podejście umożliwia integrację przepływu pracy DevOps na potrzeby kompilowania pakietów przy użyciu Obszar roboczy usługi Azure Machine Learning. Aby dowiedzieć się, jak skonfigurować kanały informacyjne języka Python przy użyciu usługi Azure DevOps, przeczytaj artykuł Wprowadzenie [do pakietów języka Python w Azure Artifacts](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops)

To podejście używa osobistego tokenu dostępu do uwierzytelniania w repozytorium. Takie samo podejście ma zastosowanie do innych repozytoriów z uwierzytelnianiem opartym na tokenach, takich jak prywatne repozytoria GitHub. 

 1. [Utwórz osobisty token dostępu](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) dla wystąpienia usługi Azure DevOps. Ustaw zakres tokenu do __spakowania > odczytać__. 

 2. Dodaj adres URL i DevOps platformy Azure jako właściwości obszaru roboczego przy użyciu metody [Workspace. set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-) .

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

Aby skonfigurować taki magazyn prywatny:

1. [Umieść obszar roboczy w sieci wirtualnej (VNET)](how-to-enable-virtual-network.md).
1. Utwórz konto magazynu i nie [Zezwalaj na dostęp publiczny](https://docs.microsoft.com/azure/storage/common/storage-network-security).
1. Umieść pakiety języka Python, które mają być używane w kontenerze na koncie magazynu 
1. [Zezwalaj na dostęp do konta magazynu z sieci wirtualnej obszaru roboczego](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)
1. [Umieść Azure Container Registry (ACR) dla obszaru roboczego za siecią wirtualną](how-to-enable-virtual-network.md#azure-container-registry).

    > [!IMPORTANT]
    > Należy wykonać ten krok, aby móc nauczyć lub wdrożyć modele przy użyciu repozytorium pakietu prywatnego.

Po zakończeniu tych konfiguracji można odwoływać się do pakietów w definicji środowiska Azure Machine Learning za pomocą pełnego adresu URL w usłudze Azure Blob Storage.

## <a name="next-steps"></a>Kolejne kroki

 * Dowiedz się więcej o [zabezpieczeniach korporacyjnych w Azure Machine Learning](concept-enterprise-security.md)
