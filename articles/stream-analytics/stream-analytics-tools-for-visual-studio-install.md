---
title: Konfigurowanie narzędzi Azure Stream Analytics dla programu Visual Studio
description: W tym artykule opisano wymagania dotyczące instalacji oraz sposób konfigurowania Azure Stream Analytics narzędzi dla programu Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 464e8fbf624dfa84ec54bf6a7e0ce202d047e15d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045114"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Zainstaluj narzędzia Azure Stream Analytics Tools for Visual Studio

Program Visual Studio 2019 i Visual Studio 2017 obsługują narzędzia Azure Data Lake i Stream Analytics. W tym artykule opisano sposób instalowania i odinstalowywania narzędzi.

Aby uzyskać więcej informacji na temat korzystania z narzędzi, zobacz [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalowanie

Narzędzia są obsługiwane w wersjach Visual Studio Enterprise (Ultimate/Premium), Professional i Community. Wersje Express i Visual Studio dla komputerów Mac nie obsługują tych funkcji.

Zalecamy użycie programu Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Zainstaluj dla programu Visual Studio 2019 i 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Narzędzia Azure Data Lake i Stream Analytics są częścią obciążeń związanych z projektowaniem i **przetwarzaniem danych** **platformy Azure** . Włączenie jednego z tych dwóch obciążeń podczas instalacji. Jeśli program Visual Studio jest już zainstalowany, wybierz pozycję **Narzędzia**  >  **Pobierz narzędzia i funkcje** , aby dodać obciążenia.

Pobierz [program Visual studio 2019 (wersja zapoznawcza 2 lub nowsza) lub program Visual studio 2017 (15,3 lub nowszy)](https://www.visualstudio.com/) i postępuj zgodnie z instrukcjami dotyczącymi instalacji.

Wybierz obciążenie **magazynu i przetwarzania danych** , jak pokazano poniżej:

![Wybrano obciążenie magazynowanie i przetwarzanie danych](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Wybierz obciążenie **Programowanie na platformie Azure** , jak pokazano:

![Wybrano obciążenie Programowanie na platformie Azure](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Po dodaniu obciążenia należy zaktualizować narzędzia. Ta procedura odwołuje się do programu Visual Studio 2019:

1. Wybierz pozycję **rozszerzenia**  >  **Zarządzanie rozszerzeniami**.

1. W obszarze **Zarządzanie rozszerzeniami**wybierz pozycję **aktualizacje** , a następnie wybierz pozycję **narzędzia Azure Data Lake i Stream Analytics**.

1. Wybierz pozycję **Aktualizuj** , aby zainstalować najnowsze rozszerzenie.

![Rozszerzenia i aktualizacje programu Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Zainstaluj dla programu Visual Studio 2015 i 2013<a name="visual-studio-2015-2013"></a>

Narzędzia są obsługiwane w wersjach Visual Studio Enterprise (Ultimate/Premium), Professional i Community. W wersji Express nie są obsługiwane.

* Zainstaluj program Visual Studio 2015 lub Visual Studio 2013 Update 4.
* Zainstaluj Zestaw Microsoft Azure SDK dla platformy .NET w wersji 2.7.1 lub nowszej przy użyciu [Instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx).
* Zainstaluj [narzędzia Microsoft Azure Data Lake i Stream Analytics Tools for Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aktualizacji<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

W przypadku programów Visual Studio 2019 i Visual Studio 2017 nowe przypomnienie o wersji są wyświetlane jako powiadomienie programu Visual Studio.

W przypadku programu Visual Studio 2015 i Visual Studio 2013 narzędzia sprawdzają, czy są nowe wersje automatycznie. Postępuj zgodnie z instrukcjami, aby zainstalować najnowszą wersję.

## <a name="uninstall"></a>Odinstalowanie

Można odinstalować narzędzia Azure Data Lake i Stream Analytics. Dla programu Visual Studio 2019 lub Visual Studio 2017 wybierz kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**. W obszarze **Modyfikowanie**wybierz pozycję **narzędzia Azure Data Lake i Stream Analytics**. Jest on wyświetlany w obszarze obciążenia **magazynu danych i przetwarzania** lub obciążenie **Programowanie na platformie Azure** .

Aby odinstalować program z programu Visual Studio 2015 lub Visual Studio 2013, przejdź do pozycji **Panel sterowania**  >  **programy i funkcje**. Odinstaluj **narzędzia Microsoft Azure Data Lake i Stream Analytics Tools for Visual Studio**.
