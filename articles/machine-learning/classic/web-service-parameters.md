---
title: 'ML Studio (klasyczny): parametry usługi sieci Web — Azure'
description: Jak używać parametrów usługi sieci Web Azure Machine Learning, aby modyfikować zachowanie modelu podczas uzyskiwania dostępu do usługi sieci Web.
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: how-to
ms.date: 01/12/2017
ms.openlocfilehash: 1a4e4e8f97f543132e4295cca59312b0e78a6637
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362840"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Użyj Azure Machine Learning Studio (klasyczne) parametrów usługi sieci Web

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Usługa sieci Web Azure Machine Learning jest tworzona przez opublikowanie eksperymentu zawierającego moduły z konfigurowalnymi parametrami. W niektórych przypadkach warto zmienić zachowanie modułu, gdy usługa sieci Web jest uruchomiona. *Parametry usługi sieci Web* umożliwiają wykonanie tego zadania. 

Typowym przykładem jest skonfigurowanie modułu [Importuj dane][reader] , aby użytkownik opublikowanej usługi sieci Web mógł określić inne źródło danych podczas uzyskiwania dostępu do usługi sieci Web. Lub skonfiguruj moduł [eksportu danych][writer] , aby można było określić inną lokalizację docelową. Niektóre inne przykłady obejmują zmianę liczby bitów dla modułu [wyznaczania wartości skrótu funkcji][feature-hashing] lub liczby pożądanych funkcji modułu [wyboru funkcji opartych na filtrach][filter-based-feature-selection] . 

Można ustawić parametry usługi sieci Web i skojarzyć je z jednym lub wieloma parametrami modułu w eksperymentie, a także określić, czy są one wymagane, czy opcjonalne. Użytkownik usługi sieci Web może następnie podać wartości tych parametrów podczas wywoływania usługi sieci Web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Jak ustawić i użyć parametrów usługi sieci Web
Należy zdefiniować parametr usługi sieci Web, klikając ikonę obok parametru modułu i wybierając pozycję "Ustaw jako parametr usługi sieci Web". Spowoduje to utworzenie nowego parametru usługi sieci Web i połączenie go z tym parametrem modułu. Po uzyskaniu dostępu do usługi sieci Web użytkownik może określić wartość parametru usługi sieci Web i zostanie zastosowany do parametru modułu.

Po zdefiniowaniu parametru usługi sieci Web jest on dostępny dla każdego innego parametru modułu w eksperymentie. Jeśli określisz parametr usługi sieci Web skojarzony z parametrem dla jednego modułu, możesz użyć tego samego parametru usługi sieci Web dla każdego innego modułu, o ile parametr oczekuje tego samego typu wartości. Na przykład jeśli parametr usługi sieci Web jest wartością numeryczną, może być używany tylko w przypadku parametrów modułu, które oczekują wartości liczbowej. Gdy użytkownik ustawi wartość dla parametru usługi sieci Web, zostanie on zastosowany do wszystkich parametrów modułu skojarzonego.

Można zdecydować, czy podać wartość domyślną dla parametru usługi sieci Web. Jeśli to zrobisz, parametr jest opcjonalny dla użytkownika usługi sieci Web. Jeśli nie podano wartości domyślnej, użytkownik musi wprowadzić wartość podczas uzyskiwania dostępu do usługi sieci Web.

Dokumentacja interfejsu API dla usługi sieci Web zawiera informacje dotyczące użytkownika usługi sieci Web, w którym można programowo określić parametr usługi sieci Web podczas uzyskiwania dostępu do usługi sieci Web.

> [!NOTE]
> Dokumentacja interfejsu API dla klasycznej usługi sieci Web jest dostępna za pomocą linku **Strona pomocy interfejsu API** na **pulpicie nawigacyjnym** usługi sieci Web w Machine Learning Studio (klasyczny). Dokumentacja interfejsu API dla nowej usługi sieci Web jest dostępna za pomocą portalu [usług sieci web Azure Machine Learning](https://services.azureml.net/Quickstart) na stronach **użycia** i **interfejsu API struktury Swagger** dla usługi sieci Web.
> 
> 

## <a name="example"></a>Przykład
Załóżmy na przykład, że mamy eksperyment z modułem [danych eksportu][writer] , który wysyła informacje do usługi Azure Blob Storage. Zdefiniujemy parametr usługi sieci Web o nazwie "Ścieżka obiektu BLOB", który umożliwia użytkownikowi usługi sieci Web zmianę ścieżki do magazynu obiektów BLOB w przypadku uzyskiwania dostępu do usługi.

1. W Machine Learning Studio (klasyczny) kliknij moduł [Eksportuj dane][writer] , aby go wybrać. Jego właściwości są wyświetlane w okienku właściwości po prawej stronie kanwy eksperymentu.
2. Określ typ magazynu:
   
   * W obszarze **określ miejsce docelowe danych**wybierz pozycję "Azure Blob Storage".
   * W obszarze **Określ typ uwierzytelniania**wybierz pozycję "konto".
   * Wprowadź informacje o koncie dla magazynu obiektów blob platformy Azure. 

3. Kliknij ikonę z prawej strony **ścieżki do obiektu BLOB rozpoczynającego się od parametru kontenera**. Wygląda ona następująco:
   
   ![Ikona parametru usługi sieci Web](./media/web-service-parameters/icon.png)
   
   Wybierz pozycję "Ustaw jako parametr usługi sieci Web".
   
   Wpis jest dodawany w obszarze **Parametry usługi sieci Web** u dołu okienka właściwości o nazwie "ścieżka do obiektu BLOB rozpoczynającego się od kontenera". Jest to parametr usługi sieci Web, który jest teraz skojarzony z tym parametrem modułu [eksportowania danych][writer] .
4. Aby zmienić nazwę parametru usługi sieci Web, kliknij nazwę, wprowadź "Ścieżka obiektu BLOB" i naciśnij klawisz **Enter** . 
5. Aby podać wartość domyślną dla parametru usługi sieci Web, kliknij ikonę z prawej strony nazwy, wybierz opcję "Podaj wartość domyślną", wprowadź wartość (na przykład "container1/output1.csv"), a następnie naciśnij klawisz **Enter** .
   
   ![Parametr usługi sieci Web](./media/web-service-parameters/parameter.png)
6. Kliknij przycisk **Uruchom**. 
7. Kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [klasyczny]** lub **Wdróż usługę sieci Web [New]** , aby wdrożyć usługę sieci Web.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci Web, musisz mieć wystarczające uprawnienia w subskrypcji, w której wdrażana jest usługa sieci Web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci web Azure Machine Learning](manage-new-webservice.md). 

Użytkownik usługi sieci Web może teraz określić nowe miejsce docelowe dla modułu [eksportu danych][writer] podczas uzyskiwania dostępu do usługi sieci Web.

## <a name="more-information"></a>Więcej informacji
Aby zapoznać się z bardziej szczegółowym przykładem, zobacz wpis [Parameters usługi sieci Web](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) w [blogu Machine Learning](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci Web Machine Learning, zobacz [jak korzystać z usługi sieci web Azure Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

