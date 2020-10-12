---
title: Korzystanie z usług równoważenia obciążenia na platformie Azure | Microsoft Docs
description: 'W tym samouczku przedstawiono sposób tworzenia scenariusza przy użyciu portfolio równoważenia obciążenia Azure: Traffic Manager, Application Gateway i Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: 431eaff9da95063648d3e80acb54be9cc5c25bc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393072"
---
# <a name="using-load-balancing-services-in-azure"></a>Korzystanie z usług równoważenia obciążenia na platformie Azure

## <a name="introduction"></a>Wprowadzenie

Microsoft Azure udostępnia wiele usług do zarządzania sposobem dystrybuowania ruchu sieciowego i równoważenia obciążenia. Możesz używać tych usług pojedynczo lub połączyć ich metody, w zależności od potrzeb, aby skompilować optymalne rozwiązanie.

W tym samouczku najpierw zdefiniujemy przypadek użycia klienta i zobaczysz, jak może być bardziej niezawodny i wykonywany przy użyciu następującego portfolio równoważenia obciążenia platformy Azure: Traffic Manager, Application Gateway i Load Balancer. Następnie udostępniamy instrukcje krok po kroku dotyczące tworzenia wdrożenia, które jest geograficznie nadmiarowy, dystrybuuje ruch do maszyn wirtualnych i ułatwiają zarządzanie różnymi typami żądań.

Na poziomie koncepcyjnym każda z tych usług odgrywa odrębną rolę w hierarchii równoważenia obciążenia.

* **Traffic Manager** zapewnia globalne Równoważenie obciążenia DNS. Sprawdza przychodzące żądania DNS i reaguje na prawidłowy punkt końcowy, zgodnie z zasadami routingu wybranymi przez klienta. Opcje dotyczące metod routingu są następujące:
  * Routing wydajności do wysyłania żądania do najbliższego punktu końcowego w warunkach opóźnienia.
  * Kierowanie priorytetów, aby skierować cały ruch do punktu końcowego z innymi punktami końcowymi jako kopię zapasową.
  * Ważony Routing działający w trybie okrężnym, który dystrybuuje ruch na podstawie wagi przypisanej do każdego punktu końcowego.
  * Routing oparty na geografii do dystrybucji ruchu do punktów końcowych aplikacji na podstawie lokalizacji geograficznej użytkownika.
  * Routing oparty na podsieci do dystrybucji ruchu do punktów końcowych aplikacji na podstawie podsieci (zakresu adresów IP) użytkownika.
  * Routing z wieloma wartościami, który umożliwia wysyłanie adresów IP z więcej niż jednego punktu końcowego aplikacji w pojedynczej odpowiedzi DNS.

  Klient łączy się bezpośrednio z punktem końcowym zwróconym przez Traffic Manager. Usługa Azure Traffic Manager wykrywa, kiedy punkt końcowy jest w złej kondycji, a następnie przekierowuje klientów do innego wystąpienia w dobrej kondycji. Zapoznaj się z [dokumentacją usługi Azure Traffic Manager](traffic-manager-overview.md) , aby dowiedzieć się więcej o usłudze.
* **Application Gateway** udostępnia kontroler dostarczania aplikacji (ADC) jako usługę, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Umożliwia ona klientom Optymalizowanie wydajności farmy sieci Web dzięki przeciążeniu przerwania procesora TLS intensywnie wykorzystującego procesor do bramy aplikacji. Inne możliwości routingu warstwy 7 obejmują dystrybucję rozruchową ruchu przychodzącego, koligację sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za pojedynczą bramą aplikacji. Application Gateway można skonfigurować jako bramę internetową, bramę tylko wewnętrzną lub kombinację obu tych elementów. Application Gateway jest w pełni zarządzana, skalowalna i wysoka dostępność na platformie Azure. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie.
* **Load Balancer** to integralna część stosu usługi Azure SDN, zapewniająca wysoką wydajność i niskie opóźnienia warstwy 4 usługi równoważenia obciążenia dla wszystkich protokołów UDP i TCP. Służy do zarządzania połączeniami przychodzącymi i wychodzącymi. Możesz skonfigurować publiczne i wewnętrzne punkty końcowe ze zrównoważonym obciążeniem i zdefiniować reguły mapowania połączeń przychodzących do miejsc docelowych w puli zaplecza, wykorzystując opcje badania kondycji protokołu TCP i HTTP do zarządzania dostępnością usługi.

