---
title: Integracja z usługą Application Gateway
description: Dowiedz się, jak zintegrować aplikację w ILB App Service Environment z Application Gateway z tego kompleksowego przewodnika.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3b73d528802a8aa33c6122eaf5edfa9d046b6753
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962081"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrate your ILB App Service Environment with the Azure Application Gateway (Integrowanie środowiska App Service Environment wewnętrznego modułu równoważenia obciążenia z usługą Azure Application Gateway) #

[App Service Environment](./intro.md) to wdrożenie Azure App Service w podsieci sieci wirtualnej platformy Azure klienta. Można ją wdrożyć za pomocą publicznego lub prywatnego punktu końcowego na potrzeby dostępu do aplikacji. Wdrożenie App Service Environment z prywatnym punktem końcowym (czyli wewnętrznym modułem równoważenia obciążenia) nosi nazwę ILB App Service Environment.  

Zapory aplikacji sieci Web ułatwiają Zabezpieczanie aplikacji sieci Web przez inspekcję ruchu przychodzącego sieci Web w celu blokowania iniekcji SQL, skryptów między lokacjami, przekazywania złośliwego oprogramowania & aplikacji DDoS i innych ataków. Sprawdza także odpowiedzi z serwerów zaplecza sieci Web w celu zapobiegania utracie danych (DLP). Możesz uzyskać urządzenie WAF z witryny Azure Marketplace lub skorzystać z [Application Gateway platformy Azure][appgw].

Application Gateway platformy Azure to urządzenie wirtualne zapewniające Równoważenie obciążenia warstwy 7, odciążanie protokołu TLS/SSL i ochronę aplikacji sieci Web (WAF). Może nasłuchiwać publicznego adresu IP i kierować ruchem do punktu końcowego aplikacji. Poniższe informacje opisują sposób integracji bramy aplikacji skonfigurowanej przez WAF z aplikacją w ILB App Service Environment.  

Integracja bramy Application Gateway z ILB App Service Environment jest na poziomie aplikacji. Gdy skonfigurujesz bramę aplikacji przy użyciu App Service Environment ILB, wykonujesz ją dla określonych aplikacji w App Service Environment ILB. Ta technika umożliwia hostowanie bezpiecznych aplikacji wielodostępnych w jednym ILB App Service Environment.  

![Brama aplikacji wskazująca aplikację na ILB App Service Environment][1]

Korzystając z tego przewodnika, wykonasz następujące czynności:

* Utwórz Application Gateway platformy Azure.
* Skonfiguruj Application Gateway tak, aby wskazywała aplikację w App Service Environmentu ILB.
* Skonfiguruj aplikację do przestrzegania niestandardowej nazwy domeny.
* Edytuj publiczną nazwę hosta DNS, która wskazuje na bramę aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować Application Gateway z usługą ILB App Service Environment, potrzebne są:

* ILB App Service Environment.
* Aplikacja działająca w App Service Environment ILB.
* Nazwa domeny z obsługą Internetu, która będzie używana z aplikacją w App Service Environment ILB.
* Adres ILB używany przez App Service Environment ILBa. Te informacje są w portalu App Service Environment w obszarze **Ustawienia**  >  **adresy IP**:

    ![Przykładowa lista adresów IP używanych przez ILB App Service Environment][9]
    
* Publiczna nazwa DNS używana później do wskazywania Application Gateway. 

Aby uzyskać szczegółowe informacje na temat tworzenia App Service Environment ILB, zobacz [Tworzenie i używanie ILB App Service Environment][ilbase].

W tym artykule założono, że chcesz, aby Application Gateway w tej samej sieci wirtualnej platformy Azure, w której wdrożono App Service Environment. Przed rozpoczęciem tworzenia Application Gateway wybierz lub Utwórz podsieć, która będzie używana do hostowania bramy. 

Należy użyć podsieci, która nie jest nazwą GatewaySubnet. Jeśli umieścisz Application Gateway w GatewaySubnet, nie będzie można później utworzyć bramy sieci wirtualnej. 

Nie można również umieścić bramy w podsieci używanej przez ILB App Service Environment. App Service Environment jest jedynym warunkiem, który może znajdować się w tej podsieci.

## <a name="configuration-steps"></a>Kroki konfiguracji ##

1. W Azure Portal przejdź do pozycji **Nowy**  >    >  **Application Gateway** sieci.

