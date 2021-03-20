---
title: Zagadnienia dotyczące pracy w sieci
description: Dowiedz się więcej o ruchu sieciowym ASE oraz o sposobie ustawiania sieciowych grup zabezpieczeń i tras zdefiniowanych przez użytkownika w środowisku ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 07/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 91b6134e7c809a8af75aa1cf23523e352e0a1a0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997345"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment) #

## <a name="overview"></a>Omówienie ##

 Usługa Azure [App Service Environment][Intro] to wdrożenie Azure App Service w podsieci w sieci wirtualnej platformy Azure. Istnieją dwa typy wdrożeń dla środowiska App Serviceowego (ASE):

- **Zewnętrzne środowisko ASE**: uwidacznia aplikacje hostowane na internetowym adresie IP. Aby uzyskać więcej informacji, zobacz [Tworzenie zewnętrznego środowiska ASE][MakeExternalASE].
- **ILB ASE**: uwidacznia aplikacje hostowane na adresie IP w sieci wirtualnej. Wewnętrzny punkt końcowy jest wewnętrznym modułem równoważenia obciążenia (ILB), dlatego jest on nazywany ILB ASE. Aby uzyskać więcej informacji, zobacz [Tworzenie i używanie środowiska ILB ASE][MakeILBASE].

Wszystkie środowisk ASE, zewnętrzne i ILB mają publiczny adres VIP używany przez ruch przychodzący zarządzania i jako adres od podczas wykonywania wywołań z programu ASE do Internetu. Wywołania z programu ASE, które przechodzą do Internetu, opuszczają sieć wirtualną za pośrednictwem adresu VIP przypisanego do środowiska ASE. Publiczny adres IP tego adresu VIP jest źródłowym adresem IP dla wszystkich wywołań z programu ASE, które przechodzą do Internetu. Jeśli aplikacje w środowisku ASE powodują wywoływanie zasobów w sieci wirtualnej lub przez sieć VPN, źródłowy adres IP jest jednym z adresów IP w podsieci używanej przez środowisko ASE. Ze względu na to, że środowisko ASE znajduje się w sieci wirtualnej, może również uzyskać dostęp do zasobów w sieci wirtualnej bez żadnej dodatkowej konfiguracji. Jeśli sieć wirtualna jest połączona z siecią lokalną, aplikacje w środowisku ASE mają również dostęp do zasobów bez dodatkowej konfiguracji.

![Zewnętrzne środowisko ASE][1] 

Jeśli masz zewnętrzny ASE, publiczny adres VIP jest również punktem końcowym, do którego są rozpoznawane aplikacje środowiska ASE:

* HTTP/S 
* FTP/S
* Wdrażanie w sieci Web
* Debugowanie zdalne

![ILB ASE][2]

Jeśli masz ILB ASE, adres ILB jest punktem końcowym dla protokołów HTTP/S, FTP/S, wdrażania w sieci Web i zdalnego debugowania.

## <a name="ase-subnet-size"></a>Rozmiar podsieci środowiska ASE ##

Nie można zmienić rozmiaru podsieci używanej do hostowania środowiska ASE po wdrożeniu środowiska ASE.  Środowisko ASE używa adresu dla każdej roli infrastruktury, a także dla każdego izolowanego wystąpienia planu App Service.  Ponadto istnieje pięć adresów używanych przez sieć Azure dla każdej tworzonej podsieci.  Przed utworzeniem aplikacji środowisko ASE bez żadnych planów App Service będzie używać 12 adresów.  Jeśli jest to ILB ASE, będzie ona używać 13 adresów przed utworzeniem aplikacji w tym środowisku ASE. W miarę skalowania środowiska ASE role infrastruktury są dodawane każda wielokrotność 15 i 20 wystąpień planu App Service.

   > [!NOTE]
   > Żadne inne elementy nie mogą znajdować się w podsieci, ale w środowisku ASE. Upewnij się, że wybrano przestrzeń adresową, która umożliwia przyszłość wzrostu. Tego ustawienia nie można zmienić później. Zalecamy rozmiar `/24` z 256 adresami.

