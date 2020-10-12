---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
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
   - Kliknij pozycję **Gotowe**.

   ![Przypisania](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. W okienku **Nowy** przejdź do okienka **kontrola dostępu — > Grant** :

   - Kliknij przycisk **Udziel dostępu**.
   - Kliknij pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   - Kliknij pozycję **Wymagaj wszystkich zaznaczonych kontrolek**.
   - Kliknij pozycję **Wybierz**.
   
   ![Udzielanie dostępu — MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. W sekcji **Włączanie zasad** :

   - Wybierz pozycję **włączone**.
   - Kliknij przycisk **Utwórz**.

   ![Włącz zasady](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)