---
title: Integrowanie serwera NPS z uwierzytelnianiem VPN Gateway RADIUS dla usługi MFA
description: Opisuje integrację uwierzytelniania usługi RADIUS bramy platformy Azure z serwerem NPS dla Multi-Factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 208e99f61694f5a81a98dbc649e2a6035f57891b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018277"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrowanie uwierzytelniania usługi RADIUS bramy sieci VPN platformy Azure z serwerem NPS dla Multi-Factor Authentication 

W tym artykule opisano sposób integrowania serwera zasad sieciowych (NPS) z uwierzytelnianiem za pomocą usługi Azure VPN Gateway w celu dostarczania Multi-Factor Authentication (MFA) dla połączeń sieci VPN typu punkt-lokacja. 

## <a name="prerequisite"></a>Wymaganie wstępne

Aby włączyć usługę MFA, użytkownicy muszą należeć do Azure Active Directory (Azure AD), które muszą zostać zsynchronizowane ze środowiska lokalnego lub w chmurze. Ponadto użytkownik musi już zakończyć proces automatycznej rejestracji w usłudze MFA.  Aby uzyskać więcej informacji, zobacz [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Szczegółowe procedury

### <a name="step-1-create-a-virtual-network-gateway"></a>Krok 1. Tworzenie bramy sieci wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W sieci wirtualnej, która będzie hostować bramę sieci wirtualnej, wybierz pozycję **podsieci**, a następnie wybierz pozycję **podsieć bramy** , aby utworzyć podsieć. 

    ![Obraz przedstawiający sposób dodawania podsieci bramy](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Utwórz bramę sieci wirtualnej, określając następujące ustawienia:

    - **Typ bramy**: Wybierz pozycję **Sieć VPN**.
    - **Typ sieci VPN**: wybierz pozycję **oparta na trasach**.
    - **Jednostka SKU**: Wybierz typ jednostki SKU zgodnie z wymaganiami.
    - **Sieć wirtualna**: Wybierz sieć wirtualną, w której utworzono podsieć bramy.

        ![Obraz dotyczący ustawień bramy sieci wirtualnej](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-ad-mfa"></a>Krok 2. Konfigurowanie serwera NPS dla usługi Azure AD MFA

1. Na serwerze NPS [Zainstaluj rozszerzenie serwera NPS dla usługi Azure AD MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Otwórz konsolę serwera zasad sieciowych, kliknij prawym przyciskiem myszy pozycję **klienci usługi RADIUS**, a następnie wybierz pozycję **Nowy**. Utwórz klienta usługi RADIUS, określając następujące ustawienia:

    - **Przyjazna nazwa**: wpisz dowolną nazwę.
    - **Adres (IP lub DNS)**: wpisz podsieć bramy utworzoną w kroku 1.
    - **Wspólny klucz tajny**: wpisz dowolny klucz tajny i Zapamiętaj go do późniejszego użycia.

      ![Obraz dotyczący ustawień klienta usługi RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na karcie **Zaawansowane** Ustaw nazwę dostawcy na **RADIUS Standard** i upewnij się, że pole wyboru **dodatkowe opcje** nie jest zaznaczone.

    ![Obraz dotyczący ustawień zaawansowanych klienta usługi RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Przejdź do **pozycji zasady**  >  **zasady sieci**, kliknij dwukrotnie **pozycję połączenia z usługą routingu i dostępu zdalnego firmy Microsoft** , wybierz pozycję **Udziel dostępu**, a następnie kliknij przycisk **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Krok 3. Konfigurowanie bramy sieci wirtualnej

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Otwórz utworzoną bramę sieci wirtualnej. Upewnij się, że typ bramy jest ustawiony na **Sieć VPN** i że typ sieci VPN jest **oparty na trasie**.
3. Kliknij pozycję **wskaż konfigurację lokacji**  >  **Skonfiguruj teraz**, a następnie określ następujące ustawienia:

    - **Pula adresów**: wpisz podsieć bramy utworzoną w kroku 1.
    - **Typ uwierzytelniania**: wybierz pozycję **uwierzytelnianie usługi RADIUS**.
    - **Adres IP serwera**: wpisz adres IP serwera NPS.

      ![Obraz przedstawiający ustawienia ustawień lokacji](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Następne kroki

- [Multi-Factor Authentication usługi Azure AD](../active-directory/authentication/concept-mfa-howitworks.md)
- [Integrowanie istniejącej infrastruktury NPS z usługą Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)