---
title: Pula hostów systemu Windows Virtual Desktop (klasyczny) Azure Marketplace — Azure
description: Jak utworzyć pulę hostów pulpitu wirtualnego systemu Windows (klasyczny) przy użyciu portalu Azure Marketplace.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04184a658ae1efd03afd3e920818eb5317bc7553
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008597"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>Samouczek: Tworzenie puli hostów w programie Virtual Desktop systemu Windows (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../create-host-pools-azure-marketplace.md).

W tym samouczku dowiesz się, jak utworzyć pulę hostów w ramach dzierżawy pulpitu wirtualnego systemu Windows przy użyciu oferty Microsoft Azure Marketplace.

Pule hostów są kolekcją co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy usług pulpitu wirtualnego systemu Windows. Każda pula hostów może zawierać grupę aplikacji, z którą użytkownicy mogą wchodzić w pracę, tak jak na pulpicie fizycznym.

Zadania w tym samouczku obejmują:

> [!div class="checklist"]
>
> * Utwórz pulę hostów w programie Virtual Desktop systemu Windows.
> * Utwórz grupę zasobów z maszynami wirtualnymi w ramach subskrypcji platformy Azure.
> * Dołącz maszyny wirtualne do domeny Active Directory.
> * Zarejestruj maszyny wirtualne za pomocą pulpitu wirtualnego systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

* Dzierżawa w pulpicie wirtualnym. Poprzedni [samouczek](tenant-setup-azure-active-directory.md) tworzy dzierżawę.
* [Moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/).

Po utworzeniu tego modułu Uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Uruchamianie oferty portalu Azure Marketplace w celu aprowizacji nowej puli hostów

Aby uruchomić ofertę Azure Marketplace w celu aprowizacji nowej puli hostów:

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
1. W oknie wyszukiwania portalu Marketplace wprowadź **Windows Virtual Desktop** .
1. Wybierz pozycję **pulpit wirtualny systemu Windows — zapewnij pulę hostów**, a następnie wybierz pozycję **Utwórz**.

Następnie postępuj zgodnie z instrukcjami w następnej sekcji, aby wprowadzić informacje dla odpowiednich kart.

### <a name="basics"></a>Podstawy

Oto co należy zrobić w przypadku karty **podstawowe** :

1. Wybierz **subskrypcję**.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową** i podaj nazwę nowej grupy zasobów.
1. Wybierz **region**.
1. Wprowadź nazwę puli hostów, która jest unikatowa w ramach dzierżawy pulpitu wirtualnego systemu Windows.
1. Wybierz pozycję **Typ pulpitu**. W przypadku wybrania opcji **osobiste**każdy użytkownik, który łączy się z tą pulą hostów, zostaje trwale przypisany do maszyny wirtualnej.
1. Wprowadź użytkowników, którzy mogą logować się do klientów pulpitu wirtualnego systemu Windows i uzyskać dostęp do pulpitu. Użyj listy rozdzielanej przecinkami. Na przykład jeśli chcesz przypisać `user1@contoso.com` i `user2@contoso.com` uzyskać dostęp, wprowadź *`user1@contoso.com,user2@contoso.com`*
1. W polu **Lokalizacja metadanych usługi**wybierz tę samą lokalizację co sieć wirtualna, która ma łączność z serwerem Active Directory.

   >[!IMPORTANT]
   >Jeśli używasz czystego Azure Active Directory Domain Services (Azure AD DS) i rozwiązania Azure Active Directory (Azure AD), upewnij się, że wdrożono pulę hostów w tym samym regionie co usługa Azure AD DS, aby uniknąć błędów przyłączania do domeny i poświadczeń.

1. Wybierz pozycję **Dalej: Skonfiguruj maszyny wirtualne**.

### <a name="configure-virtual-machines"></a>Konfigurowanie maszyn wirtualnych

Na karcie **Konfiguruj maszyny wirtualne** :

