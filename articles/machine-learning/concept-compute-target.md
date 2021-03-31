---
title: Co to są cele obliczeniowe
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyznaczyć zasób lub środowisko obliczeniowe, aby szkolić lub wdrożyć model przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: b4422c664071087ccae73a5b6f642b53dcca74bf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574649"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Co to są docelowe obiekty obliczeniowe w usłudze Azure Machine Learning?

*Obiekt docelowy obliczeń* jest wydzielonym zasobem obliczeniowym lub środowiskiem, w którym jest uruchamiany skrypt szkoleniowy lub hostowanie wdrożenia usługi. Ta lokalizacja może być maszyną lokalną lub zasobem obliczeniowym opartym na chmurze. Użycie obiektów docelowych obliczeń ułatwia późniejsze zmiany środowiska obliczeniowego bez konieczności zmiany kodu.

W typowym cyklu projektowania modelu można:

1. Zacznij od tworzenia i eksperymentowania w niewielkiej ilości danych. Na tym etapie Użyj środowiska lokalnego, takiego jak komputer lokalny lub maszyna wirtualna oparta na chmurze (VM), jako obiekt docelowy obliczeń.
1. Skaluj w górę do większej ilości danych lub przekształcenie rozproszone przy użyciu jednego z tych [obiektów docelowych obliczeń szkoleniowych](#train).
1. Po przygotowaniu modelu Wdróż go w środowisku hostingu internetowego lub na urządzeniu IoT przy użyciu jednego z tych [celów obliczeniowych wdrożenia](#deploy).

Zasoby obliczeniowe używane dla obiektów docelowych obliczeń są dołączone do [obszaru roboczego](concept-workspace.md). Zasoby obliczeniowe inne niż maszyna lokalna są współużytkowane przez użytkowników obszaru roboczego.

## <a name="training-compute-targets"></a><a name="train"></a> Szkoleniowe cele obliczeniowe

Azure Machine Learning ma różne wsparcie dla różnych obiektów docelowych obliczeń. Typowy cykl projektowania modelu rozpoczyna się od tworzenia i eksperymentowania w niewielkiej ilości danych. Na tym etapie należy używać środowiska lokalnego, takiego jak komputer lokalny lub maszyna wirtualna oparta na chmurze. Podczas skalowania w górę szkolenia w większych zestawach danych lub wykonywania szkoleń rozproszonych, użyj Azure Machine Learning obliczeń, aby utworzyć klaster z pojedynczym lub wielowęzłowym skalowaniem, który jest skalowany automatycznie za każdym razem, gdy przesyłasz przebieg. Możesz również dołączyć własny zasób obliczeniowy, chociaż obsługa różnych scenariuszy może się różnić.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Dowiedz się więcej o sposobach [przesyłania szkolenia do elementu docelowego obliczeń](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Cele obliczeniowe do wnioskowania

Następujące zasoby obliczeniowe mogą służyć do hostowania wdrożenia modelu.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Podczas wykonywania wnioskowania Azure Machine Learning tworzy kontener platformy Docker, który będzie hostować model i skojarzone zasoby niezbędne do korzystania z niego. Ten kontener jest następnie używany w jednym z następujących scenariuszy wdrażania:

* Jako *Usługa sieci Web* , która jest używana do wnioskowania w czasie rzeczywistym. Wdrożenia usług sieci Web używają jednego z następujących elementów docelowych obliczeń:

    * [Komputer lokalny](how-to-attach-compute-targets.md#local)
    * [Wystąpienie obliczeniowe usługi Azure Machine Learning](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (wersja zapoznawcza). Wdrożenie do funkcji opiera się tylko na Azure Machine Learning do skompilowania kontenera Docker. Z tego miejsca jest wdrażana za pomocą funkcji. Aby uzyskać więcej informacji, zobacz [Wdrażanie modelu uczenia maszynowego w Azure Functions (wersja zapoznawcza)](how-to-deploy-functions.md).

* Jako punkt końcowy _wnioskowania partii_ , który jest używany do okresowego przetwarzania partii danych. Wnioskowanie wsadowe używa [Azure Machine Learning klastrów obliczeniowych](how-to-create-attach-compute-cluster.md).

* Na _urządzeniu IoT_ (wersja zapoznawcza). Wdrożenie na urządzeniu IoT opiera się tylko na Azure Machine Learning do skompilowania kontenera Docker. Z tego miejsca jest wdrażana za pomocą Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [wdrażanie jako moduł IoT Edge (wersja zapoznawcza)](../iot-edge/tutorial-deploy-machine-learning.md).

Dowiedz się [, gdzie i jak wdrożyć model w miejscu docelowym obliczeń](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning COMPUTE (zarządzane)

Zarządzany zasób obliczeniowy jest tworzony i zarządzany przez Azure Machine Learning. To obliczenie jest zoptymalizowane pod kątem obciążeń uczenia maszynowego. Azure Machine Learning klastrami obliczeniowymi i [wystąpieniami obliczeniowymi](concept-compute-instance.md) są jedyne zarządzane obliczenia.

Można tworzyć Azure Machine Learning wystąpienia obliczeniowe lub Klastry obliczeniowe z:

* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md).
* Zestaw SDK języka Python i interfejs wiersza polecenia:
    * [Wystąpienie obliczeniowe](how-to-create-manage-compute-instance.md).
    * [Klaster obliczeniowy](how-to-create-attach-compute-cluster.md).
* [Zestaw SDK języka R](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (wersja zapoznawcza).
* Szablon Azure Resource Manager. Aby zapoznać się z przykładowym szablonem, zobacz [Tworzenie klastra obliczeniowego Azure Machine Learning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Rozszerzenie uczenia maszynowego [dla interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md#resource-management).

Po utworzeniu te zasoby obliczeniowe są automatycznie częścią obszaru roboczego, w przeciwieństwie do innych rodzajów obiektów docelowych obliczeń.


|Możliwość  |Klaster obliczeniowy  |Wystąpienie obliczeniowe  |
|---------|---------|---------|
|Klaster z jednym lub wiele węzłów     |    **&check;**       |         |
|Skalowanie automatyczne przy każdym przesyłaniu przebiegu     |     **&check;**      |         |
|Automatyczne zarządzanie klastrami i planowanie zadań     |   **&check;**        |     **&check;**      |
|Obsługa zasobów procesora CPU i procesora GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Gdy *klaster* obliczeniowy jest bezczynny, automatycznie skaluje się do 0 węzłów, więc nie płacisz, gdy nie jest używany. *Wystąpienie* obliczeniowe jest zawsze włączone i nie Skalowanie automatyczne. [Wystąpienie obliczeniowe należy zatrzymać,](how-to-create-manage-compute-instance.md#manage) gdy nie jest używane, aby uniknąć dodatkowych kosztów.

### <a name="supported-vm-series-and-sizes"></a>Obsługiwane serie maszyn wirtualnych i rozmiary

Po wybraniu rozmiaru węzła dla zarządzanego zasobu obliczeniowego w Azure Machine Learning można wybierać spośród spośród wybranych rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje szeroką gamę rozmiarów dla systemów Linux i Windows dla różnych obciążeń. Aby dowiedzieć się więcej, zobacz [typy i rozmiary maszyn wirtualnych](../virtual-machines/sizes.md).

Istnieje kilka wyjątków i ograniczeń umożliwiających wybranie rozmiaru maszyny wirtualnej:

* Niektóre serie maszyn wirtualnych nie są obsługiwane w Azure Machine Learning.
* Niektóre serie maszyn wirtualnych są ograniczone. Aby skorzystać z serii z ograniczeniami, skontaktuj się z pomocą techniczną i poproś o zwiększenie limitu przydziału dla serii. Aby uzyskać informacje na temat kontaktowania się z pomocą techniczną, zobacz [Opcje pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

Zapoznaj się z poniższą tabelą, aby dowiedzieć się więcej na temat obsługiwanych serii i ograniczeń.

| **Obsługiwana seria maszyn wirtualnych**  | **Ograniczenia** | **Kategoria** | **Obsługiwane przez** |
|------------|------------|------------|------------|
| D | Brak. | Ogólnego przeznaczenia | Klastry i wystąpienia obliczeniowe |
| DDSv4 | Brak. | Ogólnego przeznaczenia | Klastry i wystąpienia obliczeniowe |
| Dv2 | Brak. | Ogólnego przeznaczenia | Klastry i wystąpienia obliczeniowe |
| Dv3 | Brak.| Ogólnego przeznaczenia | Klastry i wystąpienia obliczeniowe |
| DSv2 | Brak. | Ogólnego przeznaczenia | Klastry i wystąpienia obliczeniowe |
| DSv3 | Brak.| Ogólnego przeznaczenia | Klastry i wystąpienia obliczeniowe |
| EAv4 | Brak. | Optymalizacja pod kątem pamięci | Klastry i wystąpienia obliczeniowe |
| EV3 | Brak. | Optymalizacja pod kątem pamięci | Klastry i wystąpienia obliczeniowe |
| FSv2 | Brak. | Optymalizacja pod kątem obliczeń | Klastry i wystąpienia obliczeniowe |
| H | Brak. | Obliczenia o wysokiej wydajności | Klastry i wystąpienia obliczeniowe |
| HB | Wymaga zatwierdzenia. | Obliczenia o wysokiej wydajności | Klastry i wystąpienia obliczeniowe |
| HBv2 | Wymaga zatwierdzenia. |  Obliczenia o wysokiej wydajności | Klastry i wystąpienia obliczeniowe |
| MAGAZYNU HCS | Wymaga zatwierdzenia. |  Obliczenia o wysokiej wydajności | Klastry i wystąpienia obliczeniowe |
| M | Wymaga zatwierdzenia. | Optymalizacja pod kątem pamięci | Klastry i wystąpienia obliczeniowe |
| NC | Brak. |  Procesory GPU | Klastry i wystąpienia obliczeniowe |
| Promocja kontrolera sieci | Brak. | Procesory GPU | Klastry i wystąpienia obliczeniowe |
| NCsv2 | Wymaga zatwierdzenia. | Procesory GPU | Klastry i wystąpienia obliczeniowe |
| NCsv3 | Wymaga zatwierdzenia. | Procesory GPU | Klastry i wystąpienia obliczeniowe |  
| NDs | Wymaga zatwierdzenia. | Procesory GPU | Klastry i wystąpienia obliczeniowe | 
| NDv2 | Wymaga zatwierdzenia. | Procesory GPU | Klastry i wystąpienia obliczeniowe | 
| NV | Brak. | Procesory GPU | Klastry i wystąpienia obliczeniowe | 
| NVv3 | Wymaga zatwierdzenia. | Procesory GPU | Klastry i wystąpienia obliczeniowe | 


Mimo że Azure Machine Learning obsługuje te serie maszyn wirtualnych, mogą one nie być dostępne we wszystkich regionach świadczenia usługi Azure. Aby sprawdzić, czy dostępne są serie maszyn wirtualnych, zobacz temat [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

> [!NOTE]
> Azure Machine Learning nie obsługuje wszystkich rozmiarów maszyn wirtualnych obsługiwanych przez usługę Azure COMPUTE. Aby wyświetlić listę dostępnych rozmiarów maszyn wirtualnych, użyj jednej z następujących metod:
> * [Interfejs API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Zestaw SDK dla języka Python](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>Izolacja obliczeniowa

Azure Machine Learning COMPUTE oferuje rozmiary maszyn wirtualnych, które są odizolowane od określonego typu sprzętu i przeznaczone dla jednego klienta. Rozmiary maszyn wirtualnych wyizolowane najlepiej nadają się do obciążeń, które wymagają wysokiego stopnia odizolowania obciążeń innych klientów z przyczyn, które obejmują zgodność i wymagania prawne. Wykorzystanie izolowanego rozmiaru gwarantuje, że maszyna wirtualna będzie jedyną uruchomioną w tym konkretnym wystąpieniu serwera.

Obecnie wyizolowane oferty maszyn wirtualnych obejmują:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3 *

*Obsługa technologii RDMA

Aby dowiedzieć się więcej o izolacji, zobacz [izolacja w chmurze publicznej platformy Azure](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Obliczenia niezarządzane

Niezarządzany cel obliczeń *nie* jest zarządzany przez Azure Machine Learning. Ten typ elementu docelowego obliczeń można utworzyć poza Azure Machine Learning, a następnie dołączyć go do obszaru roboczego. Niezarządzane zasoby obliczeniowe mogą wymagać dodatkowych czynności, aby zachować lub zwiększyć wydajność obciążeń uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Instrukcje:
* [Używanie elementu docelowego obliczeń do uczenia modelu](how-to-set-up-training-targets.md)
* [Wdrażanie modelu w obiekcie docelowym obliczeń](how-to-deploy-and-where.md)
