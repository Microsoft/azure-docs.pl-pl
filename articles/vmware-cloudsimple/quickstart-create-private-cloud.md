---
title: 'Szybki Start: Tworzenie chmury prywatnej'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Dowiedz się, jak utworzyć i skonfigurować chmurę prywatną przy użyciu rozwiązań VMware platformy Azure według CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 487308aca3231650aee3fac5ae127006649e19b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073905"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Szybki Start — Konfigurowanie środowiska chmury prywatnej

W tym artykule dowiesz się, jak utworzyć chmurę prywatną CloudSimple i skonfigurować środowisko chmury prywatnej.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przejrzyj [wymagania wstępne dotyczące sieci](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Chmura prywatna to izolowany stos oprogramowania VMware obsługujący hosty ESXi, vCenter, sieci vSAN i NSX.

Chmurami prywatnymi zarządza się za pomocą portalu CloudSimple. Mają własny serwer vCenter we własnej domenie zarządzania. Stos działa na dedykowanych węzłach i izolowanych węzłach sprzętowych bez systemu operacyjnego.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple Services**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. W obszarze **Przegląd** kliknij pozycję **Utwórz chmurę prywatną** , aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania na platformie Azure.  

    ![Utwórz chmurę prywatną na platformie Azure](media/create-private-cloud-from-azure.png)

5. W portalu CloudSimple Podaj nazwę chmury prywatnej.
6. Wybierz **lokalizację** chmury prywatnej.
7. Wybierz **Typ węzła**, spójny z zainicjowaną obsługą na platformie Azure.
8. Określ **liczbę węzłów**.  Aby można było utworzyć chmurę prywatną, wymagane są co najmniej trzy węzły.

    ![Tworzenie chmury prywatnej — informacje podstawowe](media/create-private-cloud-basic-info.png)

9. Kliknij przycisk **Dalej: Opcje zaawansowane**.
10. Wprowadź zakres CIDR dla podsieci vSphere/sieci vSAN. Upewnij się, że zakres CIDR nie nakłada się na żadną z lokalizacji lokalnych lub innych podsieci platformy Azure (sieci wirtualnych) lub z podsiecią bramy.

    **Opcje zakresu CIDR:** /24,/23,/22 lub/21. Zakres CIDR/24 obsługuje maksymalnie 26 węzłów, a/23 zakres CIDR obsługuje do 58 węzłów, a zakres CIDR/22 i/21 obsługuje węzły 64 (maksymalną liczbę węzłów w chmurze prywatnej).  Aby dowiedzieć się więcej o sieciach VLAN i podsieciach, zobacz [sieci VLAN i podsieci — Omówienie](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Adresy IP w zakresie CIDR vSphere/sieci vSAN są zarezerwowane do użytku przez infrastrukturę chmury prywatnej.  Nie używaj adresu IP z tego zakresu na żadnej maszynie wirtualnej.

11. Kliknij przycisk **Dalej: Przejrzyj i Utwórz**.
12. Przejrzyj ustawienia. Jeśli musisz zmienić dowolne ustawienia, kliknij przycisk **Wstecz**.
13. Kliknij pozycję **Utwórz**.

Rozpocznie się proces aprowizacji w chmurze prywatnej.  Zainicjowanie obsługi chmury prywatnej może potrwać do dwóch godzin.

## <a name="launch-cloudsimple-portal"></a>Uruchom Portal CloudSimple

Możesz uzyskać dostęp do portalu CloudSimple z poziomu Azure Portal.  Portal CloudSimple zostanie uruchomiony z poświadczeniami logowania platformy Azure przy użyciu jednego Sign-On (Logowanie jednokrotne).  Dostęp do portalu CloudSimple wymaga autoryzacji aplikacji **autoryzacji usługi CloudSimple** .  Aby uzyskać więcej informacji na temat przyznawania uprawnień, zobacz [wyrażanie zgody na aplikację autoryzacji usługi CloudSimple](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple Services**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. W obszarze przegląd kliknij pozycję **Przejdź do portalu CloudSimple** , aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania na platformie Azure.  

    ![Uruchom Portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Tworzenie sieci VPN typu punkt-lokacja

Połączenie sieci VPN typu punkt-lokacja jest najprostszym sposobem nawiązywania połączenia z chmurą prywatną z komputera. Jeśli łączysz się ze zdalną chmurą prywatną, Użyj połączenia sieci VPN typu punkt-lokacja.  Aby uzyskać szybki dostęp do chmury prywatnej, wykonaj poniższe kroki.  Dostęp do regionu CloudSimple z sieci lokalnej można wykonać przy użyciu [sieci VPN typu lokacja-lokacja](vpn-gateway.md) lub [usługi Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Utwórz bramę

1. Uruchom Portal CloudSimple i wybierz pozycję **Sieć**.
2. Wybierz **VPN Gateway**.
3. Kliknij pozycję **nowe VPN Gateway**.

    ![Tworzenie bramy sieci VPN](media/create-vpn-gateway.png)

4. W obszarze **Konfiguracja bramy** określ poniższe ustawienia, a następnie kliknij przycisk **dalej**.

    * Wybierz **Sieć VPN typu punkt-lokacja** jako typ bramy.
    * Wprowadź nazwę identyfikującą bramę.
    * Wybierz lokalizację platformy Azure, w której wdrożono usługę CloudSimple.
    * Określ podsieć klienta dla bramy punkt-lokacja.  Po nawiązaniu połączenia będą nadawane adresy DHCP z tej podsieci.

5. W polu **połączenie/użytkownik** określ poniższe ustawienia, a następnie kliknij przycisk **dalej**.

    * Aby automatycznie zezwolić wszystkim bieżącym i przyszłym użytkownikom na dostęp do chmury prywatnej za pomocą tej bramy punkt-lokacja, zaznacz opcję **automatycznie Dodaj wszystkich użytkowników**. Po wybraniu tej opcji Wszyscy użytkownicy z listy użytkowników są wybierani automatycznie. Możesz przesłonić opcję automatyczną, usuwając zaznaczenie poszczególnych użytkowników na liście.
    * Aby zaznaczyć tylko poszczególnych użytkowników, kliknij pola wyboru na liście użytkownik.

6. Sekcja sieci VLAN/podsieci pozwala określić zarządzanie i sieci VLAN użytkownika/podsieci dla bramy i połączeń.

    * Opcje **automatycznie Dodaj** ustawienia zasady globalne dla tej bramy. Ustawienia dotyczą bieżącej bramy. Ustawienia można przesłonić w obszarze **wyboru** .
    * Wybierz pozycję **Dodaj sieci VLAN zarządzania/podsieci chmur prywatnych**.
    * Aby dodać wszystkie zdefiniowane przez użytkownika podsieci/sieci VLAN, kliknij opcję  **Dodaj zdefiniowane przez użytkownika sieci VLAN/podsieci**.
    * **Wybrane** ustawienia zastępują ustawienia globalne w obszarze **Automatyczne dodawanie**.

7. Kliknij przycisk **dalej** , aby przejrzeć ustawienia. Kliknij ikonę Edytuj, aby wprowadzić zmiany.
8. Kliknij przycisk **Utwórz** , aby utworzyć bramę sieci VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Nawiązywanie połączenia z usługą CloudSimple przy użyciu sieci VPN typu punkt-lokacja

Klient sieci VPN jest wymagany do nawiązywania połączenia z usługą CloudSimple z komputera.  Pobierz [klienta OpenVPN](https://openvpn.net/community-downloads/) dla systemu Windows lub [lepkości](https://www.sparklabs.com/viscosity/download/) dla macOS i OS X.

1. Uruchom Portal CloudSimple i wybierz pozycję **Sieć**.
2. Wybierz **VPN Gateway**.
3. Na liście bram sieci VPN kliknij pozycję Brama sieci VPN typu punkt-lokacja.
4. Wybierz pozycję **Użytkownicy**.
5. Kliknij pozycję **Pobierz moją konfigurację sieci VPN**.

    ![Pobieranie konfiguracji sieci VPN](media/download-p2s-vpn-configuration.png)

6. Zaimportuj konfigurację na kliencie sieci VPN.

    * Instrukcje dotyczące [importowania konfiguracji w kliencie systemu Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instrukcje dotyczące [importowania konfiguracji w systemie macOS lub OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Nawiązywanie połączenia z usługą CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Tworzenie sieci VLAN dla maszyn wirtualnych obciążenia

Po utworzeniu chmury prywatnej Utwórz sieć VLAN, w której zostaną wdrożone maszyny wirtualne obciążenia/aplikacji.

1. W portalu CloudSimple wybierz pozycję **Sieć**.
2. Kliknij pozycję **VLAN/podsieci**.
3. Kliknij pozycję **Utwórz sieć VLAN/podsieć**.

    ![Utwórz sieć VLAN/podsieć](media/create-new-vlan-subnet.png)

4. Wybierz **chmurę prywatną** dla nowej podsieci/sieci VLAN.
5. Wybierz z listy identyfikator sieci VLAN.  
6. Wprowadź nazwę podsieci, aby zidentyfikować podsieć.
7. Określ zakres i maskę maski podsieci.  Ten zakres nie może pokrywać się z żadną istniejącą podsiecią.
8. Kliknij przycisk **Prześlij**.

    ![Utwórz szczegóły sieci VLAN/podsieci](media/create-new-vlan-subnet-details.png)

Zostanie utworzona sieć VLAN/podsieć.  Możesz teraz używać tego identyfikatora sieci VLAN do tworzenia rozproszonej grupy portów w usłudze vCenter w chmurze prywatnej.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Łączenie środowiska z siecią wirtualną platformy Azure

Usługa CloudSimple udostępnia obwód usługi ExpressRoute dla chmury prywatnej. Możesz połączyć sieć wirtualną na platformie Azure z obwodem usługi ExpressRoute. Aby uzyskać szczegółowe informacje na temat konfigurowania połączenia, wykonaj czynności opisane w temacie [Azure Virtual Network Connection przy użyciu ExpressRoute](./cloudsimple-azure-network-connection.md).

## <a name="sign-in-to-vcenter"></a>Zaloguj się do programu vCenter

Teraz możesz zalogować się do programu vCenter, aby skonfigurować maszyny wirtualne i zasady.

1. Aby uzyskać dostęp do programu vCenter, Zacznij od portalu CloudSimple. Na stronie głównej w obszarze **typowe zadania** kliknij pozycję **Uruchom program vSphere Client**.  Wybierz chmurę prywatną, a następnie kliknij pozycję **Uruchom klienta vSphere** w chmurze prywatnej.

    ![Uruchom klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Wybierz preferowanego klienta vSphere, aby uzyskać dostęp do programu vCenter i zalogować się przy użyciu nazwy użytkownika i hasła.  Wartości domyślne to:
    * Nazwa użytkownika: `CloudOwner@cloudsimple.local`
    * Password (Hasło): `CloudSimple123!`

Ekrany programu vCenter w następnych procedurach pochodzą z klienta vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Zmień hasło programu vCenter

CloudSimple zaleca zmianę hasła przy pierwszym logowaniu się do programu vCenter.  
Ustawione hasło musi spełniać następujące wymagania:

* Maksymalny okres istnienia: należy zmienić hasło co 365 dni
* Ogranicz ponowne użycie: użytkownicy nie mogą ponownie używać żadnego z pięciu poprzednich haseł
* Długość: 8-20 znaków
* Znak specjalny: co najmniej jeden znak specjalny
* Znaki alfabetyczne: co najmniej jeden znak pisany wielkimi literami, A-Z i co najmniej jedną małą literę, a – z
* Liczby: co najmniej jeden znak numeryczny, 0-9
* Maksymalna liczba identycznych znaków sąsiednich: trzy

    Przykład: CC lub CCC jest akceptowalny jako część hasła, ale CCCC nie jest.

Jeśli ustawisz hasło, które nie spełnia wymagań:

* Jeśli używasz klienta programu vSphere Flash, zgłaszany jest błąd
* Jeśli używasz klienta HTML5, nie zgłasza on błędu. Klient nie akceptuje zmiany i nadal działa stare hasło.

## <a name="access-nsx-manager"></a>Dostęp do programu NSX Manager

Program NSX Manager jest wdrażany z domyślnym hasłem. 

* Nazwa użytkownika: **administrator**
* Hasło: **CloudSimple123!**

W portalu CloudSimple można znaleźć w pełni kwalifikowaną nazwę domeny (FQDN) i adres IP programu NSX Manager.

1. Uruchom Portal CloudSimple i wybierz pozycję **zasoby**.
2. Kliknij chmurę prywatną, której chcesz użyć.
3. Wybieranie **sieci zarządzania vSphere**
4. Użyj nazwy FQDN lub adresu IP programu **NSX Manager** i nawiąż połączenie przy użyciu przeglądarki sieci Web.

    ![Znajdź nazwę FQDN programu NSX Manager](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Tworzenie grupy portów

Aby utworzyć rozproszoną grupę portów w vSphere:

1. Postępuj zgodnie z instrukcjami w temacie "Dodawanie rozproszonej grupy portów" w [przewodniku po sieci vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Podczas konfigurowania rozproszonej grupy portów podaj identyfikator sieci VLAN utworzony w temacie [Tworzenie sieci VLAN dla maszyn wirtualnych obciążenia](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* [Nawiązywanie połączenia z siecią lokalną za pomocą usługi Azure ExpressRoute](on-premises-connection.md)
* [Konfigurowanie sieci VPN typu lokacja-lokacja z poziomu lokalnego](vpn-gateway.md)
