---
title: Przejęcie przez administratora katalogu nieza zarządzania — Azure AD | Microsoft Docs
description: Jak przejąć nazwę domeny DNS w nieza zarządzanie organizacji usługi Azure AD (dzierżawie w tle).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f4645626675ae19a462ac8707e995c3b4045e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739373"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Przejmowanie katalogu niezarządzanego jako administrator w usłudze Azure Active Directory

W tym artykule opisano dwa sposoby na przejąnie nazwy domeny DNS w katalogu nieza zarządzaniem w usłudze Azure Active Directory (Azure AD). Gdy użytkownik samoobsługi rejestruje się w usłudze w chmurze, która korzysta z usługi Azure AD, jest dodawany do niezarządzanego katalogu usługi Azure AD na podstawie swojej domeny poczty e-mail. Aby uzyskać więcej informacji na temat samoobsługowej lub "wirusowej" rejestracji w usłudze, zobacz Co to jest samoobsługowa rejestracja w [usłudze Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Zdecyduj, jak chcesz przejąć katalog niezaiemowiony
Podczas procesu przejęcia przez administratora możesz udowodnić własność w sposób opisany w artykule [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md). W kolejnych sekcjach objaśniono środowisko pracy administratora bardziej szczegółowo, ale w tym miejscu znajduje się podsumowanie:

* W przypadku wykonania [„wewnętrznego” przejęcia przez administratora](#internal-admin-takeover) niezarządzanego katalogu platformy Azure użytkownik jest dodawany jako administrator globalny katalogu niezarządzanego. Użytkownicy, domeny ani plany usług nie są migrowane do żadnych innych administrowanych przez Ciebie katalogów.

* W przypadku wykonania [„zewnętrznego” przejęcia przez administratora](#external-admin-takeover) niezarządzanego katalogu platformy Azure dodajesz nazwę domeny DNS katalogu niezarządzanego do swojego zarządzanego katalogu platformy Azure. W przypadku dodania nazwy domeny mapowanie użytkowników do zasobów jest tworzone w Twoim zarządzanym katalogu usługi Azure, dzięki czemu użytkownicy mogą nadal bez przeszkód uzyskiwać dostęp do usług. 

## <a name="internal-admin-takeover"></a>Przejęcie przez administratora wewnętrznego

Niektóre produkty, takie jak SharePoint i OneDrive, takie jak Microsoft 365, nie obsługują przejęcia zewnętrznego. Jeśli tak jest w Twoim scenariuszu lub jeśli jesteś administratorem i chcesz przejąć organizację usługi Azure AD niezadawaną lub niezamkową przez użytkowników, którzy korzystali z rejestracji samoobsługowej, możesz to zrobić za pomocą wewnętrznego przejęcia przez administratora.

1. Utwórz kontekst użytkownika w organizacji niezaimażowej, tworząc konto w Power BI. Dla wygody przykładu w tych krokach założono, że ta ścieżka.

2. Otwórz [witrynę Power BI i](https://powerbi.com) wybierz pozycję **Rozpocznij bezpłatnie.** Wprowadź konto użytkownika, które używa nazwy domeny dla organizacji; na przykład `admin@fourthcoffee.xyz` . Po wprowadzeniu kodu weryfikacyjnego sprawdź w wiadomości e-mail kod potwierdzenia.

3. W wiadomości e-mail z Power BI wybierz pozycję **Tak, to ja.**

4. Zaloguj się do [centrum administracyjne platformy Microsoft 365](https://portal.office.com/admintakeover) przy użyciu Power BI konta użytkownika. Zostanie wyświetlony komunikat z powiadomieniem  o tym, że zostaniesz administratorem nazwy domeny, która została już zweryfikowana w organizacji niezaimażowej. Wybierz **pozycję Tak, chcę być administratorem**.
  
   ![pierwszy zrzut ekranu przedstawiający pozycję Zostań administratorem](./media/domains-admin-takeover/become-admin-first.png)
  
5. Dodaj rekord TXT, aby udowodnić, że jesteś właścicielem nazwy **domeny fourthcoffee.xyz** u rejestratora nazw domen. W tym przykładzie jest to GoDaddy.com.
  
   ![Dodawanie rekordu txt dla nazwy domeny](./media/domains-admin-takeover/become-admin-txt-record.png)

Po zweryfikowaniu rekordów TXT systemu DNS u rejestratora nazw domen możesz zarządzać organizacją usługi Azure AD.

Po ukończeniu powyższych kroków jesteś teraz administratorem globalnym organizacji Czwartej kawy w Microsoft 365. Aby zintegrować nazwę domeny z innymi usługami platformy Azure, możesz usunąć ją z usługi Microsoft 365 dodać do innej organizacji zarządzanej na platformie Azure.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Dodawanie nazwy domeny do organizacji zarządzanej w usłudze Azure AD

1. Otwórz [centrum administracyjne platformy Microsoft 365](https://admin.microsoft.com).
2. Wybierz **kartę** Użytkownicy i utwórz nowe konto użytkownika o nazwie, na przykład *\@ fourthcoffeexyz.onmicrosoft.com,* która nie używa niestandardowej nazwy domeny. 
3. Upewnij się, że nowe konto użytkownika ma uprawnienia administratora globalnego dla organizacji usługi Azure AD.
4. Otwórz **kartę** Domeny w centrum administracyjne platformy Microsoft 365, wybierz nazwę domeny i wybierz pozycję **Usuń**. 
  
   ![Usuń nazwę domeny z Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Jeśli masz użytkowników lub grupy w Microsoft 365 odwołujące się do usuniętej nazwy domeny, należy zmienić ich nazwę na .onmicrosoft.com domeny. Jeśli wymusisz usunięcie nazwy domeny, wszyscy użytkownicy zostaną automatycznie zmienioni, w tym przykładzie na nazwę *\@ fourthcoffeexyz.onmicrosoft.com*.
  
6. Zaloguj się do centrum [administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, które jest administratorem globalnym organizacji usługi Azure AD.
  
7. Wybierz **pozycję Niestandardowe nazwy domen,** a następnie dodaj nazwę domeny. Musisz wprowadzić rekordy TXT systemu DNS, aby zweryfikować własność nazwy domeny. 
  
   ![domena zweryfikowana jako dodana do usługi Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Każdy użytkownik usługi Power BI lub azure Rights Management, który ma przypisane licencje w organizacji Microsoft 365, musi zapisać swoje pulpity nawigacyjne, jeśli nazwa domeny zostanie usunięta. Muszą oni zalogować się przy użyciu nazwy użytkownika, na *\@ przykład nazwy fourthcoffeexyz.onmicrosoft.com,* a nie *nazwy \@ fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Przejęcie przez administratora zewnętrznego

Jeśli zarządzasz już organizacją przy użyciu usług platformy Azure lub usługi Microsoft 365, nie możesz dodać niestandardowej nazwy domeny, jeśli została ona już zweryfikowana w innej organizacji usługi Azure AD. Jednak z zarządzanej organizacji w usłudze Azure AD możesz przejąć organizację nieza zarządzaną jako zewnętrzne przejęcie przez administratora. Ogólna procedura jest zgodna z [artykułem Dodawanie domeny niestandardowej do usługi Azure AD.](../fundamentals/add-custom-domain.md)

Po zweryfikowaniu własności nazwy domeny usługa Azure AD usuwa nazwę domeny z organizacji niezaimażowej i przenosi ją do istniejącej organizacji. Przejęcie przez administratora zewnętrznego katalogu nieza zarządzanie wymaga tego samego procesu weryfikacji DNS TXT jako wewnętrznego przejęcia przez administratora. Różnica polega na tym, że następujące elementy są również przenoszone z nazwą domeny:

- Użytkownicy
- Subskrypcje
- Przypisania licencji

### <a name="support-for-external-admin-takeover"></a>Obsługa przejęcia przez administratora zewnętrznego
Przejęcie przez administratora zewnętrznego jest obsługiwane przez następujące Usługi online:

- Azure Rights Management
- Exchange Online

Obsługiwane plany usług obejmują:

- PowerApps Za darmo
- PowerFlow Free
- Usługi RMS dla użytkowników indywidualnych
- Usługa Microsoft Stream
- Dynamics 365 w bezpłatnej wersji próbnej

Przejęcie przez administratora zewnętrznego nie jest obsługiwane w przypadku żadnej usługi, która ma plany usług, takie jak SharePoint, OneDrive lub Skype dla firm; na przykład za pośrednictwem bezpłatnej subskrypcji pakietu Office. 

Opcjonalnie możesz użyć opcji [ **ForceTakeover,**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) aby usunąć nazwę domeny z organizacji niezaimażowej i zweryfikować ją w żądanej organizacji. 

#### <a name="more-information-about-rms-for-individuals"></a>Więcej informacji na temat usługi RMS dla użytkowników indywidualnych

W przypadku usługi [RMS](/azure/information-protection/rms-for-individuals)dla użytkowników indywidualnych, gdy organizacja niezamażona znajduje się w tym samym [](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) regionie co twoja organizacja, automatycznie utworzony klucz organizacji usługi [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) i domyślne szablony ochrony są dodatkowo przenoszone wraz z nazwą domeny.

Klucz i szablony nie są przenoszone, gdy organizacja niezaiemowiona znajduje się w innym regionie. Jeśli na przykład organizacja niezamażona znajduje się w Europie, a twoja organizacja znajduje się w Ameryka Północna.

Mimo że usługa RMS dla użytkowników indywidualnych jest przeznaczona do obsługi uwierzytelniania usługi Azure AD w celu otwierania chronionej zawartości, nie uniemożliwia to użytkownikom również ochrony zawartości. Jeśli użytkownicy chronili zawartość za pomocą subskrypcji usługi RMS dla użytkowników indywidualnych, a klucz i szablony nie zostały przeniesione, ta zawartość nie będzie dostępna po przejęcie domeny.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Polecenia cmdlet programu PowerShell usługi Azure AD dla opcji ForceTakeover
Te polecenia cmdlet są używane w przykładzie [programu PowerShell](#powershell-example).

cmdlet | Użycie
------- | -------
`connect-msolservice` | Po wyświetleniu monitu zaloguj się do swojej organizacji zarządzanej.
`get-msoldomain` | Wyświetla nazwy domen skojarzone z bieżącą organizacją.
`new-msoldomain –name <domainname>` | Dodaje nazwę domeny do organizacji jako Niezweryfikowana (weryfikacja DNS nie została jeszcze wykonana).
`get-msoldomain` | Nazwa domeny jest teraz uwzględniona na liście nazw domen skojarzonych z zarządzaną organizacją, ale jest wymieniona jako **Niezweryfikowana.**
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Zawiera informacje, które należy umieścić w nowym rekordzie TXT systemu DNS dla domeny (MS = xxxxx). Weryfikacja może nie nastąpić natychmiast, ponieważ propagacja rekordu TXT zajmuje trochę czasu, więc zaczekaj kilka minut, zanim rozważysz **opcję -ForceTakeover.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Jeśli nazwa domeny nadal nie została zweryfikowana, możesz przejść do **opcji -ForceTakeover.** Sprawdza, czy rekord TXT został utworzony i rozpoczyna proces przejęcia.<li>Opcja **-ForceTakeover** powinna zostać dodana do polecenia cmdlet tylko wtedy, gdy wymuszanie przejęcia przez administratora zewnętrznego, na przykład w przypadku, gdy organizacja nieza zarządzania Microsoft 365 blokuje przejęcie.
`get-msoldomain` | Na liście domen nazwa domeny jest teraz wyświetlona jako **Zweryfikowano**.

> [!NOTE]
> Nieza zarządzania organizacja usługi Azure AD zostanie usunięta 10 dni po skorzystaniu z opcji wymuszania przejęcia zewnętrznego.

### <a name="powershell-example"></a>Przykład programu PowerShell

1. Połącz się z usługą Azure AD przy użyciu poświadczeń użytych do odpowiedzi na ofertę samoobsługową:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Pobierz listę domen:
  
   ```powershell
   Get-MsolDomain
   ```
3. Uruchom Get-MsolDomainVerificationDns cmdlet , aby utworzyć wyzwanie:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Na przykład:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Skopiuj wartość (zadanie) zwróconą z tego polecenia. Na przykład:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. W publicznej przestrzeni nazw DNS utwórz rekord txt dns zawierający wartość skopiowaną w poprzednim kroku. Nazwa tego rekordu to nazwa domeny nadrzędnej, więc jeśli utworzysz ten rekord zasobu przy użyciu roli DNS z systemu Windows Server, pozostaw pole Nazwa rekordu puste i po prostu wklej wartość w polu Tekst.
6. Uruchom polecenie cmdlet Confirm-MsolDomain, aby zweryfikować wyzwanie:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Na przykład:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Pomyślne wyzwanie umożliwia powrót do wiersza polecenia bez błędu.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie nazwy domeny niestandardowej do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
