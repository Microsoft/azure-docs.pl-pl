---
title: Tworzenie ILB ASE przy użyciu platformy ARM
description: Dowiedz się, jak utworzyć środowisko App Service za pomocą wewnętrznego modułu równoważenia obciążenia (ILB ASE) przy użyciu szablonów Azure Resource Manager. W pełni Izoluj aplikacje z Internetu.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: c176a1169bf8acb41eeb59f916ba30df677184a3
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080596"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Tworzenie i używanie wewnętrznego Load Balancer App Service Environment 

Azure App Service Environment to wdrożenie Azure App Service w podsieci w sieci wirtualnej platformy Azure. Istnieją dwa sposoby wdrażania środowiska App Service Environment (ASE): 

- Z wirtualnym adresem IP będącym zewnętrznym adresem IP — jest to często nazywane zewnętrznym środowiskiem ASE.
- Z wirtualnym adresem IP będącym wewnętrznym adresem IP — jest to często nazywane środowiskiem ASE z wewnętrznym modułem równoważenia obciążenia, ponieważ wewnętrzny punkt końcowy jest wewnętrznym modułem równoważenia obciążenia (ILB, Internal Load Balancer). 

W tym artykule przedstawiono sposób tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia. Aby zapoznać się z omówieniem środowiska ASE, zobacz [wprowadzenie do środowisk App Service][Intro]. Aby dowiedzieć się, jak utworzyć zewnętrzne środowisko ASE, zobacz [Create an External ASE][MakeExternalASE] (Tworzenie zewnętrznego środowiska ASE).

## <a name="overview"></a>Omówienie 

Środowisko ASE można wdrożyć za pomocą punktu końcowego dostępnego z Internetu lub adresu IP w sieci wirtualnej. Aby można było ustawić jako adres IP adres sieci wirtualnej, należy wdrożyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia. W przypadku wdrażania środowiska ASE przy użyciu ILB należy podać nazwę środowiska ASE. Nazwa środowiska ASE jest używana w sufiksie domeny dla aplikacji w środowisku ASE.  Sufiks domeny dla środowiska ILB ASE to &lt; ASE Name &gt; . appserviceenvironment.NET. Aplikacje utworzone w środowisku ILB ASE nie są umieszczane w publicznym systemie DNS. 

Wcześniejsze wersje programu ILB ASE wymagały podania sufiksu domeny i domyślnego certyfikatu dla połączeń HTTPS. Sufiks domeny nie jest już zbierany podczas tworzenia ILB ASE i nie jest już zbierany certyfikat domyślny. Po utworzeniu ILB ASE teraz certyfikat domyślny jest dostarczany przez firmę Microsoft i jest traktowany jako zaufany przez przeglądarkę. Nadal można ustawiać niestandardowe nazwy domen w aplikacjach w środowisku ASE i ustawiać certyfikaty dla tych niestandardowych nazw domen. 

Za pomocą ILB ASE można wykonywać następujące czynności:

-   Bezpiecznie udostępniaj aplikacje intranetowe w chmurze, do których uzyskujesz dostęp za pośrednictwem typu lokacja-lokacja lub ExpressRoute.
-   Ochrona aplikacji za pomocą urządzenia WAF
-   Hostowanie w chmurze aplikacji, które nie są wymienione na publicznych serwerach DNS.
-   Tworzenie odizolowanych od Internetu aplikacji zaplecza, z którymi można bezpiecznie integrować aplikacje frontonu.

### <a name="disabled-functionality"></a>Funkcje wyłączone ###

Pewnych zadań nie można realizować w przypadku używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia:

-   Używanie protokołu SSL opartego na protokole IP.
-   Przypisywanie adresów IP do określonych aplikacji.
-   Kupowanie i używanie certyfikatu za pomocą aplikacji za pośrednictwem witryny Azure Portal. Certyfikaty można uzyskiwać bezpośrednio od urzędu certyfikacji i używać ich z aplikacjami. Nie można ich uzyskiwać za pośrednictwem witryny Azure Portal.

## <a name="create-an-ilb-ase"></a>Tworzenie środowiska ASE z wewnętrznym modułem równoważenia obciążenia ##

Aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia:

1. W Azure Portal wybierz pozycję **Utwórz zasób**  >  App Service Environment**sieci Web**  >  **App Service Environment**.

