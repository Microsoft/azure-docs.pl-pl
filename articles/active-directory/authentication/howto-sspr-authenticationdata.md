---
title: Wstępnie Wypełnij informacje kontaktowe dotyczące samoobsługowego resetowania hasła — Azure Active Directory
description: Dowiedz się, jak wstępnie wypełnić informacje kontaktowe dla użytkowników Azure Active Directory samoobsługowego resetowania hasła (SSPR), aby mogły korzystać z funkcji bez konieczności przeprowadzania procesu rejestracji.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861157"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Wstępnie Wypełnij informacje kontaktowe uwierzytelniania użytkownika dotyczące Azure Active Directory samoobsługowego resetowania hasła (SSPR)

Aby można było korzystać z funkcji samoobsługowego resetowania hasła (SSPR) w programie Azure Active Directory (Azure AD), muszą być obecne informacje kontaktowe uwierzytelniania dla użytkownika. W niektórych organizacjach użytkownicy rejestrują swoje dane uwierzytelniania. Inne organizacje wolą synchronizować dane uwierzytelniania, które już istnieją w Active Directory Domain Services (AD DS). Te zsynchronizowane dane są udostępniane w usłudze Azure AD i SSPR bez konieczności interakcji z użytkownikiem. Gdy użytkownicy muszą zmienić lub zresetować swoje hasło, mogą to zrobić nawet wtedy, gdy nie zarejestrowali wcześniej informacji kontaktowych.

Informacje kontaktowe uwierzytelniania można wstępnie wypełnić, jeśli spełnione są następujące wymagania:

* Dane w katalogu lokalnym zostały prawidłowo sformatowane.
* Skonfigurowano [Azure AD Connect](../hybrid/how-to-connect-install-express.md) dla dzierżawy usługi Azure AD.

Numery telefonów muszą mieć format *+ CountryCode*, na przykład *+ 1 4251234567*.

> [!NOTE]
> Między kodem kraju i numerem telefonu musi znajdować się spacja.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie *4251234567X12345 + 1* rozszerzenia są usuwane przed umieszczeniem wywołania.

## <a name="fields-populated"></a>Wypełniono pola

W przypadku używania ustawień domyślnych w Azure AD Connect następujące mapowania są wykonywane w celu wypełnienia informacji kontaktowych uwierzytelniania dla SSPR:

| Lokalna usługa Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Telefon służbowy |
| telefon komórkowy                       | Telefon komórkowy |

Gdy użytkownik sprawdzi swój numer telefonu komórkowego, w polu *telefon* w obszarze **informacje kontaktowe uwierzytelniania** w usłudze Azure AD zostanie również wypełniony ten numer.

## <a name="authentication-contact-info"></a>Informacje kontaktowe uwierzytelniania

Na stronie **metody uwierzytelniania** dla użytkownika usługi Azure AD w Azure Portal Administrator globalny może ręcznie ustawić informacje kontaktowe uwierzytelniania. Możesz przejrzeć istniejące metody w sekcji *metody uwierzytelniania użytecznych* lub **+ Dodaj metody uwierzytelniania**, jak pokazano na poniższym przykładowym zrzucie ekranu:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Zarządzanie metodami uwierzytelniania z poziomu Azure Portal":::

Następujące zagadnienia dotyczą tych informacji kontaktowych uwierzytelniania:

* Jeśli pole *telefon* jest wypełnione i w zasadach SSPR jest włączona obsługa *telefonu komórkowego* , użytkownik zobaczy ten numer na stronie rejestracji resetowania hasła i w trakcie przepływu pracy resetowania hasła.
* Jeśli pole *adres e-mail* zostanie wypełnione i w zasadach SSPR zostanie włączona *poczta e-mail* , użytkownik zobaczy tę wiadomość e-mail na stronie rejestracji resetowania hasła i podczas przepływu pracy resetowania hasła.

## <a name="security-questions-and-answers"></a>Pytania zabezpieczające i odpowiedzi

