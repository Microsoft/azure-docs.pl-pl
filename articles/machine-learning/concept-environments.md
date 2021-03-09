---
title: Informacje o środowiskach Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o środowiskach uczenia maszynowego, które umożliwiają przeprowadzenie powtarzalnych, podlegających inspekcji, & definicji zależności przenośnej uczenia maszynowego dla różnych obiektów docelowych
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
ms.openlocfilehash: 648dbe6b8d275c832f219cb6f3119ac0bc518a54
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508473"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co to są środowiska Azure Machine Learning?

Środowiska Azure Machine Learning są hermetyzacją środowiska, w którym odbywa się szkolenie uczenia maszynowego. Określają one pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleń i oceniania. Określają one również czasy wykonywania (Python, Spark lub Docker). Środowiska są zarządzane i obsługiwane przez program w obszarze roboczym Machine Learning, które umożliwiają powtarzalny, z inspekcją i przenośne przepływy pracy uczenia maszynowego w różnych celach obliczeniowych.

Możesz użyć `Environment` obiektu na lokalnym obliczeniu, aby:
* Opracowywanie skryptu szkoleniowego.
* Ponownie Użyj tego samego środowiska na Azure Machine Learning COMPUTE na potrzeby szkolenia modeli na dużą skalę.
* Wdróż model przy użyciu tego samego środowiska.
* Ponownie odwiedź środowisko, w którym został przeszkolony istniejący model.

Na poniższym diagramie przedstawiono, jak można użyć pojedynczego `Environment` obiektu w konfiguracji przebiegu (na potrzeby szkolenia) oraz konfiguracji wnioskowania i wdrożenia (dla wdrożeń usług sieci Web).

![Diagram środowiska w przepływie pracy uczenia maszynowego](./media/concept-environments/ml-environment.png)

Środowisko, obiekt docelowy obliczeń i skrypt szkoleniowy tworzą razem konfigurację przebiegu: pełną specyfikację przebiegu szkoleniowego.

## <a name="types-of-environments"></a>Typy środowisk

Środowiska mogą być szeroko podzielone na trzy kategorie: *nadzorowane*, *zarządzane przez użytkownika* i *zarządzane przez system*.

Środowiska nadzorowane są udostępniane przez Azure Machine Learning i są domyślnie dostępne w obszarze roboczym. Przeznaczone do użycia jako ", zawierają kolekcje pakietów i ustawień języka Python, które ułatwiają rozpoczęcie pracy z różnymi platformami uczenia maszynowego. Te wstępnie utworzone środowiska umożliwiają również szybszy czas wdrażania. Aby zapoznać się z pełną listą, zobacz artykuł dotyczący [środowisk nadzorowanych](resource-curated-environments.md).

W środowiskach zarządzanych przez użytkownika użytkownik jest odpowiedzialny za skonfigurowanie środowiska i zainstalowanie każdego pakietu, którego potrzebuje skrypt szkoleniowy w celu obliczenia. Conda nie sprawdza Twojego środowiska ani nie instaluje żadnych elementów. W przypadku definiowania własnego środowiska należy wyświetlić listę `azureml-defaults` z wersją `>= 1.0.45` jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web.

