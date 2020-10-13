---
title: Tworzenie zewnętrznego środowiska ASE
description: Dowiedz się, jak utworzyć środowisko App Service przy użyciu aplikacji lub utworzyć autonomiczną (pustą) ASE.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 03bc41a0159253359b4f87fd35d4de055b294cc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961792"
---
# <a name="create-an-external-app-service-environment"></a>Tworzenie zewnętrznego środowiska App Service

Środowisko Azure App Service Environment to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure (VNet).

> [!NOTE]
> Każdy App Service Environment ma wirtualny adres IP (VIP), który może być używany do kontaktowania się z App Service Environment.

Istnieją dwa sposoby wdrażania środowiska App Service Environment (ASE):

- Z wirtualnym adresem IP będącym zewnętrznym adresem IP — jest to często nazywane zewnętrznym środowiskiem ASE.
- Przy użyciu adresu VIP na wewnętrznym adresie IP, często nazywanego ILB ASE, ponieważ wewnętrzny punkt końcowy jest wewnętrznym Load Balancer (ILB).

W tym artykule pokazano, jak utworzyć zewnętrzny środowisko ASE. Aby zapoznać się z omówieniem środowiska ASE, zobacz [wprowadzenie do App Service Environment][Intro]. Aby uzyskać informacje na temat sposobu tworzenia środowiska ILB ASE, zobacz [Tworzenie i używanie środowiska ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Przed utworzeniem środowiska ASE

Po utworzeniu środowiska ASE nie można zmienić następujących danych:

- Lokalizacja
- Subskrypcja
- Grupa zasobów
- Użyta Sieć wirtualna
- Używana podsieć
- Rozmiar podsieci

> [!NOTE]
> Po wybraniu sieci wirtualnej i określeniu podsieci upewnij się, że jest wystarczająco duża, aby pomieścić w przyszłości potrzeby wzrostu i skalowania. Zalecamy rozmiar `/24` z 256 adresami.
>

## <a name="three-ways-to-create-an-ase"></a>Trzy sposoby tworzenia środowiska ASE

Istnieją trzy sposoby tworzenia środowiska ASE:

- **Podczas tworzenia planu App Service**. Ta metoda umożliwia utworzenie planu ASE i App Service w jednym kroku.
- **Jako akcja samodzielna**. Ta metoda tworzy autonomiczne środowisko ASE, które jest środowiskiem ASE bez żadnych elementów. Ta metoda jest bardziej zaawansowanym procesem tworzenia środowiska ASE. Służy do tworzenia środowiska ASE z ILB.
- **Z szablonu Azure Resource Manager**. Ta metoda jest dla zaawansowanych użytkowników. Aby uzyskać więcej informacji, zobacz [Tworzenie środowiska ASE na podstawie szablonu][MakeASEfromTemplate].

Zewnętrzny środowisko ASE ma publiczny adres VIP, co oznacza, że cały ruch HTTP/HTTPS do aplikacji w środowisku ASE trafi do internetowego adresu IP. Środowisko ASE z ILB ma adres IP z podsieci używanej przez środowisko ASE. Aplikacje hostowane w środowisku ILB ASE nie są uwidaczniane bezpośrednio w Internecie.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Tworzenie planu środowiska ASE i App Service razem

Plan App Service jest kontenerem aplikacji. Podczas tworzenia aplikacji w App Service wybierz lub Utwórz plan App Service. Środowiska App Service przechowują plany App Service, a App Service plany przechowują aplikacje.

Aby utworzyć środowisko ASE podczas tworzenia planu App Service:

1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Utwórz zasób**  >  **Sieć Web + aplikacje mobilne**  >  **aplikacji sieci Web**.

    ![Tworzenie aplikacji sieci Web][1]

2. Wybierz subskrypcję. Aplikacja i środowisko ASE są tworzone w tych samych subskrypcjach.

3. Wybierz lub utwórz grupę zasobów. Grupy zasobów umożliwiają zarządzanie powiązanymi zasobami platformy Azure jako jednostką. Grupy zasobów są również przydatne podczas ustanawiania Role-Based reguł Access Control dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ARMOverview].

4. Wybierz system operacyjny (Windows, Linux lub Docker). 

5. Wybierz plan App Service, a następnie wybierz pozycję **Utwórz nowy**. Aplikacje sieci Web w systemie Linux i aplikacje sieci Web systemu Windows nie mogą znajdować się w tym samym planie App Service, ale mogą być w tej samej App Service Environment. 

    ![Nowy plan App Service][2]

6. Z listy rozwijanej **Lokalizacja** wybierz region, w którym chcesz utworzyć środowisko ASE. W przypadku wybrania istniejącego środowiska ASE nowe środowisko ASE nie zostanie utworzone. W wybranym środowisku ASE zostanie utworzony plan App Service. 

