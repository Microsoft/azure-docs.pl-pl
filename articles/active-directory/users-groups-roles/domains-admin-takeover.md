---
title: Przejęcie przez administratora niezarządzanego katalogu — Azure AD | Microsoft Docs
description: Jak przejąć nazwę domeny DNS w niezarządzanej organizacji usługi Azure AD (dzierżawa w tle).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: how-to
ms.workload: identity
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6a7dcb1d24f3c1ff848e3393687b04d79d28058
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054708"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Przejmowanie katalogu niezarządzanego jako administrator w usłudze Azure Active Directory

W tym artykule opisano dwa sposoby przejęcia nazwy domeny DNS w niezarządzanym katalogu w Azure Active Directory (Azure AD). Gdy użytkownik samoobsługi rejestruje się w usłudze w chmurze, która korzysta z usługi Azure AD, jest dodawany do niezarządzanego katalogu usługi Azure AD na podstawie swojej domeny poczty e-mail. Aby uzyskać więcej informacji na temat rejestracji samoobsługowej lub "wirusa" dla usługi, zobacz [co to jest rejestracja samoobsługowa w celu Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Zdecyduj, jak chcesz przejąć niezarządzany katalog
Podczas procesu przejęcia przez administratora możesz udowodnić własność w sposób opisany w artykule [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md). W kolejnych sekcjach objaśniono środowisko pracy administratora bardziej szczegółowo, ale w tym miejscu znajduje się podsumowanie:

* Podczas przeprowadzania ["wewnętrznego" przejęcia przez administratora](#internal-admin-takeover) niezarządzanego katalogu platformy Azure zostanie on dodany jako Administrator globalny katalogu niezarządzanego. Użytkownicy, domeny ani plany usług nie są migrowane do żadnych innych administrowanych przez Ciebie katalogów.

* W przypadku wykonywania ["zewnętrznej" przejęcia przez administratora](#external-admin-takeover) niezarządzanego katalogu platformy Azure należy dodać nazwę domeny DNS niezarządzanego katalogu do zarządzanego katalogu platformy Azure. W przypadku dodania nazwy domeny mapowanie użytkowników do zasobów jest tworzone w Twoim zarządzanym katalogu usługi Azure, dzięki czemu użytkownicy mogą nadal bez przeszkód uzyskiwać dostęp do usług. 

## <a name="internal-admin-takeover"></a>Przejęcie przez administratora wewnętrznego

Niektóre produkty, które obejmują programy SharePoint i OneDrive, takie jak Microsoft 365, nie obsługują przejęcia zewnętrznego. Jeśli jesteś Twoim scenariuszem lub jesteś administratorem i chcesz przejąć organizację usługi Azure AD niezarządzaną lub "w tle" dla użytkowników korzystających z rejestracji samoobsługowej, możesz to zrobić przy użyciu wewnętrznego przejęcia administratora.

1. Utwórz kontekst użytkownika w organizacji niezarządzanej za pomocą rejestracji w usłudze Power BI. W przypadku wygody przykładu te kroki zakładają tę ścieżkę.

2. Otwórz [witrynę Power BI](https://powerbi.com) i wybierz pozycję **Rozpocznij bezpłatnie**. Wprowadź konto użytkownika, które używa nazwy domeny dla organizacji; na przykład `admin@fourthcoffee.xyz` . Po wprowadzeniu kodu weryfikacyjnego Sprawdź swój adres e-mail, aby uzyskać kod potwierdzający.

3. W wiadomości e-mail z potwierdzeniem z Power BI wybierz pozycję **tak**.

4. Zaloguj się do [Centrum administracyjnego Microsoft 365](https://portal.office.com/admintakeover) przy użyciu konta użytkownika Power BI. Zostanie wyświetlony komunikat z instrukcjami, aby zostać **administratorem** nazwy domeny, która została już zweryfikowana w niezarządzanej organizacji. Wybierz pozycję **tak, chcę być administratorem**.
  
   ![pierwszy zrzut ekranu, który ma być administratorem](./media/domains-admin-takeover/become-admin-first.png)
  
5. Dodaj rekord TXT, aby udowodnić, że jesteś posiadaczem nazwy domeny **fourthcoffee. xyz** w rejestratorze nazw domen. W tym przykładzie jest to GoDaddy.com.
  
   ![Dodawanie rekordu TXT dla nazwy domeny](./media/domains-admin-takeover/become-admin-txt-record.png)

Gdy rekordy TXT DNS są weryfikowane na rejestratorze nazw domen, można zarządzać organizacją usługi Azure AD.

Po wykonaniu powyższych kroków jesteś teraz administratorem globalnym z czwartej organizacji kawowej w Microsoft 365. Aby zintegrować nazwę domeny z innymi usługami platformy Azure, możesz usunąć ją z Microsoft 365 i dodać do innej organizacji zarządzanej na platformie Azure.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Dodawanie nazwy domeny do zarządzanej organizacji w usłudze Azure AD

1. Otwórz [Centrum administracyjne Microsoft 365](https://admin.microsoft.com).
2. Wybierz kartę **Użytkownicy** , a następnie utwórz nowe konto użytkownika o nazwie * \@ fourthcoffeexyz.onmicrosoft.com użytkownika* , która nie używa niestandardowej nazwy domeny. 
3. Upewnij się, że nowe konto użytkownika ma uprawnienia administratora globalnego dla organizacji usługi Azure AD.
4. Otwórz kartę **domeny** w centrum administracyjnym Microsoft 365, wybierz nazwę domeny i wybierz pozycję **Usuń**. 
  
   ![Usuń nazwę domeny z Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Jeśli w Microsoft 365 znajdują się wszyscy użytkownicy lub grupy, które odwołują się do usuniętej nazwy domeny, należy zmienić ich nazwy na domenę. onmicrosoft.com. Jeśli wymusisz usunięcie nazwy domeny, wszyscy użytkownicy będą automatycznie zmieniać nazwy, w tym przykładzie *do \@ fourthcoffeexyz.onmicrosoft.com użytkownika*.
  
6. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, które jest administratorem globalnym dla organizacji usługi Azure AD.
  
7. Wybierz opcję **niestandardowe nazwy domen**, a następnie Dodaj nazwę domeny. Musisz wprowadzić rekordy TXT DNS, aby zweryfikować własność nazwy domeny. 
  
   ![domena została zweryfikowana jako dodana do usługi Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Wszyscy użytkownicy Power BI lub usługi Azure Rights Management, którzy mają przypisane licencje w organizacji Microsoft 365, muszą zapisać swoje pulpity nawigacyjne, jeśli nazwa domeny zostanie usunięta. Muszą oni zalogować się przy użyciu nazwy użytkownika, takiej jak *user \@ fourthcoffeexyz.onmicrosoft.com* zamiast *User \@ fourthcoffee. xyz*.

## <a name="external-admin-takeover"></a>Przejęcie przez administratora zewnętrznego

Jeśli zarządzasz już organizacją przy użyciu usług platformy Azure lub Microsoft 365, nie możesz dodać niestandardowej nazwy domeny, jeśli została ona już zweryfikowana w innej organizacji usługi Azure AD. Jednak z organizacji zarządzanej w usłudze Azure AD można przejąć niezarządzaną organizację jako przejęcie zewnętrzną przez administratora. Ogólna procedura jest następująca: [Dodawanie domeny niestandardowej do usługi Azure AD](../fundamentals/add-custom-domain.md).

Po sprawdzeniu własności nazwy domeny usługa Azure AD usuwa nazwę domeny z niezarządzanej organizacji i przenosi ją do istniejącej organizacji. Przejęcie zewnętrzną przez administratora niezarządzanego katalogu wymaga tego samego procesu weryfikacji nazw DNS w ramach przejęcia przez administratora wewnętrznego. Różnica polega na tym, że następujące są również przenoszone za pomocą nazwy domeny:

- Użytkownicy
- Subskrypcje
- Przypisania licencji

### <a name="support-for-external-admin-takeover"></a>Obsługa przejęcia przez administratora zewnętrznego
Przejęcie przez administratora zewnętrznego jest obsługiwane przez następujące Usługi online:

- Azure Rights Management
- Exchange Online

Obsługiwane plany usługi obejmują:

- Usługa PowerApps bezpłatnie
- PowerFlow bezpłatnie
- Usługi RMS dla użytkowników indywidualnych
- Usługa Microsoft Stream
- Dynamics 365 — bezpłatna wersja próbna

Przejęcie zewnętrznych administratorów nie jest obsługiwane dla żadnej usługi, która ma plany usług, w tym SharePoint, OneDrive lub Skype dla firm; na przykład za poorednictwem bezpłatnej subskrypcji pakietu Office. 

Opcjonalnie można użyć [opcji **ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) , aby usunąć nazwę domeny z niezarządzanej organizacji i sprawdzić ją w odpowiedniej organizacji. 

#### <a name="more-information-about-rms-for-individuals"></a>Więcej informacji na temat usługi RMS dla użytkowników indywidualnych

W przypadku usługi [RMS dla użytkowników indywidualnych](/azure/information-protection/rms-for-individuals), gdy niezarządzana organizacja znajduje się w tym samym regionie, w którym jest posiadana organizacja, automatycznie tworzony [Azure Information Protection klucz organizacji](/azure/information-protection/plan-implement-tenant-key) i [domyślne szablony ochrony](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) są również przenoszone przy użyciu nazwy domeny.

Klucza i szablonów nie są przenoszone, gdy niezarządzana organizacja znajduje się w innym regionie. Na przykład jeśli niezarządzana organizacja jest w Europie, a posiadana organizacja jest w Ameryka Północna.

Mimo że usługi RMS dla użytkowników indywidualnych są przeznaczone do obsługi uwierzytelniania usługi Azure AD w celu otwierania chronionej zawartości, nie uniemożliwiają one również ochrony zawartości. Jeśli użytkownicy przeprowadzili ochronę zawartości za pomocą subskrypcji usługi RMS dla użytkowników indywidualnych, a klucze i szablony nie zostały przeniesione przez program, zawartość nie jest dostępna po przejęciu domeny.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Polecenia cmdlet programu PowerShell dla usługi Azure AD dla opcji ForceTakeover
Można wyświetlić te polecenia cmdlet używane w [przykładowym programie PowerShell](#powershell-example).

cmdlet | Użycie
------- | -------
`connect-msolservice` | Po wyświetleniu monitu zaloguj się do zarządzanej organizacji.
`get-msoldomain` | Pokazuje nazwy domen skojarzone z bieżącą organizacją.
`new-msoldomain –name <domainname>` | Dodaje nazwę domeny do organizacji jako niezweryfikowaną (nie przeprowadzono jeszcze weryfikacji usługi DNS).
`get-msoldomain` | Nazwa domeny jest teraz uwzględniona na liście nazw domen skojarzonych z zarządzaną organizacją, ale jest wymieniona jako **niezweryfikowana**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Zawiera informacje, które mają zostać umieszczone w nowym rekordzie TXT systemu DNS dla domeny (MS = XXXXX). Weryfikacja może nie nastąpić natychmiast, ponieważ trwa pewien czas na propagację rekordu TXT, więc poczekaj kilka minut przed rozważeniem opcji **-ForceTakeover** . 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Jeśli nazwa domeny nie jest jeszcze zweryfikowana, możesz kontynuować z opcją **-ForceTakeover** . Sprawdza, czy rekord TXT został utworzony i rozpoczyna proces przejęcia.<li>Opcja **-ForceTakeover** należy dodać do polecenia cmdlet tylko w przypadku wymuszenia przejęcia zewnętrznego administratora, na przykład gdy niezarządzana organizacja ma Microsoft 365 usług, które blokują przejęcie.
`get-msoldomain` | Na liście domen jest teraz wyświetlana nazwa domeny, która została **zweryfikowana**.

> [!NOTE]
> Niezarządzana organizacja usługi Azure AD jest usuwana 10 dni po skorzystaniu z opcji zewnętrznego wymuszenia przejęcia.

### <a name="powershell-example"></a>Przykład programu PowerShell

1. Połącz się z usługą Azure AD przy użyciu poświadczeń, które zostały użyte do udzielenia odpowiedzi na ofertę samoobsługi:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Pobierz listę domen:
  
   ```powershell
   Get-MsolDomain
   ```
3. Uruchom polecenie cmdlet Get-MsolDomainVerificationDns, aby utworzyć wyzwanie:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Na przykład:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Skopiuj wartość (wyzwanie) zwracaną z tego polecenia. Na przykład:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. W publicznej przestrzeni nazw DNS Utwórz rekord TXT DNS zawierający wartość skopiowaną w poprzednim kroku. Nazwa tego rekordu to nazwa domeny nadrzędnej, więc jeśli ten rekord zasobu zostanie utworzony przy użyciu roli DNS z systemu Windows Server, pozostaw pustą nazwę rekordu i po prostu wklej wartość w polu tekstowym.
6. Uruchom polecenie cmdlet Confirm-MsolDomain, aby sprawdzić wyzwanie:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Na przykład:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Pomyślne wyzwanie powraca do monitu bez błędu.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie nazwy domeny niestandardowej do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
