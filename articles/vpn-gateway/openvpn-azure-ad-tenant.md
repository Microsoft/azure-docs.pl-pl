---
title: 'VPN Gateway: dzierżawa usługi Azure AD dla połączeń sieci VPN P2S: uwierzytelnianie usługi Azure AD'
description: Dowiedz się, jak skonfigurować dzierżawę usługi Azure AD na potrzeby P2S Open VPN Authentication.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: alzam
ms.openlocfilehash: 451323b8ea4006ba6e2b63d12cc54b2b7c0b60c6
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109038"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Tworzenie dzierżawy usługi Azure Active Directory dla połączeń protokołu OpenVPN typu punkt-lokacja

Podczas nawiązywania połączenia z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania usługi RADIUS. Jednak w przypadku korzystania z otwartego protokołu sieci VPN można również użyć uwierzytelniania Azure Active Directory. Ten artykuł ułatwia skonfigurowanie dzierżawy usługi Azure AD na potrzeby P2S Open VPN Authentication.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Upewnij się, że dzierżawa usługi Azure AD

Upewnij się, że masz dzierżawę usługi Azure AD. Jeśli nie masz dzierżawy usługi Azure AD, możesz ją utworzyć, wykonując czynności opisane w artykule [Tworzenie nowej dzierżawy](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nazwa organizacji
* Początkowa nazwa domeny

Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. tworzenie użytkowników dzierżawy usługi Azure AD

Dzierżawa usługi Azure AD wymaga następujących kont: konta administratora globalnego i konta użytkownika głównego. Główne konto użytkownika jest używane jako główne konto osadzania (konto usługi). Podczas tworzenia konta użytkownika dzierżawy usługi Azure AD można dostosować rolę katalogu dla typu użytkownika, który ma zostać utworzony.

Wykonaj kroki opisane w [tym artykule](../active-directory/fundamentals/add-users-azure-active-directory.md) , aby utworzyć co najmniej dwóch użytkowników dla dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu** w celu utworzenia typów kont:

* Administrator globalny
* Użytkownik

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Włącz uwierzytelnianie usługi Azure AD na bramie sieci VPN

1. Znajdź identyfikator katalogu katalogu, który ma być używany do uwierzytelniania. Jest on wymieniony w sekcji właściwości na stronie Active Directory.

    ![Zrzut ekranu przedstawiający stronę "Właściwości katalogu" z wyróżnioną pozycją "identyfikator katalogu".](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Skopiuj identyfikator katalogu.

3. Zaloguj się do Azure Portal jako użytkownik, do którego przypisano rolę **administratora globalnego** .

4. Następnie udziel zgody administratora. Skopiuj i wklej adres URL odnoszący się do lokalizacji wdrożenia na pasku adresu przeglądarki:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Niemcy

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure w Chinach — 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```
> [!NOTE]
> Jeśli używasz konta administratora globalnego, które nie jest natywne dla dzierżawy usługi Azure AD w celu zapewnienia zgody, Zastąp ciąg "Common" identyfikatorem katalogu usługi Azure AD adresem URL. W niektórych innych przypadkach może być również konieczne zastąpienie "wspólnych" identyfikatorem katalogu.
>

5. Jeśli zostanie wyświetlony monit, wybierz konto **administratora globalnego** .

    ![Identyfikator katalogu](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Wybierz pozycję **Akceptuj** po wyświetleniu monitu.

    ![Zrzut ekranu przedstawia uprawnienia dotyczące komunikatów, które zażądały akceptacji w organizacji, z informacjami i opcją akceptacji.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. W ramach usługi Azure AD w **aplikacjach dla przedsiębiorstw**zostanie wyświetlona lista **Azure VPN** .

    ![Zrzut ekranu przedstawiający stronę "wszystkie aplikacje".](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Jeśli nie masz jeszcze działającego środowiska punkt-lokacja, postępuj zgodnie z instrukcjami, aby je utworzyć. Zobacz [Tworzenie sieci VPN typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) , aby utworzyć i skonfigurować bramę sieci VPN typu punkt-lokacja. 

    > [!IMPORTANT]
    > Podstawowa jednostka SKU nie jest obsługiwana w przypadku OpenVPN.

9. Włącz uwierzytelnianie usługi Azure AD na bramie sieci VPN, przechodząc do **konfiguracji typu punkt-lokacja** i wybierając **OpenVPN (SSL)** jako **Typ tunelu**. Wybierz **Azure Active Directory** jako **Typ uwierzytelniania** , a następnie wypełnij informacje w sekcji **Azure Active Directory** .

    ![Sieć VPN platformy Azure](./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png)


   > [!NOTE]
   > Upewnij się, że na końcu wartości dołączysz końcowy ukośnik `AadIssuerUri` . W przeciwnym razie połączenie może się nie powieść.

10. Utwórz i Pobierz profil, klikając łącze **Pobierz klienta sieci VPN** .

11. Wyodrębnij pobrany plik zip.

12. Przejdź do folderu rozpakowanego "AzureVPN".

13. Zanotuj lokalizację pliku "azurevpnconfig.xml". azurevpnconfig.xml zawiera ustawienie dla połączenia sieci VPN i można je zaimportować bezpośrednio do aplikacji klienckiej sieci VPN platformy Azure. Możesz również dystrybuować ten plik do wszystkich użytkowników, którzy muszą łączyć się za pośrednictwem poczty e-mail lub w inny sposób. Aby nawiązać połączenie, użytkownik musi dysponować prawidłowymi poświadczeniami usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby można było połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
