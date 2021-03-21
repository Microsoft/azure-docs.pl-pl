---
title: Pulpity wirtualne systemu Windows — często zadawane pytania — Azure
description: Często zadawane pytania i najlepsze rozwiązania dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ffea2d84f1a5149670976beef3b9af847ae31a35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582140"
---
# <a name="windows-virtual-desktop-faq"></a>Często zadawane pytania na temat usługi Windows Virtual Desktop

W tym artykule znajdują się odpowiedzi na często zadawane pytania i wyjaśniono najlepsze rozwiązania dotyczące pulpitu wirtualnego systemu Windows.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Jakie są minimalne uprawnienia administratora potrzebne do zarządzania obiektami?

Jeśli chcesz utworzyć pule hostów i inne obiekty, musisz mieć przypisaną rolę współautor w subskrypcji lub grupie zasobów, z którą pracujesz.

Aby opublikować grupy aplikacji dla użytkowników lub grup użytkowników, musisz mieć przypisaną rolę administratora dostępu użytkowników w grupie aplikacji.

Aby ograniczyć administrator do zarządzania sesjami użytkowników, takich jak wysyłanie komunikatów do użytkowników, Wylogowywanie użytkowników i tak dalej, można utworzyć role niestandardowe. Na przykład:

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Czy obsługa pulpitów wirtualnych systemu Windows jest dzielona Azure Active Directory modele?

Gdy użytkownik jest przypisany do grupy aplikacji, usługa wykonuje proste przypisanie roli platformy Azure. W związku z tym Azure Active Directory użytkownika (AD) i usługi Azure AD grupy aplikacji muszą znajdować się w tej samej lokalizacji. Wszystkie obiekty usługi, takie jak pule hostów, grupy aplikacji i obszary robocze, muszą również znajdować się w tej samej usłudze Azure AD jako użytkownik.

Maszyny wirtualne można tworzyć w innej usłudze Azure AD, o ile Active Directory z usługą Azure AD użytkownika w tej samej sieci wirtualnej (VNET).

## <a name="what-are-location-restrictions"></a>Co to są ograniczenia lokalizacji?

Wszystkie zasoby usługi mają skojarzoną lokalizację. Lokalizacja puli hostów określa, w którym obszarze geograficznym są przechowywane metadane usługi dla puli hostów. Grupa aplikacji nie może istnieć bez puli hostów. Jeśli dodasz aplikacje do grupy aplikacji RemoteApp, potrzebujesz także hosta sesji, aby określić aplikacje menu Start. W przypadku każdej akcji grupy aplikacji potrzebny jest również odpowiedni dostęp do danych w puli hostów. Aby upewnić się, że dane nie są transferowane między wieloma lokalizacjami, lokalizacja grupy aplikacji powinna być taka sama jak Pula hostów.

Obszary robocze muszą również znajdować się w tej samej lokalizacji co grupy aplikacji. Za każdym razem, gdy obszar roboczy jest aktualizowany, powiązana z nim Grupa aplikacji jest aktualizowana. Podobnie jak w przypadku grup aplikacji, usługa wymaga, aby wszystkie obszary robocze były skojarzone z grupami aplikacji utworzonymi w tej samej lokalizacji.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Jak rozszerzyć właściwości obiektu w programie PowerShell?

Po uruchomieniu polecenia cmdlet programu PowerShell zobaczysz tylko nazwę zasobu i lokalizację.

Na przykład:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Aby wyświetlić wszystkie właściwości zasobu, należy dodać albo `format-list` `fl` do końca polecenia cmdlet.

Na przykład:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Aby wyświetlić określone właściwości, należy dodać nazwy określonych właściwości po `format-list` lub `fl` .

Na przykład:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Czy pulpit wirtualny systemu Windows obsługuje użytkowników-Gości?

Pulpit wirtualny systemu Windows nie obsługuje kont użytkowników Gości usługi Azure AD. Załóżmy na przykład, że grupa użytkowników-Gości ma Microsoft 365 E3 dla użytkownika, Windows E3 dla użytkownika lub WIN VDA w swojej firmie, ale są użytkownikami-Gośćmi w usłudze Azure AD innej firmy. Druga firma będzie zarządzać obiektami użytkowników Gości w usłudze Azure AD i Active Directory takich jak konta lokalne.