Pytania zabezpieczające i odpowiedzi są bezpiecznie przechowywane w dzierżawie usługi Azure AD i są dostępne tylko dla użytkowników za pośrednictwem [portalu rejestracji SSPR](https://aka.ms/ssprsetup). Administratorzy nie mogą wyświetlać, ustawiać ani modyfikować zawartości innych użytkowników i odpowiedzi.

## <a name="what-happens-when-a-user-registers"></a>Co się stanie, gdy użytkownik rejestruje

Po zarejestrowaniu użytkownika Strona rejestracji ustawia następujące pola:

* **Numer telefonu uwierzytelniania**
* **Adres E-mail uwierzytelniania**
* **Pytania zabezpieczające i odpowiedzi**

Jeśli podano wartość dla *telefonu komórkowego* lub *alternatywnego adresu e-mail*, użytkownicy mogą natychmiast użyć tych wartości, aby zresetować swoje hasła, nawet jeśli nie zostały zarejestrowane dla usługi.

Użytkownicy widzą również te wartości podczas rejestracji po raz pierwszy i mogą je modyfikować, jeśli chcesz. Po pomyślnym zarejestrowaniu wartości te są zachowywane odpowiednio w polach *numer telefonu uwierzytelniania* i *adres e-mail uwierzytelniania* .

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ustawianie i odczytywanie danych uwierzytelniania za pomocą programu PowerShell

Następujące pola można ustawić za pomocą programu PowerShell:

* *Alternatywny adres e-mail*
* *Telefon komórkowy*
* *Telefon służbowy*
    * Można ustawić tylko wtedy, gdy synchronizacja z katalogiem lokalnym nie jest przeprowadzana.

> [!IMPORTANT]
> Istnieje znany brak parzystości w funkcjach poleceń między programem PowerShell V1 i programem PowerShell V2. [Interfejs API REST Microsoft Graph (beta) dla metod uwierzytelniania](/graph/api/resources/authenticationmethods-overview) jest bieżącym fokusem inżynieryjnym w celu zapewnienia nowoczesnej interakcji.

### <a name="use-powershell-version-1"></a>Korzystanie z programu PowerShell w wersji 1

Aby rozpocząć, [Pobierz i zainstaluj moduł Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). Po zainstalowaniu programu wykonaj następujące kroki, aby skonfigurować każde pole.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ustawianie danych uwierzytelniania przy użyciu programu PowerShell w wersji 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Odczytywanie danych uwierzytelniania przy użyciu programu PowerShell w wersji 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Odczytywanie opcji numeru telefonu uwierzytelniania i adresu E-mail uwierzytelniania

Aby odczytać **numer telefonu** i **adres e-mail uwierzytelniania** podczas korzystania z programu PowerShell w wersji 1, użyj następujących poleceń:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Korzystanie z programu PowerShell w wersji 2

Aby rozpocząć, [Pobierz i zainstaluj moduł programu PowerShell dla usługi Azure AD w wersji 2](/powershell/module/azuread/).

Aby szybko zainstalować program z najnowszych wersji programu PowerShell, które obsługują `Install-Module` , uruchom następujące polecenia. Pierwszy wiersz sprawdza, czy moduł jest już zainstalowany:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

Po zainstalowaniu modułu wykonaj następujące kroki, aby skonfigurować każde pole.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Ustawianie danych uwierzytelniania przy użyciu programu PowerShell w wersji 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Odczytywanie danych uwierzytelniania przy użyciu programu PowerShell w wersji 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Następne kroki

Gdy informacje kontaktowe dotyczące uwierzytelniania zostaną wstępnie wypełnione dla użytkowników, wykonaj następujące kroki, aby włączyć Samoobsługowe resetowanie hasła:

> [!div class="nextstepaction"]
> [Włącz Samoobsługowe resetowanie haseł w usłudze Azure AD](tutorial-enable-sspr.md)
