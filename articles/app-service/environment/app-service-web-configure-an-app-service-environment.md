---
title: Konfigurowanie środowiska ASE v1
description: Konfiguracja, zarządzanie i monitorowanie App Service Environment v1. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 598e43d07c213cfeb25f0ecbc7bd02b6ec54b7ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962591"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurowanie App Service Environment v1

> [!NOTE]
> Ten artykuł dotyczy App Service Environment v1.  Istnieje nowsza wersja App Service Environment ułatwiająca korzystanie z bardziej wydajną infrastrukturą. Aby dowiedzieć się więcej o nowej wersji, Zacznij od [wprowadzenia do App Service Environment](intro.md).
> 

## <a name="overview"></a>Omówienie
Na wysokim poziomie Azure App Service Environment składa się z kilku głównych składników:

* Zasoby obliczeniowe, które są uruchomione w App Service Environment hostowanej usłudze
* Storage
* Baza danych programu
* Klasyczny (v1) lub Menedżer zasobów (wersja 2) platformy Virtual Network Azure (Sieć wirtualna) 
* Podsieć z uruchomioną App Service Environment usługą hostowaną

### <a name="compute-resources"></a>Zasoby obliczeniowe
Zasoby obliczeniowe są używane dla czterech pul zasobów.  Każdy App Service Environment (ASE) ma zestaw frontonów i trzy możliwe pule procesów roboczych. Nie musisz używać wszystkich trzech pul procesów roboczych — Jeśli chcesz, możesz użyć jednej lub dwóch.

Hosty w pulach zasobów (frontony i procesy robocze) nie są bezpośrednio dostępne dla dzierżawców. Nie można użyć Remote Desktop Protocol (RDP), aby połączyć się z nimi, zmienić ich aprowizacji lub działać jako administrator.

Można ustawić ilość i rozmiar puli zasobów. W środowisku ASE są dostępne cztery opcje rozmiaru, które są oznaczone jako P1 przez P4. Aby uzyskać szczegółowe informacje o tych rozmiarach i ich cenach, zobacz [Cennik usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).
Zmiana ilości lub rozmiaru jest nazywana operacją skalowania.  W danym momencie może istnieć tylko jedna operacja skalowania.

**Frontony**: frontony są punktami końcowymi protokołu HTTP/HTTPS dla aplikacji, które są przechowywane w środowisku ASE. Nie uruchamiasz obciążeń w frontonie.

* Środowisko ASE rozpoczyna się od dwóch P2S, co jest wystarczające do obciążeń deweloperskich i testowych oraz obciążeń produkcyjnych niskiego poziomu. Zdecydowanie zalecamy P3s dla średnich i dużych obciążeń produkcyjnych.
* W przypadku średnich i dużych obciążeń produkcyjnych zaleca się, aby mieć co najmniej cztery P3s w celu zapewnienia wystarczającej liczby frontonów uruchomionych w przypadku wystąpienia zaplanowanej konserwacji. Zaplanowane działania konserwacyjne spowodują przełączenie jednego frontonu w danym momencie. Zmniejsza to ogólną dostępną pojemność frontonu podczas aktywności konserwacyjnej.
* Zainicjowanie frontonu może potrwać do godziny. 
* Aby kontynuować skalowanie w poziomie, należy monitorować wartości procentowe procesora CPU, procentu pamięci i aktywnych żądań dla puli frontonu. Jeśli wartości procentowe procesora CPU lub pamięci są powyżej 70% podczas uruchamiania P3s, Dodaj więcej frontonów. Jeśli wartość aktywnych żądań jest średnia na 15 000 do 20 000 żądań na fronton, należy również dodać więcej frontonów. Ogólnym celem jest utrzymywanie wartości procentowych procesora CPU i pamięci poniżej 70%, a aktywne żądania są uśredniane do poniżej 15 000 żądań na fronton, gdy korzystasz z usługi P3s.  

**Procesy robocze**: pracownicy są miejscem, w którym działają Twoje aplikacje. W przypadku skalowania w górę planów App Service, które używają procesów roboczych w puli skojarzonej z nimi.

