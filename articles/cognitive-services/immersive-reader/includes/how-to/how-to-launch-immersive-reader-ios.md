---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620203"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Zasób czytnika immersyjny skonfigurowany do Azure Active Directory uwierzytelniania. Postępuj zgodnie z [tymi instrukcjami](../../how-to-create-immersive-reader.md) , aby rozpocząć konfigurację.  Podczas konfigurowania właściwości środowiska będą potrzebne pewne wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* [macOS](https://www.apple.com/macos).
* Usługi [git](https://git-scm.com/).
* [Zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Konfigurowanie poświadczeń uwierzytelniania

1. Otwórz Xcode i Otwórz **Immersive-Reader-SDK/js/Samples/iOS/QuickStart-SWIFT/QuickStart-Swift. XCODEPROJ**.
1. W górnym menu wybierz kolejno opcje schemat **produktu**  >    >  **Edytuj schemat**.
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

* Zapoznaj się z [zestawem SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](../../reference.md).
* Wyświetl przykłady kodu w witrynie [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
