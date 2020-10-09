---
title: 'Samouczek: wdrażanie & konfigurowania zapory platformy Azure przy użyciu Azure Portal'
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować usługę Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 07/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d6f72231e84650a17850932979b43c21dd045f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069327"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal

Kontrolowanie dostępu do sieciowego ruchu wychodzącego jest ważną częścią ogólnego planu zabezpieczeń sieci. Na przykład możesz chcieć ograniczyć dostęp do witryn sieci Web. Można też ograniczyć liczbę wychodzących adresów IP i portów, do których można uzyskać dostęp.

Jednym ze sposobów kontrolowania dostępu do sieciowego ruchu wychodzącego z podsieci platformy Azure jest użycie usługi Azure Firewall. Za pomocą usługi Azure Firewall można skonfigurować następujące reguły:

* Reguły aplikacji, które definiują w pełni kwalifikowane nazwy domen (FQDN), do których można uzyskać dostęp z podsieci.
* Reguły sieci, które definiują adres źródłowy, protokół, port docelowy i adres docelowy.

Ruch sieciowy podlega skonfigurowanym regułom zapory podczas kierowania ruchu sieciowego do zapory jako bramy domyślnej podsieci.

W tym samouczku utworzysz uproszczoną pojedynczą sieć wirtualną z dwiema podsieciami w celu łatwego wdrożenia.

W przypadku wdrożeń produkcyjnych zaleca się [model Hub i szprych](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) , gdzie Zapora znajduje się w własnej sieci wirtualnej. Serwery obciążenia znajdują się w sieci wirtualnych komunikacji równorzędnej w tym samym regionie co co najmniej jedna podsieć.

* **AzureFirewallSubnet** — w tej podsieci znajduje się zapora.
* **Workload-SN** — w tej podsieci znajduje się serwer obciążeń. Ruch sieciowy tej podsieci przechodzi przez zaporę.