1. Zaakceptuj wartości domyślne lub Dostosuj liczbę i rozmiar maszyn wirtualnych.

    >[!NOTE]
    >Jeśli określony rozmiar maszyny wirtualnej nie jest wyświetlany w selektorze rozmiaru, oznacza to, że nie zostało to jeszcze zrobione przez nas do narzędzia Azure Marketplace. Aby zażądać rozmiaru, Utwórz żądanie lub zagłosuj na istniejące żądanie na [forum Windows Virtual Desktop UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Wprowadź prefiks nazw maszyn wirtualnych. Na przykład, jeśli wprowadzisz *prefiks*, maszyny wirtualne będą nazywane **prefiksem-0**, **prefiks-1**i tak dalej.
1. Wybierz pozycję **Dalej: ustawienia maszyny wirtualnej**.

### <a name="virtual-machine-settings"></a>Ustawienia maszyny wirtualnej

Na karcie **ustawienia maszyny wirtualnej** :

1. W polu **Źródło obrazu**wybierz źródło i wprowadź odpowiednie informacje na temat sposobu ich znalezienia i sposobu ich przechowywania. Opcje różnią się w przypadku usługi BLOB Storage, obrazu zarządzanego i galerii.

   W przypadku wybrania opcji nie używaj dysków zarządzanych wybierz konto magazynu zawierające plik *VHD* .
1. Wprowadź główną nazwę użytkownika i hasło. To konto musi być kontem domeny, które będzie przyłączać maszyny wirtualne do domeny Active Directory. Ta sama nazwa użytkownika i hasło zostaną utworzone na maszynach wirtualnych jako konto lokalne. Możesz zresetować te konta lokalne później.

   >[!NOTE]
   > Jeśli dołączysz maszyny wirtualne do środowiska AD DS platformy Azure, upewnij się, że użytkownik przyłączania do domeny jest członkiem [grupy Administratorzy domeny usługi AAD](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > Konto musi być również częścią domeny zarządzanej usługi Azure AD DS lub dzierżawy usługi Azure AD. Konta z zewnętrznych katalogów skojarzonych z dzierżawą usługi Azure AD nie mogą być prawidłowo uwierzytelniane podczas procesu przyłączania do domeny.

1. Wybierz **sieć wirtualną** , która ma łączność z serwerem Active Directory, a następnie wybierz podsieć do hostowania maszyn wirtualnych.
1. Wybierz pozycję **Dalej: informacje o pulpicie wirtualnym systemu Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informacje o dzierżawie pulpitu wirtualnego systemu Windows

Na karcie **Informacje o dzierżawie pulpitu wirtualnego systemu Windows** :

1. W polu **Nazwa grupy dzierżawy pulpitu wirtualnego systemu Windows**wprowadź nazwę grupy dzierżawców zawierającej dzierżawcę. Pozostaw to ustawienie domyślne, chyba że podano konkretną nazwę grupy dzierżawców.
1. W polu **Nazwa dzierżawy pulpitu wirtualnego systemu Windows**wprowadź nazwę dzierżawy, w której chcesz utworzyć pulę hostów.
1. Określ typ poświadczeń, które mają być używane do uwierzytelniania jako właściciel usług pulpitu wirtualnego systemu Windows. Wprowadź nazwę UPN lub nazwę główną usługi i hasło.

   Jeśli ukończono tworzenie jednostek [usługi i przypisań ról przy użyciu programu PowerShell](create-service-principal-role-powershell.md), wybierz pozycję Nazwa **główna usługi**.

1. W przypadku nazwy **głównej usługi**dla **identyfikatora dzierżawy usługi Azure AD**Wprowadź konto administratora dzierżawy dla wystąpienia usługi Azure AD, które zawiera nazwę główną. Obsługiwane są tylko jednostki usługi z poświadczeniami hasła.
1. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Ukończ instalację i Utwórz maszynę wirtualną

W obszarze **Przegląd i tworzenie**Przejrzyj informacje o instalacji. Jeśli chcesz zmienić coś, wróć i wprowadź zmiany. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz** , aby wdrożyć pulę hostów.

W zależności od liczby tworzonych maszyn wirtualnych proces ten może potrwać 30 minut lub dłużej.

>[!IMPORTANT]
> Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389 dla użytkowników w celu uzyskania dostępu do maszyn wirtualnych puli hostów.
>
> Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy korzystanie z dostępu just in Time. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie portów zarządzania przy użyciu dostępu just in Time](../../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Obowiązkowe Przypisywanie dodatkowych użytkowników do grupy aplikacji klasycznych

Po zakończeniu tworzenia puli w witrynie Azure Marketplace można przypisać więcej użytkowników do grupy aplikacji klasycznych. Jeśli nie chcesz dodawać więcej, Pomiń tę sekcję.

Aby przypisać użytkowników do grupy aplikacji klasycznych:

1. Otwórz okno programu PowerShell.

1. Uruchom następujące polecenie, aby zalogować się do środowiska pulpitu wirtualnego systemu Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Dodaj użytkowników do grupy aplikacji klasycznych za pomocą tego polecenia:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Nazwa UPN użytkownika powinna być zgodna z tożsamością użytkownika w usłudze Azure AD, na przykład *user1@contoso.com* . Jeśli chcesz dodać wielu użytkowników, uruchom polecenie dla każdego użytkownika.

Użytkownicy dodanym do grupy aplikacji klasycznych mogą zalogować się do pulpitu wirtualnego systemu Windows z obsługiwanymi klientami Pulpit zdalny i zobaczyć zasób dla pulpitu sesji.

Oto obecnie obsługiwani klienci:

* [Pulpit zdalny Client for Windows 7 i Windows 10](connect-windows-7-10-2019.md)
* [Klient sieci Web pulpitu wirtualnego systemu Windows](connect-web-2019.md)

## <a name="next-steps"></a>Następne kroki

Utworzono pulę hostów i przypisano użytkowników do dostępu do jej pulpitu. Pulę hostów można wypełniać za pomocą programów RemoteApp. Aby dowiedzieć się więcej na temat zarządzania aplikacjami w programie Virtual Desktop systemu Windows, zobacz ten samouczek:

> [!div class="nextstepaction"]
> [Samouczek zarządzania grupami aplikacji](manage-app-groups-2019.md)