* Nie można natychmiast dodać procesów roboczych. Udostępnienie może potrwać do godziny.
* Skalowanie rozmiaru zasobów obliczeniowych dla dowolnej puli zajmie < 1 godzinę dla domeny aktualizacji. W środowisku ASE znajdują się 20 domen aktualizacji. W przypadku skalowania rozmiaru obliczeniowego puli procesów roboczych z 10 wystąpieniami ukończenie tego procesu może potrwać do 10 godzin.
* Zmiana rozmiaru zasobów obliczeniowych używanych w puli procesów roboczych spowoduje zimne uruchomienie aplikacji, które są uruchomione w tej puli procesów roboczych.

Najszybszym sposobem zmiany rozmiaru zasobów obliczeniowych puli procesów roboczych, w których nie działa żadna aplikacja, jest:

* Przeskaluj w dół liczbę procesów roboczych do 2.  Minimalna wielkość skalowania w dół w portalu wynosi 2. Cofnięcie przydziału wystąpień potrwa kilka minut. 
* Wybierz nowy rozmiar i liczbę wystąpień obliczeniowych. Z tego miejsca zajmiemy do 2 godzin.

Jeśli aplikacje wymagają większego rozmiaru zasobów obliczeniowych, nie można korzystać z wcześniejszych wskazówek. Zamiast zmieniać rozmiar puli procesów roboczych, która obsługuje te aplikacje, można wypełnić inną pulę procesów roboczych pracownikami o żądanym rozmiarze i przenieść aplikacje do tej puli.

* Utwórz dodatkowe wystąpienia wymaganego rozmiaru obliczeniowego w innej puli procesów roboczych. Ukończenie tej czynności może potrwać do godziny.
* Przypisz ponownie plany App Service, które obsługują aplikacje, które mają większy rozmiar, do nowo skonfigurowanej puli procesów roboczych. Jest to szybka operacja, którą należy wykonać krócej niż minutę.  
* Skalowanie w dół pierwszej puli procesów roboczych, jeśli nie są już potrzebne nieużywane wystąpienia. Wykonanie tej operacji może potrwać kilka minut.

**Skalowanie** automatyczne: jeden z narzędzi, które ułatwiają zarządzanie użyciem zasobów obliczeniowych, jest skalowaniem automatycznym. Skalowanie automatyczne można używać w przypadku pul frontonu lub procesów roboczych. Można wykonywać takie czynności, jak zwiększenie liczby wystąpień dowolnego typu puli rano i zmniejszenie jej w wieczór. Możesz też dodać wystąpienia, gdy liczba procesów roboczych, które są dostępne w puli procesu roboczego, spadnie poniżej określonego progu.

Jeśli chcesz ustawić reguły skalowania automatycznego wokół metryk puli zasobów obliczeniowych, należy pamiętać o czasie, który jest wymagany. Aby uzyskać więcej informacji na temat automatycznego skalowania środowisk App Service, zobacz [jak skonfigurować Skalowanie automatyczne w App Service Environment][ASEAutoscale].

### <a name="storage"></a>Storage
Każde środowisko ASE jest skonfigurowane z 500 GB miejsca w magazynie. To miejsce jest używane we wszystkich aplikacjach w środowisku ASE. To miejsce do magazynowania jest częścią środowiska ASE i obecnie nie można go przełączyć do korzystania z miejsca do magazynowania. W przypadku wprowadzania zmian dotyczących routingu lub zabezpieczeń sieci wirtualnej należy nadal zezwolić na dostęp do usługi Azure Storage — lub środowisko ASE nie może działać.

### <a name="database"></a>baza danych
Baza danych zawiera informacje, które definiują środowisko, a także szczegółowe informacje o aplikacjach, które są w nim uruchomione. Jest to również część subskrypcji przechowywanej na platformie Azure. Nie jest to coś, że masz bezpośrednią zdolność do manipulowania. W przypadku wprowadzania zmian dotyczących routingu lub zabezpieczeń sieci wirtualnej należy nadal zezwolić na dostęp do usługi SQL Azure — lub środowisko ASE nie może działać.

