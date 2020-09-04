---
title: Samouczek dotyczący konfigurowania ustawień sieciowych dla Azure Stack urządzenia brzegowego z procesorem GPU w Azure Portal | Microsoft Docs
description: Samouczek wdrażania Azure Stack Edge procesora GPU powoduje skonfigurowanie ustawień sieci, sieci obliczeniowej i serwera proxy sieci Web dla urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: cc111f0df889efd1d3720e2ec0e4aaa452efd801
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461871"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-with-gpu"></a>Samouczek: Konfigurowanie sieci dla Azure Stack krawędzi z procesorem GPU

W tym samouczku opisano sposób konfigurowania sieci dla urządzenia brzegowego Azure Stack za pomocą dołączania procesora GPU przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces połączenia może potrwać około 20 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie sieci
> * Włącz sieć obliczeniową
> * Konfigurowanie serwera proxy sieci Web


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Azure Stack Edge z procesorem GPU upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Nawiązano połączenie z lokalnym interfejsem użytkownika sieci Web urządzenia zgodnie z opisem w temacie [Connect to a Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Konfigurowanie sieci

Na stronie **wprowadzenie** zostaną wyświetlone różne ustawienia wymagane do skonfigurowania i zarejestrowania urządzenia fizycznego za pomocą usługi Azure Stack Edge. 

Wykonaj następujące kroki, aby skonfigurować sieć dla urządzenia.

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź na stronę **wprowadzenie** . 

2. Na kafelku **Sieć** wybierz pozycję **Konfiguruj**.  
    
    ![Kafelek "Ustawienia sieci" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Na urządzeniu fizycznym istnieje sześć interfejsów sieciowych. PORT 1 i 2 są interfejsami sieciowymi z 1 GB/s. PORT 3, port 4, PORT 5 i PORT 6 to wszystkie 25 GB/s interfejsów sieciowych, które mogą również działać jako interfejsy sieciowe 10 GB/s. PORT 1 jest automatycznie konfigurowany jako port tylko do zarządzania, a PORT 2 do portu 6 to wszystkie porty danych. Na nowym urządzeniu znajduje się Strona **Ustawienia sieci** , jak pokazano poniżej.
    
    ![Strona ustawień sieciowych lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. Aby zmienić ustawienia sieci, wybierz port i w okienku po prawej stronie zmodyfikuj adres IP, podsieć, bramę, podstawową DNS i pomocniczy serwer DNS. 

    - W przypadku wybrania portu 1 można zobaczyć, że jest wstępnie skonfigurowany jako statyczny. 

        ![Lokalny interfejs użytkownika sieci Web "port 1 Ustawienia sieci"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - W przypadku wybrania opcji port 2, port 3, port 4 lub port 5 wszystkie te porty są domyślnie konfigurowane jako DHCP.

        ![Lokalny interfejs użytkownika sieci Web "Port 3 ustawienia sieci"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Podczas konfigurowania ustawień sieci należy pamiętać o następujących kwestiach:

   * Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieć, Brama i DNS są przypisywane automatycznie.
   * Jeśli usługa DHCP nie jest włączona, w razie konieczności można przypisywać statyczne adresy IP.
   * Interfejs sieciowy można skonfigurować jako adres IPv4.
   * W przypadku interfejsów 25 GB/s można ustawić tryb RDMA (Remote Direct Access Memory) na iWarp lub RoCE (RDMA Over Converged Ethernet). W przypadku, gdy małe opóźnienia są głównym wymaganiem i skalowalność nie jest istotna, należy użyć RoCE. Gdy opóźnienie jest wymaganym kluczem, ale łatwość użycia i skalowalności są również wysokim priorytetem, iWARP jest najlepszym kandydatem.
   * Numer seryjny dowolnego portu odpowiada numerowi seryjnemu węzła.

    Po skonfigurowaniu sieci urządzenia aktualizacje są aktualizowane jak pokazano poniżej.

    ![Strona ustawień sieciowych lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * Zaleca się, aby nie przełączać lokalnego adresu IP interfejsu sieciowego ze statycznej do protokół DHCP;, chyba że masz inny adres IP, aby połączyć się z urządzeniem. W przypadku korzystania z jednego interfejsu sieciowego i przełączania do usługi DHCP nie byłoby możliwe określenie adresu DHCP. Jeśli chcesz zmienić adres DHCP, zaczekaj, aż po aktywowaniu urządzenia za pomocą usługi, a następnie Zmień. Następnie można wyświetlić adresy IP wszystkich kart we **właściwościach urządzenia** w Azure Portal dla usługi.


    Po skonfigurowaniu i zastosowaniu ustawień sieci Wróć do pozycji **wprowadzenie**.

## <a name="enable-compute-network"></a>Włącz sieć obliczeniową

Wykonaj następujące kroki, aby włączyć obliczenia i skonfigurować sieć obliczeniową.

1. Przejdź do strony **wprowadzenie** w lokalnym interfejsie użytkownika sieci Web urządzenia. Na kafelku **Sieć** wybierz pozycję **Sieć obliczeniowa**.  

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)

2. Na stronie **Obliczanie** wybierz interfejs sieciowy, który ma zostać włączony dla obliczeń. 

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

2. W oknie dialogowym **Ustawienia sieci** wybierz pozycję **Włącz**. Po włączeniu obliczeń na urządzeniu zostanie utworzony przełącznik wirtualny w tym interfejsie sieciowym. Przełącznik wirtualny jest używany na potrzeby infrastruktury obliczeniowej na urządzeniu. 
    
3. Przypisz **adresy IP węzła Kubernetes**. Te statyczne adresy IP są przeznaczone dla maszyny wirtualnej obliczeniowej. 

    W przypadku urządzenia *n*-węzłowego w przypadku maszyny wirtualnej obliczeniowej z początkowym i końcowym adresem IP jest dostępny ciągły zakres co najmniej *n + 1* adresów IPv4. Dana Azure Stack Edge jest urządzeniem z 1 węzłem, podano co najmniej dwa ciągłe adresy IPv4.

    > [!IMPORTANT]
    > Kubernetes na Azure Stack Edge używa podsieci 172.27.0.0/16 dla usługi w podsieci pod i 172.28.0.0/16. Upewnij się, że nie są one używane w sieci. Jeśli te podsieci są już używane w sieci, można zmienić te podsieci, uruchamiając `Set-HcsKubeClusterNetworkInfo` polecenie cmdlet z interfejsu programu PowerShell urządzenia. Aby uzyskać więcej informacji, zobacz [Zmienianie Kubernetes pod i podsieci usługi](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


4. Przypisywanie **adresów IP usług zewnętrznych Kubernetes**. Są to również adresy IP równoważenia obciążenia. Te ciągłe adresy IP są przeznaczone dla usług, które mają zostać ujawnione poza klastrem Kubernetes, i określają zakres statycznych adresów IP w zależności od liczby ujawnionych usług. 
    
    > [!IMPORTANT]
    > Zdecydowanie zalecamy określenie co najmniej jednego adresu IP dla usługi Azure Stack Edge Hub, aby uzyskać dostęp do modułów obliczeniowych. Opcjonalnie można określić dodatkowe adresy IP dla innych usług/modułów IoT Edge (1 na usługę/moduł), które muszą być dostępne spoza klastra. Adresy IP usługi można później zaktualizować. 
    
5. Wybierz przycisk **Zastosuj**.

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)


## <a name="configure-web-proxy"></a>Konfigurowanie serwera proxy sieci Web

Jest to opcjonalna konfiguracja.

> [!IMPORTANT]
> * W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu brzegowym Azure Stack, zalecamy skonfigurowanie uwierzytelniania serwera proxy sieci Web jako **none**. Uwierzytelnianie NTLM nie jest obsługiwane.
>* Pliki proxy-autoconfig (PAC) nie są obsługiwane. Plik PAC definiuje, w jaki sposób przeglądarki sieci Web i inni agenci użytkowników mogą automatycznie wybierać odpowiedni serwer proxy (metoda dostępu) do pobierania danego adresu URL. Serwery proxy próbujące przechwycić i odczytać cały ruch (a następnie ponownie podpisać wszystko z własnymi certyfikatami) nie są zgodne, ponieważ certyfikat serwera proxy nie jest zaufany. Zazwyczaj przezroczyste serwery proxy działają dobrze z Azure Stack Edge. Nieprzezroczyste proxy sieci Web nie są obsługiwane.

1. Przejdź do strony **wprowadzenie** w lokalnym interfejsie użytkownika sieci Web urządzenia.
2. Na kafelku **Sieć** Skonfiguruj ustawienia serwera proxy sieci Web. Mimo że konfiguracja serwera proxy sieci Web jest opcjonalna, jeśli używasz serwera proxy sieci Web, możesz go skonfigurować tylko na tej stronie.

   ![Strona lokalnego interfejsu użytkownika sieci Web — ustawienia serwera proxy sieci Web](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

3. Na stronie **Ustawienia serwera proxy sieci Web** wykonaj następujące czynności:

    1. W polu **adres URL serwera proxy sieci Web** wprowadź adres URL w tym formacie: `http://host-IP address or FQDN:Port number` . Adresy URL HTTPS nie są obsługiwane.

    2. W obszarze **uwierzytelnianie**wybierz opcję **Brak** lub **NTLM**. W przypadku włączenia modułu obliczeniowego i używania IoT Edge na urządzeniu brzegowym Azure Stack, zalecamy ustawienie uwierzytelniania serwera proxy sieci Web na **Brak**. **Uwierzytelnianie NTLM** nie jest obsługiwane.

    3. Jeśli używasz uwierzytelniania, wprowadź nazwę użytkownika i hasło.

    4. Aby sprawdzić poprawność skonfigurowanych ustawień serwera proxy sieci Web i zastosować je, wybierz pozycję **Zastosuj**.
    
   ![Strona lokalnego interfejsu użytkownika sieci Web — ustawienia serwera proxy sieci Web](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

4. Po zastosowaniu ustawień Wróć do pozycji **wprowadzenie**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Konfigurowanie sieci
> * Włącz sieć obliczeniową
> * Konfigurowanie serwera proxy sieci Web


Aby dowiedzieć się, jak skonfigurować urządzenie Azure Stack Edge, zobacz:

> [!div class="nextstepaction"]
> [Konfiguruj ustawienia urządzenia](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
