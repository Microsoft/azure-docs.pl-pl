---
title: 'Samouczek: Tworzenie projektu etykietowania na potrzeby klasyfikacji obrazów'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zarządzać procesem etykietowania obrazów, aby można było ich używać w modelach klasyfikacji obrazów wieloklasowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 238801171d19191bcac505f0c95395320f96d75c
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882470"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Samouczek: Tworzenie projektu etykietowania dla wieloklasowej klasyfikacji obrazów 


W tym samouczku pokazano, jak zarządzać procesem etykietowania (nazywanymi również znakami oznakowania), które mają być używane jako dane do kompilowania modeli uczenia maszynowego. Etykietowanie danych w Azure Machine Learning jest w publicznej wersji zapoznawczej.

Jeśli chcesz przeprowadzić uczenie modelu uczenia maszynowego do klasyfikowania obrazów, potrzebne są setki lub nawet tysiące obrazów, które są poprawnie oznaczone etykietami.  Azure Machine Learning ułatwia zarządzanie postępem prywatnych zespołów ekspertów domeny w miarę ich etykietowania.
 
W tym samouczku będziesz używać obrazów kotów i psów.  Ponieważ każdy obraz to kot lub pies, jest to *wieloklasowy* projekt etykietowania. Omawiane tematy:

> [!div class="checklist"]
>
> * Utwórz konto usługi Azure Storage i przekaż obrazy do konta.
> * Utwórz obszar roboczy Azure Machine Learning.
> * Utwórz projekt z wieloklasowym obrazem etykiet.
> * Oznacz dane.  Ty lub Twoje etykiety mogą wykonać to zadanie.
> * Ukończ projekt, przeglądając i eksportując dane.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze.

Istnieje wiele [sposobów tworzenia obszaru roboczego](how-to-manage-workspace.md). W tym samouczku utworzysz obszar roboczy za pośrednictwem Azure Portalej konsoli internetowej do zarządzania zasobami platformy Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Uruchom projekt etykietowania

Następnie będziesz zarządzać projektem etykietowania danych w programie Azure Machine Learning Studio, skonsolidowanym interfejsem, który obejmuje narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych o wszystkich poziomach umiejętności. Program Studio nie jest obsługiwany w przeglądarkach programu Internet Explorer.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Tworzenie magazynu danych

Magazyny danych Azure Machine Learning są używane do przechowywania informacji o połączeniu, takich jak identyfikator subskrypcji i autoryzacja tokenu. W tym miejscu jest używany magazyn danych do łączenia się z kontem magazynu zawierającym obrazy dla tego samouczka.

1. Z lewej strony obszaru roboczego wybierz pozycję **magazyny** danych.

1. Wybierz pozycję **+ nowy magazyn** danych.

1. Wypełnij formularz tymi ustawieniami:

    Pole|Opis 
    ---|---
    Nazwa magazynu danych | Nadaj nazwę magazynowi danych.  Tutaj używamy **labeling_tutorial**.
    Typ magazynu danych | Wybierz typ magazynu.  Tutaj korzystamy z **usługi Azure Blob Storage**, preferowanego magazynu dla obrazów.
    Metoda wyboru konta | Wybierz pozycję **wprowadź ręcznie**.
    Adres URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Typ uwierzytelniania | Wybierz pozycję **token SAS**.
    Klucz konta | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Wybierz pozycję **Utwórz** , aby utworzyć magazyn danych.

### <a name="create-a-labeling-project"></a>Tworzenie projektu etykietowania

Teraz, gdy masz dostęp do danych, które mają mieć etykietę, Utwórz projekt etykietowania.

1. W górnej części strony wybierz pozycję **projekty**.

1. Wybierz pozycję **+ Dodaj projekt**.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Tworzenie projektu":::

### <a name="project-details"></a>Szczegóły projektu

1. Użyj następujących danych wejściowych dla formularza **szczegóły projektu** :

    Pole|Opis 
    ---|---
    Project name (Nazwa projektu) | Nadaj projektowi nazwę.  W tym miejscu zostanie użyty **samouczek-koty-n-psy**.
    Typ zadania etykietowania | Wybierz pozycję **Klasyfikacja obrazu wiele klas**.  
    
    Wybierz pozycję **dalej** , aby kontynuować tworzenie projektu.