W przypadku skalowania w górę lub w dół nowe role o odpowiednim rozmiarze są dodawane, a następnie obciążenia są migrowane z bieżącego rozmiaru do rozmiaru docelowego. Oryginalne maszyny wirtualne zostały usunięte dopiero po migracji obciążeń. Jeśli masz wystąpienia ASE z 100 ASP, będzie to okres, w którym potrzebna jest podwójna liczba maszyn wirtualnych.  Z tego powodu zalecamy użycie "/24" w celu uwzględnienia wszelkich wymaganych zmian.  

## <a name="ase-dependencies"></a>Zależności środowiska ASE ##

### <a name="ase-inbound-dependencies"></a>Zależności przychodzące środowiska ASE ###

Aby środowisko ASE mogło działać, środowisko ASE wymaga otwarcia następujących portów:

| Zastosowanie | Źródło | Działanie |
|-----|------|----|
| Zarządzanie | Adresy zarządzania App Service | Podsieć środowiska ASE: 454, 455 |
|  Komunikacja wewnętrzna ASE | Podsieć środowiska ASE: wszystkie porty | Podsieć środowiska ASE: wszystkie porty
|  Zezwalaj na ruch przychodzący modułu równoważenia obciążenia platformy Azure | Moduł równoważenia obciążenia platformy Azure | Podsieć środowiska ASE: 16001

Istnieją 2 inne porty, które mogą być wyświetlane jako otwarte przy skanowaniu portów, 7654 i 1221. Odpowiadają one za adres IP i nic nie rób. W razie potrzeby można je zablokować. 

Ruch przychodzący zarządzania udostępnia polecenie i kontrolę środowiska ASE oprócz monitorowania systemu. Adresy źródłowe dla tego ruchu są wymienione w dokumencie [Zarządzanie środowiska ASE][ASEManagement] . Konfiguracja zabezpieczeń sieci musi zezwalać na dostęp z adresów administracyjnych środowiska ASE na portach 454 i 455. Jeśli zablokujesz dostęp z tych adresów, środowisko ASE stanie się w złej kondycji, a następnie stanie się zawieszone. Ruch TCP, który znajduje się na portach 454 i 455 musi wycofać się z tego samego adresu VIP lub wystąpił problem z routingiem asymetrycznym. 

W podsieci ASE istnieje wiele portów używanych do komunikacji między składnikami wewnętrznymi i ich zmiana. Wymaga to dostępności wszystkich portów w podsieci środowiska ASE z podsieci środowiska ASE. 

W przypadku komunikacji między modułem równoważenia obciążenia platformy Azure i podsiecią środowiska ASE minimalna ilość wymaganych portów to 454, 455 i 16001. Port 16001 jest używany do utrzymywania ruchu między modułem równoważenia obciążenia i środowiskiem ASE. Jeśli używasz środowiska ILB ASE, możesz zablokować ruch w dół do tylko 454, 455, 16001 portów.  W przypadku korzystania z zewnętrznego środowiska ASE należy wziąć pod uwagę normalne porty dostępu do aplikacji.  

Inne porty, z którymi należy się zapoznać, to porty aplikacji:

| Zastosowanie | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Zdalne debugowanie programu Visual Studio  |  4020, 4022, 4024 |
|  Usługa Web Deploy | 8172 |

W przypadku zablokowania portów aplikacji środowisko ASE może nadal działać, ale aplikacja może nie.  Jeśli używasz adresów IP przypisanych do aplikacji z zewnętrznym środowiskiem ASE, musisz zezwolić na ruch z adresów IP przypisanych do aplikacji do podsieci środowiska ASE na portach pokazanych w portalu ASE > stronie adresy IP.

### <a name="ase-outbound-dependencies"></a>Zależności wychodzące środowiska ASE ###

