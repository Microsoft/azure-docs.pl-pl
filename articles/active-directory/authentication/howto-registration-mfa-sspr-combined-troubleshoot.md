---
title: Rozwiązywanie problemów z rejestracją łączną — Azure Active Directory
description: Rozwiązywanie problemów z usługą Azure AD Multi-Factor Authentication i rejestracja łączenia samoobsługowego resetowania haseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac03ff017f8beefe7cc487cdc32741ac1c5a35
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838182"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Rozwiązywanie problemów z rejestracją połączonych informacji o zabezpieczeniach

Informacje przedstawione w tym artykule dotyczą administratorów, którzy rozwiązywają problemy zgłaszane przez użytkowników połączonego środowiska rejestracji.

## <a name="audit-logs"></a>Dzienniki inspekcji

Zdarzenia zarejestrowane dla połączonej rejestracji znajdują się w kategorii metody uwierzytelniania w dziennikach inspekcji usługi Azure AD.

![Interfejs dzienników inspekcji usługi Azure AD przedstawiający zdarzenia rejestracji](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

W poniższej tabeli wymieniono wszystkie zdarzenia inspekcji wygenerowane przez łączną rejestrację:

| Działanie | Stan | Przyczyna | Opis |
| --- | --- | --- | --- |
| Użytkownik zarejestrował wszystkie wymagane informacje zabezpieczające | Powodzenie | Użytkownik zarejestrował wszystkie wymagane informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik pomyślnie zakończył rejestrację.|
| Użytkownik zarejestrował wszystkie wymagane informacje zabezpieczające | Niepowodzenie | Użytkownik anulował rejestrację informacji zabezpieczających. | To zdarzenie występuje, gdy użytkownik anuluje rejestrację w trybie przerwania.|
| Informacje zabezpieczające zarejestrowane przez użytkownika | Powodzenie | *Metoda* zarejestrowana przez użytkownika. | To zdarzenie występuje, gdy użytkownik rejestruje indywidualną metodę. *Metodą* może być aplikacja uwierzytelniania, numer telefonu, adres e-mail, pytania zabezpieczające, hasło aplikacji, alternatywny numer telefonu i tak dalej.| 
| Informacje o zabezpieczeniach zrecenzowanych przez użytkownika | Powodzenie | Użytkownik pomyślnie sprawdził informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik wybierze pozycję **wygląda dobrze** na stronie Przegląd informacji zabezpieczających.|
| Informacje o zabezpieczeniach zrecenzowanych przez użytkownika | Niepowodzenie | Użytkownik nie mógł przejrzeć informacji zabezpieczających. | To zdarzenie występuje, gdy użytkownik wybierze pozycję **wygląda dobrze** na stronie Przegląd informacji zabezpieczających, ale coś nie powiedzie się w zapleczu.|
| Użytkownik usunął informacje zabezpieczające | Powodzenie | *Metoda* usunięta przez użytkownika. | To zdarzenie występuje, gdy użytkownik usunie indywidualną metodę. *Metodą* może być aplikacja uwierzytelniania, numer telefonu, adres e-mail, pytania zabezpieczające, hasło aplikacji, alternatywny numer telefonu i tak dalej.|
| Użytkownik usunął informacje zabezpieczające | Niepowodzenie | Usunięcie *metody* przez użytkownika nie powiodło się. | To zdarzenie występuje, gdy użytkownik próbuje usunąć metodę, ale próba nie powiedzie się z jakiegoś powodu. *Metodą* może być aplikacja uwierzytelniania, numer telefonu, adres e-mail, pytania zabezpieczające, hasło aplikacji, alternatywny numer telefonu i tak dalej.|
| Zmieniono domyślne informacje o zabezpieczeniach użytkownika | Powodzenie | Użytkownik zmienił domyślne informacje zabezpieczające dla *metody*. | To zdarzenie występuje, gdy użytkownik zmieni metodę domyślną. *Metodą* może być powiadomienie aplikacji uwierzytelniania, kod z mojej aplikacji lub tokenu uwierzytelniania, wywołać + x XXXXXXXXXX, tekst kodu do + x xxxxxxxxx i tak dalej.|
| Zmieniono domyślne informacje o zabezpieczeniach użytkownika | Niepowodzenie | Użytkownik nie mógł zmienić domyślnych informacji zabezpieczających dla *metody*. | To zdarzenie występuje, gdy użytkownik próbuje zmienić metodę domyślną, ale próba nie powiedzie się z jakiegoś powodu. *Metodą* może być powiadomienie aplikacji uwierzytelniania, kod z mojej aplikacji lub tokenu uwierzytelniania, wywołać + x XXXXXXXXXX, tekst kodu do + x xxxxxxxxx i tak dalej.|

## <a name="troubleshooting-interrupt-mode"></a>Rozwiązywanie problemów z trybem przerwania

| Objaw | Kroki rozwiązywania problemów |
| --- | --- |
| Nie widzę oczekiwanych metod. | 1. Sprawdź, czy użytkownik ma rolę administratora usługi Azure AD. Jeśli tak, Wyświetl różnice zasad administratora SSPR. <br> 2. Ustal, czy użytkownik jest przerywany z powodu wymuszania rejestracji Multi-Factor Authentication lub wymuszania rejestracji SSPR. Aby określić, które metody mają być wyświetlane, zobacz [schemat blokowy](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) w obszarze "połączone tryby rejestracji". <br> 3. Określ, jak ostatnio Multi-Factor Authentication lub zasady SSPR zostały zmienione. Jeśli zmiana była niedawna, może upłynąć trochę czasu, zanim zaktualizowane zasady są propagowane.|

## <a name="troubleshooting-manage-mode"></a>Rozwiązywanie problemów z trybem zarządzania

| Objaw | Kroki rozwiązywania problemów |
| --- | --- |
| Nie mam opcji dodania określonej metody. | 1. Ustal, czy metoda jest włączona dla Multi-Factor Authentication lub SSPR. <br> 2. Jeśli metoda jest włączona, Zapisz zasady ponownie i poczekaj 1-2 godzin przed ponownym przetestowaniem. <br> 3. Jeśli metoda jest włączona, upewnij się, że użytkownik nie skonfigurował jeszcze maksymalnej liczby tej metody, którą mogą konfigurować.|

## <a name="disable-combined-registration"></a>Wyłącz rejestrację połączoną

Gdy użytkownik rejestruje numer telefonu i/lub aplikację mobilną w nowym połączonym środowisku, Nasza usługa sygnaturuje zestaw flag (StrongAuthenticationMethods) dla tych metod na tym użytkowniku. Ta funkcja umożliwia użytkownikowi wykonywanie Multi-Factor Authentication przy użyciu tych metod, gdy Multi-Factor Authentication jest wymagane.

Jeśli administrator włączy Podgląd, użytkownicy rejestrują się za pomocą nowego środowiska, a następnie administrator wyłącza wersję zapoznawczą, użytkownicy mogą nieświadomie rejestrować się do Multi-Factor Authentication.

Jeśli użytkownik, który ukończył rejestrację, przejdzie do bieżącej strony rejestracji samoobsługowego resetowania hasła (SSPR) w programie [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) , użytkownik zostanie poproszony o wykonanie Multi-Factor Authentication przed uzyskaniem dostępu do tej strony. Ten krok jest oczekiwany z technicznego punktu widzenia, ale jest nowy dla użytkowników, którzy zostali wcześniej zarejestrowani wyłącznie do SSPR. Mimo że ten dodatkowy krok ulepsza stan zabezpieczeń użytkownika, zapewniając inny poziom zabezpieczeń, Administratorzy mogą chcieć wycofać swoich użytkowników, aby nie mieli już możliwości wykonywania Multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Jak wycofać użytkowników

Jeśli administrator chce zresetować ustawienia Multi-Factor Authentication użytkownika, można użyć skryptu programu PowerShell podanego w następnej sekcji. Skrypt wyczyści Właściwość StrongAuthenticationMethods dla aplikacji mobilnej i/lub numeru telefonu użytkownika. Jeśli uruchomisz ten skrypt dla użytkowników, będzie konieczne ponowne zarejestrowanie się w celu Multi-Factor Authentication, jeśli ich potrzebują. Zalecamy przetestowanie wycofywania z jednego lub dwóch użytkowników przed wycofaniem wszystkich użytkowników, których to dotyczy.

Poniższe kroki pomogą Ci wycofać użytkownika lub grupę użytkowników.

#### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj odpowiednie moduły programu PowerShell usługi Azure AD. W oknie programu PowerShell uruchom następujące polecenia, aby zainstalować moduły:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Zapisz listę identyfikatorów obiektów użytkownika, których dotyczy ten komputer, jako plik tekstowy z jednym IDENTYFIKATORem w każdym wierszu. Zanotuj lokalizację pliku.
1. Zapisz następujący skrypt na komputerze i zanotuj lokalizację skryptu:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Wycofywania

W oknie programu PowerShell uruchom następujące polecenie, podając plik skryptu i lokalizacji plików użytkownika. Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego. Skrypt będzie wyprowadzał wynik każdej operacji aktualizacji użytkownika.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Wyłącz zaktualizowane środowisko

Aby wyłączyć zaktualizowane środowisko dla użytkowników, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal jako administrator użytkownika.
2. Przejdź do pozycji **Azure Active Directory**  >  **Ustawienia użytkownika**  >  **Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **Użytkownicy mogą używać funkcji w wersji zapoznawczej na potrzeby rejestrowania i zarządzania informacjami o zabezpieczeniach**, ustawić dla selektora wartość **Brak**, a następnie wybrać pozycję **Zapisz**.

Użytkownicy nie będą już monitowani o rejestrację przy użyciu zaktualizowanego środowiska.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat połączonej rejestracji do samoobsługowego resetowania haseł i usługi Azure AD Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