![Infrastruktura sieci samouczka](media/tutorial-firewall-deploy-portal/tutorial-network.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Skonfiguruj regułę aplikacji, aby zezwolić na dostęp do www.google.com
> * Konfigurowanie reguły sieci w celu umożliwienia dostępu do zewnętrznych serwerów DNS
> * Konfigurowanie reguły NAT zezwalającej na pulpit zdalny na serwerze testowym
> * Testowanie zapory

Jeśli chcesz, możesz wykonać kroki tego samouczka przy użyciu [programu Azure PowerShell](deploy-ps.md).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="set-up-the-network"></a>Konfigurowanie sieci

Najpierw utwórz grupę zasobów zawierającą zasoby wymagane do wdrożenia zapory. Następnie Utwórz sieć wirtualną, podsieci i serwer testowy.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów zawiera wszystkie zasoby wymagane w tym samouczku.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W menu Azure Portal wybierz pozycję **grupy zasobów** lub Wyszukaj, a następnie wybierz pozycję *grupy zasobów* z dowolnej strony. Następnie wybierz pozycję **Dodaj**.
3. W obszarze **Nazwa grupy zasobów**wprowadź polecenie *test-PD-RG*.
4. W polu **Subskrypcja** wybierz subskrypcję.
5. W polu **Lokalizacja grupy zasobów** wybierz lokalizację. Wszystkie inne zasoby, które tworzysz, muszą znajdować się w tej samej lokalizacji.
6. Wybierz przycisk **Utwórz**.

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Ta sieć wirtualna będzie zawierać trzy podsieci.

> [!NOTE]
> Rozmiar podsieci AzureFirewallSubnet to/26. Aby uzyskać więcej informacji o rozmiarze podsieci, zobacz [często zadawane pytania dotyczące zapory platformy Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
1. Wybierz **pozycję Sieć**  >  **Sieć wirtualna**.
2. W polu **Subskrypcja** wybierz subskrypcję.
3. W obszarze **Grupa zasobów**wybierz pozycję **test-PD-RG**.
4. W polu **Nazwa** wpisz wartość **Test-FW-VN**.
5. W polu **region**wybierz tę samą lokalizację, która została wcześniej użyta.
6. Wybierz pozycję **Dalej: adresy IP**.
7. W **polu przestrzeń adresowa IPv4**wpisz **10.0.0.0/16**.
8. W obszarze **podsieć**wybierz pozycję **domyślne**.
9. Dla **nazwy podsieci** wpisz **AzureFirewallSubnet**. Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
10. W obszarze **zakres adresów**wpisz **10.0.1.0/26**.
11. Wybierz pozycję **Zapisz**.

   Następnie Utwórz podsieć dla serwera obciążenia.

1. Wybierz pozycję **Dodaj podsieć**.
4. W obszarze **Nazwa podsieci**wpisz polecenie **obciążenia-SN**.
5. W obszarze **zakres adresów podsieci**wpisz **10.0.2.0/24**.
6. Wybierz pozycję **Dodaj**.
7. Wybierz pozycję **Przeglądanie + tworzenie**.
8. Wybierz pozycję **Utwórz**.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Teraz Utwórz maszynę wirtualną obciążenia i umieść ją w podsieci **obciążenia-SN** .

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **obliczenia** , a następnie pozycję **maszyna wirtualna**.
3. **System Windows Server 2016 Datacenter** znajduje się na liście polecanych.
4. Wprowadź poniższe wartości dla maszyny wirtualnej:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Grupa zasobów     |**Test-PD-RG**|
   |Nazwa maszyny wirtualnej     |**SRV — Work**|
   |Region     |Taki sam jak poprzedni|
   |Obraz|Windows Server 2019 Datacenter|
   |Nazwa użytkownika administratora     |Wpisz nazwę użytkownika|
   |Hasło     |Wpisz hasło|

4. W obszarze **reguły portów ruchu przychodzącego**, **publiczne porty przychodzące**, wybierz **Brak**.
6. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej: dyski**.
7. Zaakceptuj ustawienia domyślne dysku i wybierz pozycję **Dalej: sieć**.
8. Upewnij się, że dla sieci wirtualnej jest wybrana wartość **test-PD-VN** , a podsieć jest **obciążeniem-SN**.
9. W obszarze **publiczny adres IP**wybierz pozycję **Brak**.
11. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej: Zarządzanie**.
12. Wybierz pozycję **wyłączone** , aby wyłączyć diagnostykę rozruchu. Zaakceptuj inne ustawienia domyślne i wybierz pozycję **Recenzja + Utwórz**.
13. Przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz pozycję **Utwórz**.

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

Wdróż zaporę w sieci wirtualnej.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
2. W polu wyszukiwania wpisz **zaporę** i naciśnij klawisz **Enter**.
3. Wybierz opcję **Zapora** , a następnie wybierz pozycję **Utwórz**.
4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Subskrypcja     |\<your subscription\>|
   |Grupa zasobów     |**Test-PD-RG** |
   |Nazwa     |**Test-FW01**|
   |Lokalizacja     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Wybieranie sieci wirtualnej     |**Użyj istniejącej**: **test-PD-VN**|
   |Publiczny adres IP     |**Dodaj nowe**<br>**Nazwa**:  **PD-PIP**|

5. Wybierz pozycję **Przeglądanie + tworzenie**.
6. Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz** , aby utworzyć zaporę.

   Wdrożenie potrwa klika minut.
7. Po zakończeniu wdrażania przejdź do grupy zasobów **test-PD-RG** , a następnie wybierz zaporę **test-FW01** .
8. Zanotuj prywatne i publiczne adresy IP zapory. Te adresy będą używane później.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Na potrzeby podsieci **Workload-SN** skonfiguruj trasę domyślną ruchu wychodzącego, aby przechodziła przez zaporę.

1. W menu Azure Portal wybierz pozycję **wszystkie usługi** lub Wyszukaj i wybierz pozycję *wszystkie usługi* z dowolnej strony.
2. W obszarze **Sieć**wybierz pozycję **tabele tras**.
3. Wybierz pozycję **Dodaj**.
4. W polu **Nazwa** wpisz wartość **Firewall-route**.
5. W polu **Subskrypcja** wybierz subskrypcję.
6. W obszarze **Grupa zasobów**wybierz pozycję **test-PD-RG**.
7. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
8. Wybierz przycisk **Utwórz**.
9. Wybierz pozycję **Odśwież**, a następnie wybierz tabelę **Zapora trasy tras** .
10. Wybierz pozycję **podsieci** , a następnie wybierz pozycję **Skojarz**.
11. Wybierz pozycję **Virtual Network**  >  **test-PD-VN**.
12. W obszarze **podsieć**wybierz pozycję **obciążenie — SN**. Upewnij się, że dla tej trasy jest zaznaczona tylko podsieć **obciążenia-SN** , w przeciwnym razie Zapora nie będzie działała poprawnie.

13. Wybierz przycisk **OK**.
14. Wybierz pozycję **trasy** , a następnie wybierz pozycję **Dodaj**.
15. Dla **nazwy trasy**wpisz **PD-DG**.
16. W polu **Prefiks adresu** wpisz wartość **0.0.0.0/0**.
17. W obszarze **Typ następnego skoku** wybierz pozycję **Urządzenie wirtualne**.

    Usługa Azure Firewall to w rzeczywistości usługa zarządzana, ale urządzenie wirtualne działa w tej sytuacji.
18. W polu **Adres następnego skoku** wpisz wcześniej zanotowany prywatny adres IP zapory.
19. Wybierz przycisk **OK**.

## <a name="configure-an-application-rule"></a>Konfigurowanie reguły aplikacji

Jest to reguła aplikacji zezwalająca na dostęp wychodzący do `www.google.com` .

1. Otwórz polecenie **test-PD-RG**i wybierz zaporę **test-FW01** .
2. Na stronie **test-FW01** w obszarze **Ustawienia**wybierz pozycję **reguły**.
3. Wybierz kartę **Kolekcja reguł aplikacji** .
4. Wybierz pozycję **Dodaj kolekcję reguł aplikacji**.
5. W polu **Nazwa** wpisz wartość **App-Coll01**.
6. W polu **Priorytet** wpisz wartość **200**.
7. W polu **Akcja** wybierz opcję **Zezwalaj**.
8. W obszarze **reguły**, **docelowe nazwy FQDN**, w polu **Nazwa**wpisz **Zezwól-Google**.
9. W obszarze **Typ źródła**wybierz pozycję **adres IP**.
10. W obszarze **Źródło**wpisz **10.0.2.0/24**.
11. W polu **Protocol:port** wpisz wartość **http, https**.
12. W przypadku **docelowych nazw FQDN**wpisz **`www.google.com`**
13. Wybierz pozycję **Dodaj**.

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. Aby uzyskać więcej informacji, zobacz [Infrastrukturalne nazwy FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurowanie reguły sieci

Jest to reguła sieci, która umożliwia ruchowi wychodzącemu dostęp do dwóch adresów IP na porcie 53 (DNS).

1. Wybierz kartę **Kolekcja reguł sieci** .
2. Wybierz pozycję **Dodaj kolekcję reguł sieci**.
3. W polu **Nazwa** wpisz wartość **Net-Coll01**.
4. W polu **Priorytet** wpisz wartość **200**.
5. W polu **Akcja** wybierz opcję **Zezwalaj**.
6. W obszarze **reguły**, **adresy IP**, w polu **Nazwa**, wpisz **Allow-DNS**.
7. W polu **Protokół** wybierz **UDP**.
9. W obszarze **Typ źródła**wybierz pozycję **adres IP**.
1. W obszarze **Źródło**wpisz **10.0.2.0/24**.
2. W obszarze **Typ docelowy** wybierz pozycję **adres IP**.
3. W obszarze **adres docelowy**wpisz **209.244.0.3, 209.244.0.4**

   Są to publiczne serwery DNS obsługiwane przez usługę CenturyLink.
1. W polu **Porty docelowe** wpisz wartość **53**.
2. Wybierz pozycję **Dodaj**.

## <a name="configure-a-dnat-rule"></a>Konfigurowanie reguły DNAT

Ta reguła umożliwia połączenie pulpitu zdalnego z maszyną wirtualną Srv-Work za pomocą zapory.

1. Wybierz kartę **Kolekcja reguł translatora adresów sieciowych** .
2. Wybierz pozycję **Dodaj kolekcję reguł NAT**.
3. W obszarze **Nazwa**wpisz **protokół RDP**.
4. W polu **Priorytet** wpisz wartość **200**.
5. W obszarze **reguły**, w polu **Nazwa**wpisz **protokół RDP-translator adresów sieciowych**.
6. W polu **Protokół** wybierz **TCP**.
7. W obszarze **Typ źródła**wybierz pozycję **adres IP**.
8. Dla elementu **Source**wpisz **\*** .
9. W polu **adres docelowy**wpisz publiczny adres IP zapory.
10. W przypadku **portów docelowych**wpisz **3389**.
11. W polu **adres przetłumaczony**wpisz prywatny adres IP **SRV** .
12. W polu **Przekształcony port** wpisz **3389**.
13. Wybierz pozycję **Dodaj**.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Zmienianie podstawowego i pomocniczego adresu DNS dla interfejsu sieciowego **Srv-Work**

W celach testowych w tym samouczku Skonfiguruj podstawowe i pomocnicze adresy serwera DNS. To nie jest ogólne wymaganie dotyczące zapory platformy Azure.

1. W menu Azure Portal wybierz pozycję **grupy zasobów** lub Wyszukaj, a następnie wybierz pozycję *grupy zasobów* z dowolnej strony. Wybierz grupę zasobów **test-PD-RG** .
2. Wybierz interfejs sieciowy dla maszyny wirtualnej **SRV** .
3. W obszarze **Ustawienia**wybierz pozycję **serwery DNS**.
4. W obszarze **serwery DNS**wybierz opcję **niestandardowe**.
5. Wpisz wartość **209.244.0.3** w polu tekstowym **Dodaj serwer DNS**, a następnie wpisz wartość **209.244.0.4** w następnym polu tekstowym.
6. Wybierz pozycję **Zapisz**.
7. Uruchom ponownie maszynę wirtualną **Srv-Work**.

## <a name="test-the-firewall"></a>Testowanie zapory

Teraz Przetestuj zaporę, aby upewnić się, że działa zgodnie z oczekiwaniami.

1. Połącz pulpit zdalny z publicznym adresem IP zapory i zaloguj się do maszyny wirtualnej **SRV** . 
3. Otwórz program Internet Explorer i przejdź do `https://www.google.com`.
4. Wybierz pozycję **OK**  >  **Zamknij** na stronie Alerty zabezpieczeń programu Internet Explorer.

   Powinna zostać wyświetlona strona główna Google.

5. Przejdź na stronę `https://www.microsoft.com`.

   Dostęp powinien zostać zablokowany przez zaporę.

Teraz sprawdzono, że reguły zapory działają:

* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.
* Możesz rozpoznać nazwy DNS przy użyciu skonfigurowanego zewnętrznego serwera DNS.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **Test-FW-RG**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
