---
title: Wymuś zasady nazewnictwa grup w Azure Active Directory | Microsoft Docs
description: Jak skonfigurować zasady nazewnictwa dla grup Microsoft 365 w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 458fbdf4cdfa40da871d34a62101020330c79265
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488797"
---
# <a name="enforce-a-naming-policy-on-microsoft-365-groups-in-azure-active-directory"></a>Wymuś zasady nazewnictwa w grupach Microsoft 365 w Azure Active Directory

Aby wymusić spójne konwencje nazewnictwa dla grup Microsoft 365 utworzonych lub edytowanych przez użytkowników, skonfiguruj zasady nazewnictwa grup dla organizacji w Azure Active Directory (Azure AD). Na przykład można użyć zasad nazewnictwa do przekazywania funkcji grupy, członkostwa, regionu geograficznego lub osoby, która utworzyła grupę. Można również użyć zasad nazewnictwa, aby ułatwić kategoryzowanie grup w książce adresowej. Zasad można używać do blokowania określonych słów z używania w nazwach grup i aliasach.

> [!IMPORTANT]
> Zasady nazewnictwa usługi Azure AD dla grup Microsoft 365 wymagają posiadania, ale niekoniecznie przypisywania licencji Azure Active Directory — wersja Premium P1 lub licencji Azure AD — wersja Podstawowa EDU dla każdego unikatowego użytkownika, który jest członkiem co najmniej jednej grupy Microsoft 365.

Zasady nazewnictwa są stosowane do tworzenia lub edytowania grup utworzonych w ramach obciążeń (na przykład Outlook, Microsoft Teams, SharePoint, Exchange lub planista). Jest on stosowany zarówno w przypadku nazwy grupy, jak i aliasu grupy. Jeśli skonfigurujesz zasady nazewnictwa w usłudze Azure AD i masz istniejące zasady nazewnictwa grup programu Exchange, zasady nazewnictwa usługi Azure AD zostaną wymuszone w organizacji.

Gdy zasady nazewnictwa grup są skonfigurowane, zasady zostaną zastosowane do nowych grup Microsoft 365 utworzonych przez użytkowników końcowych. Zasady nazewnictwa nie dotyczą niektórych ról katalogu, takich jak Administrator globalny lub administrator użytkowników (poniżej znajduje się pełna lista ról wykluczonych z zasad nazewnictwa grup). W przypadku istniejących grup Microsoft 365 zasady nie będą od razu stosowane w czasie konfigurowania. Gdy właściciel grupy Edytuje nazwę grupy dla tych grup, zostaną wymuszone zasady nazewnictwa.

## <a name="naming-policy-features"></a>Funkcje zasad nazewnictwa

Zasady nazewnictwa dla grup można wymusić na dwa różne sposoby:

- **Zasady nazewnictwa sufiksu prefiksu** Można zdefiniować prefiksy lub sufiksy, które są następnie automatycznie dodawane do wymuszania konwencji nazewnictwa w grupach (na przykład w nazwie grupy "GRP \_ Japonia \_ My Group \_ Engineering", GRP \_ Japonia \_ jest prefiksem, a \_ Inżynieria jest sufiksem). 

- **Niestandardowe słowa zablokowane** Można przekazać zestaw zablokowanych słów specyficznych dla Twojej organizacji, aby można go było zablokować w grupach tworzonych przez użytkowników (na przykład "dyrektor generalny, płace, HR").

### <a name="prefix-suffix-naming-policy"></a>Zasady nazewnictwa sufiksu prefiksu

Ogólna struktura konwencji nazewnictwa to "prefix [nazwa grupy] sufiks". Chociaż można zdefiniować wiele prefiksów i sufiksów, w ustawieniach można mieć tylko jedno wystąpienie elementu [groupname]. Prefiksy lub sufiksy mogą być stałymi ciągami lub atrybutami użytkownika, takimi jak \[ dział \] , które są zastępowane na podstawie użytkownika, który tworzy grupę. Całkowita dozwolona liczba znaków dla prefiksu i ciągów sufiksu, łącznie z nazwą grupy, to 53 znaków. 

Prefiksy i sufiksy mogą zawierać znaki specjalne, które są obsługiwane w nazwach grup i aliasie grup. Wszystkie znaki prefiksu lub sufiksu, które nie są obsługiwane w aliasie grupy, są nadal stosowane w nazwie grupy, ale usuwane z aliasu grupy. Ze względu na to ograniczenie prefiksy i sufiksy stosowane do nazwy grupy mogą się różnić od tych, które są stosowane do aliasu grupy. 

