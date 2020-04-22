---
title: 'Szybki start: tworzenie chmury prywatnej'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Dowiedz się, jak utworzyć i skonfigurować chmurę prywatną za pomocą rozwiązań Azure VMware firmy CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10831c0dc39fa241a43541850afae39593ae033b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678284"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Szybki start — konfigurowanie środowiska chmury prywatnej

W tym artykule dowiesz się, jak utworzyć cloudsimple private cloud i skonfigurować środowisko private cloud.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przejrzyj [wymagania wstępne sieci .](cloudsimple-network-checklist.md)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Private Cloud to izolowany stos VMware, który obsługuje hosty ESXi, vCenter, vSAN i NSX.

Chmury prywatne są zarządzane za pośrednictwem portalu CloudSimple. Mają własny serwer vCenter we własnej domenie zarządzania. Stos działa na dedykowanych węzłach i izolowanych węzłach sprzętowych z gołym metalem.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. Z **przeglądu**kliknij pozycję **Utwórz chmurę prywatną,** aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania platformy Azure.  

    ![Tworzenie chmury prywatnej z platformy Azure](media/create-private-cloud-from-azure.png)

5. W portalu CloudSimple podaj nazwę chmury prywatnej.
6. Wybierz **lokalizację** chmury prywatnej.
7. Wybierz **typ węzła,** zgodne z tym, co aprowizowana na platformie Azure.
8. Określ **liczbę węzłów**.  Do utworzenia chmury prywatnej wymagane są co najmniej trzy węzły.

    ![Tworzenie chmury prywatnej — podstawowe informacje](media/create-private-cloud-basic-info.png)