### <a name="select-or-create-a-dataset"></a>Wybierz lub Utwórz zestaw danych

1.   W formularzu **Wybierz lub Utwórz zestaw danych** wybierz drugi wybór, **Utwórz zestaw danych**, a następnie wybierz łącze **z magazynu** danych.

1. Użyj następujących danych wejściowych dla formularza **Create DataSet from datastore** :

    1. W formularzu **podstawowe informacje** Dodaj nazwę. w tym miejscu będziemy używać **obrazów na potrzeby samouczka**.  Dodaj opis, jeśli chcesz.  Następnie wybierz przycisk **Dalej**.
    1. W formularzu **wyboru magazynu** danych wybierz **wcześniej utworzony magazyn** danych, a następnie kliknij nazwę magazynu danych i wybierz pozycję **Wybierz magazyn** danych.
    1. Na następnej stronie Sprawdź, czy aktualnie wybrany magazyn danych jest prawidłowy. W przeciwnym razie wybierz **wcześniej utworzony magazyn** danych i Powtórz poprzedni krok.
    1. Następnie w formularzu **wyboru magazynu** danych wybierz pozycję **Przeglądaj** , a następnie wybierz pozycję **wieloclass-DogsCats**.  Wybierz pozycję **Zapisz** , aby użyć **/MULTICLASS-DogsCats** jako ścieżki.
    1. Wybierz pozycję **dalej** , aby potwierdzić szczegóły, a następnie **Utwórz** , aby utworzyć zestaw danych.
    1. Wybierz okrąg obok nazwy zestawu danych na liście, na przykład **obrazy dla samouczka**.

1. Wybierz pozycję **dalej** , aby kontynuować tworzenie projektu.

### <a name="incremental-refresh"></a>Odświeżanie przyrostowe

Jeśli planujesz dodać nowe obrazy do zestawu danych, odświeżanie przyrostowe będzie odszukać te nowe obrazy i dodać je do projektu.  Po włączeniu tej funkcji projekt będzie okresowo sprawdzać, czy są nowe obrazy.  Nie dodasz nowych obrazów do magazynu danych dla tego samouczka, więc nie zaznaczaj tej funkcji.

Wybierz przycisk **Dalej**, aby kontynuować.

### <a name="label-classes"></a>Klasy etykiet

1. W formularzu **klasy etykiet** wpisz nazwę etykiety, a następnie wybierz pozycję **+ Dodaj etykietę** , aby wpisać następną etykietę.  W przypadku tego projektu etykiety to **kot**, **Dog** i **niepewne**.

1. Wybierz pozycję **Next (dalej** ), gdy dodano wszystkie etykiety.

### <a name="labeling-instructions"></a>Instrukcje etykietowania

1. Na formularzu **instrukcji etykietowania** można podać link do witryny sieci Web, która zawiera szczegółowe instrukcje dla etykiet.  Ten samouczek zostanie pozostawiony jako pusty.

1. Możesz również dodać Krótki opis zadania bezpośrednio w formularzu.  **Samouczek typu etykieta — koty & psy.**

1. Wybierz pozycję **Dalej**.

1. W sekcji **etykieta zatytułowana "ml** " pozostaw niezaznaczone pole wyboru. Etykieta z asystą, wymaga większej ilości danych niż ta, która będzie używana w tym samouczku.

1. Wybierz pozycję **Create project** (Utwórz projekt).

Ta strona nie jest automatycznie odświeżana. Po wstrzymaniu ręcznie Odśwież stronę do momentu zmiany stanu projektu na wartość **utworzone**.

## <a name="start-labeling"></a>Rozpocznij etykietowanie

Już skonfigurowano zasoby platformy Azure i skonfigurowano projekt etykietowania danych. Czas na dodanie etykiet do danych.

### <a name="tag-the-images"></a>Oznacz obrazy

W tej części samouczka przełączysz role z *administratorem projektu* na *Labeler*.  Każdy, kto ma dostęp współautora do obszaru roboczego, może zostać Labeler.

