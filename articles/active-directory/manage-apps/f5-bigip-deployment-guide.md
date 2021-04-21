---
title: Przewodnik wdrażania bezpiecznego dostępu hybrydowego usługi Azure AD z klawiszem F5 | Microsoft Docs
description: Samouczek dotyczący wdrażania maszyny wirtualnej F5 BIG-IP Virtual Edition (VE) na platformie Azure IaaS w celu uzyskania bezpiecznego dostępu hybrydowego
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 139009c55573e1e115a22069671f66e93695a635
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783327"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Samouczek dotyczący wdrażania maszyny wirtualnej F5 BIG-IP Virtual Edition w usłudze Azure IaaS w celu uzyskania bezpiecznego dostępu hybrydowego

Ten samouczek zawiera omówienie procesów wdrażania systemu BIG-IP Vitural Edition (VE) w usłudze Azure IaaS. Po zakończeniu tego samouczka będziesz mieć:

- W pełni przygotowana maszyna wirtualna BIG-IP do modelowania weryfikacji koncepcji bezpiecznego dostępu hybrydowego (SHA)

- Wystąpienie przejściowe do testowania nowych aktualizacji i poprawek systemu BIG-IP

## <a name="prerequisites"></a>Wymagania wstępne

Wcześniejsze doświadczenie lub wiedza dotycząca obsługi big-IP W5 nie jest konieczne, jednak zalecamy zapoznanie się z terminologią [F5 BIG-IP.](https://www.f5.com/services/resources/glossary) Wdrożenie dużego adresu IP na platformie Azure dla sha wymaga:

- Płatna subskrypcja platformy Azure lub bezpłatna 12-miesięczna [subskrypcja próbna.](https://azure.microsoft.com/free/)

- Dowolna z następujących jednostki SKU licencji F5 BIG-IP

  - F5 BIG-IP® najlepszy pakiet

  - F5 BIG-IP Access Policy Manager™ autonomiczna licencja (APM)

  - F5 BIG-IP Access Policy Manager™ (APM) add-on license on an existing BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM)
  
  - 90-dniowa pełna wersja próbna wersji [próbnej funkcji](https://www.f5.com/trial/big-ip-trial.php)BIG-IP.

- Symbol wieloznaczny lub certyfikat alternatywnej nazwy podmiotu (SAN) do publikowania aplikacji internetowych za pośrednictwem protokołu Secure Socket Layer (SSL). [Zaszyfrujmy](https://letsencrypt.org/) bezpłatne 90-dniowe certyfikaty do testowania.

- Certyfikat SSL do zabezpieczania BIG-IPs zarządzania. Można użyć certyfikatu używanego do publikowania aplikacji internetowych, jeśli jego podmiot odpowiada w pełni kwalifikowanej nazwie domeny (FQDN) big-IP. Na przykład certyfikat z symbolami wieloznaczny zdefiniowany z podmiotem *.contoso.com będzie odpowiedni dla `https://big-ip-vm.contoso.com:8443`

Wdrażanie maszyny wirtualnej i podstawowe konfiguracje systemu potrwają około 30 minut. W tym momencie platforma BIG-IP będzie gotowa do wdrożenia dowolnego ze scenariuszy SHA wymienionych [tutaj.](f5-aad-integration.md)

W przypadku testowania scenariuszy w tym samouczku przyjęto założenie, że adres BIG-IP zostanie wdrożony w grupie zasobów platformy Azure zawierającej środowisko usługi Active Directory (AD). Środowisko powinno składać się z maszyn wirtualnych kontrolera domeny (DC) i hosta sieci Web (IIS). Posiadanie tych serwerów w innych lokalizacjach dla maszyny wirtualnej z dużymi adresami IP jest również prawidłowe, o podanie adresu BIG-IP dla każdej z ról wymaganych do obsługi danego scenariusza. Obsługiwane są również scenariusze, w których maszyna wirtualna BIG-IP jest połączona z innym środowiskiem za pośrednictwem połączenia sieci VPN.

Jeśli nie masz elementów wymienionych w tym miejscu do testowania, możesz wdrożyć całe środowisko domeny usługi AD na platformie Azure przy użyciu tego [skryptu](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Kolekcję przykładowych aplikacji testowych można również programowo wdrożyć na hoście internetowym usług IIS przy użyciu tej [automatyzacji ze skryptami.](https://github.com/jeevanbisht/DemoSuite)

>[!NOTE]
>Proces [Azure Portal](https://portal.azure.com/#home) stale się rozwija, więc niektóre kroki w tym samouczku mogą różnić się od rzeczywistego układu zaobserwowanego w Azure Portal.

## <a name="azure-deployment"></a>Wdrażanie na platformie Azure

Big-IP można wdrożyć w różnych topologiach. Ten przewodnik koncentruje się na wdrożeniu z jedną kartą sieciową. Jeśli jednak wdrożenie funkcji BIG-IP wymaga wielu interfejsów sieciowych w celu wysokiej dostępności, podziału sieci lub przepływności większej niż 1 GB, rozważ użycie wstępnie skompilowanych szablonów interfejsu [Azure Resource Manager (ARM)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)języka F5.

Wykonaj następujące zadania, aby wdrożyć system BIG-IP VE [z](https://azuremarketplace.microsoft.com/marketplace/apps)Azure Marketplace .

1. Zaloguj się do [Azure Portal](https://portal.azure.com/#home) przy użyciu konta, które ma uprawnienia do tworzenia maszyn wirtualnych. Na przykład Współautor

2. W górnym polu wyszukiwania wstążki wpisz **marketplace,** a następnie **enter**

3. Wpisz **F5 w** filtrze witryny Marketplace, a następnie klawisz **Enter**

4. Wybierz **pozycję + Dodaj** na górnej wstążce i wpisz **F5** w filtrze witryny Marketplace, a następnie klawisz **Enter**

5. Wybierz **pozycję F5 BIG-IP Virtual Edition (BYOL)** Wybierz plan oprogramowania  >    >  **F5 BIG-IP VE — ALL (BYOL, 2 lokalizacje rozruchu)**

6. Wybierz przycisk **Utwórz**.

![Obraz przedstawia kroki wybierania planu oprogramowania](./media/f5ve-deployment-plan/software-plan.png)

7. Skorzystaj z menu **Podstawy** i użyj następujących ustawień

 |  Szczegóły projektu     |  Wartość     |
 |:-------|:--------|
 |Subskrypcja|Subskrypcja docelowa wdrożenia maszyny wirtualnej BIG-IP|
 |Grupa zasobów | Istniejąca grupa zasobów platformy Azure maszyna wirtualna BIG-IP zostanie wdrożona w tej grupie lub zostanie w nim utworzyć. Powinna to być ta sama grupa zasobów co maszyny wirtualne kontrolera domeny i usług IIS|
 | **Szczegóły wystąpienia**|  |
 |Nazwa maszyny wirtualnej| Przykład maszyny wirtualnej BIG-IP-VM |
 |Region (Region) | Lokalizacja geograficzna platformy Azure dla maszyny wirtualnej BIG-IP-VM |
 |Opcje dostępności| Włącz tylko w przypadku używania maszyny wirtualnej w środowisku produkcyjnym|
 |Obraz| F5 BIG-IP VE — WSZYSTKIE (BYOL, 2 lokalizacje rozruchu)|
 |Wystąpienie usługi Azure Spot| Nie, ale możesz je włączyć w razie potrzeby |
 |Rozmiar| Minimalna specyfikacja powinna mieć rozmiar 2 procesorów wirtualnych i 8 GB pamięci|
 |**Konto administratora**|  |
 |Typ uwierzytelniania|Na razie wybierz hasło. Później możesz przełączyć się na parę kluczy |
 |Nazwa użytkownika|Tożsamość, która zostanie utworzona jako konto lokalne BIG-IP do uzyskiwania dostępu do jego interfejsów zarządzania. W nazwie użytkownika jest zróżnicowa wielkość liter.|
 |Hasło|Zabezpieczanie dostępu administratora przy użyciu silnego hasła|
 |**Reguły portów przychodzących**|  |
 |Publiczne porty wejściowe|Brak|

8. Wybierz **pozycję Dalej: Dyski** pozostawiają wszystkie wartości domyślne, a następnie wybierz pozycję **Dalej: Sieć**.

9. W menu **Sieć** wypełnij te ustawienia.

 |Interfejs sieciowy|      Wartość |
 |:--------------|:----------------|
 |Sieć wirtualna|Ta sama sieć wirtualna platformy Azure, która jest używana przez kontroler domeny i maszyny wirtualne usług IIS, lub utwórz jedną z nich|
 |Podsieć| Ta sama wewnętrzna podsieć platformy Azure co maszyny wirtualne kontrolera domeny i usług IIS lub utwórz taką podsieć|
 |Publiczny adres IP |  Brak|
 |Sieciowa grupa zabezpieczeń kart sieciowych| Wybierz pozycję Brak, jeśli podsieć platformy Azure wybrana w poprzednich krokach jest już skojarzona z sieciową grupą zabezpieczeń. w przeciwnym razie wybierz pozycję Podstawowa|
 |Przyspieszanie pracy w sieci| Wyłączone |
 |**Równoważenie obciążenia**|     |
 |Równoważenie obciążenia maszyny wirtualnej| Nie|

10. Wybierz **pozycję Dalej: Zarządzanie** i wykonaj te ustawienia.

 |Monitorowanie|    Wartość |
 |:---------|:-----|
 |Szczegółowe monitorowanie| Wyłączone|
 |Diagnostyka rozruchu|Włącz przy użyciu niestandardowego konta magazynu. Umożliwia nawiązywanie połączenia z interfejsem BIG-IP Secure Shell (SSH) za pośrednictwem opcji Konsola szeregowa w Azure Portal. Wybierz dowolne dostępne konto usługi Azure Storage|
 |**Tożsamość**|  |
 |Tożsamość zarządzana przypisana przez system|Wyłączone|
 |Azure Active Directory|Big-IP nie obsługuje obecnie tej opcji|
 |**Automatyczneshutdown**|    |
 |Włączanie automatycznego zamykania| W przypadku testowania rozważ ustawienie codziennego zamykania maszyny wirtualnej BIG-IP-VM|

11. Wybierz **pozycję Dalej: Zaawansowane,** pozostawiając wszystkie wartości domyślne, a następnie wybierz pozycję **Dalej: Tagi**.

12. Wybierz **pozycję Dalej: Przeglądanie +** tworzenie, aby przejrzeć  konfiguracje maszyny wirtualnej BIG-IP przed wybraniem przycisku Utwórz w celu wdrożenia.

13. Czas pełnego wdrożenia maszyny wirtualnej z dużymi adresami IP wynosi zwykle 5 minut. Po zakończeniu nie wybieraj pozycji Przejdź do zasobu **,** Azure Portal rozwiń  menu po lewej stronie i wybierz pozycję Grupy zasobów, aby przejść do nowej maszyny wirtualnej BIG-IP-VM. Jeśli tworzenie maszyny wirtualnej nie powiedzie się, wybierz pozycję **Wstecz** i **Dalej.**

## <a name="network-configuration"></a>Konfiguracja sieci

Gdy maszyna wirtualna BIG-IP zostanie po raz  pierwszy uruchamiana, jej karta sieciowa zostanie aprowizowana przy użyciu podstawowego prywatnego adresu IP wystawionego przez usługę Dynamic Host Configuration Protocol (DHCP) podsieci platformy Azure, z którym jest połączona. Ten adres IP będzie używany przez system operacyjny do zarządzania ruchem (TMOS) big-IP do komunikowania się z:

- Komunikacja z innymi hostami i usługami

- Dostęp wychodzący do publicznego Internetu

- Dostęp przychodzący do konfiguracji BIG-IPs sieci Web i interfejsów zarządzania SSH

Uwsłanianie jednego z tych interfejsów zarządzania w Internecie zwiększa BIG-IPs, dlatego podstawowy adres IP usługi BIG-IPs nie został zaaprowizowany przy użyciu publicznego adresu IP podczas wdrażania. Zamiast tego dla usług publikowania zostanie aprowowany pomocniczy wewnętrzny adres IP i skojarzony publiczny adres IP.
To mapowanie 1 do 1 między publicznym i prywatnym adresem IP maszyny wirtualnej umożliwia ruchowi zewnętrznemu dotarcie do maszyny wirtualnej. Jednak aby zezwolić na ruch, w znacznie taki sam sposób jak w przypadku zapory, wymagana jest również reguła sieciowej sieciowej organizacji platformy Azure.

Na diagramie przedstawiono wdrożenie jednej karty sieciowej funkcji BIG-IP VE na platformie Azure, skonfigurowane z podstawowym adresem IP do operacji ogólnych i zarządzania oraz oddzielnymi adresami IP serwera wirtualnego do publikowania usług.
W takim przypadku reguła sieciowej sieciowej organizacji umożliwia ruch zdalny przeznaczony dla usługi do przekierowania do publicznego adresu IP dla opublikowanej usługi przed jego przesyłaniem do serwera wirtualnego `intranet.contoso.com` BIG-IP.

![Obraz przedstawia wdrożenie z jedną kartą sieciową Domyślnie prywatne i publiczne ip wystawione dla maszyn wirtualnych platformy Azure są zawsze dynamiczne, więc prawdopodobnie zmienią się przy każdym ponownym ](./media/f5ve-deployment-plan/single-nic-deployment.png) uruchomieniu maszyny wirtualnej. Unikaj nieprzewidzianych problemów z łącznością, zmieniając adres IP zarządzania BIG-IPs na statyczny i zrób to samo co dodatkowe adresy IP używane do publikowania usług.

1. Z menu maszyny wirtualnej BIG-IP przejdź do pozycji **Ustawienia**  >  **Sieci**

2. W widoku sieci wybierz link z prawej strony karty **Interfejs sieciowy**

![Obraz przedstawia konfigurację sieci](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>Nazwy maszyn wirtualnych są generowane losowo podczas wdrażania.

3. W okienku po lewej stronie wybierz pozycję **Konfiguracje adresów IP,** a następnie wybierz **wiersz ipconfig1**

4. Ustaw opcję **Przypisywanie adresów IP** na statyczną i w razie potrzeby zmień podstawowy adres IP maszyn wirtualnych BIG-IP. Następnie wybierz **pozycję Zapisz** i zamknij menu ipconfig1

>[!NOTE]
>Użyjesz tego podstawowego adresu IP do nawiązania połączenia z maszyną wirtualną BIG-IP-VM i zarządzania tą maszyną.

5. Wybierz **pozycję + Dodaj** na górnej wstążce i podaj nazwę pomocniczego prywatnego adresu IP, na przykład ipconfig2

6. Ustaw opcję **Alokacja** ustawień prywatnego adresu IP na **statyczny**. Podanie kolejnego wyższego lub niższego kolejnego adresu IP pomaga zachować porządek.

7. Ustaw publiczny adres IP na **Skojarz i** wybierz pozycję **Utwórz**

8. Podaj nazwę nowego publicznego adresu IP, na przykład BIG-IP-VM_ipconfig2_Public

9. Jeśli zostanie wyświetlony monit, ustaw wartość **Standardowa dla tej wersji**

10. Jeśli zostanie wyświetlony monit, ustaw **warstwę na** globalne **i**

11. Ustaw opcję **Przypisanie** na **statyczną,** a następnie dwukrotnie wybierz przycisk **OK.**

Maszyna wirtualna BIG-IP-VM jest teraz gotowa do skonfigurowania przy użyciu:

- **Podstawowy prywatny adres IP:** dedykowane do zarządzania maszyną wirtualną BIG-IP-VM za pomocą narzędzia konfiguracji sieci Web i protokołu SSH. Będzie on używany przez system BIG-IP jako własny **adres IP** do łączenia się z opublikowanymi usługami zaplecza. Ponadto łączy się z usługami zewnętrznymi, takimi jak NTP, AD i LDAP.

- **Pomocniczy prywatny adres IP:** używany podczas tworzenia serwera wirtualnego BIG-IP APM do nasłuchiwać żądań przychodzących do opublikowanych usług.

- **Publiczny adres IP:** skojarzona z pomocniczym prywatnym adresem IP umożliwia ruchowi klienta z publicznego Internetu dotarcie do serwera wirtualnego BIG-IP dla opublikowanych usług

W przykładzie pokazano relację od 1 do 1 między publicznymi i prywatnymi ip maszyny wirtualnej. Karta sieciowa maszyny wirtualnej platformy Azure może mieć tylko jeden podstawowy adres IP, a wszystkie dodatkowo utworzone adresy IP są zawsze określane jako pomocnicze.

>[!NOTE]
>Będziesz potrzebować dodatkowych mapowań adresów IP do publikowania usług BIG-IP.

![Ten obraz przedstawia wszystkie pomocnicze ip](./media/f5ve-deployment-plan/secondary-ips.png)

Aby zaimplementować sha przy użyciu konfiguracji z przewodnikiem dostępu BIG-IP, powtórz kroki 5–11, aby utworzyć dodatkowe pary prywatnych i publicznych adresów IP dla każdej dodatkowej usługi, która ma być publikowana za pośrednictwem programu BIG-IP APM. Tego samego podejścia można również użyć w przypadku publikowania usług przy użyciu BIG-IPs **Advanced Configuration.** Jednak w tym scenariuszu istnieje możliwość uniknięcia narzutów publicznych adresów IP przy użyciu konfiguracji wskaźnika nazwy serwera [(SNI).](https://support.f5.com/csp/#/article/K13452) W tej konfiguracji serwer wirtualny BIG-IP będzie akceptować cały odbierany ruch klienta i przekierowywować go do miejsca docelowego.

## <a name="dns-configuration"></a>Konfiguracja usługi DNS

Ważne jest, aby system DNS był skonfigurowany dla klientów w celu rozpoznawania opublikowanych usług SHA do publicznych adresów IP maszyn wirtualnych BIG-IP.

W poniższych krokach przyjęto założenie, że strefa DNS domeny publicznej używanej dla usług SHA jest zarządzana na platformie Azure. Jednak te same zasady DNS dotyczące tworzenia rekordu lokalizatora nadal mają zastosowanie niezależnie od tego, gdzie jest zarządzana strefa DNS.

1. Jeśli jeszcze nie zostało to otwarte, rozwiń menu po lewej stronie portalu i przejdź do maszyny wirtualnej BIG-IP-VM za pomocą **opcji Grupy** zasobów

2. Z menu maszyny wirtualnej BIG-IP przejdź do pozycji **Ustawienia**  >  **Sieci**

3. W widoku sieci maszyny wirtualnej BIG-IP wybierz pierwszy pomocniczy adres IP z listy rozwijanej konfiguracji adresu IP i wybierz link dla jego publicznego adresu **IP karty sieciowej**

![Zrzut ekranu przedstawiający publiczny adres IP karty sieciowej](./media/f5ve-deployment-plan/networking.png)

4. Poniżej sekcji **Ustawienia** w okienku po  lewej stronie wybierz pozycję Konfiguracja, aby wyprowadzić publiczny adres IP i menu właściwości DNS dla wybranego pomocniczego adresu IP

5. Wybierz pozycję **i utwórz** rekord aliasu, a następnie **wybierz strefę DNS** z listy rozwijanej. Jeśli strefa DNS jeszcze nie istnieje, może być zarządzana poza platformą Azure lub można utworzyć ją dla sufiksu domeny zweryfikowanego w usłudze Azure AD.

6. Użyj następujących szczegółów, aby utworzyć pierwszy rekord aliasu DNS:

 | Pole | Wartość |
 |:-------|:-----------|
 |Subskrypcja| Ta sama subskrypcja co maszyna wirtualna BIG-IP|
 |Strefa DNS| Strefa DNS, która jest autorytatywna dla sufiksu zweryfikowanego domeny, będzie używać opublikowanych witryn internetowych, na przykład www.contoso.com |
 |Nazwa | Wybrana nazwa hosta będzie rozpoznawane jako publiczny adres IP skojarzony z wybranym pomocniczym adresem IP. Upewnij się, że zdefiniowano poprawne mapowania DNS na IP. Zobacz ostatni obraz w sekcji Konfiguracje sieci, na przykład intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |Jednostki TTL | Godziny |

7. Wybierz **pozycję Utwórz** dla platformy Azure, aby zapisać te ustawienia w publicznym systemie DNS.

8. Pozostaw **etykietę nazwy DNS (opcjonalnie)** i wybierz **pozycję Zapisz** przed zamknięciem menu Publiczny adres IP.

9. Powtórz kroki od 1 do 6, aby utworzyć dodatkowe rekordy DNS dla każdej usługi, która ma być publikowana przy użyciu konfiguracji z przewodnikiem BIG-IP.

  Po utworzeniu rekordów DNS można użyć dowolnego narzędzia [](https://dnschecker.org/) w trybie online, takiego jak narzędzie do sprawdzania DNS, aby sprawdzić, czy utworzony rekord został pomyślnie rozpropagowany na wszystkich globalnych publicznych serwerach DNS.

  Jeśli zarządzasz przestrzenią nazw domeny DNS przy użyciu zewnętrznego dostawcy, takiego jak [GoDaddy,](https://www.godaddy.com/)musisz utworzyć rekordy przy użyciu własnej funkcji zarządzania systemem DNS.

>[!NOTE]
>Można również użyć lokalnego pliku hosts komputera podczas testowania i częstego przełączania rekordów DNS. Plik localhosts na komputerze z systemem Windows jest dostępny po naciśnięciu klawiszy Win + R na klawiaturze i przesłaniu sterowników **słów** w polu uruchamiania. Należy pamiętać, że rekord hosta lokalnego zapewni rozpoznawanie nazw DNS tylko dla komputera lokalnego, a nie dla innych klientów.

## <a name="client-traffic"></a>Ruch klientów

Domyślnie sieci wirtualne platformy Azure i skojarzone podsieci są sieciami prywatnymi, które nie mogą odbierać ruchu internetowego. Karta sieciowa maszyny wirtualnej BIG-IP-VM powinna być dołączona do sieciowej sieciowej usługi sieciowej określonej podczas wdrażania. Aby zewnętrzny ruch internetowy docierał do maszyny wirtualnej BIG-IP-VM, należy zdefiniować regułę sieciowej sieci, aby zezwolić na ruch przychodzący przez porty 443 (HTTPS) i 80 (HTTP) z publicznego Internetu.

1. Z głównego menu Przegląd maszyny  wirtualnej BIG-IP wybierz pozycję **Sieć**

2. Wybierz **pozycję Dodaj** regułę ruchu przychodzącego, aby wprowadzić następujące właściwości reguły sieciowej sieciowej sieciowej sieci:

 |     Pole   |   Wartość        |
 |:------------|:------------|
 |Źródło| Dowolne|
 |Zakresy portów źródłowych| *|
 |Docelowe adresy IP|Rozdzielana przecinkami lista wszystkich pomocniczych prywatnych adresów IP big-ip-vm|
 |Porty docelowe| 80 443|
 |Protokół| TCP |
 |Akcja| Zezwalaj|
 |Priorytet|Najniższa dostępna wartość z 100 do 4096|
 |Nazwa | Opisowa nazwa, na przykład: `BIG-IP-VM_Web_Services_80_443`|

3. Wybierz **pozycję Dodaj,** aby zatwierdzić zmiany, a następnie **zamknij** menu Sieć.

Ruch HTTP i HTTPS z dowolnej lokalizacji będzie teraz mógł docierać do wszystkich interfejsów pomocniczych maszyn wirtualnych BIG-IP. Zezwolenie na port 80 jest przydatne w umożliwianiu systemowi BIG-IP APM automatycznego przekierowywania użytkowników z protokołu HTTP do protokołu HTTPS. Tę regułę można edytować w celu dodawania lub usuwania docelowych adresów IP w razie potrzeby.

## <a name="manage-big-ip"></a>Zarządzanie big-IP

System BIG-IP jest administrowana za pośrednictwem interfejsu użytkownika konfiguracji sieci Web, do którego można uzyskać dostęp przy użyciu jednej z następujących zalecanych metod:

- Z maszyny w sieci wewnętrznej big-IP

- Z klienta sieci VPN połączonego z siecią wewnętrzną maszyny wirtualnej BIG-IP-VM

- Opublikowane za [pośrednictwem usługi Azure AD serwer proxy aplikacji](./application-proxy-add-on-premises-application.md)

Przed kontynuowaniem pracy z pozostałymi konfiguracjami należy wybrać najbardziej odpowiednią metodę. W razie potrzeby możesz połączyć się bezpośrednio z konfiguracją sieci Web z Internetu, konfigurując podstawowy adres IP BIG-IP przy użyciu publicznego adresu IP. Następnie dodaj regułę sieciowej sieci, aby zezwolić na ruch 8443 do tego podstawowego adresu IP. Pamiętaj, aby ograniczyć źródło do własnego zaufanego adresu IP. W przeciwnym razie każdy będzie mógł nawiązać połączenie.

Gdy wszystko będzie gotowe, potwierdź, że możesz nawiązać połączenie z konfiguracją sieci Web maszyny wirtualnej BIG-IP i zalogować się przy użyciu poświadczeń określonych podczas wdrażania maszyny wirtualnej:

- Jeśli łączysz się z maszyny wirtualnej w sieci wewnętrznej lub za pośrednictwem sieci VPN, połącz się bezpośrednio z podstawowym adresem IP BIG-IPs portem konfiguracji sieci Web. Na przykład `https://<BIG-IP-VM_Primary_IP:8443`. Przeglądarka wyświetli monit o niezabezpieczone połączenie, ale możesz zignorować monit do momentu skonfigurowania big-IP. Jeśli przeglądarka naciska na blokowanie dostępu, wyczyść jego pamięć podręczną i spróbuj ponownie.

- Jeśli konfiguracja sieci Web została opublikowana za pośrednictwem serwer proxy aplikacji, użyj zdefiniowanego adresu URL, aby uzyskać dostęp do konfiguracji sieci Web zewnętrznie, bez dołączania portu, na przykład `https://big-ip-vm.contoso.com` . Wewnętrzny adres URL musi być zdefiniowany przy użyciu portu konfiguracji sieci Web, na przykład `https://big-ip-vm.contoso.com:8443` 

Systemem BIG-IP można również zarządzać za pośrednictwem podstawowego środowiska SSH, które jest zwykle używane do zadań wiersza polecenia (CLI) i dostępu na poziomie głównym. Istnieje kilka opcji nawiązywania połączenia z interfejsem wiersza polecenia, w tym:

- [Azure Bastion Service:](../../bastion/bastion-overview.md)umożliwia szybkie i bezpieczne połączenia z dowolną maszyną wirtualną w sieci wirtualnej z dowolnej lokalizacji

- Nawiązywanie bezpośredniego połączenia za pośrednictwem klienta SSH, takiego jak PuTTY, za pośrednictwem podejścia JIT

- Konsola szeregowa: oferowana w dolnej części sekcji Pomoc techniczna i rozwiązywanie problemów w menu maszyn wirtualnych w portalu. Nie obsługuje transferów plików.

- Podobnie jak w przypadku konfiguracji sieci Web, możesz również połączyć się bezpośrednio z interfejsem wiersza polecenia z Internetu, konfigurując podstawowy adres IP BIG-IP przy użyciu publicznego adresu IP i dodając regułę sieciowej sieciowej organizacji w celu zezwalania na ruch SSH. Pamiętaj również, aby ograniczyć źródło do własnego zaufanego adresu IP, jeśli jest stosowana ta metoda.  

## <a name="big-ip-license"></a>Licencja BIG-IP

System BIG-IP musi zostać aktywowany i aprowzowany za pomocą modułu APM, zanim będzie można go skonfigurować do publikowania usług i sha.

1. Zaloguj się ponownie do konfiguracji sieci Web i na stronie **właściwości Ogólne** wybierz pozycję **Aktywuj**

2. W polu **Klucz rejestracji podstawowej** wprowadź klucz z wielkością liter dostarczony przez klawisz F5

3. Pozostaw opcję Metoda  **aktywacji** ustawioną na Wartość automatyczna i wybierz pozycję **Dalej**. Funkcja BIG-IP zweryfikuje licencję i wyświetli umowę licencyjną użytkownika oprogramowania (EULA).

4. Przed **wybraniem** przycisku Kontynuuj wybierz pozycję Zaakceptuj i poczekaj na ukończenie **aktywacji.**

5. Zaloguj się ponownie i w dolnej części strony Podsumowania licencji wybierz pozycję **Dalej.** Funkcja BIG-IP będzie teraz wyświetlać listę modułów, które zapewniają różne funkcje wymagane przez sha. Jeśli nie widzisz tej informacji, na karcie głównej przejdź do karty **Aprowowanie**  >  **zasobów systemowych.**

6. Sprawdź kolumnę aprowizowania dla zasad dostępu (APM)

![Obraz przedstawia aprowizowanie dostępu](./media/f5ve-deployment-plan/access-provisioning.png)

7. Wybierz **pozycję Prześlij** i zaakceptuj ostrzeżenie

8. Czekaj na ukończenie oświetlenia nowych funkcji przez system BIG-IP. Przed pełnym zainicjowaniem może on być kilka razy cykliczny. 

9. Gdy wszystko będzie **gotowe, wybierz** pozycję Kontynuuj i na karcie **Informacje** wybierz **pozycję Uruchom narzędzie konfiguracji**

>[!IMPORTANT]
>Licencje F5 są ograniczone do jednego wystąpienia BIG-IP VE w dowolnym momencie. Mogą być powody, dla których warto przeprowadzić migrację licencji z jednego [](https://support.f5.com/csp/article/K41458656) wystąpienia do innego. W przeciwnym razie należy odwołać licencję próbną aktywnego wystąpienia przed jego likwidacją. W przeciwnym razie licencja zostanie trwale utracona.

## <a name="provision-big-ip"></a>Aprowizuj big-IP

Najważniejszą sprawą jest zabezpieczanie ruchu BIG-IPs do i z sieci Web. Skonfiguruj certyfikat zarządzania urządzeniami, aby chronić kanał konfiguracji sieci Web przed naruszeniami.

1. Na pasku nawigacyjnym po lewej stronie przejdź do menu **Zarządzanie** certyfikatami systemu  >    >  **Traffic Certificate Management** SSL Certificate List  >    >  **Import**

2. Z listy **rozwijanej Typ** importu wybierz pozycję **PKCS 12 (IIS)** i **wybierz pozycję Plik.** Znajdź certyfikat sieci Web PROTOKOŁU SSL z nazwą podmiotu lub siecią SAN, który będzie obejmować nazwę FQDN, przypisz maszynę wirtualną BIG-IP-VM w kilku następnych krokach

3. Podaj hasło certyfikatu, a następnie wybierz pozycję **Importuj**

4. Na lewym pasku nawigacyjnym przejdź do menu **Platforma**  >  **systemowa.**

5. Skonfiguruj maszynę wirtualną BIG-IP-VM przy użyciu w pełni kwalifikowanej nazwy hosta i strefy czasowej dla jej środowiska, a następnie **zaktualizuj**

![Obraz przedstawia właściwości ogólne](./media/f5ve-deployment-plan/general-properties.png)

6. Na lewym pasku nawigacyjnym przejdź do opcji **System**  >  **Configuration**  >  **Device**  >  **NTP.**

7. Określ niezawodne źródło NTP i wybierz **dodaj**, a następnie **aktualizacji**. Na przykład `time.windows.com`

Teraz potrzebny jest rekord DNS do rozpoznania nazwy FQDN BIG-IPs określonej w poprzednich krokach do jego podstawowego prywatnego adresu IP. Rekord należy dodać do wewnętrznego systemu DNS środowiska lub do pliku hosta lokalnego komputera, który będzie używany do nawiązywania połączenia z konfiguracją sieci Web big-IP. W obu sposób ostrzeżenie przeglądarki nie powinno już być wyświetlane po bezpośrednim połączeniu z konfiguracją sieci Web. Oznacza to, że nie za pośrednictwem serwer proxy aplikacji ani żadnego innego zwrotnego serwera proxy.

## <a name="ssl-profile"></a>Profil SSL

Jako zwrotny serwer proxy system BIG-IP może działać jako prosta usługa przesyłania dalej, znana także jako przezroczysty serwer proxy lub pełny serwer proxy, który aktywnie uczestniczy w wymianie między klientami i serwerami.
Pełny serwer proxy tworzy dwa odrębne połączenia: połączenie klienta TCP frontou z oddzielnym połączeniem serwera TCP zaplecza, połączone przez niedużo rozgałęzienie w środku. Klienci łączą się z odbiornikiem serwera proxy po jednej stronie, w przeciwnym razie nazywanym serwerem wirtualnym, a serwer proxy ustanawia oddzielne, niezależne połączenie z serwerem zaplecza. Jest to dwukierunkowe po obu stronach.
W tym trybie pełnego serwera proxy system F5 BIG-IP może sprawdzać ruch i w związku z tym wchodzić w interakcje z żądaniami i odpowiedziami. Niektóre funkcje, takie jak równoważenie obciążenia i optymalizacja wydajności sieci Web, a także bardziej zaawansowane usługi zarządzania ruchem, takie jak zabezpieczenia warstwy aplikacji, przyspieszanie sieci Web, routing stron i bezpieczny dostęp zdalny, korzystają z tej funkcji.
Podczas publikowania usług opartych na protokołach SSL proces odszyfrowywania i szyfrowania ruchu między klientami a usługami zaplecza jest obsługiwany przez profile big Połączenie SSL z adresu IP danych.

Istnieją dwa typy profilów:

- **Protokół SSL klienta:** najczęstszym sposobem konfigurowania systemu BIG-IP w celu publikowania usług wewnętrznych za pomocą protokołu SSL jest utworzenie profilu SSL klienta. Dzięki profilowi SSL klienta system BIG-IP może odszyfrować przychodzące żądania klientów przed wysłaniem ich do usługi podrzędnej. Następnie szyfruje odpowiedzi zaplecza ruchu wychodzącego przed wysłaniem ich do klientów.

- **Protokół SSL serwera:** w przypadku usług zaplecza skonfigurowanych do obsługi protokołu HTTPS można skonfigurować big-IP do używania profilu SSL serwera. W przypadku profilu protokołu SSL serwera big-IP ponownie szyfruje żądanie klienta przed wysłaniem go do docelowej usługi zaplecza. Gdy serwer zwraca zaszyfrowaną odpowiedź, system BIG-IP odszyfrowuje i ponownie szyfruje odpowiedź przed wysłaniem jej do klienta za pośrednictwem skonfigurowanego profilu SSL klienta.

Aprowizowanie zarówno profilów SSL klienta, jak i serwera będzie mieć wstępnie skonfigurowany i gotowy do użycia we wszystkich scenariuszach SHA.

1. Na pasku nawigacyjnym po lewej stronie przejdź do menu **Zarządzanie** certyfikatami systemowymi Zarządzanie certyfikatami  >    >  **ruchu**  >  **Lista certyfikatów SSL Import**  >  

2. Z **listy rozwijanej Typ** importu wybierz pozycję **PKCS 12 (IIS)**

3. Podaj nazwę zaimportowanych certyfikatów, na przykład  `ContosoWilcardCert`

4. Wybierz **pozycję Wybierz plik,** aby przejść do certyfikatu sieci Web SSL, który ma nazwę podmiotu odpowiadającą sufiksowi domeny, który ma być dla opublikowanych usług.

5. Podaj hasło **zaimportowanych** certyfikatów, a następnie wybierz pozycję **Importuj**

6. Na pasku nawigacyjnym po lewej stronie przejdź do strony Klient SSL **lokalnych profilów**  >    >    >  **ruchu,** a następnie wybierz pozycję **Utwórz**

7. Na stronie **Nowy profil SSL** klienta podaj unikatową przyjazną nazwę nowego profilu SSL klienta i upewnij się, że profil nadrzędny jest ustawiony na **kliencil**

![Obraz przedstawia aktualizację big-ip](./media/f5ve-deployment-plan/client-ssl.png)

8. Zaznacz pole wyboru po prawej stronie w **wierszu Łańcuch kluczy certyfikatów** i wybierz pozycję **Dodaj**

9. Z trzech list rozwijanych wybierz certyfikat wieloznaczny zaimportowany bez hasła, a następnie wybierz pozycję **Dodaj**  >  **zakończone.**

![Obraz przedstawia symbol wieloznaczny update big-ip contoso](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Powtórz kroki 6–9, aby utworzyć profil **certyfikatu serwera SSL.** Na górnej wstążce wybierz pozycję **SSL** Server Create  >  **(Utwórz serwer**  >  **SSL).**

11. Na stronie **Nowy profil SSL** serwera podaj unikatową przyjazną nazwę dla nowego profilu SSL serwera i upewnij się, że profil nadrzędny jest ustawiony na **serverssl**

12. Zaznacz pole wyboru po prawej stronie wierszy Certificate (Certyfikat) i Key (Klucz), z listy rozwijanej wybierz zaimportowany certyfikat, a następnie pozycję **Finished (Zakończono).**

![Obraz przedstawia aktualizowanie wszystkich profilów serwera big-ip](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Jeśli nie możesz uzyskać certyfikatu SSL, możesz użyć zintegrowanego certyfikatu BIG-IP z podpisem własnym i certyfikatów SSL klienta. W przeglądarce zostanie wyświetlony błąd certyfikatu.

Ostatnim krokiem w przygotowaniu big ip dla SHA jest upewnienie się, że jest w stanie zlokalizować zasoby jego publikowania, a także usługi katalogowej, na których opiera się logowanie jednokrotne. Big-IP ma dwa źródła rozpoznawania nazw, począwszy od pliku lokalnego /.../hosts lub jeśli nie znaleziono rekordu, system BIG-IP używa dowolnej usługi DNS, za pomocą których został skonfigurowany. Metoda pliku hosts nie ma zastosowania do węzłów I pul APM, które używają nazwy FQDN.

1. W konfiguracji sieci Web przejdź do opcji **System**  >  **Configuration**  >  **Device**  >  **DNS.**

2. Na **liście serwerów wyszukiwania DNS** wprowadź adres IP serwera DNS środowisk

3. Wybierz **pozycję Dodaj**  >  **aktualizację**

W oddzielnym i opcjonalnym kroku można rozważyć konfigurację [protokołu LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) w celu uwierzytelniania administratorów systemu BIG-IP w u usługi AD zamiast zarządzania lokalnymi kontami BIG-IP.

## <a name="update-big-ip"></a>Aktualizowanie big-IP

Maszyna wirtualna BIG-IP-VM powinna zostać zaktualizowana w celu odblokowania wszystkich nowych funkcji objętych wskazówkami opartymi [na scenariuszach.](f5-aad-integration.md) Wersję TMOS systemów możesz sprawdzić, ustawiając wskaźnik myszy na BIG-IPs hosta w lewym górnym rogu strony głównej. Zaleca się uruchamianie w wersji 15.x lub więcej. Można ją pobrać ze strony [F5 download](https://downloads.f5.com/esd/productlines.jsp). Skorzystaj ze [wskazówek,](https://support.f5.com/csp/article/K34745165) aby zaktualizować główny system operacyjny (TMOS).

Jeśli aktualizowanie głównego serwera TMOS nie jest możliwe, rozważ przynajmniej uaktualnienie samej konfiguracji z przewodnikiem, korzystając z tych kroków.

1. Na karcie głównej w konfiguracji sieci Web BIG-IP przejdź do opcji **Konfiguracja** z  >  **przewodnikiem dostępu**

2. Na stronie **Konfiguracja z przewodnikiem** wybierz pozycję **Uaktualnij konfigurację z przewodnikiem**

![Na obrazie pokazano, jak zaktualizować plik big-ip](./media/f5ve-deployment-plan/update-big-ip.png)

3. W **oknie dialogowym Uaktualnij konfigurację** z przewodnikiem wybierz **pozycję Plik,** aby przekazać pobrany pakiet przypadków użycia, a następnie wybierz **pozycję Przekaż i zainstaluj**

4. Po zakończeniu uaktualniania wybierz pozycję **Kontynuuj.**

## <a name="backup-big-ip"></a>Kopia zapasowa BIG-IP

Po w pełni zaaprowizowanych systemach BIG-IP zalecamy wykonywanie pełnej kopii zapasowej jego konfiguracji:

1. Przejdź do  >  **archiwum systemowego**  >  **Utwórz**

2. Podaj unikatową **nazwę pliku i** włącz **szyfrowanie** przy użyciu hasła

3. Ustaw opcję **Klucze prywatne na** wartość **Uwzględnij,** aby również wrócić do kopii zapasowej urządzeń i certyfikatów SSL

4. Wybierz **pozycję Zakończono** i poczekaj na ukończenie procesu

5. Zostanie wyświetlony stan operacji ze stanem wyniku tworzenia kopii zapasowej. Wybierz przycisk **OK**.

6. Zapisz lokalnie archiwum zestawu konfiguracji użytkownika (UCS), wybierając link do kopii zapasowej, a następnie wybierz pozycję **Pobierz.**

Opcjonalnie możesz również wykonać kopię zapasową całego dysku systemowego przy użyciu migawek platformy [Azure,](../../virtual-machines/windows/snapshot-copy-managed-disk.md)które w przeciwieństwie do kopii zapasowej konfiguracji sieci Web zapewniłyby pewne awaryjne testowanie między wersjami TMOS lub powrót do nowego systemu.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Przywracanie big-IP

Przywracanie big ip jest zgodna z procedurą podobną do kopii zapasowej i może służyć do migrowania konfiguracji między maszynami wirtualnych BIG-IP. Szczegółowe informacje na temat obsługiwanych ścieżek uaktualniania należy zaobserwować przed zaimportowaniem kopii zapasowej.

1. Przejdź do **archiwów**  >  **systemowych**

2. Wybierz link do kopii zapasowej, którą  chcesz przywrócić, lub wybierz przycisk Przekaż, aby przejść do wcześniej zapisanego archiwum usług UCS, które nie znajduje się na liście

3. Podaj hasło dla kopii zapasowej i wybierz pozycję **Przywróć**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>W momencie pisania tego polecenia cmdlet AzVmSnapshot jest ograniczone do przywracania najnowszej migawki na podstawie daty. Migawki są przechowywane w katalogu głównym grupy zasobów maszyny wirtualnej. Należy pamiętać, że przywracanie migawek powoduje ponowne uruchomienie maszyny wirtualnej platformy Azure, więc czas na to uważnie.

## <a name="additional-resources"></a>Dodatkowe zasoby

-   [Resetowanie hasła BIG-IP VE na platformie Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Resetowanie hasła bez korzystania z portalu](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Zmiana karty sieciowej używanej do zarządzania przez system BIG-IP VE](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Informacje o trasach w konfiguracji pojedynczej karty sieciowej](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Następne kroki

Wybierz scenariusz [wdrażania i](f5-aad-integration.md) rozpocznij implementację.