7. Wybierz pozycję **warstwa cenowa**i wybierz jedną z **wyizolowanych** jednostek SKU cenowych. W przypadku wybrania karty **izolowanej** jednostki SKU i lokalizacji, która nie jest ASE, w tej lokalizacji zostanie utworzony nowy środowisko ASE. Aby rozpocząć proces tworzenia środowiska ASE, wybierz pozycję **Wybierz**. **Izolowana** jednostka SKU jest dostępna tylko w połączeniu z środowiskiem ASE. Nie można również używać żadnej innej jednostki SKU cenowej w środowisku ASE innym niż **izolowany**. 

    ![Wybór warstwy cenowej][3]

8. Wprowadź nazwę dla środowiska ASE. Ta nazwa jest używana w nazwie adresowanej dla aplikacji. Jeśli nazwa środowiska ASE to _appsvcenvdemo_, nazwa domeny to *. appsvcenvdemo.p.azurewebsites.NET*. Jeśli utworzysz aplikację o nazwie *MojaAplikacja*, adresowanie jest możliwe pod adresem mytestapp.appsvcenvdemo.p.azurewebsites.NET. Nie można użyć odstępu w nazwie. Jeśli używasz wielkich liter, nazwa domeny to łączna mała wersja tej nazwy.

    ![Nazwa nowego planu App Service][4]

9. Określ szczegóły sieci wirtualnej platformy Azure. Wybierz opcję **Utwórz nową** lub **Wybierz istniejącą**. Opcja wyboru istniejącej sieci wirtualnej jest dostępna tylko wtedy, gdy w wybranym regionie znajduje się sieć wirtualna. W przypadku wybrania opcji **Utwórz nową**wprowadź nazwę sieci wirtualnej. Zostanie utworzona nowa Menedżer zasobów Sieć wirtualna o tej nazwie. Używa przestrzeni adresowej `192.168.250.0/23` w wybranym regionie. W przypadku wybrania opcji **Wybierz istniejący**należy:

    a. Wybierz blok adresów sieci wirtualnej, jeśli masz więcej niż jeden.

    b. Wprowadź nową nazwę podsieci.

    c. Wybierz rozmiar podsieci. *Pamiętaj, aby wybrać wystarczająco duży rozmiar, aby uwzględnić przyszły rozwój środowiska ASE.* Zalecane jest `/24` , które mają 128 adresów i obsługujące środowisko ASE o maksymalnym rozmiarze. Nie zalecamy `/28` , na przykład, ponieważ dostępne są tylko 16 adresów. Infrastruktura używa co najmniej siedmiu adresów i sieci platformy Azure używa innej 5. W `/28` podsieci pozostało maksymalne skalowanie wystąpień planu 4 App Service dla zewnętrznego środowiska ASE i tylko 3 App Service wystąpienia planu dla środowiska ILB ASE.

    d. Wybierz zakres adresów IP podsieci.

10. Wybierz pozycję **Utwórz** , aby utworzyć środowisko ASE. Ten proces powoduje również utworzenie planu App Service i aplikacji. Środowisko ASE, plan App Service i aplikacja są w tej samej subskrypcji, a także w tej samej grupie zasobów. Jeśli środowisko ASE wymaga oddzielnej grupy zasobów lub jeśli potrzebujesz ILB ASE, postępuj zgodnie z instrukcjami, aby utworzyć środowisko ASE.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Tworzenie aplikacji sieci Web środowiska ASE i systemu Linux przy użyciu niestandardowego obrazu platformy Docker

1. W [Azure Portal](https://portal.azure.com/)Utwórz sieć Web + aplikacje mobilne **zasobów**  >  **Web + Mobile**  >  **Web App for Containers.** 

    ![Tworzenie aplikacji sieci Web][7]

1. Wybierz subskrypcję. Aplikacja i środowisko ASE są tworzone w tych samych subskrypcjach.

1. Wybierz lub utwórz grupę zasobów. Grupy zasobów umożliwiają zarządzanie powiązanymi zasobami platformy Azure jako jednostką. Grupy zasobów są również przydatne podczas ustanawiania Role-Based reguł Access Control dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ARMOverview].

1. Wybierz plan App Service, a następnie wybierz pozycję **Utwórz nowy**. Aplikacje sieci Web w systemie Linux i aplikacje sieci Web systemu Windows nie mogą znajdować się w tym samym planie App Service, ale mogą być w tej samej App Service Environment. 

    ![Nowy plan App Service][8]

1. Z listy rozwijanej **Lokalizacja** wybierz region, w którym chcesz utworzyć środowisko ASE. W przypadku wybrania istniejącego środowiska ASE nowe środowisko ASE nie zostanie utworzone. W wybranym środowisku ASE zostanie utworzony plan App Service. 

