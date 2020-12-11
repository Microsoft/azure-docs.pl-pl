---
title: Bezpieczny dostęp hybrydowy usługi Azure AD za pomocą programu F5 Deployment Guide | Microsoft Docs
description: Samouczek pozwalający wdrożyć MASZYNę wirtualną z systemem F5 BIG-IP (VE) w usłudze Azure IaaS na potrzeby bezpiecznego dostępu hybrydowego
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
ms.openlocfilehash: 6c03009b08dcf33bf4b84bc91232af96e7ba2c71
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095189"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Samouczek pozwalający wdrożyć MASZYNę wirtualną z wirtualnym wydaniem F5 BIG-IP w usłudze Azure IaaS na potrzeby bezpiecznego dostępu hybrydowego

Ten samouczek przeprowadzi Cię przez proces wdrażania BIG-IP vitural Edition (VE) na platformie Azure IaaS. Po zakończeniu tego samouczka należy wykonać następujące działania:

- W pełni gotowa maszyna wirtualna BIG-IP (VM) do modelowania weryfikacji koncepcji bezpiecznego dostępu hybrydowego (SHA)

- Wystąpienie przejściowe służące do testowania nowych aktualizacji i poprawek systemu BIG-IP

## <a name="prerequisites"></a>Wymagania wstępne

