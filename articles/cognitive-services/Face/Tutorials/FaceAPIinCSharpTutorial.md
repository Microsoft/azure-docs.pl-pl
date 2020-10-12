---
title: 'Samouczek: wykrywanie i wyświetlanie danych twarzy na obrazie za pomocą zestawu SDK platformy .NET'
titleSuffix: Azure Cognitive Services
description: W ramach tego samouczka utworzysz aplikację systemu Windows, która korzysta z usługi twarzy do wykrywania i tworzenia klatek na obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 13bbb5e006f725ff0b75a5b86aee414f84a80dcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936303"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Samouczek: Tworzenie aplikacji platformy Windows Presentation Framework (WPF) w celu wyświetlania danych o kroju w obrazie

W tym samouczku dowiesz się, jak korzystać z usługi Azure twarzy przy użyciu zestawu SDK klienta platformy .NET, aby wykrywać twarze w obrazie, a następnie przedstawić te dane w interfejsie użytkownika. Utworzysz aplikację WPF, która wykrywa twarze, Rysuje ramkę dookoła każdej twarzy i wyświetla opis twarzy na pasku stanu. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie aplikacji WPF
> - Instalowanie biblioteki klienta programu Front
> - Korzystanie z biblioteki klienta do wykrywania twarzy na obrazie
> - Rysowanie ramki wokół każdej wykrytej twarzy
> - Wyświetlanie opisu wyróżnionej twarzy na pasku stanu

![Zrzut ekranu przedstawiający wykryte twarze otoczone prostokątami](../Images/getting-started-cs-detected.png)

Kompletny przykładowy kod jest dostępny w repozytorium [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) (Przykład rozpoznawania twarzy w języku C# za pomocą usług Cognitive) w witrynie GitHub.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/). 


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API rozpoznawania twarzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu punktu końcowego klucza i usługi, `FACE_SUBSCRIPTION_KEY` odpowiednio nazwane i `FACE_ENDPOINT` .
- Dowolna wersja programu [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Wykonaj następujące kroki, aby utworzyć nowy projekt aplikacji WPF.

1. W programie Visual Studio otwórz okno dialogowe Nowy projekt. Rozwiń węzeł **Zainstalowane** i węzeł **Visual C#**, a następnie wybierz pozycję **Aplikacja WPF (.NET Framework)**.
1. Nazwij aplikację **FaceTutorial**, a następnie kliknij przycisk **OK**.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**, a następnie znajdź i zainstaluj następujący pakiet:
    - [Microsoft. Azure. CognitiveServices. Vision. 2.6.0 — wersja zapoznawcza. 1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)

## <a name="add-the-initial-code"></a>Dodawanie początkowego kodu

W tej sekcji dodasz podstawową strukturę aplikacji bez funkcji specyficznych dla rozpoznawania twarzy.

### <a name="create-the-ui"></a>Tworzenie interfejsu użytkownika

Otwórz *MainWindow. XAML* i Zastąp zawartość następującym kodem &mdash; ten kod tworzy okno interfejsu użytkownika. `FacePhoto_MouseMove`Metody i `BrowseButton_Click` są programami obsługi zdarzeń, które będą później definiowane.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Tworzenie głównej klasy

Otwórz plik *MainWindow.xaml.cs* i dodaj przestrzenie nazw biblioteki klienta oraz inne wymagane przestrzenie nazw. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Następnie wstaw poniższy kod w klasie **MainWindow**. Ten kod tworzy wystąpienie **FaceClient** przy użyciu klucza subskrypcji i punktu końcowego.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Następnie Dodaj Konstruktor **MainWindow** . Sprawdza ciąg adresu URL punktu końcowego, a następnie kojarzy go z obiektem Client.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Na koniec dodaj do klasy metody **BrowseButton_Click** i **FacePhoto_MouseMove**. Metody te odpowiadają procedurom obsługi zdarzeń zadeklarowanym w *MainWindow. XAML*. Metoda **BrowseButton_Click** tworzy element **OpenFileDialog**, który pozwala użytkownikowi wybrać obraz jpg. Następnie wyświetla ona obraz w głównym oknie. Wstawisz pozostały kod metod **BrowseButton_Click** i **FacePhoto_MouseMove** w kolejnych krokach. Zwróć też uwagę na odwołanie `faceList`&mdash; listę obiektów **DetectedFace**. To odwołanie polega na tym, że aplikacja będzie przechowywać i wywoływać rzeczywiste dane dotyczące kroju.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Testowanie aplikacji

Naciśnij pozycję **Uruchom** w menu, aby przetestować aplikację. Po otwarciu okna aplikacji kliknij pozycję **Browse** (Przeglądaj) w lewym dolnym rogu. Powinno pojawić się okno dialogowe **Otwieranie pliku**. Wybierz obraz z systemu plików i sprawdź, czy został wyświetlony w oknie. Następnie zamknij aplikację i przejdź do kolejnego kroku.

![Zrzut ekranu przedstawiający niezmodyfikowany obraz z twarzami](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Przekazywanie obrazu i wykrywanie twarzy

Aplikacja będzie wykrywać twarze, wywołując metodę **FaceClient.Face.DetectWithStreamAsync**, która opakowuje interfejs API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) na potrzeby przekazywania obrazu lokalnego.

Wstaw następującą metodę w klasie **MainWindow** poniżej metody **FacePhoto_MouseMove**. Ta metoda definiuje listę atrybutów kroju do pobrania i odczytu przesłanego pliku obrazu do **strumienia**. Następnie oba te obiekty zostaną przekazane do wywołania metody **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Rysowanie prostokątów wokół twarzy

Dodasz teraz kod, który będzie rysował prostokąt wokół każdej wykrytej twarzy na obrazie. W klasie **MainWindow** wstaw następujący kod na końcu metody **BrowseButton_Click**, poniżej wiersza `FacePhoto.Source = bitmapSource`. Ten kod wypełnia listę wykrytych twarzy z wywołania do **UploadAndDetectFaces**. Następnie zostanie narysowany prostokąt wokół każdej twarzy, a zmodyfikowany obraz zostanie wyświetlony w głównym oknie.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Opisywanie twarzy

Dodaj następującą metodę do klasy **MainWindow**, poniżej metody **UploadAndDetectFaces**. Ta metoda konwertuje pobrane atrybuty kroju na ciąg opisujący miarę.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Wyświetlanie opisu twarzy

Dodaj następujący kod do metody **FacePhoto_MouseMove**. Ta procedura obsługi zdarzeń wyświetla ciąg opisu twarzy w elemencie `faceDescriptionStatusBar` po zatrzymaniu kursora na prostokącie wykrytej twarzy.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Uruchamianie aplikacji

Uruchom aplikację i przejdź do obrazu zawierającego twarz. Zaczekaj kilka sekund, aby umożliwić usłudze rozpoznawania twarzy udzielenie odpowiedzi. Wokół każdej twarzy na obrazie powinien pojawić się czerwony prostokąt. Gdy przesuniesz wskaźnik myszy na prostokąt otaczający twarz, powinien pojawić się opis tej twarzy na pasku stanu.

![Zrzut ekranu przedstawiający wykryte twarze otoczone prostokątami](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono podstawowy proces korzystania z zestawu SDK platformy .NET dla usługi rozpoznawania twarzy oraz utworzono aplikację do wykrywania i oznaczania ramkami twarzy na obrazie. Teraz dowiedz się więcej o szczegółach wykrywania twarzy.

> [!div class="nextstepaction"]
> [Jak wykrywać twarze na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