1. Wybierz pozycję **warstwa cenowa**i wybierz jedną z **wyizolowanych** jednostek SKU cenowych. W przypadku wybrania karty **izolowanej** jednostki SKU i lokalizacji, która nie jest ASE, w tej lokalizacji zostanie utworzony nowy środowisko ASE. Aby rozpocząć proces tworzenia środowiska ASE, wybierz pozycję **Wybierz**. **Izolowana** jednostka SKU jest dostępna tylko w połączeniu z środowiskiem ASE. Nie można również używać żadnej innej jednostki SKU cenowej w środowisku ASE innym niż **izolowany**. 

    ![Wybór warstwy cenowej][3]

1. Wprowadź nazwę dla środowiska ASE. Ta nazwa jest używana w nazwie adresowanej dla aplikacji. Jeśli nazwa środowiska ASE to _appsvcenvdemo_, nazwa domeny to *. appsvcenvdemo.p.azurewebsites.NET*. Jeśli utworzysz aplikację o nazwie *MojaAplikacja*, adresowanie jest możliwe pod adresem mytestapp.appsvcenvdemo.p.azurewebsites.NET. Nie można użyć odstępu w nazwie. Jeśli używasz wielkich liter, nazwa domeny to łączna mała wersja tej nazwy.

    ![Nazwa nowego planu App Service][4]

1. Określ szczegóły sieci wirtualnej platformy Azure. Wybierz opcję **Utwórz nową** lub **Wybierz istniejącą**. Opcja wyboru istniejącej sieci wirtualnej jest dostępna tylko wtedy, gdy w wybranym regionie znajduje się sieć wirtualna. W przypadku wybrania opcji **Utwórz nową**wprowadź nazwę sieci wirtualnej. Zostanie utworzona nowa Menedżer zasobów Sieć wirtualna o tej nazwie. Używa przestrzeni adresowej `192.168.250.0/23` w wybranym regionie. W przypadku wybrania opcji **Wybierz istniejący**należy:

    a. Wybierz blok adresów sieci wirtualnej, jeśli masz więcej niż jeden.

    b. Wprowadź nową nazwę podsieci.

    c. Wybierz rozmiar podsieci. *Pamiętaj, aby wybrać wystarczająco duży rozmiar, aby uwzględnić przyszły rozwój środowiska ASE.* Zalecane jest `/24` , które mają 128 adresów i obsługujące środowisko ASE o maksymalnym rozmiarze. Nie zalecamy `/28` , na przykład, ponieważ dostępne są tylko 16 adresów. Infrastruktura używa co najmniej siedmiu adresów i sieci platformy Azure używa innej 5. W `/28` podsieci pozostało maksymalne skalowanie wystąpień planu 4 App Service dla zewnętrznego środowiska ASE i tylko 3 App Service wystąpienia planu dla środowiska ILB ASE.

    d. Wybierz zakres adresów IP podsieci.

1.  Wybierz pozycję "Konfiguruj kontener".
    * Wprowadź nazwę niestandardowego obrazu (można użyć Azure Container Registry, Docker Hub i własnego rejestru prywatnego). Jeśli nie chcesz używać własnego kontenera niestandardowego, możesz po prostu przenieść kod i użyć wbudowanego obrazu z App Service w systemie Linux, korzystając z powyższych instrukcji. 

    ![Konfigurowanie kontenera][9]

1. Wybierz pozycję **Utwórz** , aby utworzyć środowisko ASE. Ten proces powoduje również utworzenie planu App Service i aplikacji. Środowisko ASE, plan App Service i aplikacja są w tej samej subskrypcji, a także w tej samej grupie zasobów. Jeśli środowisko ASE wymaga oddzielnej grupy zasobów lub jeśli potrzebujesz ILB ASE, postępuj zgodnie z instrukcjami, aby utworzyć środowisko ASE.


## <a name="create-an-ase-by-itself"></a>Tworzenie środowiska ASE przez siebie

Jeśli tworzysz autonomiczną aplikację ASE, nie ma nic w nim. Pusta środowisko ASE nadal jest miesięczną opłatą za infrastrukturę. Wykonaj następujące kroki, aby utworzyć środowisko ASE z ILB lub utworzyć środowisko ASE w jego własnej grupie zasobów. Po utworzeniu środowiska ASE można tworzyć w nim aplikacje przy użyciu normalnego procesu. Wybierz nowy środowisko ASE jako lokalizację.

1. Wyszukaj **App Service Environment**portalu Azure Marketplace, a następnie wybierz pozycję **Utwórz zasób**  >  **Sieć Web**  >  **App Service Environment**Mobile. 