#### <a name="fixed-strings"></a>Stałe ciągi

Za pomocą ciągów można ułatwić skanowanie i odróżnianie grup na globalnej liście adresów oraz w lewym łączu nawigacyjnym obciążeń grup. Niektóre często występujące prefiksy są słowami kluczowymi, takimi jak "GRP \_ name", " \# name", " \_ name"

#### <a name="user-attributes"></a>Atrybuty użytkownika

Można używać atrybutów, które mogą pomóc użytkownikom, a użytkownicy identyfikują dział, biuro lub region geograficzny, dla którego Grupa została utworzona. Na przykład, jeśli zdefiniujesz zasady nazewnictwa jako `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"` , a `User’s department = Engineering` następnie nazwa grupy wymuszana może być "GRP moją grupę inżynierów". Obsługiwane atrybuty usługi Azure AD to \[ dział \] , \[ firma \] , \[ Office \] , \[ StateOrProvince \] , \[ CountryorRegion \] , \[ title \] . Nieobsługiwane atrybuty użytkownika są traktowane jako ciągi stałe; na przykład " \[ KodPocztowy \] ". Atrybuty rozszerzenia i atrybuty niestandardowe nie są obsługiwane.

Zalecamy używanie atrybutów, które mają wartości wypełnione dla wszystkich użytkowników w organizacji i nie używają atrybutów, które mają długie wartości.

### <a name="custom-blocked-words"></a>Niestandardowe słowa zablokowane

Lista zablokowanych słów jest rozdzielaną przecinkami listą fraz, które mają być blokowane w nazwach grup i aliasach. Nie są wykonywane żadne wyszukiwania podciągów. Aby wyzwolić awarię, wymagane jest dokładne dopasowanie nazwy grupy i jednego lub więcej niestandardowo zablokowanych wyrazów. Nie wykonano wyszukiwania podciągów, aby użytkownicy mogli używać typowych słów takich jak "Class", nawet jeśli "Lass" jest zablokowanym słowem.

Zablokowane reguły listy słów:

- W zablokowanych słowach nie jest rozróżniana wielkość liter.
- Gdy użytkownik wprowadzi zablokowany wyraz jako część nazwy grupy, zobaczy komunikat o błędzie z zablokowanym słowem.
- W zablokowanych słowach nie ma ograniczeń dotyczących znaków.
- Istnieje górny limit 5000 wyrażeń, które można skonfigurować na liście zablokowanych wyrazów. 

### <a name="roles-and-permissions"></a>Role i uprawnienia

Aby skonfigurować zasady nazewnictwa, wymagana jest jedna z następujących ról:
- Administrator globalny
- Administrator grupy


Wybrani Administratorzy mogą być wykluczeni z tych zasad, we wszystkich obciążeniach grup i punktach końcowych, dzięki czemu mogą tworzyć grupy przy użyciu zablokowanych słów i własnych konwencji nazewnictwa. Poniżej znajduje się lista ról administratorów wykluczonych z zasad nazewnictwa grup.

- Administrator globalny
- Obsługa warstwy 1 dla partnerów
- Obsługa warstwy 2 partnera
- Administrator użytkowników
- Autorzy katalogów

