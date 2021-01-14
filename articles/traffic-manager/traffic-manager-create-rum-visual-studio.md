---
title: Pomiary rzeczywistego użytkownika z programem Visual Studio Mobile Center — Azure Traffic Manager
description: Skonfiguruj aplikację mobilną opracowaną przy użyciu programu Visual Studio Mobile Center, aby wysyłać Pomiary rzeczywistego użytkownika do Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: devx-track-js
ms.openlocfilehash: f9e8cdd3eb5c9f441444683fb5efaccc880b2757
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184615"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Jak wysyłać Pomiary rzeczywistego użytkownika do Traffic Manager za pomocą programu Visual Studio Mobile Center

Można skonfigurować aplikację mobilną opracowaną przy użyciu programu Visual Studio Mobile Center, aby wysyłać Pomiary rzeczywistego użytkownika do Traffic Manager, wykonując następujące czynności:

>[!NOTE]
> Obecnie wysyłanie Pomiary rzeczywistego użytkownika do usługi Traffic Manager jest obsługiwane tylko dla systemu Android.

Aby skonfigurować Pomiary rzeczywistego użytkownika, musisz uzyskać klucz i instrumentację aplikacji za pomocą pakietu RUM.

## <a name="step-1-obtain-a-key"></a>Krok 1. Uzyskiwanie klucza
    
Pomiary wykonywane i wysyłane do Traffic Manager z aplikacji klienckiej są identyfikowane przez usługę przy użyciu unikatowego ciągu, zwanego kluczem Pomiary rzeczywistego użytkownika (RUM). Klucz RUM można uzyskać przy użyciu Azure Portal, interfejsu API REST lub interfejsów CLI programu PowerShell.

Aby uzyskać klucz RUM przy użyciu Azure Portal przy użyciu następującej procedury:
1. Z poziomu przeglądarki zaloguj się do witryny Azure Portal. Jeśli jeszcze nie masz konta, możesz skorzystać z bezpłatnej miesięcznej wersji próbnej.
2. Korzystając z paska wyszukiwania portalu, wyszukaj nazwę profilu usługi Traffic Manager, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach wyszukiwania.
3. Na stronie profil Traffic Manager kliknij pozycję **pomiary rzeczywistego użytkownika** w obszarze **Ustawienia**.
4. Kliknij przycisk **Generuj klucz** , aby utworzyć nowy klucz rum.
        
   ![Generuj klucz Pomiary rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: generowanie klucza Pomiary rzeczywistego użytkownika**

5. Na stronie jest wyświetlany wygenerowany klucz RUM i fragment kodu JavaScript, który musi zostać osadzony na stronie HTML.
 
   ![Kod JavaScript dla klucza Pomiary rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Rysunek 2: Pomiary rzeczywistego użytkownika klucz i pomiar JavaScript**
 
6. Kliknij przycisk **Kopiuj** , aby SKOPIOWAĆ klucz rum. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2. Instrumentacja aplikacji za pomocą pakietu rumu programu Mobile Center SDK

Jeśli jesteś nowym w programie Visual Studio Mobile Center, odwiedź jego [witrynę internetową](https://mobile.azure.com). Aby uzyskać szczegółowe instrukcje dotyczące integracji z zestawem SDK, zobacz [wprowadzenie z Android SDK](/mobile-center/sdk/getting-started/Android).

Aby użyć Pomiary rzeczywistego użytkownika, wykonaj następującą procedurę:

1.  Dodawanie zestawu SDK do projektu

    W wersji zapoznawczej zestawu SDK protokołu ATM, należy jawnie odwoływać się do repozytorium pakietu.

    W pliku **App/Build. Gradle** Dodaj następujące wiersze:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    W pliku **App/Build. Gradle** Dodaj następujące wiersze:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Uruchamianie zestawu SDK

    Otwórz główną klasę działania aplikacji i Dodaj następujące instrukcje importu:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Poszukaj `onCreate` wywołania zwrotnego w tym samym pliku i Dodaj następujący kod:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [pomiary rzeczywistego użytkownika](traffic-manager-rum-overview.md)
- Dowiedz się, [jak działa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o usłudze [Mobile Center](/mobile-center/)
- [Utwórz konto](https://mobile.azure.com) w usłudze Mobile Center
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez Traffic Manager
- Dowiedz się, jak [utworzyć profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)