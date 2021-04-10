---
title: 'ML Studio (klasyczny): Migruj do usługi sieci Web do odbudowy Azure Machine Learning'
description: Kompiluj ponownie usługi sieci Web programu Studio (klasyczne) jako punkty końcowe potoku w Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565247"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Ponowne kompilowanie usługi sieci Web programu Studio (klasycznej) w Azure Machine Learning

W tym artykule dowiesz się, jak ponownie skompilować usługę sieci Web programu Studio (klasyczną) jako **punkt końcowy** w Azure Machine Learning.

Użyj punktów końcowych potoku Azure Machine Learning, aby tworzyć przewidywania, reciągać modele lub uruchamiać dowolny potok ogólny. Punkt końcowy REST umożliwia uruchamianie potoków z dowolnej platformy. 

Ten artykuł jest częścią Studio (klasyczną) do Azure Machine Learning serii migracji. Aby uzyskać więcej informacji na temat migracji do Azure Machine Learning, zobacz [artykuł Omówienie migracji](migrate-overview.md).

> [!NOTE]
> Ta seria migracji koncentruje się na projektancie przeciągania i upuszczania. Aby uzyskać więcej informacji na temat programistycznego wdrażania modeli, zobacz [Wdrażanie modeli uczenia maszynowego na platformie Azure](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Obszar roboczy usługi Azure Machine Learning. [Utwórz obszar roboczy Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Potoku szkolenia Azure Machine Learning. Aby uzyskać więcej informacji, zobacz temat [Odbudowywanie eksperymentu programu Studio (klasycznego) w Azure Machine Learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Punkt końcowy w czasie rzeczywistym programu vs

Usługi sieci Web programu Studio (klasyczne) zostały zastąpione przez **punkty końcowe** w Azure Machine Learning. Skorzystaj z poniższej tabeli, aby wybrać typ punktu końcowego, który ma być używany:

|Studio (klasyczna) usługa sieci Web| Zastępowanie Azure Machine Learning
|---|---|
|Usługa sieci Web żądania/reagowania (przewidywania w czasie rzeczywistym)|Punkt końcowy w czasie rzeczywistym|
|Usługa sieci Web usługi Batch (prognoza wsadowa)|Punkt końcowy potoku|
|Przeszkolenie usługi sieci Web (przeszkolenie)|Punkt końcowy potoku| 


## <a name="deploy-a-real-time-endpoint"></a>Wdrażanie punktu końcowego w czasie rzeczywistym

W programie Studio (klasyczny) użyto **usługi sieci Web żądanie/odpowiedź** do wdrożenia modelu dla prognoz w czasie rzeczywistym. W Azure Machine Learning używany jest **punkt końcowy w czasie rzeczywistym**.

Istnieje wiele sposobów wdrażania modelu w Azure Machine Learning. Jednym z najprostszych sposobów jest użycie projektanta w celu zautomatyzowania procesu wdrażania. Wykonaj następujące kroki, aby wdrożyć model jako punkt końcowy w czasie rzeczywistym:

1. Uruchom ukończony potok szkoleniowy co najmniej raz.
1. Po zakończeniu przebiegu w górnej części kanwy wybierz pozycję **Utwórz**  >  **potok wnioskowania w czasie rzeczywistym**.

    ![Tworzenie potoku wnioskowania o czasie rzeczywistym](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Projektant konwertuje potok szkoleniowy na potok wnioskowania w czasie rzeczywistym. Podobna konwersja występuje również w programie Studio (klasyczne).

    W projektancie krok konwersji również [rejestruje przeszkolony model w obszarze roboczym Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Wybierz pozycję **Prześlij** , aby uruchomić potok wnioskowania w czasie rzeczywistym i sprawdź, czy działa poprawnie.

1. Po sprawdzeniu potoku wnioskowania wybierz pozycję **Wdróż**.

1. Wprowadź nazwę dla punktu końcowego i typ obliczania.

    W poniższej tabeli opisano opcje obliczeniowe wdrożenia w projektancie:

    | Docelowy zasób obliczeniowy | Sposób użycia | Opis | Tworzenie |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |Wnioskowanie w czasie rzeczywistym|Wdrażanie w dużej skali w środowisku produkcyjnym. Krótki czas odpowiedzi i automatyczne skalowanie usługi.| Utworzone przez użytkownika. Aby uzyskać więcej informacji, zobacz [Tworzenie obiektów docelowych obliczeń](../how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Azure Container Instances ](../how-to-deploy-azure-container-instance.md)|Testowanie lub programowanie | Duże obciążenia oparte na PROCESORAch, które wymagają mniej niż 48 GB pamięci RAM.| Automatycznie utworzone przez Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Testowanie punktu końcowego w czasie rzeczywistym

Po zakończeniu wdrażania możesz zobaczyć więcej szczegółów i przetestować punkt końcowy:

1. Przejdź do karty **punkty końcowe** .
1. Wybierz punkt końcowy.
1. Wybierz kartę **Test**.
    
    ![Zrzut ekranu przedstawiający kartę punkty końcowe z przyciskiem punktu końcowego testu](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Publikowanie punktu końcowego potoku na potrzeby przewidywania lub ponownego szkolenia usługi Batch

Możesz również użyć potoku szkoleniowego, aby utworzyć **punkt końcowy potoku** zamiast punktu końcowego w czasie rzeczywistym. Użyj **punktów końcowych potoku** do przeprowadzenia prognozy lub przeszkolenia wsadowego.

Punkty końcowe potoku zastępują Studio (klasyczne) **punkty końcowe wykonywania**  i **przeszkolenie usług sieci Web**.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Publikowanie punktu końcowego potoku na potrzeby prognozowania partii

Publikowanie punktu końcowego przewidywania partii jest podobne do punktu końcowego w czasie rzeczywistym.

Wykonaj następujące kroki, aby opublikować punkt końcowy potoku na potrzeby prognozowania partii:

1. Uruchom ukończony potok szkoleniowy co najmniej raz.

1. Po zakończeniu przebiegu w górnej części kanwy wybierz pozycję Utwórz potok wnioskowania o **potoku**  >  **przetwarzania wsadowego**.

    ![Zrzut ekranu przedstawiający przycisk Utwórz potok wnioskowania w potoku szkoleniowym](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Projektant konwertuje potok szkoleniowy na potok wnioskowania wsadowego. Podobna konwersja występuje również w programie Studio (klasyczne).

    W projektancie ten krok powoduje także [zarejestrowanie przeszkolonego modelu w obszarze roboczym Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Wybierz pozycję **Prześlij** , aby uruchomić potok wnioskowania partii i sprawdzić, czy pomyślnie został ukończony.

1. Po sprawdzeniu potoku wnioskowania wybierz pozycję **Publikuj**.
 
1. Utwórz nowy punkt końcowy potoku lub wybierz istniejący.
    
    Nowy punkt końcowy potoku tworzy nowy punkt końcowy REST dla potoku. 

    W przypadku wybrania istniejącego punktu końcowego potoku nie zastąpi istniejącego potoku. Zamiast tego Azure Machine Learning wersje każdego potoku w punkcie końcowym. Możesz określić, która wersja ma być uruchamiana w wywołaniu REST. Należy również ustawić potok domyślny, jeśli wywołanie REST nie określa wersji.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Publikowanie punktu końcowego potoku w celu przeszkolenia

Aby opublikować punkt końcowy potoku na potrzeby ponownego uczenia, musisz mieć już wersję roboczą potoku, która pociągnie za dany model. Aby uzyskać więcej informacji na temat tworzenia potoku szkoleniowego, zobacz [odtwarzanie eksperymentu programu Studio (klasycznego)](migrate-rebuild-experiment.md).

Aby ponownie użyć punktu końcowego potoku w celu przeszkolenia, należy utworzyć **parametr potoku** dla wejściowego zestawu danych. Dzięki temu można dynamicznie ustawić zestaw danych szkoleniowych, aby można było ponownie nauczyć model.

Wykonaj następujące kroki, aby opublikować punkt końcowy potoku ponownego szkolenia:

1. Uruchom potok szkoleniowy co najmniej raz.
1. Po zakończeniu przebiegu wybierz moduł DataSet.
1. W okienku Szczegóły modułu wybierz pozycję **Ustaw jako parametr potoku**.
1. Podaj nazwę opisową, taką jak "InputDataset".    

    ![Zrzut ekranu przedstawiający sposób tworzenia parametru potoku](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Spowoduje to utworzenie parametru potoku dla wejściowego zestawu danych. Gdy wywołasz punkt końcowy potoku w celu szkolenia, możesz określić nowy zestaw danych, aby ponownie przeprowadzić uczenie modelu.

1. Kliknij pozycję **Opublikuj**.

    ![Zrzut ekranu z wyróżnionym przyciskiem Publikuj w potoku szkolenia](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Wywoływanie punktu końcowego potoku z poziomu programu Studio

Po utworzeniu punktu końcowego wnioskowania o przeprowadzeniu lub przeszkoleniu partii można wywołać punkt końcowy bezpośrednio z przeglądarki.

1. Przejdź do karty **potoki** , a następnie wybierz pozycję **punkty końcowe potoku**.
1. Wybierz punkt końcowy potoku, który chcesz uruchomić.
1. Wybierz pozycję **Prześlij**.

    Po wybraniu opcji **Prześlij** można określić dowolne parametry potoku.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób odbudowywania usługi sieci Web programu Studio (klasycznej) w Azure Machine Learning. Następnym krokiem jest [zintegrowanie usługi sieci Web z aplikacjami klienckimi](migrate-rebuild-integrate-with-client-app.md).


Zapoznaj się z innymi artykułami z serii migracji programu Studio (klasycznej):

1. [Omówienie migracji](migrate-overview.md).
1. [Migrowanie zestawu danych](migrate-register-dataset.md).
1. [Kompiluj potok szkoleniowy Studio (klasyczny)](migrate-rebuild-experiment.md).
1. **Kompiluj ponownie usługę sieci Web programu Studio (klasyczna)**.
1. [Zintegruj usługę sieci web Azure Machine Learning z aplikacjami klienckimi](migrate-rebuild-integrate-with-client-app.md).
1. [Migruj skrypt wykonania skryptu języka R](migrate-execute-r-script.md).
