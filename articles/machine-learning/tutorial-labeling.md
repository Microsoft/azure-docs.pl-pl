---
title: 'Samouczek: tworzenie projektu etykietowania na celu klasyfikację obrazów'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zarządzać procesem etykietowania obrazów, aby można było ich używać w wieloklasowych modelach klasyfikacji obrazów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 41e93584937ca10740e9ee0be3353d1edf5efb3e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587684"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Samouczek: tworzenie projektu etykietowania na celu klasyfikację obrazów wieloklasowych 


W tym samouczku pokazano, jak zarządzać procesem etykietowania obrazów (nazywanych również tagami), które będą używane jako dane do tworzenia modeli uczenia maszynowego. Etykietowanie danych w Azure Machine Learning jest w publicznej wersji zapoznawczej.

Jeśli chcesz trenować model uczenia maszynowego do klasyfikowania obrazów, potrzebujesz setek, a nawet tysięcy obrazów, które są poprawnie oznaczone etykietami.  Azure Machine Learning ułatwia zarządzanie postępem prywatnego zespołu ekspertów z dziedziny, gdy etykietują dane.
 
W tym samouczku użyjesz obrazów kotów i psów.  Ponieważ każdy obraz jest kotem lub psem, jest to *wieloklasowy* projekt etykietowania. Omawiane tematy:

> [!div class="checklist"]
>
> * Utwórz konto usługi Azure Storage i przekaż obrazy do konta.
> * Tworzenie Azure Machine Learning roboczego.
> * Utwórz wieloklasowy projekt etykietowania obrazów.
> * Oznacz etykietami dane.  To zadanie może wykonać Ty lub Twoi etykietowanie.
> * Ukończ projekt, przeglądając i eksportując dane.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar Azure Machine Learning to zasób w chmurze, który umożliwia eksperymentowanie, trenowanie i wdrażanie modeli uczenia maszynowego. Łączy ona subskrypcję platformy Azure i grupę zasobów z łatwym w użyciu obiektem w usłudze.

Istnieje wiele [sposobów tworzenia obszaru roboczego.](how-to-manage-workspace.md) W tym samouczku utworzysz obszar roboczy za pośrednictwem konsoli Azure Portal internetowej do zarządzania zasobami platformy Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Uruchamianie projektu etykietowania

Następnie będziesz zarządzać projektem etykietowania danych w Azure Machine Learning studio, skonsolidowanym interfejsie, który zawiera narzędzia uczenia maszynowego do wykonywania scenariuszy nauki o danych dla praktyków nauki o danych o wszystkich poziomach umiejętności. Studio nie jest obsługiwane w Internet Explorer przeglądarkach.

