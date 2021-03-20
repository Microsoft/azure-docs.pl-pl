---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77471556"
---
Dostęp warunkowy umożliwia uzyskanie szczegółowej kontroli dostępu dla poszczególnych aplikacji. Aby można było korzystać z dostępu warunkowego, należy mieć Azure AD — wersja Premium 1 lub większą licencję dla użytkowników, którzy będą objęci regułami dostępu warunkowego.

1. Przejdź do strony **aplikacje dla przedsiębiorstw — wszystkie aplikacje** , a następnie kliknij pozycję **Sieć VPN platformy Azure**.

   - Kliknij pozycję **dostęp warunkowy**.
   - Kliknij pozycję **nowe zasady** , aby otworzyć **nowe** okienko.
2. W okienku **Nowy** przejdź do **przypisań — > użytkowników i grup**. Na karcie **Użytkownicy i grupy — >** **Dołącz** :

   - Kliknij pozycję **Wybierz użytkowników i grupy**.
   - Sprawdź **użytkowników i grupy**.
   - Kliknij przycisk **Wybierz** , aby wybrać grupę lub zbiór użytkowników, których ma dotyczyć uwierzytelnianie wieloskładnikowe.
   - Kliknij przycisk **Gotowe**.

   ![Przypisania](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. W okienku **Nowy** przejdź do okienka **kontrola dostępu — > Grant** :

   - Kliknij przycisk **Udziel dostępu**.
   - Kliknij pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   - Kliknij pozycję **Wymagaj wszystkich zaznaczonych kontrolek**.
   - Kliknij pozycję **Wybierz**.
   
   ![Udzielanie dostępu — MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. W sekcji **Włączanie zasad** :

   - Wybierz pozycję **włączone**.
   - Kliknij pozycję **Utwórz**.

   ![Włącz zasady](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)