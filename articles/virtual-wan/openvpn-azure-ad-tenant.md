---
title: 'Dzierżawa usługi Azure AD dla połączeń sieci VPN użytkownika: uwierzytelnianie usługi Azure AD'
description: Do łączenia się z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD można używać sieci VPN użytkownika wirtualnej sieci WAN platformy Azure (punkt-lokacja).
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91367856"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Przygotowywanie Azure Active Directory dzierżawy dla połączeń protokołu VPN użytkowników OpenVPN

Podczas nawiązywania połączenia z koncentratorem wirtualnym za pośrednictwem protokołu IKEv2 można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania usługi RADIUS. Jednak w przypadku korzystania z protokołu OpenVPN można także użyć uwierzytelniania Azure Active Directory. W tym artykule opisano sposób konfigurowania dzierżawy usługi Azure AD na potrzeby sieci VPN użytkownika wirtualnego sieci WAN (punkt-lokacja) przy użyciu uwierzytelniania OpenVPN.

> [!NOTE]
> Uwierzytelnianie za pomocą usługi Azure AD jest obsługiwane tylko dla &reg; połączeń protokołu OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Tworzenie dzierżawy usługi Azure AD

Upewnij się, że masz dzierżawę usługi Azure AD. Jeśli nie masz dzierżawy usługi Azure AD, możesz ją utworzyć, wykonując czynności opisane w artykule [Tworzenie nowej dzierżawy](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nazwa organizacji
* Początkowa nazwa domeny

Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. tworzenie użytkowników dzierżawy usługi Azure AD

Następnie Utwórz dwa konta użytkowników w nowo utworzonej dzierżawie usługi Azure AD, jedno konto administratora globalnego i jedno konto użytkownika. Konto użytkownika może służyć do testowania uwierzytelniania OpenVPN, a konto administratora globalnego zostanie użyte do udzielenia zgody na rejestrację aplikacji sieci VPN platformy Azure. Po utworzeniu konta użytkownika usługi Azure AD przypiszesz do użytkownika **rolę katalogu** w celu delegowania uprawnień administracyjnych.

Wykonaj kroki opisane w [tym artykule](../active-directory/fundamentals/add-users-azure-active-directory.md) , aby utworzyć dwóch użytkowników dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu** na jednym z utworzonych kont na **administratora globalnego**.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. Udziel zgody na rejestrację aplikacji sieci VPN platformy Azure

1. Zaloguj się do witryny Azure Portal jako użytkownik, który ma przypisaną rolę **administratora globalnego** .

2. Następnie udziel zgody administratora na swoją organizację, dzięki czemu aplikacja sieci VPN platformy Azure będzie mogła się zalogować i odczytać profile użytkowników. Skopiuj i wklej adres URL odnoszący się do lokalizacji wdrożenia na pasku adresu przeglądarki:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Niemcy

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure w Chinach — 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. Jeśli zostanie wyświetlony monit, wybierz konto **administratora globalnego** .

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/pick.png)

4. Wybierz pozycję **Akceptuj** po wyświetleniu monitu.

    ![Zrzut ekranu przedstawia okno dialogowe z komunikatem żądania zaakceptowania dla organizacji i dodatkowych informacji.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. W ramach usługi Azure AD w **aplikacjach dla przedsiębiorstw**powinna zostać wyświetlona lista **sieci VPN platformy Azure** .

    ![Sieć VPN platformy Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Następne kroki

Aby można było połączyć się z sieciami wirtualnymi przy użyciu uwierzytelniania usługi Azure AD, należy utworzyć konfigurację sieci VPN użytkownika i skojarzyć ją z koncentratorem wirtualnym. Zobacz [Konfigurowanie uwierzytelniania usługi Azure AD na potrzeby połączenia punkt-lokacja z platformą Azure](virtual-wan-point-to-site-azure-ad.md).