Środowiska zarządzane przez system są używane, gdy chcesz, aby [Conda](https://conda.io/docs/) zarządzać środowiskiem Python i zależnościami skryptów. Nowe środowisko Conda jest tworzone na podstawie obiektu zależności Conda. Usługa Azure Machine Learning domyślnie przyjmuje ten typ środowiska, ponieważ jest to użyteczność w przypadku zdalnych obiektów docelowych obliczeń, które nie są ręcznie konfigurowane.

## <a name="create-and-manage-environments"></a>Tworzenie środowisk i zarządzanie nimi

Środowiska można tworzyć według:

* Definiowanie nowych `Environment` obiektów przy użyciu nadzorowanego środowiska lub przez Definiowanie własnych zależności.
* Używanie istniejących `Environment` obiektów w obszarze roboczym. Takie podejście umożliwia spójność i odtwarzalność z zależnościami.
* Importowanie z istniejącej definicji środowiska Anaconda.
* Korzystanie z interfejsu wiersza polecenia Azure Machine Learning
* [Korzystanie z rozszerzenia VS Code](how-to-manage-resources-vscode.md#create-environment)

Aby zapoznać się z konkretnymi przykładami kodu, zobacz sekcję "Tworzenie środowiska" w temacie [jak korzystać z środowisk](how-to-use-environments.md#create-an-environment). Środowiska są również łatwo zarządzane za poorednictwem obszaru roboczego. Obejmują one następujące funkcje:

* Środowiska są automatycznie rejestrowane w obszarze roboczym podczas przesyłania eksperymentu. Można je również zarejestrować ręcznie.
* Możesz pobrać środowiska z obszaru roboczego, aby użyć go do szkolenia lub wdrożenia lub wprowadzić zmiany w definicji środowiska.
* Dzięki wersji można zobaczyć zmiany w środowiskach w czasie, co zapewnia odtwarzalność.
* Możesz automatycznie tworzyć obrazy platformy Docker w swoich środowiskach.

Aby zapoznać się z przykładami kodu, zobacz sekcję "zarządzanie środowiskami" dotyczącą [korzystania ze środowisk](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Kompilowanie, buforowanie i ponowne używanie środowiska

Usługa Azure Machine Learning kompiluje definicje środowiska w obrazy platformy Docker i środowiska Conda. W pamięci podręcznej są również używane środowiska, dzięki czemu można je ponownie wykorzystać w kolejnych przebiegach szkoleniowych i wdrożeniach punktów końcowych usługi. Uruchamianie skryptu szkoleniowego zdalnie wymaga utworzenia obrazu platformy Docker, a uruchomienie lokalne może bezpośrednio używać środowiska Conda. 

### <a name="submitting-a-run-using-an-environment"></a>Przesyłanie przebiegu przy użyciu środowiska

Podczas pierwszego przesyłania zdalnego przebiegu przy użyciu środowiska usługa Azure Machine Learning wywołuje [zadanie kompilacji ACR](../container-registry/container-registry-tasks-overview.md) na Azure Container Registry (ACR) skojarzonym z obszarem roboczym. Skompilowany obraz platformy Docker jest następnie buforowany w obszarze roboczym ACR. Nadzorowane środowiska są obsługiwane przez obrazy platformy Docker, które są buforowane w ACR globalnym. Na początku wykonywania przebiegu obraz jest pobierany przez obiekt docelowy obliczeń z odpowiednich ACR.

W przypadku uruchomień lokalnych środowisko Docker lub Conda jest tworzone na podstawie definicji środowiska. Skrypty są następnie wykonywane w docelowym środowisku obliczeniowym (środowisko uruchomieniowe lokalnego lub lokalnego aparatu platformy Docker).

### <a name="building-environments-as-docker-images"></a>Kompilowanie środowisk jako obrazów platformy Docker

Jeśli definicja środowiska nie istnieje już w obszarze roboczym ACR, zostanie utworzony nowy obraz. Kompilacja obrazu składa się z dwóch kroków:

 1. Pobieranie obrazu podstawowego i wykonywanie wszelkich kroków platformy Docker
 2. Kompilowanie środowiska Conda zgodnie z zależnościami Conda określonymi w definicji środowiska.

Drugi krok zostanie pominięty w przypadku określenia [zależności zarządzanych przez użytkownika](/python/api/azureml-core/azureml.core.environment.pythonsection). W takim przypadku użytkownik jest odpowiedzialny za instalację dowolnych pakietów języka Python, dołączając je do obrazu podstawowego lub określając niestandardowe kroki platformy Docker w pierwszym kroku. Użytkownik jest również odpowiedzialny za określenie prawidłowej lokalizacji pliku wykonywalnego języka Python. Istnieje również możliwość użycia [niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="image-caching-and-reuse"></a>Buforowanie i ponowne użycie obrazu

Jeśli używasz tej samej definicji środowiska dla innego uruchomienia, usługa Azure Machine Learning ponownie używa buforowanego obrazu z obszaru roboczego ACR. 

Aby wyświetlić szczegóły buforowanego obrazu, użyj metody [Environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) .

Aby określić, czy ponownie użyć buforowanego obrazu, czy utworzyć nowy, usługa oblicza [wartość skrótu](https://en.wikipedia.org/wiki/Hash_table) z definicji środowiska i porównuje ją z skrótami istniejących środowisk. Skrót jest oparty na:
 
 * Wartość właściwości obrazu podstawowego
 * Niestandardowa wartość właściwości kroków platformy Docker
 * Lista pakietów języka Python w definicji Conda
 * Lista pakietów w definicji platformy Spark 

Wartość skrótu nie jest zależna od nazwy środowiska lub wersji — Jeśli zmienisz nazwę środowiska lub utworzysz nowe środowisko z dokładnymi właściwościami i pakietami istniejącymi, a następnie wartości skrótu pozostają takie same. Jednak zmiany definicji środowiska, takie jak dodawanie lub usuwanie pakietu języka Python lub zmiana wersji pakietu, powodują zmianę wartości skrótu. Zmiana kolejności zależności lub kanałów w środowisku spowoduje powstanie nowego środowiska i w związku z tym wymaga nowej kompilacji obrazu. Należy pamiętać, że jakakolwiek zmiana w środowisku nadzorowanym spowoduje unieważnienie skrótu i powstanie nowego środowiska bez nadzoru.

Obliczona wartość skrótu jest porównywana z wartościami w obszarze roboczym i globalnym ACR (lub na obiekcie docelowym obliczeń dla lokalnych przebiegów). Jeśli istnieje dopasowanie, zostanie pobrany buforowany obraz, w przeciwnym razie zostanie wyzwolona kompilacja obrazu. Czas trwania ściągania obrazu w pamięci podręcznej obejmuje czas pobierania, podczas którego czas ściągania nowo skompilowanego obrazu obejmuje zarówno czas kompilacji, jak i czas pobierania. 

Na poniższym diagramie przedstawiono trzy definicje środowiska. Dwa z nich mają różne nazwy i wersje, ale identyczne obrazy podstawowe i pakiety języka Python. Ale mają one ten sam skrót i odpowiadają na ten sam zapisany w pamięci podręcznej. Trzecie środowisko ma inne pakiety i wersje języka Python, dlatego odnosi się do innego obrazu w pamięci podręcznej.

![Diagram pamięci podręcznej środowiska jako obrazów platformy Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> W przypadku tworzenia środowiska z przypiętym zależnością pakietu, na przykład, ```numpy``` to środowisko będzie korzystać z zainstalowanej wersji pakietu _w momencie tworzenia środowiska_. Ponadto wszystkie przyszłe środowiska ze zgodną definicją będą nadal używane w starej wersji. 

Aby zaktualizować pakiet, określ numer wersji, aby wymusić Odbudowywanie obrazu ```numpy==1.18.1``` . Zostaną zainstalowane nowe zależności, w tym zagnieżdżone, które mogą spowodować uszkodzenie wcześniej działającego scenariusza. 

> [!WARNING]
>  Metoda [Environment. Build](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) spowoduje odbudowanie buforowanego obrazu z możliwym efektem ubocznym aktualizowania przypiętych pakietów i przerwaniem odtwarzalności dla wszystkich definicji środowiska odpowiadających danemu z pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak tworzyć środowiska i korzystać](how-to-use-environments.md) z nich w Azure Machine Learning.
* Zobacz dokumentację referencyjną zestawu SDK języka Python dla [klasy Environment](/python/api/azureml-core/azureml.core.environment%28class%29).
* Zobacz dokumentację referencyjną języka R SDK dla [środowisk](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).