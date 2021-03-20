---
title: 'Samouczek: Zabezpieczanie koncentratora wirtualnego przy użyciu Menedżera zapory platformy Azure'
description: W tym samouczku dowiesz się, jak zabezpieczyć koncentrator wirtualny za pomocą Menedżera zapory platformy Azure przy użyciu Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: victorh
ms.openlocfilehash: 9d1e2d257074555e7a2e78930e1f9be6cd4d90fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89536006"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Samouczek: Zabezpieczanie koncentratora wirtualnego przy użyciu Menedżera zapory platformy Azure

Korzystając z Menedżera zapory platformy Azure, można utworzyć zabezpieczone centra wirtualne, aby zabezpieczyć ruch sieciowy w chmurze przeznaczony dla prywatnych adresów IP, platformy Azure PaaS i Internetu. Routing ruchu do zapory jest zautomatyzowany, dlatego nie ma potrzeby tworzenia tras zdefiniowanych przez użytkownika (UDR).

![Zabezpieczanie sieci w chmurze](media/secure-cloud-network/secure-cloud-network.png)

Menedżer zapory obsługuje również architekturę sieci wirtualnej centrum. Aby zapoznać się z porównaniem bezpiecznych typów architektury sieci wirtualnych i koncentratorów wirtualnych, zobacz [co to są opcje architektury usługi Azure firewall Manager?](vhubs-and-vnets.md)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej będącej szprychą
> * Tworzenie bezpiecznego centrum wirtualnego
> * Łączenie sieci wirtualnych Hub i gwiazdy
> * Kierowanie ruchu do centrum
> * Wdrażanie serwerów
> * Tworzenie zasad zapory i zabezpieczanie centrum
> * Testowanie zapory

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-hub-and-spoke-architecture"></a>Tworzenie architektury gwiazdy

Najpierw utwórz sieci wirtualne szprychy, w których można umieścić serwery.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Tworzenie dwóch sieci wirtualnych szprych i podsieci

Każda z tych sieci wirtualnych będzie zawierać w nich serwer obciążenia i będzie chroniona przez zaporę.

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Sieć** wybierz pozycję **Sieć wirtualna**.
2. W polu **Subskrypcja** wybierz subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, a następnie wpisz **PD-Manager** dla nazwy i wybierz **przycisk OK**.
2. W obszarze **Nazwa** wpisz polecenie **szprych-01**.
3. W **obszarze region** wybierz pozycję **(US) Wschodnie stany USA**.
4. Wybierz pozycję **Dalej: adresy IP**.
1. W **polu przestrzeń adresowa** wpisz **10.1.0.0/16**.
3. Wybierz pozycję **Dodaj podsieć**.
4. Typ **obciążenia-01-SN**.
5. W obszarze **zakres adresów podsieci** wpisz **10.1.1.0/24**.
6. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
2. Wybierz pozycję **Utwórz**.

Powtórz tę procedurę, aby utworzyć kolejną podobną sieć wirtualną:

Nazwa: **szprychy-02**<br>
Przestrzeń adresowa: **10.2.0.0/16**<br>
Nazwa podsieci: **obciążenie-02-SN**<br>
Zakres adresów podsieci: **10.2.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Tworzenie bezpiecznego centrum wirtualnego

Utwórz bezpieczne centrum wirtualne przy użyciu Menedżera zapory.

1. Na stronie głównej Azure Portal wybierz pozycję **wszystkie usługi**.
2. W polu wyszukiwania wpisz **Menedżer zapory** i wybierz pozycję **Menedżer zapory**.
3. Na stronie **Menedżer zapory** wybierz pozycję **Wyświetl zabezpieczone centra wirtualne**.
4. W **Menedżerze zapory | Na stronie zabezpieczone centra wirtualne** wybierz pozycję **Utwórz nowe zabezpieczone centrum wirtualne**.
5. W obszarze **Grupa zasobów** wybierz pozycję **PD-Manager**.
7. W **obszarze region** wybierz pozycję **Wschodnie stany USA**.
1. W polu **Nazwa zabezpieczonego koncentratora wirtualnego** wpisz **Hub-01**.
2. W **polu przestrzeń adresowa centrum** wpisz **10.0.0.0/16**.
3. Dla nowej nazwy vWAN wpisz **vWAN-01**.
4. Pozostaw zaznaczone pole wyboru **Uwzględnij bramę sieci VPN, aby włączyć zaufanych partnerów zabezpieczeń** .
5. Wybierz pozycję **Dalej: Zapora platformy Azure**.
6. Zaakceptuj domyślne ustawienie **zapory platformy Azure**  , a następnie wybierz pozycję **Dalej: zaufany partner zabezpieczeń**.
7. Zaakceptuj ustawienie domyślny **zaufany partner zabezpieczeń**  , a następnie wybierz kolejno pozycje **Dalej: przegląd + Utwórz**.
8. Wybierz przycisk **Utwórz**. Wdrożenie zajmie około 30 minut.

Teraz możesz uzyskać publiczny adres IP zapory.

1. Po zakończeniu wdrażania na Azure Portal wybierz **wszystkie usługi**.
1. Wpisz **Menedżer zapory** , a następnie wybierz pozycję **Menedżer zapory**.
2. Wybierz pozycję **zabezpieczone centra wirtualne**.
3. Wybierz pozycję **Hub-01**.
7. Wybierz pozycję **Konfiguracja publicznego adresu IP**.
8. Zanotuj publiczny adres IP do użycia później.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Łączenie sieci wirtualnych Hub i gwiazdy

Teraz można połączyć równorzędne sieci wirtualne z koncentratorem i szprychą.

1. Wybierz grupę zasobów **Menedżer zapory** , a następnie wybierz wirtualną sieć WAN **Vwan-01** .
2. W obszarze **łączność** wybierz pozycję **połączenia sieci wirtualnej**.
3. Wybierz pozycję **Dodaj połączenie**.
4. W obszarze **Nazwa połączenia** wpisz **Hub-szprych-01**.
5. W przypadku **centrów** wybierz pozycję **Hub-01**.
6. W obszarze **Grupa zasobów** wybierz pozycję **PD-Manager**.
7. W obszarze **Sieć wirtualna** wybierz opcję **szprych-01**.
8. Wybierz przycisk **Utwórz**.

Powtórz, aby połączyć sieć wirtualną **szprych-02** : Connection Name- **Hub-02**

## <a name="deploy-the-servers"></a>Wdrażanie serwerów

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Windows Server 2016 Datacenter** na **popularnej** liście.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Grupa zasobów     |**PD — Menedżer**|
   |Nazwa maszyny wirtualnej     |**SRV — obciążenie — 01**|
   |Region (Region)     |**Prześlij Wschodnie stany USA)**|
   |Nazwa użytkownika administratora     |Wpisz nazwę użytkownika|
   |Hasło     |Wpisz hasło|

4. W obszarze **reguły portów ruchu przychodzącego** dla **publicznych portów przychodzących** wybierz opcję **Brak**.
6. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej: dyski**.
7. Zaakceptuj ustawienia domyślne dysku i wybierz pozycję **Dalej: sieć**.
8. Wybierz opcję **szprych-01** dla sieci wirtualnej i wybierz pozycję **obciążenie — 01-SN** dla podsieci.
9. W obszarze **publiczny adres IP** wybierz pozycję **Brak**.
11. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej: Zarządzanie**.
12. Wybierz pozycję **wyłączone** , aby wyłączyć diagnostykę rozruchu. Zaakceptuj inne ustawienia domyślne i wybierz pozycję **Recenzja + Utwórz**.
13. Przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz pozycję **Utwórz**.

Skorzystaj z informacji podanych w poniższej tabeli, aby skonfigurować inną maszynę wirtualną o nazwie **SRV-obciążeń-02**. Pozostała część konfiguracji jest taka sama jak maszyna wirtualna **SRV-obciążeń-01** .

|Ustawienie  |Wartość  |
|---------|---------|
|Sieć wirtualna|**Szprycha-02**|
|Podsieć|**Obciążenie — 02-SN**|

Po wdrożeniu serwerów wybierz zasób serwera, a w obszarze **Sieć** Zanotuj prywatny adres IP dla każdego serwera.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Tworzenie zasad zapory i zabezpieczanie centrum

Zasady zapory definiują kolekcje reguł, aby kierować ruchem do co najmniej jednego zabezpieczonego koncentratora wirtualnego. Utworzysz zasady zapory, a następnie zabezpieczę centrum.

1. W Menedżerze zapory wybierz pozycję **Wyświetl zasady zapory platformy Azure**.
2. Wybierz pozycję **Utwórz zasady zapory platformy Azure**.
3. W obszarze **Szczegóły zasad** w polu **Nazwa** wpisz **zasady-01** , a dla **regionu** wybierz pozycję **Wschodnie stany USA**.
4. Wybierz pozycję **Dalej: ustawienia DNS (wersja zapoznawcza)**.
1. Wybierz pozycję **Dalej: reguły**.
2. Na karcie **reguły** wybierz pozycję **Dodaj kolekcję reguł**.
3. Na stronie **Dodawanie kolekcji reguł** wpisz dla **nazwy** **App-RC-01** .
4. W obszarze **Typ kolekcji reguł** wybierz pozycję **aplikacja**.
5. Dla **priorytetu** wpisz **100**.
6. Upewnij się, że **Akcja kolekcji reguł** to **Zezwalaj**.
7. Dla **nazwy** reguły wpisz **Allow-MSFT**.
8. W polu **Typ źródła** wybierz pozycję **adres IP**.
9. Dla elementu **Source** wpisz **\*** .
10. W obszarze **Protokół** wpisz **http i https**.
11. Upewnij się, że **Typ docelowy** to **nazwa FQDN**.
12. W obszarze **docelowy** wpisz **\* . Microsoft.com**.
13. Wybierz pozycję **Dodaj**.

Dodaj regułę DNAT, aby można było połączyć pulpit zdalny z maszyną wirtualną **SRV-obciążeń-01** .

1. Wybierz pozycję **Dodaj kolekcję reguł**.
2. W obszarze **Nazwa** wpisz **DNAT-RDP**.
3. W obszarze **Typ kolekcji reguł** wybierz pozycję **DNAT**.
4. Dla **priorytetu** wpisz **100**.
5. W polu **Nazwa** reguły wpisz **Allow-RDP**.
6. W polu **Typ źródła** wybierz pozycję **adres IP**.
7. Dla elementu **Source** wpisz **\*** .
8. W polu **Protokół** wybierz **TCP**.
9. W przypadku **portów docelowych** wpisz **3389**.
10. W obszarze **Typ docelowy** wybierz pozycję **adres IP**.
11. W polu **Lokalizacja docelowa** wpisz wcześniej zanotowany publiczny adres IP zapory.
12. Dla **adresu przetłumaczonego** wpisz prywatny adres IP dla **SRV-obciążeń-01** zanotowanych wcześniej.
13. W polu **Przekształcony port** wpisz **3389**.
14. Wybierz pozycję **Dodaj**.

Dodaj regułę sieciową, aby można było połączyć pulpit zdalny od **SRV-obciążeń-01** z **obciążeniem SRV-02**.

1. Wybierz pozycję **Dodaj kolekcję reguł**.
2. W obszarze **Nazwa** wpisz **VNET-RDP**.
3. W obszarze **Typ kolekcji reguł** wybierz pozycję **Sieć**.
4. Dla **priorytetu** wpisz **100**.
5. W polu **Nazwa** reguły wpisz **Zezwalaj na sieć wirtualną**.
6. W polu **Typ źródła** wybierz pozycję **adres IP**.
7. Dla elementu **Source** wpisz **\*** .
8. W polu **Protokół** wybierz **TCP**.
9. W przypadku **portów docelowych** wpisz **3389**.
9. W obszarze **Typ docelowy** wybierz pozycję **adres IP**.
10. W polu **miejsce docelowe** wpisz wcześniej zanotowany prywatny adres IP **SRV-obciążenia-02** .
11. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Dalej: analiza zagrożeń**.
2. Wybierz pozycję **Dalej: centra**.
3. Na karcie **centra** wybierz opcję **Skojarz centra wirtualne**.
4. Wybierz pozycję **Hub-01** , a następnie wybierz pozycję **Dodaj**.
5. Wybierz pozycję **Przejrzyj i utwórz**.
6. Wybierz pozycję **Utwórz**.

Ukończenie tego procesu może potrwać około pięciu minut.

## <a name="route-traffic-to-your-hub"></a>Kierowanie ruchu do centrum

Teraz musisz upewnić się, że ruch sieciowy jest kierowany przez zaporę.

1. W Menedżerze zapory wybierz pozycję **zabezpieczone centra wirtualne**.
2. Wybierz pozycję **Hub-01**.
3. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja zabezpieczeń**.
4. W obszarze **ruch internetowy** wybierz pozycję **Zapora platformy Azure**.
5. W obszarze **ruch prywatny** wybierz pozycję **Wyślij za pośrednictwem zapory platformy Azure**.
10. Upewnij **się, że połączenie gwiazdy** ukazuje **ruch internetowy** jako **zabezpieczony**.
11. Wybierz pozycję **Zapisz**.


## <a name="test-your-firewall"></a>Testowanie zapory

Aby przetestować reguły zapory, Połącz się z pulpitem zdalnym przy użyciu publicznego adresu IP zapory, który jest **oparty na SRV-obciążeń-01**. Z tego miejsca będziesz używać przeglądarki do testowania reguły aplikacji i łączenia pulpitu zdalnego z usługą **SRV-obciążeń-02** w celu przetestowania reguły sieci.

### <a name="test-the-application-rule"></a>Testowanie reguły aplikacji

Teraz Przetestuj reguły zapory, aby upewnić się, że działa zgodnie z oczekiwaniami.

1. Połącz pulpit zdalny z publicznym adresem IP zapory i zaloguj się.

3. Otwórz program Internet Explorer i przejdź do https://www.microsoft.com.
4. Wybierz pozycję **OK**  >  **Zamknij** na stronie Alerty zabezpieczeń programu Internet Explorer.

   Powinna zostać wyświetlona strona główna firmy Microsoft.

5. Przejdź na stronę https://www.google.com.

   Dostęp powinien zostać zablokowany przez zaporę.

Teraz sprawdzono, że reguła aplikacji zapory działa:

* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.

### <a name="test-the-network-rule"></a>Testowanie reguły sieci

Teraz Przetestuj regułę sieci.

- Otwórz pulpit zdalny dla prywatnego adresu IP **SRV-** obciążeniowego 02.

   Pulpit zdalny powinien łączyć się z **obciążeniem SRV — 02**.

Teraz sprawdzono, że reguła sieci zapory działa:
* Pulpit zdalny można połączyć z serwerem znajdującym się w innej sieci wirtualnej.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu testowania zasobów zapory Usuń grupę zasobów **Menedżer zapory** , aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zaufanych partnerach zabezpieczeń](trusted-security-partners.md)
