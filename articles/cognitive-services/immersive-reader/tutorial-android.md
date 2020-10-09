---
title: 'Samouczek: uruchamianie czytnika immersyjny przy użyciu przykładów kodu dla systemu Android'
titleSuffix: Azure Cognitive Services
description: W tym samouczku skonfigurujesz i uruchomisz przykładową aplikację dla systemu Android, która uruchamia czytnik immersyjny.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: 637b4d988a4845369a441dce55f0043d873879f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516439"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>Samouczek: uruchamianie czytnika immersyjny przy użyciu przykładu kodu Java dla systemu Android

W [przeglądzie](./overview.md)zawarto informacje na temat tego, co to jest czytnik immersyjny i w jaki sposób implementuje sprawdzone techniki w celu zwiększenia czytelności dla osób uczące się, nowych czytelników i studentów z różnicami w nauce. W tym samouczku opisano sposób tworzenia aplikacji systemu Android, która uruchamia czytnik immersyjny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj i uruchom aplikację dla systemu Android przy użyciu przykładowego projektu.
> * Uzyskaj token dostępu.
> * Uruchom czytnik immersyjny z przykładową zawartością.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób czytnika immersyjny skonfigurowany do Azure Active Directory uwierzytelniania. Postępuj zgodnie z [tymi instrukcjami](./how-to-create-immersive-reader.md) , aby rozpocząć konfigurację. Podczas konfigurowania właściwości środowiska będą potrzebne pewne wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* Usługi [git](https://git-scm.com/).
* [Zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Konfigurowanie poświadczeń uwierzytelniania

1. Rozpocznij Android Studio i Otwórz projekt z poziomu **zestawu immersyjny-Reader-SDK/js/Samples/Start-Java-Android** Directory (Java) lub **immersyjny-Reader-SDK/js/Samples/Start-Kotlin** Directory (Kotlin).

1. Utwórz plik o nazwie **ENV** w folderze **/Assets** . Dodaj następujące nazwy i wartości, a następnie podaj wartości zgodnie z potrzebami. Nie Zatwierdź tego pliku w kontroli źródła, ponieważ zawiera on wpisy tajne, które nie powinny być publiczne.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

Wybierz emulator urządzenia z Menedżera AVD i Uruchom projekt.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [zestawem SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](./reference.md).
* Wyświetl przykłady kodu w witrynie [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).