### <a name="network"></a>Sieć
Sieć wirtualna, która jest używana z środowiskiem ASE, może być taka, która została utworzona podczas tworzenia środowiska ASE lub jednej wcześniej. Podczas tworzenia podsieci podczas tworzenia środowiska ASE środowisko ASE jest w tej samej grupie zasobów co sieć wirtualna. Jeśli potrzebujesz, aby grupa zasobów używana przez środowisko ASE była inna niż sieć wirtualna, należy utworzyć środowisko ASE przy użyciu szablonu usługi Resource Manager.

Istnieją pewne ograniczenia dotyczące sieci wirtualnej, która jest używana w środowisku ASE:

* Sieć wirtualna musi być regionalną siecią wirtualną.
* Musi być podsiecią zawierającą co najmniej 8 adresów, w których wdrożono środowisko ASE.
* Gdy podsieć jest używana do hostowania środowiska ASE, nie można zmienić zakresu adresów podsieci. Z tego powodu zaleca się, aby podsieć zawierała co najmniej 64 adresów w celu uwzględnienia przyszłego wzrostu środowiska ASE.
* W podsieci nie mogą znajdować się żadne inne elementy, ale środowisko ASE.

W przeciwieństwie do usługi hostowanej zawierającej środowisko ASE, [Sieć wirtualna][virtualnetwork] i podsieć są pod kontrolą użytkownika.  Siecią wirtualną można administrować za pomocą interfejsu użytkownika Virtual Network lub programu PowerShell.  Środowisko ASE można wdrożyć w środowisku klasycznym lub Menedżer zasobów sieci wirtualnej.  Środowisko portalu i interfejsu API są nieco inne niż klasyczne i Menedżer zasobów sieci wirtualnych, ale środowisko ASE jest takie samo.

Sieć wirtualna, która jest używana do hostowania środowiska ASE, może używać prywatnych adresów IP RFC1918 lub używać publicznych adresów IP.  Jeśli chcesz użyć zakresu adresów IP, który nie jest objęty przez RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), należy utworzyć sieć wirtualną i podsieci, które będą używane przez środowisko ASE przed tworzeniem środowiska ASE.

Ponieważ ta funkcja umieszcza Azure App Service w sieci wirtualnej, oznacza to, że aplikacje hostowane w środowisku ASE mogą teraz uzyskiwać dostęp do zasobów udostępnionych za pośrednictwem ExpressRoute lub bezpośrednio wirtualnych sieci prywatnych (VPN) typu lokacja-lokacja. Aplikacje znajdujące się w App Service Environment nie wymagają dodatkowych funkcji sieciowych w celu uzyskania dostępu do zasobów dostępnych dla sieci wirtualnej, która obsługuje App Service Environment. Oznacza to, że nie trzeba używać Integracja z siecią wirtualną ani Połączenia hybrydowe do uzyskiwania zasobów w sieci wirtualnej lub połączenia z nią. Można nadal używać obu tych funkcji, aby uzyskać dostęp do zasobów w sieciach, które nie są połączone z siecią wirtualną.

Można na przykład użyć usługi Integracja z siecią wirtualną do integracji z siecią wirtualną, która znajduje się w subskrypcji, ale nie jest połączona z siecią wirtualną, w której znajduje się środowisko ASE. Nadal można także używać Połączenia hybrydowe do uzyskiwania dostępu do zasobów, które znajdują się w innych sieciach, podobnie jak zwykle.  

Jeśli masz skonfigurowaną sieć wirtualną przy użyciu sieci VPN ExpressRoute, należy pamiętać, że niektóre z nich są wymagane. Istnieją konfiguracje tras zdefiniowanych przez użytkownika (UDR), które są niezgodne ze środowiskiem ASE. Aby uzyskać więcej informacji na temat uruchamiania środowiska ASE w sieci wirtualnej za pomocą ExpressRoute, zobacz [uruchamianie App Service Environment w sieci wirtualnej z ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Zabezpieczanie ruchu przychodzącego
Istnieją dwie podstawowe metody sterowania ruchem przychodzącym do środowiska ASE.  Za pomocą sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) można kontrolować adresy IP, które mogą uzyskiwać dostęp do środowiska ASE, zgodnie [z opisem w temacie Jak kontrolować ruch przychodzący w App Service Environment](app-service-app-service-environment-control-inbound-traffic.md) i można również skonfigurować środowisko ASE przy użyciu wewnętrznego Load BALANCER (ILB).  Te funkcje mogą być również używane razem, jeśli chcesz ograniczyć dostęp za pomocą sieciowych grup zabezpieczeń do ILB ASE.

