---
title: Azure Stack zarządzanie Wi-Fią w usłudze Edge
description: Opisuje, jak za pomocą Azure Portal zarządzać Wi-Fi na Azure Stack brzegowej.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/28/2020
ms.author: alkohli
ms.openlocfilehash: f00be43d023d912d4b0b6e825dfe9d3e0ca2d250
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467824"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web do zarządzania łącznością bezprzewodową na urządzeniu Azure Stack Edge mini R

W tym artykule opisano sposób zarządzania łącznością sieci bezprzewodowej na urządzeniu z systemem Azure Stack Edge mini R. Możesz użyć lokalnego interfejsu użytkownika sieci Web na urządzeniu z systemem Azure Stack Edge mini R za pośrednictwem, aby dodawać, łączyć i usuwać profile Wi-Fi.

## <a name="about-wi-fi"></a>Informacje o Wi-Fi

Urządzenie usługi mini R Azure Stack Edge może działać zarówno w sieci przewodowej, jak i za pośrednictwem sieci bezprzewodowej. Urządzenie ma Wi-Fi port, który musi być włączony, aby umożliwić urządzeniu łączenie się z siecią bezprzewodową. 

Urządzenie ma pięć portów, PORT 1 i port 4 oraz piąty port Wi-Fi. Poniżej znajduje się Diagram płaszczyzny tylnej urządzenia Mini R po nawiązaniu połączenia z siecią bezprzewodową.