9. Kliknij **przycisk Dalej: Opcje zaawansowane**.
10. Wprowadź zakres CIDR dla podsieci vSphere/vSAN. Upewnij się, że zakres CIDR nie pokrywa się z żadnym z lokalnych lub innych podsieci platformy Azure (sieci wirtualnych) lub z podsiecią bramy.

    **Opcje zakresu CIDR:** /24, /23, /22 lub /21. Zakres /24 CIDR obsługuje do 26 węzłów, /23 zakres CIDR obsługuje do 58 węzłów, a zakres /22 i /21 CIDR obsługuje 64 węzły (maksymalna liczba węzłów w chmurze prywatnej).  Aby dowiedzieć się więcej oraz sieci VPN i podsieci, zobacz [omówienie sieci VPN i podsieci](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Adresy IP w zakresie cidr vSphere/vSAN są zarezerwowane do użytku przez infrastrukturę private cloud.  Nie używaj adresu IP w tym zakresie na żadnej maszynie wirtualnej.

11. Kliknij **przycisk Dalej: Przejrzyj i utwórz**plik .
12. Przejrzyj ustawienia. Jeśli chcesz zmienić ustawienia, kliknij przycisk **Poprzedni**.
13. Kliknij przycisk **Utwórz**.

Rozpoczyna się proces inicjowania obsługi administracyjnej usługi Private Cloud.  Może upłynąć do dwóch godzin dla chmury prywatnej do obsługi administracyjnej.

## <a name="launch-cloudsimple-portal"></a>Uruchom portal CloudSimple

Dostęp do portalu CloudSimple można uzyskać z witryny Azure portal.  CloudSimple portal zostanie uruchomiony z poświadczenia logowania platformy Azure przy użyciu logowania jednokrotnego (logowanie jednokrotne).  Uzyskiwanie dostępu do portalu CloudSimple wymaga autoryzacji aplikacji **CloudSimple Service Authorization.**  Aby uzyskać więcej informacji na temat udzielania uprawnień, zobacz [Aplikacja autoryzacji usługi CloudSimple](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. W obszarze przeglądu kliknij pozycję **Przejdź do portalu CloudSimple,** aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania platformy Azure.  

    ![Uruchom portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Tworzenie sieci VPN typu punkt-lokacja

Połączenie sieci VPN typu "point-to-site" to najprostszy sposób na połączenie się z chmurą prywatną z komputera. Użyj połączenia sieci VPN typu punkt-lokacja, jeśli łączysz się zdalnie z chmurą prywatną.  Aby uzyskać szybki dostęp do chmury prywatnej, wykonaj poniższe czynności.  Dostęp do regionu CloudSimple z sieci lokalnej można wykonać za pomocą [sieci VPN lokacja lokacja](vpn-gateway.md) lub [usługi Azure ExpressRoute.](on-premises-connection.md)

### <a name="create-gateway"></a>Tworzenie bramy

1. Uruchom portal CloudSimple i wybierz pozycję **Sieć**.
2. Wybierz **bramę sieci VPN**.
3. Kliknij **pozycję Nowa brama sieci VPN**.

    ![Tworzenie bramy sieci VPN](media/create-vpn-gateway.png)

4. W przypadku **konfiguracji bramy**należy określić następujące ustawienia i kliknąć przycisk **Dalej**.

    * Wybierz sieć VPN typu sieć WEB typu typu bramy typu typu **wskaż** lokację.
    * Wprowadź nazwę, aby zidentyfikować bramę.
    * Wybierz lokalizację platformy Azure, w której wdrożona jest usługa CloudSimple.
    * Określ podsieć klienta bramy typu punkt-lokacja.  Adresy DHCP będą podane z tej podsieci podczas nawiązywania połączenia.

5. W przypadku **opcji Połączenie/Użytkownik**określ następujące ustawienia i kliknij przycisk **Dalej**.

    * Aby automatycznie zezwolić wszystkim obecnym i przyszłym użytkownikom na dostęp do chmury prywatnej za pośrednictwem tej bramy typu punkt-lokacja, wybierz opcję **Automatycznie dodawaj wszystkich użytkowników**. Po wybraniu tej opcji wszyscy użytkownicy z listy Użytkownik są automatycznie wybierani. Opcję automatyczną można zastąpić, odznaczając poszczególnych użytkowników na liście.
    * Aby wybrać tylko pojedynczych użytkowników, kliknij pola wyboru na liście Użytkowników.

6. Sekcja Sieci/podsieci VLAN umożliwia określenie sieci/podsieci zarządzania i sieci VLAN dla bramy i połączeń.

    * Opcje **automatycznego dodawania** ustawiają zasady globalne dla tej bramy. Ustawienia dotyczą bieżącej bramy. Ustawienia można zastąpić w obszarze **Wybierz.**
    * Wybierz **pozycję Dodaj sieci VLAN/podsieci chmur prywatnych .**
    * Aby dodać wszystkie sieci VLAN/podsieci zdefiniowane przez użytkownika, kliknij pozycję **Dodaj zdefiniowane przez użytkownika sieci VLAN/Podsieci**.
    * Ustawienia **wyboru** zastępują ustawienia globalne w obszarze **Automatycznie dodawaj**.

7. Kliknij **przycisk Dalej,** aby przejrzeć ustawienia. Kliknij ikony edycji, aby wprowadzić zmiany.
8. Kliknij **przycisk Utwórz,** aby utworzyć bramę sieci VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Łączenie się z cloudsimple przy użyciu sieci VPN typu punkt-lokacja

Klient sieci VPN jest potrzebny do łączenia się z cloudsimple z komputera.  Pobierz [klienta OpenVPN](https://openvpn.net/community-downloads/) dla Windows lub [Lepkości](https://www.sparklabs.com/viscosity/download/) dla systemów macOS i OS X.

1. Uruchom portal CloudSimple i wybierz pozycję **Sieć**.
2. Wybierz **bramę sieci VPN**.
3. Z listy bram sieci VPN kliknij bramę sieci VPN typu punkt-lokacja.
4. Wybierz pozycję **Użytkownicy**.
5. Kliknij **pozycję Pobierz konfigurację sieci VPN**.

    ![Pobieranie konfiguracji sieci VPN](media/download-p2s-vpn-configuration.png)

6. Zaimportuj konfigurację na kliencie sieci VPN.

    * Instrukcje [dotyczące importowania konfiguracji na kliencie systemu Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instrukcje [importowania konfiguracji w systemie macOS lub OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Połącz się z CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Tworzenie sieci VLAN dla maszyn wirtualnych obciążenia

Po utworzeniu chmury prywatnej utwórz sieć VLAN, w której wdrożysz maszyny wirtualne obciążenia/aplikacji.

1. W portalu CloudSimple wybierz pozycję **Sieć**.
2. Kliknij **pozycję Sieć VLAN/Podsieci**.
3. Kliknij **pozycję Utwórz sieć VLAN/Podsieć**.

    ![Tworzenie sieci VLAN/podsieci](media/create-new-vlan-subnet.png)

4. Wybierz **chmurę prywatną** dla nowej sieci VLAN/podsieci.
5. Wybierz identyfikator sieci VLAN z listy.  
6. Wprowadź nazwę podsieci, aby zidentyfikować podsieć.
7. Określ zakres i maskę CIDR podsieci.  Ten zakres nie może pokrywać się z istniejącymi podsieciami.
8. Kliknij **przycisk Prześlij**.

    ![Tworzenie szczegółów sieci VLAN/Podsieci](media/create-new-vlan-subnet-details.png)

Zostanie utworzona sieć VLAN/podsieć.  Teraz można użyć tego identyfikatora sieci VLAN do utworzenia rozproszonej grupy portów w centrum vCenter private cloud.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Łączenie środowiska z siecią wirtualną platformy Azure

CloudSimple zapewnia obwód usługi ExpressRoute dla chmury prywatnej. Sieć wirtualną można połączyć na platformie Azure z obwodem usługi ExpressRoute. Aby uzyskać szczegółowe informacje na temat konfigurowania połączenia, wykonaj kroki opisane w [usłudze Azure Virtual Network Connection przy użyciu usługi ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Zaloguj się do vCenter

Teraz możesz zalogować się do vCenter, aby skonfigurować maszyny wirtualne i zasady.

1. Aby uzyskać dostęp do vCenter, uruchom z portalu CloudSimple. Na stronie głównej w obszarze **Typowe zadania**kliknij pozycję Uruchom **klienta vSphere**.  Wybierz chmurę prywatną, a następnie kliknij przycisk **Uruchom klienta vSphere** w chmurze prywatnej.

    ![Uruchom klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Wybierz preferowanego klienta vSphere, aby uzyskać dostęp do vCenter i zalogować się przy użyciu nazwy użytkownika i hasła.  Wartości domyślne to:
    * Nazwa użytkownika:`CloudOwner@cloudsimple.local`
    * Password (Hasło): `CloudSimple123!`

Ekrany vCenter w następnych procedurach pochodzą z klienta vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Zmienianie hasła vCenter

CloudSimple zaleca zmianę hasła przy pierwszym logowanie się do vCenter.  
Ustawione hasło musi spełniać następujące wymagania:

* Maksymalny okres eksploatacji: hasło musi być zmieniane co 365 dni
* Ograniczanie ponownego użycia: użytkownicy nie mogą ponownie używać żadnego z pięciu poprzednich haseł
* Długość: 8 - 20 znaków
* Znak specjalny: Co najmniej jeden znak specjalny
* Znaki alfabetyczne: co najmniej jeden znak wielkim, A-Z i co najmniej jeden znak małych liter, a-z
* Liczby: co najmniej jeden znak numeryczny, 0-9
* Maksymalne identyczne sąsiednie znaki: Trzy

    Przykład: CC lub CCC jest dopuszczalne jako część hasła, ale CCCC nie jest.

Jeśli ustawisz hasło, które nie spełnia wymagań:

* jeśli korzystasz z vSphere Flash Client, zgłasza błąd
* Jeśli używasz klienta HTML5, nie zgłasza błędu. Klient nie akceptuje zmiany, a stare hasło nadal działa.

## <a name="access-nsx-manager"></a>Dostęp do menedżera NSX

Menedżer NSX jest wdrażany z domyślnym hasłem. 

* Nazwa użytkownika: **admin**
* Hasło: **CloudSimple123!**

W portalu CloudSimple można znaleźć w pełni kwalifikowaną nazwę domeny (FQDN) i adres IP menedżera NSX.

1. Uruchom portal CloudSimple i wybierz pozycję **Zasoby**.
2. Kliknij chmurę prywatną, której chcesz użyć.
3. Wybierz **sieć zarządzania vSphere**
4. Użyj adresu FQDN lub IP **NSX Managera** i połącz się za pomocą przeglądarki internetowej.

    ![Znajdź NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Tworzenie grupy portów

Aby utworzyć grupę portów rozproszonych w vSphere:

1. Postępuj zgodnie z instrukcjami w "Dodaj rozproszoną grupę portów" w [przewodniku sieci vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Podczas konfigurowania rozproszonej grupy portów podaj identyfikator sieci VLAN utworzony w [aplikacji Utwórz sieć VLAN dla maszyn wirtualnych obciążenia](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* [Łączenie się z siecią lokalną przy użyciu usługi Azure ExpressRoute](on-premises-connection.md)
* [Konfigurowanie sieci VPN między lokacjami z lokalnego](vpn-gateway.md)