Nie możesz korzystać z własnych licencji na korzyść osoby trzeciej. Ponadto pulpit wirtualny systemu Windows nie obsługuje obecnie konta Microsoft (MSA).

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Dlaczego nie widzę adresu IP klienta w tabeli WVDConnections?

Obecnie nie mamy niezawodnego sposobu zbierania adresów IP klienta sieci Web, więc nie dodaliśmy tej wartości w tabeli.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Jak pulpity wirtualne systemu Windows obsługują kopie zapasowe?

Na platformie Azure dostępnych jest wiele opcji obsługi kopii zapasowych. Możesz użyć usługi Azure Backup, Site Recovery i migawek.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Czy pulpit wirtualny systemu Windows obsługuje aplikacje do współpracy innych firm?

Pulpit wirtualny systemu Windows jest obecnie zoptymalizowany dla zespołów. Obecnie firma Microsoft nie obsługuje aplikacji współpracy innych firm, takich jak powiększenie. Organizacje innych firm są odpowiedzialne za przekazywanie klientom wytycznych dotyczących zgodności. Pulpit wirtualny systemu Windows nie obsługuje również usługi Skype dla firm.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>Czy mogę zmienić pule hostów w puli na osobiste?

Po utworzeniu puli hostów nie można zmienić jej typu. Można jednak przenieść wszystkie maszyny wirtualne zarejestrowane w puli hostów do innego typu puli hostów.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Jaki jest największy rozmiar profilu FSLogix może obsłużyć?

Ograniczenia lub limity przydziału w programie FSLogix zależą od sieci szkieletowej magazynu używanej do przechowywania plików wirtualnego dysku twardego (X) profilu użytkownika.

W poniższej tabeli przedstawiono przykład liczby operacji we/wy, jaką musi obsługiwać profil FSLogix. Wymagania mogą się różnić w zależności od użytkownika, aplikacji i aktywności w każdym profilu.

| Zasób | Wymaganie |
|---|---|
| Stałe operacje we/wy stanu | 10 |
| Logowanie/logowanie IOPS | 50 |

Przykład w tej tabeli jest pojedynczy użytkownik, ale może służyć do oceny wymagań dotyczących łącznej liczby użytkowników w danym środowisku. Na przykład potrzeba około 1 000 operacji we/wy dla użytkowników 100 i około 5 000 operacji we/wy podczas logowania i wylogowywania.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Czy istnieje limit skalowania pul hostów utworzonych w Azure Portal?

Czynniki te mogą mieć wpływ na limit skalowania pul hostów:

- Szablon platformy Azure jest ograniczony do 800 obiektów. Aby dowiedzieć się więcej, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits). Każda maszyna wirtualna tworzy również około sześciu obiektów, co oznacza, że można utworzyć około 132 maszyn wirtualnych przy każdym uruchomieniu szablonu.

