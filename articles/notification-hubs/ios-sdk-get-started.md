---
title: Wysyłanie powiadomień wypychanych do systemu iOS przy użyciu usługi Azure Notification Hubs i zestawu iOS SDK
description: W tym samouczku dowiesz się, jak wysyłać powiadomienia wypychane do urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs i usług powiadomień wypychanych firmy Apple.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c920c9b3b28df7f5bf3bf169ef88ab967f23649e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085381"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs

W tym samouczku przedstawiono sposób konfigurowania usługi Azure Notification Hubs i konfigurowania poświadczeń powiadomień wypychanych na urządzeniu z systemem iOS za pośrednictwem [usługi Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla kolejnych samouczków dotyczących systemu iOS w języku C i Swift oraz obejmuje następujące kroki:

- Wygeneruj plik żądania podpisania certyfikatu.
- Zażądaj aplikacji powiadomień wypychanych.
- Utwórz profil aprowizacji dla aplikacji.
- Tworzenie centrum powiadomień.
- Skonfiguruj centrum powiadomień przy użyciu informacji dotyczących usługi APNS.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/free/).

Potrzebne są również następujące elementy:

- Aktywne konto [dewelopera firmy Apple](https://developer.apple.com/) .
- Na komputerze Mac z systemem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), wraz z prawidłowym certyfikatem dewelopera zainstalowanym w łańcuchu kluczy.
- IPhone lub iPad z systemem iOS w wersji 10 lub nowszej.
- Urządzenie fizyczne zarejestrowane w [portalu firmy Apple](https://developer.apple.com/) i skojarzone z Twoim certyfikatem.

Pamiętaj, aby zapoznać się z [omówieniem Notification Hubs platformy Azure](notification-hubs-push-notification-overview.md) , jeśli nie znasz usługi.

> [!NOTE]
> Centrum powiadomień zostanie skonfigurowane do używania tylko trybu uwierzytelniania piaskownicy. Tego trybu uwierzytelniania nie należy używać w przypadku obciążeń produkcyjnych.

## <a name="generate-the-certificate-signing-request-file"></a>Generuj plik żądania podpisania certyfikatu

Usługa Apple Push Notification Service (APNS) używa certyfikatów do uwierzytelniania powiadomień wypychanych. Wykonaj instrukcje, aby utworzyć niezbędny certyfikat powiadomień wypychanych umożliwiający ich wysyłanie i odbieranie. Więcej informacji dotyczących tych pojęć można znaleźć w oficjalnej dokumentacji usługi [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Wygeneruj plik żądania podpisania certyfikatu (CSR), którego firma Apple używa do generowania podpisanego certyfikatu wypychania:

1. Na komputerze Mac uruchom narzędzie Keychain Access. Można go otworzyć z folderu **Utilities** lub **innego** folderu w programie Launchpad.

2. Wybierz pozycję **dostęp do łańcucha kluczy** , rozwiń węzeł **Asystent certyfikatów** , a następnie wybierz pozycję **Żądaj certyfikatu z urzędu certyfikacji** .

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

   > [!NOTE]
   > Domyślnie dostęp do łańcucha kluczy wybiera pierwszy element z listy. Może to być problem, jeśli jesteś w kategorii **Certyfikaty** i **urząd certyfikacji relacji deweloperów firmy Apple Worldwide** nie jest pierwszym elementem na liście. Przed wygenerowaniem CSR (żądanie podpisania certyfikatu) Upewnij się, że masz element niebędący kluczem lub wybrano klucz **urzędu certyfikacji relacji deweloperów firmy Apple Worldwide** .

3. Wybierz **adres E-mail użytkownika** , wprowadź wartość **Nazwa pospolita** , upewnij się, że określono opcję **Zapisano na dysku** , a następnie wybierz pozycję **Kontynuuj** . Pozostaw puste **adresy E-mail urzędu certyfikacji** , ponieważ nie jest to wymagane.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

4. Wprowadź nazwę pliku CSR w obszarze **Zapisz jako** , wybierz lokalizację w **miejscu** , a następnie wybierz pozycję **Zapisz** .

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

   Ta akcja zapisuje plik CSR w wybranej lokalizacji. Domyślna lokalizacja to **Desktop** . Zapamiętaj wybraną lokalizację pliku.

Następnie zarejestruj aplikację za pomocą firmy Apple, Włącz powiadomienia wypychane i przekaż wyeksportowany certyfikat CSR w celu utworzenia certyfikatu wypychania.

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Aby wysyłać powiadomienia wypychane do aplikacji systemu iOS, zarejestruj swoją aplikację za pomocą firmy Apple, a także Zarejestruj się w celu otrzymywania powiadomień wypychanych.

1. Jeśli aplikacja nie została jeszcze zarejestrowana, przejdź do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) w centrum deweloperów firmy Apple. Zaloguj się do portalu przy użyciu identyfikatora Apple ID, a następnie wybierz pozycję **identyfikatory** . Następnie wybierz pozycję **+** , aby zarejestrować nową aplikację.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

2. Na ekranie **Rejestrowanie nowego identyfikatora** wybierz przycisk radiowy **identyfikatory aplikacji** . Następnie wybierz pozycję **Kontynuuj** .

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

3. Zaktualizuj następujące trzy wartości dla nowej aplikacji, a następnie wybierz pozycję **Kontynuuj** :

   - **Opis** : wpisz nazwę opisową aplikacji.
   - **Identyfikator pakietu** : Wprowadź identyfikator pakietu formularza **identyfikator organizacji. Nazwa produktu** , jak wspomniano w [przewodniku po dystrybucji aplikacji](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). **Identyfikator organizacji** i wartości **nazwy produktu** muszą być zgodne z identyfikatorem organizacji i nazwą produktu używaną podczas tworzenia projektu Xcode. Na poniższym zrzucie ekranu wartość **NotificationHubs** jest używana jako identyfikator organizacji, a wartość **getstarted** jest używana jako nazwa produktu. Upewnij się, że wartość **identyfikatora pakietu** pasuje do wartości w projekcie Xcode, tak aby Xcode używała poprawnego profilu publikacji.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

   - **Powiadomienia wypychane** : zaznacz opcję **powiadomienia wypychane** w sekcji **możliwości** .

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

      Ta akcja spowoduje wygenerowanie identyfikatora aplikacji i żądań potwierdzających informacje. Wybierz pozycję **Kontynuuj** , a następnie wybierz pozycję **zarejestruj** , aby potwierdzić nowy identyfikator aplikacji.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

      Po wybraniu pozycji **zarejestruj** nowy identyfikator aplikacji zostanie wyświetlony na stronie **certyfikaty, identyfikatory & profile** .

4. Na stronie **certyfikaty, identyfikatory & profile** w obszarze **identyfikatory** Znajdź element wiersza identyfikatora aplikacji, który został właśnie utworzony, a następnie wybierz jego wiersz w celu wyświetlenia ekranu **Edytowanie konfiguracji identyfikatora aplikacji** .

## <a name="create-a-certificate-for-notification-hubs"></a>Utwórz certyfikat dla Notification Hubs

> [!NOTE]
> W wersji systemu iOS 13 można odbierać tylko powiadomienia dyskretne przy użyciu uwierzytelniania opartego na tokenach. Jeśli używasz uwierzytelniania opartego na certyfikatach dla poświadczeń usługi APNS, musisz przełączyć się do korzystania z uwierzytelniania opartego na tokenach.

Aby umożliwić centrum powiadomień współdziałanie z usługą **APNs** , wymagany jest certyfikat. Można to zrobić na jeden z dwóch sposobów:

- Utwórz plik **. p12** , który można przekazać bezpośrednio do Notification Hubs.

- Utwórz plik **. P8** , który może być używany do [uwierzytelniania opartego na tokenach](notification-hubs-push-notification-http2-token-authentication.md) (rozwiązanie nowsze).

Druga opcja ma wiele korzyści w porównaniu z użyciem certyfikatów, zgodnie z opisem w temacie [uwierzytelnianie oparte na tokenach (http/2) dla usługi APNs](notification-hubs-push-notification-http2-token-authentication.md). Jednak kroki są dostępne dla obu tych metod.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Opcja 1: utworzenie certyfikatu wypychania o stanie P12, który można przekazać bezpośrednio do Notification Hubs

1. Przewiń w dół do opcji sprawdzone **powiadomienia wypychane** , a następnie wybierz pozycję **Konfiguruj** , aby utworzyć certyfikat.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

2. Zostanie wyświetlone okno **Certyfikaty SSL usługi powiadomień wypychanych firmy Apple** . Wybierz przycisk **Utwórz certyfikat** w sekcji **programowanie certyfikatu SSL** .

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

   Zostanie wyświetlony ekran **Utwórz nowy certyfikat** .

   > [!NOTE]
   > Instrukcje w tym samouczku obejmują użycie certyfikatu deweloperskiego. Ten sam proces jest używany podczas rejestrowania certyfikatu produkcyjnego. Upewnij się, że używasz tego samego typu certyfikatu podczas wysyłania powiadomień.

3. Wybierz pozycję **Wybierz plik** , przejdź do lokalizacji, w której ZAPISANO plik CSR z pierwszego zadania, a następnie kliknij dwukrotnie nazwę certyfikatu w celu jego załadowania. Następnie wybierz pozycję **Kontynuuj** .

4. Po utworzeniu certyfikatu przez portal wybierz przycisk **Pobierz** . Zapisz certyfikat i Zapamiętaj lokalizację, w której został zapisany.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

   Certyfikat zostanie pobrany i zapisany w folderze **pobierania** .

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

   Domyślnie pobrany certyfikat programistyczny nosi nazwę **aps_development. cer** .

5. Kliknij dwukrotnie pobrany certyfikat wypychania **APS \_ Development. cer** . Ta akcja powoduje zainstalowanie nowego certyfikatu w narzędziu Keychain, jak przedstawiono na poniższym rysunku:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

   Mimo że nazwa w certyfikacie może być inna, nazwa zostanie poprzedzona przez **Apple Development iOS usługi wypychania** .

6. W narzędziu Keychain Access kliknij prawym przyciskiem myszy nowy certyfikat powiadomień wypychanych utworzony w kategorii **Certyfikaty** . Wybierz pozycję **Eksportuj** , Nazwij plik, wybierz format **. p12** , a następnie wybierz pozycję **Zapisz** .

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

   Możesz zdecydować się na ochronę certyfikatu hasłem, ale jest to opcjonalne. Kliknij przycisk **OK** , jeśli chcesz pominąć tworzenie haseł. Zanotuj nazwę pliku i lokalizację wyeksportowanego certyfikatu .p12. Są one używane do włączania uwierzytelniania przy użyciu usługi APNS.

   > [!NOTE]
   > Nazwa i lokalizacja pliku. p12 mogą różnić się od informacji w tym samouczku.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Opcja 2: Utwórz certyfikat P8, którego można użyć do uwierzytelniania opartego na tokenach

1. Zwróć uwagę na następujące informacje:

   - **Prefiks identyfikatora aplikacji** (jest to **Identyfikator zespołu** )
   - **Identyfikator pakietu**

2. W obszarze **certyfikaty, identyfikatory & profile** , kliknij pozycję **klucze** . Jeśli masz już skonfigurowany klucz dla usługi **APNs** , możesz użyć pobranego wcześniej certyfikatu P8 bezpośrednio po jego utworzeniu. Jeśli tak, możesz zignorować kroki od 3 do 5.

3. Kliknij **+** przycisk (lub przycisk **Utwórz klucz** ), aby utworzyć nowy klucz.

4. Podaj odpowiednią wartość **nazwy klucza** , zaznacz opcję **usługi Apple Push Notification Service (APNs)** , a następnie kliknij przycisk **Kontynuuj** , a następnie **zarejestruj** się na następnym ekranie.

5. Kliknij pozycję **Pobierz** , a następnie przenieś plik **P8** (z prefiksem `AuthKey_` ) do bezpiecznego katalogu lokalnego, a następnie kliknij pozycję **gotowe** .

   > [!IMPORTANT]
   > Pamiętaj, aby zachować plik. P8 w bezpiecznym miejscu (i zapisać kopię zapasową). Po pobraniu klucza nie można go pobrać od nowa. kopia serwera zostanie usunięta.

6. W obszarze **klucze** kliknij właśnie utworzony klucz (lub istniejący klucz, jeśli wybrano opcję użycia tego elementu).

7. Zanotuj wartość **identyfikatora klucza** .

8. Otwórz swój certyfikat. P8 w odpowiedniej wybranej aplikacji, na przykład [Visual Studio Code](https://code.visualstudio.com/), a następnie zanotuj wartość klucza. Jest to wartość między **-----rozpocznij-----klucza prywatnego** i **-----Zakończ-----klucza prywatnego** .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Jest to wartość tokenu, która będzie używana później w celu skonfigurowania Notification Hubs.

Po zakończeniu tych kroków należy skorzystać z poniższych informacji dotyczących [konfigurowania centrum powiadomień przy użyciu informacji dotyczących usługi APNs](#configure-the-notification-hub-with-apns-information):

- **Identyfikator zespołu** (zobacz krok 1)
- **Identyfikator pakietu** (zobacz krok 1)
- **Identyfikator klucza** (zobacz krok 7)
- **Wartość tokenu** (wartość klucza. P8, zobacz krok 8)

## <a name="create-a-provisioning-profile"></a>Utwórz profil aprowizacji

1. Wróć do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), wybierz pozycję **certyfikaty, identyfikatory & profile** , wybierz pozycję **Profile** z menu po lewej stronie, a następnie wybierz pozycję **+** Utwórz nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil aprowizacji** .

2. Wybierz pozycję **Programowanie aplikacji dla systemu iOS** w obszarze **programowanie** jako typ profilu aprowizacji, a następnie wybierz pozycję **Kontynuuj** .

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

3. Następnie wybierz identyfikator aplikacji utworzony na podstawie listy rozwijanej **Identyfikator aplikacji** , a następnie wybierz pozycję **Kontynuuj** .

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

4. W oknie **Wybieranie certyfikatów** wybierz certyfikat programistyczny używany do podpisywania kodu, a następnie wybierz pozycję **Kontynuuj** . Ten certyfikat nie jest utworzonym certyfikatem wypychania. Jeśli taki nie istnieje, należy go utworzyć. Jeśli certyfikat istnieje, przejdź do następnego kroku. Aby utworzyć certyfikat programistyczny, jeśli taki nie istnieje:

   1. Jeśli widzisz **Brak dostępnych certyfikatów** , wybierz pozycję **Utwórz certyfikat** .
   2. W sekcji **oprogramowanie** wybierz pozycję **programowanie firmy Apple** . Następnie wybierz pozycję **Kontynuuj** .
   3. Na ekranie **Tworzenie nowego certyfikatu** wybierz pozycję **Wybierz plik** .
   4. Przejdź do utworzonego wcześniej certyfikatu **żądania podpisania certyfikatu** , zaznacz go, a następnie wybierz pozycję **Otwórz** .
   5. Wybierz opcję **Kontynuuj** .
   6. Pobierz certyfikat programistyczny i Zapamiętaj lokalizację, w której został zapisany.

5. Wróć do strony **certyfikaty, identyfikatory & profile** , wybierz pozycję **Profile** z menu po lewej stronie, a następnie wybierz pozycję **+** Utwórz nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil aprowizacji** .

6. W oknie **Wybieranie certyfikatów** wybierz właśnie utworzony certyfikat programistyczny. Następnie wybierz pozycję **Kontynuuj** .

7. Następnie wybierz urządzenia, które mają być używane do testowania, a następnie wybierz pozycję **Kontynuuj** .

8. Na koniec wybierz nazwę profilu w polu **Nazwa profilu aprowizacji** , a następnie wybierz pozycję **Generuj** .

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

9. Po utworzeniu nowego profilu aprowizacji wybierz pozycję **Pobierz** . Zapamiętaj lokalizację, w której została zapisana.

10. Przejdź do lokalizacji profilu aprowizacji, a następnie kliknij go dwukrotnie, aby go zainstalować na maszynie deweloperskiej Xcode.

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

W tej sekcji utworzysz centrum powiadomień i skonfigurujesz uwierzytelnianie za pomocą usługi APNS przy użyciu certyfikatu wypychania. p12 lub uwierzytelniania opartego na tokenach. Jeśli chcesz użyć już utworzonego centrum powiadomień, możesz przejść do kroku 5.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **Notification Hubs** w sekcji **mobilnej** . Wybierz ikonę gwiazdki obok nazwy usługi, aby dodać usługę do sekcji **Ulubione** w menu po lewej stronie. Po dodaniu **Notification Hubs** do **ulubionych** wybierz ją.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

3. Na stronie **Notification Hubs** wybierz pozycję **Dodaj** na pasku narzędzi.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

4. Na stronie **Notification Hubs** wykonaj następujące czynności:

   1. Wprowadź nazwę w **centrum powiadomień** .
   2. Wprowadź nazwę w polu **Utwórz nową przestrzeń nazw** . Przestrzeń nazw zawiera jeden lub więcej centrów powiadomień.
   3. Wybierz wartość z listy rozwijanej **Lokalizacja** . Ta wartość określa lokalizację, w której ma zostać utworzone centrum powiadomień.
   4. Wybierz istniejącą grupę zasobów w **grupie zasobów** lub Utwórz nową grupę zasobów.
   5. Wybierz pozycję **Utwórz** .

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

5. Wybierz pozycję **powiadomienia** (ikona dzwonka), a następnie wybierz pozycję **Przejdź do zasobu** . Możesz również odświeżyć listę na stronie **Notification Hubs** i wybrać centrum.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

6. Wybierz z listy pozycję **Zasady dostępu** . Należy pamiętać, że dwa parametry połączenia są dostępne dla Ciebie. Będą one potrzebne później do obsługi powiadomień wypychanych.

   > [!IMPORTANT]
   > Nie należy używać zasad **DefaultFullSharedAccessSignatureymi** w aplikacji. Jest to przeznaczone tylko do użycia w zapleczu.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Konfigurowanie centrum powiadomień przy użyciu informacji dotyczących usługi APNS

W obszarze **usługi powiadomień** wybierz pozycję **Apple (APNs)** , a następnie postępuj zgodnie z odpowiednimi instrukcjami w sekcji [Tworzenie certyfikatu dla Notification Hubs](#create-a-certificate-for-notification-hubs) .

> [!NOTE]
> Używaj środowiska **produkcyjnego** w **trybie aplikacji** tylko wtedy, gdy chcesz wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu.

### <a name="option-1-use-a-p12-push-certificate"></a>Opcja 1: Użyj certyfikatu wypychania. p12

1. Wybierz pozycję **Certyfikat** .

2. Wybierz ikonę pliku.

3. Wybierz wyeksportowany wcześniej plik. P12, a następnie wybierz pozycję **Otwórz** .

4. W razie potrzeby określ poprawne hasło.

5. Wybierz tryb **Piaskownica** .

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Zrzut ekranu, który wyróżnia opcję Żądaj certyfikatu z urzędu certyfikacji.":::

6. Wybierz pozycję **Zapisz** .

### <a name="option-2-use-token-based-authentication"></a>Opcja 2: korzystanie z uwierzytelniania opartego na tokenach

1. Wybierz pozycję **token** .

2. Wprowadź następujące wartości, które zostały nabyte wcześniej:

   - **Identyfikator klucza**
   - **Identyfikator pakietu**
   - **Identyfikator zespołu**
   - **Token**

3. Wybierz **piaskownicę**

4. Wybierz pozycję **Zapisz** .

Twoje centrum powiadomień zostało już skonfigurowane za pomocą usługi APNS. Istnieją również parametry połączenia potrzebne do zarejestrowania aplikacji i wysyłania powiadomień wypychanych.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono i skonfigurowano centrum powiadomień na platformie Azure i skonfigurowano go w taki sposób, aby zezwalał na wysyłanie powiadomień do aplikacji za pomocą Apple Push Notification Service (APNS). Następnie utworzymy przykładową aplikację dla systemu iOS i zintegrujemy zestaw SDK centrów powiadomień platformy Azure, dzięki czemu będzie można odbierać powiadomienia wypychane wysyłane przez Azure Portal. Przejdź do następującego samouczka w zależności od wybranego języka:

- [Samouczek: wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-300.md)