Wcześniejsze, niepotrzebne środowisko F5 lub wiedza nie jest konieczna, zalecamy zapoznanie się z terminologią dotyczącą korzystania z [F5 Big-IP](https://www.f5.com/services/resources/glossary). Wdrożenie BIG-IP na platformie Azure dla algorytmu SHA wymaga:

- Płatna subskrypcja platformy Azure lub bezpłatna 12-miesięczna [subskrypcja wersji próbnej](https://azure.microsoft.com/free/).

- Dowolna z następujących jednostek SKU licencji BIG-IP

  - Pakiet F5 BIG-IP® Best

  - F5 BIG-IP — licencja autonomiczna Menedżera zasad (APM)™

  - Protokół F5 BIG-IP Access Manager — licencja dodatku™ (APM) na istniejącym standardzie BIG-IP F5® lokalnym Traffic Manager™ (LTM)
  
  - 90-dniowa [licencja na wersję próbną](https://www.f5.com/trial/big-ip-trial.php)Big-IP Full feature.

- Symbol wieloznaczny lub alternatywna nazwa podmiotu (SAN) do publikowania aplikacji sieci Web za pośrednictwem protokołu SSL (Secure Socket Layer). [Szyfrujmy](https://letsencrypt.org/) bezpłatnie certyfikat 90 dni na potrzeby testowania.

- Certyfikat SSL służący do zabezpieczania interfejsu zarządzania BIG-IPs. Może być używany certyfikat używany do publikowania aplikacji sieci Web, jeśli jego podmiot odnosi się do w pełni kwalifikowanej nazwy domeny (FQDN) BIG-IP. Na przykład certyfikat z symbolem wieloznacznym zdefiniowany przy użyciu podmiotu *. contoso.com będzie odpowiedni dla `https://big-ip-vm.contoso.com:8443`

Wdrożenia maszyn wirtualnych i podstawowe konfiguracje systemu trwają około 30 minut, w których platforma BIG-IP będzie gotowa do wdrożenia któregokolwiek z wymienionych w [tym miejscu](f5-aad-integration.md)scenariuszy SHA.

W przypadku testowania scenariuszy w tym samouczku założono, że BIG-IP zostanie wdrożony w grupie zasobów platformy Azure zawierającej Środowisko Active Directory (AD). Środowisko powinno zawierać maszyny wirtualne kontrolera domeny i hosta sieci Web (IIS). Jeśli serwery te znajdują się w innych lokalizacjach dla maszyny wirtualnej BIG-IP, to w przypadku wszystkich ról wymaganych do obsługi danego scenariusza należy zwrócić szczególną część informacji. Obsługiwane są również scenariusze, w których jest podłączona do innego środowiska za pośrednictwem połączenia sieci VPN.

Jeśli nie masz elementów wymienionych tutaj do testowania, możesz wdrożyć całe środowisko domeny usługi AD na platformie Azure przy użyciu tego [skryptu](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Kolekcja przykładowych aplikacji testowych może być również programowo wdrożona na hoście sieci Web usług IIS przy użyciu tej [automatyzacji skryptowej](https://github.com/jeevanbisht/DemoSuite).

>[!NOTE]
>[Azure Portal](https://portal.azure.com/#home) ciągle się zmienia, dlatego niektóre kroki opisane w tym samouczku mogą różnić się od rzeczywistego układu zaobserwowanego w Azure Portal.

## <a name="azure-deployment"></a>Wdrażanie na platformie Azure

BIG-IP można wdrożyć w różnych topologiach. Ten przewodnik koncentruje się na jednym wdrożeniu interfejsu sieciowego (NIC). Jeśli jednak wdrożenie BIG-IP wymaga wielu interfejsów sieciowych w celu zapewnienia wysokiej dostępności, podziału sieci lub ponad 1 GB przepływności, należy rozważyć użycie wstępnie skompilowanych [szablonów Azure Resource Manager (ARM)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)usługi F5's.

Wykonaj następujące zadania, aby wdrożyć BIG-IP VE z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Zaloguj się do [Azure Portal](https://portal.azure.com/#home) przy użyciu konta, które ma uprawnienia do tworzenia maszyn wirtualnych. Na przykład współautor

2. W górnym polu wyszukiwania wstążki wpisz **Marketplace**, a następnie **Enter**

3. Wpisz **F5** w filtrze portalu Marketplace, a następnie **naciśnij klawisz ENTER**

4. Wybierz pozycję **+ Dodaj** z górnej wstążki i wpisz **F5** w filtrze Marketplace, a następnie **naciśnij klawisz ENTER**

5. Wybierz kolejno pozycje **F5 Big-IP Virtual Edition (BYOL)**  >  **Wybierz plan oprogramowania**  >  **F5 Big-IP ve (BYOL, 2 lokalizacje rozruchowe)**

6. Wybierz przycisk **Utwórz**.

![Obraz przedstawia kroki umożliwiające wybranie planu oprogramowania](./media/f5ve-deployment-plan/software-plan.png)

7. Przejdź do menu **podstawowe** i użyj następujących ustawień

 |  Szczegóły projektu     |  Wartość     |
 |:-------|:--------|
 |Subskrypcja|Subskrypcja docelowa wdrożenia maszyny wirtualnej BIG-IP|
 |Grupa zasobów | Istniejąca Grupa zasobów platformy Azure zostanie wdrożona na maszynie wirtualnej BIG-IP lub utworzona. Powinna to być taka sama Grupa zasobów maszyn wirtualnych DC i IIS|
 | **Szczegóły wystąpienia**|  |
 |Nazwa maszyny wirtualnej| Przykład BIG-IP-VM |
 |Region | Ukierunkowane miejsce na platformę Azure dla usługi BIG-IP-VM |
 |Opcje dostępności| Włącz tylko w przypadku używania maszyny wirtualnej w środowisku produkcyjnym|
 |Obraz| F5 BIG-IP VE — wszystkie (BYOL, 2 lokalizacje rozruchowe)|
 |Wystąpienie usługi Azure Spot| Nie, ale możesz je włączyć, jeśli jest to konieczne |
 |Rozmiar| Minimalna specyfikacja powinna mieć wartość 2 procesorów wirtualnych vCPU i 8 GB pamięci|
 |**Konto administratora**|  |
 |Typ uwierzytelniania|Na razie wybierz pozycję hasło. Można przełączyć do pary kluczy później |
 |Nazwa użytkownika|Tożsamość, która zostanie utworzona jako konto lokalne BIG-IP do uzyskiwania dostępu do jego interfejsów zarządzania. Nazwa użytkownika uwzględnia wielkość liter.|
 |Hasło|Bezpieczny dostęp administratora przy użyciu silnego hasła|
 |**Reguły portów ruchu przychodzącego**|  |
 |Publiczne porty wejściowe|Brak|

8. Wybierz pozycję **Dalej: dyski** opuszczają wszystkie ustawienia domyślne, a następnie wybierz pozycję **Dalej: sieć**.

9. W menu **Sieć** Wypełnij te ustawienia.

 |Interfejs sieciowy|      Wartość |
 |:--------------|:----------------|
 |Sieć wirtualna|Ta sama sieć wirtualna platformy Azure używana przez maszyny wirtualne z kontrolerami domeny i IIS lub utwórz je|
 |Podsieć| Ta sama wewnętrzna podsieć platformy Azure jako maszyny wirtualne z kontrolerami domeny i IIS lub utwórz je|
 |Publiczny adres IP |  Brak|
 |Grupa zabezpieczeń sieci karty sieciowej| Wybierz opcję Brak, jeśli podsieć platformy Azure wybrana w poprzednich krokach jest już skojarzona z sieciową grupą zabezpieczeń (sieciowej grupy zabezpieczeń); w przeciwnym razie wybierz pozycję podstawowa|
 |Przyspiesz sieci| Wyłączone |
 |**Równoważenie obciążenia**|     |
 |Równoważenie obciążenia maszyny wirtualnej| Nie|

10. Wybierz pozycję **Dalej: Zarządzanie** i wypełnij te ustawienia.

 |Monitorowanie|    Wartość |
 |:---------|:-----|
 |Szczegółowe monitorowanie| Wyłączone|
 |Diagnostyka rozruchu|Włącz z niestandardowym kontem magazynu. Umożliwia nawiązanie połączenia z interfejsem BIG-IP Secure Shell (SSH) za pośrednictwem opcji konsola szeregowa w Azure Portal. Wybierz dowolne dostępne konto usługi Azure Storage|
 |**Tożsamość**|  |
 |Tożsamość zarządzana przypisana przez system|Wyłączone|
 |Usługa Azure Active Directory|Opcja BIG-IP nie obsługuje obecnie tej opcji|
 |**Automatyczne zamykanie**|    |
 |Włącz automatyczne zamykanie| Jeśli testujesz, rozważ ustawienie opcji "BIG-IP-VM" do wyłączania codziennie|

11. Wybierz pozycję **Dalej: Zaawansowane** opuszcza wszystkie ustawienia domyślne i wybierz **Następny: Tagi**.

12. Wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby PRZEJRZEĆ konfiguracje Big-IP-VM przed wybraniem pozycji **Utwórz** do rozpoczęcia wdrożenia.

13. Czas całkowitego wdrożenia maszyny wirtualnej BIG-IP jest zwykle 5 minut. Po zakończeniu nie zaznaczaj opcji **Przejdź do zasobu**, a następnie rozwiń menu po lewej stronie Azure Portal a następnie wybierz pozycję **grupy zasobów** , aby przejść do nowej sieci Web Big-IP-VM. Jeśli Tworzenie maszyny wirtualnej nie powiedzie się, wybierz pozycję **Wstecz** i **dalej**.

## <a name="network-configuration"></a>Konfiguracja sieci

Po pierwszym uruchomieniu maszyny wirtualnej BIG-IP jego karta sieciowa zostanie zainicjowana przy użyciu **podstawowego** prywatnego adresu IP wystawionego przez usługę Dynamic Host Configuration Protocol (DHCP) podsieci platformy Azure, z którą jest połączona. Ten adres IP będzie używany przez system operacyjny zarządzania ruchem (TMOS) BIG-IP w celu komunikowania się z:

- Komunikacja z innymi hostami i usługami

- Dostęp wychodzący do publicznego Internetu

- Dostęp przychodzący do interfejsów BIG-IPs Web config i zarządzania SSH

Udostępnienie jednego z tych interfejsów zarządzania w Internecie spowoduje zwiększenie BIG-IPs narażonych na ataki, w związku z czym BIG-IPs podstawowy adres IP nie został zainicjowany przy użyciu publicznego adresu IP podczas wdrażania. Zamiast tego zostanie zainicjowany dodatkowy wewnętrzny adres IP i skojarzony publiczny adres IP dla usług publikowania.
Mapowanie od 1 do 1 między publicznym adresem IP maszyny wirtualnej a prywatnym adresem IP umożliwia dostęp zewnętrzny do maszyny wirtualnej. Jednak reguła usługi Azure sieciowej grupy zabezpieczeń jest również wymagana w celu zezwolenia na ruch w podobny sposób jak Zapora.

Na diagramie przedstawiono wdrożenie pojedynczej karty sieciowej na platformie Azure, skonfigurowane z podstawowym adresem IP dla ogólnych operacji i zarządzania oraz oddzielne adresy IP serwera wirtualnego dla usług publikacji.
W tym rozmieszczeniu reguła sieciowej grupy zabezpieczeń umożliwia kierowanie ruchu zdalnego kierowanego do `intranet.contoso.com` publicznego adresu IP dla opublikowanej usługi przed przekazaniem ich do serwera wirtualnego Big-IP.

![Obraz pokazuje pojedyncze wdrożenie karty sieciowej ](./media/f5ve-deployment-plan/single-nic-deployment.png) Domyślnie, prywatne i publiczne adresy IP wydane dla maszyn wirtualnych platformy Azure są zawsze dynamiczne, więc prawdopodobnie zmienią się po każdym ponownym uruchomieniu maszyny wirtualnej. Unikaj przewidzianych problemów z łącznością, zmieniając adres IP zarządzania BIG-IPs na statyczny i wykonaj te same czynności w przypadku dodatkowych adresów IP używanych do publikowania usług.

1. Z menu wszystkie maszyny wirtualne z dużym adresem IP przejdź do pozycji **Ustawienia**  >  **Sieć**

2. W widoku sieć wybierz link z prawej strony **interfejsu sieciowego** .

![Obraz pokazuje konfigurację sieci](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>Nazwy maszyn wirtualnych są losowo generowane podczas wdrażania.

3. W okienku po lewej stronie wybierz pozycję **konfiguracje IP** , a następnie wybierz pozycję **ipconfig1** wiersz

4. Ustaw opcję **przypisanie adresu IP** na statyczną i w razie potrzeby zmień podstawowy adres IP dla maszyn wirtualnych Big-IP. Następnie wybierz pozycję **Zapisz** i Zamknij menu ipconfig1

>[!NOTE]
>Ten podstawowy adres IP będzie używany do nawiązywania połączenia z siecią BIG-IP-VM i zarządzania nią.

5. Wybierz pozycję **+ Dodaj** na górnej Wstążce i podaj nazwę pomocniczego prywatnego adresu IP, na przykład ipconfig2

6. Ustaw opcję **alokacji** ustawień prywatnych adresów IP na **statyczną**. Dostarczenie kolejnego lub mniejszego kolejnego adresu IP ułatwia zachowanie kolejności.

7. Ustaw publiczny adres IP na **Skojarz** i wybierz pozycję **Utwórz**

8. Podaj nazwę nowego publicznego adresu IP, na przykład BIG-IP-VM_ipconfig2_Public

9. Jeśli zostanie wyświetlony monit, ustaw **jednostkę SKU** na Standard

10. Jeśli zostanie wyświetlony monit, ustaw **warstwę** na **globalne** i

11. Ustaw opcję **przypisanie** na **statyczny**, a następnie wybierz przycisk **OK** dwa razy.

Twoja BIG-IP-VM jest teraz gotowa do skonfigurowania z:

- **Podstawowy prywatny adres IP**: dedykowany do zarządzania Big-IP-VM za pośrednictwem narzędzia Web config i protokołu SSH. Będzie on używany przez system BIG-IP jako własny **adres IP** do łączenia się z opublikowanymi usługami zaplecza. Ponadto łączy się on z usługami zewnętrznymi, takimi jak NTP, AD i LDAP.

- **Pomocniczy prywatny adres IP**: używany podczas tworzenia serwera wirtualnego usługi APM Big-IP do nasłuchiwania żądań przychodzących do opublikowanych usług.

- **Publiczny adres IP**: skojarzony z pomocniczym prywatnym adresem IP, umożliwia ruch z publicznego Internetu do serwera wirtualnego Big-IP dla opublikowanych usług

Przykład ilustruje relację od 1 do 1 między publicznym i prywatnym adresy IP maszyny wirtualnej. Karta sieciowa maszyny wirtualnej platformy Azure może mieć tylko jeden podstawowy adres IP, a wszystkie dodatkowo tworzone adresy IP są zawsze określane jako pomocnicze.

>[!NOTE]
>Wymagane są dodatkowe mapowania adresów IP na potrzeby publikowania usług BIG-IP.

![Ten obraz przedstawia wszystkie pomocnicze adresy IP](./media/f5ve-deployment-plan/secondary-ips.png)

Aby zaimplementować Algorytm SHA przy użyciu konfiguracji z **przewodnikiem dostępu** Big-IP, należy powtórzyć kroki 5-11, aby utworzyć dodatkowe pary prywatnych i publicznych adresów IP dla każdej dodatkowej usługi, którą planujesz opublikować za pośrednictwem programu APM Big-IP. Takie samo podejście może być również używane w przypadku publikowania usług przy użyciu **zaawansowanej konfiguracji** BIG-IPs. Jednak w tym scenariuszu masz możliwość uniknięcia publicznego biura adresów IP przy użyciu konfiguracji [wskaźnika nazwy serwera (SNI)](https://support.f5.com/csp/#/article/K13452) . W tej konfiguracji serwer wirtualny BIG-IP akceptuje cały odbierany ruch klienta i kieruje go do miejsca docelowego.

## <a name="dns-configuration"></a>Konfiguracja usługi DNS

Konieczna jest konfiguracja usługi DNS dla klientów w celu rozpoznawania opublikowanych usług agenta SHA dla publicznych adresów IP BIG-IP-VM.

W poniższych krokach przyjęto założenie, że strefa DNS domeny publicznej używanej przez usługi SHA jest zarządzana na platformie Azure. Jednak te same zasady DNS tworzenia rekordu lokalizatora nadal mają zastosowanie niezależnie od tego, gdzie strefa DNS jest zarządzana.

1. Jeśli jeszcze tego nie zrobiono, rozwiń menu po lewej stronie portalu i przejdź do sieci Web BIG-IP-VM przy użyciu opcji **grupy zasobów**

2. Z menu wszystkie maszyny wirtualne z dużym adresem IP przejdź do pozycji **Ustawienia**  >  **Sieć**

3. W widoku sieci BIG-IP-VM wybierz pierwszy pomocniczy adres IP z listy rozwijanej Konfiguracja protokołu IP i wybierz link do **publicznego adresu IP karty sieciowej**

![Zrzut ekranu przedstawiający publiczny adres IP karty sieciowej](./media/f5ve-deployment-plan/networking.png)

4. Poniżej sekcji **Ustawienia** w okienku po lewej stronie wybierz pozycję **Konfiguracja** , aby wyświetlić menu właściwości publicznego adresu IP i DNS dla wybranego pomocniczego adresu IP

5. Wybierz i **Utwórz** rekord aliasu i wybierz **strefę DNS** z listy rozwijanej. Jeśli strefa DNS jeszcze nie istnieje, może być ona zarządzana poza platformą Azure lub można ją utworzyć dla sufiksu domeny, który został zweryfikowany w usłudze Azure AD.

6. Poniższe szczegóły służą do tworzenia pierwszego rekordu aliasu DNS:

 | Pole | Wartość |
 |:-------|:-----------|
 |Subskrypcja| Ta sama subskrypcja co BIG-IP-VM|
 |Strefa DNS| Strefa DNS, która jest autorytatywna dla zweryfikowanego sufiksu domeny, który będzie używany przez opublikowane witryny sieci Web, na przykład www.contoso.com |
 |Nazwa | Określona nazwa hosta zostanie rozpoznana jako publiczny adres IP skojarzony z wybranym pomocniczym adresem IP. Upewnij się, że zdefiniowano poprawne mapowania DNS na adres IP. Zobacz ostatni obraz w sekcji konfiguracje sieciowe, na przykład intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |Jednostki czasu wygaśnięcia | Godziny |

7. Wybierz pozycję **Utwórz** dla platformy Azure, aby zapisać te ustawienia w publicznym systemie DNS.

8. Pozostaw **etykietę nazwy DNS (opcjonalnie)** i wybierz pozycję **Zapisz** przed zamknięciem publicznego adresu IP.

9. Powtórz kroki od 1 do 6, aby utworzyć dodatkowe rekordy DNS dla każdej usługi planowanej do opublikowania przy użyciu konfiguracji z przewodnikiem BIG-IP.

  W przypadku rekordów DNS na miejscu można użyć dowolnego narzędzia online, takiego jak [kontroler DNS](https://dnschecker.org/) , aby sprawdzić, czy utworzony rekord został pomyślnie rozpropagowany przez wszystkie globalne publiczne serwery DNS.

  W przypadku zarządzania przestrzenią nazw domeny DNS przy użyciu dostawcy zewnętrznego, takiego jak [GoDaddy](https://www.godaddy.com/), należy utworzyć rekordy przy użyciu własnej funkcji zarządzania DNS.

>[!NOTE]
>Można również użyć lokalnego pliku hosts w przypadku testowania i często przełączania rekordów DNS. Do pliku localhosts na komputerze z systemem Windows można uzyskać dostęp przez naciśnięcie przycisku Win + R na klawiaturze i przesłanie **sterowników** programu Word w polu Run (uruchamianie). Pamiętaj, że rekord localhost będzie zawierać tylko rozpoznawanie nazw DNS dla lokalnego komputera, a nie innych klientów.

## <a name="client-traffic"></a>Ruch klienta

Domyślnie usługa Azure sieci wirtualnych i skojarzone podsieci są sieciami prywatnymi, które nie mogą odbierać ruchu internetowego. Karta sieciowa BIG-IP-VM powinna być dołączona do sieciowej grupy zabezpieczeń określonej podczas wdrażania. W przypadku zewnętrznego ruchu internetowego w celu połączenia z maszyną wirtualną BIG-IP należy zdefiniować regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego zezwalającą na porty 443 (HTTPS) i 80 (HTTP) z publicznego Internetu.

1. Z głównego menu **przeglądu** dla maszyn wirtualnych Big-IP wybierz pozycję **Sieć**

2. Wybierz pozycję **Dodaj** regułę ruchu przychodzącego, aby wprowadzić następujące właściwości reguły sieciowej grupy zabezpieczeń:

 |     Pole   |   Wartość        |
 |:------------|:------------|
 |Źródło| Dowolne|
 |Zakresy portów źródłowych| *|
 |Docelowe adresy IP|Rozdzielana przecinkami lista wszystkich dodatkowych prywatnych adresów IP w formacie BIG-IPv4-VM|
 |Porty docelowe| 80 443|
 |Protokół| TCP |
 |Akcja| Zezwalaj|
 |Priorytet|Najniższa dostępna wartość z zakresu od 100 do 4096|
 |Nazwa | Nazwa opisowa, na przykład: `BIG-IP-VM_Web_Services_80_443`|

3. Wybierz pozycję **Dodaj** , aby zatwierdzić zmiany, a następnie zamknij menu **Sieć** .

Ruch HTTP i HTTPS z dowolnej lokalizacji będzie teraz mógł nawiązać połączenie z wszystkimi interfejsami pomocniczymi BIG-IP-VM. Zezwolenie na port 80 jest przydatne w przypadku, gdy program APM BIG-IP przekieruje użytkowników z protokołu HTTP na HTTPS. Tę regułę można edytować w celu dodania lub usunięcia docelowych adresów IP, w razie potrzeby.

## <a name="manage-big-ip"></a>Zarządzanie BIG-IP

System BIG-IP jest administrowany za pośrednictwem interfejsu użytkownika konfiguracji sieci Web, do którego można uzyskać dostęp przy użyciu jednej z następujących zalecanych metod:

- Z komputera w sieci wewnętrznej BIG-IP

- Z klienta VPN podłączonego do sieci wewnętrznej BIG-IP-VM

- Opublikowano za pośrednictwem [usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

Aby można było kontynuować pracę z pozostałymi konfiguracjami, należy podjąć decyzję dotyczącą najbardziej odpowiedniej metody. W razie potrzeby można połączyć się bezpośrednio z Internetem za pośrednictwem Internetu, konfigurując podstawowy adres IP BIG-IP z publicznym adresem IP. Następnie Dodaj regułę sieciowej grupy zabezpieczeń, aby zezwolić na ruch 8443 do tego podstawowego adresu IP. Upewnij się, że źródło jest ograniczone do własnego zaufanego adresu IP. w przeciwnym razie każdy będzie mógł nawiązać połączenie.

Gdy wszystko będzie gotowe, potwierdź, że możesz nawiązać połączenie z konfiguracją sieci Web BIG-IP i zaloguj się przy użyciu poświadczeń określonych podczas wdrażania maszyny wirtualnej:

- Jeśli łączysz się z maszyny wirtualnej w sieci wewnętrznej lub za pośrednictwem sieci VPN, Połącz się bezpośrednio z BIG-IPs podstawowym adresem IP i portem konfiguracji sieci Web. Na przykład `https://<BIG-IP-VM_Primary_IP:8443`. Przeglądarka wyświetli monit o niebezpieczeństwo połączenia, ale możesz zignorować ten monit do momentu skonfigurowania BIG-IP. Jeśli przeglądarka zablokuje dostęp, wyczyść jej pamięć podręczną i spróbuj ponownie.

- Jeśli opublikowano konfigurację sieci Web za pośrednictwem serwera proxy aplikacji, użyj adresu URL zdefiniowanego w celu uzyskania dostępu do konfiguracji sieci Web zewnętrznie, bez dołączania portu, na przykład `https://big-ip-vm.contoso.com` . Wewnętrzny adres URL musi być zdefiniowany przy użyciu portu konfiguracji sieci Web, na przykład `https://big-ip-vm.contoso.com:8443` 

System BIG-IP może być również zarządzany za pośrednictwem własnego środowiska SSH, który jest zazwyczaj używany w przypadku zadań wiersza polecenia (CLI) i dostępu na poziomie głównym. Istnieje kilka opcji nawiązywania połączenia z interfejsem wiersza polecenia, w tym:

- [Usługa Azure bastionu](https://docs.microsoft.com/azure/bastion/bastion-overview): umożliwia szybkie i bezpieczne nawiązywanie połączeń z dowolną maszyną wirtualną w sieci wirtualnej, z dowolnej lokalizacji

- Nawiązywanie połączenia bezpośrednio za pośrednictwem klienta SSH, takiego jak podano przez podejście JIT

- Konsola szeregowa: oferowana w dolnej części sekcji Obsługa i rozwiązywanie problemów w menu maszyny wirtualne w portalu. Nie obsługuje transferów plików.

- Podobnie jak w przypadku konfiguracji sieci Web, można także połączyć się bezpośrednio z interfejsem wiersza polecenia z Internetu przez skonfigurowanie podstawowego adresu IP BIG-IP przy użyciu publicznego adresu IP i dodanie reguły sieciowej grupy zabezpieczeń w celu zezwolenia na ruch SSH. W przypadku korzystania z tej metody należy pamiętać o ograniczeniu źródła do własnego zaufanego adresu IP.  

## <a name="big-ip-license"></a>Licencja BIG-IP

Aby można było skonfigurować program do publikowania usług i SHA, należy aktywować system BIG-IP i zainicjować go przy użyciu modułu APM.

1. Zaloguj się ponownie do konfiguracji sieci Web i na stronie **Właściwości ogólne** wybierz pozycję **Aktywuj**

2. W polu **klucz rejestracji podstawowej** wprowadź klucz z uwzględnieniem wielkości liter dostarczony przez F5

3. Pozostaw **metodę aktywacji** ustawioną na wartość **automatyczny** i wybierz pozycję **dalej**. wartość Big-IP spowoduje zweryfikowanie licencji i wyświetlenie umowy licencyjnej użytkownika oprogramowania (EULA).

4. Wybierz opcję **Zaakceptuj** i poczekaj na zakończenie aktywacji, przed wybraniem opcji **Kontynuuj**.

5. Zaloguj się ponownie i w dolnej części strony podsumowania licencji wybierz pozycję **dalej**. W ramach BIG-IP zostanie wyświetlona lista modułów, które udostępniają różne funkcje wymagane przez algorytm SHA. Jeśli ta wartość nie jest widoczna, z karty głównej wybierz opcję   >  **Inicjowanie obsługi zasobów** systemu.

6. Sprawdzanie kolumny aprowizacji dla zasad dostępu (APM)

![Obraz przedstawia Inicjowanie obsługi dostępu](./media/f5ve-deployment-plan/access-provisioning.png)

7. Wybierz pozycję **Prześlij** i zaakceptuj ostrzeżenie

8. Poczekaj, aż zakończysz pracę z pełnym adresem IP, aby zakończyć nowe funkcje. Może to potrwać kilka razy przed pełnym zainicjowaniem. 

9. Po wybraniu opcji **Kontynuuj** i z karty **informacje** wybierz pozycję **Uruchom narzędzie Instalatora** .

>[!IMPORTANT]
>Licencje F5 są ograniczone do wykorzystania przez pojedyncze wystąpienie BIG-IP VE w dowolnym momencie. Może być konieczne przeprowadzenie migracji licencji z jednego wystąpienia do drugiego. w przeciwnym razie należy pamiętać, aby [odwołać](https://support.f5.com/csp/article/K41458656) licencję próbną w aktywnym wystąpieniu przed jego wycofaniem. w związku z tym licencja zostanie trwale utracona.

## <a name="provision-big-ip"></a>Udostępnianie BIG-IP

Zabezpieczanie ruchu zarządzania do i z BIG-IPs Web config jest nadrzędne. Skonfiguruj certyfikat zarządzania urządzeniami, aby ułatwić ochronę kanału konfiguracji sieci Web przed naruszeniem zabezpieczeń.

1. Na pasku nawigacyjnym po lewej stronie wybierz kolejno pozycje **system**  >  **Zarządzanie certyfikatem**  >  **ruch zarządzanie certyfikatami** certyfikat  >  **SSL**  >  **Importuj**

2. Z listy rozwijanej **Typ importu** wybierz pozycję **PKCS 12 (IIS)** i **Wybierz pozycję plik**. Zlokalizuj certyfikat sieci Web SSL, który ma nazwę podmiotu lub sieć SAN, która będzie obejmować daną nazwę FQDN, w ciągu następnych kilku kroków przypiszesz maszynę wirtualną BIG-IP.

3. Podaj hasło dla certyfikatu, a następnie wybierz pozycję **Importuj** .

4. Na pasku nawigacyjnym po lewej stronie przejdź do pozycji Platforma **systemowa**  >  

5. Skonfiguruj opcję BIG-IP-VM przy użyciu w pełni kwalifikowanej nazwy hosta i strefy czasowej dla środowiska, a następnie **zaktualizuj**

![Obraz pokazuje ogólne właściwości](./media/f5ve-deployment-plan/general-properties.png)

6. Na pasku nawigacyjnym po lewej stronie wybierz kolejno pozycje konfiguracja **systemu**  >    >    >  **NTP**

7. Określ niezawodne źródło NTP i wybierz polecenie **Dodaj**, a następnie pozycję **Update**. Na przykład `time.windows.com`

Teraz musisz mieć rekord DNS, aby rozwiązać BIG-IPs FQDN określone w poprzednich krokach do jego podstawowego prywatnego adresu IP. Rekord należy dodać do wewnętrznego systemu DNS środowiska lub do pliku localhost komputera, który zostanie użyty do nawiązania połączenia z konfiguracją sieci Web BIG-IP. W obu przypadkach ostrzeżenie przeglądarki nie powinno być już wyświetlane w przypadku bezpośredniego połączenia z konfiguracją sieci Web. Oznacza to, nie za pośrednictwem serwera proxy aplikacji lub innego zwrotnego serwera proxy.

## <a name="ssl-profile"></a>Profil SSL

Jako zwrotny serwer proxy system BIG-IP może działać jako prosta Usługa przekazywania, w przeciwnym razie jako przezroczysty serwer proxy lub pełny serwer proxy, który aktywnie uczestniczy w wymianie między klientami a serwerami.
Pełny serwer proxy tworzy dwa odrębne połączenia: połączenie klienta TCP frontonu z oddzielnym połączeniem z serwerem TCP zaplecza, połączone przez przerwę nieprzerwaną w środku. Klienci łączą się z odbiornikiem serwera proxy na jednym końcu, w innym przypadku, nazywanym **serwerem wirtualnym**, a serwer proxy nawiązuje oddzielne, niezależne połączenie z serwerem wewnętrznej bazy danych. Jest to dwukierunkowa po obu stronach.
W tym pełnym trybie serwera proxy, system F5 BIG-IP jest w stanie sprawdzać ruch i w związku z tym możliwe jest również korzystanie z żądań i odpowiedzi. Niektóre funkcje, takie jak równoważenie obciążenia i Optymalizacja wydajności sieci Web, a także bardziej zaawansowane usługi zarządzania ruchem, takie jak zabezpieczenia warstwy aplikacji, przyspieszanie sieci Web, routing stron i bezpieczny dostęp zdalny, polegają na tej funkcji.
Podczas publikowania usług opartych na protokole SSL proces odszyfrowywania i szyfrowania ruchu między klientami a usługami zaplecza jest obsługiwany przez profile BIG Połączenie SSL z adresu IP.

Istnieją dwa typy profilów:

- **SSL klienta**: najbardziej typowym sposobem skonfigurowania systemu Big-IP do publikowania usług wewnętrznych przy użyciu protokołu SSL jest utworzenie profilu SSL klienta. W przypadku profilu SSL klienta System BIG-IP może odszyfrować przychodzące żądania klientów przed wysłaniem ich do usługi podrzędnej. Następnie szyfruje wychodzące odpowiedzi wewnętrznej bazy danych przed wysłaniem ich do klientów.

- **SSL serwera**: dla usług zaplecza skonfigurowanych dla protokołu HTTPS można skonfigurować Big-IP, aby używał profilu SSL serwera. W przypadku profilu SSL serwera, BIG-IP szyfruje żądanie klienta przed wysłaniem go do docelowej usługi zaplecza. Gdy serwer zwraca zaszyfrowaną odpowiedź, system BIG-IP odszyfrowuje i ponownie szyfruje odpowiedź przed wysłaniem jej do klienta za pośrednictwem skonfigurowanego profilu SSL klienta.

Obsługa administracyjna profilów SSL klienta i serwera będzie mieć wstępnie skonfigurowany i gotowy do użycia dla wszystkich scenariuszy SHA.

1. Na pasku nawigacyjnym po lewej stronie wybierz kolejno pozycje **system**  >  **Zarządzanie certyfikatem**  >  **ruch zarządzanie certyfikatami** certyfikat  >  **SSL**  >  **Importuj**

2. Z listy rozwijanej **Typ importu** wybierz pozycję **PKCS 12 (IIS).**

3. Podaj nazwę zaimportowanego certyfikatu, na przykład  `ContosoWilcardCert`

4. Wybierz pozycję **Wybierz plik** , aby przejść do certyfikatu sieci Web SSL, którego nazwa podmiotu odnosi się do sufiksu domeny, który ma być używany dla opublikowanych usług

5. Podaj **hasło** dla zaimportowanego certyfikatu, a następnie wybierz pozycję **Importuj** .

6. Na pasku nawigacyjnym po lewej stronie przejdź do pozycji **lokalne**  >  **Profile** ruchu  >  **SSL**  >  **klienta** , a następnie wybierz pozycję **Utwórz** .

7. Na stronie **Nowy profil SSL klienta** Podaj unikatową przyjazną nazwę nowego profilu SSL klienta i upewnij się, że profil nadrzędny jest ustawiony na **clientssl**

![Obraz przedstawia aktualizację Big-IP](./media/f5ve-deployment-plan/client-ssl.png)

8. Zaznacz pole wyboru daleko w prawo w wierszu **łańcucha kluczy certyfikatów** i wybierz pozycję **Dodaj** .

9. Z trzech listy rozwijanej wybierz certyfikat z symbolami wieloznacznymi zaimportowany bez hasła, a następnie wybierz pozycję **Dodaj**  >  **Zakończono**.

![Obraz przedstawia aktualizację Big-IP contoso symbol wieloznaczny](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Powtórz kroki 6-9, aby utworzyć **profil certyfikatu serwera SSL**. Na najwyższej wstążce wybierz pozycję serwer **SSL**  >    >  **Utwórz**.

11. Na stronie **Nowy profil protokołu SSL serwera** Podaj unikatową przyjazną nazwę nowego profilu protokołu SSL serwera i upewnij się, że profil nadrzędny jest ustawiony na **serverssl**

12. Zaznacz pole wyboru daleko w prawo dla certyfikatu i wierszy kluczy, a następnie z listy rozwijanej wybierz zaimportowany certyfikat, a następnie pozycję **gotowe**.

![Obraz pokazuje wartość Aktualizuj cały serwer IP wszystkie profile](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Nie Despair Jeśli nie możesz uzyskać certyfikatu SSL, możesz użyć zintegrowanego z własnym certyfikatem serwera BIG-IP i certyfikatów SSL klienta. Po prostu zobaczysz błąd certyfikatu w przeglądarce.

Jednym z końcowych etapów przygotowywania pełnego adresu IP dla algorytmu SHA jest upewnienie się, że istnieje możliwość zlokalizowania zasobów publikowanych przez program, a także usługi katalogowej, na której opiera się Logowanie jednokrotne. BIG-IP ma dwa źródła rozpoznawania nazw, rozpoczynając od jego lokalnego lub.../hosty, lub jeśli nie odnaleziono rekordu, system BIG-IP używa dowolnej usługi DNS, z którą została skonfigurowana. Metoda pliku hosts nie ma zastosowania do węzłów i pul APM, które używają nazwy FQDN.

1. W konfiguracji sieci Web przejdź do pozycji **Konfiguracja systemu system**  >    >    >  **DNS**

2. Na **liście serwer wyszukiwania DNS** wpisz adres IP serwera DNS środowiska

3. Wybierz pozycję **Dodaj**  >  **aktualizację**

W ramach oddzielnego i opcjonalnego kroku można rozważyć [konfigurację LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) , aby uwierzytelnić administratorów systemu Big-IP w odniesieniu do usługi AD zamiast zarządzać lokalnymi kontami Big-IP.

## <a name="update-big-ip"></a>Aktualizowanie BIG-IP

Aby odblokować wszystkie nowe funkcje omówione w [przewodnikach opartych na scenariuszu](f5-aad-integration.md), należy zaktualizować całą maszynę wirtualną. Możesz sprawdzić, czy systemy TMOS wersji, aktywując wskaźnik myszy nad BIG-IPs nazwą hosta w lewym górnym rogu strony głównej. Zaleca się, aby można było uruchomić program 15. x lub nowszy, co pozwala uzyskać możliwość pobrania z usługi [F5](https://downloads.f5.com/esd/productlines.jsp). Skorzystaj z [wskazówek](https://support.f5.com/csp/article/K34745165) , aby zaktualizować główny system operacyjny systemu (TMOS).

Jeśli nie można zaktualizować głównego TMOS, należy rozważyć co najmniej uaktualnienie konfiguracji z przewodnikiem, wykonując poniższe kroki.

1. Z poziomu karty głównej w pliku Web config Big-IP **Przejdź do**  >  **konfiguracji z przewodnikiem**

2. Na stronie **Konfiguracja z przewodnikiem** wybierz opcję **Uaktualnij konfigurację z przewodnikiem**

![Obraz pokazuje, jak zaktualizować Big-IP](./media/f5ve-deployment-plan/update-big-ip.png)

3. W oknie dialogowym **Uaktualnij konfigurację z przewodnikiem** **Wybierz pozycję plik** , aby przekazać pobrany pakiet przypadków użycia i wybierz pozycję **Przekaż i zainstaluj** .

4. Po zakończeniu uaktualniania wybierz pozycję **Kontynuuj**.

## <a name="backup-big-ip"></a>Utwórz kopię zapasową BIG-IP

Gdy system BIG-IP jest obecnie w pełni zainicjowany, zalecamy utworzenie pełnej kopii zapasowej swojej konfiguracji:

1. Przejdź do   >  **archiwów** systemu  >  **Tworzenie**

2. Podaj unikatową **nazwę pliku** i Włącz **szyfrowanie** przy użyciu hasła

3. Ustaw opcję **klucze prywatne** , aby **uwzględnić** również kopie zapasowe urządzeń i certyfikatów SSL

4. Wybierz pozycję **gotowe** i poczekaj na zakończenie procesu

5. Zostanie wyświetlony stan operacji dostarczającej stan wyniku kopii zapasowej. Wybierz przycisk **OK**.

6. Zapisz archiwum zestawu konfiguracyjnego (UCS) użytkownika lokalnie, wybierając link do kopii zapasowej i wybierz pozycję **Pobierz**.

Opcjonalnie można również utworzyć kopię zapasową całego dysku systemowego za pomocą [migawek platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk), które w przeciwieństwie do kopii zapasowej w konfiguracji sieci Web spowodują pewne sytuacje awaryjne testowania między wersjami TMOS lub przywrócenie do nowego systemu.

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

## <a name="restore-big-ip"></a>Przywróć BIG-IP

Przywrócenie BIG-IP następuje w ramach podobnej procedury do kopii zapasowej i może być również używane do migrowania konfiguracji między maszynami wirtualnymi BIG-IP. Przed zaimportowaniem kopii zapasowej należy przestrzegać szczegółowych informacji o obsługiwanych ścieżkach uaktualniania.

1. Przejdź do   >  **archiwów** systemu

2. Wybierz łącze do kopii zapasowej, którą chcesz przywrócić, lub wybierz przycisk **Przekaż** , aby przejść do wcześniej zapisanego archiwum UCS, którego nie ma na liście

3. Podaj hasło do kopii zapasowej i wybierz pozycję **Przywróć**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>W momencie zapisu polecenie cmdlet AzVmSnapshot jest ograniczone do przywracania najnowszej migawki na podstawie daty. Migawki są przechowywane w folderze głównym grupy zasobów maszyny wirtualnej. Należy pamiętać, że przywracanie migawek ponownie uruchamia maszynę wirtualną platformy Azure, więc należy to uważnie.

## <a name="additional-resources"></a>Dodatkowe zasoby

-   [Zresetuj hasło BIG-IP VE na platformie Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Zresetuj hasło bez korzystania z portalu](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Zmiana karty sieciowej używanej do zarządzania BIG-IP VE](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Informacje o trasach w jednej konfiguracji karty sieciowej](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Następne kroki

Wybierz [scenariusz wdrażania](f5-aad-integration.md) i Rozpocznij wdrożenie.