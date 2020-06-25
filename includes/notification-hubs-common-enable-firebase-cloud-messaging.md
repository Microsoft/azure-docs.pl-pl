---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081438"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Tworzenie projektu Firebase i Włączanie obsługi komunikatów Firebase w chmurze dla systemu Android

1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, wprowadzając **PushDemo** jako **nazwę projektu**.

    > [!NOTE]
    > Zostanie wygenerowana unikatowa nazwa. Domyślnie jest to składająca się z małej litery odmiany podanej nazwy i wygenerowanej liczby rozdzielonej kreską. Możesz to zmienić, jeśli chcesz, aby był on nadal unikatowy globalnie.

1. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**.

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. Na stronie **Dodawanie Firebase do aplikacji systemu Android** wykonaj następujące czynności.
    1. W polu **Nazwa pakietu systemu Android**wprowadź nazwę pakietu. Na przykład: `com.<organization_identifier>.<package_name>`.

        ![Określ nazwę pakietu](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. Wybierz pozycję **zarejestruj aplikację**.  
    1. Wybierz pozycję **pobierz google-services.jsna**. Następnie Zapisz plik w folderze lokalnym w celu późniejszego użycia i wybierz **dalej**.  

        ![Pobierz google-services.jsna](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. Wybierz pozycję **Dalej**.
    1. Wybierz pozycję **Kontynuuj do konsoli**

        > [!NOTE]
        > Jeśli przycisk **Kontynuuj do konsoli** nie jest włączony z powodu *sprawdzenia instalacji* , wybierz pozycję **Pomiń ten krok**.

1. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Jeśli nie pobrano **google-services.js** pliku, możesz go pobrać na tej stronie.

1. Przejdź na kartę usługi **Cloud Messaging** w górnej części strony. Skopiuj i Zapisz **klucz serwera** do późniejszego użycia. Ta wartość służy do konfigurowania centrum powiadomień.

    ![Kopiuj klucz serwera](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
