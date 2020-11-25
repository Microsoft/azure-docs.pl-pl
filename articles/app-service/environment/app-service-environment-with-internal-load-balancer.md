---
title: Tworzenie ILB ASE v1
description: Tworzenie i używanie środowiska ASE z ILB. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c4e5dedf2075a2e13cc91c5eed2c0f03ba498b97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021524"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Używanie wewnętrznego Load Balancer z App Service Environment

> [!NOTE] 
> Ten artykuł dotyczy App Service Environment v1. Istnieje nowsza wersja App Service Environment ułatwiająca korzystanie z bardziej wydajną infrastrukturą. Aby dowiedzieć się więcej o nowej wersji, Zacznij od [wprowadzenia do App Service Environment](intro.md).
>

Funkcja App Service Environment (ASE) to opcja usługi Premium Azure App Service, która zapewnia rozszerzoną funkcję konfiguracji, która nie jest dostępna w sygnaturach z wieloma dzierżawcami. Funkcja ASE zasadniczo wdraża Azure App Service w usłudze Azure Virtual Network (VNet). Aby uzyskać lepsze zrozumienie możliwości oferowanych przez App Service środowiska, przeczytaj artykuł [co to jest App Service Environment][WhatisASE] dokumentacja. Jeśli nie znasz korzyści płynących z używania sieci wirtualnej, zapoznaj się z tematem [często zadawanych pytań dotyczących platformy Azure Virtual Network][virtualnetwork]. 

## <a name="overview"></a>Omówienie
Środowisko ASE można wdrożyć za pomocą punktu końcowego dostępnego z Internetu lub z adresem IP w sieci wirtualnej. Aby ustawić adres IP na adres sieci wirtualnej, należy wdrożyć środowisko ASE przy użyciu wewnętrznego Load Balancer (ILB). Gdy środowisko ASE jest skonfigurowane z ILB, należy udostępnić:

* Twoja domena lub poddomena. Aby to ułatwić, ten dokument zakłada, że jest poddomeną, ale można go skonfigurować w dowolny sposób. 
* certyfikat używany na potrzeby protokołu HTTPS
* Zarządzanie usługą DNS dla domeny podrzędnej. 

Umożliwia to realizowanie m.in. następujących zadań:

* Hostowanie aplikacji intranetowych, takich jak aplikacje biznesowe, bezpiecznie w chmurze, do których uzyskuje się dostęp za pośrednictwem lokacji do lokacji lub sieci VPN ExpressRoute
* Hostowanie aplikacji w chmurze, które nie są wymienione na publicznych serwerach DNS
* Tworzenie izolowanych od Internetu aplikacji zaplecza, z którymi aplikacje frontonu mogą być bezpiecznie integrowane

#### <a name="disabled-functionality"></a>Funkcje wyłączone
W przypadku korzystania z programu ILB ASE nie można wykonać niektórych rzeczy. Należą do nich następujące elementy:

* Korzystanie z IPSSL
* Przypisywanie adresów IP do określonych aplikacji
* Kupowanie i używanie certyfikatu z aplikacją za pośrednictwem portalu. Oczywiście można nadal uzyskiwać certyfikaty bezpośrednio z urzędem certyfikacji i używać ich z aplikacjami, ale nie za pomocą Azure Portal.

## <a name="creating-an-ilb-ase"></a>Tworzenie ILB ASE
Tworzenie środowiska ILB ASE nie różni się od tworzenia w normalnych warunkach środowiska ASE. Dokładniejsze omówienie tworzenia środowiska ASE można znaleźć w temacie [How to Create a App Service Environment][HowtoCreateASE]. Proces tworzenia środowiska ILB ASE jest taki sam między tworzeniem sieci wirtualnej podczas tworzenia środowiska ASE lub wybrania istniejącej sieci wirtualnej. Aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia: 

