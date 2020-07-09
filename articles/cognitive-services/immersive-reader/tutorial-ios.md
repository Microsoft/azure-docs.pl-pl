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
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050170"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Samouczek: uruchamianie czytnika immersyjny przy użyciu przykładu SWIFT kod dla systemu iOS

W [przeglądzie](./overview.md)zawarto informacje na temat tego, co to jest czytnik immersyjny i w jaki sposób implementuje sprawdzone techniki w celu zwiększenia czytelności dla osób uczące się, nowych czytelników i studentów z różnicami w nauce. W tym samouczku opisano sposób tworzenia aplikacji dla systemu iOS, która uruchamia czytnik immersyjny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie i uruchamianie aplikacji SWIFT dla systemu iOS przy użyciu przykładowego projektu
> * Uzyskiwanie tokenu dostępu
> * Uruchom czytnik immersyjny z przykładową zawartością

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób czytnika immersyjny skonfigurowany do Azure Active Directory uwierzytelniania. Postępuj zgodnie z [tymi instrukcjami](./how-to-create-immersive-reader.md) , aby rozpocząć konfigurację. Podczas konfigurowania właściwości środowiska będą potrzebne pewne wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [Zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Konfigurowanie poświadczeń uwierzytelniania

1. Otwórz Xcode i Otwórz **Immersive-Reader-SDK/js/Samples/iOS/QuickStart-SWIFT/QuickStart-Swift. XCODEPROJ**.
2. W górnym menu kliknij pozycję **produkt > schemat > Edytuj schemat...**
3. W widoku **uruchamiania** kliknij kartę **argumenty** .
4. W sekcji **zmienne środowiskowe** Dodaj następujące nazwy i wartości, podając wartości podane podczas tworzenia zasobu czytnika immersyjny.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Upewnij się, że nie zatwierdzisz powyższej zmiany w kontroli źródła, ponieważ zawiera ona wpisy tajne, które nie powinny być publiczne.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

1. W Xcode naciśnij **klawisze CTRL-R** , aby uruchomić projekt.

## <a name="next-steps"></a>Następne kroki

* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](./reference.md)
* Wyświetl przykłady kodu w witrynie [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
