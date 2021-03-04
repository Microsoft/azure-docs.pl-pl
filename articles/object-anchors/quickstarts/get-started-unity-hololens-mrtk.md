---
title: 'Szybki Start: Tworzenie aplikacji HoloLens przy użyciu aparatu Unity i MRTK'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć aplikację aparatu HoloLens Unity przy użyciu MRTK i kotwic obiektów.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 1fd42f7b2da82da17dc19f2a57ea9b64f78f3fe0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049749"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Szybki Start: Tworzenie aplikacji HoloLens przy użyciu kotwic obiektów platformy Azure w środowisku Unity z MRTK

W tym przewodniku szybki start utworzysz aplikację HoloLens Unity, która używa [kotwic obiektów platformy Azure](../overview.md). Kotwice obiektów platformy Azure to zarządzana usługa w chmurze, która konwertuje zasoby 3D na modele AI, które umożliwiają korzystanie z rzeczywistości mieszanej opartej na obiektach dla urządzenia HoloLens. Po zakończeniu będziesz mieć aplikację HoloLens utworzoną przy użyciu aparatu Unity, która może wykrywać obiekty w świecie fizycznym.

Omawiane tematy:

> [!div class="checklist"]
> * Przygotuj ustawienia kompilacji aparatu Unity.
> * Wyeksportuj projekt HoloLens programu Visual Studio.
> * Wdróż aplikację i uruchom ją na urządzeniu HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

W aparacie Unity Otwórz `quickstarts/apps/unity/mrtk` projekt.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Gdy w oknie dialogowym "importer TMP" pojawia się monit o zaimportowanie zasobów typu textsiatk, wybierz opcję "Importuj podstawowe informacje o programie TMP", aby to zrobić.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="Importuj zasoby w postaci textsiatk":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Włącz urządzenie, wybierz pozycję **wszystkie aplikacje**, a następnie Znajdź i uruchom aplikację. Po ekranie powitalnym aparatu Unity zobaczysz białe pole ograniczenia. Możesz użyć swojej dłoni do przenoszenia, skalowania lub obracania pola ograniczenia. Umieść pole, aby pokryć obiekt, który ma zostać wykryty.

Otwórz <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu dłoń</a> i wybierz pozycję **Lock SearchArea** , aby zapobiec dalszemu przeniesieniu pola ograniczenia. Wybierz pozycję **Rozpocznij wyszukiwanie** , aby rozpocząć wykrywanie obiektów. Po wykryciu obiektu siatka będzie renderowana na obiekcie. Szczegóły wykrytego wystąpienia zostaną wyświetlone na ekranie, takie jak zaktualizowana sygnatura czasowa i współczynnik pokrycia powierzchni. Wybierz pozycję **Zatrzymaj wyszukiwanie** , aby zatrzymać śledzenie, a wszystkie wykryte wystąpienia zostaną usunięte.

#### <a name="the-app-menus"></a>Menu aplikacji

Inne akcje można także wykonać przy użyciu <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu rączka</a>.

##### <a name="primary-menu"></a>Menu podstawowe

* **Rozpocznij wyszukiwanie/Zatrzymaj wyszukiwanie** — uruchamia lub zatrzymuje proces wykrywania obiektów.
* **Przełącz mapowanie przestrzenne** — Pokaż/Ukryj renderowanie mapowania przestrzennego. Tej opcji można użyć do debugowania, jeśli skanowanie zostało ukończone.
* **Ustawienia śledzenia** — przełącza aktywację menu ustawienia śledzenia.
* **Ustawienia obszaru wyszukiwania** — przełącza aktywację menu Ustawienia obszaru wyszukiwania.
* **Rozpocznij śledzenie** — Przechwyć dane diagnostyczne i Zapisz je na urządzeniu. Zobacz więcej szczegółów w sekcji **problemy dotyczące wykrywania debugowania i Diagnostyka przechwytywania**.
* **Przekazywanie śledzenia** — przekazywanie danych diagnostycznych do usługi kotwic obiektów. Użytkownik musi podać swoje konto subskrypcji w `subscription.json` usłudze i przekazać go do `LocalState` folderu. Przykładowy `subscription.json` plik można znaleźć poniżej.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Podstawowe menu aparatu Unity":::

##### <a name="tracker-settings-menu"></a>Menu ustawień śledzenia

* **Wysoka dokładność** — eksperymentalna funkcja używana do uzyskania dokładniejszego celu. Włączenie tej opcji będzie wymagało więcej zasobów systemowych podczas wykrywania obiektów. Siatka obiektów będzie renderowana w kolorze różowym w tym trybie. Ponownie wybierz ten przycisk, aby wrócić do trybu normalnego śledzenia.
* **Swobodne wyrównanie w pionie** — po włączeniu umożliwia wykrycie obiektu w kącie innym niż pionowy. Przydatne do wykrywania obiektów na rampach.
* **Zezwalaj na zmianę skali** — umożliwia śledzenie zmiany rozmiaru wykrytego obiektu na podstawie informacji o środowisku.
* **Suwak współczynnika pokrycia** — dostosowuje proporcje punktów powierzchni, które muszą być zgodne, aby śledzenie wykrywało obiekt.  Niższe wartości umożliwiają śledzenie lepszego wykrywania obiektów, które są trudne do wykrycia przez czujniki HoloLens, takie jak ciemne obiekty lub wysoce odbicie obiektów. Wyższe wartości spowodują zmniejszenie częstotliwości wykrywania fałszywych.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Menu podręczne śledzenia aparatu Unity":::

##### <a name="search-area-settings-menu"></a>Menu ustawień obszaru wyszukiwania

* **Zablokuj obszar wyszukiwania** — pole ograniczenia obszaru blokady, aby zapobiec przypadkowemu przeniesieniu przez ręce.
* **Dopasuj automatycznie obszar wyszukiwania** — umożliwia przemieszczenie obszaru wyszukiwania do samego siebie podczas wykrywania obiektów.
* **Siatka cyklu** — przechodzi przez wizualizację załadowanych siatek w obszarze wyszukiwania.  Ta opcja może pomóc użytkownikom w wyrównaniu pola wyszukiwania trudno wykrywać obiekty.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Menu przeszukiwane obszaru wyszukiwania aparatu Unity":::

Przykład `subscription.json` :

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pojęcia: Omówienie zestawu SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Często zadawane pytania](../faq.md)
