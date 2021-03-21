---
title: Wdróż modele ML do FPGA
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat tablic bram opartych na programowalnych polach. Usługę sieci Web można wdrożyć na FPGA z Azure Machine Learning w przypadku wystąpienia bardzo małych opóźnień.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, devx-track-python, deploy
ms.openlocfilehash: e6a58a6555602af2494683037721a1f83e7ea33c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519320"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Wdrażanie modeli ML do opartych na programowalnych tablic bram (FPGA) z Azure Machine Learning 

Ten artykuł zawiera informacje na temat FPGA i sposobu wdrażania modeli ML na platformie Azure FPGA przy użyciu [pakietu języka Python z przyspieszeniem sprzętowym](/python/api/azureml-accel-models/azureml.accel) z [Azure Machine Learning](overview-what-is-azure-ml.md).

## <a name="what-are-fpgas"></a>Co to są FPGA?
Układy FPGA zawierają tablicę programowalnych bloków logicznych i hierarchię możliwych do ponownego skonfigurowania wzajemnych połączeń. Połączenia międzyłączne umożliwiają skonfigurowanie tych bloków na różne sposoby po zakończeniu produkcji. W porównaniu z innymi mikroukładami FPGA zapewniają kombinację programowania i wydajności. 

FPGA umożliwiają osiągnięcie małych opóźnień dla żądań wnioskowania w czasie rzeczywistym (lub oceniania modeli). Żądania asynchroniczne (wsadowe) nie są potrzebne. Przetwarzanie wsadowe może spowodować opóźnienia, ponieważ trzeba przetworzyć więcej danych. Implementacje jednostek przetwarzania neuronowych nie wymagają przetwarzania wsadowego; w związku z tym opóźnienie może być znacznie mniejsze, w porównaniu z procesorami CPU i procesora GPU.

Można ponownie skonfigurować FPGA dla różnych typów modeli uczenia maszynowego. Ta elastyczność ułatwia przyspieszenie aplikacji w oparciu o najbardziej optymalną precyzję liczbową i używany model pamięci. Ze względu na to, że FPGA są modyfikowane, można zachować aktualność z wymaganiami natychmiastowej zmiany algorytmów AI.