1. W programie [Machine Learning Studio](https://ml.azure.com)wybierz pozycję **dane z etykietami** po lewej stronie, aby znaleźć projekt.  

1. Wybierz **łącze etykieta** dla projektu.

1. Przeczytaj instrukcje, a następnie wybierz pozycję **zadania**.

1. Wybierz obraz miniatury po prawej stronie, aby wyświetlić liczbę obrazów, które chcesz oznaczyć w jednym. Aby można było przenieść wszystkie te obrazy, należy je oznaczyć. Przełączaj układy tylko wtedy, gdy masz nową stronę bez etykietowania danych. Przełączenie układu czyści działanie tagowania strony w toku.

1. Wybierz co najmniej jeden obraz, a następnie wybierz tag, który ma zostać zastosowany do zaznaczenia. Tag pojawia się poniżej obrazu.  Kontynuuj wybór i Oznacz wszystkie obrazy na stronie.  Aby jednocześnie zaznaczyć wszystkie wyświetlane obrazy, wybierz pozycję **Zaznacz wszystko**. Wybierz co najmniej jeden obraz, aby zastosować tag.


    > [!TIP]
    > Można wybrać pierwsze dziewięć tagów przy użyciu klawiszy liczbowych na klawiaturze.

1. Po oznakowaniu wszystkich obrazów na stronie wybierz pozycję **Prześlij** , aby przesłać te etykiety.

    ![Tagowanie obrazów](media/tutorial-labeling/catsndogs.gif)

1. Po przesłaniu tagów dla danych, platforma Azure Odświeża stronę nowym zestawem obrazów z kolejki roboczej.

## <a name="complete-the-project"></a>Ukończ projekt

Teraz przełączasz role z powrotem do *administratora projektu* dla projektu etykietowania.

Jako Menedżer możesz chcieć przejrzeć prace Twojego labeleru.  

### <a name="review-labeled-data"></a>Przejrzyj dane z etykietami

1. W programie [Machine Learning Studio](https://ml.azure.com)wybierz pozycję **dane z etykietami** po lewej stronie, aby znaleźć projekt.  

1. Wybierz łącze Nazwa projektu.

1. Pulpit nawigacyjny pokazuje postęp projektu.

1. W górnej części strony wybierz pozycję **dane**.

1. Po lewej stronie wybierz pozycję **dane z etykietami** , aby wyświetlić oznakowane obrazy.  

1. Jeśli zgadzasz się z etykietą, wybierz obraz, a następnie wybierz pozycję **Odrzuć** w dolnej części strony.  Tagi zostaną usunięte, a obraz zostanie umieszczony w kolejce z obrazami nieoznaczonymi etykietami.

### <a name="export-labeled-data"></a>Eksportuj dane z etykietą

W dowolnym momencie możesz wyeksportować dane etykiet dla Machine Learning eksperymentowania. Użytkownicy często eksportują wiele razy i przeszkolią różne modele, zamiast czekać, aż wszystkie obrazy mają etykietę.

Etykiety obrazów można eksportować w [formacie Coco](http://cocodataset.org/#format-data) lub jako zestaw danych Azure Machine Learning. Format zestawu danych ułatwia korzystanie z szkoleń w Azure Machine Learning.  

1. W programie [Machine Learning Studio](https://ml.azure.com)wybierz pozycję **dane z etykietami** po lewej stronie, aby znaleźć projekt.  

1. Wybierz łącze Nazwa projektu.

1. Wybierz pozycję **Eksportuj** i wybierz pozycję **Eksportuj jako zestaw danych usługi Azure ml**. 

    Stan eksportu pojawia się tuż poniżej przycisku **Eksportuj** . 

1. Po pomyślnym wyeksportowaniu etykiet wybierz pozycję **zestawy danych** po lewej stronie, aby wyświetlić wyniki.

## <a name="clean-up-resources"></a>Czyszczenie zasobów


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono etykietki obrazów.  Teraz Użyj danych z etykietami:

> [!div class="nextstepaction"]
> [Uczenie modelu rozpoznawania obrazów uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
