---
title: Tworzenie plików w obszarze roboczym i zarządzanie nimi
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć pliki w obszarze roboczym w programie Azure Machine Learning Studio i zarządzać nimi.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100101589"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Jak tworzyć pliki w obszarze roboczym i zarządzać nimi

Dowiedz się, jak tworzyć pliki w obszarze roboczym Azure Machine Learning i zarządzać nimi.  Te pliki są przechowywane w domyślnym magazynie obszarów roboczych. Pliki i foldery można udostępniać innym osobom z dostępem do odczytu do obszaru roboczego i mogą być używane z dowolnego wystąpienia obliczeniowego w obszarze roboczym.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Utwórz pliki

Aby utworzyć nowy plik w folderze domyślnym ( `Users > yourname` ):

1. Otwórz obszar roboczy w programie [Azure Machine Learning Studio](https://ml.azure.com).
1. Po lewej stronie wybierz pozycję **notesy**.
1. Wybierz **+** obraz.
1. Wybierz obraz  **Utwórz nowy plik** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Utwórz nowy plik":::

1. Nazwij plik.
1. Wybierz typ pliku.
1. Wybierz przycisk **Utwórz**.

Notesy i większość typów plików tekstowych są wyświetlane w sekcji Podgląd.  Większość innych typów plików nie jest dostępna w wersji zapoznawczej.

Aby utworzyć nowy plik w innym folderze:
1. Wybierz "..." na końcu folderu
1. Wybierz pozycję **Utwórz nowy plik**.

> [!IMPORTANT]
> Zawartość w notesach i skryptach może potencjalnie odczytywać dane z sesji i uzyskiwać dostęp do danych bez Twojej organizacji na platformie Azure.  Ładuj tylko pliki z zaufanych źródeł. Aby uzyskać więcej informacji, zobacz [Secure Best Practices](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

## <a name="manage-files-with-git"></a>Zarządzanie plikami za pomocą usługi git

[Użyj terminalu wystąpienia obliczeniowego](how-to-access-terminal.md#git) do klonowania repozytoriów Git i zarządzania nimi.

## <a name="clone-samples"></a>Klonowanie próbek

Obszar roboczy zawiera **Przykładowy folder notesów** z notesami, które ułatwiają zapoznanie się z zestawem SDK i służą jako przykłady dla własnych projektów uczenia maszynowego.   Lone te notesy do własnego folderu, aby je uruchamiać i edytować.  

Aby zapoznać się z przykładem, zobacz [Samouczek: Tworzenie pierwszego eksperymentu z](tutorial-1st-experiment-sdk-setup.md#azure)tablicą.

## <a name="share-files"></a>Udostępnianie plików

Skopiuj i wklej adres URL, aby udostępnić plik.  Tylko inni użytkownicy obszaru roboczego mogą uzyskać dostęp do tego adresu URL.  Dowiedz się więcej o [udzielaniu dostępu do obszaru roboczego](how-to-assign-roles.md).

## <a name="delete-a-file"></a>Usuwanie pliku

*Nie* można usunąć **przykładowych plików notesów** .  Te pliki są częścią Studio i są aktualizowane za każdym razem, gdy nowy zestaw SDK jest publikowany.  

Pliki znajdujące się w sekcji **plików** *można* usunąć w dowolny z następujących sposobów:

* W programie Studio wybierz pozycję **...** na końcu folderu lub pliku.  Upewnij się, że używasz obsługiwanej przeglądarki (Microsoft Edge, Chrome lub Firefox).
* [Użyj terminalu](how-to-access-terminal.md) z dowolnego wystąpienia obliczeniowego w obszarze roboczym. Folder **~/CloudFiles** jest mapowany na magazyn na koncie magazynu obszaru roboczego.
* W Jupyter lub JupyterLab z narzędziami.
