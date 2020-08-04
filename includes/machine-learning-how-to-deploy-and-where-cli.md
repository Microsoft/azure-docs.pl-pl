---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542805"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).
- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).
- [Rozszerzenie interfejsu wiersza polecenia (CLI) platformy Azure dla usługi Machine Learning](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

Postępuj zgodnie z instrukcjami w dokumentacji interfejsu wiersza polecenia platformy Azure, aby [ustawić kontekst subskrypcji](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Następnie wykonaj następujące czynności:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

Aby wyświetlić obszary robocze, do których masz dostęp.

## <a name="register-your-model"></a><a id="registermodel"></a>Zarejestruj model

Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym. Po zarejestrowaniu plików można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP]
> Po zarejestrowaniu modelu podajesz ścieżkę do lokalizacji w chmurze (z przebiegu szkoleniowego) lub katalogu lokalnego. Ta ścieżka służy tylko do lokalizowania plików do przekazania w ramach procesu rejestracji. Nie musi być zgodna ze ścieżką używaną w skrypcie wprowadzania. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wprowadzania](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Modele uczenia maszynowego są rejestrowane w obszarze roboczym Azure Machine Learning. Model może pochodzić z Azure Machine Learning lub w innym miejscu. Podczas rejestrowania modelu można opcjonalnie dostarczyć metadane dotyczące modelu. `tags` `properties` Do filtrowania modeli można używać słowników i, które są stosowane do rejestracji modelu.

W poniższych przykładach pokazano, jak zarejestrować model.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Zarejestruj model na podstawie przebiegu szkolenia z usługi Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

`--asset-path`Parametr odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka pojedynczego pliku. Aby uwzględnić w rejestracji modelu wiele plików, ustaw `--asset-path` ścieżkę folderu zawierającego pliki.

### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Aby uwzględnić w rejestracji modelu wiele plików, ustaw `-p` ścieżkę folderu zawierającego pliki.

Aby uzyskać więcej informacji na temat `az ml model register` , zapoznaj się z [dokumentacją referencyjną](/cli/azure/ext/azure-cli-ml/ml/model).

## <a name="define-an-entry-script"></a>Zdefiniuj skrypt wpisu

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

Następujące polecenie pokazuje, jak wdrożyć model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

W tym przykładzie konfiguracja określa następujące ustawienia:

* Że model wymaga języka Python
* [Skrypt wejścia](#define-an-entry-script), który jest używany do obsługi żądań sieci Web wysyłanych do wdrożonej usługi
* Plik Conda opisujący pakiety języka Python, które są zbędne do wnioskowania

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](../articles/machine-learning/how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Wybierz element docelowy obliczeń

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Zdefiniuj konfigurację wdrożenia

Opcje dostępne dla konfiguracji wdrożenia różnią się w zależności od wybranego elementu docelowego obliczeń.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

Więcej informacji można znaleźć w dokumentacji [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

## <a name="deploy-your-model"></a>Wdrażanie modelu

Teraz można przystąpić do wdrażania modelu.

### <a name="using-a-registered-model"></a>Korzystanie z zarejestrowanego modelu

Jeśli model został zarejestrowany w obszarze roboczym Azure Machine Learning, Zastąp ciąg "webmodeling: 1" nazwą Twojego modelu i numerem wersji.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Korzystanie z modelu lokalnego

Jeśli wolisz nie rejestrować modelu, możesz przekazać parametr "sourceDirectory" w inferenceconfig.js, aby określić katalog lokalny, który ma być obsługiwany przez model.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>Usuwanie zasobów

Aby usunąć wdrożoną usługę sieci Web, użyj programu `az ml service <name of webservice>` .

Aby usunąć zarejestrowany model z obszaru roboczego, użyj`az ml model delete <model id>`

Przeczytaj więcej [na temat usuwania usługi sieci Web](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) i [usuwania modelu](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)