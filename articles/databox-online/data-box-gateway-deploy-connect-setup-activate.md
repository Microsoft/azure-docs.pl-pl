---
title: Łączenie się, konfigurowanie i aktywowanie bramy usługi Azure Data Box w witrynie Azure portal
description: Trzeci samouczek do wdrażania bramy pola danych nakazuje nawiązywać połączenie, skonfigurowanie i aktywowanie urządzenia wirtualnego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: b3cf4fd958202c28586b7c15932dc88a21d7c60f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686875"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Samouczek: Łączenie, konfigurowanie, aktywowanie bramy usługi Azure Data Box

## <a name="introduction"></a>Wprowadzenie

W tym samouczku opisano sposób łączenia się, konfigurowania i aktywowania urządzenia bramy pola danych przy użyciu lokalnego interfejsu użytkownika sieci Web. 

Proces konfiguracji i aktywacji może potrwać około 10 minut. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Łączenie się z urządzeniem wirtualnym
> * Konfigurowanie i aktywowanie urządzenia wirtualnego

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem bramy pola danych upewnij się, że:

* Udostępniono urządzenie wirtualne i uzyskano do niego podłączony adres URL, jak opisano w [programie A Provision a Data Box Gateway w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md) lub [Aprowizuj bramę pola danych w VMware](data-box-gateway-deploy-provision-vmware.md).
* Masz klucz aktywacyjny z usługi Brama pola danych, który został utworzony w celu zarządzania urządzeniami Bramy pola danych. Aby uzyskać więcej informacji, przejdź do [witryny Przygotowanie do wdrożenia bramy usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Łączenie się z lokalną konfiguracją interfejsu użytkownika sieci Web 

1. Otwórz okno przeglądarki i uzyskaj dostęp do lokalnego interfejsu użytkownika urządzenia pod adresem:
   
   https:\//ip-address-of-network-interface
   
   Użyj adresu URL połączenia odnotowanego w poprzednim samouczku. Zostanie wyświetlony błąd lub ostrzeżenie wskazujące, że wystąpił problem z certyfikatem zabezpieczeń witryny sieci Web.

2. Wybierz **pozycję Kontynuuj do tej strony sieci Web**. Te kroki mogą się różnić w zależności od używanej przeglądarki.
   
    ![Komunikat o błędzie certyfikatu zabezpieczeń witryny sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Zaloguj się do internetowego interfejsu użytkownika urządzenia wirtualnego. Domyślnym hasłem jest *Password1*. 
   
    ![Logowanie się do lokalnego interfejsu użytkownika sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. W wierszu polecenia zmień hasło urządzenia. Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać 3 z następujących znaków: wielkie, małe, numeryczne i znaki specjalne.

    ![Zmienianie hasła urządzenia](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Jesteś teraz na **pulpicie nawigacyjnym** urządzenia.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurowanie i aktywowanie urządzenia wirtualnego
 
Pulpit nawigacyjny wyświetla różne ustawienia, które są wymagane do skonfigurowania i zarejestrowania urządzenia wirtualnego w usłudze Brama pól danych. **Nazwa urządzenia,** **ustawienia sieciowe,** ustawienia serwera proxy **sieci Web**i **ustawienia czasu** są opcjonalne. Jedynymi wymaganymi ustawieniami są **ustawienia chmury**.
   
![Strona "Pulpit nawigacyjny" interfejsu użytkownika sieci web](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. W lewym okienku wybierz **pozycję Nazwa urządzenia**, a następnie wprowadź przyjazną nazwę urządzenia. Przyjazna nazwa musi zawierać od 1 do 15 znaków i zawierać litery, cyfry i łączniki.

    ![Strona lokalny interfejs użytkownika sieci Web "Nazwa urządzenia"](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Opcjonalnie) W lewym okienku wybierz pozycję **Ustawienia sieciowe,** a następnie skonfiguruj ustawienia. Na urządzeniu wirtualnym zobaczysz co najmniej jeden interfejs sieciowy i więcej w zależności od liczby skonfigurowanych w podstawowej maszynie wirtualnej. Strona **Ustawienia sieci** dla urządzenia wirtualnego z włączonym jednym interfejsem sieciowym jest jak pokazano poniżej.
    
    ![Strona Lokalny interfejs użytkownika sieci web "Ustawienia sieci"](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Podczas konfigurowania ustawień sieciowych należy pamiętać:

    - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieć, brama i dns są przypisywane automatycznie.
    - Jeśli usługa DHCP nie jest włączona, w razie potrzeby można przypisać statyczne serwery IP.
    - Interfejs sieciowy można skonfigurować jako IPv4.

     >[!NOTE] 
     > Zaleca się, aby nie przełączać lokalnego adresu IP interfejsu sieciowego ze statycznego na DHCP, chyba że masz inny adres IP do łączenia się z urządzeniem. W przypadku korzystania z jednego interfejsu sieciowego i przełączenia się na protokół DHCP nie można określić adresu DHCP. Jeśli chcesz zmienić adres DHCP, poczekaj, aż urządzenie zostanie zarejestrowane w usłudze, a następnie zmień. Następnie można wyświetlić ustawienia IP wszystkich kart we **właściwościach urządzenia** w witrynie Azure portal dla twojej usługi.

3. Opcjonalnie skonfiguruj serwer proxy sieci Web. Chociaż konfiguracja serwera proxy sieci Web jest opcjonalna, jeśli używasz serwera proxy sieci Web, można go skonfigurować tylko na tej stronie.
   
   ![Strona lokalny interfejs użytkownika sieci Web "Ustawienia serwera proxy sieci Web"](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na stronie **serwera proxy sieci Web** wykonaj następujące czynności:
   
   1. W polu **Adres URL serwera proxy sieci** `http://&lt;host-IP address or FQDN&gt;:Port number`Web wprowadź adres URL w tym formacie: . Adresy URL HTTPS nie są obsługiwane.
   2. W obszarze **Uwierzytelnianie**wybierz **pozycję Brak** lub **NTLM**.
   3. Jeśli używasz uwierzytelniania, wprowadź **nazwę użytkownika** i **hasło**.
   4. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia serwera proxy sieci Web, wybierz pozycję **Zastosuj**.

   > [!NOTE]
   > Pliki konfiguracji proxy-auto (PAC) nie są obsługiwane. Plik PAC definiuje, w jaki sposób przeglądarki internetowe i inni agenci użytkowników mogą automatycznie wybrać odpowiedni serwer proxy (metodę dostępu) do pobierania danego adresu URL.
   > Serwery proxy, które próbują przechwycić i odczytać cały ruch (a następnie ponownie podpisać wszystko z własnej certyfikacji) nie są zgodne, ponieważ certyfikat serwera proxy nie jest zaufany.
   > Zazwyczaj przezroczyste serwery proxy działają dobrze z bramą usługi Azure Data Box.

4. (Opcjonalnie) W lewym okienku wybierz **pozycję Ustawienia czasu**, a następnie skonfiguruj strefę czasową oraz podstawowe i pomocnicze serwery NTP dla urządzenia. 

    Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.
    
    ![Strona lokalny interfejs użytkownika sieci Web "Ustawienia czasu"](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na stronie **Ustawienia czasu** wykonaj następujące czynności:
    
    1. Na liście rozwijanej **Strefa czasowa** wybierz strefę czasową odpowiadającą lokalizacji geograficznej, w której urządzenie jest wdrażane.
        Domyślną strefą czasową urządzenia jest PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    2. Określ **podstawowy serwer NTP** dla urządzenia lub `time.windows.com`zaakceptuj domyślną wartość programu .   
        Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.

    3. Opcjonalnie w polu **Serwer pomocniczy NTP** wprowadź serwer pomocniczy dla urządzenia.

    4. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia czasu, wybierz opcję **Zastosuj**.

6. W lewym okienku wybierz **pozycję Ustawienia chmury**, a następnie aktywuj urządzenie za pomocą usługi Brama pola danych w witrynie Azure portal.
    
    1. W polu **Klucz aktywacji** wprowadź **klucz aktywacyjny,** który otrzymałeś w [pobierz klucz aktywacyjny](data-box-gateway-deploy-prep.md#get-the-activation-key) bramy pola danych.

    2. Wybierz **pozycję Aktywuj**.
       
         ![Strona lokalny interfejs użytkownika sieci Web "Ustawienia chmury"](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Urządzenie jest aktywowane, a aktualizacje krytyczne, jeśli są dostępne, są stosowane automatycznie. Zobaczysz powiadomienie w tej sprawie. Monitorowanie postępu aktualizacji za pośrednictwem witryny Azure portal.

        ![Strona lokalny interfejs użytkownika sieci Web "Ustawienia chmury"](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Okno dialogowe zawiera również klucz odzyskiwania, który należy skopiować i zapisać w bezpiecznej lokalizacji. Ten klucz służy do odzyskiwania danych w przypadku, gdy urządzenie nie może się uruchomić.**


    4. Może być konieczne odczekanie kilku minut na pomyślne zakończenie aktualizacji. Po zakończeniu aktualizacji zaloguj się do urządzenia. Strona **Ustawienia chmury** zostanie zaktualizowana, aby wskazać, że urządzenie zostało pomyślnie aktywowane.

        ![Zaktualizowano stronę lokalnego interfejsu użytkownika sieci Web "Ustawienia chmury"](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Konfiguracja urządzenia została zakończona. Teraz możesz dodać udziały na swoim urządzeniu.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Łączenie się z urządzeniem wirtualnym
> * Konfigurowanie i aktywowanie urządzenia wirtualnego

Aby dowiedzieć się, jak przesyłać dane za pomocą bramy pól danych, zobacz:

> [!div class="nextstepaction"]
> [Przesyłanie danych za pomocą bramy pola danych](./data-box-gateway-deploy-add-shares.md).