2. W obszarze **podstawy** :

   a. W polu **Nazwa** wprowadź nazwę Application Gateway.

   b. W obszarze **warstwa** wybierz pozycję **WAF**.

   c. W polu **subskrypcja** wybierz tę samą subskrypcję, której używa App Service Environment Sieć wirtualna.

   d. W obszarze **Grupa zasobów** Utwórz lub wybierz grupę zasobów.

   e. W polu **Lokalizacja** wybierz lokalizację sieci wirtualnej App Service Environment.

   ![Nowe podstawy tworzenia Application Gateway][2]

3. W obszarze **Ustawienia** :

   a. W obszarze **Sieć wirtualna** wybierz App Service Environment sieci wirtualnej.

   b. W obszarze **podsieć** wybierz podsieć, w której ma zostać wdrożona Application Gateway. Nie należy używać GatewaySubnet, ponieważ uniemożliwi to tworzenie bram sieci VPN.

   c. W obszarze **Typ adresu IP** wybierz pozycję **publiczny**.

   d. W obszarze **publiczny adres IP** wybierz publiczny adres IP. Jeśli go nie masz, utwórz go teraz.

   e. W obszarze **Protokół** wybierz pozycję **http** lub **https**. W przypadku konfigurowania protokołu HTTPS należy podać certyfikat PFX.

   f. W przypadku **zapory aplikacji sieci Web** można włączyć zaporę i skonfigurować ją do **wykrywania** lub **zapobiegania** , jak jest to zgodne.

   ![Nowe ustawienia tworzenia Application Gateway][3]
    
4. W sekcji **Podsumowanie** przejrzyj ustawienia, a następnie wybierz **przycisk OK**. Ukończenie instalacji Application Gateway może potrwać zaledwie 30 minut.  

5. Po zakończeniu instalacji Application Gateway przejdź do portalu Application Gateway. Wybierz **pulę zaplecza**. Dodaj adres ILB dla App Service Environment ILB.

   ![Konfigurowanie puli zaplecza][4]

6. Po zakończeniu procesu konfigurowania puli zaplecza wybierz pozycję **sondy kondycji**. Utwórz sondę kondycji dla nazwy domeny, która ma być używana dla aplikacji. 

   ![Konfigurowanie sond kondycji][5]
    
7. Po zakończeniu procesu konfigurowania sond kondycji wybierz pozycję **Ustawienia http**. Edytuj istniejące ustawienia, wybierz opcję **Użyj sondy niestandardowej** i wybierz skonfigurowaną sondę.

   ![Skonfiguruj ustawienia protokołu HTTP][6]
    
8. Przejdź do sekcji **przegląd** Application Gateway i skopiuj publiczny adres IP używany przez Application Gateway. Ustaw ten adres IP jako rekord A dla nazwy domeny aplikacji lub użyj nazwy DNS dla tego adresu w rekordzie CNAME. Łatwiejszym rozwiązaniem jest wybranie publicznego adresu IP i skopiowanie go z interfejsu użytkownika publicznego adresu IP, a nie skopiowanie go z linku w sekcji **przegląd** Application Gateway. 

   ![Portal Application Gateway][7]

9. Ustaw niestandardową nazwę domeny dla aplikacji w App Service Environment ILB. Przejdź do aplikacji w portalu, a następnie w obszarze **Ustawienia** wybierz pozycję **domeny niestandardowe**.

   ![Ustawianie niestandardowej nazwy domeny w aplikacji][8]

Informacje na temat ustawiania niestandardowych nazw domen dla aplikacji sieci Web w artykule [Ustawienia niestandardowych nazw domen dla aplikacji sieci Web][custom-domain]. Jednak w przypadku aplikacji w ILB App Service Environment nie ma żadnego sprawdzenia poprawności nazwy domeny. Ze względu na to, że jesteś administratorem usługi DNS, która zarządza punktami końcowymi aplikacji, możesz w tym miejscu umieścić dowolną z nich. Niestandardowa nazwa domeny dodawana w tym przypadku nie musi znajdować się w systemie DNS, ale nadal trzeba ją skonfigurować przy użyciu aplikacji. 

Po zakończeniu instalacji i przekroczeniu limitu czasu oczekiwania na propagowanie zmian w systemie DNS możesz uzyskać dostęp do aplikacji przy użyciu utworzonej nazwy domeny niestandardowej. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md