W przypadku dostępu wychodzącego środowisko ASE jest zależne od wielu systemów zewnętrznych. Wiele z tych zależności systemowych jest definiowanych za pomocą nazw DNS i nie są mapowane na stały zestaw adresów IP. W tym celu środowisko ASE wymaga dostępu wychodzącego z podsieci środowiska ASE do wszystkich zewnętrznych adresów IP na różnych portach. 

Środowisko ASE komunikuje się z dostępnymi adresami internetowymi na następujących portach:

| Użycie | Porty |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| Lista CRL, aktualizacje systemu Windows, zależności Linux, usługi platformy Azure | 80/443 |
| Azure SQL | 1433 | 
| Monitorowanie | 12000 |

Zależności wychodzące są wymienione w dokumencie, który opisuje [blokowanie App Service Environment ruchu wychodzącego](./firewall-integration.md). Jeśli środowisko ASE utraci dostęp do jego zależności, przestanie działać. Gdy ma to miejsce wystarczająco długo, środowisko ASE jest zawieszone. 

### <a name="customer-dns"></a>Klient DNS ###

Jeśli sieć wirtualna jest skonfigurowana z serwerem DNS zdefiniowanym przez klienta, obciążenia dzierżawców używają go. Środowisko ASE używa Azure DNS do celów zarządzania. W przypadku skonfigurowania sieci wirtualnej z serwerem DNS wybranym przez klienta serwer DNS musi być dostępny z podsieci zawierającej środowisko ASE.

Aby przetestować rozpoznawanie nazw DNS z poziomu aplikacji sieci Web, możesz użyć polecenia konsoli *nameresolver*. Przejdź do okna debugowania w witrynie SCM dla swojej aplikacji lub przejdź do aplikacji w portalu i wybierz pozycję konsola. W wierszu powłoki programu można wydać polecenie *nameresolver* wraz z nazwą DNS, którą chcesz wyszukać. Wynik, który powrócisz, jest taki sam jak w przypadku aplikacji podczas wykonywania tego samego wyszukiwania. Jeśli używasz polecenia nslookup, będziesz wykonywać wyszukiwanie przy użyciu Azure DNS zamiast tego.

W przypadku zmiany ustawienia DNS sieci wirtualnej, w której znajduje się środowisko ASE, należy ponownie uruchomić środowisko ASE. Aby uniknąć ponownego uruchomienia środowiska ASE, zdecydowanie zaleca się skonfigurowanie ustawień DNS dla sieci wirtualnej przed utworzeniem środowiska ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Zależności portalu ##

Oprócz zależności funkcjonalnych, istnieje kilka dodatkowych elementów związanych z korzystaniem z portalu. Niektóre funkcje w Azure Portal zależą od bezpośredniego dostępu do _witryny SCM_. Dla każdej aplikacji w Azure App Service istnieją dwa adresy URL. Pierwszy adres URL ma na celu uzyskanie dostępu do aplikacji. Drugim adresem URL jest dostęp do witryny SCM, która jest również nazywana _konsolą kudu_. Do funkcji korzystających z witryny SCM należą:

-   Zadania WebJob
-   Funkcje
-   Przesyłanie strumieniowe dzienników
-   Kudu
-   Rozszerzenia
-   Eksplorator procesów
-   Konsola

W przypadku korzystania z ILB ASE witryna SCM nie jest dostępna spoza sieci wirtualnej. Niektóre funkcje nie będą działały w portalu aplikacji, ponieważ wymagają dostępu do witryny SCM aplikacji. Możesz połączyć się bezpośrednio z witryną SCM, zamiast korzystać z portalu. 

Jeśli ILB ASE jest nazwą domeny *contoso.appserviceenvironment.NET* , a nazwa aplikacji to *TestApp*, aplikacja zostanie osiągnięta na *TestApp.contoso.appserviceenvironment.NET*. Lokacja SCM, do której się odnosi, została osiągnięta pod adresem *TestApp.SCM.contoso.appserviceenvironment.NET*.

