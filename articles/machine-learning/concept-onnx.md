---
title: 'ONNX: o wysokiej wydajności, wnioskowanie Międzyplatformowe'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą programu Open neuronowych Network Exchange (ONNX) można zoptymalizować wnioskowanie o modelu uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270175"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX i Azure Machine Learning: Tworzenie i przyspieszenie modeli ML

Dowiedz się, jak za pomocą programu [Open neuronowych Network Exchange](https://onnx.ai) (ONNX) można zoptymalizować wnioskowanie o modelu uczenia maszynowego. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej dotyczący danych produkcyjnych. 

Optymalizacja modeli uczenia maszynowego na potrzeby wnioskowania (lub oceniania modelu) jest trudna, ponieważ należy dostroić model i bibliotekę wnioskowania w celu zapewnienia większości możliwości sprzętowych. Problem jest niezwykle trudno, jeśli chcesz uzyskać optymalną wydajność na różnych typach platform (chmura/Edge, procesor CPU/GPU itp.), ponieważ każdy z nich ma różne możliwości i cechy. Złożoność zwiększa się, jeśli masz modele z różnych platform, które muszą działać na różnych platformach. Jest to bardzo czasochłonne, aby zoptymalizować wszystkie różne kombinacje platform i sprzętu. Konieczne jest rozwiązanie do uczenia się jednokrotnie w preferowanej strukturze i działające w dowolnym miejscu w chmurze lub na krawędzi. Jest to miejsce, w którym znajduje się ONNX.

Firma Microsoft i społeczność partnerów utworzyli ONNX jako otwarty standard do reprezentowania modeli uczenia maszynowego. Modele z [wielu platform](https://onnx.ai/supported-tools) , w tym TensorFlow, PyTorch, SciKit-nauka, Keras, łańcucher, MXNET i MATLAB, można wyeksportować lub przekonwertować do formatu standardowego ONNX. Gdy modele są w formacie ONNX, można je uruchamiać na różnych platformach i urządzeniach.

[Środowisko uruchomieniowe ONNX](https://github.com/Microsoft/onnxruntime) to aparat wnioskowania o wysokiej wydajności służący do wdrażania modeli ONNX w środowisku produkcyjnym. Jest zoptymalizowany pod kątem chmury i brzegowej oraz działa w systemach Linux, Windows i Mac. Zapisywana w języku C++, zawiera również interfejsy API języka C, Python i C#. Środowisko uruchomieniowe ONNX zapewnia obsługę wszystkich specyfikacji ONNX-ML i integruje się z akceleratorami na różnych urządzeniach, takich jak TensorRT na procesorach GPU NVidia.

Środowisko uruchomieniowe ONNX jest używane w wysokiej skali usług firmy Microsoft, takich jak Bing, Office i Cognitive Services. Zyski wydajności są zależne od wielu czynników, ale te usługi firmy Microsoft miały __średni wzrost wydajności procesora CPU__. Środowisko uruchomieniowe ONNX jest również używane jako część systemu Windows ML na setkach milionów urządzeń. Środowiska uruchomieniowego można użyć z Azure Machine Learning. Za pomocą środowiska uruchomieniowego ONNX można korzystać z zalet optymalizacji, testowania i ciągłego ulepszania klasy produkcyjnej.

[![Diagram przepływu ONNX przedstawiający szkolenia, konwertery i wdrażanie](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Pobierz modele ONNX

Modele ONNX można uzyskać na kilka sposobów:
+ Uczenie nowego modelu ONNX w Azure Machine Learning (Zobacz przykłady w dolnej części tego artykułu)
+ Konwertuj istniejący model z innego formatu na ONNX (zobacz [samouczki](https://github.com/onnx/tutorials)) 
+ Pobierz wstępnie szkolony model ONNX z [modelu ONNX zoo](https://github.com/onnx/models) (Zobacz przykłady w dolnej części tego artykułu)
+ Generuj dostosowany model ONNX z [usługi Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Wiele modeli, w tym Klasyfikacja obrazu, wykrywanie obiektów i przetwarzanie tekstu, można reprezentować jako modele ONNX. Jednak niektóre modele mogą nie być możliwe do pomyślnego przekonwertowania. W przypadku uruchomienia w tej sytuacji należy rozwiązać problem w witrynie GitHub odpowiedniego konwertera, który został użyty. Możesz kontynuować korzystanie z istniejącego modelu formatu do momentu rozwiązania problemu.

## <a name="deploy-onnx-models-in-azure"></a>Wdrażanie modeli ONNX na platformie Azure

Za pomocą Azure Machine Learning można wdrażać i monitorować modele ONNX oraz zarządzać nimi. Korzystając ze standardowego [przepływu pracy wdrażania](concept-model-management-and-deployment.md) i środowiska uruchomieniowego ONNX, można utworzyć punkt końcowy REST hostowany w chmurze. Zobacz przykładowe notesy Jupyter na końcu tego artykułu, aby wypróbować je samodzielnie. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalowanie i używanie środowiska uruchomieniowego ONNX z językiem Python

Pakiety Python dla środowiska uruchomieniowego ONNX są dostępne w [PyPi.org](https://pypi.org) ([procesor CPU](https://pypi.org/project/onnxruntime), [procesor GPU](https://pypi.org/project/onnxruntime-gpu)). Przed instalacją zapoznaj się z [wymaganiami systemowymi](https://github.com/Microsoft/onnxruntime#system-requirements) . 

 Aby zainstalować środowisko uruchomieniowe ONNX dla języka Python, użyj jednego z następujących poleceń: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Aby wywołać środowisko uruchomieniowe ONNX w skrypcie języka Python, użyj:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

Dokumentacja towarzysząca modelowi zwykle informuje dane wejściowe i wyjściowe dotyczące korzystania z modelu. Możesz również użyć narzędzia do wizualizacji, takiego jak [Netron](https://github.com/lutzroeder/Netron) , aby wyświetlić model. Środowisko uruchomieniowe ONNX umożliwia również wykonywanie zapytań dotyczących metadanych modelu, danych wejściowych i wyjść:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Aby wywnioskować model, użyj `run` i przekaż listę danych wyjściowych, które chcesz zwrócić (pozostaw puste, jeśli chcesz wszystkie), i mapę wartości wejściowych. Wynik jest listą danych wyjściowych.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Aby uzyskać pełną dokumentację interfejsu API języka Python, zobacz [dokumenty referencyjne środowiska uruchomieniowego ONNX](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Przykłady

Zapoznaj się z artykułem [How to-use-Azure/Deployment/Onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) na przykład notesów, które tworzą i wdrażają modele Onnx.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Więcej informacji

Dowiedz się więcej na temat ONNX lub współtworzenia projektu:
+ [Witryna sieci Web projektu ONNX](https://onnx.ai)
+ [Kod ONNX w serwisie GitHub](https://github.com/onnx/onnx)

Dowiedz się więcej na temat środowiska uruchomieniowego ONNX lub współtworzenia projektu:
+ [Repozytorium GitHub środowiska uruchomieniowego ONNX](https://github.com/Microsoft/onnxruntime)