- Istnieją ograniczenia dotyczące liczby rdzeni, które można utworzyć na region i na subskrypcję. Na przykład jeśli masz subskrypcję Enterprise Agreement, możesz utworzyć 350 rdzeni. Aby określić liczbę maszyn wirtualnych, które można utworzyć przy każdym uruchomieniu szablonu, należy podzielić 350 przez wartość domyślną liczbę rdzeni na maszynę wirtualną lub własny limit rdzeni. Więcej informacji znajduje się w [Virtual Machines limitów — Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- Nazwa prefiksu maszyny wirtualnej i liczba maszyn wirtualnych są krótsze niż 15 znaków. Aby dowiedzieć się więcej, zobacz [reguły nazewnictwa i ograniczenia dotyczące zasobów platformy Azure](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute).

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Czy mogę zarządzać środowiskami pulpitu wirtualnego systemu Windows za pomocą usługi Azure Lighthouse?

Usługa Azure Lighthouse nie obsługuje w pełni zarządzania środowiskami pulpitu wirtualnego systemu Windows. Ponieważ usługa Lighthouse nie obsługuje obecnie zarządzania użytkownikami dzierżawy usługi Azure AD, klienci Lighthouse nadal muszą zalogować się do usługi Azure AD, która jest używana przez klientów do zarządzania użytkowników.

Nie można również używać subskrypcji piaskownicy CSP z usługą pulpitu wirtualnego systemu Windows. Aby dowiedzieć się więcej, zobacz [konto piaskownicy integracji](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account).

Na koniec po włączeniu dostawcy zasobów z poziomu konta właściciela dostawcy CSP konta klienta CSP nie będą mogły modyfikować dostawcy zasobów.

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>Jak często należy włączyć maszynę wirtualną, aby zapobiec problemom z rejestracją?

Po zarejestrowaniu maszyny wirtualnej w puli hostów w ramach usługi pulpitu wirtualnego systemu Windows Agent regularnie odświeża token maszyny wirtualnej za każdym razem, gdy maszyna wirtualna jest aktywna. Certyfikat dla tokenu rejestracji jest ważny przez 90 dni. Ze względu na 90 dzienny limit zalecamy rozpoczęcie maszyn wirtualnych co 90 dni. Włączenie tej maszyny wirtualnej w tym limicie czasu uniemożliwi wygaśnięcie tokenu rejestracji lub jego nieprawidłowość. Jeśli maszyna wirtualna została uruchomiona po upływie 90 dni i występują problemy z rejestracją, postępuj zgodnie z instrukcjami w [przewodniku rozwiązywania problemów agenta usług pulpitu wirtualnego systemu Windows](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) , aby usunąć maszynę wirtualną z puli hostów, ponownie zainstaluj agenta i zarejestruj go w puli.

## <a name="can-i-set-availability-options-when-creating-host-pools"></a>Czy mogę ustawić opcje dostępności podczas tworzenia pul hostów?

Tak. Pule hostów usług pulpitu wirtualnego systemu Windows mają opcję wyboru zestawu dostępności lub stref dostępności podczas tworzenia maszyny wirtualnej. Te opcje dostępności są takie same, jak w przypadku użycia obliczeń na platformie Azure. W przypadku wybrania strefy dla maszyny wirtualnej utworzonej w puli hostów ustawienie to automatycznie dotyczy wszystkich maszyn wirtualnych utworzonych w ramach tej strefy. Jeśli wolisz rozłożyć maszyny wirtualne puli hostów w wielu strefach, musisz postępować zgodnie z instrukcjami w temacie [Dodawanie maszyn wirtualnych z Azure Portal](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal) , aby ręcznie wybrać nową strefę dla każdej nowo utworzonej maszyny wirtualnej.

## <a name="which-availability-option-is-best-for-me"></a>Która opcja dostępności jest Najlepsza dla mnie?

Opcja dostępności, która powinna być używana dla maszyn wirtualnych, zależy od lokalizacji obrazu i pól dysku zarządzanego. W poniższej tabeli opisano relacje poszczególnych ustawień z tymi zmiennymi, które pomogą ustalić, która opcja jest Najlepsza dla danego wdrożenia. 

| Opcja dostępności | Lokalizacja obrazu | Przycisk opcji używania dysku zarządzanego (przycisk radiowy) |
|---|---|---|
| Brak | Galeria | Wyłączone z opcją "tak" jako domyślne |
| Brak | Blob Storage | Włączone z opcją "No" jako domyślne |
| Strefa dostępności | Galeria (opcja magazynu obiektów BLOB wyłączona) | Wyłączone z opcją "tak" jako domyślne |
| Zestaw dostępności z zarządzaną jednostką SKU (dysk zarządzany) | Galeria | Wyłączone z opcją "tak" jako domyślne |
| Zestaw dostępności z zarządzaną jednostką SKU (dysk zarządzany) | Blob Storage | Włączone z opcją "No" jako domyślne |
| Zestaw dostępności z zarządzaną jednostką SKU (dysk zarządzany) | BLOB Storage (opcja Galeria wyłączona) | Wyłączone z opcją "No" jako domyślne |
| Zestaw dostępności (nowo utworzony przez użytkownika) | Galeria | Wyłączone z opcją "tak" jako domyślne |
| Zestaw dostępności (nowo utworzony przez użytkownika) | Blob Storage | Włączone z opcją "No" jako domyślne |
