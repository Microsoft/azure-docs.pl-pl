---
title: Łączenie z usługą Azure Data Box Gateway, Konfigurowanie i aktywowanie w programie Azure Portal
description: Trzeci samouczek dotyczący wdrażania Data Box Gateway powoduje nawiązanie połączenia, skonfigurowanie i aktywowanie urządzenia wirtualnego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96583526"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Samouczek: Łączenie, konfigurowanie i aktywowanie usługi Azure Data Box Gateway

## <a name="introduction"></a>Wprowadzenie

W tym samouczku opisano sposób nawiązywania połączenia z usługą, konfigurowania i aktywowania urządzenia Data Box Gateway przy użyciu lokalnego interfejsu użytkownika sieci Web. 

Proces instalacji i aktywacji może potrwać około 10 minut. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązywanie połączenia z urządzeniem wirtualnym
> * Konfigurowanie i aktywowanie urządzenia wirtualnego

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem Data Box Gateway upewnij się, że:

* Zainicjowano obsługę administracyjną urządzenia wirtualnego i uzyskano do niego połączony adres URL zgodnie z opisem w temacie [Inicjowanie obsługi Data Box Gateway w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md) lub [Inicjowanie obsługi Data Box Gateway w oprogramowaniu VMware](data-box-gateway-deploy-provision-vmware.md).
* Masz klucz aktywacji z usługi Data Box Gateway utworzonej w celu zarządzania urządzeniami Data Box Gateway. Aby uzyskać więcej informacji, przejdź do obszaru [Przygotowywanie do wdrożenia Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Nawiązywanie połączenia z konfiguracją lokalnego interfejsu użytkownika sieci Web 

1. Otwórz okno przeglądarki i uzyskaj dostęp do lokalnego interfejsu użytkownika sieci Web urządzenia w:
   
   https: \/ /IP-address-of-Network-Interface
   
   Użyj adresu URL połączenia zanotowanego w poprzednim samouczku. Zostanie wyświetlony komunikat o błędzie lub ostrzeżenie informujące o problemie z certyfikatem zabezpieczeń witryny internetowej.

2. Wybierz pozycję **Kontynuuj na tej stronie sieci Web**. Te kroki mogą się różnić w zależności od używanej przeglądarki.
   
    ![Komunikat o błędzie dotyczący certyfikatu zabezpieczeń witryny sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Zaloguj się do internetowego interfejsu użytkownika urządzenia wirtualnego. Domyślne hasło to *Password1*. 
   
    ![Zaloguj się do lokalnego interfejsu użytkownika sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. W wierszu polecenia Zmień hasło urządzenia. Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać 3 z następujących elementów: wielkie litery, małe litery, cyfry i znaki specjalne.

    ![Zmienianie hasła urządzenia](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Jesteś teraz na **pulpicie nawigacyjnym** Twojego urządzenia.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurowanie i aktywowanie urządzenia wirtualnego
 
Pulpit nawigacyjny wyświetla różne ustawienia, które są wymagane do skonfigurowania i zarejestrowania urządzenia wirtualnego w usłudze Data Box Gateway. **Nazwa urządzenia**, **Ustawienia sieci**, **Ustawienia serwera proxy sieci Web** i **Ustawienia czasu** są opcjonalne. Jedyne wymagane ustawienia to **Ustawienia chmury**.
   
![Strona "pulpit nawigacyjny" lokalnego interfejsu użytkownika sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. W okienku po lewej stronie wybierz pozycję **Nazwa urządzenia**, a następnie wprowadź przyjazną nazwę urządzenia. Przyjazna nazwa musi zawierać od 1 do 15 znaków i zawierać litery, cyfry i łączniki. 

    ![Strona "nazwa urządzenia" lokalnego interfejsu użytkownika sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. Obowiązkowe W okienku po lewej stronie wybierz pozycję **Ustawienia sieci** , a następnie skonfiguruj ustawienia. Na urządzeniu wirtualnym zobaczysz co najmniej jeden interfejs sieciowy i więcej, w zależności od liczby skonfigurowanych w podstawowej maszynie wirtualnej. Strona **Ustawienia sieci** dla urządzenia wirtualnego z włączonym jednym interfejsem sieciowym jest pokazana poniżej.
    
    ![Strona ustawień sieciowych lokalnego interfejsu użytkownika sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Podczas konfigurowania ustawień sieciowych należy pamiętać o następujących kwestiach:

    - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieć, brama i DNS są przypisywane automatycznie.
    - Jeśli usługa DHCP nie jest włączona, w razie konieczności można przypisywać statyczne adresy IP.
    - Interfejs sieciowy można skonfigurować jako adres IPv4.

     >[!NOTE] 
     > Zaleca się, aby nie przełączać lokalnego adresu IP interfejsu sieciowego ze statycznego na DHCP, chyba że masz inny adres IP, aby połączyć się z urządzeniem. W przypadku korzystania z jednego interfejsu sieciowego i przełączenia na protokół DHCP nie będzie możliwości określenia adresu DHCP. Jeśli chcesz zmienić na adres DHCP, zaczekaj, aż urządzenie zostanie zarejestrowane w usłudze, a następnie dokonaj zmiany. Następnie można wyświetlić adresy IP wszystkich kart we **właściwościach urządzenia** w Azure Portal dla usługi.

3. Opcjonalnie skonfiguruj serwer proxy sieci Web. Mimo że konfiguracja serwera proxy sieci Web jest opcjonalna, jeśli używasz serwera proxy sieci Web, możesz go skonfigurować tylko na tej stronie.
   
   ![Strona lokalnego interfejsu użytkownika sieci Web — ustawienia serwera proxy sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na stronie **serwer proxy sieci Web** wykonaj następujące czynności:
   
   1. W polu **adres URL serwera proxy sieci Web** wprowadź adres URL w tym formacie: `http://&lt;host-IP address or FQDN&gt;:Port number` . Adresy URL HTTPS nie są obsługiwane.
   2. W obszarze **Uwierzytelnianie** wybierz opcję **Brak** lub **NTLM**.
   3. Jeśli używasz uwierzytelniania, wprowadź **nazwę użytkownika** i **hasło**.
   4. Aby sprawdzić poprawność skonfigurowanych ustawień serwera proxy sieci Web i zastosować je, wybierz pozycję **Zastosuj**.

   > [!NOTE]
   > Pliki proxy-autoconfig (PAC) nie są obsługiwane. Plik PAC definiuje, w jaki sposób przeglądarki sieci Web i inni agenci użytkowników mogą automatycznie wybierać odpowiedni serwer proxy (metoda dostępu) do pobierania danego adresu URL.
   > Serwery proxy próbujące przechwycić i odczytać cały ruch (a następnie ponownie podpisać wszystko własnym certyfikatem) nie są zgodne, ponieważ certyfikat serwera proxy nie jest zaufany.
   > Zazwyczaj przezroczyste serwery proxy działają dobrze z Azure Data Box Gateway.

4. Obowiązkowe W okienku po lewej stronie wybierz pozycję **Ustawienia czasu**, a następnie skonfiguruj strefę czasową i serwery NTP w podstawowym i pomocniczym urządzeniu. 

    Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.
    
    ![Strona Ustawienia czasu dla lokalnego interfejsu użytkownika sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na stronie **Ustawienia czasu** wykonaj następujące czynności:
    
    1. Z listy rozwijanej **strefa czasowa** wybierz strefę czasową, która odnosi się do lokalizacji geograficznej, w której jest wdrożone urządzenie.
        Domyślna strefa czasowa urządzenia to PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    2. Określ **podstawowy serwer NTP** dla urządzenia lub Zaakceptuj wartość domyślną `time.windows.com` .   
        Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.

    3. Opcjonalnie w polu **pomocniczy serwer NTP** wprowadź serwer pomocniczy dla urządzenia.

    4. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia czasu, wybierz pozycję **Zastosuj**.

6. W okienku po lewej stronie wybierz pozycję **Ustawienia chmury**, a następnie aktywuj urządzenie w usłudze Data Box Gateway w Azure Portal.
    
    1. W polu **klucz aktywacji** wprowadź **klucz aktywacji** uzyskany w polu [Pobierz klucz aktywacji](data-box-gateway-deploy-prep.md#get-the-activation-key) dla Data Box Gateway.

    2. Wybierz pozycję **Aktywuj**.
       
         ![Strona lokalnego interfejsu użytkownika sieci Web "Ustawienia chmury"](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Urządzenie jest aktywowane i aktualizacje krytyczne, jeśli są dostępne, są automatycznie stosowane. Zobaczysz powiadomienie w tym efekcie. Monitoruj postęp aktualizacji za pomocą Azure Portal.

        ![Lokalny interfejs użytkownika sieci Web "Ustawienia chmury" — Strona 2](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **W oknie dialogowym znajduje się również klucz odzyskiwania, który należy skopiować i zapisać w bezpiecznej lokalizacji. Ten klucz służy do odzyskiwania danych w przypadku, gdy urządzenie nie może się uruchomić.**


    4. Pomyślne ukończenie aktualizacji może potrwać kilka minut. Po zakończeniu aktualizacji zaloguj się na urządzeniu. Strona **Ustawienia chmury** jest aktualizowana, aby wskazać, że urządzenie zostało pomyślnie aktywowane.

        ![Strona lokalnego interfejsu użytkownika sieci Web "Ustawienia chmury" została zaktualizowana](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Konfiguracja urządzenia została ukończona. Możesz teraz dodawać udziały na urządzeniu.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązywanie połączenia z urządzeniem wirtualnym
> * Konfigurowanie i aktywowanie urządzenia wirtualnego

Aby dowiedzieć się, jak transferować dane za pomocą Data Box Gateway, zobacz:

> [!div class="nextstepaction"]
> [Transferuj dane przy użyciu Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