## <a name="scenario"></a>Scenariusz

W tym przykładowym scenariuszu korzystamy z prostej witryny sieci Web, która obsługuje dwa typy zawartości: obrazy i dynamicznie renderowane strony. Witryna sieci Web musi być geograficznie nadmiarowa i powinna służyć jej użytkownikom z najbliższej lokalizacji (najniższego opóźnienia). Deweloper aplikacji zdecydował, że wszystkie adresy URL zgodne ze wzorcem/images/* są obsługiwane przez dedykowaną pulę maszyn wirtualnych, która różni się od reszty farmy serwerów sieci Web.

Ponadto domyślna pula maszyn wirtualnych obsługująca zawartość dynamiczną musi komunikować się z bazą danych zaplecza hostowaną w klastrze o wysokiej dostępności. Całe wdrożenie zostało skonfigurowane za pomocą Azure Resource Manager.

Użycie Traffic Manager, Application Gateway i Load Balancer pozwala tej witrynie sieci Web osiągnąć następujące cele projektowe:

* **Nadmiarowość geograficzna**: w przypadku przekroczenia jednego regionu Traffic Manager bezproblemowo kieruje ruch do najbliższego regionu bez żadnej interwencji od właściciela aplikacji.
* **Zredukowane opóźnienie**: ponieważ Traffic Manager automatycznie kieruje klienta do najbliższego regionu, klient napotyka małe opóźnienia podczas żądania zawartości strony sieci Web.
* **Niezależna skalowalność**: ze względu na to, że obciążenie aplikacji sieci Web jest rozdzielone typem zawartości, właściciel aplikacji może skalować obciążenia żądań niezależnie od siebie. Application Gateway zapewnia, że ruch jest kierowany do odpowiednich pul na podstawie określonych zasad i kondycji aplikacji.
* **Wewnętrzne równoważenie obciążenia**: ponieważ Load Balancer jest przed klastrem o wysokiej dostępności, tylko aktywny i zdrowy punkt końcowy bazy danych jest narażony na aplikację. Ponadto administrator bazy danych może zoptymalizować obciążenie, rozkładając aktywne i pasywne repliki w klastrze niezależnym od aplikacji frontonu. Load Balancer dostarcza połączenia z klastrem o wysokiej dostępności i zapewnia, że tylko zdrowe bazy danych odbierają żądania połączenia.

Na poniższym diagramie przedstawiono architekturę tego scenariusza:

![Diagram architektury równoważenia obciążenia](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Ten przykład dotyczy tylko jednej z wielu możliwych konfiguracji usług równoważenia obciążenia oferowanej przez platformę Azure. Traffic Manager, Application Gateway i Load Balancer mogą być mieszane i dopasowywane do potrzeb związanych z równoważeniem obciążenia. Na przykład jeśli nie jest konieczne odciążanie protokołu TLS lub przetwarzanie warstwy 7, Load Balancer mogą być używane zamiast Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Konfigurowanie stosu równoważenia obciążenia

### <a name="step-1-create-a-traffic-manager-profile"></a>Krok 1. Tworzenie profilu Traffic Manager

1. W Azure Portal kliknij pozycję **Utwórz zasób zasobów**  >  **Networking**  >  **Traffic Manager profil**  >  **Utwórz**.
2. Wprowadź następujące podstawowe informacje:

   * **Nazwa**: nadaj profilowi Traffic Manager nazwę prefiksu DNS.
   * **Metoda routingu**: wybierz zasady metody routingu ruchu. Aby uzyskać więcej informacji na temat tych metod, zobacz [Informacje o metodach routingu ruchu Traffic Manager](traffic-manager-routing-methods.md).
   * **Subskrypcja**: wybierz subskrypcję zawierającą profil.
   * **Grupa zasobów**: Wybierz grupę zasobów, która zawiera ten profil. Może to być nowa lub istniejąca Grupa zasobów.
   * **Lokalizacja grupy zasobów**: Usługa Traffic Manager jest globalna i nie jest powiązana z lokalizacją. Należy jednak określić region dla grupy, w której znajdują się metadane skojarzone z profilem Traffic Manager. Ta lokalizacja nie ma wpływu na dostępność profilu w czasie wykonywania.

3. Kliknij przycisk **Utwórz** , aby wygenerować profil Traffic Manager.

   ![Blok "Utwórz Traffic Manager"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Krok 2. Tworzenie bram aplikacji

1. W Azure Portal w lewym okienku kliknij pozycję **Utwórz zasób**  >  **Networking**  >  **Application Gateway**sieci.
2. Wprowadź następujące podstawowe informacje o bramie aplikacji:

   * **Name**: Nazwa bramy aplikacji.
   * **Rozmiar jednostki SKU**: rozmiar bramy aplikacji, dostępny jako mały, średni lub duży.
   * **Liczba**wystąpień: liczba wystąpień, wartość z przenoszącą od 2 do 10.
   * **Grupa zasobów**: Grupa zasobów zawierająca bramę aplikacji. Może to być istniejąca Grupa zasobów lub nowa.
   * **Lokalizacja**: region bramy aplikacji, który jest tą samą lokalizacją jak grupa zasobów. Lokalizacja jest ważna, ponieważ sieć wirtualna i publiczny adres IP muszą znajdować się w tej samej lokalizacji co brama.
3. Kliknij przycisk **OK**.
4. Zdefiniuj konfigurację sieci wirtualnej, podsieci, adresu IP frontonu i odbiornika dla bramy aplikacji. W tym scenariuszu adres IP frontonu jest **publiczny**, co umożliwia jego dodanie jako punkt końcowy do profilu Traffic Manager w późniejszym czasie.
5. Skonfiguruj odbiornik przy użyciu jednej z następujących opcji:
    * Jeśli używasz protokołu HTTP, nie ma niczego do skonfigurowania. Kliknij przycisk **OK**.
    * Jeśli używasz protokołu HTTPS, wymagana jest dodatkowa konfiguracja. Zapoznaj się z tematem [Tworzenie bramy aplikacji](../application-gateway/application-gateway-create-gateway-portal.md), rozpoczynając od kroku 9. Po zakończeniu konfiguracji kliknij przycisk **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurowanie routingu adresów URL dla bram aplikacji

W przypadku wybrania puli zaplecza Brama aplikacji, która jest skonfigurowana za pomocą reguły opartej na ścieżce, przyjmuje wzorzec ścieżki adresu URL żądania oprócz rozkładu okrężnego. W tym scenariuszu dodajemy regułę opartą na ścieżce, aby skierować dowolny adres URL z "/images/ \* " do puli serwera obrazów. Aby uzyskać więcej informacji na temat konfigurowania routingu opartego na ścieżkach URL dla bramy aplikacji, zapoznaj się z tematem [Tworzenie reguły opartej na ścieżce dla bramy aplikacji](../application-gateway/application-gateway-create-url-route-portal.md).

![Application Gateway diagram warstwy sieci Web](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Z poziomu grupy zasobów przejdź do wystąpienia bramy aplikacji utworzonej w poprzedniej sekcji.
2. W obszarze **Ustawienia**wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj** , aby dodać maszyny wirtualne, które mają zostać skojarzone z pulami zaplecza warstwy sieci Web.
3. Wprowadź nazwę puli zaplecza i wszystkie adresy IP maszyn znajdujących się w puli. W tym scenariuszu nawiązujemy połączenie dwóch pul serwerów zaplecza maszyn wirtualnych.

   ![Application Gateway "Dodaj pulę zaplecza"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. W obszarze **Ustawienia** bramy aplikacji wybierz pozycję **reguły**, a następnie kliknij przycisk **ścieżka** , aby dodać regułę.

   ![Przycisk reguł Application Gateway "Path based"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Skonfiguruj regułę, podając poniższe informacje.

   Ustawienia podstawowe:

   + **Nazwa**: przyjazna nazwa reguły, która jest dostępna w portalu.
   + **Odbiornik**: odbiornik, który jest używany dla reguły.
   + **Domyślna pula zaplecza**: Pula zaplecza, która ma być używana z regułą domyślną.
   + **Domyślne ustawienia protokołu HTTP**: ustawienia protokołu HTTP, które mają być używane z regułą domyślną.

   Reguły oparte na ścieżce:

   + **Nazwa**: przyjazna nazwa reguły opartej na ścieżce.
   + **Ścieżki**: reguła ścieżki używana do przekazywania ruchu.
   + **Pula zaplecza**: Pula zaplecza, która ma być używana z tą regułą.
   + **Ustawienie protokołu HTTP**: ustawienia protokołu HTTP, które mają być używane z tą regułą.

   > [!IMPORTANT]
   > Ścieżki: prawidłowe ścieżki muszą zaczynać się od "/". Symbol wieloznaczny " \* " jest dozwolony tylko na końcu. Prawidłowe przykłady to/XYZ,/XYZ \* lub/XYZ/ \* .

   ![Application Gateway "Dodawanie reguły opartej na ścieżce"](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Krok 3. Dodawanie bram aplikacji do punktów końcowych Traffic Manager

W tym scenariuszu Traffic Manager jest połączony z bramami aplikacji (zgodnie z konfiguracją w poprzednich krokach), które znajdują się w różnych regionach. Po skonfigurowaniu bram aplikacji następnym krokiem jest nawiązanie połączenia z profilem Traffic Manager.

1. Otwórz profil Traffic Manager. Aby to zrobić, zapoznaj się z grupą zasobów lub Wyszukaj nazwę profilu Traffic Manager ze **wszystkich zasobów**.
2. W lewym okienku wybierz **punkty końcowe**, a następnie kliknij przycisk **Dodaj** , aby dodać punkt końcowy.

   ![Przycisk "Dodaj" Traffic Manager punkty końcowe](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Utwórz punkt końcowy, wprowadzając następujące informacje:

   * **Typ**: Wybierz typ punktu końcowego do równoważenia obciążenia. W tym scenariuszu wybierz pozycję **punkt końcowy platformy Azure** , ponieważ łączymy ją z wystąpieniami usługi Application Gateway, które zostały wcześniej skonfigurowane.
   * **Nazwa**: Wprowadź nazwę punktu końcowego.
   * **Typ zasobu docelowego**: wybierz pozycję **publiczny adres IP** , a następnie w obszarze **zasób docelowy**wybierz publiczny IP bramy aplikacji, która została wcześniej skonfigurowana.

   ![Traffic Manager "Dodaj punkt końcowy"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Teraz można testować konfigurację, uzyskując do niej dostęp przy użyciu systemu DNS profilu Traffic Manager (w tym przykładzie: TrafficManagerScenario.trafficmanager.net). Możesz ponownie wysyłać żądania, wyłączać lub wyłączyć maszyny wirtualne i serwery sieci Web, które zostały utworzone w różnych regionach, i zmienić ustawienia profilu Traffic Manager, aby przetestować konfigurację.

### <a name="step-4-create-a-load-balancer"></a>Krok 4. Tworzenie modułu równoważenia obciążenia

W tym scenariuszu Load Balancer dystrybuuje połączenia z warstwy sieci Web do baz danych w klastrze o wysokiej dostępności.

Jeśli klaster baz danych o wysokiej dostępności korzysta z SQL Server AlwaysOn, zapoznaj się z tematem [Konfigurowanie jednego lub większej liczby odbiorników grupy dostępności](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) , aby uzyskać instrukcje krok po kroku.

Aby uzyskać więcej informacji o konfigurowaniu wewnętrznego modułu równoważenia obciążenia, zobacz [Tworzenie wewnętrznego modułu równoważenia obciążenia w Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. W Azure Portal w lewym okienku kliknij pozycję **Utwórz zasób**  >  **Sieć**  >  **moduł równoważenia obciążenia**.
2. Wybierz nazwę modułu równoważenia obciążenia.
3. Ustaw **Typ** na **Internal**, a następnie wybierz odpowiednią sieć wirtualną i podsieć dla usługi równoważenia obciążenia, w której ma się znajdować.
4. W obszarze **przypisywanie adresów IP**wybierz opcję **dynamiczny** lub **statyczny**.
5. W obszarze **Grupa zasobów**wybierz grupę zasobów dla usługi równoważenia obciążenia.
6. W obszarze **Lokalizacja**wybierz odpowiedni region dla modułu równoważenia obciążenia.
7. Kliknij przycisk **Utwórz** , aby wygenerować moduł równoważenia obciążenia.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Podłączanie warstwy bazy danych zaplecza do modułu równoważenia obciążenia

1. W grupie zasobów Znajdź moduł równoważenia obciążenia, który został utworzony w poprzednich krokach.
2. W obszarze **Ustawienia**kliknij pozycję **Pule zaplecza**, a następnie kliknij przycisk **Dodaj** , aby dodać pulę zaplecza.

   ![Load Balancer "Dodaj pulę zaplecza"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Wprowadź nazwę puli zaplecza.
4. Dodaj pojedyncze maszyny lub zestaw dostępności do puli zaplecza.

#### <a name="configure-a-probe"></a>Konfigurowanie sondy

1. W module równoważenia obciążenia w obszarze **Ustawienia**wybierz pozycję **sondy**, a następnie kliknij przycisk **Dodaj** , aby dodać sondę.

   ![Load Balancer "Dodaj sondę"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Wprowadź nazwę sondy.
3. Wybierz **Protokół** dla sondy. W przypadku bazy danych może być potrzebna sonda TCP, a nie sonda HTTP. Aby dowiedzieć się więcej o sondach modułu równoważenia obciążenia, zobacz [Omówienie sond modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md).
4. Wprowadź **port** bazy danych, który ma być używany do uzyskiwania dostępu do sondy.
5. W obszarze **Interwał**określ częstotliwość sondowania aplikacji.
6. W obszarze **próg złej kondycji**Określ liczbę niepowodzeń ciągłego sondowania, które muszą wystąpić, aby maszyna wirtualna zaplecza była uznawana za złą.
7. Kliknij przycisk **OK** , aby utworzyć sondę.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurowanie reguł równoważenia obciążenia

1. W obszarze **Ustawienia** modułu równoważenia obciążenia wybierz pozycję **reguły równoważenia obciążenia**, a następnie kliknij przycisk **Dodaj** , aby utworzyć regułę.
2. Wprowadź **nazwę** reguły równoważenia obciążenia.
3. Wybierz **adres IP frontonu** modułu równoważenia obciążenia, **protokołu**i **portu**.
4. W obszarze **port zaplecza**określ port, który ma być używany w puli zaplecza.
5. Wybierz **pulę zaplecza** i **sondę** , która została utworzona w poprzednich krokach, aby zastosować regułę do programu.
6. W obszarze **trwałość sesji**wybierz sposób, w jaki sesje mają być utrwalane.
7. W obszarze **limity czasu bezczynności**Określ liczbę minut przed upływem limitu czasu bezczynności.
8. W obszarze **zmiennoprzecinkowy adres IP**wybierz opcję **wyłączone** lub **włączone**.
9. Kliknij przycisk **OK**, aby utworzyć regułę.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Krok 5. Łączenie maszyn wirtualnych warstwy internetowej z usługą równoważenia obciążenia

Teraz skonfigurujemy adres IP i port frontonu modułu równoważenia obciążenia w aplikacjach uruchamianych na maszynach wirtualnych warstwy sieci Web dla wszystkich połączeń z bazą danych. Ta konfiguracja jest specyficzna dla aplikacji działających na tych maszynach wirtualnych. Aby skonfigurować docelowy adres IP i port, zapoznaj się z dokumentacją aplikacji. Aby znaleźć adres IP frontonu, w Azure Portal przejdź do puli adresów IP frontonu w **ustawieniach modułu równoważenia obciążenia**.

![Load Balancer okienku nawigacji puli adresów IP frontonu](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Traffic Manager](traffic-manager-overview.md)
* [Application Gateway — omówienie](../application-gateway/application-gateway-introduction.md)
* [Omówienie usługi Azure Load Balancer](../load-balancer/load-balancer-overview.md)