## <a name="configure-naming-policy-in-azure-portal"></a>Skonfiguruj zasady nazewnictwa w Azure Portal

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora grupy.
1. Wybierz pozycję **grupy**, a następnie wybierz pozycję **zasady nazewnictwa** , aby otworzyć stronę Zasady nazewnictwa.

    ![Otwórz stronę Zasady nazewnictwa w centrum administracyjnym](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Wyświetl lub Edytuj zasady nazewnictwa sufiksu prefiksu

1. Na stronie **zasady nazewnictwa** wybierz pozycję **zasady nazewnictwa grup**.
1. Można wyświetlić lub edytować bieżące zasady nazewnictwa prefiksu lub sufiksu, wybierając atrybuty lub ciągi, które mają zostać wymuszone w ramach zasad nazewnictwa.
1. Aby usunąć prefiks lub sufiks z listy, wybierz prefiks lub sufiks, a następnie wybierz pozycję **Usuń**. Jednocześnie można usunąć wiele elementów.
1. Zapisz zmiany nowych zasad, aby wejść w życie, wybierając pozycję **Zapisz**.

### <a name="edit-custom-blocked-words"></a>Edytuj niestandardowe słowa zablokowane

1. Na stronie **zasady nazewnictwa** wybierz pozycję **zablokowane słowa**.

    ![Edytuj i przekaż listę zablokowanych wyrazów dla zasad nazewnictwa](./media/groups-naming-policy/blockedwords.png)

1. Wyświetl lub Edytuj bieżącą listę niestandardowych zablokowanych wyrazów, wybierając pozycję **Pobierz**.
1. Przekaż nową listę niestandardowych zablokowanych słów, wybierając ikonę pliku.
1. Zapisz zmiany nowych zasad, aby wejść w życie, wybierając pozycję **Zapisz**.

## <a name="install-powershell-cmdlets"></a>Instalowanie poleceń cmdlet programu PowerShell

Pamiętaj, aby odinstalować starszą wersję modułu Azure Active Directory PowerShell dla programu Graph z programu Windows PowerShell i zainstalować moduł [Azure Active Directory PowerShell dla programu Graph w publicznej wersji zapoznawczej 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) przed uruchomieniem poleceń programu PowerShell.

1. Otwórz aplikację Windows PowerShell jako administrator.
2. Odinstaluj poprzednią wersję programu AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Zainstaluj najnowszą wersję programu AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Jeśli zostanie wyświetlony monit o uzyskanie dostępu do niezaufanego repozytorium, wprowadź **Y**. Zainstalowanie nowego modułu może potrwać kilka minut.

## <a name="configure-naming-policy-in-powershell"></a>Konfigurowanie zasad nazewnictwa w programie PowerShell

1. Otwórz okno programu Windows PowerShell na komputerze. Można go otworzyć bez podwyższonego poziomu uprawnień.

1. Uruchom następujące polecenia, aby przygotować się do uruchomienia poleceń cmdlet.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   Na ekranie **Zaloguj się na swoje konto** wprowadź swoje konto administratora i hasło, aby połączyć się z usługą, a następnie wybierz polecenie **Zaloguj**.

1. Wykonaj kroki opisane w temacie [polecenia cmdlet Azure Active Directory, aby skonfigurować ustawienia grupy](../enterprise-users/groups-settings-cmdlets.md) w celu utworzenia ustawień grupy dla tej organizacji.

### <a name="view-the-current-settings"></a>wyświetlanie bieżących ustawień

1. Pobierz bieżące zasady nazewnictwa, aby wyświetlić bieżące ustawienia.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Wyświetl bieżące ustawienia grupy.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Ustawianie zasad nazewnictwa i zablokowanych słów niestandardowych

1. Ustaw prefiksy i sufiksy nazw grup w usłudze Azure AD PowerShell. Aby ta funkcja działała poprawnie, należy dodać element [GroupName] do ustawienia.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Ustaw niestandardowe słowa zablokowane. W poniższym przykładzie pokazano, jak dodać własne słowa niestandardowe.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Zapisz ustawienia nowych zasad, aby wejść w życie, takich jak w poniższym przykładzie.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
To wszystko. Ustawisz zasady nazewnictwa i dodałeś zablokowane słowa.

## <a name="export-or-import-custom-blocked-words"></a>Eksportowanie lub importowanie niestandardowych słów zablokowanych

Aby uzyskać więcej informacji, zapoznaj się z artykułem [Azure Active Directory polecenia cmdlet służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-cmdlets.md).

Oto przykład skryptu programu PowerShell służącego do eksportowania wielu zablokowanych słów:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Oto przykładowy skrypt programu PowerShell umożliwiający zaimportowanie wielu zablokowanych słów:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Usuń zasady nazewnictwa

### <a name="remove-the-naming-policy-using-azure-portal"></a>Usuń zasady nazewnictwa przy użyciu Azure Portal

1. Na stronie **zasady nazewnictwa** wybierz pozycję **Usuń zasady**.
1. Po potwierdzeniu usunięcia zasady nazewnictwa zostaną usunięte, w tym wszystkie zasady nazewnictwa sufiksów prefiksów i wszelkie niestandardowe niezablokowane słowa.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Usuwanie zasad nazewnictwa przy użyciu programu Azure AD PowerShell

1. Usuń prefiksy i sufiksy nazw grup w usłudze Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Usuń niestandardowe słowa zablokowane.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Zapisz ustawienia.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-microsoft-365-apps"></a>Środowisko pracy w aplikacjach Microsoft 365

Po ustawieniu zasad nazewnictwa grup w usłudze Azure AD, gdy użytkownik tworzy grupę w aplikacji Microsoft 365, zobaczy:

- Wersja zapoznawcza nazwy zgodnie z zasadami nazewnictwa (z prefiksami i sufiksami) zaraz po typach użytkowników w nazwie grupy
- Jeśli użytkownik wprowadzi zablokowane słowa, zobaczy komunikat o błędzie, aby umożliwić usunięcie zablokowanych słów.

Obciążenie | Zgodność
----------- | -------------------------------
Portale Azure Active Directory | W portalu usługi Azure AD i w portalu panelu dostępu wyświetlane są nazwy wymuszone zasad nazewnictwa, gdy użytkownik wpisze nazwę grupy podczas tworzenia lub edytowania grupy. Gdy użytkownik przejdzie do niestandardowego zablokowanego wyrazu, zostanie wyświetlony komunikat o błędzie z zablokowanym słowem, aby użytkownik mógł go usunąć.
Dostęp w sieci Web Outlook (OWA) | Program Outlook Dostęp w sieci Web Wyświetla nazwę wymuszone zasady nazewnictwa, gdy użytkownik wpisze nazwę grupy lub alias grupy. Gdy użytkownik przejdzie do niestandardowego zablokowanego słowa, zostanie wyświetlony komunikat o błędzie w interfejsie użytkownika wraz z zablokowanym słowem, dzięki czemu użytkownik może go usunąć.
Program Outlook Desktop | Grupy utworzone w programie Outlook Desktop są zgodne z ustawieniami zasad nazewnictwa. Aplikacja klasyczna Outlook nie pokazuje jeszcze wersji zapoznawczej wymuszonej nazwy grupy i nie zwraca niestandardowych błędów zablokowanych wyrazów, gdy użytkownik wprowadzi nazwę grupy. Jednak zasady nazewnictwa są automatycznie stosowane podczas tworzenia lub edytowania grupy, a użytkownicy widzą komunikaty o błędach, jeśli w nazwie grupy lub aliasie są używane niestandardowe słowa.
Microsoft Teams | W programie Microsoft Teams są wyświetlane nazwy grup wymuszane przez zasady nazewnictwa, gdy użytkownik wprowadzi nazwę zespołu. Gdy użytkownik przejdzie do niestandardowego zablokowanego wyrazu, zostanie wyświetlony komunikat o błędzie wraz z zablokowanym słowem, aby użytkownik mógł go usunąć.
SharePoint  |  Program SharePoint wyświetla nazwę wymuszone zasady nazewnictwa, gdy użytkownik wpisze nazwę witryny lub adres e-mail grupy. Gdy użytkownik przejdzie do niestandardowego zablokowanego wyrazu, zostanie wyświetlony komunikat o błędzie wraz z zablokowanym słowem, aby użytkownik mógł go usunąć.
Usługa Microsoft Stream | Microsoft Stream pokazuje, że zasady nazewnictwa grup są wymuszane, gdy użytkownik wpisze nazwę grupy lub alias e-mail grupy. Gdy użytkownik przejdzie do niestandardowego zablokowanego wyrazu, zostanie wyświetlony komunikat o błędzie z zablokowanym słowem, aby użytkownik mógł go usunąć.
Aplikacja Outlook dla systemów iOS i Android | Grupy utworzone w aplikacjach Outlook są zgodne ze skonfigurowanymi zasadami nazewnictwa. Aplikacja mobilna Outlook nie pokazuje jeszcze wersji zapoznawczej wymuszonej nazwy zasad nazewnictwa i nie zwraca niestandardowych błędów zablokowanego słowa, gdy użytkownik wprowadzi nazwę grupy. Zasady nazewnictwa są jednak automatycznie stosowane po kliknięciu pozycji Utwórz/Edytuj i użytkownicy zobaczą komunikaty o błędach, jeśli w nazwie grupy lub aliasie są używane niestandardowe słowa.
Aplikacja mobilna grup | Grupy utworzone w aplikacji mobilnej grup są zgodne z zasadami nazewnictwa. Aplikacja mobilna grup nie pokazuje wersji zapoznawczej zasad nazewnictwa i nie zwraca niestandardowych błędów zablokowanego słowa, gdy użytkownik wprowadzi nazwę grupy. Jednak zasady nazewnictwa są automatycznie stosowane podczas tworzenia lub edytowania grupy, a użytkownicy są prezentowane z odpowiednimi błędami, jeśli w nazwie grupy lub aliasie są używane niestandardowe słowa.
Planner | Planista jest zgodny z zasadami nazewnictwa. Podczas wprowadzania nazwy planu planista wyświetla podgląd zasad nazewnictwa. Gdy użytkownik przejdzie do niestandardowego zablokowanego wyrazu, podczas tworzenia planu zostanie wyświetlony komunikat o błędzie.
Dynamics 365 for Customer Engagement | Dynamics 365 dla zaangażowania klienta jest zgodne z zasadami nazewnictwa. Dynamics 365 wyświetla nazwę wymuszone zasady nazewnictwa, gdy użytkownik wpisze nazwę grupy lub alias e-mail grupy. Gdy użytkownik wprowadzi niestandardowe niezasłonięte słowo, zostanie wyświetlony komunikat o błędzie z zablokowanym słowem, aby użytkownik mógł go usunąć.
Synchronizacja danych szkolnych | Grupy utworzone za pomocą tej operacji są zgodne z zasadami nazewnictwa, ale zasady nazewnictwa nie są stosowane automatycznie. Administratorzy usługi muszą dołączać prefiksy i sufiksy do nazw klas, dla których należy utworzyć grupy, a następnie przekazać je do usługi przesyłania na stronie. W przeciwnym razie utworzenie grupy lub Edycja będzie kończyć się niepowodzeniem.
Aplikacja klasowa | Grupy utworzone w aplikacji klasy są zgodne z zasadami nazewnictwa, ale zasady nazewnictwa nie są stosowane automatycznie, a użytkownicy nie są pokazani podczas wprowadzania nazwy grupy klas przy użyciu wersji zapoznawczej zasad nazewnictwa. Użytkownicy muszą wprowadzić nazwę grupy klas wymuszonych z prefiksami i sufiksami. W przeciwnym razie operacja tworzenia lub edytowania grupy klas kończy się niepowodzeniem z błędami.
Power BI | Obszary robocze Power BI są zgodne z zasadami nazewnictwa.    
Yammer | Gdy użytkownik zalogowany do usługi Yammer przy użyciu konta Azure Active Directory tworzy grupę lub edytuje nazwę grupy, nazwa grupy będzie zgodna z zasadami nazewnictwa. Dotyczy to zarówno Microsoft 365 połączonych grup, jak i wszystkich innych grup usługi Yammer.<br>Jeśli Microsoft 365 podłączona Grupa została utworzona przed wprowadzeniem zasad nazewnictwa, nazwa grupy nie będzie automatycznie podążać za zasadami nazewnictwa. Gdy użytkownik edytuje nazwę grupy, zostanie wyświetlony monit o dodanie prefiksu i sufiksu.
Usłudze staffhub  | Zespoły usłudze staffhub nie są zgodne z zasadami nazewnictwa, ale podstawową grupą Microsoft 365. Nazwa zespołu usłudze staffhub nie stosuje prefiksów ani sufiksów i nie sprawdza niestandardowych zablokowanych wyrazów. Ale usłudze staffhub stosuje prefiksy i sufiksy i usuwa zablokowane słowa z podstawowej grupy Microsoft 365.
Program Exchange PowerShell | Polecenia cmdlet programu Exchange PowerShell są zgodne z zasadami nazewnictwa. Użytkownicy otrzymują odpowiednie komunikaty o błędach z sugerowanymi prefiksami i sufiksami oraz dla niestandardowych zablokowanych słów, jeśli nie są zgodne z zasadami nazewnictwa w Nazwa grupy i alias grupy (mailNickname).
Azure Active Directory polecenia cmdlet programu PowerShell | Polecenia cmdlet programu PowerShell Azure Active Directory są zgodne z zasadami nazewnictwa. Użytkownicy otrzymują odpowiednie komunikaty o błędach z sugerowanymi prefiksami i sufiksami oraz dla niestandardowych zablokowanych słów, jeśli nie są zgodne z konwencją nazewnictwa w nazwach grup i aliasie grup.
Centrum administracyjne programu Exchange | Centrum administracyjne programu Exchange jest zgodne z zasadami nazewnictwa. Użytkownicy otrzymują odpowiednie komunikaty o błędach z sugerowanymi prefiksami i sufiksami oraz dla niestandardowych zablokowanych słów, jeśli nie są zgodne z konwencją nazewnictwa w aliasie nazw grup i grup.
Centrum administracyjne platformy Microsoft 365 | Centrum administracyjne Microsoft 365 jest zgodne z zasadami nazewnictwa. Gdy użytkownik tworzy lub edytuje nazwy grup, zasady nazewnictwa są automatycznie stosowane i użytkownicy otrzymują odpowiednie błędy po wprowadzeniu niestandardowych zablokowanych wyrazów. Centrum administracyjne Microsoft 365 nie wyświetla jeszcze wersji zapoznawczej zasad nazewnictwa i nie zwraca niestandardowych błędów zablokowanego słowa, gdy użytkownik wprowadzi nazwę grupy.

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje dotyczące grup usługi Azure AD.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zasady wygasania dla grup Microsoft 365](groups-lifecycle.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkami grupy](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
