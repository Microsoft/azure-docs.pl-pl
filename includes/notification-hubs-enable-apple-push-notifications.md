---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 7b5034f2163e8478d7ddb7b9271402b094a809d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95563315"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generuj plik żądania podpisania certyfikatu

Apple Push Notification Service (APNs) używa certyfikatów do uwierzytelniania powiadomień wypychanych. Wykonaj instrukcje, aby utworzyć niezbędny certyfikat powiadomień wypychanych umożliwiający ich wysyłanie i odbieranie. Więcej informacji dotyczących tych pojęć można znaleźć w oficjalnej dokumentacji usługi [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Wygeneruj plik żądania podpisania certyfikatu (CSR), którego firma Apple używa do generowania podpisanego certyfikatu wypychania.

1. Na komputerze Mac uruchom narzędzie Keychain Access. Można go otworzyć z folderu **Utilities** lub **innego** folderu w programie Launchpad.

1. Wybierz pozycję **dostęp do łańcucha kluczy**, rozwiń węzeł **Asystent certyfikatów**, a następnie wybierz pozycję **Żądaj certyfikatu z urzędu certyfikacji**.

    ![Żądanie nowego certyfikatu za pomocą narzędzia Keychain Access](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Domyślnie dostęp do łańcucha kluczy wybiera pierwszy element z listy. Może to być problem, jeśli jesteś w kategorii **Certyfikaty** i **urząd certyfikacji relacji deweloperów firmy Apple Worldwide** nie jest pierwszym elementem na liście. Przed wygenerowaniem CSR (żądanie podpisania certyfikatu) Upewnij się, że masz element niebędący kluczem lub wybrano klucz **urzędu certyfikacji relacji deweloperów firmy Apple Worldwide** .

1. Wybierz **adres E-mail użytkownika**, wprowadź wartość **Nazwa pospolita** , upewnij się, że określono opcję **Zapisano na dysku**, a następnie wybierz pozycję **Kontynuuj**. Pozostaw puste **adresy E-mail urzędu certyfikacji** , ponieważ nie jest to wymagane.

    ![Wymagane informacje o certyfikacie](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Wprowadź nazwę pliku CSR w obszarze **Zapisz jako**, wybierz lokalizację w **miejscu**, a następnie wybierz pozycję **Zapisz**.

    ![Wybierz nazwę pliku dla certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ta akcja zapisuje plik CSR w wybranej lokalizacji. Domyślna lokalizacja to **Desktop**. Zapamiętaj wybraną lokalizację pliku.

Następnie zarejestruj aplikację za pomocą firmy Apple, Włącz powiadomienia wypychane i przekaż wyeksportowany certyfikat CSR w celu utworzenia certyfikatu wypychania.

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Aby wysyłać powiadomienia wypychane do aplikacji systemu iOS, zarejestruj swoją aplikację za pomocą firmy Apple, a także Zarejestruj się w celu otrzymywania powiadomień wypychanych.  

1. Jeśli aplikacja nie została jeszcze zarejestrowana, przejdź do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) w centrum deweloperów firmy Apple. Zaloguj się do portalu przy użyciu identyfikatora Apple ID, a następnie wybierz pozycję **identyfikatory**. Następnie wybierz pozycję **+** , aby zarejestrować nową aplikację.

    ![Strona identyfikatorów aplikacji w portalu aprowizowania dla systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na ekranie **Rejestrowanie nowego identyfikatora** wybierz przycisk radiowy **identyfikatory aplikacji** . Następnie wybierz pozycję **Kontynuuj**.

    ![nowy identyfikator strony rejestracji w portalu aprowizacji systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Zaktualizuj następujące trzy wartości dla nowej aplikacji, a następnie wybierz pozycję **Kontynuuj**:

   * **Opis**: wpisz nazwę opisową aplikacji.

   * **Identyfikator pakietu**: Wprowadź identyfikator pakietu formularza **identyfikator organizacji. Nazwa produktu** , jak wspomniano w [przewodniku po dystrybucji aplikacji](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). *Identyfikator organizacji* i wartości *nazwy produktu* muszą być zgodne z identyfikatorem organizacji i nazwą produktu używaną podczas tworzenia projektu Xcode. Na poniższym zrzucie ekranu wartość **NotificationHubs** jest używana jako identyfikator organizacji, a wartość **getstarted** jest używana jako nazwa produktu. Upewnij się, że wartość **identyfikatora pakietu** pasuje do wartości w projekcie Xcode, tak aby Xcode używała poprawnego profilu publikacji.

      ![Strona identyfikatora aplikacji w portalu aprowizacji systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Powiadomienia wypychane**: zaznacz opcję **powiadomienia wypychane** w sekcji **możliwości** .

      ![Formularz rejestracji nowego identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Ta akcja spowoduje wygenerowanie identyfikatora aplikacji i żądań potwierdzających informacje. Wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **zarejestruj** , aby potwierdzić nowy identyfikator aplikacji.

      ![Potwierdź nowy identyfikator aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Po wybraniu pozycji **zarejestruj** nowy identyfikator aplikacji zostanie wyświetlony jako element wiersza na stronie **certyfikaty, identyfikatory & profile** .

4. Na stronie **certyfikaty, identyfikatory & profile** w obszarze **identyfikatory** Znajdź element wiersza identyfikatora aplikacji, który został właśnie utworzony, a następnie wybierz jego wiersz w celu wyświetlenia ekranu **Edytowanie konfiguracji identyfikatora aplikacji** .

## <a name="creating-a-certificate-for-notification-hubs"></a>Tworzenie certyfikatu dla Notification Hubs
Aby umożliwić centrum powiadomień współdziałanie z usługą **APNs**, wymagany jest certyfikat. Można to zrobić na jeden z dwóch sposobów:

1. Utwórz plik **. p12** , który można przekazać bezpośrednio do centrum powiadomień.  
2. Utwórz plik **. P8** , który może być używany do [uwierzytelniania opartego na tokenach](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md) (*rozwiązanie nowsze*).

Nowsze podejście ma wiele korzyści (w porównaniu z użyciem certyfikatów), zgodnie z opisem w temacie [uwierzytelnianie oparte na tokenach (http/2) dla usługi APNs](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md). Należy jednak wykonać kroki dla obu tych metod. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>Opcja 1: Tworzenie certyfikatu wypychania z. P12, który można przekazać bezpośrednio do centrum powiadomień

1. Przewiń w dół do opcji sprawdzone **powiadomienia wypychane** , a następnie wybierz pozycję **Konfiguruj** , aby utworzyć certyfikat.

    ![Edytowanie strony identyfikatorów aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Zostanie wyświetlone okno **Certyfikaty SSL usługi powiadomień wypychanych firmy Apple** . Wybierz przycisk **Utwórz certyfikat** w sekcji **certyfikat protokołu SSL dla deweloperów** .

    ![Przycisk tworzenia certyfikatu dla identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zostanie wyświetlony ekran **Utwórz nowy certyfikat** .

    > [!NOTE]
    > W tym samouczku jest używany certyfikat programistyczny używany przez aplikację do generowania unikatowego tokenu urządzenia. Ten sam proces jest używany podczas rejestrowania certyfikatu produkcyjnego. Należy po prostu pamiętać, aby używać tego samego typu certyfikatu podczas wysyłania powiadomień.

3. Wybierz pozycję **Wybierz plik**, przejdź do lokalizacji, w której ZAPISANO plik CSR z pierwszego zadania, a następnie kliknij dwukrotnie nazwę certyfikatu w celu jego załadowania. Następnie wybierz pozycję **Kontynuuj**.

4. Po utworzeniu certyfikatu przez portal wybierz przycisk **Pobierz** . Zapisz certyfikat i Zapamiętaj lokalizację, w której został zapisany.

    ![Strona pobierania wygenerowanego certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certyfikat zostanie pobrany i zapisany na komputerze w folderze **pobierania** .

    ![Lokalizowanie pliku certyfikatu w folderze Pobrane](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Domyślnie pobrany certyfikat programistyczny nosi nazwę **aps_development. cer**.

5. Kliknij dwukrotnie pobrany certyfikat powiadomień wypychanych **aps_development.cer**. Ta akcja powoduje zainstalowanie nowego certyfikatu w narzędziu Keychain, jak przedstawiono na poniższym rysunku:

    ![Lista certyfikatów narzędzia Keychain Access z nowym certyfikatem](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Mimo że nazwa w certyfikacie może być inna, nazwa zostanie poprzedzona przez **Apple Development iOS usługi wypychania**.

6. W narzędziu Keychain Access kliknij prawym przyciskiem myszy nowy certyfikat powiadomień wypychanych utworzony w kategorii **Certyfikaty**. Wybierz pozycję **Eksportuj**, Nazwij plik, wybierz format **. p12** , a następnie wybierz pozycję **Zapisz**.

    ![Eksportowanie certyfikatu w formacie p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Możesz zdecydować się na ochronę certyfikatu hasłem, ale jest to opcjonalne. Kliknij przycisk **OK** , jeśli chcesz pominąć tworzenie haseł. Zanotuj nazwę pliku i lokalizację wyeksportowanego certyfikatu .p12. Są one używane do włączania uwierzytelniania przy użyciu usługi APNs.

    > [!NOTE]
    > Nazwa i lokalizacja pliku. p12 mogą różnić się od informacji w tym samouczku.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Opcja 2: Tworzenie certyfikatu. P8, który może być używany do uwierzytelniania opartego na tokenach

1. Zwróć uwagę na następujące informacje:

    - **Prefiks identyfikatora aplikacji** (jest to **Identyfikator zespołu**)
    - **Identyfikator pakietu**
    
2. W obszarze **certyfikaty, identyfikatory & profile**, kliknij pozycję **klucze**.

   > [!NOTE]
   > Jeśli masz już skonfigurowany klucz dla usługi **APNs**, możesz użyć pobranego wcześniej certyfikatu P8 bezpośrednio po jego utworzeniu. Jeśli tak, możesz zignorować kroki od **3** do **5**.

3. Kliknij **+** przycisk (lub przycisk **Utwórz klucz** ), aby utworzyć nowy klucz.
4. Podaj odpowiednią wartość **nazwy klucza** , a następnie sprawdź opcję **usługi Apple Push Notification Service (APNs)** , a następnie kliknij przycisk **Kontynuuj**, a następnie **zarejestruj** się na następnym ekranie.
5. Kliknij pozycję **Pobierz** , a następnie przenieś plik **P8** (z prefiksem *AuthKey_*) do bezpiecznego katalogu lokalnego, a następnie kliknij pozycję **gotowe**.

   > [!NOTE] 
   > Pamiętaj, aby zachować plik. P8 w bezpiecznym miejscu (i zapisać kopię zapasową). Po pobraniu klucza nie można go pobrać z jego pobrania, ponieważ kopia serwera zostanie usunięta.
  
6. W obszarze **klucze** kliknij właśnie utworzony klucz (lub istniejący klucz, jeśli wybrano opcję użycia tego elementu).
7. Zanotuj wartość **identyfikatora klucza** .
8. Otwórz swój certyfikat. P8 w odpowiedniej wybranej aplikacji, na przykład [**Visual Studio Code**](https://code.visualstudio.com) następnie zanotuj wartość klucza. Jest to wartość między **-----rozpocznij-----klucza prywatnego** i **-----Zakończ-----klucza prywatnego** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Jest to **wartość tokenu** , która będzie używana później do konfigurowania **centrum powiadomień**. 

Po zakończeniu tych kroków należy skorzystać z poniższych informacji dotyczących [konfigurowania centrum powiadomień przy użyciu informacji dotyczących usługi APNs](#configure-your-notification-hub-with-apns-information):

- **Identyfikator zespołu** (zobacz krok 1)
- **Identyfikator pakietu** (zobacz krok 1)
- **Identyfikator klucza** (zobacz krok 7)
- **Wartość tokenu** tj. wartość klucza P8 (zobacz krok 8)

## <a name="create-a-provisioning-profile-for-the-app"></a>Tworzenie profilu inicjowania obsługi dla aplikacji

1. Wróć do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), wybierz pozycję **certyfikaty, identyfikatory & profile**, wybierz pozycję **Profile** z menu po lewej stronie, a następnie wybierz pozycję **+** Utwórz nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil aprowizacji** .

1. Wybierz pozycję **Programowanie aplikacji dla systemu iOS** w obszarze **programowanie** jako typ profilu aprowizacji, a następnie wybierz pozycję **Kontynuuj**.

    ![Lista profili aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Następnie wybierz identyfikator aplikacji utworzony na podstawie listy rozwijanej **Identyfikator aplikacji** , a następnie wybierz pozycję **Kontynuuj**.

    ![Wybieranie identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. W oknie **Wybieranie certyfikatów** wybierz certyfikat programistyczny używany do podpisywania kodu, a następnie wybierz pozycję **Kontynuuj**. Ten certyfikat nie jest utworzonym certyfikatem wypychania. Jeśli taki nie istnieje, należy go utworzyć. Jeśli certyfikat istnieje, przejdź do następnego kroku. Aby utworzyć certyfikat programistyczny, jeśli taki nie istnieje:

    1. Jeśli widzisz **Brak dostępnych certyfikatów**, wybierz pozycję **Utwórz certyfikat**.
    2. W sekcji **oprogramowanie** wybierz pozycję **programowanie firmy Apple**. Następnie wybierz pozycję **Kontynuuj**.
    3. Na ekranie **Tworzenie nowego certyfikatu** wybierz pozycję **Wybierz plik**.
    4. Przejdź do utworzonego wcześniej certyfikatu **żądania podpisania certyfikatu** , zaznacz go, a następnie wybierz pozycję **Otwórz**.
    5. Wybierz opcję **Kontynuuj**.
    6. Pobierz certyfikat programistyczny i Zapamiętaj lokalizację, w której został zapisany.

1. Wróć do strony **certyfikaty, identyfikatory & profile** , wybierz pozycję **Profile** z menu po lewej stronie, a następnie wybierz pozycję **+** Utwórz nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil aprowizacji** .

1. W oknie **Wybieranie certyfikatów** wybierz właśnie utworzony certyfikat programistyczny. Następnie wybierz pozycję **Kontynuuj**.

1. Następnie wybierz urządzenia, które mają być używane do testowania, a następnie wybierz pozycję **Kontynuuj**.

1. Na koniec wybierz nazwę profilu w polu **Nazwa profilu aprowizacji**, a następnie wybierz pozycję **Generuj**.

    ![Wybieranie nazwy profilu aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po utworzeniu nowego profilu aprowizacji wybierz pozycję **Pobierz**. Zapamiętaj lokalizację, w której została zapisana.

1. Przejdź do lokalizacji profilu aprowizacji, a następnie kliknij go dwukrotnie, aby go zainstalować na maszynie deweloperskiej Xcode.

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

W tej sekcji utworzysz centrum powiadomień i skonfigurujesz uwierzytelnianie za pomocą usługi APNs przy użyciu certyfikatu wypychania. p12 lub uwierzytelniania opartego na tokenach. Jeśli chcesz użyć już utworzonego centrum powiadomień, możesz przejść do kroku 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurowanie centrum powiadomień przy użyciu informacji dotyczących usługi APNs

W obszarze **usługi powiadomień** wybierz pozycję **Apple (APNs)** , a następnie postępuj zgodnie z odpowiednimi instrukcjami w sekcji [Tworzenie certyfikatu dla Notification Hubs](#creating-a-certificate-for-notification-hubs) .  

> [!NOTE]
> W przypadku kompilowania aplikacji za pomocą sklepu z aplikacjami lub profilu dystrybucji ad hoc Użyj środowiska **produkcyjnego** dla **trybu aplikacji**. Umożliwi to urządzeniu wysyłanie powiadomień wypychanych do użytkowników, którzy kupili aplikację ze sklepu.

### <a name="option-1-using-a-p12-push-certificate"></a>Opcja 1: używanie certyfikatu wypychania z. p12

1. Wybierz pozycję **Certyfikat**.

1. Wybierz ikonę pliku.

1. Wybierz wyeksportowany wcześniej plik. P12, a następnie wybierz pozycję **Otwórz**.

1. W razie potrzeby określ poprawne hasło.

1. Wybierz tryb **Piaskownica**.

    ![Konfigurowanie certyfikacji APNs w witrynie Azure Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Wybierz pozycję **Zapisz**.

### <a name="option-2-using-token-based-authentication"></a>Opcja 2: korzystanie z uwierzytelniania opartego na tokenach

1. Wybierz pozycję **token**.
1. Wprowadź następujące wartości, które zostały nabyte wcześniej:

    - **Identyfikator klucza**
    - **Identyfikator pakietu**
    - **Identyfikator zespołu**
    - **Token** 

1. Wybierz **piaskownicę**
1. Wybierz pozycję **Zapisz**. 

Twoje centrum powiadomień zostało już skonfigurowane za pomocą usługi APNs. Istnieją także parametry połączenia do rejestrowania aplikacji i wysyłania powiadomień wypychanych.