---
title: Samouczek dotyczący konfigurowania ustawień sieciowych dla usługi Azure Stack Edge na urządzeniu z procesorem GPU w Azure Portal | Microsoft Docs
description: Samouczek umożliwiający wdrożenie procesora GPU w programie Azure Stack Edge powoduje skonfigurowanie ustawień sieci, sieci obliczeniowej i serwera proxy sieci Web dla urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: ac64233467166ca6567f1601c3b90f80fdba3dcf
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954651"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-with-gpu"></a>Samouczek: Konfigurowanie sieci na potrzeby Azure Stack brzeg Pro z procesorem GPU

W tym samouczku opisano sposób konfigurowania sieci dla urządzenia z programem Azure Stack Edge z użyciem procesora GPU przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces połączenia może potrwać około 20 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie sieci
> * Włącz sieć obliczeniową
> * Konfigurowanie serwera proxy sieci Web


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Azure Stack EDGE Pro przy użyciu procesora GPU upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-install.md).
* Nawiązano połączenie z lokalnym interfejsem użytkownika sieci Web na urządzeniu, zgodnie z opisem w temacie [Connect to Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Konfigurowanie sieci

Na stronie **wprowadzenie** zostaną wyświetlone różne ustawienia wymagane do skonfigurowania i zarejestrowania urządzenia fizycznego za pomocą usługi Azure Stack Edge. 

Wykonaj następujące kroki, aby skonfigurować sieć dla urządzenia.

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź na stronę **wprowadzenie** . 

2. Na kafelku **Sieć** wybierz pozycję **Konfiguruj**.  
    
    ![Kafelek "Ustawienia sieci" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Na urządzeniu fizycznym znajduje się sześć interfejsów sieciowych. PORT 1 i PORT 2 są interfejsami sieciowymi 1 GB/s. PORT 3, port 4, PORT 5 i PORT 6 to wszystkie 25 GB/s interfejsów sieciowych, które mogą również działać jako interfejsy sieciowe 10 GB/s. PORT 1 jest automatycznie konfigurowany jako port tylko do zarządzania, a PORT 2 do portu 6 to wszystkie porty danych. Na nowym urządzeniu znajduje się Strona **Ustawienia sieci** , jak pokazano poniżej.
    
    ![Strona ustawień sieciowych lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. Aby zmienić ustawienia sieci, wybierz port i w okienku po prawej stronie zmodyfikuj adres IP, podsieć, bramę, podstawową DNS i pomocniczy serwer DNS. 

    - W przypadku wybrania portu 1 można zobaczyć, że jest wstępnie skonfigurowany jako statyczny. 

        ![Lokalny interfejs użytkownika sieci Web "port 1 Ustawienia sieci"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - W przypadku wybrania opcji port 2, port 3, port 4 lub port 5 wszystkie te porty są domyślnie konfigurowane jako DHCP.

        ![Lokalny interfejs użytkownika sieci Web "Port 3 ustawienia sieci"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Podczas konfigurowania ustawień sieci należy pamiętać o następujących kwestiach:

   * Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieć, brama i DNS są przypisywane automatycznie.
   * Jeśli usługa DHCP nie jest włączona, w razie konieczności można przypisywać statyczne adresy IP.
   * Interfejs sieciowy można skonfigurować jako adres IPv4.
   * W przypadku interfejsów 25 GB/s można ustawić tryb RDMA (Remote Direct Access Memory) na iWarp lub RoCE (RDMA Over Converged Ethernet). W przypadku, gdy małe opóźnienia są głównym wymaganiem i skalowalność nie jest istotna, należy użyć RoCE. Gdy opóźnienie jest wymaganym kluczem, ale łatwość użycia i skalowalności są również wysokim priorytetem, iWARP jest najlepszym kandydatem.
   * Numer seryjny dowolnego portu odpowiada numerowi seryjnemu węzła.

    Po skonfigurowaniu sieci urządzenia aktualizacje są aktualizowane jak pokazano poniżej.

    ![Lokalny interfejs użytkownika sieci Web "Ustawienia sieci" — Strona 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * Zaleca się, aby nie przełączać lokalnego adresu IP interfejsu sieciowego ze statycznego na protokół DHCP, chyba że dostępny jest inny adres IP, aby połączyć się z urządzeniem. W przypadku korzystania z jednego interfejsu sieciowego i przełączenia na protokół DHCP nie będzie możliwości określenia adresu DHCP. Jeśli chcesz zmienić adres DHCP, zaczekaj, aż po aktywowaniu urządzenia za pomocą usługi, a następnie Zmień. Następnie można wyświetlić adresy IP wszystkich kart we **właściwościach urządzenia** w Azure Portal dla usługi.


    Po skonfigurowaniu i zastosowaniu ustawień sieci wybierz kolejno pozycje dalej: obliczenia, aby skonfigurować sieć obliczeniową.

## <a name="enable-compute-network"></a>Włącz sieć obliczeniową

Wykonaj następujące kroki, aby włączyć obliczenia i skonfigurować sieć obliczeniową. 

<!--1. Go to the **Get started** page in the local web UI of your device. On the **Network** tile, select **Compute network**.  

    ![Compute page in local UI 1](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)-->

1. Na stronie **Obliczanie** wybierz interfejs sieciowy, który ma zostać włączony dla obliczeń. 

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. W oknie dialogowym **Ustawienia sieci** wybierz pozycję **Włącz**. Po włączeniu obliczeń na urządzeniu zostanie utworzony przełącznik wirtualny w tym interfejsie sieciowym. Przełącznik wirtualny jest używany na potrzeby infrastruktury obliczeniowej na urządzeniu. 
    
1. Przypisz **adresy IP węzła Kubernetes**. Te statyczne adresy IP są przeznaczone dla maszyny wirtualnej obliczeniowej.  

    W przypadku urządzenia *n*-węzłowego w przypadku maszyny wirtualnej obliczeniowej z początkowym i końcowym adresem IP jest dostępny ciągły zakres co najmniej *n + 1* adresów IPv4. Dana Azure Stack Edge jest urządzeniem z 1 węzłem, podano co najmniej dwa ciągłe adresy IPv4.

    > [!IMPORTANT]
    > Kubernetes na Azure Stack Edge używa podsieci 172.27.0.0/16 dla usługi w podsieci pod i 172.28.0.0/16. Upewnij się, że nie są one używane w sieci. Jeśli te podsieci są już używane w sieci, można zmienić te podsieci, uruchamiając `Set-HcsKubeClusterNetworkInfo` polecenie cmdlet z interfejsu programu PowerShell urządzenia. Aby uzyskać więcej informacji, zobacz [Zmienianie Kubernetes pod i podsieci usługi](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Przypisywanie **adresów IP usług zewnętrznych Kubernetes**. Są to również adresy IP równoważenia obciążenia. Te ciągłe adresy IP są przeznaczone dla usług, które mają zostać ujawnione poza klastrem Kubernetes, i określają zakres statycznych adresów IP w zależności od liczby ujawnionych usług. 
    
    > [!IMPORTANT]
    > Zdecydowanie zalecamy określenie co najmniej jednego adresu IP dla usługi centrum Azure Stack EDGE Pro, aby uzyskać dostęp do modułów obliczeniowych. Opcjonalnie można określić dodatkowe adresy IP dla innych usług/modułów IoT Edge (1 na usługę/moduł), które muszą być dostępne spoza klastra. Adresy IP usługi można później zaktualizować. 
    
1. Wybierz przycisk **Zastosuj**.

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 3](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Zastosowanie konfiguracji trwa kilka minut i może być konieczne odświeżenie przeglądarki. Można sprawdzić, czy określony port jest włączony dla obliczeń. 
 
    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 4](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Wybierz pozycję **Dalej: serwer proxy sieci Web** , aby skonfigurować serwer proxy sieci Web.  

  
## <a name="configure-web-proxy"></a>Konfigurowanie serwera proxy sieci Web

Jest to opcjonalna konfiguracja.

> [!IMPORTANT]
> * W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu z systemem Azure Stack EDGE Pro zalecamy skonfigurowanie uwierzytelniania serwera proxy sieci Web jako **none**. Uwierzytelnianie NTLM nie jest obsługiwane.
> * Pliki proxy-autoconfig (PAC) nie są obsługiwane. Plik PAC definiuje, w jaki sposób przeglądarki sieci Web i inni agenci użytkowników mogą automatycznie wybierać odpowiedni serwer proxy (metoda dostępu) do pobierania danego adresu URL. 
> * Przezroczyste serwery proxy działają dobrze w przypadku Azure Stack EDGE Pro. W przypadku nieprzezroczystych serwerów proxy, które przechwytuje i odczytują cały ruch (za pośrednictwem własnych certyfikatów zainstalowanych na serwerze proxy), należy przekazać klucz publiczny certyfikatu serwera proxy jako łańcuch podpisywania na urządzeniu Azure Stack EDGE Pro. Następnie można skonfigurować ustawienia serwera proxy na urządzeniu Azure Stack Edge. Aby uzyskać więcej informacji, zobacz [przenoszenie własnych certyfikatów i przekazywanie ich za pomocą lokalnego interfejsu użytkownika](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).  

<!--1. Go to the **Get started** page in the local web UI of your device.
2. On the **Network** tile, configure your web proxy server settings. Although web proxy configuration is optional, if you use a web proxy, you can configure it on this page only.

   ![Local web UI "Web proxy settings" page](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)-->

1. Na stronie **Ustawienia serwera proxy sieci Web** wykonaj następujące czynności:

    1. W polu **adres URL serwera proxy sieci Web** wprowadź adres URL w tym formacie: `http://host-IP address or FQDN:Port number` . Adresy URL HTTPS nie są obsługiwane.

    2. W obszarze **uwierzytelnianie** wybierz opcję **Brak** lub **NTLM**. W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu z systemem Azure Stack EDGE Pro zalecamy ustawienie uwierzytelniania serwera proxy sieci Web na **Brak**. **Uwierzytelnianie NTLM** nie jest obsługiwane.

    3. Jeśli używasz uwierzytelniania, wprowadź nazwę użytkownika i hasło.

    4. Aby sprawdzić poprawność skonfigurowanych ustawień serwera proxy sieci Web i zastosować je, wybierz pozycję **Zastosuj**.
    
   ![Lokalny interfejs użytkownika sieci Web "ustawienia proxy sieci Web" — Strona 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Po zastosowaniu ustawień wybierz pozycję **Dalej: urządzenie**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Konfigurowanie sieci
> * Włącz sieć obliczeniową
> * Konfigurowanie serwera proxy sieci Web


Aby dowiedzieć się, jak skonfigurować urządzenie Azure Stack EDGE Pro, zobacz:

> [!div class="nextstepaction"]
> [Konfiguruj ustawienia urządzenia](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
