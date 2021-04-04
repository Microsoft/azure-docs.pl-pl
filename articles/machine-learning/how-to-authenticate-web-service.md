---
title: Konfigurowanie uwierzytelniania dla modeli wdrożonych jako usługi sieci Web
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować uwierzytelnianie dla modeli uczenia maszynowego wdrożonych w usługach sieci Web w Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94447777"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Konfigurowanie uwierzytelniania dla modeli wdrożonych jako usługi sieci Web

Azure Machine Learning pozwala na wdrożenie przeszkolonych modeli uczenia maszynowego jako usług sieci Web. W tym artykule dowiesz się, jak skonfigurować uwierzytelnianie dla tych wdrożeń.

Wdrożenia modelu utworzone przez Azure Machine Learning można skonfigurować tak, aby korzystały z jednej z dwóch metod uwierzytelniania:

* **oparte na kluczach**: klucz statyczny jest używany do uwierzytelniania w usłudze sieci Web.
* **oparta na tokenach**: tymczasowy token musi być uzyskany z obszaru roboczego Azure Machine Learning (przy użyciu Azure Active Directory) i używany do uwierzytelniania w usłudze sieci Web. Ten token wygasa po upływie okresu czasu i musi zostać odświeżony, aby kontynuować pracę z usługą sieci Web.

    > [!NOTE]
    > Uwierzytelnianie oparte na tokenach jest dostępne tylko w przypadku wdrażania w usłudze Azure Kubernetes Service.

## <a name="key-based-authentication"></a>Uwierzytelnianie oparte na kluczach

Usługi sieci Web wdrożone w usłudze Azure Kubernetes Service (AKS) mają domyślnie *włączone* uwierzytelnianie oparte na kluczach.

W przypadku wdrożonych usług Azure Container Instances (ACI) uwierzytelnianie oparte na kluczach jest domyślnie *wyłączone* , ale można je włączyć przy użyciu ustawienia `auth_enabled=True` podczas tworzenia usługi sieci Web ACI. Poniższy kod stanowi przykład tworzenia konfiguracji wdrożenia ACI z włączoną funkcją uwierzytelniania opartego na kluczach.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Następnie można użyć konfiguracji niestandardowej ACI we wdrożeniu przy użyciu `Model` klasy.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Aby pobrać klucze uwierzytelniania, użyj `aci_service.get_keys()` . Aby ponownie wygenerować klucz, użyj `regen_key()` funkcji i przekaż wartość **podstawową** lub **pomocniczą**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Uwierzytelnianie oparte na tokenach

Po włączeniu uwierzytelniania tokenów dla usługi sieci Web użytkownicy muszą przedstawić Azure Machine Learning token sieci Web JSON dla usługi sieci Web, aby uzyskać do niej dostęp. Token wygasa po określonym czasie i musi zostać odświeżony w celu dalszego wykonywania wywołań.

* Uwierzytelnianie tokenu jest **domyślnie wyłączone** w przypadku wdrażania w usłudze Azure Kubernetes Service.
* Uwierzytelnianie tokenu **nie jest obsługiwane** w przypadku wdrażania programu w celu Azure Container Instances.
* Uwierzytelnianie tokenu **nie może być używane w tym samym czasie, co uwierzytelnianie oparte na kluczach**.

Aby kontrolować uwierzytelnianie tokenu, użyj `token_auth_enabled` parametru podczas tworzenia lub aktualizowania wdrożenia:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Jeśli jest włączone uwierzytelnianie tokenu, można użyć metody, `get_token` Aby pobrać token sieci Web JSON (JWT) i czas wygaśnięcia tego tokenu:

> [!TIP]
> Jeśli używasz nazwy głównej usługi do pobrania tokenu i chcesz, aby miał minimalny wymagany dostęp do pobierania tokenu, przypisz go do roli **czytelnik** dla obszaru roboczego.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Będzie konieczne zażądanie nowego tokenu po upłynięciu czasu odświeżania `refresh_by` tokenu. Aby odświeżyć tokeny poza zestawem SDK języka Python, jedną z opcji jest użycie interfejsu API REST z uwierzytelnianiem podmiotu zabezpieczeń usługi w celu okresowego wykonania `service.get_token()` wywołania, jak opisano wcześniej.
>
> Zdecydowanie zalecamy utworzenie obszaru roboczego Azure Machine Learning w tym samym regionie, w którym znajduje się klaster usługi Azure Kubernetes.
>
> W celu uwierzytelnienia przy użyciu tokenu usługa sieci Web wykona wywołanie do regionu, w którym jest tworzony obszar roboczy Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci Web, nawet jeśli klaster znajduje się w innym regionie niż obszar roboczy. W efekcie uwierzytelnianie usługi Azure AD jest niedostępne, dopóki region obszaru roboczego nie będzie dostępny ponownie.
>
> Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej potrwa pobieranie tokenu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat uwierzytelniania do wdrożonego modelu, zobacz [Tworzenie klienta dla modelu wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).