Podczas tworzenia środowiska ASE zostanie utworzony adres VIP w sieci wirtualnej.  Istnieją dwa typy adresów VIP — zewnętrzna i wewnętrzna.  Po utworzeniu środowiska ASE z zewnętrznym adresem VIP aplikacje w środowisku ASE będą dostępne za pośrednictwem adresu IP z obsługą Internetu. Po wybraniu opcji wewnętrzne środowisko ASE zostanie skonfigurowane z ILB i nie będzie bezpośrednio dostępne w Internecie.  ILB ASE nadal wymaga zewnętrznego adresu VIP, ale jest on używany tylko do zarządzania platformą Azure i dostępu do konserwacji.  

Podczas tworzenia ILB środowiska ASE podano poddomenę używaną przez środowisko ILB ASE i będzie on musiał zarządzać własnym systemem DNS dla określonej domeny podrzędnej.  Ponieważ należy ustawić nazwę domeny podrzędnej, należy również zarządzać certyfikatem używanym do dostępu HTTPS.  Po utworzeniu środowiska ASE zostanie wyświetlony monit o podanie certyfikatu.  Aby dowiedzieć się więcej o tworzeniu i korzystaniu z ILB ASE do odczytu [przy użyciu wewnętrznego Load Balancer z App Service Environment][ILBASE]. 

## <a name="portal"></a>Portal
App Service Environment można zarządzać i monitorować za pomocą interfejsu użytkownika w Azure Portal. Jeśli masz środowisko ASE, zobaczysz symbol App Service na pasku bocznym. Ten symbol jest używany do reprezentowania środowisk App Service w Azure Portal:

![Symbol App Service Environment][1]

Aby otworzyć interfejs użytkownika, który zawiera listę wszystkich środowisk App Service, możesz użyć ikony lub wybrać cudzysłów ostrokątny (">") w dolnej części paska bocznego, aby wybrać App Service środowiska. Wybierając jeden z środowisk ASE na liście, należy otworzyć interfejs użytkownika, który jest używany do monitorowania i zarządzania nim.

![Interfejs użytkownika do monitorowania App Service Environment i zarządzania nim][2]

Ten pierwszy blok przedstawia niektóre właściwości środowiska ASE oraz wykres metryki dla puli zasobów. Niektóre właściwości, które są wyświetlane w bloku **podstawy** , są również hiperłączami, które spowodują otwarcie bloku, który jest skojarzony z nim. Na przykład możesz wybrać nazwę **Virtual Network** , aby otworzyć interfejs użytkownika skojarzony z siecią wirtualną, w której działa środowisko ASE. **App Service plany** i **aplikacje** każda z nich otwiera listę tych elementów, które znajdują się w środowisku ASE.  

### <a name="monitoring"></a>Monitorowanie
Na wykresach można zobaczyć różne metryki wydajności w każdej puli zasobów. Dla puli frontonu można monitorować średni procesor CPU i pamięć. W przypadku pul procesów roboczych można monitorować użytą ilość i ilość, która jest dostępna.

Wiele planów App Service może korzystać z pracowników w puli procesów roboczych. Obciążenie nie jest dystrybuowane w taki sam sposób, jak w przypadku serwerów frontonu, dlatego użycie procesora i pamięci nie zapewnia znacznie możliwości użytecznych informacji. Jest to ważniejsze śledzenie liczby używanych i dostępnych procesów roboczych — szczególnie w przypadku zarządzania tym systemem przez inne osoby.  

Możesz również użyć wszystkich metryk, które mogą być śledzone na wykresach w celu skonfigurowania alertów. Konfigurowanie alertów w tym miejscu działa tak samo, jak w obszarze App Service. Można ustawić alert z części interfejsu użytkownika **alertów** lub przechodzenie do szczegółów dowolnego interfejsu użytkownika metryk i wybrać pozycję **Dodaj alert**.

![Interfejs użytkownika metryk][3]

