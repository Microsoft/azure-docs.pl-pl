---
title: Informacje o środowiskach Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Ten artykuł zawiera informacje o zaletach środowisk uczenia maszynowego, które umożliwiają powtarzalnych, poddawanych inspekcji i przenośnych definicji zależności Machine Learning w różnych obiektach docelowych obliczeń.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064193"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co to są środowiska Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Środowiska Azure Machine Learning określają pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleń i oceniania. Określają one również czasy wykonywania (Python, Spark lub Docker). Środowiska są zarządzane i obsługiwane przez program w obszarze roboczym Machine Learning, które umożliwiają powtarzalny, z inspekcją i przenośne przepływy pracy uczenia maszynowego w różnych celach obliczeniowych.

Możesz użyć `Environment` obiektu na lokalnym obliczeniu, aby:
* Opracowywanie skryptu szkoleniowego.
* Ponownie Użyj tego samego środowiska na Azure Machine Learning COMPUTE na potrzeby szkolenia modeli na dużą skalę.
* Wdróż model przy użyciu tego samego środowiska.

Na poniższym diagramie pokazano, w jaki sposób można użyć `Environment` pojedynczego obiektu w konfiguracji przebiegu, na potrzeby szkoleń oraz konfiguracji wnioskowania i wdrożenia w przypadku wdrożeń usług sieci Web.

