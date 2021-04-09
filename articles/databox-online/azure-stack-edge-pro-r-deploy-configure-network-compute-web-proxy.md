---
title: Samouczek dotyczący konfigurowania ustawień sieciowych dla programu Azure Stack EDGE Pro urządzenie R in Azure Portal | Microsoft Docs
description: Samouczek dotyczący wdrażania Azure Stack EDGE Pro R instruuje użytkownika o skonfigurowaniu ustawień sieci, sieci obliczeniowej i serwera proxy sieci Web na urządzeniu fizycznym.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 11c1d76b5784587f234455f81595778897569eb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594353"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Samouczek: Konfigurowanie sieci dla Azure Stack EDGE — Pro R

W tym samouczku opisano sposób konfigurowania sieci dla urządzenia z Azure Stack brzeg Pro R przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces połączenia może potrwać około 20 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie sieci
> * Włącz sieć obliczeniową
> * Konfigurowanie serwera proxy sieci Web


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia z systemem Azure Stack EDGE Pro R upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro R](azure-stack-edge-gpu-deploy-install.md).
* Nawiązano połączenie z lokalnym interfejsem użytkownika sieci Web urządzenia zgodnie z opisem w temacie [Connect to a Azure Stack EDGE Pro R](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Konfigurowanie sieci

Na stronie **wprowadzenie** zostaną wyświetlone różne ustawienia wymagane do skonfigurowania i zarejestrowania urządzenia fizycznego za pomocą usługi Azure Stack Edge. 

Wykonaj następujące kroki, aby skonfigurować sieć dla urządzenia.

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź na stronę **wprowadzenie** . 

2. Na kafelku **Sieć** wybierz pozycję **Konfiguruj** , aby przejść do strony **Sieć** . 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    Na urządzeniu fizycznym dostępne są cztery interfejsy sieciowe. PORT 1 i PORT 2 są interfejsami sieciowymi 1 GB/s. PORT 3 i 4-10 GB/s są interfejsami sieciowymi. PORT 1 jest automatycznie konfigurowany jako port tylko do zarządzania, a PORT 2 do portu 4 to wszystkie porty danych. Strona **sieci** jest jak pokazano poniżej.
    
    ![Strona ustawień sieciowych lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. Aby zmienić ustawienia sieci, wybierz port i w okienku po prawej stronie zmodyfikuj adres IP, podsieć, bramę, podstawową DNS i pomocniczy serwer DNS. 

    - W przypadku wybrania portu 1 można zobaczyć, że jest wstępnie skonfigurowany jako statyczny. 

        ![Lokalny interfejs użytkownika sieci Web "port 1 Ustawienia sieci"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - W przypadku wybrania portu 2, portu 3 lub 4 wszystkie te porty są domyślnie konfigurowane jako DHCP.

        ![Lokalny interfejs użytkownika sieci Web "Port 3 ustawienia sieci"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    Podczas konfigurowania ustawień sieci należy pamiętać o następujących kwestiach:

   * Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieć, brama i DNS są przypisywane automatycznie.
   * Jeśli usługa DHCP nie jest włączona, w razie konieczności można przypisywać statyczne adresy IP.
   * Interfejs sieciowy można skonfigurować jako adres IPv4.
   * Tworzenie zespołu kart interfejsu sieciowego (NIC) lub agregacja łączy nie jest obsługiwane w przypadku Azure Stack Edge.
   * Numer seryjny dowolnego portu odpowiada numerowi seryjnemu węzła.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    Po skonfigurowaniu sieci urządzenia aktualizacje są aktualizowane jak pokazano poniżej.

    ![Lokalny interfejs użytkownika sieci Web "Ustawienia sieci" — Strona 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * Zaleca się, aby nie przełączać lokalnego adresu IP interfejsu sieciowego ze statycznego na protokół DHCP, chyba że dostępny jest inny adres IP, aby połączyć się z urządzeniem. W przypadku korzystania z jednego interfejsu sieciowego i przełączenia na protokół DHCP nie będzie możliwości określenia adresu DHCP. Jeśli chcesz zmienić adres DHCP, zaczekaj, aż po aktywowaniu urządzenia za pomocą usługi, a następnie Zmień. Następnie można wyświetlić adresy IP wszystkich kart we **właściwościach urządzenia** w Azure Portal dla usługi.


    Po skonfigurowaniu i zastosowaniu ustawień sieci wybierz kolejno pozycje **Dalej: obliczenia** , aby skonfigurować sieć obliczeniową.

## <a name="enable-compute-network"></a>Włącz sieć obliczeniową

Wykonaj następujące kroki, aby włączyć obliczenia i skonfigurować sieć obliczeniową. 

1. Na stronie **Obliczanie** wybierz interfejs sieciowy, który ma zostać włączony dla obliczeń. 

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. W oknie dialogowym **Ustawienia sieci** wybierz pozycję **Włącz**. Po włączeniu obliczeń na urządzeniu zostanie utworzony przełącznik wirtualny w tym interfejsie sieciowym. Przełącznik wirtualny jest używany na potrzeby infrastruktury obliczeniowej na urządzeniu. 
    
1. Przypisz **adresy IP węzła Kubernetes**. Te statyczne adresy IP są przeznaczone dla maszyny wirtualnej obliczeniowej.  

    W przypadku urządzenia *n*-węzłowego w przypadku maszyny wirtualnej obliczeniowej z początkowym i końcowym adresem IP jest dostępny ciągły zakres co najmniej *n + 1* adresów IPv4. Dana Azure Stack Edge jest urządzeniem z 1 węzłem, podano co najmniej dwa ciągłe adresy IPv4. Te adresy IP muszą znajdować się w tej samej sieci, w której włączono obliczenia, a przełącznik wirtualny został utworzony.

    > [!IMPORTANT]
    > Kubernetes na Azure Stack Edge używa podsieci 172.27.0.0/16 dla usługi w podsieci pod i 172.28.0.0/16. Upewnij się, że nie są one używane w sieci. Jeśli te podsieci są już używane w sieci, można zmienić te podsieci, uruchamiając `Set-HcsKubeClusterNetworkInfo` polecenie cmdlet z interfejsu programu PowerShell urządzenia. Aby uzyskać więcej informacji, zobacz [Zmienianie Kubernetes pod i podsieci usługi](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Przypisywanie **adresów IP usług zewnętrznych Kubernetes**. Są to również adresy IP równoważenia obciążenia. Te ciągłe adresy IP są przeznaczone dla usług, które mają zostać ujawnione poza klastrem Kubernetes, i określają zakres statycznych adresów IP w zależności od liczby ujawnionych usług. 
    
    > [!IMPORTANT]
    > Zdecydowanie zalecamy, aby określić co najmniej 1 adres IP dla usługi Hub programu Azure Stack EDGE Pro, aby uzyskać dostęp do modułów obliczeniowych. Opcjonalnie można określić dodatkowe adresy IP dla innych usług/modułów IoT Edge (1 na usługę/moduł), które muszą być dostępne spoza klastra. Adresy IP usługi można później zaktualizować. 
    
1. Wybierz przycisk **Zastosuj**.

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Zastosowanie konfiguracji trwa kilka minut, a może być konieczne odświeżenie przeglądarki. Można sprawdzić, czy określony port jest włączony dla obliczeń. 
 
    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Wybierz pozycję **Dalej: serwer proxy sieci Web** , aby skonfigurować serwer proxy sieci Web.  

  
## <a name="configure-web-proxy"></a>Konfigurowanie serwera proxy sieci Web

Jest to opcjonalna konfiguracja.

> [!IMPORTANT]
> * W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu z systemem Azure Stack EDGE Pro R zalecamy skonfigurowanie uwierzytelniania serwera proxy sieci Web jako **none**. Uwierzytelnianie NTLM nie jest obsługiwane.
>* Pliki proxy-autoconfig (PAC) nie są obsługiwane. Plik PAC definiuje, w jaki sposób przeglądarki sieci Web i inni agenci użytkowników mogą automatycznie wybierać odpowiedni serwer proxy (metoda dostępu) do pobierania danego adresu URL. Serwery proxy próbujące przechwycić i odczytać cały ruch (a następnie ponownie podpisać wszystko z własnymi certyfikatami) nie są zgodne, ponieważ certyfikat serwera proxy nie jest zaufany. Zazwyczaj przezroczyste serwery proxy działają dobrze w przypadku Azure Stack EDGE Pro R. nieprzezroczyste proxy sieci Web nie są obsługiwane.


1. Na stronie **Ustawienia serwera proxy sieci Web** wykonaj następujące czynności:

    1. W polu **adres URL serwera proxy sieci Web** wprowadź adres URL w tym formacie: `http://host-IP address or FQDN:Port number` . Adresy URL HTTPS nie są obsługiwane.

    2. W obszarze **Uwierzytelnianie** wybierz opcję **Brak** lub **NTLM**. W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu z systemem Azure Stack EDGE Pro R zalecamy ustawienie uwierzytelniania serwera proxy sieci Web na **Brak**. **Uwierzytelnianie NTLM** nie jest obsługiwane.

    3. Jeśli używasz uwierzytelniania, wprowadź nazwę użytkownika i hasło.

    4. Aby sprawdzić poprawność skonfigurowanych ustawień serwera proxy sieci Web i zastosować je, wybierz pozycję **Zastosuj**.
    
   ![Lokalny interfejs użytkownika sieci Web "ustawienia proxy sieci Web" — Strona 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Po zastosowaniu ustawień wybierz pozycję **Dalej: urządzenie**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Konfigurowanie sieci
> * Włącz sieć obliczeniową
> * Konfigurowanie serwera proxy sieci Web


Aby dowiedzieć się, jak skonfigurować swoje urządzenie Azure Stack EDGE Pro R, zobacz:

> [!div class="nextstepaction"]
> [Konfiguruj ustawienia urządzenia](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
