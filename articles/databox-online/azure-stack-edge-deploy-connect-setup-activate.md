---
title: Samouczek dotyczący nawiązywania połączenia z usługą, konfigurowania, aktywowania Azure Stack EDGE Pro w Azure Portal | Microsoft Docs
description: Samouczek wdrażania Azure Stack EDGE Pro powoduje nawiązanie połączenia, skonfigurowanie i aktywowanie urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
ms.openlocfilehash: af0c305cc13b94666a87d937d1eac10586135a4d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067694"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge-pro"></a>Samouczek: łączenie, Konfigurowanie i aktywowanie Azure Stack EDGE Pro 

W tym samouczku opisano sposób nawiązywania połączenia z usługą, konfigurowania i aktywowania urządzenia Azure Stack EDGE Pro przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces instalacji i aktywacji może potrwać około 20 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Nawiązywanie połączenia z urządzeniem fizycznym
> * Konfigurowanie i aktywowanie urządzenia fizycznego

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Azure Stack EDGE Pro upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro](azure-stack-edge-deploy-install.md).
* Masz klucz aktywacji z usługi Azure Stack Edge, która została utworzona w celu zarządzania urządzeniem Azure Stack EDGE Pro. Aby uzyskać więcej informacji, przejdź do pozycji [przygotowanie do wdrożenia Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Nawiązywanie połączenia z konfiguracją lokalnego interfejsu użytkownika sieci Web

1. Skonfiguruj kartę Ethernet na komputerze, aby nawiązać połączenie z urządzeniem Azure Stack EDGE Pro ze statycznym adresem IP 192.168.100.5 i podsiecią 255.255.255.0.

2. Podłącz komputer do PORTU 1 na urządzeniu. Skorzystaj z poniższej ilustracji, aby zidentyfikować PORT 1 na urządzeniu.

    ![Płyta montażowa okablowanego urządzenia](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

3. Otwórz okno przeglądarki i uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`.  
    Ta akcja może potrwać kilka minut po włączeniu urządzenia.

    Zostanie wyświetlony komunikat o błędzie lub ostrzeżenie informujące o problemie z certyfikatem zabezpieczeń witryny internetowej.
   
    ![Komunikat o błędzie dotyczący certyfikatu zabezpieczeń witryny sieci Web](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Wybierz pozycję **Kontynuuj na tej stronie sieci Web**.  
    Te kroki mogą się różnić w zależności od używanej przeglądarki.

5. Zaloguj się do internetowego interfejsu użytkownika urządzenia. Domyślne hasło to *Password1*. 
   
    ![Strona logowania do urządzenia z systemem Azure Stack Edge](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. W wierszu polecenia Zmień hasło administratora urządzenia.  
    Nowe hasło musi zawierać od 8 do 16 znaków. Hasło musi zawierać trzy z następujących rodzajów znaków: małe litery, wielkie litery, cyfry i znaki specjalne.

Jesteś teraz na pulpicie nawigacyjnym Twojego urządzenia.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurowanie i aktywowanie urządzenia fizycznego
 
Pulpit nawigacyjny wyświetla różne ustawienia, które są wymagane do skonfigurowania i zarejestrowania urządzenia fizycznego za pomocą usługi Azure Stack Edge. **Nazwa urządzenia**, **Ustawienia sieci**, **Ustawienia serwera proxy sieci Web** i **Ustawienia czasu** są opcjonalne. Jedyne wymagane ustawienia to **Ustawienia chmury**.
   
![Strona "pulpit nawigacyjny" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. W lewym okienku wybierz pozycję **Nazwa urządzenia**, a następnie wprowadź przyjazną nazwę urządzenia.  
    Przyjazna nazwa musi zawierać od 1 do 15 znaków i zawierać litery, cyfry i łączniki.

    ![Strona "nazwa urządzenia" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. Obowiązkowe W okienku po lewej stronie wybierz pozycję **Ustawienia sieci** , a następnie skonfiguruj ustawienia.  
    Na urządzeniu fizycznym znajduje się sześć interfejsów sieciowych. PORT 1 i PORT 2 są interfejsami sieciowymi 1 GB/s. PORT 3, port 4, PORT 5 i PORT 6 to wszystkie 25 GB/s interfejsów sieciowych, które mogą również działać jako interfejsy sieciowe 10 GB/s. PORT 1 jest automatycznie konfigurowany jako port tylko do zarządzania, a PORT 2 do portu 6 to wszystkie porty danych. Zostanie wyświetlona strona **Ustawienia sieci** .
    
    ![Strona ustawień sieciowych lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Podczas konfigurowania ustawień sieci należy pamiętać o następujących kwestiach:

   - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieć, brama i DNS są przypisywane automatycznie.
   - Jeśli usługa DHCP nie jest włączona, w razie konieczności można przypisywać statyczne adresy IP.
   - Interfejs sieciowy można skonfigurować jako adres IPv4.

     >[!NOTE] 
     > Zaleca się, aby nie przełączać lokalnego adresu IP interfejsu sieciowego ze statycznego na protokół DHCP, chyba że dostępny jest inny adres IP, aby połączyć się z urządzeniem. W przypadku korzystania z jednego interfejsu sieciowego i przełączenia na protokół DHCP nie będzie możliwości określenia adresu DHCP. Jeśli chcesz zmienić na adres DHCP, zaczekaj, aż urządzenie zostanie zarejestrowane w usłudze, a następnie dokonaj zmiany. Następnie można wyświetlić adresy IP wszystkich kart we **właściwościach urządzenia** w Azure Portal dla usługi.

3. Obowiązkowe W okienku po lewej stronie wybierz pozycję **Ustawienia serwera proxy sieci Web**, a następnie skonfiguruj serwer proxy sieci Web. Mimo że konfiguracja serwera proxy sieci Web jest opcjonalna, jeśli używasz serwera proxy sieci Web, możesz go skonfigurować tylko na tej stronie.
   
   ![Strona lokalnego interfejsu użytkownika sieci Web — ustawienia serwera proxy sieci Web](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na stronie **Ustawienia serwera proxy sieci Web** wykonaj następujące czynności:
   
   a. W polu **adres URL serwera proxy sieci Web** wprowadź adres URL w tym formacie: `http://host-IP address or FQDN:Port number` . Adresy URL HTTPS nie są obsługiwane.

   b. W obszarze **Uwierzytelnianie** wybierz opcję **Brak** lub **NTLM**. W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu z systemem Azure Stack EDGE Pro zalecamy ustawienie uwierzytelniania serwera proxy sieci Web na **Brak**. **Uwierzytelnianie NTLM** nie jest obsługiwane.

   c. Jeśli używasz uwierzytelniania, wprowadź nazwę użytkownika i hasło.

   d. Aby sprawdzić poprawność skonfigurowanych ustawień serwera proxy sieci Web i zastosować je, wybierz pozycję **Zastosuj ustawienia**.

   > [!NOTE]
   > Pliki proxy-autoconfig (PAC) nie są obsługiwane. Plik PAC definiuje, w jaki sposób przeglądarki sieci Web i inni agenci użytkowników mogą automatycznie wybierać odpowiedni serwer proxy (metoda dostępu) do pobierania danego adresu URL.
   > Serwery proxy próbujące przechwycić i odczytać cały ruch (a następnie ponownie podpisać wszystko własnym certyfikatem) nie są zgodne, ponieważ certyfikat serwera proxy nie jest zaufany.
   > Zazwyczaj przezroczyste serwery proxy działają dobrze w przypadku Azure Stack krawędzi Pro.

4. Obowiązkowe W okienku po lewej stronie wybierz pozycję **Ustawienia czasu**, a następnie skonfiguruj strefę czasową i serwery NTP w podstawowym i pomocniczym urządzeniu.  
    Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.
       
    Na stronie **Ustawienia czasu** wykonaj następujące czynności:
    
    1. Z listy rozwijanej **strefa czasowa** wybierz strefę czasową odpowiadającą lokalizacji geograficznej, w której jest wdrażane urządzenie.
        Domyślna strefa czasowa urządzenia to PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    2. W polu **podstawowy serwer NTP** wprowadź serwer podstawowy dla urządzenia lub Zaakceptuj wartość domyślną Time.Windows.com.  
        Upewnij się, że sieć zezwala na przekazywanie ruchu NTP z centrum danych do Internetu.

    3. Opcjonalnie w polu **pomocniczy serwer NTP** wprowadź serwer pomocniczy dla urządzenia.

    4. Aby sprawdzić poprawność skonfigurowanych ustawień czasu i zastosować je, wybierz pozycję **Zastosuj ustawienia**.

        ![Strona Ustawienia czasu dla lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. Obowiązkowe W okienku po lewej stronie wybierz pozycję **ustawienia magazynu** , aby skonfigurować odporność magazynu na urządzeniu. Ta funkcja jest obecnie w wersji zapoznawczej. Domyślnie magazyn na urządzeniu nie jest odporny na awarie, a w przypadku awarii dysku danych na urządzeniu następuje utrata danych. Po włączeniu opcji odporności magazyn na urządzeniu zostanie ponownie skonfigurowany i urządzenie może wytrzymać awarię jednego dysku z danymi bez utraty danych. Skonfigurowanie magazynu jako odpornego spowoduje zmniejszenie możliwości użycia urządzenia.

    > [!IMPORTANT] 
    > Odporność można skonfigurować tylko przed aktywowaniem urządzenia. 

    ![Strona ustawień magazynu lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. W lewym okienku wybierz pozycję **Ustawienia chmury**, a następnie aktywuj urządzenie przy użyciu usługi Azure Stack Edge w Azure Portal.
    
    1. W polu **klucz aktywacji** wprowadź klucz aktywacji uzyskany w ramach elementu [Pobierz klucz aktywacji](azure-stack-edge-deploy-prep.md#get-the-activation-key) dla Azure Stack brzeg Pro.
    2. Wybierz przycisk **Zastosuj**.
       
        ![Strona lokalnego interfejsu użytkownika sieci Web "Ustawienia chmury"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Pierwsze urządzenie zostało aktywowane. Urządzenie jest następnie skanowane pod kątem aktualizacji krytycznych i jeśli są dostępne, aktualizacje są automatycznie stosowane. Zobaczysz powiadomienie w tym efekcie.

        W oknie dialogowym znajduje się również klucz odzyskiwania, który należy skopiować i zapisać w bezpiecznej lokalizacji. Ten klucz służy do odzyskiwania danych w przypadku, gdy urządzenie nie może się uruchomić.

        ![Strona lokalnego interfejsu użytkownika sieci Web "Ustawienia chmury" została zaktualizowana](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Po pomyślnym zakończeniu aktualizacji może być konieczne odczekanie kilku minut. Strona zostanie zaktualizowana, aby wskazać, że urządzenie zostało pomyślnie aktywowane.

        ![Strona lokalnego interfejsu użytkownika sieci Web "Ustawienia chmury" została zaktualizowana 2](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Konfiguracja urządzenia została ukończona. Możesz teraz dodawać udziały na urządzeniu.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązywanie połączenia z urządzeniem fizycznym
> * Konfigurowanie i aktywowanie urządzenia fizycznego

Aby dowiedzieć się, jak przesyłać dane przy użyciu urządzenia z Azure Stack EDGE Pro, zobacz:

> [!div class="nextstepaction"]
> [Transferuj dane przy użyciu Azure Stack Edge](./azure-stack-edge-deploy-add-shares.md).