![Diagram Azure Machine Learning porównania FPGA](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Procesor| Skrót |Opis|
|---|:-------:|------|
|Zintegrowane obwody specyficzne dla aplikacji|ASICs|Obwody niestandardowe, takie jak jednostki procesora (TPU) firmy Google, zapewniają najwyższą wydajność. Nie można ich ponownie skonfigurować zgodnie z potrzebami zmian.|
|Programowalne tablice bram|Układy FPGA|FPGA, takie jak te dostępne na platformie Azure, zapewniają wydajność zbliżoną do ASICs. Są one również elastyczne i można je skonfigurować w miarę upływu czasu w celu zaimplementowania nowej logiki.|
|Jednostki przetwarzania grafiki|Procesory GPU|Popularny wybór dla obliczeń AI. Procesory GPU oferują równoległe możliwości przetwarzania, przyspieszając renderowanie obrazów niż procesory CPU.|
|Jednostki przetwarzania centralnego|Procesory CPU|Procesor ogólnego przeznaczenia, wydajność, która nie jest idealnym rozwiązaniem w przypadku przetwarzania grafiki i wideo.|

## <a name="fpga-support-in-azure"></a>Obsługa FPGA na platformie Azure

Microsoft Azure to największe inwestycje chmury w FPGA. Firma Microsoft używa usługi FPGA do oceny usługi głębokiej sieci neuronowych (DNN), klasyfikacji wyszukiwania Bing i przyspieszenia sieci zdefiniowanej przez oprogramowanie (SDN), aby ograniczyć opóźnienia, a jednocześnie zwalniając procesory dla innych zadań.

FPGA na platformie Azure są oparte na urządzeniach FPGA firmy Intel, których analityki danych i Deweloperzy używają do przyspieszenia obliczeń w czasie rzeczywistym. Ta architektura z obsługą FPGA oferuje wydajność, elastyczność i skalowalność oraz jest dostępna na platformie Azure.

Usługa Azure FPGA jest zintegrowana z usługą Azure Machine Learning. Platforma Azure może zrównoleglanie wstępnie przeszkolony DNN w firmie FPGA w celu skalowania w poziomie usługi. DNNsy mogą być wstępnie nauczone, jako głębokie featurized do uczenia w zakresie transferu, lub precyzyjnie dopasowane do zaktualizowanych wag.

|Scenariusze & konfiguracje na platformie Azure|Obsługiwane modele DNN|Wsparcie regionalne|
|--------------------------|--------------------|----------------|
|+ Scenariusze klasyfikacji i rozpoznawania obrazów<br/>+ Wdrożenie TensorFlow (wymaga Tensorflow 1. x)<br/>+ Sprzęt Intel FPGA|-ResNet 50<br/>-ResNet 152<br/>-DenseNet-121<br/>-VGG-16<br/>-SSD-VGG|— Wschodnie stany USA<br/>— Azja Południowo-Wschodnia<br/>— Europa Zachodnia<br/>— Zachodnie stany USA 2|

Aby zoptymalizować opóźnienia i przepływność, klient wysyłający dane do modelu FPGA powinien znajdować się w jednym z powyższych regionów (na którym został wdrożony model).

**Rodzina PBS maszyn wirtualnych platformy Azure** zawiera technologię Intel Arria 10 FPGA. Zostanie on wyświetlony jako "standardowa rodzina PBS procesorów wirtualnych vCPU" podczas sprawdzania przydziału przydziału platformy Azure. Maszyna wirtualna PB6 ma sześć procesorów wirtualnych vCPU i jeden FPGA. Maszyna wirtualna w PB6 jest automatycznie obsługiwana przez Azure Machine Learning podczas wdrażania modelu w FPGA. Jest ona używana tylko z platformą Azure ML i nie może uruchamiać dowolnego bitstreams. Na przykład nie będzie można wykonać czynności Flash FPGA z bitstreams do szyfrowania, kodowania itp.

## <a name="deploy-models-on-fpgas"></a>Wdróż modele na FPGA

Model można wdrożyć jako usługę sieci Web na FPGA z [Modele z przyspieszaniem sprzętowym Azure Machine Learning](/python/api/azureml-accel-models/azureml.accel). Korzystanie z usługi FPGA zapewnia bardzo małe ilości opóźnienia, nawet w przypadku pojedynczego rozmiaru partii. 

W tym przykładzie utworzysz Graf TensorFlow na potrzeby wstępnego przetwarzania obrazu wejściowego, ustaw go jako featurized przy użyciu ResNet 50 na FPGA, a następnie uruchom funkcje za pośrednictwem klasyfikatora przeszkolonego na zestawie danych ImageNet. Następnie model jest wdrażany w klastrze AKS.

### <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli go nie masz, Utwórz konto z [płatnością zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) użyciem (bezpłatne konta platformy Azure nie kwalifikują się do przydziału FPGA).

- Azure Machine Learning obszar roboczy i zestaw Azure Machine Learning SDK dla języka Python, zgodnie z opisem w temacie [Tworzenie obszaru roboczego](how-to-manage-workspace.md).
 
- Pakiet modeli przyspieszanych sprzętowo:  `pip install --upgrade azureml-accel-models[cpu]`    
    
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

- Limit przydziału FPGA. Prześlij żądanie przydzielenia [limitu przydziału](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)lub Uruchom to polecenie interfejsu wiersza polecenia, aby sprawdzić limit przydziału: 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   Upewnij się, że masz co najmniej 6 procesorów wirtualnych vCPU pod zwróconym __CurrentValue__ .  

### <a name="define-the-tensorflow-model"></a>Zdefiniuj model TensorFlow

Aby utworzyć definicję usługi, Zacznij od użycia [zestawu SDK Azure Machine Learning dla języka Python](/python/api/overview/azure/ml/intro) . Definicja usługi jest plikiem opisującym potok grafów (Input, featurized i klasyfikator) oparty na TensorFlow. Polecenie wdrażania kompresuje definicje i wykresy do pliku ZIP i przekazuje je do usługi Azure Blob Storage. DNN jest już wdrożony do uruchamiania na FPGA.

1. Załaduj obszar roboczy Azure Machine Learning

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Obraz wstępnie przetworzony. Wejście do usługi sieci Web jest obrazem JPEG.  Pierwszym krokiem jest Dekodowanie obrazu JPEG i jego wstępne przetworzenie.  Obrazy JPEG są traktowane jako ciągi, a wynikiem są dwuplikowe dane wejściowe modelu ResNet 50.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Ładuj featurized. Zainicjuj model i Pobierz punkt kontrolny TensorFlow wersji ResNet50, który będzie używany jako featurized.  Zastąp ciąg "QuantizedResnet50" w fragmencie kodu, aby zaimportować inne głębokie sieci neuronowych:

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. Dodaj klasyfikator. Ten klasyfikator został przeszkolony na zestawie danych ImageNet.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Zapisz model. Teraz, gdy preprocesor, ResNet 50 featurized i klasyfikator został załadowany, Zapisz wykres i skojarzone zmienne jako model.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. Zapisz natężenia danych wejściowych i wyjściowych **, ponieważ będą one używane do konwersji modeli i żądań wnioskowania**. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   Następujące modele są dostępne z licznikami wyjściowymi klasyfikatora dla wnioskowania, jeśli użyto klasyfikatora domyślnego.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>Konwertuj model na otwarty format neuronowych Network Exchange (ONNX)

Aby można było wdrożyć program w programie FPGA, należy przekonwertować model do formatu [ONNX](https://onnx.ai/) .

1. [Zarejestruj](concept-model-management-and-deployment.md) model przy użyciu zestawu SDK z plikiem zip w usłudze Azure Blob Storage. Dodawanie tagów i innych metadanych dotyczących modelu ułatwia śledzenie przeszkolonych modeli.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Jeśli model został już zarejestrowany i chcesz go załadować, możesz go pobrać.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Przekonwertuj wykres TensorFlow na format ONNX.  Należy podać nazwy natężeń wejściowych i wyjściowych, aby klient mógł ich używać podczas korzystania z usługi sieci Web.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

### <a name="containerize-and-deploy-the-model"></a>Konteneryzowanie i Wdróż model

Następnie Utwórz obraz platformy Docker z konwertowanego modelu i wszystkich zależności.  Ten obraz platformy Docker można następnie wdrożyć i utworzyć jego wystąpienie.  Obsługiwane cele wdrożenia obejmują usługę Azure Kubernetes Service (AKS) w chmurze lub urządzeniem brzegowym, takim jak [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md).  Możesz również dodać tagi i opisy dla zarejestrowanego obrazu platformy Docker.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   Wyświetl listę obrazów według tagów i uzyskaj szczegółowe dzienniki dla dowolnego debugowania.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Wdrażanie w klastrze usługi Azure Kubernetes Service

1. Aby wdrożyć model jako usługę sieci Web o dużej skali produkcyjnej, użyj AKS. Nowy element można utworzyć przy użyciu zestawu SDK Azure Machine Learning, interfejsu wiersza polecenia lub [Azure Machine Learning Studio](https://ml.azure.com).

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    Wdrożenie AKS może potrwać około 15 minut.  Sprawdź, czy wdrożenie zakończyło się pomyślnie.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Wdróż kontener w klastrze AKS.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

#### <a name="deploy-to-a-local-edge-server"></a>Wdrażanie na lokalnym serwerze brzegowym

Wszystkie [urządzenia Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md
) zawierają FPGA do uruchamiania modelu.  Tylko jeden model może być uruchomiony w FPGA jednocześnie.  Aby uruchomić inny model, po prostu wdróż nowy kontener. Instrukcje i przykładowy kod można znaleźć w [tym przykładzie platformy Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

### <a name="consume-the-deployed-model"></a>Korzystanie ze wdrożonego modelu

Na koniec użyj przykładowego klienta do wywołania obrazu platformy Docker, aby uzyskać prognozy z modelu.  Kod przykładowego klienta jest dostępny:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Obraz platformy Docker obsługuje gRPC i TensorFlow obsługujący interfejs API "przewidywania".

Możesz również pobrać przykładowego klienta do obsługi TensorFlow.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Ponieważ ten klasyfikator został przeszkolony w zestawie danych [ImageNet](http://www.image-net.org/) , Mapuj klasy na etykiety czytelne dla człowieka.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby uniknąć niepotrzebnych kosztów, wyczyść zasoby **w następującej kolejności**: usługa sieci Web, następnie obraz, a następnie model.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się, jak [zabezpieczyć dokument usług sieci Web](how-to-secure-web-service.md) .

+ Dowiedz się więcej na temat [cen i FPGA Azure Machine Learning i kosztów](https://azure.microsoft.com/pricing/details/machine-learning/).

+ [Sprzęt ze skalowaniem: ML na dużą skalę na platformie Azure i FPGA: kompilacja 2018 (wideo)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Usługa FPGA w chmurze (wideo) oparta na programie Microsoft](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave dla programu AI w czasie rzeczywistym](https://www.microsoft.com/research/project/project-brainwave/)

+ [Zautomatyzowany system kontroli optycznej](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
