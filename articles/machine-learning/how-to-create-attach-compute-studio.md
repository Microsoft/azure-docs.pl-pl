---
title: Tworzenie szkoleń & wdrażanie obliczeń (Studio)
titleSuffix: Azure Machine Learning
description: Korzystanie z programu Studio do tworzenia zasobów obliczeniowych dotyczących szkoleń i wdrożeń (cele obliczeniowe) dla uczenia maszynowego
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: 4194c71823e1affde1dcae47fd7e64668b57c0cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149362"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Utwórz cele obliczeniowe dla szkolenia i wdrożenia modelu w programie Azure Machine Learning Studio

W tym artykule dowiesz się, jak tworzyć cele obliczeniowe i zarządzać nimi w usłudze Azure Machine Studio.  Można również tworzyć cele obliczeń i zarządzać nimi za pomocą:

* Azure Machine Learning uczenie zestawu SDK lub rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning
  * [Wystąpienie obliczeniowe](how-to-create-manage-compute-instance.md)
  * [Klaster obliczeniowy](how-to-create-attach-compute-cluster.md)
  * [Klaster usługi Azure Kubernetes](how-to-create-attach-kubernetes.md)
  * [Inne zasoby obliczeniowe](how-to-attach-compute-targets.md)
* [Vs Code rozszerzenie](how-to-manage-resources-vscode.md#compute-clusters) Azure Machine Learning.


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Co to jest obiekt docelowy obliczeń? 

Dzięki Azure Machine Learning można nauczyć model na różnych zasobach lub środowiskach, zbiorczo nazywanymi [__obiektami docelowymi obliczeń__](concept-azure-machine-learning-architecture.md#compute-targets). Obiekt docelowy obliczeń może być maszyną lokalną lub zasobem w chmurze, takim jak Azure Machine Learning COMPUTE, Azure HDInsight lub zdalną maszynę wirtualną.  Możesz również utworzyć cele obliczeniowe dla wdrożenia modelu, zgodnie z opisem w artykule ["gdzie i jak wdrażać modele"](how-to-deploy-and-where.md).

## <a name="view-compute-targets"></a><a id="portal-view"></a>Wyświetl cele obliczeń

Aby wyświetlić wszystkie cele obliczeniowe dla obszaru roboczego, wykonaj następujące czynności:

1. Przejdź do [Azure Machine Learning Studio](https://ml.azure.com).
 
1. W obszarze __Zarządzaj__ wybierz pozycję __obliczenia__.

1. Wybierz pozycję karty u góry, aby wyświetlić każdy typ elementu docelowego obliczeń.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Wyświetl listę elementów docelowych obliczeń":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Utwórz element docelowy obliczeń

Wykonaj poprzednie kroki, aby wyświetlić listę elementów docelowych obliczeń. Następnie wykonaj następujące kroki, aby utworzyć obiekt docelowy obliczeń:

1. Wybierz kartę u góry odpowiadającą typowi obliczeń, które chcesz utworzyć.

1. Jeśli nie masz obiektów docelowych obliczeń, wybierz pozycję  **Utwórz** w środku strony.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Utwórz element docelowy obliczeń":::

1. Jeśli zostanie wyświetlona lista zasobów obliczeniowych, wybierz pozycję **+ Nowy** nad listą.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Wybierz pozycję Nowy":::


1. Wypełnij formularz dla typu obliczenia:

  * [Wystąpienie obliczeniowe](#compute-instance)
  * [Klastry obliczeniowe](#amlcompute)
  * [Klastry wnioskowania](#inference-clusters)
  * [Dołączone obliczenia](#attached-compute)

1. Wybierz przycisk __Utwórz__.

1. Wyświetl stan operacji tworzenia, wybierając obiekt docelowy obliczeń z listy:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Wyświetlanie stanu obliczeń z listy":::


### <a name="compute-instance"></a>Wystąpienie obliczeniowe

Wykonaj [powyższe kroki](#portal-create) , aby utworzyć wystąpienie obliczeniowe.  Następnie wypełnij formularz w następujący sposób:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Tworzenie nowego wystąpienia obliczeniowego":::


|Pole  |Opis  |
|---------|---------|
|Nazwa obiektu obliczeniowego     |  <li>Nazwa jest wymagana i musi mieć długość od 3 do 24 znaków.</li><li>Prawidłowe znaki to wielkie i małe litery, cyfry i  **-** znaki.</li><li>Nazwa musi rozpoczynać się od litery</li><li>Nazwa musi być unikatowa we wszystkich istniejących obliczeniach w regionie świadczenia usługi Azure. Jeśli wybrana nazwa nie jest unikatowa, zostanie wyświetlony alert</li><li>Jeśli **-**  jest używany znak, musi następować co najmniej jedną literę w dalszej części nazwy</li>     |
|Typ maszyny wirtualnej |  Wybierz procesor CPU lub GPU. Tego typu nie można zmienić po utworzeniu     |
|Rozmiar maszyny wirtualnej     |  Obsługiwane rozmiary maszyn wirtualnych mogą być ograniczone w Twoim regionie. Sprawdź [listę dostępności](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Włącz/Wyłącz dostęp SSH     |   Dostęp SSH jest domyślnie wyłączony.  Nie można uzyskać dostępu do protokołu SSH. Zmieniono po utworzeniu. Upewnij się, że włączono dostęp, jeśli planujesz interaktywną debugowanie za pomocą usługi [vs Code Remote](how-to-set-up-vs-code-remote.md)   |
|Ustawienia zaawansowane     |  Opcjonalny. Skonfiguruj sieć wirtualną. Określ **grupę zasobów**, **sieć wirtualną** i **podsieć** , aby utworzyć wystąpienie obliczeniowe wewnątrz Virtual Network platformy Azure. Aby uzyskać więcej informacji, zobacz te [wymagania sieciowe dotyczące sieci](./how-to-secure-training-vnet.md) wirtualnej.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Klastry obliczeniowe

Tworzenie klastra obliczeniowego pojedynczego lub wielowęzłowego na potrzeby szkoleń, inferencingów wsadowych lub obciążeń edukacyjnych. Wykonaj [powyższe kroki](#portal-create) , aby utworzyć klaster obliczeniowy.  Następnie wypełnij formularz w następujący sposób:


|Pole  |Opis  |
|---------|---------|
|Nazwa obiektu obliczeniowego     |  <li>Nazwa jest wymagana i musi mieć długość od 3 do 24 znaków.</li><li>Prawidłowe znaki to wielkie i małe litery, cyfry i  **-** znaki.</li><li>Nazwa musi rozpoczynać się od litery</li><li>Nazwa musi być unikatowa we wszystkich istniejących obliczeniach w regionie świadczenia usługi Azure. Jeśli wybrana nazwa nie jest unikatowa, zostanie wyświetlony alert</li><li>Jeśli **-**  jest używany znak, musi następować co najmniej jedną literę w dalszej części nazwy</li>     |
|Typ maszyny wirtualnej |  Wybierz procesor CPU lub GPU. Tego typu nie można zmienić po utworzeniu     |
|Priorytet maszyny wirtualnej | Wybierz opcję **dedykowany** lub **niski priorytet**.  Maszyny wirtualne o niskim priorytecie są tańsze, ale nie gwarantują węzłów obliczeniowych. Zadanie może zostać przeniesiona.
|Rozmiar maszyny wirtualnej     |  Obsługiwane rozmiary maszyn wirtualnych mogą być ograniczone w Twoim regionie. Sprawdź [listę dostępności](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Minimalna liczba węzłów | Minimalna liczba węzłów, które mają zostać objęte obsługą administracyjną. Jeśli potrzebujesz dedykowanej liczby węzłów, ustaw tę wartość w tym miejscu. Oszczędzaj pieniądze, ustawiając minimalną wartość 0, aby nie zapłacić za żadne węzły, gdy klaster jest w stanie bezczynności. |
|Maksymalna liczba węzłów | Maksymalna liczba węzłów, które mają zostać objęte obsługą administracyjną. Obliczenia będą automatycznie skalowane do maksymalnej liczby węzłów, gdy zadanie zostanie przesłane. |
|Ustawienia zaawansowane     |  Opcjonalny. Skonfiguruj sieć wirtualną. Określ **grupę zasobów**, **sieć wirtualną** i **podsieć** , aby utworzyć wystąpienie obliczeniowe wewnątrz Virtual Network platformy Azure. Aby uzyskać więcej informacji, zobacz te [wymagania sieciowe dotyczące sieci](./how-to-secure-training-vnet.md) wirtualnej.   Dołącz także [zarządzane tożsamości](#managed-identity) , aby udzielić dostępu do zasobów     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Konfigurowanie tożsamości zarządzanej

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Podczas tworzenia klastra lub podczas edycji szczegółów klastra obliczeniowego, w **ustawieniach zaawansowanych** Przełącz opcję **Przypisz tożsamość zarządzaną** i określ tożsamość przypisaną przez użytkownika lub tożsamość.

#### <a name="managed-identity-usage"></a>Użycie tożsamości zarządzanej

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Klastry wnioskowania

> [!IMPORTANT]
> Korzystanie z usługi Azure Kubernetes z Azure Machine Learning ma wiele opcji konfiguracji. Niektóre scenariusze, takie jak obsługa sieci, wymagają dodatkowej instalacji i konfiguracji. Aby uzyskać więcej informacji na temat używania AKS z platformą Azure ML, zobacz [Tworzenie i dołączanie klastra usługi Azure Kubernetes](how-to-create-attach-kubernetes.md).

Utwórz lub Dołącz klaster usługi Azure Kubernetes Service (AKS) dla dużej skali inferencing. Wykonaj [powyższe kroki](#portal-create) , aby utworzyć klaster AKS.  Następnie wypełnij formularz w następujący sposób:


|Pole  |Opis  |
|---------|---------|
|Nazwa obiektu obliczeniowego     |  <li>Nazwa jest wymagana. Nazwa musi składać się z od 2 do 16 znaków. </li><li>Prawidłowe znaki to wielkie i małe litery, cyfry i  **-** znaki.</li><li>Nazwa musi rozpoczynać się od litery</li><li>Nazwa musi być unikatowa we wszystkich istniejących obliczeniach w regionie świadczenia usługi Azure. Jeśli wybrana nazwa nie jest unikatowa, zostanie wyświetlony alert</li><li>Jeśli **-**  jest używany znak, musi następować co najmniej jedną literę w dalszej części nazwy</li>     |
|Kubernetes Service | Wybierz pozycję **Utwórz nowy** i Wypełnij resztę formularza.  Lub wybierz pozycję **Użyj istniejącej** , a następnie wybierz istniejący klaster AKS z subskrypcji.
|Region (Region) |  Wybierz region, w którym zostanie utworzony klaster |
|Rozmiar maszyny wirtualnej     |  Obsługiwane rozmiary maszyn wirtualnych mogą być ograniczone w Twoim regionie. Sprawdź [listę dostępności](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Cel klastra  | Wybierz **produkcję lub tworzenie** i **testowanie** |
|Liczba węzłów | Liczba węzłów pomnożona przez liczbę rdzeni (procesorów wirtualnych vCPU) maszyny wirtualnej musi być większa lub równa 12. |
| Konfiguracja sieci | Wybierz pozycję **Zaawansowane** , aby utworzyć obliczenia w ramach istniejącej sieci wirtualnej. Aby uzyskać więcej informacji na temat AKS w sieci wirtualnej, zapoznaj się [z izolacją sieci podczas szkoleń i wnioskowania z prywatnymi punktami końcowymi i sieciami wirtualnymi](./how-to-secure-inferencing-vnet.md). |
| Włącz konfigurację protokołu SSL | Użyj tego, aby skonfigurować certyfikat SSL na potrzeby obliczeń |

### <a name="attached-compute"></a>Dołączone obliczenia

Aby używać obiektów docelowych obliczeń utworzonych poza obszarem roboczym Azure Machine Learning, należy je dołączyć. Dołączenie obiektu docelowego obliczeń sprawia, że jest on dostępny dla Twojego obszaru roboczego.  Użyj **dołączonego obliczenia** , aby dołączyć element docelowy obliczeń do **szkolenia**.  Użyj **klastrów wnioskowania** , aby dołączyć klaster AKS dla **inferencing**.

Wykonaj [powyższe kroki](#portal-create) , aby dołączyć obliczenia.  Następnie wypełnij formularz w następujący sposób:

1. Wprowadź nazwę dla elementu docelowego obliczeń. 
1. Wybierz typ obliczeń do dołączenia. Nie wszystkie typy obliczeniowe mogą być dołączane z programu Azure Machine Learning Studio. Typy obliczeń, które są obecnie dołączone do szkolenia, obejmują:
    * Maszyna wirtualna platformy Azure (w celu dołączenia Data Science Virtual Machine)
    * Azure Databricks (do użycia w potokach uczenia maszynowego)
    * Azure Data Lake Analytics (do użycia w potokach uczenia maszynowego)
    * Azure HDInsight

1. Wypełnij formularz i podaj wartości wymaganych właściwości.

    > [!NOTE]
    > Firma Microsoft zaleca korzystanie z kluczy SSH, które są bezpieczniejsze niż hasła. Hasła są podatne na ataki z wymuszeniem. Klucze SSH korzystają z podpisów kryptograficznych. Aby uzyskać informacje na temat sposobu tworzenia kluczy SSH do użycia z usługą Azure Virtual Machines, zobacz następujące dokumenty:
    >
    > * [Tworzenie i Używanie kluczy SSH w systemie Linux lub macOS](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Tworzenie i Używanie kluczy SSH w systemie Windows](../virtual-machines/linux/ssh-from-windows.md)

1. Wybierz pozycję __Dołącz__. 


## <a name="next-steps"></a>Następne kroki

Po utworzeniu elementu docelowego i dołączeniu go do obszaru roboczego użyjesz go w [konfiguracji przebiegu](how-to-set-up-training-targets.md) z `ComputeTarget` obiektem:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Aby [przesłać przebieg szkoleniowy](how-to-set-up-training-targets.md), użyj zasobu COMPUTE.
* [Samouczek: uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Dowiedz się, jak [efektywnie dostrajać parametry](how-to-tune-hyperparameters.md) , aby tworzyć lepsze modele.
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* [Używanie Azure Machine Learning z sieciami wirtualnymi platformy Azure](./how-to-network-security-overview.md)