1. Zaloguj się do [Azure Machine Learning studio](https://ml.azure.com).

1. Wybierz subskrypcję i utworzony obszar roboczy.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Tworzenie magazynu danych

Azure Machine Learning magazyny danych są używane do przechowywania informacji o połączeniu, takich jak identyfikator subskrypcji i autoryzacja tokenu. W tym miejscu użyjemy magazynu danych do nawiązania połączenia z kontem magazynu zawierającym obrazy z tego samouczka.

1. Po lewej stronie obszaru roboczego wybierz pozycję **Magazyny danych.**

1. Wybierz **pozycję + Nowy magazyn danych.**

1. Wypełnij formularz przy użyciu tych ustawień:

    Pole|Opis 
    ---|---
    Nazwa magazynu danych | Nadaj nazwę magazynowi danych.  W tym miejscu **używamy labeling_tutorial**.
    Typ magazynu danych | Wybierz typ magazynu.  W tym miejscu **używamy Azure Blob Storage**, preferowanego magazynu dla obrazów.
    Metoda wyboru konta | Wybierz **pozycję Wprowadź ręcznie.**
    Adres URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Typ uwierzytelniania | Wybierz pozycję **Token SAS.**
    Klucz konta | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Wybierz **pozycję Utwórz,** aby utworzyć magazyn danych.

### <a name="create-a-labeling-project"></a>Tworzenie projektu etykietowania

Teraz, gdy masz dostęp do danych, które mają być oznaczone etykietami, utwórz projekt etykietowania.

1. W górnej części strony wybierz pozycję **Projekty**.

1. Wybierz **pozycję + Dodaj projekt.**

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Tworzenie projektu":::

### <a name="project-details"></a>Szczegóły projektu

1. Użyj następujących danych wejściowych dla **formularza Szczegóły** projektu:

    Pole|Opis 
    ---|---
    Project name (Nazwa projektu) | Nadaj projektowi nazwę.  W tym miejscu użyjemy **tutorial-cats-n-dogs.**
    Etykietowanie typu zadania | Wybierz **pozycję Klasyfikacja obrazów — wiele klas.**  
    
    Wybierz **przycisk Dalej,** aby kontynuować tworzenie projektu.

### <a name="select-or-create-a-dataset"></a>Wybieranie lub tworzenie zestawu danych

1.   W **formularzu Wybierz lub utwórz zestaw danych** wybierz drugi wybór: Utwórz zestaw danych, **a** następnie wybierz link From datastore (Z **magazynu danych).**

1. Użyj następujących danych wejściowych dla formularza **Tworzenie zestawu danych z magazynu** danych:

    1. W **formularzu Informacje podstawowe** dodaj nazwę. W tym miejscu użyjemy nazwy **images-for-tutorial.**  Jeśli chcesz, dodaj opis.  Następnie wybierz przycisk **Dalej**.
    1. W **formularzu wyboru Magazyn danych** wybierz pozycję Utworzony wcześniej magazyn **danych,** a następnie kliknij nazwę magazynu danych i wybierz **pozycję Wybierz magazyn danych.**
    1. Na następnej stronie sprawdź, czy aktualnie wybrany magazyn danych jest poprawny. Jeśli nie, wybierz **pozycję Wcześniej utworzony magazyn danych** i powtórz poprzedni krok.
    1. Następnie nadal w formularzu **wyboru magazynu** danych wybierz pozycję **Przeglądaj,** a następnie wybierz pozycję **MultiClass — DogsCats.**  Wybierz **pozycję Zapisz,** aby **użyć ścieżki /MultiClass — DogsCats.**
    1. Wybierz **przycisk Dalej,** aby potwierdzić szczegóły, a następnie **pozycję Utwórz,** aby utworzyć zestaw danych.
    1. Wybierz okrąg obok nazwy zestawu danych na liście, na przykład **images-for-tutorial**.

1. Wybierz **przycisk Dalej,** aby kontynuować tworzenie projektu.

### <a name="incremental-refresh"></a>Odświeżanie przyrostowe

Jeśli planujesz dodać nowe obrazy do zestawu danych, odświeżanie przyrostowe znajdzie te nowe obrazy i doda je do projektu.  Po włączeniu tej funkcji projekt będzie okresowo sprawdzać, czy są nowe obrazy.  W tym samouczku nie będziesz dodawać nowych obrazów do magazynu danych, więc pozostaw tę funkcję niezaznaczone.

Wybierz przycisk **Dalej**, aby kontynuować.

### <a name="label-classes"></a>Klasy etykiet

1. W **formularzu Klasy etykiet** wpisz nazwę etykiety, a następnie wybierz **pozycję +Dodaj** etykietę, aby wpisać następną etykietę.  W tym projekcie etykiety to **Cat**, **Dog** i **Niewiadome.**

1. Po **dodaniu** wszystkich etykiet wybierz przycisk Dalej.

### <a name="labeling-instructions"></a>Instrukcje dotyczące etykietowania

1. Na **formularzu Instrukcje dotyczące etykietowania** możesz podać link do witryny internetowej, która zawiera szczegółowe instrukcje dla etykieterów.  W tym samouczku pozostawimy to pole puste.

1. Możesz również dodać krótki opis zadania bezpośrednio w formularzu.  Samouczek **dotyczący etykietowania typów — Cats & Dogs.**

1. Wybierz opcję **Dalej**.

1. W sekcji **Etykietowanie wspomagane przez ml** pozostaw pole wyboru niezaznaczone. Etykietowanie wspomagane ml wymaga większej liczby danych, niż będziesz używać w tym samouczku.

1. Wybierz pozycję **Create project** (Utwórz projekt).

Ta strona nie jest automatycznie odświeżana. Po wstrzymaniu odśwież stronę ręcznie, aż stan projektu zmieni się na **Utworzono.**

## <a name="start-labeling"></a>Rozpoczynanie etykietowania

Masz teraz skonfigurowane zasoby platformy Azure i projekt etykietowania danych. Na teraz należy dodać etykiety do danych.

### <a name="tag-the-images"></a>Tagowanie obrazów

W tej części samouczka przełączysz role z *administratora* projektu na rolę etykietcy *.*  Każda osoba, która ma dostęp współautora do Twojego obszaru roboczego, może zostać etykietą.

1. W [Machine Learning Studio](https://ml.azure.com)wybierz pozycję **Etykiety** danych po lewej stronie, aby znaleźć projekt.  

1. Wybierz **link Etykieta** dla projektu.

1. Przeczytaj instrukcje, a następnie wybierz **pozycję Zadania.**

1. Wybierz miniaturę po prawej stronie, aby wyświetlić liczbę obrazów, które chcesz za jednym przyciskiem oznaczać etykietami. Wszystkie te obrazy należy oznaczać etykietami, zanim będzie można przejść dalej. Układy można przełączać tylko wtedy, gdy masz nową stronę danych bez etykiet. Przełączanie układów wyczyści pracę tagowania w toku strony.

1. Wybierz co najmniej jeden obraz, a następnie wybierz tag do zastosowania do zaznaczenia. Tag zostanie wyświetlony poniżej obrazu.  Kontynuuj zaznaczanie i tagowanie wszystkich obrazów na stronie.  Aby wybrać wszystkie wyświetlane obrazy jednocześnie, wybierz **pozycję Zaznacz wszystkie.** Wybierz co najmniej jeden obraz, aby zastosować tag.


    > [!TIP]
    > Pierwsze dziewięć tagów można wybrać przy użyciu klawiszy liczbowych na klawiaturze.

1. Po otagowaniu wszystkich obrazów na stronie wybierz pozycję **Prześlij,** aby przesłać te etykiety.

    ![Tagowanie obrazów](media/tutorial-labeling/catsndogs.gif)

1. Po przesłaniu tagów dla danych platforma Azure odświeża stronę przy użyciu nowego zestawu obrazów z kolejki służbowej.

## <a name="complete-the-project"></a>Ukończenie projektu

Teraz przełączysz role z powrotem do *administratora projektu* w projekcie etykietowania.

Jako menedżer możesz chcieć przejrzeć pracę etykiety.  

### <a name="review-labeled-data"></a>Przeglądanie danych oznaczonych etykietami

1. W [Machine Learning studio](https://ml.azure.com)wybierz pozycję **Etykiety** danych po lewej stronie, aby znaleźć projekt.  

1. Wybierz link nazwy projektu.

1. Pulpit nawigacyjny pokazuje postęp projektu.

1. W górnej części strony wybierz pozycję **Dane**.

1. Po lewej stronie wybierz pozycję **Dane z etykietami,** aby wyświetlić oznakowane obrazy.  

1. Jeśli nie zgadzasz się z etykietą, wybierz obraz, a następnie wybierz pozycję **Odrzuć** w dolnej części strony.  Tagi zostaną usunięte, a obraz zostanie ponownie umieścić w kolejce obrazów bez etykiet.

### <a name="export-labeled-data"></a>Eksportowanie danych z etykietami

Dane etykiety można wyeksportować do Machine Learning eksperymentowania w dowolnym momencie. Użytkownicy często eksportują dane wielokrotnie i trenują różne modele, zamiast czekać na etykietę wszystkich obrazów.

Etykiety obrazów można eksportować w formacie [COCO](http://cocodataset.org/#format-data) lub jako Azure Machine Learning danych. Format zestawu danych ułatwia trenowania w Azure Machine Learning.  

1. W [Machine Learning studio](https://ml.azure.com)wybierz pozycję **Etykiety** danych po lewej stronie, aby znaleźć projekt.  

1. Wybierz link nazwy projektu.

1. Wybierz pozycję **Eksportuj** i wybierz **pozycję Eksportuj jako zestaw danych usługi Azure ML.** 

    Stan eksportu jest wyświetlany tuż poniżej **przycisku** Eksportuj. 

1. Po pomyślnym wyeksportowania etykiet wybierz pozycję **Zestawy danych** po lewej stronie, aby wyświetlić wyniki.

## <a name="clean-up-resources"></a>Czyszczenie zasobów


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Trenowanie modelu rozpoznawania obrazów uczenia maszynowego.](/azure/machine-learning/how-to-use-labeled-dataset)