1. W Azure Portal wybierz pozycję **Utwórz zasób > sieć Web + aplikacje mobilne-> App Service Environment**.
2. Wybierz subskrypcję.
3. Wybierz lub utwórz grupę zasobów.
4. Wybierz lub utwórz sieć wirtualną.
5. Utwórz podsieć w przypadku wybrania sieci wirtualnej.
6. Wybierz pozycję **Konfiguracja sieci wirtualnej Virtual Network/lokalizacja->** i ustaw dla opcji Typ adresu VIP wartość wewnętrzna.
7. Podaj nazwę domeny podrzędnej (Ta nazwa jest poddomeną używaną dla aplikacji tworzonych w tym środowisku ASE).
8. Wybierz przycisk **OK** , a następnie **Utwórz**.

![Pokazuje ekrany używane do tworzenia ILB środowiska ASE.][1]

W okienku Virtual Network jest dostępna opcja konfiguracji sieci wirtualnej, która umożliwia wybranie między zewnętrznym adresem VIP lub wewnętrznym adresem VIP. Wartość domyślna to Zewnętrzny. Jeśli jest ustawiona na zewnętrzny, środowisko ASE używa adresu VIP dostępnego z Internetu. W przypadku wybrania opcji Wewnętrzny środowisko ASE jest skonfigurowane z wewnętrznym modułem równoważenia obciążenia i adresem IP w sieci wirtualnej. 

Po wybraniu opcji wewnętrzna możliwość dodawania większej liczby adresów IP do środowiska ASE jest usuwana, a zamiast tego należy udostępnić poddomenę ASE. W środowisku ASE z zewnętrznym adresem VIP Nazwa środowiska ASE jest używana w poddomenie dla aplikacji utworzonych w tym środowisku ASE. Jeśli środowisko ASE ma nazwę **_contosotest_* _, a aplikacja w tym środowisku ASE nosi nazwę "Moje _*_testy_*_", poddomena ma format _*_contosotest.p.azurewebsites.NET_*_ i adres URL dla tej aplikacji to _*_mytest.contosotest.p.azurewebsites.NET_*_. Jeśli ustawisz typ adresu VIP na wewnętrzny, Nazwa środowiska ASE nie zostanie użyta w poddomenie dla środowiska ASE. Należy jawnie określić poddomenę. Jeśli poddomeną jest _*_contoso.Corp.NET_*_ , a aplikacja w tym środowisku ASE nosi nazwę _*_raportowanieczasu_*_, adres URL dla tej aplikacji to _*_timereporting.contoso.Corp.NET_*_.

## <a name="apps-in-an-ilb-ase"></a>Aplikacje w środowisku ILB ASE
Tworzenie aplikacji w środowisku ILB ASE jest takie samo jak w przypadku normalnego tworzenia aplikacji w środowisku ASE. 

1. W Azure Portal wybierz pozycję _ *Utwórz zasób > sieć Web + aplikacje mobilne-> Web** lub **Mobile** lub **aplikacja interfejsu API**.
2. Wprowadź nazwę aplikacji.
3. Wybierz subskrypcję.
4. Wybierz lub utwórz grupę zasobów.
5. Wybierz lub Utwórz plan App Service (ASP). W przypadku tworzenia nowego środowiska ASP Wybierz środowisko ASE jako lokalizację i wybierz pulę procesów roboczych, w której ma zostać utworzona wartość ASP. Podczas tworzenia środowiska ASP należy wybrać środowisko ASE jako lokalizację i pulę procesów roboczych. Po określeniu nazwy aplikacji zobaczysz, że poddomena w ramach nazwy aplikacji jest zastępowana poddomeną dla środowiska ASE. 
6. Wybierz pozycję **Utwórz**. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** , jeśli chcesz, aby aplikacja była wyświetlana na pulpicie nawigacyjnym. 

![Pokazuje, jak utworzyć aplikację w ILB ASE w Azure Portal.][2]

