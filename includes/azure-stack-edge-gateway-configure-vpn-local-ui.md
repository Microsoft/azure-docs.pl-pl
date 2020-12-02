---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467373"
---
Wykonaj następujące kroki w lokalnym interfejsie użytkownika sieci Web urządzenia. Ten krok zajmie około 15 minut, łącznie z przekazywaniem pliku konfiguracji sieci VPN (lub pliku tagu usługi). 

1. Przejdź do pozycji **konfiguracja > sieci VPN**. Wybierz pozycję **Konfiguruj**.

    ![Konfigurowanie lokalnego interfejsu użytkownika 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. W bloku **Konfigurowanie sieci VPN** :

    - Włącz **Ustawienia sieci VPN**.
    - Podaj wspólny **klucz tajny sieci VPN**. Jest to klucz współużytkowany podany podczas tworzenia zasobu połączenia sieci VPN platformy Azure.
    - Podaj adres **IP bramy sieci VPN** . Jest to adres IP bramy sieci lokalnej platformy Azure.
    - W obszarze **Grupa PFS** wybierz opcję **Brak**. 
    - Dla **grupy DH** wybierz pozycję **group2**.
    - W przypadku **metody integralności protokołu IPSec** wybierz pozycję **SHA256**.
    - Aby uzyskać **stałe przekształcenia protokołu IPSec**, wybierz pozycję **GCMAES256**.
    - W przypadku **stałych transformacji uwierzytelniania protokołu IPSec** wybierz pozycję **GCMAES256**.
    - W przypadku **metody szyfrowania IKE** wybierz pozycję **AES256**.
    - Wybierz przycisk **Zastosuj**.

        ![Konfigurowanie lokalnego interfejsu użytkownika 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Aby uzyskać więcej informacji na temat obsługiwanych algorytmów kryptograficznych, przejdź do [informacji o wymaganiach kryptograficznych i bramach sieci VPN platformy Azure](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Aby przekazać plik konfiguracji trasy sieci VPN, wybierz pozycję **Przekaż**. 

    ![Konfigurowanie lokalnego interfejsu użytkownika 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Przejdź do pliku *JSON* tagów usługi pobranego w systemie lokalnym w poprzednim kroku.
    - Wybierz region jako region platformy Azure skojarzony z urządzeniem, siecią wirtualną i bramami.
    - Wybierz przycisk **Zastosuj**.

        ![Konfigurowanie lokalnego interfejsu użytkownika 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    Przekazywanie zajmie około 7-8 minut na urządzeniu.

4. Aby dodać trasy specyficzne dla klienta, skonfiguruj zakresy adresów IP, które mają być dostępne tylko przy użyciu sieci VPN. 

    - W obszarze **zakresy adresów IP, które mają być dostępne tylko przy użyciu sieci VPN**, wybierz pozycję **Konfiguruj**.
    - Podaj prawidłowy zakres adresów IPv4 i wybierz pozycję **Dodaj**. Powtórz kroki, aby dodać inne zakresy.
    - Wybierz przycisk **Zastosuj**.

        ![Konfigurowanie lokalnego interfejsu użytkownika 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

