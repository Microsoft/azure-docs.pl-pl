---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70935097"
---
1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.
2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**. 

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na stronie **Dodawanie Firebase do aplikacji systemu Android** wykonaj następujące czynności: 
    1. **W polu** **Nazwa pakietu systemu Android** skopiuj wartość Twojego elementu w pliku Build. Gradle aplikacji. W tym przykładzie jest to `com.fabrikam.fcmtutorial1app` . 

        ![Określ nazwę pakietu](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Wybierz pozycję **zarejestruj aplikację**. 
4. Wybierz pozycję **pobierz google-services.jsna**, Zapisz plik w folderze **aplikacji** projektu, a następnie wybierz przycisk **dalej**. 

    ![Pobierz google-services.jsna](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Wprowadź następujące **zmiany konfiguracji** w projekcie w Android Studio. 
    1.  W pliku Build. Gradle ( &lt; Project/Build.Gradle) na poziomie projektu &gt; Dodaj następującą instrukcję do sekcji **zależności** . 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. W pliku Build. Gradle programu App-Level ( &lt; Project &gt; / &lt; App-module &gt; /Build.Gradle) Dodaj następujące instrukcje do sekcji **zależności** . 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Dodaj następujący wiersz na końcu pliku Build. Gradle programu App-Level po sekcji zależności. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Na pasku narzędzi wybierz pozycję **Synchronizuj teraz** . 
 
        ![zmiany konfiguracji kompilacji. Gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Wybierz opcję **Dalej**. 
7. Wybierz pozycję **Pomiń ten krok**. 

    ![Pomiń ostatni krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Jeśli nie pobrano google-services.jspliku do folderu **aplikacji** projektu Android Studio, można to zrobić na tej stronie. 
5. Przejdź na kartę usługi **Cloud Messaging** w górnej części strony. 
6. Skopiuj i Zapisz **klucz serwera** do późniejszego użycia. Ta wartość służy do konfigurowania centrum.
