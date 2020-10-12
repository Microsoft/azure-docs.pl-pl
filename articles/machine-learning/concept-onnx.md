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
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: acaab8aaa12a107f4d0f8a8aac0baf7d5ebb8e4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87012762"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX i Azure Machine Learning: Tworzenie i przyspieszenie modeli ML

Dowiedz się, jak za pomocą programu [Open neuronowych Network Exchange](https://onnx.ai) (ONNX) można zoptymalizować wnioskowanie o modelu uczenia maszynowego. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej dotyczący danych produkcyjnych. 

Optymalizacja modeli uczenia maszynowego na potrzeby wnioskowania (lub oceniania modelu) jest trudna, ponieważ należy dostroić model i bibliotekę wnioskowania w celu zapewnienia większości możliwości sprzętowych. Problem jest niezwykle trudno, jeśli chcesz uzyskać optymalną wydajność na różnych typach platform (chmura/Edge, procesor CPU/GPU itp.), ponieważ każdy z nich ma różne możliwości i cechy. Złożoność zwiększa się, jeśli masz modele z różnych platform, które muszą działać na różnych platformach. Jest to bardzo czasochłonne, aby zoptymalizować wszystkie różne kombinacje platform i sprzętu. Konieczne jest rozwiązanie do uczenia się jednokrotnie w preferowanej strukturze i działające w dowolnym miejscu w chmurze lub na krawędzi. Jest to miejsce, w którym znajduje się ONNX.

Firma Microsoft i społeczność partnerów utworzyli ONNX jako otwarty standard do reprezentowania modeli uczenia maszynowego. Modele z [wielu platform](https://onnx.ai/supported-tools) , w tym TensorFlow, PyTorch, SciKit-nauka, Keras, łańcucher, MXNET, MATLAB i SparkML, można wyeksportować lub przekonwertować do formatu standardowego ONNX. Gdy modele są w formacie ONNX, można je uruchamiać na różnych platformach i urządzeniach.

[Środowisko uruchomieniowe ONNX](https://onnxruntime.ai) to aparat wnioskowania o wysokiej wydajności służący do wdrażania modeli ONNX w środowisku produkcyjnym. Jest zoptymalizowany pod kątem chmury i brzegowej oraz działa w systemach Linux, Windows i Mac. Pisanie w języku C++ zawiera również interfejsy API języka C, Python, C#, Java i JavaScript (Node.js) do użycia w różnych środowiskach. Środowisko uruchomieniowe ONNX obsługuje zarówno DNN, jak i tradycyjne modele ML oraz integruje się z akceleratorami na różnych urządzeniach, takich jak TensorRT w procesorach GPU NVidia, OpenVINO na procesorach Intel, DirectML w systemie Windows i innych. Za pomocą środowiska uruchomieniowego ONNX można korzystać z zalet optymalizacji, testowania i ciągłego ulepszania klasy produkcyjnej.

Środowisko uruchomieniowe ONNX jest używane w usługach firmy Microsoft o dużej skali, takich jak Bing, Office i Azure Cognitive Services. Zyski wydajności są zależne od wielu czynników, ale te usługi firmy Microsoft miały średni wzrost __wydajności procesora CPU__. Oprócz usług Azure Machine Learning środowisko uruchomieniowe ONNX działa również w innych produktach, które obsługują Machine Learning obciążeń, w tym:
+ System Windows: środowisko uruchomieniowe jest wbudowane w system Windows w ramach [systemu windows Machine Learning](https://docs.microsoft.com/windows/ai/windows-ml/) i działa na setkach milionów urządzeń. 
+ Rodzina produktów SQL Azure: uruchamianie natywnej oceny danych w [usłudze Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/onnx-overview) i [wystąpieniu zarządzanym Azure SQL](https://docs.microsoft.com/azure/azure-sql/managed-instance/machine-learning-services-overview).
+ ML.NET: [Uruchom modele ONNX w ml.NET](https://docs.microsoft.com/dotnet/machine-learning/tutorials/object-detection-onnx).


[![Diagram przepływu ONNX przedstawiający szkolenia, konwertery i wdrażanie](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Pobierz modele ONNX

Modele ONNX można uzyskać na kilka sposobów:
+ Uczenie nowego modelu ONNX w Azure Machine Learning (Zobacz przykłady w dolnej części tego artykułu) lub przy użyciu [funkcji automatycznych Machine Learning](concept-automated-ml.md#automl--onnx)
+ Konwertuj istniejący model z innego formatu na ONNX (zobacz [samouczki](https://github.com/onnx/tutorials)) 
+ Pobierz wstępnie szkolony model ONNX z [modelu ONNX zoo](https://github.com/onnx/models)
+ Generuj dostosowany model ONNX z [usługi Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Wiele modeli, w tym Klasyfikacja obrazu, wykrywanie obiektów i przetwarzanie tekstu, można reprezentować jako modele ONNX. W przypadku wystąpienia problemu z modelem, którego nie można pomyślnie przekonwertować, należy rozwiązać problem w usłudze GitHub odpowiedniego konwertera, który został użyty. Możesz kontynuować korzystanie z istniejącego modelu formatu do momentu rozwiązania problemu.

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
Zapoznaj się z artykułem [How to-use-Azure/Deployment/Onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) na przykład notesów Python, które tworzą i wdrażają modele Onnx.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

Przykłady użycia w innych językach można znaleźć w witrynie [GitHub środowiska uruchomieniowego ONNX](https://github.com/microsoft/onnxruntime/tree/master/samples).

## <a name="more-info"></a>Więcej informacji

Dowiedz się więcej na temat **ONNX** lub współtworzenia projektu:
+ [Witryna sieci Web projektu ONNX](https://onnx.ai)
+ [Kod ONNX w serwisie GitHub](https://github.com/onnx/onnx)

Dowiedz się więcej na temat **środowiska uruchomieniowego ONNX** lub współtworzenia projektu:
+ [Witryna sieci Web projektu środowiska uruchomieniowego ONNX](https://onnxruntime.ai)
+ [Repozytorium GitHub środowiska uruchomieniowego ONNX](https://github.com/Microsoft/onnxruntime)