2. Wybierz subskrypcję.

3. Wybierz lub utwórz grupę zasobów.

4. Wprowadź nazwę App Service Environment.

5. Wybierz typ wirtualnego adresu IP wewnętrzny.

    ![Tworzenie środowiska ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> Nazwa App Service Environment nie może być dłuższa niż 37 znaków.

6. Wybieranie sieci

7. Wybierz lub Utwórz Virtual Network. Jeśli w tym miejscu utworzysz nową sieć wirtualną, zostanie ona zdefiniowana z zakresem adresów 192.168.250.0/23. Aby utworzyć sieć wirtualną z innym zakresem adresów lub w innej grupie zasobów niż środowisko ASE, użyj portalu tworzenia Virtual Network platformy Azure. 

8. Wybierz lub Utwórz pustą podsieć. Jeśli chcesz wybrać podsieć, musi ona być pusta, a nie delegowana. Nie można zmienić rozmiaru podsieci po utworzeniu środowiska ASE. Zalecamy rozmiar `/24`, który zapewnia 256 adresów i może obsłużyć środowiska ASE o maksymalnym rozmiarze i dowolnych potrzebach dotyczących skalowania. 

    ![Sieć ASE][1]

7. Wybierz pozycję **Przegląd i Utwórz,** a następnie wybierz pozycję **Utwórz**.


## <a name="create-an-app-in-an-ilb-ase"></a>Tworzenie aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia ##

Aplikację w środowisku ASE z wewnętrznym modułem równoważenia obciążenia tworzy się tak samo jak w normalnym środowisku ASE.

1. W Azure Portal wybierz pozycję **Utwórz zasób**  >  **Sieć**Web Web  >  **App**.

1. Wprowadź nazwę aplikacji.

1. Wybierz subskrypcję.

1. Wybierz lub utwórz grupę zasobów.

1. Wybierz pozycję publikowanie, stos środowiska uruchomieniowego i system operacyjny.

1. Wybierz lokalizację, w której lokalizacja jest istniejącym ILB ASE.  Możesz również utworzyć nowy środowisko ASE podczas tworzenia aplikacji, wybierając odizolowany plan App Service. Jeśli chcesz utworzyć nowe środowisko ASE, wybierz region, w którym ma zostać utworzone środowisko ASE.

1. Wybierz lub utwórz plan usługi App Service. 

1. Wybierz pozycję **Przejrzyj i Utwórz,** a następnie wybierz pozycję **Utwórz** , gdy wszystko będzie gotowe.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Zadania Web Job, usługa Functions i środowisko ASE z wewnętrznym modułem równoważenia obciążenia 

W środowisku ASE z wewnętrznym modułem równoważenia obciążenia jest obsługiwana zarówno usługa Functions, jak i zadania Web Job, ale aby portal z nimi współdziałał, musisz mieć dostęp do witryny SCM.  Oznacza to, że przeglądarka musi działać na hoście, który albo znajduje się w sieci wirtualnej, albo jest z nią połączony. Jeśli ILB ASE ma nazwę domeny, która nie kończy się *appserviceenvironment.NET*, należy uzyskać dostęp do przeglądarki, aby ufać certyfikatowi HTTPS używanego przez witrynę SCM.

## <a name="dns-configuration"></a>Konfiguracja usługi DNS 

Gdy używasz zewnętrznego wirtualnego adresu IP, usługą DNS zarządza platforma Azure. Każda aplikacja utworzona w środowisku ASE jest automatycznie dodawana do usługi Azure DNS, która jest publiczną usługą DNS. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia musisz zarządzać własną usługą DNS. Sufiks domeny używany z ILB ASE zależy od nazwy środowiska ASE. Sufiks domeny to * &lt; ASE name &gt; . appserviceenvironment.NET*. Adres IP ILB znajduje się w portalu w obszarze **adresy IP**. 

Aby skonfigurować serwer DNS:

- Utwórz strefę dla * &lt; nazwy środowiska ASE &gt; . appserviceenvironment.NET*
- Utwórz rekord A w tej strefie, który wskazuje * na adres IP ILB
- Utwórz rekord A w tej strefie, który wskazuje na ILB adres IP
- Utwórz strefę w programie * &lt; ASE name &gt; . appserviceenvironment.NET* o nazwie SCM
- Utwórz rekord A w strefie SCM, który wskazuje * na adres IP ILB

## <a name="publish-with-an-ilb-ase"></a>Publikowanie za pomocą środowiska ASE z wewnętrznym modułem równoważenia obciążenia

Dla każdej tworzonej aplikacji istnieją dwa punkty końcowe. W środowisku ILB ASE masz * &lt; nazwę aplikacji &gt; . &lt; Domena &gt; * i * &lt; Nazwa aplikacji ILB ASE &gt; . SCM. &lt; &gt;Domena ILB ASE*. 

Nazwa witryny SCM umożliwia przejście do konsoli Kudu, nazywanej **portalem zaawansowanym**, w witrynie Azure Portal. Konsola Kudu umożliwia między innymi wyświetlanie zmiennych środowiskowych, eksplorowanie dysku i używanie konsoli. Aby uzyskać więcej informacji, zobacz [Kudu console for Azure App Service][Kudu] (Konsola Kudu dla usługi Azure App Service). 

Internetowe systemy ciągłej integracji, takie jak usługi GitHub i Azure DevOps, będą nadal działać ze środowiskiem ASE z wewnętrznym modułem równoważenia obciążenia, jeśli agent kompilacji jest dostępny w Internecie i w tej samej sieci co to środowisko. Dlatego w przypadku usługi Azure DevOps, jeśli agent kompilacji został utworzony w tej samej sieci wirtualnej co środowisko ASE z wewnętrznym modułem równoważenia obciążenia (być może w innej podsieci), to będzie mógł ściągnąć kod z usługi Git Azure DevOps i wdrożyć go w tym środowisku. Jeśli nie chcesz tworzyć własnego agenta kompilacji, musisz użyć systemu ciągłej integracji wykorzystującego model ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Ta domena jest wyświetlana w profilu publikowania aplikacji i w bloku portalu aplikacji (**Omówienie**  >  **podstawy** i **Właściwości**). Jeśli masz ILB ASE z sufiksem domeny * &lt; ASE name &gt; . appserviceenvironment.NET*i aplikacją o nazwie Moje *testy*, użyj *testu. &lt; Środowisko ASE Name &gt; . appserviceenvironment.NET* dla usługi FTP i *mytest.SCM.contoso.NET* na potrzeby wdrażania w sieci Web.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurowanie ILB ASE z urządzeniem WAF ##

Możesz połączyć urządzenie zapory aplikacji sieci Web (WAF) z ILB ASE, aby uwidocznić tylko aplikacje, które chcesz połączyć z Internetem, i utrzymać dostęp tylko z sieci wirtualnej. Dzięki temu można tworzyć bezpieczne aplikacje wielowarstwowe między innymi.

Aby dowiedzieć się więcej o sposobie konfigurowania ILB ASE za pomocą urządzenia WAF, zobacz [Konfigurowanie zapory aplikacji sieci Web za pomocą środowiska App Service][ASEWAF]. W tym artykule wyjaśniono, jak używać urządzenia wirtualnego Barracuda ze środowiskiem ASE. Innym rozwiązaniem jest używanie usługi Azure Application Gateway. Usługa Application Gateway zabezpiecza wszelkie umieszczone za nią aplikacje za pomocą reguł podstawowych OWASP. Aby uzyskać więcej informacji na temat usługi Application Gateway, zobacz [Introduction to the Azure web application firewall][AppGW] (Wprowadzenie do zapory aplikacji internetowych platformy Azure).

## <a name="ilb-ases-made-before-may-2019"></a>ILB środowisk ASE wykonane przed 2019 maja

ILB środowisk ASE, które zostały wprowadzone przed 2019 maja, wymagały ustawienia sufiksu domeny podczas tworzenia środowiska ASE. Wymagały one również przekazania domyślnego certyfikatu opartego na tym sufiksie domeny. Ponadto przy użyciu starszej wersji ILB ASE nie można przeprowadzić logowania jednokrotnego do konsoli kudu z aplikacjami w tym ILB ASE. Podczas konfigurowania systemu DNS dla starszej wersji środowiska ILB ASE należy ustawić symbol wieloznaczny A rekordu w strefie, która pasuje do sufiksu domeny. 

## <a name="get-started"></a>Rozpoczęcie pracy ##

* Aby rozpocząć pracę ze środowiskami ASE, zobacz [Wprowadzenie do środowisk App Service Environment][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux
