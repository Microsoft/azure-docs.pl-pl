---
title: 'Samouczek: Zabezpieczanie koncentratora wirtualnego przy użyciu Menedżera zapory platformy Azure'
description: W tym samouczku dowiesz się, jak zabezpieczyć koncentrator wirtualny za pomocą Menedżera zapory platformy Azure przy użyciu Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c44daa67b4029c73c57ca82d72ee0a9759dd4c2d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563655"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Samouczek: Zabezpieczanie koncentratora wirtualnego przy użyciu Menedżera zapory platformy Azure

Korzystając z Menedżera zapory platformy Azure, można utworzyć zabezpieczone centra wirtualne, aby zabezpieczyć ruch sieciowy w chmurze przeznaczony dla prywatnych adresów IP, platformy Azure PaaS i Internetu. Routing ruchu do zapory jest zautomatyzowany, dlatego nie ma potrzeby tworzenia tras zdefiniowanych przez użytkownika (UDR).

![Zabezpieczanie sieci w chmurze](media/secure-cloud-network/secure-cloud-network.png)

Menedżer zapory obsługuje również architekturę sieci wirtualnej centrum. Aby zapoznać się z porównaniem bezpiecznych typów architektury sieci wirtualnych i koncentratorów wirtualnych, zobacz [co to są opcje architektury usługi Azure firewall Manager?](vhubs-and-vnets.md)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej będącej szprychą
> * Tworzenie bezpiecznego centrum wirtualnego
> * Łączenie gwiazdy i sieci wirtualnych
> * Tworzenie zasad zapory i zabezpieczanie centrum
> * Kierowanie ruchu do centrum
> * Testowanie zapory

## <a name="create-a-hub-and-spoke-architecture"></a>Tworzenie architektury gwiazdy

Najpierw Utwórz sieć wirtualną szprych, w której można umieścić serwery.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Tworzenie sieci wirtualnej i podsieci dla szprychy

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Sieć**wybierz pozycję **Sieć wirtualna**.
2. W polu **Subskrypcja** wybierz subskrypcję.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**, a następnie wpisz **PD-Manager** dla nazwy i wybierz **przycisk OK**.
2. W obszarze **Nazwa**wpisz polecenie **szprych-01**.
3. W **obszarze region**wybierz pozycję **(US) Wschodnie stany USA**.
4. Wybierz pozycję **Dalej: adresy IP**.
1. W polu **przestrzeń adresowa**zaakceptuj domyślną wartość **10.0.0.0/16**.
3. W obszarze **Nazwa podsieci**wybierz pozycję **domyślne**.
4. Zmień nazwę podsieci na **obciążenie-SN**.
5. W obszarze **zakres adresów podsieci**wpisz **10.0.1.0/24**.
6. Wybierz pozycję **Zapisz**.

Następnie Utwórz podsieć dla serwera skoku.

1. Wybierz pozycję **Dodaj podsieć**.
4. W obszarze **Nazwa podsieci**wpisz polecenie **skoku-SN**.
5. W obszarze **zakres adresów podsieci**wpisz **10.0.2.0/24**.
6. Wybierz pozycję **Dodaj**.

Teraz Utwórz sieć wirtualną.

1. Wybierz pozycję **Przegląd + utwórz**.
2. Wybierz przycisk **Utwórz**.

### <a name="create-the-secured-virtual-hub"></a>Tworzenie bezpiecznego centrum wirtualnego

Utwórz bezpieczne centrum wirtualne przy użyciu Menedżera zapory.

1. Na stronie głównej Azure Portal wybierz pozycję **wszystkie usługi**.
2. W polu wyszukiwania wpisz **Menedżer zapory** i wybierz pozycję **Menedżer zapory**.
3. Na stronie **Menedżer zapory** wybierz pozycję **Wyświetl zabezpieczone centra wirtualne**.
4. W **Menedżerze zapory | Na stronie zabezpieczone centra wirtualne** wybierz pozycję **Utwórz nowe zabezpieczone centrum wirtualne**.
5. W obszarze **Grupa zasobów**wybierz pozycję **PD-Manager**.
7. W **obszarze region**wybierz pozycję **Wschodnie stany USA**.
1. W polu **Nazwa zabezpieczonego koncentratora wirtualnego**wpisz **Hub-01**.
2. W **polu przestrzeń adresowa usługi Hub**wpisz **10.1.0.0/16**.
3. Dla nowej nazwy vWAN wpisz **vWAN-01**.
4. Pozostaw zaznaczone pole wyboru **Uwzględnij bramę sieci VPN, aby włączyć zaufanych partnerów zabezpieczeń** .
5. Wybierz pozycję **Dalej: Zapora platformy Azure**.
6. Zaakceptuj domyślne ustawienie **zapory platformy Azure** **Enabled** , a następnie wybierz pozycję **Dalej: zaufany partner zabezpieczeń**.
7. Zaakceptuj ustawienie domyślny **zaufany partner zabezpieczeń** **Disabled** , a następnie wybierz kolejno pozycje **Dalej: przegląd + Utwórz**.
8. Wybierz przycisk **Utwórz**. Wdrożenie zajmie około 30 minut.

### <a name="connect-the-hub-and-spoke-vnets"></a>Łączenie gwiazdy i sieci wirtualnych

Teraz możesz połączyć się za pomocą elementu równorzędnego z koncentratorem i szprychą sieci wirtualnych.

1. Wybierz grupę zasobów **Menedżer zapory** , a następnie wybierz wirtualną sieć WAN **Vwan-01** .
2. W obszarze **łączność**wybierz pozycję **połączenia sieci wirtualnej**.
3. Wybierz pozycję **Dodaj połączenie**.
4. W obszarze **Nazwa połączenia**wpisz **Hub-szprych**.
5. W przypadku **centrów**wybierz pozycję **Hub-01**.
6. W obszarze **Grupa zasobów**wybierz pozycję **PD-Manager**.
7. W obszarze **Sieć wirtualna**wybierz opcję **szprych-01**.
8. Wybierz przycisk **Utwórz**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Tworzenie zasad zapory i zabezpieczanie centrum

Zasady zapory definiują kolekcje reguł, aby kierować ruchem do co najmniej jednego zabezpieczonego koncentratora wirtualnego. Utworzysz zasady zapory, a następnie zabezpieczę centrum.

1. W Menedżerze zapory wybierz pozycję **Wyświetl zasady zapory platformy Azure**.
2. Wybierz pozycję **Utwórz zasady zapory platformy Azure**.
3. W obszarze **Szczegóły zasad**w polu **Nazwa** wpisz **zasady-01** , a dla **regionu** wybierz pozycję **Wschodnie stany USA**.
4. Wybierz pozycję **Dalej: reguły**.
5. Na karcie **reguły** wybierz pozycję **Dodaj kolekcję reguł**.
6. Na stronie **Dodawanie kolekcji reguł** wpisz **RC-01** jako **nazwę**.
7. W obszarze **Typ kolekcji reguł**wybierz pozycję **aplikacja**.
8. Dla **priorytetu**wpisz **100**.
9. Upewnij się, że **Akcja kolekcji reguł** to **Zezwalaj**.
10. Dla **nazwy** reguły wpisz **Allow-MSFT**.
11. W polu **Typ źródła**wybierz pozycję **adres IP**.
12. Dla elementu **Source**wpisz **\*** .
13. W obszarze **Protokół**wpisz **http i https**.
14. Upewnij się, że **Typ docelowy** to **nazwa FQDN**.
15. W obszarze **docelowy**wpisz ** \* . Microsoft.com**.
16. Wybierz pozycję **Dodaj**.
17. Wybierz pozycję **Dalej: analiza zagrożeń**.
18. Wybierz pozycję **Dalej: centra**.
19. Na karcie **centra** wybierz opcję **Skojarz centra wirtualne**.
20. Wybierz pozycję **Hub-01** , a następnie wybierz pozycję **Dodaj**.
21. Wybierz pozycję **Przegląd + utwórz**.
22. Wybierz przycisk **Utwórz**.

Ukończenie tego procesu może potrwać około pięciu minut.

## <a name="route-traffic-to-your-hub"></a>Kierowanie ruchu do centrum

Teraz należy zadbać o to, aby ruch sieciowy był kierowany przez zaporę.

1. W Menedżerze zapory wybierz pozycję **zabezpieczone centra wirtualne**.
2. Wybierz pozycję **Hub-01**.
3. W obszarze **Ustawienia**wybierz pozycję **Konfiguracja zabezpieczeń**.
4. W obszarze **ruch internetowy**wybierz pozycję **Zapora platformy Azure**.
5. W obszarze **ruch prywatny**wybierz pozycję **Wyślij za pośrednictwem zapory platformy Azure**.
10. Upewnij **się, że połączenie gwiazdy** ukazuje **ruch internetowy** jako **zabezpieczony**.
11. Wybierz pozycję **Zapisz**.


## <a name="test-your-firewall"></a>Testowanie zapory

Aby przetestować reguły zapory, należy wdrożyć kilka serwerów. W celu przetestowania reguł zapory i przechodzenia do usługi SRV można użyć Pulpit zdalny do nawiązania połączenia z Internetem, a następnie nawiązać połączenie z usługą obciążenia — w sieci SRV.

### <a name="deploy-the-servers"></a>Wdrażanie serwerów

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Windows Server 2016 Datacenter** na **popularnej** liście.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Grupa zasobów     |**PD — Menedżer**|
   |Nazwa maszyny wirtualnej     |**Przejdź do SRV**|
   |Region     |**Prześlij Wschodnie stany USA)**|
   |Nazwa użytkownika administratora     |Wpisz nazwę użytkownika|
   |Hasło     |Wpisz hasło|

4. W obszarze **reguły portów ruchu przychodzącego**dla **publicznych portów przychodzących**wybierz opcję **Zezwalaj na wybrane porty**.
5. W obszarze **Wybierz porty wejściowe** wybierz pozycję **RDP (3389)**.
6. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej: dyski**.
7. Zaakceptuj ustawienia domyślne dysku i wybierz pozycję **Dalej: sieć**.
8. Upewnij się, że dla sieci wirtualnej została wybrana wartość **szprych-01** , a podsieć jest **Przeskocz-SN**.
9. Dla **publicznego adresu IP**zaakceptuj domyślną nową nazwę publicznego adresu IP (Przeskocz-SRV-IP).
11. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej: Zarządzanie**.
12. Wybierz pozycję **wyłączone** , aby wyłączyć diagnostykę rozruchu. Zaakceptuj inne ustawienia domyślne i wybierz pozycję **Recenzja + Utwórz**.
13. Przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz pozycję **Utwórz**.

Skorzystaj z informacji podanych w poniższej tabeli, aby skonfigurować inną maszynę wirtualną o nazwie **obciążenie-SRV**. Pozostała część konfiguracji jest taka sama jak w przypadku maszyny wirtualnej Srv-Jump.

|Ustawienie  |Wartość  |
|---------|---------|
|Podsieć|**Workload-SN**|
|Publiczny adres IP|**Brak**|
|Publiczne porty wejściowe|**Brak**|

### <a name="add-a-route-table-and-default-route"></a>Dodawanie tabeli tras i trasy domyślnej

Aby zezwolić na połączenie internetowe z przeskokiem SRV, należy utworzyć tabelę tras i domyślną trasę bramy do Internetu z podsieci **skoku-SN** .

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. W polu wyszukiwania wpisz **tabelę tras** , a następnie wybierz pozycję **tabela tras**.
3. Wybierz przycisk **Utwórz**.
4. Wpisz **RT-01** jako **nazwę**.
5. Wybierz swoją subskrypcję, usługi **PD-Manager** dla grupy zasobów i **(USA) dla regionu Wschodnie stany USA** .
6. Wybierz przycisk **Utwórz**.
7. Po zakończeniu wdrożenia wybierz tabelę tras **RT-01** .
8. Wybierz pozycję **trasy** , a następnie wybierz pozycję **Dodaj**.
9. Wpisz **Przeskocz do inet** dla **nazwy trasy**.
10. Wpisz **0.0.0.0/0** dla **prefiksu adresu**.
11. Wybierz pozycję **Internet** dla **typu następnego przeskoku**.
12. Wybierz przycisk **OK**.
13. Po zakończeniu wdrożenia wybierz pozycję **podsieci**, a następnie wybierz pozycję **Skojarz**.
14. Wybierz opcję **szprych-01** dla **sieci wirtualnej**.
15. Wybierz pozycję **skoku-SN** dla **podsieci**.
16. Wybierz przycisk **OK**.

### <a name="test-the-rules"></a>Testowanie reguł

Teraz Przetestuj reguły zapory, aby upewnić się, że działa zgodnie z oczekiwaniami.

1. W Azure Portal Sprawdź ustawienia sieci dla maszyny wirtualnej **obciążenia** i zanotuj prywatny adres IP.
2. Połącz pulpit zdalny z maszyną **wirtualną i zaloguj się.** W tym miejscu Otwórz połączenie pulpitu zdalnego z prywatnym adresem IP **SRV** .

3. Otwórz program Internet Explorer i przejdź do https://www.microsoft.com.
4. Wybierz pozycję **OK**  >  **Zamknij** na stronie Alerty zabezpieczeń programu Internet Explorer.

   Powinna zostać wyświetlona strona główna firmy Microsoft.

5. Przejdź do https://www.google.com.

   Dostęp powinien zostać zablokowany przez zaporę.

Teraz sprawdzono, że reguły zapory działają:

* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zaufanych partnerach zabezpieczeń](trusted-security-partners.md)
