---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: df3d1c34fe0fc045e829c2ea9166664f06fd3127
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116714"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Tworzenie dzierżawy usługi Azure AD

Utwórz dzierżawę usługi Azure AD, wykonując czynności opisane w artykule [Tworzenie nowej dzierżawy](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nazwa organizacji
* Początkowa nazwa domeny

  Przykład:

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. tworzenie użytkowników dzierżawy

W tym kroku utworzysz dwóch użytkowników dzierżawy usługi Azure AD: jedno konto administratora globalnego i jedno konto użytkownika głównego. Główne konto użytkownika jest używane jako główne konto osadzania (konto usługi). Podczas tworzenia konta użytkownika dzierżawy usługi Azure AD można dostosować rolę katalogu dla typu użytkownika, który ma zostać utworzony. Wykonaj kroki opisane w [tym artykule](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) , aby utworzyć co najmniej dwóch użytkowników dla dzierżawy usługi Azure AD. Pamiętaj, aby zmienić **rolę katalogu** w celu utworzenia typów kont:

* Administrator globalny
* Użytkownik

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Zarejestruj klienta sieci VPN

Zarejestruj klienta sieci VPN w dzierżawie usługi Azure AD.

1. Znajdź identyfikator katalogu katalogu, który ma być używany do uwierzytelniania. Jest on wymieniony w sekcji właściwości na stronie Active Directory.

    ![Identyfikator katalogu](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

> [!NOTE]
> Jeśli używasz konta administratora globalnego, które nie jest natywne dla dzierżawy usługi Azure AD w celu zapewnienia zgody, Zastąp ciąg "Common" identyfikatorem katalogu usługi Azure AD adresem URL. W niektórych innych przypadkach może być również konieczne zastąpienie "wspólnych" identyfikatorem katalogu.
>

5. Jeśli zostanie wyświetlony monit, wybierz konto **administratora globalnego** .

    ![Identyfikator katalogu](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Wybierz pozycję **Akceptuj** po wyświetleniu monitu.

    ![Zrzut ekranu przedstawia okno z żądanymi uprawnieniami do organizacji i informacji o żądaniu.](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. W ramach usługi Azure AD w **aplikacjach dla przedsiębiorstw**zostanie wyświetlona lista **sieci VPN platformy Azure** .

     ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Zarejestruj dodatkowe aplikacje

W tym kroku zarejestrowano dodatkowe aplikacje dla różnych użytkowników i grup.

1. W Azure Active Directory kliknij przycisk **rejestracje aplikacji** , a następnie pozycję **+ Nowa rejestracja**.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na stronie **Rejestrowanie aplikacji** wprowadź **nazwę**. Wybierz żądane **obsługiwane typy kont**, a następnie kliknij pozycję **zarejestruj**.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Po zarejestrowaniu nowej aplikacji kliknij pozycję **uwidaczniaj interfejs API** w bloku aplikacji.

4. Kliknij pozycję **+ Dodaj zakres**.

5. Pozostaw domyślny **Identyfikator URI aplikacji**. Kliknij przycisk **Zapisz i Kontynuuj**.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Wypełnij wymagane pola i upewnij się, że **stan** jest **włączony**. Kliknij pozycję **Dodaj zakres**.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Kliknij pozycję **Uwidacznianie interfejsu API,** a następnie **Dodaj aplikację kliencką**.  W polu **Identyfikator klienta**wprowadź następujące wartości w zależności od chmury:

    - Wprowadź **41b23e61-6c1e-4545-b367-cd054e0ed4b4** for Azure **Public**
    - Wprowadź **51bb15d4-3a4f-4ebf-9dca-40096fe32426** dla **instytucji rządowych** platformy Azure
    - Wprowadź **538ee9e6-310a-468d-afef-ea97365856a9** dla platformy Azure ( **Niemcy** )
    - Wprowadź **49f817b6-84ae-4cc0-928c-73f27289b3aa** dla platformy Azure ( **Chiny** )

8. Kliknij pozycję **Dodaj aplikację**.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Skopiuj **Identyfikator aplikacji (klienta)** ze strony **Przegląd** . Te informacje będą potrzebne do skonfigurowania bram sieci VPN.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Powtórz kroki opisane w sekcji [zarejestruj dodatkowe aplikacje](#register-apps) , aby utworzyć dowolną liczbę aplikacji potrzebnych do wymagania dotyczącego zabezpieczeń. Każda aplikacja zostanie skojarzona z bramą sieci VPN i może mieć innego zestawu użytkowników. Tylko jedna aplikacja może być skojarzona z bramą.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Przypisywanie użytkowników do aplikacji

Przypisz użytkowników do aplikacji.

1. W obszarze **Azure AD-> aplikacje dla przedsiębiorstw**wybierz nowo zarejestrowanej aplikacji i kliknij pozycję **Właściwości**. Upewnij się, że **przypisanie użytkownika jest wymagane?** ma ustawioną **wartość tak**. Kliknij pozycję **Zapisz**.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na stronie Aplikacja kliknij pozycję **Użytkownicy i grupy**, a następnie kliknij pozycję **+ Dodaj użytkownika**.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. W obszarze **Dodaj przypisanie**kliknij pozycję **Użytkownicy i grupy**. Wybierz użytkowników, którym chcesz mieć dostęp do tej aplikacji sieci VPN. Kliknij pozycję **Wybierz**.

    ![Sieć VPN platformy Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
