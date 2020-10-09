---
title: 'Samouczek: uruchamianie czytnika immersyjny przy użyciu przykładu SWIFT kod dla systemu iOS'
titleSuffix: Azure Cognitive Services
description: W tym samouczku skonfigurujesz i uruchomisz przykładową aplikację SWIFT, która uruchamia czytnik immersyjny.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: ce9d3af8f7517e2acae5264197b842d47085279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516371"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>Samouczek: uruchamianie czytnika immersyjny przy użyciu przykładu SWIFT kod dla systemu iOS

W [przeglądzie](./overview.md)zawarto informacje na temat tego, co to jest czytnik immersyjny i w jaki sposób implementuje sprawdzone techniki w celu zwiększenia czytelności dla osób uczące się, nowych czytelników i studentów z różnicami w nauce. W tym samouczku opisano sposób tworzenia aplikacji dla systemu iOS, która uruchamia czytnik immersyjny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj i uruchom aplikację SWIFT dla systemu iOS przy użyciu przykładowego projektu.
> * Uzyskaj token dostępu.
> * Uruchom czytnik immersyjny z przykładową zawartością.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób czytnika immersyjny skonfigurowany do Azure Active Directory uwierzytelniania. Postępuj zgodnie z [tymi instrukcjami](./how-to-create-immersive-reader.md) , aby rozpocząć konfigurację. Podczas konfigurowania właściwości środowiska będą potrzebne pewne wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* [macOS](https://www.apple.com/macos).
* Usługi [git](https://git-scm.com/).
* [Zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Konfigurowanie poświadczeń uwierzytelniania

1. Otwórz Xcode i Otwórz **Immersive-Reader-SDK/js/Samples/iOS/QuickStart-SWIFT/QuickStart-Swift. XCODEPROJ**.
1. W górnym menu wybierz kolejno opcje schemat **produktu**  >  **Scheme**  >  **Edytuj schemat**.
1. W widoku **uruchamiania** wybierz kartę **argumenty** .
1. W sekcji **zmienne środowiskowe** Dodaj następujące nazwy i wartości. Podaj wartości podanych podczas tworzenia zasobu czytnika immersyjny.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Nie należy zatwierdzić tej zmiany w kontroli źródła, ponieważ zawiera ona wpisy tajne, które nie powinny być udostępniane publicznie.

## <a name="start-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

W Xcode wybierz **kombinację klawiszy Ctrl + R** , aby uruchomić projekt.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [zestawem SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](./reference.md).
* Wyświetl przykłady kodu w witrynie [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
