---
title: Samouczek dotyczący konfigurowania ustawień sieciowych dla Azure Stack Edge mini R urządzenia w Azure Portal
description: Samouczek dotyczący wdrażania Azure Stack Edge mini R powoduje skonfigurowanie ustawień sieci, sieci obliczeniowej i serwera proxy sieci Web na urządzeniu fizycznym.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: a202ee1d708feebc7643fa6f20aafa9d8603fc38
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546775"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Samouczek: Konfigurowanie sieci dla Azure Stack krawędzi mini R

W tym samouczku opisano sposób konfigurowania sieci dla urządzenia z Azure Stack Edge mini R z dołączanym procesorem GPU przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces połączenia może potrwać około 20 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie sieci
> * Włącz sieć obliczeniową
> * Konfigurowanie serwera proxy sieci Web


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia z systemem Azure Stack Edge mini R upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge mini R](azure-stack-edge-gpu-deploy-install.md).
* Nawiązano połączenie z lokalnym interfejsem użytkownika sieci Web urządzenia zgodnie z opisem w temacie [Connect to a Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-connect.md)


## <a name="configure-network"></a>Konfigurowanie sieci

Na stronie **wprowadzenie** zostaną wyświetlone różne ustawienia wymagane do skonfigurowania i zarejestrowania urządzenia fizycznego za pomocą usługi Azure Stack Edge. 

Wykonaj następujące kroki, aby skonfigurować sieć dla urządzenia.

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź na stronę **wprowadzenie** . 