![Okablowanie dla Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Dodawanie, łączenie z profilem Wi-Fi

Wykonaj następujące kroki w lokalnym interfejsie użytkownika urządzenia, aby dodać profil Wi-Fi i nawiązać z nim połączenie.

1. Przejdź do strony **wprowadzenie** w lokalnym interfejsie użytkownika sieci Web urządzenia. Na kafelku **Sieć** wybierz pozycję **Konfiguruj**.  
    
    Na urządzeniu fizycznym istnieje pięć interfejsów sieciowych. PORT 1 i PORT 2 są interfejsami sieciowymi 1 GB/s. PORT 3 i 4 to wszystkie interfejsy sieciowe 10 GB/s. Piąty port jest portem Wi-Fi. 

    [![Lokalny interfejs użytkownika sieci Web "Ustawienia sieci" — Strona 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Wybierz port Wi-Fi i skonfiguruj ustawienia portu. 
    
    > [!IMPORTANT]
    > Zdecydowanie zalecamy skonfigurowanie statycznego adresu IP dla portu Wi-Fi.  

    ![Lokalny interfejs użytkownika sieci Web "Ustawienia sieci" — Strona 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Po zastosowaniu ustawień portu Wi-Fi należy zaktualizować stronę **sieci** .

    ![Lokalny interfejs użytkownika sieci Web "Ustawienia sieci" — Strona 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. Wybierz pozycję **Dodaj profil Wi-Fi** i przekaż profil Wi-Fi. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi Network Settings" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Profil sieci bezprzewodowej zawiera informacje o identyfikatorze SSID (sieci), klucz hasła i zabezpieczeniach, które mają być w stanie połączyć się z siecią bezprzewodową. Możesz uzyskać profil Wi-Fi dla swojego środowiska z poziomu administratora sieci.

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi Network Settings" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Po dodaniu profilu lista Wi-Fi profilów zostanie zaktualizowana w celu odzwierciedlenia nowego profilu. Profil powinien zawierać **stan połączenia** jako **odłączony**. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi Network Settings" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. Po pomyślnym załadowaniu profilu sieci bezprzewodowej Nawiąż połączenie z tym profilem. Wybierz pozycję **Połącz z profilem Wi-Fi**. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi ustawienia sieci" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Wybierz profil Wi-Fi, który został dodany w poprzednim kroku, a następnie wybierz pozycję **Zastosuj**. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi ustawienia sieci" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **Stan połączenia** należy zaktualizować na wartość **połączono**. Siła sygnału jest aktualizowana w celu wskazania jakości sygnału. 

    ![Lokalny interfejs użytkownika sieci Web "Port Wi-Fi ustawienia sieci" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Aby przenieść duże ilości danych, zalecamy użycie połączenia przewodowego zamiast sieci bezprzewodowej. 


## <a name="download-wi-fi-profile"></a>Pobierz profil Wi-Fi

Możesz pobrać profil Wi-Fi używany do łączności z siecią bezprzewodową.

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź do pozycji **konfiguracja > sieć**. 

2. W obszarze Ustawienia profilu Wi-Fi wybierz pozycję **Pobierz profil**. Powinno to spowodować pobranie aktualnie używanego profilu Wi-Fi.


## <a name="delete-wi-fi-profile"></a>Usuwanie profilu Wi-Fi

Można usunąć profil Wi-Fi używany do łączności z siecią bezprzewodową.


1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź do pozycji **konfiguracja > sieć**. 

2. W obszarze Ustawienia profilu Wi-Fi wybierz pozycję **Usuń profil Wi-Fi**.

3. W bloku **usuwanie Wi-Fi profilu** wybierz profil, który chcesz usunąć. Wybierz przycisk **Zastosuj**.


## <a name="configure-cisco-wi-fi-profile"></a>Konfigurowanie profilu Cisco Wi-Fi

Poniżej przedstawiono wskazówki dotyczące sposobu zarządzania i konfigurowania kontrolera sieci bezprzewodowej Cisco oraz punktu dostępu na urządzeniu. 

### <a name="dhcp-bridging-mode"></a>Tryb mostkowania DHCP

Aby użyć kontrolera sieci bezprzewodowej Cisco dla urządzenia, należy włączyć tryb mostkowania protokołu DHCP (Dynamic Host Configuration Protocol) na kontrolerze sieci bezprzewodowej sieci LAN (WLC).

Aby uzyskać więcej informacji, zobacz [tryb mostkowania DHCP](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>Przykład konfiguracji mostkowania

Aby włączyć funkcję mostkowania DHCP na kontrolerze, należy wyłączyć funkcję serwera proxy DHCP na kontrolerze. Aby włączyć mostkowanie DHCP przy użyciu wiersza polecenia:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Jeśli serwer DHCP nie istnieje w tej samej sieci warstwy 2 (L2) co klient, emisja powinna być przekazywana do serwera DHCP w bramie klienta przy użyciu pomocnika IP. To jest Przykładowa konfiguracja:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

Funkcja mostkowania DHCP jest ustawieniem globalnym, więc ma wpływ na wszystkie transakcje DHCP w ramach kontrolera. Należy dodać instrukcje pomocnika IP w infrastrukturze przewodowej dla wszystkich niezbędnych wirtualnych sieci lokalnych (VLAN) s na kontrolerze.

### <a name="enable-the-passive-client-for-wlan"></a>Włączanie klienta pasywnego dla sieci WLAN

Aby włączyć pasywną funkcję klienta dla bezprzewodowej sieci lokalnej (WLAN) na kontrolerze sieci bezprzewodowej Cisco:

* Interfejs skojarzony z siecią WLAN musi mieć włączone znakowanie sieci VLAN.
* Multiemisja sieci VLAN musi być włączona dla sieci WLAN.
* Przekazywanie GARP musi być włączone na WLC.

Aby uzyskać więcej informacji, zobacz [Informacje o multiemisji sieci VLAN dotyczące optymalizacji multiemisji](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html).

### <a name="troubleshoot"></a>Rozwiązywanie problemów

W przypadku wystąpienia problemów z przydziałami adresów IP na maszynach wirtualnych działających na urządzeniu Azure Stack Edge mini R należy zweryfikować powyższe ustawienia konfiguracji w środowisku sieciowym.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć Azure Stack Edge mini R urządzenia](azure-stack-edge-mini-r-deploy-prep.md).
