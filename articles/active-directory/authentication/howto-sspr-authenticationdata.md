---
title: Wymagania dotyczące danych usługi Azure AD SSPR — Azure Active Directory
description: Wymagania dotyczące danych w przypadku samoobsługowego resetowania hasła usługi Azure AD oraz sposób ich zaspokojenia
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f7e120745357d3bd5735cca568bdd6971ea061
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80652357"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Wdróż Resetowanie hasła bez konieczności rejestrowania użytkowników końcowych

Aby wdrożyć Azure Active Directory (Azure AD) Samoobsługowe resetowanie hasła (SSPR), muszą być obecne dane uwierzytelniania. W niektórych organizacjach użytkownicy wprowadzają swoje dane uwierzytelniania. Inne organizacje wolą synchronizować z danymi, które już istnieją w Active Directory. Te zsynchronizowane dane są udostępniane w usłudze Azure AD i SSPR bez konieczności interakcji z użytkownikiem, jeśli spełniasz następujące wymagania:

* Prawidłowo sformatuj dane w katalogu lokalnym.
* Skonfiguruj [Azure AD Connect przy użyciu ustawień ekspresowych](../hybrid/how-to-connect-install-express.md).

Aby działały prawidłowo, numery telefonów muszą mieć format *+ CountryCode*, na przykład + 1 4255551234.

> [!NOTE]
> Musi być odstęp między kodem kraju i numerem telefonu.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie 4255551234X12345 + 1 rozszerzenia są usuwane przed umieszczeniem wywołania.

## <a name="fields-populated"></a>Wypełniono pola

W przypadku używania ustawień domyślnych w Azure AD Connect są tworzone następujące mapowania:

| Lokalna usługa Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Telefon służbowy |
| telefon komórkowy | Telefon komórkowy |

Gdy użytkownik sprawdzi swój numer telefonu komórkowego, w polu *telefon* w obszarze **informacje kontaktowe uwierzytelniania** w usłudze Azure AD zostanie również wypełniony ten numer.

## <a name="authentication-contact-info"></a>Informacje kontaktowe uwierzytelniania

Na stronie **metody uwierzytelniania** dla użytkownika usługi Azure AD w Azure Portal Administrator globalny może ręcznie ustawić informacje kontaktowe uwierzytelniania, jak pokazano na poniższym przykładowym zrzucie ekranu:

![Informacje kontaktowe uwierzytelniania użytkownika w usłudze Azure AD][Contact]

* Jeśli pole **telefon** jest wypełnione i w zasadach SSPR jest włączona obsługa **telefonu komórkowego** , użytkownik zobaczy ten numer na stronie rejestracji resetowania hasła i w trakcie przepływu pracy resetowania hasła.
* Pole **alternatywny numer telefonu** nie jest używane do resetowania hasła.
* Jeśli pole **adres e-mail** zostanie wypełnione i w zasadach SSPR zostanie włączona **poczta e-mail** , użytkownik zobaczy tę wiadomość e-mail na stronie rejestracji resetowania hasła i podczas przepływu pracy resetowania hasła.
* Jeśli pole **alternatywny adres e-mail** zostanie wypełnione i w zasadach SSPR zostanie włączona **poczta e-mail** , użytkownik **nie** zobaczy tej wiadomości e-mail na stronie rejestracji resetowania hasła, ale zobaczy ją podczas przepływu pracy resetowania hasła.

## <a name="security-questions-and-answers"></a>Pytania zabezpieczające i odpowiedzi

Pytania zabezpieczające i odpowiedzi są bezpiecznie przechowywane w dzierżawie usługi Azure AD i są dostępne tylko dla użytkowników za pośrednictwem [portalu rejestracji SSPR](https://aka.ms/ssprsetup). Administratorzy nie mogą wyświetlać, ustawiać ani modyfikować zawartości innych użytkowników i odpowiedzi.

## <a name="what-happens-when-a-user-registers"></a>Co się stanie, gdy użytkownik rejestruje

Po zarejestrowaniu użytkownika Strona rejestracji ustawia następujące pola:

* **Numer telefonu uwierzytelniania**
* **Adres E-mail uwierzytelniania**
* **Pytania zabezpieczające i odpowiedzi**

Jeśli podano wartość dla **telefonu komórkowego** lub **alternatywnego adresu e-mail**, użytkownicy mogą natychmiast użyć tych wartości, aby zresetować swoje hasła, nawet jeśli nie zostały zarejestrowane dla usługi. Ponadto użytkownicy widzą te wartości przy pierwszym zarejestrowaniu i mogą je modyfikować, jeśli chcesz. Po pomyślnym zarejestrowaniu wartości te są zachowywane odpowiednio w polach **numer telefonu uwierzytelniania** i **adres e-mail uwierzytelniania** .

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ustawianie i odczytywanie danych uwierzytelniania za pomocą programu PowerShell

Następujące pola można ustawić za pomocą programu PowerShell:

* **Alternatywny adres e-mail**
* **Telefon komórkowy**
* **Telefon biurowy**: można ustawić tylko wtedy, gdy synchronizacja z katalogiem lokalnym nie jest przeprowadzana

### <a name="use-powershell-version-1"></a>Korzystanie z programu PowerShell w wersji 1

Aby rozpocząć, musisz [pobrać i zainstalować moduł Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Po zainstalowaniu programu można wykonać poniższe kroki, aby skonfigurować każde pole.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ustawianie danych uwierzytelniania przy użyciu programu PowerShell w wersji 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
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

Aby rozpocząć, musisz [pobrać i zainstalować moduł programu PowerShell dla usługi Azure AD w wersji 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Po zainstalowaniu programu można wykonać poniższe kroki, aby skonfigurować każde pole.

Aby szybko zainstalować program z najnowszych wersji programu PowerShell, które obsługują moduł Install-module, uruchom następujące polecenia. (Pierwszy wiersz sprawdza, czy moduł jest już zainstalowany.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Ustawianie danych uwierzytelniania przy użyciu programu PowerShell w wersji 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
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

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmiana hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś jest zerwane. Jak mogę Rozwiązywanie problemów z SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Administratorzy globalni mogą modyfikować informacje kontaktowe uwierzytelniania użytkownika"