W obszarze Nazwa aplikacji nazwa poddomeny zostanie zaktualizowana w celu odzwierciedlenia poddomeny środowiska ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Sprawdzanie poprawności tworzenia ILB ASE
Środowisko ASE z wewnętrznym modułem równoważenia obciążenia różni się nieco od wersji bez tego modułu. Jak już zauważono, musisz zarządzać własnym systemem DNS i należy również udostępnić własny certyfikat dla połączeń HTTPS. 

Po utworzeniu środowiska ASE należy zauważyć, że poddomena zawiera określoną poddomenę, a w menu **Ustawienia** o nazwie **certyfikat ILB** jest dostępny nowy element. Środowisko ASE jest tworzone z certyfikatem z podpisem własnym, co ułatwia testowanie protokołu HTTPS. Portal informuje o tym, że musisz podać własny certyfikat dla protokołu HTTPS, ale ma to na celu zachęcanie do posiadania certyfikatu z poddomeną. 

![Pokazuje poddomenę określoną podczas tworzenia środowiska ASE.][3]

Jeśli chcesz po prostu próbować i nie wiesz, jak utworzyć certyfikat, możesz użyć aplikacji konsolowej MMC usług IIS do utworzenia certyfikatu z podpisem własnym. Po jego utworzeniu można wyeksportować go jako plik PFX, a następnie przekazać go w interfejsie użytkownika certyfikatu ILB. Gdy uzyskujesz dostęp do witryny zabezpieczonej przy użyciu certyfikatu z podpisem własnym, przeglądarka wyświetli ostrzeżenie, że dostęp do witryny nie jest bezpieczny z powodu braku możliwości zweryfikowania certyfikatu. Jeśli chcesz uniknąć tego ostrzeżenia, będziesz potrzebować prawidłowo podpisanego certyfikatu, który jest zgodny z poddomeną i ma łańcuch zaufania, który jest rozpoznawany przez przeglądarkę.

![Pokazuje, jak utworzyć certyfikat z podpisem własnym za pomocą aplikacji konsoli MMC usług IIS.][6]

Jeśli chcesz wypróbować przepływ z własnymi certyfikatami i przetestować dostęp do środowiska ASE przy użyciu protokołów HTTP i HTTPS:

1. Przejdź do interfejsu użytkownika środowiska ASE po utworzeniu środowiska ASE **-> ustawień — > certyfikatów ILB**.
2. Ustaw certyfikat ILB, wybierając plik PFX certyfikatu i podaj hasło. Ten krok zajmuje trochę czasu, aż do przetworzenia i pojawi się komunikat, że operacja skalowania jest w toku.
3. Pobierz adres ILB dla właściwości środowiska ASE (środowisko ASE **-> — > wirtualny adres IP**).
4. Utwórz aplikację internetową w środowisku ASE po utworzeniu. 
5. Utwórz maszynę wirtualną, jeśli nie masz jej w tej sieci wirtualnej (nie w tej samej podsieci co środowisko ASE lub uszkodzenie).
6. Ustaw serwer DNS dla domeny podrzędnej. Możesz użyć symbolu wieloznacznego z poddomeną w systemie DNS lub jeśli chcesz wykonać kilka prostych testów, edytuj plik Hosts na maszynie wirtualnej, aby ustawić nazwę aplikacji sieci Web na adres IP adresu VIP. Jeśli środowisko ASE miało nazwę poddomeny. ilbase.com i MojaAplikacja aplikację sieci Web, tak aby była ona w mytestapp.ilbase.com, należy ustawić ją w pliku Hosts. (W systemie Windows plik hosts ma wartość C:\Windows\System32\drivers\etc\)
7. Użyj przeglądarki na tej maszynie wirtualnej i przejdź do witryny `https://mytestapp.ilbase.com` (lub niezależnie od nazwy aplikacji sieci Web z poddomeną).
8. Skorzystaj z przeglądarki na tej maszynie wirtualnej i przejdź na stronę `https://mytestapp.ilbase.com`. Brak zabezpieczeń należy zaakceptować, jeśli jest używany certyfikat z podpisem własnym. 

