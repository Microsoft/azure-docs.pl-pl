---
title: 'Szybki Start: Tworzenie modelu kotwic obiektów, który ma być używany w aplikacji'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć model kotwic obiektów z modelu 3W.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607904"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Szybki Start: Tworzenie modelu kotwic obiektów z modelu 3D

Kotwice obiektów platformy Azure to zarządzana usługa w chmurze, która konwertuje modele 3D na modele AI, które umożliwiają korzystanie z rzeczywistości mieszanej opartej na obiektach dla urządzeń HoloLens. W tym przewodniku szybki start opisano sposób tworzenia modelu kotwic obiektów z modelu 3D przy użyciu zestawu SDK języka C#/.NET Core.

Omawiane tematy:

> [!div class="checklist"]
> * Utwórz konto kotwic obiektów
> * Konwertowanie modelu 3D w celu utworzenia modelu kotwic obiektów

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Maszyna z systemem Windows z programem <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git dla systemu Windows</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">Zestaw SDK platformy .NET Core 3,1</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Utwórz konto kotwic obiektów

Najpierw należy utworzyć konto z usługą kotwice obiektów.

1. Przejdź do [Azure Portal](https://portal.azure.com/) i wybierz pozycję **Utwórz zasób**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Utwórz nowy zasób":::

2. Wyszukaj zasób **kotwicy obiektu** .

   Wyszukaj frazę "kotwice obiektów".

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Wybierz zasób zakotwiczenia obiektu":::

   W obszarze zasoby **obiektów zakotwiczenia** w wynikach wyszukiwania wybierz pozycję **Utwórz-> kotwice obiektów**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Tworzenie zasobu zakotwiczenia obiektu":::

3. W oknie dialogowym **konto zakotwiczenia obiektów** :
    * Wprowadź unikatową nazwę zasobu.
    * Wybierz subskrypcję, do której chcesz dołączyć zasób.
    * Utwórz lub Użyj istniejącej grupy zasobów.
    * Wybierz region, w którym ma się znajdować zasób.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Wprowadź szczegóły konta zasobu zakotwiczenia obiektów":::

    Wybierz pozycję **Utwórz** , aby rozpocząć tworzenie zasobu.

4. Po utworzeniu zasobu wybierz pozycję **Przejdź do zasobu**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Przejdź do zasobu":::

5. Na stronie przegląd:

   Zanotuj **domenę konta**. Będzie on potrzebny później.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Skopiuj domenę konta dla zasobu zakotwiczenia obiektów":::

   Zanotuj **Identyfikator konta**. Będzie on potrzebny później.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Skopiuj identyfikator konta dla zasobu zakotwiczenia obiektu":::

   Przejdź do strony **klucze dostępu** i zanotuj **klucz podstawowy**. Będzie on potrzebny później.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Skopiuj klucz konta dla zasobu zakotwiczenia obiektów":::

## <a name="get-the-sample-project"></a>Pobierz przykładowy projekt

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Konwertowanie modelu 3W

Teraz możesz wykonać konwersję modelu 3W.

1. Otwórz plik `quickstarts/conversion/Conversion.sln` w programie Visual Studio. To rozwiązanie zawiera projekt konsoli języka C#.

2. Otwórz `Configuration.cs` plik znajdujący się w katalogu głównym projektu i Zastąp `set-me` wartości w następujących polach:

   | Pole         | Opis                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | **Domena konta** zakotwiczenia obiektu utworzonego powyżej. |
   | AccountId     | **Identyfikator konta** zakotwiczenia obiektu utworzonego powyżej.     |
   | AccountKey    | **Klucz podstawowy** konta kotwicy obiektu utworzonego powyżej     |

   Istnieją cztery dodatkowe pola, które należy zweryfikować:

    | Pole                    | Opis                       |
    | ---                      | ---                               |
    | InputAssetPath           | Ścieżka bezwzględna do modelu 3D na komputerze lokalnym. Obsługiwane formaty plików to `fbx` ,,, `ply` `obj` `glb` i `gltf` . |
    | AssetDimensionUnit       | Jednostka miary modelu 3W. Dostęp do wszystkich obsługiwanych jednostek miary można uzyskać przy użyciu `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` wyliczenia. |
    | Ciężkości                  | Kierunek wektora ciężkości modelu 3W. Ten wektor 3W zapewnia kierunek w dół w układzie współrzędnych modelu. Na przykład jeśli `y` wartość ujemna reprezentuje kierunek w dół w miejscu trójwymiarowym modelu, to powinna być `Vector3(0.0f, -1.0f, 0.0f)` . |

3. Skompiluj i Uruchom projekt, aby przekazać model 3W, zarejestrować nowe zadanie konwersji w usłudze i poczekać na jego zakończenie. Po zakończeniu zadania model kotwic obiektów zostanie pobrany obok pliku określonego w `InputAssetPath` . Powinien zostać wyświetlony komunikat podobny do następującego:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Zanotuj **Identyfikator zadania** do użycia w przyszłości. Może być przydatne podczas debugowania lub rozwiązywania problemów.

4. Po pomyślnym ukończeniu zadania powinien zostać wyświetlony plik z formatem `<Model-Filename-Without-Extension>_<JobID>.ou` w określonej lokalizacji wyjściowej. Na przykład, jeśli nazwa pliku modelu 3D to, `chair.ply` a identyfikator zadania to `00000000-0000-0000-0000-000000000000` Nazwa pliku, będzie ona zawierać dane wyjściowe usługi `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono konto kotwic obiektów i przeprowadzono konwersję modelu 3D w celu utworzenia modelu kotwic obiektu. Aby dowiedzieć się, jak zintegrować ten model z ZESTAWem zakotwiczenia obiektów w aplikacji rzeczywistości mieszanej, przejdź do dowolnego z następujących artykułów:

> [!div class="nextstepaction"]
> [Unity — HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens z MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Urządzenie HoloLens DirectX](get-started-hololens-directx.md)
