---
title: Konfigurowanie protokołu Kerberos centrum dystrybucji kluczy proxy Windows Virtual Desktop — Azure
description: Jak skonfigurować pulę hostów Windows Virtual Desktop do korzystania z serwera proxy protokołu Kerberos centrum dystrybucji kluczy proxy.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 9dce264b7f2c88aed11f5b82a61f83cbac6c9697
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785113"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurowanie serwera proxy usługi Kerberos centrum dystrybucji kluczy (wersja zapoznawcza)

> [!IMPORTANT]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie zalecamy używania jej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Klienci dbani o bezpieczeństwo, tacy jak organizacje finansowe lub rządowe, często logują się przy użyciu kart inteligentnych. Karty inteligentne sprawiają, że wdrożenia są bezpieczniejsze, wymagając uwierzytelniania wieloskładnikowego (MFA). Jednak w przypadku części protokołu RDP sesji usługi Windows Virtual Desktop karty inteligentne wymagają bezpośredniego połączenia lub "linii widzenia" z kontrolerem domeny usługi Active Directory (AD) do uwierzytelniania Kerberos. Bez tego bezpośredniego połączenia użytkownicy nie mogą automatycznie logować się do sieci organizacji z połączeń zdalnych. Użytkownicy we wdrożeniu Windows Virtual Desktop mogą używać usługi serwera proxy centrum dystrybucji kluczy do obsługi serwera proxy tego ruchu uwierzytelniania i zdalnego logowania. Serwer proxy centrum dystrybucji kluczy umożliwia uwierzytelnianie Remote Desktop Protocol sesji Windows Virtual Desktop, umożliwiając użytkownikowi bezpieczne logowanie. Dzięki temu praca z domu jest znacznie łatwiejsza i umożliwia bardziej bezproblemowe działanie niektórych scenariuszy odzyskiwania po awarii.

Jednak konfigurowanie serwera proxy centrum dystrybucji kluczy zwykle obejmuje przypisanie roli bramy systemu Windows Server w systemie Windows Server 2016 lub nowszym. Jak używać roli Usługi pulpitu zdalnego, aby zalogować się do Windows Virtual Desktop? Aby odpowiedzieć na to pytanie, przyjrzyjmy się składnikom.

Istnieją dwa składniki usługi Windows Virtual Desktop, które muszą być uwierzytelnione:

- Kanał informacyjny w kliencie Windows Virtual Desktop, który zapewnia użytkownikom listę dostępnych komputerów stacjonarnych lub aplikacji, do których mają dostęp. Ten proces uwierzytelniania odbywa Azure Active Directory, co oznacza, że ten składnik nie jest głównym tematem tego artykułu.
- Sesja protokołu RDP, która wynika z tego, że użytkownik wybiera jeden z dostępnych zasobów. Ten składnik używa uwierzytelniania Kerberos i wymaga serwera proxy centrum dystrybucji kluczy dla użytkowników zdalnych.

W tym artykule opisano sposób konfigurowania kanału informacyjnego w kliencie Windows Virtual Desktop w Azure Portal. Jeśli chcesz dowiedzieć się, jak skonfigurować rolę bramy usług pulpitu zdalnego, zobacz [Wdrażanie roli bramy usług pulpitu zdalnego](/azure/virtual-desktop/rd-gateway-role).

## <a name="requirements"></a>Wymagania

Aby skonfigurować hosta Windows Virtual Desktop z serwerem proxy centrum dystrybucji kluczy, potrzebne są następujące elementy:

- Dostęp do Azure Portal i konta administratora platformy Azure.
- Na zdalnych maszynach klienckich musi być uruchomiony Windows 10 lub Windows 7 i mieć zainstalowanego [klienta pulpitu systemu Windows.](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) Obecnie klient internetowy nie jest obsługiwany.
- Na maszynie musi już być zainstalowany serwer proxy centrum dystrybucji kluczy. Aby dowiedzieć się, jak to zrobić, zobacz Konfigurowanie roli bramy usług [pulpitu zdalnego dla Windows Virtual Desktop](rd-gateway-role.md).
- System operacyjny komputera musi być systemem Windows Server 2016 lub nowszym.

Gdy upewnisz się, że spełniasz te wymagania, możesz rozpocząć pracę.

## <a name="how-to-configure-the-kdc-proxy"></a>Jak skonfigurować serwer proxy centrum dystrybucji kluczy

Aby skonfigurować serwer proxy centrum dystrybucji kluczy:

1. Zaloguj się do Azure Portal jako administrator.

2. Przejdź do Windows Virtual Desktop aplikacji.

3. Wybierz pulę hostów, dla której chcesz włączyć serwer proxy centrum dystrybucji kluczy, a następnie wybierz pozycję **Właściwości protokołu RDP.**

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Azure Portal użytkownika wybierającego pule hostów, nazwę przykładowej puli hostów, a następnie właściwości protokołu RDP.](media/rdp-properties.png)

4. Wybierz **kartę Zaawansowane,** a następnie wprowadź wartość w następującym formacie bez spacji:

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wybraną kartę Zaawansowane z wartością wprowadzona zgodnie z opisem w kroku 4.](media/advanced-tab-selected.png)

5. Wybierz pozycję **Zapisz**.

6. Wybrana pula hostów powinna teraz rozpocząć wydawanie plików połączenia RDP, które zawierają wartość kdcproxyname wprocedywną w kroku 4.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać Usługi pulpitu zdalnego serwera proxy centrum dystrybucji kluczy i przypisać rolę bramy usług pulpitu zdalnego, zobacz [Wdrażanie roli bramy usług pulpitu zdalnego.](rd-gateway-role.md)

Jeśli interesuje Cię skalowanie serwerów proxy centrum dystrybucji kluczy, dowiedz się, jak skonfigurować wysoką dostępność dla serwera proxy centrum dystrybucji kluczy, zobacz Dodawanie wysokiej dostępności do frontu internetowego sieci Web usług pulpitu zdalnego i [bramy.](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)