1. Wprowadź nazwę środowiska ASE. Ta nazwa jest używana dla aplikacji utworzonych w środowisku ASE. Jeśli nazwa to *mynewdemoase*, nazwa domeny podrzędnej to *. mynewdemoase.p.azurewebsites.NET*. Jeśli utworzysz aplikację o nazwie *MojaAplikacja*, adresowanie jest możliwe pod adresem mytestapp.mynewdemoase.p.azurewebsites.NET. Nie można użyć odstępu w nazwie. Jeśli używasz wielkich liter, nazwa domeny to łączna mała wersja nazwy. Jeśli używasz ILB, Nazwa środowiska ASE nie jest używana w poddomenie, ale zamiast tego jest jawnie określona podczas tworzenia ASE.

    ![Nazewnictwo środowiska ASE][5]

1. Wybierz subskrypcję. Ta subskrypcja jest również tą, którą używają wszystkie aplikacje w środowisku ASE. Nie można umieścić środowiska ASE w sieci wirtualnej, która znajduje się w innej subskrypcji.

1. Wybierz lub określ nową grupę zasobów. Grupa zasobów używana dla środowiska ASE musi być taka sama, jak ta, która jest używana w sieci wirtualnej. W przypadku wybrania istniejącej sieci wirtualnej wybór grupy zasobów dla środowiska ASE zostanie zaktualizowany w celu odzwierciedlenia tej sieci wirtualnej. *Jeśli używasz szablonu Menedżer zasobów, możesz utworzyć środowisko ASE z grupą zasobów, która różni się od grupy zasobów sieci wirtualnej.* Aby utworzyć środowisko ASE na podstawie szablonu, zobacz [Tworzenie środowiska App Service na podstawie szablonu][MakeASEfromTemplate].

    ![Wybór grupy zasobów][6]

1. Wybierz sieć wirtualną i lokalizację. Można utworzyć nową sieć wirtualną lub wybrać istniejącą sieć wirtualną: 

    * W przypadku wybrania nowej sieci wirtualnej możesz określić nazwę i lokalizację. 
    
    * Nowa sieć wirtualna ma zakres adresów 192.168.250.0/23 i podsieć o nazwie Default. Podsieć jest definiowana jako 192.168.250.0/24. Można wybrać tylko Menedżer zasobów sieci wirtualnej. Wybór **typu adresu VIP** określa, czy środowisko ASE można uzyskać bezpośrednio z Internetu (zewnętrznego) czy przy użyciu ILB. Aby dowiedzieć się więcej na temat tych opcji, zobacz [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia ze środowiskiem App Service][MakeILBASE]. 

      * W przypadku wybrania opcji **zewnętrzny** dla **typu adresu VIP**można wybrać liczbę zewnętrznych adresów IP, z którymi system został utworzony na potrzeby protokołu SSL opartego na protokole IP. 
    
      * W przypadku wybrania opcji **wewnętrzna** dla **typu adresu VIP**należy określić domenę używaną przez środowisko ASE. Środowisko ASE można wdrożyć w sieci wirtualnej, która używa zakresów adresów publicznych lub prywatnych. Aby użyć sieci wirtualnej z zakresem adresów publicznych, należy najpierw utworzyć sieć wirtualną przed czasem. 
    
    * Jeśli zostanie wybrana istniejąca sieć wirtualna, podczas tworzenia środowiska ASE zostanie utworzona nowa podsieć. *Nie można użyć wstępnie utworzonej podsieci w portalu. Jeśli używasz szablonu Menedżer zasobów, możesz utworzyć środowisko ASE z istniejącą podsiecią.* Aby utworzyć środowisko ASE na podstawie szablonu, zobacz [tworzenie App Service Environment na podstawie szablonu][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1

Nadal można tworzyć wystąpienia pierwszej wersji App Service Environment (środowiska asev1). Aby rozpocząć ten proces, Wyszukaj w witrynie Marketplace **App Service Environment V1**. Tworzysz środowisko ASE w taki sam sposób, jak w przypadku tworzenia autonomicznego środowiska ASE. Po zakończeniu środowiska asev1 ma dwa Frontony i dwóch procesów roboczych. W programie środowiska asev1 należy zarządzać frontonami i pracownikami. Nie są one automatycznie dodawane podczas tworzenia planów App Service. Frontony działają jako punkty końcowe protokołu HTTP/HTTPS i wysyłają ruch do pracowników. Procesy robocze to role obsługujące aplikacje. Możesz dostosować liczbę frontonów i procesów roboczych po utworzeniu środowiska ASE. 

Aby dowiedzieć się więcej na temat środowiska asev1, zobacz [wprowadzenie do App Service Environment V1][ASEv1Intro]. Aby uzyskać więcej informacji na temat skalowania i monitorowania środowiska asev1 oraz zarządzania nim, zobacz [jak skonfigurować App Service Environment][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md