## <a name="ase-ip-addresses"></a>Adresy IP ASE ##

Środowisko ASE zawiera kilka adresów IP, z którymi należy się zapoznać. Są to:

- **Publiczny adres IP ruchu przychodzącego**: używany dla ruchu aplikacji w zewnętrznym środowisku ASE i ruch związany z zarządzaniem zarówno w zewnętrznym środowisku ASE, jak i w środowisku ILB ASE.
- **Wychodzący publiczny adres IP**: używany jako adres IP "od" dla połączeń wychodzących z środowiska ASE, które opuszczają sieć wirtualną, która nie jest przekierowywana do sieci VPN.
- **ILB adres IP**: adres IP ILB istnieje tylko w ILB ASE.
- **Adresy SSL oparte na aplikacjach przypisane do aplikacji**: możliwe tylko z zewnętrznym środowiskiem ASE i SKONFIGUROWANYm protokołem SSL opartym na protokole IP.

Wszystkie te adresy IP są widoczne w Azure Portal z interfejsu użytkownika środowiska ASE. Jeśli masz ILB ASE, zostanie wyświetlony adres IP dla ILB.

   > [!NOTE]
   > Te adresy IP nie zmienią się, dopóki środowisko ASE zacznie działać.  Jeśli środowisko ASE zostanie zawieszone i przywrócone, adresy używane przez środowisko ASE zmienią się. Normalna przyczyna wstrzymania środowiska ASE jest blokowana w przypadku zablokowania dostępu do zarządzania ruchem przychodzącym lub blokowania dostępu do zależności środowiska ASE. 

![Adresy IP][3]

### <a name="app-assigned-ip-addresses"></a>Adresy IP przypisane do aplikacji ###

Przy użyciu zewnętrznego środowiska ASE można przypisywać adresy IP do poszczególnych aplikacji. Nie można tego zrobić za pomocą ILB ASE. Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji w celu posiadania własnego adresu IP, zobacz [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](../configure-ssl-bindings.md).

Gdy aplikacja ma swój własny adres SSL oparty na protokole IP, środowisko ASE rezerwuje dwa porty do mapowania na ten adres IP. Jeden port jest przeznaczony dla ruchu HTTP, a drugi port jest przeznaczony dla protokołu HTTPS. Porty te są wymienione w interfejsie użytkownika środowiska ASE w sekcji adresy IP. Ruch musi być w stanie połączyć się z tymi portami z adresu VIP lub aplikacje są niedostępne. Ten wymóg należy pamiętać podczas konfigurowania sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń).

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci ##

[Sieciowe grupy zabezpieczeń][NSGs] umożliwiają kontrolowanie dostępu do sieci w ramach połączenia sieciowego. Gdy używasz portalu, istnieje niejawna reguła odmowy o najniższym priorytecie, aby odmówić wszystkiego. To, co tworzysz, są reguły zezwalania.

W środowisku ASE nie masz dostępu do maszyn wirtualnych używanych do hostowania środowiska ASE. Są one w ramach subskrypcji zarządzanej przez firmę Microsoft. Jeśli chcesz ograniczyć dostęp do aplikacji w środowisku ASE, ustaw sieciowych grup zabezpieczeń w podsieci środowiska ASE. W tym celu należy zwrócić szczególną uwagę na zależności środowiska ASE. Jeśli zablokujesz jakiekolwiek zależności, środowisko ASE przestanie działać.

Sieciowych grup zabezpieczeń można skonfigurować za pomocą Azure Portal lub za pośrednictwem programu PowerShell. W tym miejscu znajdują się informacje o Azure Portal. Możesz tworzyć sieciowych grup zabezpieczeń w portalu i zarządzać nimi jako zasób najwyższego poziomu w obszarze **Sieć**.