Omawiane metryki to App Service Environment metryki. Istnieją także metryki, które są dostępne na poziomie planu App Service. Jest to miejsce, w którym monitorowanie procesora CPU i pamięci sprawia dużo sensu.

W środowisku ASE wszystkie plany App Service są dedykowane App Service plany. Oznacza to, że jedynymi aplikacjami uruchomionymi na hostach przydzielonymi do tego planu App Service są aplikacje w tym App Service planie. Aby wyświetlić szczegółowe informacje na temat planu App Service, zapoznaj się z planem App Service na podstawie dowolnych list w interfejsie użytkownika środowiska ASE lub z **planów App Service przeglądania** (które wyświetla wszystkie z nich).   

### <a name="settings"></a>Ustawienia
W bloku ASE istnieje sekcja **ustawień** , która zawiera kilka ważnych funkcji:

**Ustawienia**  >  **Właściwości**: blok **Ustawienia** zostanie automatycznie otwarty po wyświetleniu bloku środowiska ASE. U góry jest **Właściwości**. W tym miejscu znajduje się wiele elementów, które są nadmiarowe w programie **Essentials**, ale co jest bardzo przydatne w przypadku **wirtualnego adresu IP**, a także **wychodzących adresów IP**.

![Blok ustawień i właściwości][4]

**Ustawienia**  >  **Adresy IP**: w przypadku tworzenia aplikacji Secure Sockets Layer IP (SSL) w środowisku ASE wymagany jest adres połączenie SSL z adresu IP. Aby można było uzyskać ten element, środowisko ASE wymaga Połączenie SSL z adresu IP adresów, do których należy przydzielenia. Po utworzeniu środowisko ASE ma jeden adres Połączenie SSL z adresu IP do tego celu, ale możesz dodać więcej. Jest naliczana opłata za dodatkowe adresy Połączenie SSL z adresu IP, jak pokazano w [App Service cenach][AppServicePricing] (w sekcji połączeń SSL). Cena dodatkowa to Połączenie SSL z adresu IP cena.

**Ustawienia**  >  **Pula**  /  frontonu **Pule procesów roboczych**: Każda z tych bloków puli zasobów umożliwia wyświetlanie informacji tylko w tej puli zasobów, a także zapewnianie kontroli w celu pełnego skalowania tej puli zasobów.  

Blok podstawowy dla każdej puli zasobów zawiera wykres z metrykami dla tej puli zasobów. Podobnie jak w przypadku wykresów z bloku ASE, możesz przejść do wykresu i skonfigurować alerty zgodnie z potrzebami. Ustawienie alertu z bloku ASE dla określonej puli zasobów jest takie samo, jak w przypadku tej samej puli zasobów. W bloku **Ustawienia** puli procesów roboczych masz dostęp do wszystkich aplikacji lub planów App Service, które są uruchomione w tej puli procesów roboczych.

![Interfejs użytkownika ustawień puli procesów roboczych][5]

### <a name="portal-scale-capabilities"></a>Możliwości skalowania portalu
Istnieją trzy operacje skalowania:

* Zmiana liczby adresów IP w środowisku ASE, które są dostępne do Połączenie SSL z adresu IP użycia.
* Zmiana rozmiaru zasobu obliczeniowego, który jest używany w puli zasobów.
* Zmiana liczby zasobów obliczeniowych, które są używane w puli zasobów ręcznie lub za pomocą skalowania automatycznego.

W portalu istnieją trzy sposoby kontrolowania liczby serwerów, które znajdują się w pulach zasobów:

* Operacja skalowania z głównego bloku środowiska ASE w górnej części. Można wprowadzać zmiany konfiguracji wielu skalowania do pul frontonu i procesów roboczych. Są one stosowane w ramach jednej operacji.
* Operacja skalowania ręcznego z **poziomu bloku** pojedynczej puli zasobów, która znajduje się w obszarze **Ustawienia**.
* Skalowanie **Automatyczne** , które można skonfigurować z poziomu pojedynczego bloku puli zasobów.

Aby użyć operacji skalowania w bloku ASE, przeciągnij suwak do odpowiedniej ilości i Zapisz. Ten interfejs użytkownika obsługuje również Zmienianie rozmiaru.  

