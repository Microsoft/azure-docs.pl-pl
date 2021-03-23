---
title: Konfigurowanie serwera proxy protokołu Kerberos centrum dystrybucji kluczy Windows Virtual Desktop — Azure
description: Jak skonfigurować pulę hostów usług pulpitu wirtualnego systemu Windows do korzystania z serwera proxy centrum dystrybucji kluczy Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: bcf28fbc0d2f4ec9eeac5bcb8f0b2c9b65a62b6b
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775043"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurowanie serwera proxy protokołu Kerberos centrum dystrybucji kluczy (wersja zapoznawcza)

> [!IMPORTANT]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Klienci korzystający z zabezpieczeń, tacy jak finanse lub organizacje rządowe, często logują się przy użyciu kart inteligentnych. Karty inteligentne sprawiają, że wdrożenia są bezpieczniejsze przez wymaganie uwierzytelniania wieloskładnikowego (MFA). Jednak w przypadku części protokołu RDP sesji pulpitu wirtualnego systemu Windows karty inteligentne wymagają bezpośredniego połączenia lub "wiersz wglądu" z kontrolerem domeny Active Directory (AD) na potrzeby uwierzytelniania przy użyciu protokołu Kerberos. Bez bezpośredniego połączenia użytkownicy nie mogą automatycznie logować się do sieci organizacji z połączeń zdalnych. Użytkownicy w ramach wdrożenia pulpitu wirtualnego systemu Windows mogą używać usługi proxy centrum dystrybucji kluczy do proxy tego ruchu uwierzytelniania i logowania się zdalnie. Serwer proxy centrum dystrybucji kluczy umożliwia uwierzytelnianie Remote Desktop Protocol sesji pulpitu wirtualnego systemu Windows, co pozwala na bezpieczne logowanie użytkownika. Ułatwia to pracę z domu i pozwala na efektywniejsze działanie niektórych scenariuszy odzyskiwania po awarii.

Jednak skonfigurowanie serwera proxy centrum dystrybucji kluczy zazwyczaj obejmuje przypisanie roli bramy systemu Windows Server w systemie Windows Server 2016 lub nowszym. Jak używać roli Usługi pulpitu zdalnego do logowania się do pulpitu wirtualnego systemu Windows? Aby to odpowiedzieć, przyjrzyjmy się szybkiemu wyszukiwaniu składników.

Istnieją dwa składniki usługi pulpitu wirtualnego systemu Windows, które muszą zostać uwierzytelnione:

- Kanał informacyjny klienta pulpitu wirtualnego systemu Windows, który zapewnia użytkownikom listę dostępnych pulpitów lub aplikacji, do których mają dostęp. Ten proces uwierzytelniania występuje w Azure Active Directory, co oznacza, że ten składnik nie jest fokusem tego artykułu.
- Sesja RDP, która powoduje, że użytkownik wybiera jeden z tych dostępnych zasobów. Ten składnik używa uwierzytelniania Kerberos i wymaga serwera proxy centrum dystrybucji kluczy dla użytkowników zdalnych.

W tym artykule przedstawiono sposób konfigurowania kanału informacyjnego w kliencie pulpitu wirtualnego systemu Windows w Azure Portal. Jeśli chcesz dowiedzieć się, jak skonfigurować rolę bramy usług pulpitu zdalnego, zobacz [Wdrażanie roli bramy usług pulpitu zdalnego](/windows-server/remote/rd-gateway-role).

## <a name="requirements"></a>Wymagania

Aby skonfigurować hosta sesji usług pulpitu wirtualnego systemu Windows przy użyciu serwera proxy centrum dystrybucji kluczy, potrzebne są następujące elementy:

- Dostęp do Azure Portal i konta administratora platformy Azure.
- Na zdalnych komputerach klienckich musi być uruchomiony system Windows 10 lub Windows 7 i zainstalowany [Klient stacjonarny z systemem Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) .
- Na maszynie musi być już zainstalowany serwer proxy centrum dystrybucji kluczy. Aby dowiedzieć się, jak to zrobić, zobacz [Konfigurowanie roli bramy usług pulpitu zdalnego dla pulpitu wirtualnego systemu Windows](rd-gateway-role.md).
- System operacyjny maszyny musi być w systemie Windows Server 2016 lub nowszym.

Po upewnieniu się, że spełniasz te wymagania, możesz zacząć korzystać z programu.

## <a name="how-to-configure-the-kdc-proxy"></a>Jak skonfigurować serwer proxy centrum dystrybucji kluczy

Aby skonfigurować serwer proxy centrum dystrybucji kluczy:

1. Zaloguj się do Azure Portal jako administrator.

2. Przejdź do strony pulpit wirtualny systemu Windows.

3. Wybierz pulę hostów, dla której chcesz włączyć serwer proxy centrum dystrybucji kluczy, a następnie wybierz pozycję **Właściwości RDP**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę Azure Portal, w której użytkownik wybiera pule hostów, a następnie nazwę przykładowej puli hostów, a następnie właściwości protokołu RDP.](media/rdp-properties.png)

4. Wybierz kartę **Zaawansowane** , a następnie wprowadź wartość w następującym formacie bez spacji:

    
    > kdcproxyname: s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wybraną kartę Zaawansowane z wartością wprowadzoną w kroku 4.](media/advanced-tab-selected.png)

5. Wybierz pozycję **Zapisz**.

6. Wybrana Pula hostów powinna teraz rozpocząć wystawianie plików połączeń RDP zawierających wartość kdcproxyname wprowadzoną w kroku 4.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać Usługi pulpitu zdalnego stronie serwera proxy centrum dystrybucji kluczy i przypisać rolę bramy usług pulpitu zdalnego, zobacz [Wdrażanie roli bramy usług pulpitu zdalnego](/windows-server/remote/rd-gateway-role).

Jeśli interesuje Cię skalowanie serwerów proxy centrum dystrybucji kluczy, Dowiedz się, jak skonfigurować wysoką dostępność serwera proxy centrum dystrybucji kluczy, [dodając wysoką dostępność do frontonu sieci Web i bramy usług pulpitu zdalnego](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).