Adres IP ILB jest wyświetlany na liście właściwości jako wirtualny adres IP.

![Pokazuje, że adres IP ILB jest wyświetlany w właściwościach jako wirtualny adres IP.][4]

## <a name="using-an-ilb-ase"></a>Korzystanie z ILB ASE
#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
ILB ASE umożliwia izolację sieci dla aplikacji. Aplikacje są niedostępne lub nawet znane przez Internet. Takie podejście jest doskonałe do hostowania witryn intranetowych, takich jak aplikacje biznesowe. W przypadku konieczności jeszcze bardziej ograniczonego dostępu można nadal korzystać z sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) w celu kontrolowania dostępu na poziomie sieci. 

Aby dodatkowo ograniczyć dostęp do programu sieciowych grup zabezpieczeń, należy się upewnić, że nie przerywasz komunikacji wymaganej do działania środowiska ASE. Chociaż dostęp do protokołu HTTP/HTTPS odbywa się tylko za pośrednictwem ILB używanego przez środowisko ASE, środowisko ASE nadal zależy od zasobów poza siecią wirtualną. Aby sprawdzić, jaki dostęp do sieci jest nadal wymagany, zobacz [kontrolowanie ruchu przychodzącego do App Service Environment][ControlInbound] i  [szczegóły konfiguracji sieci dla środowisk App Service z ExpressRoute][ExpressRoute]. 

Aby skonfigurować sieciowych grup zabezpieczeń, musisz znać adres IP używany przez platformę Azure do zarządzania środowiskiem ASE. Ten adres IP jest również wychodzącym adresem IP z środowiska ASE, jeśli wykonuje żądania internetowe. Wychodzący adres IP dla środowiska ASE pozostaje statyczny dla cyklu życia środowiska ASE. Jeśli usuniesz i utworzysz ponownie środowisko ASE, otrzymasz nowy adres IP. Aby znaleźć adres IP, przejdź do pozycji **Ustawienia-> właściwości** i Znajdź **wychodzący adres IP**. 

![Pokazuje, gdzie można znaleźć wychodzący adres IP dla środowiska ASE.][5]

#### <a name="general-ilb-ase-management"></a>Ogólne zarządzanie ILB ASE
Zarządzanie ILB ASE jest w dużym stopniu takie samo jak w przypadku normalnego zarządzania środowiskiem ASE. Należy skalować pule procesów roboczych, aby hostować więcej wystąpień ASP i skalować serwery frontonu w celu obsługi zwiększonych ilości ruchu HTTP/HTTPS. Aby uzyskać ogólne informacje na temat zarządzania konfiguracją środowiska ASE, zobacz [konfigurowanie App Service Environment][ASEConfig]. 

Dodatkowe elementy zarządzania to zarządzanie certyfikatami i zarządzanie systemem DNS. Musisz uzyskać i przekazać certyfikat używany do protokołu HTTPS po utworzeniu ILB ASE i zamienić go przed wygaśnięciem. Ponieważ platforma Azure jest właścicielem domeny podstawowej, może udostępnić certyfikaty środowisk ASE z zewnętrznym adresem VIP. Ponieważ poddomena używana przez środowisko ILB ASE może być dowolna, należy podać własny certyfikat dla protokołu HTTPS. 

#### <a name="dns-configuration"></a>Konfiguracja DNS
W przypadku korzystania z zewnętrznego adresu VIP system DNS jest zarządzany przez platformę Azure. Każda aplikacja utworzona w środowisku ASE jest automatycznie dodawana do usługi Azure DNS, która jest publiczną usługą DNS. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia musisz zarządzać własną usługą DNS. Dla danej domeny podrzędnej, takiej jak contoso.corp.net, należy utworzyć rekordy A systemu DNS wskazujące adres ILB dla:

- \*
- *. SCM
- ftp
- publish

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z App Service środowiskami, zobacz [wprowadzenie do App Service środowisk][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md