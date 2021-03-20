---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68728859"
---
1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.
2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**. 

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Na stronie **Dodawanie Firebase do aplikacji systemu Android** wykonaj następujące czynności: 
    1. W polu **Nazwa pakietu systemu Android** wprowadź nazwę pakietu. Na przykład: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Określ nazwę pakietu](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Wybierz pozycję **zarejestruj aplikację**.  
    1. Wybierz pozycję **pobierz google-services.jsna**. Następnie Zapisz plik w folderze projektu i wybierz pozycję **dalej**. Jeśli projekt programu Visual Studio nie został jeszcze utworzony, możesz wykonać ten krok po utworzeniu projektu. 

        ![Pobierz google-services.jsna](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Wybierz opcję **Dalej**. 
    7. Wybierz pozycję **Pomiń ten krok**. 

        ![Pomiń ostatni krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Jeśli nie pobrano **google-services.js** pliku, możesz go pobrać na tej stronie. 

    ![Pobierz google-services.jsna karcie Ogólne](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Przejdź na kartę usługi **Cloud Messaging** w górnej części strony. Skopiuj i Zapisz **klucz serwera** do późniejszego użycia. Ta wartość służy do konfigurowania centrum powiadomień.

    ![Kopiuj klucz serwera](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
