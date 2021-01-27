---
title: 'Wirtualna sieć WAN: dzierżawa usługi Azure AD dla różnych grup użytkowników: uwierzytelnianie w usłudze Azure AD'
description: Skonfiguruj dzierżawę usługi Azure AD na potrzeby uwierzytelniania P2S OpenVPN, a następnie utwórz i zarejestruj wiele aplikacji w usłudze Azure AD, aby zezwolić na inny dostęp dla różnych użytkowników i grup.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e25ef7f55492be4ee491b9ebbbef4aa1eb03c80b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898091"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Utwórz dzierżawę usługi Azure Active Directory (AD) dla połączeń protokołu P2S OpenVPN

Podczas nawiązywania połączenia z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania usługi RADIUS. Jednak w przypadku korzystania z otwartego protokołu sieci VPN można również użyć uwierzytelniania Azure Active Directory. Jeśli chcesz, aby inny zestaw użytkowników mógł nawiązać połączenie z innymi bramami, możesz zarejestrować wiele aplikacji w usłudze AD i połączyć je z innymi bramami.

Ten artykuł ułatwia skonfigurowanie dzierżawy usługi Azure AD na potrzeby uwierzytelniania P2S OpenVPN i tworzenie i rejestrowanie wielu aplikacji w usłudze Azure AD w celu umożliwienia różnym użytkownikom i grupom dostępu do różnych użytkowników.

> [!NOTE]
> Uwierzytelnianie za pomocą usługi Azure AD jest obsługiwane tylko dla &reg; połączeń protokołu OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Utwórz nową konfigurację P2S

Konfiguracja P2S definiuje parametry służące do łączenia z klientami zdalnymi.

1. Ustaw następujące zmienne, zastępując wartości odpowiednie dla danego środowiska.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Uruchom następujące polecenia, aby utworzyć konfigurację:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Nie używaj identyfikatora aplikacji klienta sieci VPN platformy Azure w powyższych poleceniach: spowoduje to przyznanie wszystkim użytkownikom dostępu do bramy. Użyj identyfikatora zarejestrowanej aplikacji.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Edytowanie przypisania centrum

1. Przejdź do bloku **centra** w wirtualnej sieci WAN.

2. Wybierz centrum, do którego chcesz skojarzyć konfigurację serwera sieci VPN, a następnie kliknij przycisk wielokropka (...).

    ![Zrzut ekranu przedstawia polecenie Edytuj centrum wirtualne wybrane z menu.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Kliknij pozycję **Edytuj koncentrator wirtualny**.

4. Zaznacz pole wyboru **Uwzględnij bramę punkt-lokacja** i wybierz żądaną **jednostkę skalowania bramy** .

    ![Zrzut ekranu przedstawia okno dialogowe Edytowanie centrum wirtualnego, w którym można wybrać jednostkę skalowania bramy.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Wprowadź **pulę adresów** , z której będą przypisywane adresy IP klientów sieci VPN.

6. Kliknij pozycję **Potwierdź**.

7. Ukończenie operacji może potrwać do 30 minut.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Pobieranie profilu sieci VPN

Użyj profilu sieci VPN, aby skonfigurować klientów.

1. Na stronie wirtualnej sieci WAN kliknij pozycję **konfiguracje sieci VPN użytkownika**.

2. W górnej części strony kliknij pozycję **Pobierz konfigurację sieci VPN użytkownika**.

3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.

4. Użyj pliku profilu, aby skonfigurować klientów sieci VPN.

5. Wyodrębnij pobrany plik zip.

6. Przejdź do folderu rozpakowanego "AzureVPN".

7. Zanotuj lokalizację pliku "azurevpnconfig.xml". azurevpnconfig.xml zawiera ustawienie dla połączenia sieci VPN i można je zaimportować bezpośrednio do aplikacji klienckiej sieci VPN platformy Azure. Możesz również dystrybuować ten plik do wszystkich użytkowników, którzy muszą łączyć się za pośrednictwem poczty e-mail lub w inny sposób. Aby nawiązać połączenie, użytkownik musi dysponować prawidłowymi poświadczeniami usługi Azure AD.

## <a name="9-configure-user-vpn-clients"></a>9. Konfigurowanie klientów sieci VPN użytkowników

Aby nawiązać połączenie, należy pobrać klienta sieci VPN platformy Azure i zaimportować profil klienta sieci VPN, który został pobrany w poprzednich krokach na każdym komputerze, który chce nawiązać połączenie z siecią wirtualną.

> [!NOTE]
> Uwierzytelnianie za pomocą usługi Azure AD jest obsługiwane tylko dla &reg; połączeń protokołu OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Aby pobrać klienta sieci VPN platformy Azure

Użyj tego [linku](https://go.microsoft.com/fwlink/?linkid=2117554) , aby pobrać klienta sieci VPN platformy Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Aby zaimportować profil klienta

1. Na stronie wybierz pozycję **Importuj**.

    ![Zrzut ekranu przedstawia Importowanie wybrane z menu plus.](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Przejdź do pliku XML profilu i wybierz go. Po wybraniu pliku wybierz pozycję **Otwórz**.

    ![Zrzut ekranu przedstawia otwarte okno dialogowe, w którym można wybrać plik.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Określ nazwę profilu i wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawia dodaną nazwę połączenia i przycisk Zapisz zaznaczone.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Wybierz pozycję **Połącz** , aby nawiązać połączenie z siecią VPN.

    ![Zrzut ekranu przedstawia przycisk Połącz dla właśnie utworzonego połączenia.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Po nawiązaniu połączenia ikona zmieni kolor na zielony i powiedzie **się.**

    ![Zrzut ekranu przedstawia połączenie w stanie połączonym z opcją rozłączenia.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Aby usunąć profil klienta

1. Wybierz wielokropek (...) obok profilu klienta, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![Zrzut ekranu przedstawia polecenie Usuń wybrane z menu.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Wybierz pozycję **Usuń** , aby usunąć.

    ![Zrzut ekranu przedstawia okno dialogowe potwierdzenia z opcją usunięcia lub anulowania.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Aby zdiagnozować problemy z połączeniem

1. Aby zdiagnozować problemy z połączeniem, można użyć narzędzia do **diagnozowania** . Wybierz wielokropek (...) obok połączenia sieci VPN, które chcesz zdiagnozować, aby wyświetlić menu. Następnie wybierz pozycję **Diagnozuj**.

    ![Zrzut ekranu przedstawia wybraną diagnostykę z menu.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Na stronie **Właściwości połączenia** wybierz pozycję **Uruchom diagnostykę**.

    ![Zrzut ekranu przedstawia przycisk Uruchom diagnostykę dla połączenia.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Zaloguj się przy użyciu swoich poświadczeń.

    ![Diagnozuj 3](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Wyświetl wyniki diagnostyki.

    ![Zrzut ekranu przedstawia przycisk Uruchom diagnostykę dla połączenia.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Zaloguj się przy użyciu swoich poświadczeń.

    ![Zrzut ekranu przedstawia okno dialogowe logowania dla tej akcji.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Wyświetl wyniki diagnostyki.

    ![Zrzut ekranu przedstawia wyniki diagnostyki.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Wyświetl wirtualną sieć WAN

1. Przejdź do wirtualnej sieci WAN.

2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator.

3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Czyszczenie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
