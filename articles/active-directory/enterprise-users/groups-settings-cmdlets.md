---
title: Konfigurowanie ustawień grupy przy użyciu programu PowerShell — Azure AD | Microsoft Docs
description: Jak zarządzać ustawieniami grup za pomocą poleceń cmdlet Azure Active Directory
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
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6aa4e112d182eb41c3d0c262289a2d3263c17ef0
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488577"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy

Ten artykuł zawiera instrukcje dotyczące używania poleceń cmdlet programu PowerShell w usłudze Azure Active Directory (Azure AD) do tworzenia i aktualizowania grup. Ta zawartość dotyczy tylko grup Microsoft 365 (nazywanych czasami grupami ujednoliconymi).

> [!IMPORTANT]
> Niektóre ustawienia wymagają licencji na Azure Active Directory — wersja Premium P1. Aby uzyskać więcej informacji, zobacz tabela [ustawień szablonu](#template-settings) .

Aby uzyskać więcej informacji na temat zapobiegania tworzeniu grup zabezpieczeń przez użytkowników niebędących administratorami, ustaw `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` zgodnie z opisem w [Set-MSOLCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

Ustawienia grup Microsoft 365 są konfigurowane przy użyciu obiektu Settings i obiektu SettingsTemplate. Początkowo nie widzisz żadnych obiektów Settings w katalogu, ponieważ katalog jest skonfigurowany z ustawieniami domyślnymi. Aby zmienić ustawienia domyślne, należy utworzyć nowy obiekt ustawień przy użyciu szablonu ustawień. Szablony ustawień są definiowane przez firmę Microsoft. Istnieje kilka różnych szablonów ustawień. Aby skonfigurować ustawienia grupy Microsoft 365 dla katalogu, należy użyć szablonu o nazwie "Group. Unified". Aby skonfigurować ustawienia grupy Microsoft 365 dla pojedynczej grupy, użyj szablonu o nazwie "Group. Unified. Guest". Ten szablon służy do zarządzania dostępem gościa do grupy Microsoft 365. 

Polecenia cmdlet są częścią modułu Azure Active Directory PowerShell V2. Aby uzyskać instrukcje dotyczące pobierania i instalowania modułu na komputerze, zapoznaj się z artykułem [Azure Active Directory programu PowerShell w wersji 2](/powershell/azure/active-directory/overview). Wersję 2 modułu można zainstalować z [Galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Instalowanie poleceń cmdlet programu PowerShell

Pamiętaj, aby odinstalować wszystkie starsze wersje modułu Azure Active Directory PowerShell for Graph dla środowiska Windows PowerShell i zainstalować program [Azure Active Directory PowerShell dla programu Graph — publiczna wersja zapoznawcza (nowsza niż 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview) przed uruchomieniem poleceń programu PowerShell.

1. Otwórz aplikację Windows PowerShell jako administrator.
2. Odinstaluj poprzednią wersję programu AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Zainstaluj najnowszą wersję programu AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Tworzenie ustawień na poziomie katalogu
Te kroki umożliwiają utworzenie ustawień na poziomie katalogu, które mają zastosowanie do wszystkich grup Microsoft 365 w katalogu. Get-AzureADDirectorySettingTemplate polecenie cmdlet jest dostępne tylko w [module programu Azure AD PowerShell w wersji zapoznawczej dla programu Graph](https://www.powershellgallery.com/packages/AzureADPreview).

1. W poleceniach cmdlet obiektów directorysettings należy określić identyfikator SettingsTemplate, który ma być używany. Jeśli nie znasz tego identyfikatora, to polecenie cmdlet zwróci listę wszystkich szablonów ustawień:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   To wywołanie polecenia cmdlet zwraca wszystkie dostępne szablony:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Microsoft 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Aby dodać adres URL wytycznych użytkowania, najpierw należy uzyskać obiekt SettingsTemplate, który definiuje wartość adresu URL dla wskazówki dotyczące użycia; oznacza to, że szablon grupy. ujednolicony:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Następnie utwórz nowy obiekt ustawień na podstawie tego szablonu:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Następnie zaktualizuj wartość wskazówki dotyczące użycia:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Następnie Zastosuj ustawienie:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Można odczytać wartości przy użyciu:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Aktualizowanie ustawień na poziomie katalogu
Aby zaktualizować wartość dla UsageGuideLinesUrl w szablonie ustawienia, zapoznaj się z bieżącymi ustawieniami z usługi Azure AD. w przeciwnym razie możemy zakończyć zastąpienie istniejących ustawień innych niż UsageGuideLinesUrl.

1. Pobierz bieżące ustawienia z grupy. ujednolicony SettingsTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Sprawdź bieżące ustawienia:
   
   ```powershell
   $Setting.Values
   ```
   
   Dane wyjściowe:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. Aby usunąć wartość UsageGuideLinesUrl, Edytuj adres URL jako pusty ciąg:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Zapisz aktualizację do katalogu:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Ustawienia szablonu
Oto ustawienia zdefiniowane w grupie. ujednolicone SettingsTemplate. O ile nie wskazano inaczej, te funkcje wymagają licencji na Azure Active Directory — wersja Premium P1. 

| **Ustawienie** | **Opis** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Typ: wartość logiczna<li>Wartość domyślna: prawda |Flaga oznaczająca, czy w katalogu nie są dozwolone tworzenie grup Microsoft 365. To ustawienie nie wymaga licencji na Azure Active Directory — wersja Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Typ: ciąg<li>Wartość domyślna: "" |Identyfikator GUID grupy zabezpieczeń, do której członkowie mogą tworzyć grupy Microsoft 365, nawet gdy EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Typ: ciąg<li>Wartość domyślna: "" |Łącze do wytycznych dotyczących użycia grup. |
|  <ul><li>ClassificationDescriptions<li>Typ: ciąg<li>Wartość domyślna: "" | Rozdzielana przecinkami lista opisów klasyfikacji. Wartość ClassificationDescriptions jest prawidłowa tylko w tym formacie:<br>$setting ["ClassificationDescriptions"] = "Klasyfikacja: Opis, klasyfikacja: Opis"<br>gdzie Klasyfikacja pasuje do wpisu w ClassificationList.<br>To ustawienie nie ma zastosowania, gdy EnableMIPLabels = = true.|
|  <ul><li>DefaultClassification<li>Typ: ciąg<li>Wartość domyślna: "" | Klasyfikacja, która ma być używana jako Klasyfikacja domyślna dla grupy, jeśli żadna nie została określona.<br>To ustawienie nie ma zastosowania, gdy EnableMIPLabels = = true.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Typ: ciąg<li>Wartość domyślna: "" | Ciąg o maksymalnej długości 64 znaków, który definiuje konwencję nazewnictwa skonfigurowaną dla grup Microsoft 365. Aby uzyskać więcej informacji, zobacz [wymuszanie zasad nazewnictwa dla grup Microsoft 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Typ: ciąg<li>Wartość domyślna: "" | Ciąg wyrażeń rozdzielonych przecinkami, których użytkownicy nie będą mogli używać w nazwach grup lub aliasach. Aby uzyskać więcej informacji, zobacz [wymuszanie zasad nazewnictwa dla grup Microsoft 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: wartość logiczna<li>Wartość domyślna: "false" | Nie używaj
|  <ul><li>AllowGuestsToBeGroupOwner<li>Typ: wartość logiczna<li>Wartość domyślna: FAŁSZ | Wartość logiczna wskazująca, czy użytkownik-Gość może być właścicielem grup. |
|  <ul><li>AllowGuestsToAccessGroups<li>Typ: wartość logiczna<li>Wartość domyślna: prawda | Wartość logiczna wskazująca, czy użytkownik-Gość może mieć dostęp do zawartości grup Microsoft 365.  To ustawienie nie wymaga licencji na Azure Active Directory — wersja Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Typ: ciąg<li>Wartość domyślna: "" | Adres URL linku do wskazówek dotyczących użycia gościa. |
|  <ul><li>AllowToAddGuests<li>Typ: wartość logiczna<li>Wartość domyślna: prawda | Wartość logiczna wskazująca, czy można dodawać Gości do tego katalogu. <br>To ustawienie może być zastąpione i stać się tylko do odczytu, jeśli *EnableMIPLabels* ma *wartość true* , a zasady gościa są skojarzone z etykietą czułości przypisaną do grupy.<br>Jeśli ustawienie AllowToAddGuests ma wartość FAŁSZ na poziomie organizacji, wszystkie ustawienia AllowToAddGuests na poziomie grupy zostaną zignorowane. Jeśli chcesz włączyć dostęp gościa tylko dla kilku grup, musisz ustawić AllowToAddGuests na wartość true na poziomie organizacji, a następnie selektywnie wyłączyć go dla określonych grup. |
|  <ul><li>ClassificationList<li>Typ: ciąg<li>Wartość domyślna: "" | Rozdzielana przecinkami lista prawidłowych wartości klasyfikacji, które mogą być stosowane do grup Microsoft 365. <br>To ustawienie nie ma zastosowania, gdy EnableMIPLabels = = true.|
|  <ul><li>EnableMIPLabels<li>Typ: wartość logiczna<li>Wartość domyślna: "false" |Flaga oznaczająca, czy etykiety czułości publikowane w centrum zgodności Microsoft 365 mogą być stosowane do Microsoft 365 grup. Aby uzyskać więcej informacji, zobacz [przypisywanie etykiet czułości dla grup Microsoft 365](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Przykład: Konfigurowanie zasad gościa dla grup na poziomie katalogu
1. Pobierz wszystkie szablony ustawień:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Aby ustawić zasady gościa dla grup na poziomie katalogu, należy zgrupować. ujednolicony szablon
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Następnie utwórz nowy obiekt ustawień na podstawie tego szablonu:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Następnie zaktualizuj ustawienie AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Następnie Zastosuj ustawienie:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Można odczytać wartości przy użyciu:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Odczytaj ustawienia na poziomie katalogu

Jeśli znasz nazwę ustawienia, które chcesz pobrać, możesz użyć poniższego polecenia cmdlet, aby pobrać bieżące wartości ustawień. W tym przykładzie pobieramy wartość ustawienia o nazwie "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Te kroki odczytują ustawienia na poziomie katalogu, które mają zastosowanie do wszystkich grup pakietu Office w katalogu.

1. Odczytaj wszystkie istniejące ustawienia katalogu:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   To polecenie cmdlet zwraca listę wszystkich ustawień katalogu:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Odczytaj wszystkie ustawienia dla określonej grupy:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Odczytywanie wszystkich wartości ustawień katalogu dla określonego obiektu ustawień katalogu, przy użyciu identyfikatora GUID ID ustawień:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   To polecenie cmdlet zwraca nazwy i wartości w tym obiekcie ustawień dla tej konkretnej grupy:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Usuń ustawienia na poziomie katalogu
Ten krok powoduje usunięcie ustawień na poziomie katalogu, które mają zastosowanie do wszystkich grup pakietu Office w katalogu.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Tworzenie ustawień dla określonej grupy

1. Wyszukaj szablon ustawień o nazwie "groups. Unified. Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Microsoft 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Pobierz obiekt szablonu dla szablonu groups. Unified. gościa:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Utwórz nowy obiekt ustawień na podstawie szablonu:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Ustaw wymaganą wartość ustawienia:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Pobierz identyfikator grupy, do której chcesz zastosować to ustawienie:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Utwórz nowe ustawienie dla wymaganej grupy w katalogu:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Aby sprawdzić ustawienia, uruchom następujące polecenie:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Aktualizowanie ustawień dla określonej grupy
1. Pobierz identyfikator grupy, której ustawienie chcesz zaktualizować:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Pobierz ustawienie grupy:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Zaktualizuj ustawienie grupy w miarę potrzeb, np.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Następnie Uzyskaj identyfikator ustawienia dla tej konkretnej grupy:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Otrzymasz odpowiedź podobną do tej:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Następnie można ustawić nową wartość dla tego ustawienia:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Można odczytać wartość ustawienia, aby upewnić się, że zostało prawidłowo zaktualizowane:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Dokumentacja składni poleceń cmdlet
Więcej Azure Active Directory dokumentacji programu PowerShell można znaleźć w [Azure Active Directory poleceniach cmdlet](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Materiały uzupełniające

* [Zarządzanie dostępem do zasobów za pomocą grup Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)