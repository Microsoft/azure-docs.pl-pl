---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081435"
---
### <a name="register-your-ios-app-for-push-notifications"></a>Rejestrowanie aplikacji systemu iOS na potrzeby powiadomień wypychanych

Aby wysyłać powiadomienia wypychane do aplikacji systemu iOS, zarejestruj swoją aplikację za pomocą firmy Apple, a także Zarejestruj się w celu otrzymywania powiadomień wypychanych.  

1. Jeśli aplikacja nie została jeszcze zarejestrowana, przejdź do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) w centrum deweloperów firmy Apple. Zaloguj się do portalu przy użyciu identyfikatora Apple ID, przejdź do opcji **certyfikaty, identyfikatory & profile**, a następnie wybierz pozycję **identyfikatory**. Kliknij **+** , aby zarejestrować nową aplikację.

    ![Strona identyfikatorów aplikacji w portalu aprowizowania dla systemu iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Na ekranie **Rejestrowanie nowego identyfikatora** wybierz przycisk radiowy **identyfikatory aplikacji** . Następnie wybierz pozycję **Kontynuuj**.

    ![nowy identyfikator strony rejestracji w portalu aprowizacji systemu iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. Zaktualizuj następujące trzy wartości dla nowej aplikacji, a następnie wybierz pozycję **Kontynuuj**:

   * **Opis**: wpisz nazwę opisową aplikacji.

   * **Identyfikator pakietu**: Wprowadź identyfikator pakietu formularza **com. <organization_identifier>. <Product_Name>** , jak wspomniano w [przewodniku po dystrybucji aplikacji](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Na poniższym zrzucie ekranu `mobcat` wartość jest używana jako identyfikator organizacji, a wartość **PushDemo** jest używana jako nazwa produktu.

      ![Strona identyfikatora aplikacji w portalu aprowizacji systemu iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Powiadomienia wypychane**: zaznacz opcję **powiadomienia wypychane** w sekcji **możliwości** .

      ![Formularz rejestracji nowego identyfikatora aplikacji](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Ta akcja spowoduje wygenerowanie identyfikatora aplikacji i żądań potwierdzających informacje. Wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **zarejestruj** , aby potwierdzić nowy identyfikator aplikacji.

      ![Potwierdź nowy identyfikator aplikacji](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Po wybraniu pozycji **zarejestruj**nowy identyfikator aplikacji zostanie wyświetlony jako element wiersza na stronie **certyfikaty, identyfikatory & profile** .

1. Na stronie **certyfikaty, identyfikatory & profile** w obszarze **identyfikatory**Znajdź utworzony element wiersza identyfikatora aplikacji. Następnie wybierz jego wiersz, aby wyświetlić ekran **Edytowanie konfiguracji identyfikatora aplikacji** .

#### <a name="creating-a-certificate-for-notification-hubs"></a>Tworzenie certyfikatu dla Notification Hubs

Certyfikat jest wymagany, aby centrum powiadomień działało z **usługami Apple Push Notification Services (APNs)** i można je udostępnić na jeden z dwóch sposobów:

1. [Tworzenie certyfikatu wypychania P12, który można przekazać bezpośrednio do centrum powiadomień](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (*oryginalne podejście*)

1. [Tworzenie certyfikatu P8, który może być używany do uwierzytelniania opartego na tokenach](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*rozwiązanie nowsze i zalecane*)

Nowe podejście ma wiele korzyści, które zostały udokumentowane w ramach [uwierzytelniania opartego na tokenach (http/2) dla usługi APNs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Wymagana jest mniejsza liczba kroków, ale jest to również wymagane dla konkretnych scenariuszy. Jednak należy wykonać kroki dla obu metod, ponieważ będzie ona działała na potrzeby tego samouczka.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>Opcja 1: Tworzenie certyfikatu wypychania P12, który można przekazać bezpośrednio do centrum powiadomień

1. Na komputerze Mac uruchom narzędzie Keychain Access. Można go otworzyć z folderu **Utilities** lub **innego** folderu w programie Launchpad.

1. Wybierz pozycję **dostęp do łańcucha kluczy**, rozwiń węzeł **Asystent certyfikatów**, a następnie wybierz pozycję **Żądaj certyfikatu z urzędu certyfikacji**.

    ![Żądanie nowego certyfikatu za pomocą narzędzia Keychain Access](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Domyślnie dostęp do łańcucha kluczy wybiera pierwszy element z listy. Może to być problem, jeśli jesteś w kategorii **Certyfikaty** i **urząd certyfikacji relacji deweloperów firmy Apple Worldwide** nie jest pierwszym elementem na liście. Przed wygenerowaniem CSR (żądanie podpisania certyfikatu) Upewnij się, że masz element niebędący kluczem lub wybrano klucz **urzędu certyfikacji relacji deweloperów firmy Apple Worldwide** .

1. Wybierz **adres E-mail użytkownika**, wprowadź wartość **Nazwa pospolita** , upewnij się, że określono opcję **Zapisano na dysku**, a następnie wybierz pozycję **Kontynuuj**. Pozostaw puste **adresy E-mail urzędu certyfikacji** , ponieważ nie jest to wymagane.

    ![Oczekiwanie informacji o certyfikacie](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Wprowadź nazwę **pliku żądania podpisania certyfikatu (CSR)** w obszarze **Zapisz jako**, wybierz lokalizację w **miejscu**, a następnie wybierz pozycję **Zapisz**.

    ![Wybierz nazwę pliku dla certyfikatu](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ta akcja zapisuje **plik CSR** w wybranej lokalizacji. Domyślna lokalizacja to **Desktop**. Zapamiętaj wybraną lokalizację pliku.

1. Wróć do strony **certyfikaty, identyfikatory & profile** w [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), przewiń w dół do opcji sprawdzone **powiadomienia wypychane** , a następnie wybierz pozycję **Konfiguruj** , aby utworzyć certyfikat.

    ![Edytowanie strony identyfikatorów aplikacji](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Zostanie wyświetlone okno **Certyfikaty protokołu TLS/SSL usługi wypychania powiadomień firmy Apple** . Wybierz przycisk **Utwórz certyfikat** w sekcji Tworzenie **certyfikatu TLS/SSL** .

    ![Przycisk tworzenia certyfikatu dla identyfikatora aplikacji](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zostanie wyświetlony ekran **Utwórz nowy certyfikat** .

    > [!NOTE]
    > Instrukcje w tym samouczku obejmują użycie certyfikatu deweloperskiego. Ten sam proces jest używany podczas rejestrowania certyfikatu produkcyjnego. Należy po prostu pamiętać, aby używać tego samego typu certyfikatu podczas wysyłania powiadomień.

1. Wybierz pozycję **Wybierz plik**, przejdź do lokalizacji, w której zapisano **plik CSR**, a następnie kliknij dwukrotnie nazwę certyfikatu w celu jego załadowania. Następnie wybierz pozycję **Kontynuuj**.

1. Po utworzeniu certyfikatu przez portal wybierz przycisk **Pobierz** . Zapisz certyfikat i Zapamiętaj lokalizację, w której został zapisany.

    ![Strona pobierania wygenerowanego certyfikatu](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certyfikat zostanie pobrany i zapisany na komputerze w folderze **pobierania** .

    ![Lokalizowanie pliku certyfikatu w folderze Pobrane](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Domyślnie pobrany certyfikat programistyczny nosi nazwę **aps_development. cer**.

1. Kliknij dwukrotnie pobrany certyfikat powiadomień wypychanych **aps_development.cer**. Ta akcja powoduje zainstalowanie nowego certyfikatu w narzędziu Keychain, jak przedstawiono na poniższym rysunku:

    ![Lista certyfikatów narzędzia Keychain Access z nowym certyfikatem](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Mimo że nazwa w certyfikacie może być inna, nazwa zostanie poprzedzona przez **Apple Development iOS usługi wypychania** i ma skojarzony odpowiedni identyfikator pakietu.

1. W oknie dostęp do **Control**łańcucha kluczy  +  **kliknij** nowy certyfikat wypychania utworzony w kategorii **Certyfikaty** . Wybierz pozycję **Eksportuj**, Nazwij plik, wybierz format **P12** , a następnie wybierz pozycję **Zapisz**.

    ![Eksportowanie certyfikatu w formacie p12](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Możesz zdecydować się na ochronę certyfikatu hasłem, ale hasło jest opcjonalne. Kliknij przycisk **OK** , jeśli chcesz pominąć tworzenie haseł. Zanotuj nazwę pliku i lokalizację wyeksportowanego certyfikatu P12. Są one używane do włączania uwierzytelniania przy użyciu usługi APNs.

    > [!NOTE]
    > Nazwa i lokalizacja pliku P12 mogą różnić się od informacji w tym samouczku.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Opcja 2: Tworzenie certyfikatu P8, który może być używany do uwierzytelniania opartego na tokenach

1. Zwróć uwagę na następujące informacje:

    * **Prefiks identyfikatora aplikacji** (**Identyfikator zespołu**)
    * **Identyfikator pakietu**

1. W obszarze **certyfikaty, identyfikatory & profile**, kliknij pozycję **klucze**.

   > [!NOTE]
   > Jeśli masz już skonfigurowany klucz dla usługi **APNs**, możesz użyć poprawnego certyfikatu P8 po jego utworzeniu. Jeśli tak, możesz zignorować kroki od **3** do **5**.

1. Kliknij **+** przycisk (lub przycisk **Utwórz klucz** ), aby utworzyć nowy klucz.
1. Podaj odpowiednią wartość **nazwy klucza** , a następnie sprawdź opcję **usługi Apple Push Notification Service (APNs)** , a następnie kliknij przycisk **Kontynuuj**, a następnie **zarejestruj** się na następnym ekranie.
1. Kliknij pozycję **Pobierz** , a następnie przenieś plik **P8** (z prefiksem *AuthKey_*) do bezpiecznego katalogu lokalnego, a następnie kliknij pozycję **gotowe**.

   > [!NOTE]
   > Pamiętaj, aby zachować plik P8 w bezpiecznym miejscu (i zapisać kopię zapasową). Po pobraniu klucza nie można go pobrać z jego pobrania, ponieważ kopia serwera zostanie usunięta.
  
1. W obszarze **klucze**kliknij utworzony klucz (lub istniejący klucz, jeśli wybrano opcję użycia tego polecenia).
1. Zanotuj wartość **identyfikatora klucza** .
1. Otwórz swój certyfikat P8 w odpowiedniej wybranej aplikacji, takiej jak [**Visual Studio Code**](https://code.visualstudio.com). Zanotuj wartość klucza (między **-----rozpocznij-----klucza prywatnego** i **----------klucz prywatny**).

    -----ROZPOCZNIJ-----KLUCZA PRYWATNEGO  
    <key_value>  
    ----------KOŃCOWYM KLUCZEM PRYWATNYM

    > [!NOTE]
    > Jest to **wartość tokenu** , która będzie używana później do konfigurowania **centrum powiadomień**.

Po wykonaniu tych kroków należy skorzystać z poniższych informacji dotyczących [konfigurowania centrum powiadomień przy użyciu informacji dotyczących usługi APNs](#configure-your-notification-hub-with-apns-information):

* **Identyfikator zespołu** (zobacz krok 1)
* **Identyfikator pakietu** (zobacz krok 1)
* **Identyfikator klucza** (zobacz krok 7)
* **Wartość tokenu** (wartość klucza P8 uzyskana w kroku 8)

### <a name="create-a-provisioning-profile-for-the-app"></a>Tworzenie profilu inicjowania obsługi dla aplikacji

1. Wróć do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), wybierz pozycję **certyfikaty, identyfikatory & profile**, wybierz pozycję **Profile** z menu po lewej stronie, a następnie wybierz pozycję **+** Utwórz nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil aprowizacji** .

1. Wybierz pozycję **Programowanie aplikacji dla systemu iOS** w obszarze **programowanie** jako typ profilu aprowizacji, a następnie wybierz pozycję **Kontynuuj**.

    ![Lista profili aprowizacji](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Następnie wybierz identyfikator aplikacji utworzony na podstawie listy rozwijanej **Identyfikator aplikacji** , a następnie wybierz pozycję **Kontynuuj**.

    ![Wybieranie identyfikatora aplikacji](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. W oknie **Wybieranie certyfikatów** wybierz certyfikat programistyczny używany do podpisywania kodu, a następnie wybierz pozycję **Kontynuuj**.

    > [!NOTE]
    > Ten certyfikat nie jest certyfikatem wypychanym utworzonym w [poprzednim kroku](#creating-a-certificate-for-notification-hubs). Jest to Twój certyfikat programistyczny. Jeśli jeden z tych elementów nie istnieje, należy go utworzyć, ponieważ jest to [wymaganie wstępne](#prerequisites) dla tego samouczka. Certyfikaty deweloperów można tworzyć w portalu dla [deweloperów firmy Apple](https://developer.apple.com)za pośrednictwem usługi [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) lub [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/).

1. Wróć do strony **certyfikaty, identyfikatory & profile** , wybierz pozycję **Profile** z menu po lewej stronie, a następnie wybierz pozycję **+** Utwórz nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil aprowizacji** .

1. W oknie **Wybieranie certyfikatów** wybierz utworzony certyfikat programistyczny. Następnie wybierz pozycję **Kontynuuj**.

1. Następnie wybierz urządzenia, które mają być używane do testowania, a następnie wybierz pozycję **Kontynuuj**.

1. Na koniec wybierz nazwę profilu w polu **Nazwa profilu aprowizacji**, a następnie wybierz pozycję **Generuj**.

    ![Wybieranie nazwy profilu aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po utworzeniu nowego profilu aprowizacji wybierz pozycję **Pobierz**. Zapamiętaj lokalizację, w której została zapisana.

1. Przejdź do lokalizacji profilu aprowizacji, a następnie kliknij go dwukrotnie, aby go zainstalować na komputerze deweloperskim.
