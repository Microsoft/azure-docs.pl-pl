---
title: plik dołączania
description: plik dołączania
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67509094"
---
1. W Android Studio wybierz pozycję **Narzędzia** z menu, a następnie wybierz pozycję **Menedżer zestawów SDK**. 
2. Wybierz docelową wersję Android SDK, która jest używana w projekcie. Następnie wybierz pozycję **Pokaż szczegóły pakietu**. 

    ![Menedżer zestawów Android SDK — wybieranie wersji docelowej](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Wybierz pozycję **Google API**, jeśli nie została jeszcze zainstalowana.

    ![Menedżer zestawów Android SDK — wybrane interfejsy API firmy Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Przejdź do karty **SDK Tools** . Jeśli nie zainstalowano jeszcze Usługi Google Play, wybierz pozycję **usługi Google Play** , jak pokazano na poniższej ilustracji. Następnie wybierz pozycję **Zastosuj** do instalacji. Zanotuj ścieżkę zestawu SDK, która będzie potrzebna w kolejnym kroku.

    ![Menedżer zestawów Android SDK — wybrane usługi Google Play](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Jeśli zobaczysz okno dialogowe **Potwierdzanie zmiany**, wybierz przycisk **OK**. Instalator składników zainstaluje żądane składniki. Wybierz przycisk **Finish** (Zakończ) po zainstalowaniu składników.
4. Wybierz przycisk **OK**, aby zamknąć okno dialogowe **Settings for New Projects** (Ustawienia nowego projektu).  
5. Otwórz plik Build. Gradle w katalogu **aplikacji** , a następnie Dodaj następujący wiersz poniżej `dependencies` . 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Na pasku narzędzi wybierz ikonę **Synchronizuj teraz** .

    ![Synchronizacja za pomocą narzędzia Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Otwórz plik AndroidManifest.xml, a następnie Dodaj następujący tag do znacznika *aplikacji* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