Wymagane wpisy w sieciowej grupy zabezpieczeń, dla których środowisko ASE ma działać, mają na celu Zezwalanie na ruch:

**Przychodzący**
* TCP ze znacznika usługi IP AppServiceManagement na portach 454 455
* TCP z modułu równoważenia obciążenia na porcie 16001
* z podsieci środowiska ASE do podsieci środowiska ASE na wszystkich portach

**Wychodzący**
* UDP do wszystkich adresów IP na porcie 53
* UDP do wszystkich adresów IP na porcie 123
* TCP do wszystkich adresów IP na portach 80, 443
* TCP do tagu usługi IP `Sql` na portach 1433
* TCP na wszystkie adresy IP na porcie 12000
* do podsieci środowiska ASE na wszystkich portach

Te porty nie obejmują portów wymaganych przez aplikacje do pomyślnego użycia. Na przykład aplikacja może wymagać wywołania serwera MySQL na porcie 3306. Protokół NTP (Network Time Protocol) na porcie 123 to protokół synchronizacji czasu używany przez system operacyjny. Punkty końcowe NTP nie są specyficzne dla App Services, mogą się różnić w zależności od systemu operacyjnego i nie znajdują się w dobrze zdefiniowanej liście adresów. Aby zapobiec problemom z synchronizacją czasu, należy zezwolić na ruch UDP do wszystkich adresów na porcie 123. Ruch wychodzący TCP do portu 12000 jest przeznaczony do obsługi i analizy systemu. Punkty końcowe są dynamiczne i nie znajdują się w dobrze zdefiniowanym zestawie adresów.

Normalne porty dostępu do aplikacji są następujące:

| Zastosowanie | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Zdalne debugowanie programu Visual Studio  |  4020, 4022, 4024 |
|  Usługa Web Deploy | 8172 |

W przypadku uwzględnienia wymagań dotyczących ruchu przychodzącego i wychodzącego sieciowych grup zabezpieczeń powinny wyglądać podobnie do sieciowych grup zabezpieczeń, jak pokazano w tym przykładzie. 

![Reguły zabezpieczeń dla ruchu przychodzącego][4]

Reguła domyślna umożliwia komunikację między adresami IP w sieci wirtualnej w podsieci środowiska ASE. Inna domyślna reguła umożliwia modułowi równoważenia obciążenia, znanym również jako publiczny adres VIP, komunikowanie się ze środowiskiem ASE. Aby wyświetlić reguły domyślne, wybierz opcję **domyślne reguły** obok ikony **Dodaj** . Jeśli przed regułami domyślnymi zostanie umieszczona reguła Odmów wszystkiego innego, zapobiegasz ruchu między wirtualnym adresem IP a środowiskiem ASE. Aby zapobiec ruchowi pochodzącemu z sieci wirtualnej, Dodaj własną regułę, aby zezwolić na przychodzący. Użyj źródła równego AzureLoadBalancer z miejscem docelowym **dowolnego** i zakresu portów **\*** . Ponieważ reguła sieciowej grupy zabezpieczeń jest stosowana do podsieci środowiska ASE, nie musisz być konkretna w miejscu docelowym.

Jeśli do aplikacji został przypisany adres IP, upewnij się, że porty są otwarte. Aby wyświetlić porty, wybierz pozycję **App Service Environment**  >  **adresy IP**.  

Wszystkie elementy wyświetlane w następujących regułach wychodzących są zbędne, z wyjątkiem ostatniego elementu. Umożliwiają one dostęp sieciowy do zależności środowiska ASE, które zostały zanotowane we wcześniejszej części tego artykułu. Jeśli zablokujesz dowolne z nich, środowisko ASE przestanie działać. Ostatni element na liście umożliwia firmie ASE komunikowanie się z innymi zasobami w sieci wirtualnej.

![Reguły zabezpieczeń dla ruchu wychodzącego][5]