2. Jeśli wymagana jest aktualizacja zero dni, można to zrobić, konfigurując port danych przy użyciu połączenia przewodowego. Aby uzyskać więcej informacji na temat sposobu konfigurowania połączenia przewodowego dla tego urządzenia, zobacz [kabel urządzenia](azure-stack-edge-mini-r-deploy-install.md#cable-the-device). Po zakończeniu aktualizacji można usunąć połączenie przewodowe.

3. Utwórz certyfikaty dla Wi-Fi i łańcucha podpisywania. Zarówno łańcuch podpisywania, jak i certyfikaty Wi-Fi muszą być w formacie DER z rozszerzeniem *. cer* . Aby uzyskać instrukcje, zobacz [Tworzenie certyfikatów](azure-stack-edge-gpu-manage-certificates.md).

4. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **wprowadzenie**. Na kafelku **zabezpieczenia** wybierz pozycję **Certyfikaty** , a następnie wybierz pozycję **Konfiguruj**. 

    [![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Wybierz pozycję **+ Dodaj certyfikat**. 
    
        [![Lokalny interfejs użytkownika sieci Web "certyfikaty" Strona 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Przekaż łańcuch podpisywania i wybierz pozycję **Zastosuj**.

        ![Lokalny interfejs użytkownika sieci Web "certyfikaty" Strona 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Powtórz procedurę z certyfikatem Wi-Fi. 

        ![Lokalny interfejs użytkownika sieci Web "certyfikaty" — Strona 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. Nowe certyfikaty powinny być wyświetlane na stronie **Certyfikaty** . 
    
        [![Lokalny interfejs użytkownika sieci Web "certyfikaty" — Strona 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. Wróć do pozycji **Rozpoczynanie pracy**.

3. Na kafelku **Sieć** wybierz pozycję **Konfiguruj**.  
    
    Na urządzeniu fizycznym istnieje pięć interfejsów sieciowych. PORT 1 i PORT 2 są interfejsami sieciowymi 1 GB/s. PORT 3 i 4 to wszystkie interfejsy sieciowe 10 GB/s. Piąty port jest portem Wi-Fi. 

    [![Lokalny interfejs użytkownika sieci Web "Ustawienia sieci" — Strona 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Wybierz port Wi-Fi i skonfiguruj ustawienia portu. 
    
    > [!IMPORTANT]
    > Zdecydowanie zalecamy skonfigurowanie statycznego adresu IP dla portu Wi-Fi.  

    ![Lokalny interfejs użytkownika sieci Web "Ustawienia sieci" — Strona 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Po zastosowaniu ustawień portu Wi-Fi należy zaktualizować stronę **sieci** .

    ![Lokalny interfejs użytkownika sieci Web "Ustawienia sieci" — Strona 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. Wybierz pozycję **Dodaj profil Wi-Fi** i przekaż profil Wi-Fi. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi Network Settings" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Profil sieci bezprzewodowej zawiera informacje o identyfikatorze SSID (sieci), klucz hasła i zabezpieczeniach, które mają być w stanie połączyć się z siecią bezprzewodową. Możesz uzyskać profil Wi-Fi dla swojego środowiska z poziomu administratora sieci.

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi Network Settings" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Po dodaniu profilu lista Wi-Fi profilów zostanie zaktualizowana w celu odzwierciedlenia nowego profilu. Profil powinien zawierać **stan połączenia** jako **odłączony**. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi Network Settings" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. Po pomyślnym załadowaniu profilu sieci bezprzewodowej Nawiąż połączenie z tym profilem. Wybierz pozycję **Połącz z profilem Wi-Fi**. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi ustawienia sieci" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Wybierz profil Wi-Fi, który został dodany w poprzednim kroku, a następnie wybierz pozycję **Zastosuj**. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi ustawienia sieci" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **Stan połączenia** należy zaktualizować na wartość **połączono**. Siła sygnału jest aktualizowana w celu wskazania jakości sygnału. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi ustawienia sieci" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Aby przenieść duże ilości danych, zalecamy użycie połączenia przewodowego zamiast sieci bezprzewodowej. 

6. Odłącz PORT 1 na urządzeniu od komputera przenośnego. 

7. Podczas konfigurowania ustawień sieci należy pamiętać o następujących kwestiach:

   - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieć, brama i DNS są przypisywane automatycznie.
   - Jeśli usługa DHCP nie jest włączona, w razie konieczności można przypisywać statyczne adresy IP.
   - Interfejs sieciowy można skonfigurować jako adres IPv4.
   - Tworzenie zespołu kart interfejsu sieciowego (NIC) lub agregacja łączy nie jest obsługiwane w przypadku Azure Stack Edge.
   - Numer seryjny dowolnego portu odpowiada numerowi seryjnemu węzła. W przypadku urządzenia z serii K zostanie wyświetlony tylko jeden numer seryjny.

     >[!NOTE] 
     > Zaleca się, aby nie przełączać lokalnego adresu IP interfejsu sieciowego ze statycznego na protokół DHCP, chyba że dostępny jest inny adres IP, aby połączyć się z urządzeniem. W przypadku korzystania z jednego interfejsu sieciowego i przełączenia na protokół DHCP nie będzie możliwości określenia adresu DHCP. Jeśli chcesz zmienić na adres DHCP, zaczekaj, aż urządzenie zostanie zarejestrowane w usłudze, a następnie dokonaj zmiany. Następnie można wyświetlić adresy IP wszystkich kart we **właściwościach urządzenia** w Azure Portal dla usługi.

Po skonfigurowaniu i zastosowaniu ustawień sieci wybierz kolejno pozycje **Dalej: obliczenia** , aby skonfigurować sieć obliczeniową.

## <a name="enable-compute-network"></a>Włącz sieć obliczeniową

Wykonaj następujące kroki, aby włączyć obliczenia i skonfigurować sieć obliczeniową. 


1. Na stronie **Obliczanie** wybierz interfejs sieciowy, który ma zostać włączony dla obliczeń. 

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. W oknie dialogowym **Ustawienia sieci** wybierz pozycję **Włącz**. Po włączeniu obliczeń na urządzeniu zostanie utworzony przełącznik wirtualny w tym interfejsie sieciowym. Przełącznik wirtualny jest używany na potrzeby infrastruktury obliczeniowej na urządzeniu. 
    
1. Przypisz **adresy IP węzła Kubernetes**. Te statyczne adresy IP są przeznaczone dla maszyny wirtualnej obliczeniowej.  

    W przypadku urządzenia *n*-węzłowego w przypadku maszyny wirtualnej obliczeniowej z początkowym i końcowym adresem IP jest dostępny ciągły zakres co najmniej *n + 1* adresów IPv4. Dana Azure Stack Edge jest urządzeniem z 1 węzłem, podano co najmniej dwa ciągłe adresy IPv4.

    > [!IMPORTANT]
    > Kubernetes na Azure Stack Edge używa podsieci 172.27.0.0/16 dla usługi w podsieci pod i 172.28.0.0/16. Upewnij się, że nie są one używane w sieci. Jeśli te podsieci są już używane w sieci, można zmienić te podsieci, uruchamiając `Set-HcsKubeClusterNetworkInfo` polecenie cmdlet z interfejsu programu PowerShell urządzenia. Aby uzyskać więcej informacji, zobacz [Zmienianie Kubernetes pod i podsieci usługi](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Przypisywanie **adresów IP usług zewnętrznych Kubernetes**. Są to również adresy IP równoważenia obciążenia. Te przyległe adresy IP są przeznaczone dla usług, które mają być udostępniane poza klastrem Kubernetes, i określają zakres statycznych adresów IP w zależności od liczby ujawnionych usług. 
    
    > [!IMPORTANT]
    > Zdecydowanie zalecamy, aby określić co najmniej 1 adres IP dla usługi Azure Stack krawędź mini R Hub, aby uzyskać dostęp do modułów obliczeniowych. Opcjonalnie można określić dodatkowe adresy IP dla innych usług/modułów IoT Edge (1 na usługę/moduł), które muszą być dostępne spoza klastra. Adresy IP usługi można później zaktualizować. 
    
1. Wybierz przycisk **Zastosuj**.

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Zastosowanie konfiguracji trwa kilka minut i może być konieczne odświeżenie przeglądarki. Można sprawdzić, czy określony port jest włączony dla obliczeń. 
 
    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Wybierz pozycję **Dalej: serwer proxy sieci Web** , aby skonfigurować serwer proxy sieci Web.  

  
## <a name="configure-web-proxy"></a>Konfigurowanie serwera proxy sieci Web

Jest to opcjonalna konfiguracja.

> [!IMPORTANT]
> * W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu z systemem Azure Stack Edge mini R zalecamy skonfigurowanie uwierzytelniania serwera proxy sieci Web jako **none**. Uwierzytelnianie NTLM nie jest obsługiwane.
>* Pliki proxy-autoconfig (PAC) nie są obsługiwane. Plik PAC definiuje, w jaki sposób przeglądarki sieci Web i inni agenci użytkowników mogą automatycznie wybierać odpowiedni serwer proxy (metoda dostępu) do pobierania danego adresu URL. Serwery proxy próbujące przechwycić i odczytać cały ruch (a następnie ponownie podpisać wszystko z własnymi certyfikatami) nie są zgodne, ponieważ certyfikat serwera proxy nie jest zaufany. Zazwyczaj przezroczyste serwery proxy działają dobrze w przypadku Azure Stack Edge mini R. nieprzezroczyste proxy sieci Web nie są obsługiwane.


1. Na stronie **Ustawienia serwera proxy sieci Web** wykonaj następujące czynności:

    1. W polu **adres URL serwera proxy sieci Web** wprowadź adres URL w tym formacie: `http://host-IP address or FQDN:Port number` . Adresy URL HTTPS nie są obsługiwane.

    2. W obszarze **uwierzytelnianie** wybierz opcję **Brak** lub **NTLM**. W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu z systemem Azure Stack Edge mini R zalecamy ustawienie uwierzytelniania serwera proxy sieci Web na **Brak**. **Uwierzytelnianie NTLM** nie jest obsługiwane.

    3. Jeśli używasz uwierzytelniania, wprowadź nazwę użytkownika i hasło.

    4. Aby sprawdzić poprawność skonfigurowanych ustawień serwera proxy sieci Web i zastosować je, wybierz pozycję **Zastosuj**.
    
   ![Strona lokalnego interfejsu użytkownika sieci Web — ustawienia serwera proxy sieci Web](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. Po zastosowaniu ustawień wybierz pozycję **Dalej: urządzenie**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Konfigurowanie sieci
> * Włącz sieć obliczeniową
> * Konfigurowanie serwera proxy sieci Web


Aby dowiedzieć się, jak skonfigurować urządzenie z usługą Azure Stack Edge mini R, zobacz:

> [!div class="nextstepaction"]
> [Konfiguruj ustawienia urządzenia](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
