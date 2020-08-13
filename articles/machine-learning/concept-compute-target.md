---
title: Co to są cele obliczeniowe
titleSuffix: Azure Machine Learning
description: Zdefiniuj, gdzie chcesz nauczyć lub wdrożyć model przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: 27c129af9fbf3e76c6c57fbf084596876b51955b
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141929"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Co to są cele obliczeniowe w Azure Machine Learning? 

**Obiekt docelowy obliczeń** to wyznaczono zasób obliczeniowy/środowisko, w którym jest uruchamiany skrypt szkoleniowy lub hostowanie wdrożenia usługi. Ta lokalizacja może być maszyną lokalną lub zasobem obliczeniowym opartym na chmurze. Użycie obiektów docelowych obliczeń ułatwia późniejsze zmiany środowiska obliczeniowego bez konieczności zmiany kodu.  

W typowym cyklu projektowania modelu można:
1. Zacznij od tworzenia i eksperymentowania w niewielkiej ilości danych. Na tym etapie zalecamy środowisko lokalne (komputer lokalny lub maszyna wirtualna oparta na chmurze) jako obiekt docelowy obliczeń. 
2. Skaluj w górę do większej ilości danych lub przekształcenie rozproszone przy użyciu jednego z tych [obiektów docelowych obliczeń szkoleniowych](#train).  
3. Gdy model będzie gotowy, wdróż go w środowisku hostingu internetowego lub na urządzeniu IoT przy użyciu jednego z tych [celów obliczeniowych wdrożenia](#deploy).

Zasoby obliczeniowe używane dla obiektów docelowych obliczeń są dołączone do [obszaru roboczego](concept-workspace.md). Zasoby obliczeniowe inne niż maszyna lokalna są współużytkowane przez użytkowników obszaru roboczego.

## <a name="training-compute-targets"></a><a name="train"></a>Szkoleniowe cele obliczeniowe

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych.  Możesz również dołączyć własny zasób obliczeniowy, chociaż obsługa różnych scenariuszy może się różnić.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Dowiedz się więcej o [konfigurowaniu elementu docelowego obliczeń i korzystaniu z niego do uczenia modelu](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Cele wdrożenia

Następujące zasoby obliczeniowe mogą służyć do hostowania wdrożenia modelu.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Dowiedz się [, gdzie i jak wdrożyć model w miejscu docelowym obliczeń](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning COMPUTE (zarządzane)

Zarządzany zasób obliczeniowy jest tworzony i zarządzany przez Azure Machine Learning. To obliczenie jest zoptymalizowane pod kątem obciążeń uczenia maszynowego. Azure Machine Learning klastrami obliczeniowymi i [wystąpieniami obliczeniowymi](concept-compute-instance.md) są jedyne zarządzane obliczenia. W przyszłości mogą zostać dodane dodatkowe zarządzane zasoby obliczeniowe.

Można tworzyć Azure Machine Learning wystąpienia obliczeniowe lub Klastry obliczeniowe z:
* Studio uczenia maszynowego Azure
* Witryna Azure Portal
* Klasy [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) i [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) zestawu SDK języka Python
* [Zestaw SDK języka R](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (wersja zapoznawcza)
* Szablon Menedżer zasobów. Aby zapoznać się z przykładowym szablonem, zobacz [create Azure Machine Learning COMPUTE Template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Rozszerzenie uczenia maszynowego [dla interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md#resource-management).  

Po utworzeniu te zasoby obliczeniowe są automatycznie częścią obszaru roboczego, w przeciwieństwie do innych rodzajów obiektów docelowych obliczeń.


|Możliwość  |Klaster obliczeniowy  |Wystąpienie obliczeniowe  |
|---------|---------|---------|
|Klaster z jednym lub wiele węzłów     |    **&check;**       |         |
|Skalowanie automatyczne przy każdym przesyłaniu przebiegu     |     **&check;**      |         |
|Automatyczne zarządzanie klastrami i planowanie zadań     |   **&check;**        |     **&check;**      |
|Obsługa zasobów procesora CPU i procesora GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Gdy klaster obliczeniowy jest bezczynny, automatycznie skaluje się do 0 węzłów, więc nie płacisz, gdy nie jest używany.  Jednak *wystąpienie*obliczeniowe jest zawsze włączone i nie Skalowanie automatyczne.  [Wystąpienie obliczeniowe należy zatrzymać,](tutorial-1st-experiment-sdk-train.md#stop-the-compute-instance) gdy nie jest używane, aby uniknąć dodatkowych kosztów.

### <a name="supported-vm-series-and-sizes"></a>Obsługiwane serie maszyn wirtualnych i rozmiary

Po wybraniu rozmiaru węzła dla zarządzanego zasobu obliczeniowego w Azure Machine Learning można wybierać spośród spośród wybranych rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje szeroką gamę rozmiarów dla systemów Linux i Windows dla różnych obciążeń. Zapoznaj się z tym artykułem, aby dowiedzieć się więcej o różnych [typach i rozmiarach maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

Istnieje kilka wyjątków i ograniczeń umożliwiających wybranie rozmiaru maszyny wirtualnej:
* Niektóre serie maszyn wirtualnych nie są obsługiwane w Azure Machine Learning.
* Niektóre serie maszyn wirtualnych są ograniczone. Aby skorzystać z serii z ograniczeniami, skontaktuj się z pomocą techniczną i poproś o zwiększenie limitu przydziału dla serii. Aby uzyskać informacje na temat kontaktowania się z pomocą techniczną, zobacz [Opcje pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)

Zapoznaj się z poniższą tabelą, aby dowiedzieć się więcej na temat obsługiwanych serii i ograniczeń. 

| **Obsługiwana seria maszyn wirtualnych**  | **Ograniczenia** |
|------------|------------|
| D | Brak |
| Dv2 | Brak |  
| DSv2 | Brak |  
| FSv2 | Brak | 
| HBv2 | Wymaga zatwierdzenia |  
| MAGAZYNU HCS | Wymaga zatwierdzenia |  
| M | Wymaga zatwierdzenia |
| NC | Brak |    
| NCsv2 | Wymaga zatwierdzenia |
| NCsv3 | Wymaga zatwierdzenia |  
| NDs | Wymaga zatwierdzenia |
| NDv2 | Wymaga zatwierdzenia |
| NV | Brak |
| NVv3 | Wymaga zatwierdzenia | 


Mimo że Azure Machine Learning obsługuje te serie maszyn wirtualnych, mogą one nie być dostępne we wszystkich regionach świadczenia usługi Azure. Serie maszyn wirtualnych można sprawdzić tutaj: dostępne [produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Obliczenia niezarządzane

Niezarządzany cel obliczeń *nie* jest zarządzany przez Azure Machine Learning. Ten typ elementu docelowego obliczeń można utworzyć poza Azure Machine Learning, a następnie dołączyć go do obszaru roboczego. Niezarządzane zasoby obliczeniowe mogą wymagać dodatkowych czynności, aby zachować lub zwiększyć wydajność obciążeń uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Instrukcje:
* [Skonfiguruj cel obliczeń do uczenia modelu](how-to-set-up-training-targets.md)
* [Wdrażanie modelu w obiekcie docelowym obliczeń](how-to-deploy-and-where.md)