Po zdefiniowaniu sieciowych grup zabezpieczeń Przypisz je do podsieci, w której znajduje się środowisko ASE. Jeśli nie pamiętasz sieci wirtualnej lub podsieci środowiska ASE, możesz ją zobaczyć na stronie portalu środowiska ASE. Aby przypisać sieciowej grupy zabezpieczeń do podsieci, przejdź do interfejsu użytkownika podsieci i wybierz sieciowej grupy zabezpieczeń.

## <a name="routes"></a>Trasy ##

Wymuszone tunelowanie polega na ustawieniu tras w sieci wirtualnej, tak aby ruch wychodzący nie przechodził bezpośrednio do Internetu, ale w innych, takich jak Brama ExpressRoute lub urządzenie wirtualne.  Jeśli musisz skonfigurować środowisko ASE w taki sposób, Przeczytaj dokument dotyczący [konfigurowania App Service Environment przy użyciu wymuszonego tunelowania][forcedtunnel].  Ten dokument zawiera informacje o opcjach dostępnych do pracy z ExpressRoute i wymuszonym tunelowaniem.

Po utworzeniu środowiska ASE w portalu utworzymy również zestaw tabel tras w podsieci, która jest tworzona przy użyciu środowiska ASE.  Trasy te po prostu wysyłają ruch wychodzący bezpośrednio do Internetu.  
Aby ręcznie utworzyć te same trasy, wykonaj następujące czynności:

1. Przejdź do witryny Azure Portal. Wybierz pozycję **Sieć**  >  **tabele tras**.

2. Utwórz nową tabelę tras w tym samym regionie, w którym znajduje się sieć wirtualna.

3. Z poziomu interfejsu użytkownika tabeli tras wybierz pozycję **trasy**  >  **Dodaj**.

4. Ustaw **Typ następnego przeskoku** na **Internet** i **prefiks adresu** **0.0.0.0/0**. Wybierz pozycję **Zapisz**.

    Następnie zobaczysz coś podobne do następujących:

    ![Trasy funkcjonalne][6]

5. Po utworzeniu nowej tabeli tras przejdź do podsieci zawierającej środowisko ASE. Wybierz tabelę tras z listy w portalu. Po zapisaniu zmiany należy zobaczyć sieciowych grup zabezpieczeń i trasy zanotowane w podsieci.

    ![Sieciowych grup zabezpieczeń i trasy][7]

## <a name="service-endpoints"></a>Punkty końcowe usługi ##

Punkty końcowe usługi pozwalają ograniczyć dostęp do usług wielodostępnych do zestawu sieci wirtualnych i podsieci platformy Azure. Więcej informacji na temat punktów końcowych usługi zawiera dokumentacja [punktów końcowych usługi dla sieci wirtualnej][serviceendpoints]. 

Po włączeniu punktów końcowych usługi w zasobie niektóre trasy są utworzone z wyższym priorytetem niż wszystkie inne trasy. W przypadku korzystania z punktów końcowych usługi w dowolnej usłudze platformy Azure z wymuszonym tunelowanym środowiskiem ASE ruch do tych usług nie zostanie wymuszony. 

Kiedy punkty końcowe usługi są włączone w podsieci z wystąpieniem usługi Azure SQL, wszystkie wystąpienia usługi Azure SQL połączone z tej podsieci muszą mieć włączone punkty końcowe usługi. Jeśli chcesz uzyskać dostęp do wielu wystąpień usługi Azure SQL z tej samej podsieci, musisz włączyć punkty końcowe usługi na każdym wystąpieniu usługi Azure SQL. Żadna inna usługa Azure nie działa jak Azure SQL w odniesieniu do punktów końcowych usługi. Włączenie punktów końcowych usługi w usłudze Azure Storage powoduje zablokowanie dostępu do tego zasobu z podsieci, ale można nadal uzyskiwać dostęp do innych kont usługi Azure Storage, nawet jeśli nie mają włączonych punktów końcowych usługi.  

![Punkty końcowe usługi][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md