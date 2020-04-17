---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 04/15/2020
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: b5b6389491fd91b716a05e7c57c98addb6352bd9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461928"
---
1. Pobierz szybki start sdk klienta dla następujących platform:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Jeśli korzystasz z projektu iOS, musisz pobrać "azuresdk-iOS-\*.zip" z [najnowszej wersji GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Rozpaj i `MicrosoftAzureMobile.framework` dodaj plik do katalogu głównego projektu.
    >

2. Należy dodać połączenie z bazą danych lub połączyć się z istniejącym połączeniem. Najpierw określ, czy utworzysz magazyn danych, czy użyjesz istniejącego.

    - **Tworzenie nowego magazynu danych:** jeśli zamierzasz utworzyć magazyn danych, użyj następującego przewodnika Szybki start:

        [Szybki start: wprowadzenie do pojedynczych baz danych w bazie danych SQL usługi Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Istniejące źródło danych:** Postępuj zgodnie z poniższymi instrukcjami, jeśli chcesz użyć istniejącego połączenia z bazą danych

        1. Format ciągu połączenia bazy danych SQL -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** Nazwa serwera, można ją znaleźć na stronie przeglądu bazy danych i zwykle w postaci "server_name.database.windows.net".
            **{port}** zwykle 1433.
            **{your_catalogue}** Nazwa bazy danych.
            **{your_username}** Nazwa użytkownika, aby uzyskać dostęp do bazy danych.
            **{your_password}** Hasło dostępu do bazy danych.

            [Dowiedz się więcej o formacie ciągu połączenia SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Dodaj ciąg połączenia do **aplikacji mobilnej** W usłudze App Service możesz zarządzać ciągami połączeń dla aplikacji za pomocą opcji **Konfiguracja** w menu.

            Aby dodać parametry połączenia:

            1. Kliknij kartę **Ustawienia aplikacji.**

            2. Kliknij **[+] Nowy ciąg połączenia**.

            3. Należy podać **nazwę**, **wartość** i **typ** dla ciągu połączenia.

            4. **Wpisz nazwę** jako`MS_TableConnectionString`

            5. Wartość powinna być ciągiem łączącym utworzonym w poprzednim kroku.

            6. Jeśli dodajesz parametry połączenia do bazy danych platformy SQL Azure, wybierz **sqlazure** w **obszarze typu**.

3. Usługa Azure Mobile Apps ma zestawy SDK dla zaplecza platformy .NET i Node.js.

   - **Zaplecze node.js**
    
     Jeśli zamierzasz korzystać z aplikacji Szybki start Node.js, postępuj zgodnie z poniższymi instrukcjami.
     
        1. Utwórz nowy interfejs API — można wprowadzić zmiany bezpośrednio w witrynie Azure portal lub zmodyfikować kod lokalnie w środowisku deweloperskim, a następnie opublikować na platformie Azure. Kliknij menu `App Service Editor (Preview)` `Development Tools` w obszarze, które zapewnia sposób edycji w przeglądarce dla kodu aplikacji.
        
        2. Kliknij `Go` i po otwarciu Edytora usługi app service masz pełną kontrolę nad kodem źródłowym. Zakładając, że masz już zainstalowany pakiet express i azure-mobile-apps z poleceniem npm install, kliknij folder interfejsu API w obszarze WWWROOT, aby utworzyć lub edytować niestandardowy interfejs API. Wprowadzać zmiany w pliku kodu, a zmiany są zapisywane automatycznie.
        
        3. Masz pełną kontrolę nad usługą Azure SQL Database używane do przechowywania danych aplikacji. Można łatwo utworzyć nowe tabele w bazie danych.
 
   - **Zaplecze .NET**
    
        Jeśli zamierzasz korzystać z aplikacji Szybki start .NET, postępuj zgodnie z poniższymi instrukcjami.

        1. Pobierz projekt serwera usługi Azure Mobile Apps .NET z [repozytorium azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Tworzenie projektu serwera platformy .NET lokalnie w programie Visual Studio.

        3. W programie Visual Studio otwórz Eksploratora rozwiązań, kliknij prawym przyciskiem myszy `ZUMOAPPNAMEService` projekt, kliknij polecenie **Publikuj** `Publish to App Service` , zobaczysz okno. Jeśli pracujesz na komputerze Mac, zapoznaj się z innymi sposobami wdrożenia aplikacji [tutaj](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Publikowanie w programie Visual Studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Wybierz **pozycję Usługa app service** jako miejsce docelowe publikowania, a następnie kliknij pozycję Wybierz **istniejący**, a następnie kliknij przycisk **Publikuj** u dołu okna.

        5. Najpierw musisz zalogować się do programu Visual Studio za pomocą subskrypcji platformy Azure. Wybierz `Subscription`opcję `Resource Group`, a następnie wybierz nazwę aplikacji. Gdy będzie gotowy, kliknij przycisk **OK**, spowoduje to wdrożenie projektu serwera .NET, który masz lokalnie w wewnętrznej bazy danych usługi aplikacji. Po zakończeniu wdrażania zostaniesz przekierowany `http://{zumoappname}.azurewebsites.net/` do przeglądarki.                   