![Diagram środowiska w przepływie pracy uczenia maszynowego](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typy środowisk

Środowiska mogą być szeroko podzielone na trzy kategorie: *nadzorowane*, *zarządzane przez użytkownika*i *zarządzane przez system*.

Środowiska nadzorowane są udostępniane przez Azure Machine Learning i są domyślnie dostępne w obszarze roboczym. Zawierają one kolekcje pakietów i ustawień języka Python, które ułatwiają rozpoczęcie pracy z różnymi platformami uczenia maszynowego. 

W środowiskach zarządzanych przez użytkownika użytkownik jest odpowiedzialny za skonfigurowanie środowiska i zainstalowanie każdego pakietu, którego potrzebuje skrypt szkoleniowy w celu obliczenia. Conda nie sprawdza Twojego środowiska ani nie instaluje żadnych elementów. W przypadku definiowania własnego środowiska należy wyświetlić listę `azureml-defaults` z wersją `>= 1.0.45` jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web.

Środowiska zarządzane przez system są używane, gdy chcesz, aby [Conda](https://conda.io/docs/) zarządzać środowiskiem Python i zależnościami skryptów. Usługa domyślnie przyjmuje ten typ środowiska, z powodu jego użyteczności w przypadku zdalnych obiektów docelowych obliczeń, które nie są ręcznie konfigurowane.

## <a name="create-and-manage-environments"></a>Tworzenie środowisk i zarządzanie nimi

Środowiska można tworzyć według:

* Definiowanie nowych `Environment` obiektów przy użyciu nadzorowanego środowiska lub przez Definiowanie własnych zależności.
* Używanie istniejących `Environment` obiektów w obszarze roboczym. Takie podejście umożliwia spójność i odtwarzalność z zależnościami.
* Importowanie z istniejącej definicji środowiska Anaconda.
* Korzystanie z interfejsu wiersza polecenia Azure Machine Learning

Aby zapoznać się z konkretnymi przykładami kodu, zobacz sekcję "Tworzenie środowiska" w temacie [ponowne używanie środowisk do szkolenia i wdrażania](how-to-use-environments.md#create-an-environment). Środowiska są również łatwo zarządzane za poorednictwem obszaru roboczego. Obejmują one następujące funkcje:

* Środowiska są automatycznie rejestrowane w obszarze roboczym podczas przesyłania eksperymentu. Można je również zarejestrować ręcznie.
* Możesz pobrać środowiska z obszaru roboczego, aby użyć go do szkolenia lub wdrożenia lub wprowadzić zmiany w definicji środowiska.
* Dzięki wersji można zobaczyć zmiany w środowiskach w czasie, co zapewnia odtwarzalność.
* Możesz automatycznie tworzyć obrazy platformy Docker w swoich środowiskach.

Aby zapoznać się z przykładami kodu, zobacz sekcję "zarządzanie środowiskami" w temacie [ponowne używanie środowisk do szkolenia i wdrażania](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Kompilowanie, buforowanie i ponowne używanie środowiska

Usługa Azure Machine Learning kompiluje definicje środowiska w obrazy platformy Docker i środowiska Conda. W pamięci podręcznej są również używane środowiska, dzięki czemu można je ponownie wykorzystać w kolejnych przebiegach szkoleniowych i wdrożeniach punktów końcowych usługi.

### <a name="building-environments-as-docker-images"></a>Kompilowanie środowisk jako obrazów platformy Docker

Zazwyczaj podczas pierwszego przesyłania przebiegu przy użyciu środowiska usługa Azure Machine Learning wywołuje [zadanie kompilacji ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) na Azure Container Registry (ACR) skojarzonym z obszarem roboczym. Skompilowany obraz platformy Docker jest następnie buforowany w obszarze roboczym ACR. Na początku wykonywania przebiegu obraz jest pobierany przez obiekt docelowy obliczeń.

Kompilacja obrazu składa się z dwóch kroków:

 1. Pobieranie obrazu podstawowego i wykonywanie wszelkich kroków platformy Docker
 2. Kompilowanie środowiska Conda zgodnie z zależnościami Conda określonymi w definicji środowiska.

Drugi krok zostanie pominięty w przypadku określenia [zależności zarządzanych przez użytkownika](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). W takim przypadku użytkownik jest odpowiedzialny za instalację dowolnych pakietów języka Python, dołączając je do obrazu podstawowego lub określając niestandardowe kroki platformy Docker w pierwszym kroku. Użytkownik jest również odpowiedzialny za określenie prawidłowej lokalizacji pliku wykonywalnego języka Python.

### <a name="image-caching-and-reuse"></a>Buforowanie i ponowne użycie obrazu

Jeśli używasz tej samej definicji środowiska dla innego uruchomienia, usługa Azure Machine Learning ponownie używa buforowanego obrazu z obszaru roboczego ACR. 

Aby wyświetlić szczegóły buforowanego obrazu, użyj metody [Environment. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) .

Aby określić, czy ponownie użyć buforowanego obrazu, czy utworzyć nowy, usługa oblicza [wartość skrótu](https://en.wikipedia.org/wiki/Hash_table) z definicji środowiska i porównuje ją z skrótami istniejących środowisk. Skrót jest oparty na:
 
 * Wartość właściwości obrazu podstawowego
 * Niestandardowa wartość właściwości kroków platformy Docker
 * Lista pakietów języka Python w definicji Conda
 * Lista pakietów w definicji platformy Spark 

Wartość skrótu nie jest zależna od nazwy lub wersji środowiska. Zmiany definicji środowiska, takie jak dodawanie lub usuwanie pakietu języka Python lub zmiana wersji pakietu, powodują zmianę wartości skrótu i wyzwalanie odbudowy obrazu. Jeśli jednak po prostu zmienisz nazwę środowiska lub utworzysz nowe środowisko z dokładnymi właściwościami i pakietami istniejącymi, wartość skrótu pozostanie taka sama i zostanie użyty obraz w pamięci podręcznej.

Na poniższym diagramie przedstawiono trzy definicje środowiska. Dwa z nich mają różne nazwy i wersje, ale identyczne obrazy podstawowe i pakiety języka Python. Mają one ten sam skrót i dlatego odpowiadają te same zapisane w pamięci podręcznej. Trzecie środowisko ma inne pakiety i wersje języka Python, dlatego odnosi się do innego obrazu w pamięci podręcznej.

![Diagram pamięci podręcznej środowiska jako obrazów platformy Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> W przypadku tworzenia środowiska z przypiętym zależnością pakietu, na ```numpy```przykład, to środowisko będzie korzystać z zainstalowanej wersji pakietu _w momencie tworzenia środowiska_. Ponadto wszystkie przyszłe środowiska ze zgodną definicją będą nadal używane w starej wersji. 

Aby zaktualizować pakiet, określ numer wersji, aby wymusić Odbudowywanie obrazu ```numpy==1.18.1```. Należy zauważyć, że zostaną zainstalowane nowe zależności, w tym zagnieżdżone, które mogą spowodować uszkodzenie wcześniej działającego scenariusza.

> [!WARNING]
>  Metoda [Environment. Build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) spowoduje odbudowanie buforowanego obrazu z możliwym efektem ubocznym aktualizowania przypiętych pakietów i przerwaniem odtwarzalności dla wszystkich definicji środowiska odpowiadających danemu z pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak tworzyć środowiska i korzystać](how-to-use-environments.md) z nich w Azure Machine Learning.
* Zobacz dokumentację referencyjną zestawu SDK języka Python dla [klasy Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Zobacz dokumentację referencyjną języka R SDK dla [środowisk](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