![Interfejs użytkownika skalowania][6]

Aby użyć funkcji ręcznych lub skalowania automatycznego w określonej puli zasobów, przejdź do pozycji **Ustawienia**  >    /  **Pule procesów roboczych** puli frontonu zgodnie z potrzebami. Następnie otwórz pulę, którą chcesz zmienić. Przejdź do pozycji **Ustawienia** Skaluj w poziomie  >   lub **Ustawienia**  >  **Skaluj w górę**. Blok **skalowanie w poziomie** pozwala sterować ilością wystąpień. **Skalowanie w górę** umożliwia sterowanie rozmiarem zasobów.  

![Interfejs użytkownika ustawień skalowania][7]

## <a name="fault-tolerance-considerations"></a>Uwagi dotyczące odporności na uszkodzenia
App Service Environment można skonfigurować do korzystania z maksymalnie 55 całkowitej ilości zasobów obliczeniowych. Z tych zasobów obliczeniowych 55 można używać tylko 50 do hostowania obciążeń. Przyczyną tego jest dwa cele. Dostępne są co najmniej dwa zasoby obliczeniowe frontonu.  Powoduje to, że do obsługi alokacji puli procesów roboczych przyjdzie do 53. W celu zapewnienia odporności na uszkodzenia należy dysponować dodatkowym zasobem obliczeniowym przydzielonym zgodnie z następującymi regułami:

* Każda pula procesów roboczych wymaga co najmniej 1 dodatkowego zasobu obliczeniowego, który nie jest dostępny do przypisywania obciążenia.
* Gdy ilość zasobów obliczeniowych w puli procesów roboczych przekracza określoną wartość, do odporności na uszkodzenia jest wymagany inny zasób obliczeniowy. Nie jest to przypadek w puli frontonu.

W ramach jednej puli procesów roboczych wymagania dotyczące odporności na uszkodzenia są przeznaczone dla danej wartości X zasobów przypisanych do puli procesów roboczych:

* Jeśli X jest z zakresu od 2 do 20, ilość zasobów obliczeniowych, których można użyć do obciążeń, to X-1.
* Jeśli X jest z zakresu od 21 do 40, ilość zasobów obliczeniowych, których można użyć do obciążeń, to X-2.
* Jeśli X wynosi od 41 do 53, ilość zasobów obliczeniowych, których można użyć do obciążeń, to X-3.

Minimalna wartość to 2 serwery frontonu i 2 procesy robocze.  Korzystając z powyższych instrukcji, poniżej przedstawiono kilka przykładów, które należy wyjaśnić:  

* Jeśli masz 30 procesów roboczych w pojedynczej puli, 28 z nich można używać do hostowania obciążeń.
* Jeśli masz 2 procesy robocze w pojedynczej puli, to 1 może służyć do hostowania obciążeń.
* Jeśli masz 20 procesów roboczych w pojedynczej puli, 19 może służyć do hostowania obciążeń.  
* Jeśli masz 21 procesów roboczych w pojedynczej puli, nadal można używać tylko 19 do obsługi obciążeń.  

Aspekt odporności na uszkodzenia jest istotny, ale należy go zachować na potrzeby skalowania powyżej pewnych progów. Jeśli chcesz zwiększyć pojemność przechodzącą z 20 wystąpień, przejdź do 22 lub nowszej, ponieważ 21 nie dodaje większej pojemności. Taka sama wartość ma miejsce powyżej 40, gdzie kolejny numer, który dodaje pojemność, to 42.  

## <a name="deleting-an-app-service-environment"></a>Usuwanie App Service Environment
Jeśli chcesz usunąć App Service Environment, po prostu Użyj akcji **Usuń** w górnej części bloku App Service Environment. Gdy to zrobisz, zostanie wyświetlony monit o wprowadzenie nazwy App Service Environment, aby potwierdzić, że naprawdę chcesz to zrobić. Należy pamiętać, że po usunięciu App Service Environment należy również usunąć całą jego zawartość.  

![Usuwanie App Service Environment interfejsu użytkownika][9]  

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z App Service środowiskami, zobacz [jak utworzyć App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md