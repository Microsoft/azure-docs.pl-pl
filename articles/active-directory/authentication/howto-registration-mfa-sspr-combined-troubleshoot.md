---
title: Rozwiązywanie problemów z rejestracją skojarzoną — usługa Azure Active Directory
description: Rozwiązywanie problemów z uwierzytelnianiem wieloskładnikowym usługi Azure AD i samoobsługową rejestracją przy resetowania hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c840df2c53554519f62a3d1d7a7d8b305187ffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450942"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Rozwiązywanie problemów z rejestracją połączonych informacji o zabezpieczeniach

Informacje zawarte w tym artykule mają na celu poprowadzenie administratorów, którzy rozwiązują problemy zgłoszone przez użytkowników połączonego środowiska rejestracji.

## <a name="audit-logs"></a>Dzienniki inspekcji

Zdarzenia rejestrowane dla połączonej rejestracji znajdują się w kategorii Metody uwierzytelniania w dziennikach inspekcji usługi Azure AD.

![Interfejs dzienników inspekcji usługi Azure AD przedstawiający zdarzenia rejestracji](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

W poniższej tabeli wymieniono wszystkie zdarzenia inspekcji wygenerowane przez rejestrację połączoną:

| Działanie | Stan | Przyczyna | Opis |
| --- | --- | --- | --- |
| Użytkownik zarejestrował wszystkie wymagane informacje zabezpieczające | Powodzenie | Użytkownik zarejestrował wszystkie wymagane informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik pomyślnie zakończył rejestrację.|
| Użytkownik zarejestrował wszystkie wymagane informacje zabezpieczające | Niepowodzenie | Użytkownik anulował rejestrację informacji zabezpieczających. | To zdarzenie występuje, gdy użytkownik anuluje rejestrację z trybu przerwania.|
| Informacje zabezpieczające zarejestrowane przez użytkownika | Powodzenie | *Metoda*zarejestrowana przez użytkownika . | To zdarzenie występuje, gdy użytkownik rejestruje indywidualną metodę. *Metoda* może być aplikacja Authenticator, Telefon, E-mail, Pytania zabezpieczające, Hasło aplikacji, Alternatywny telefon, i tak dalej.| 
| Informacje zabezpieczające użytkownika | Powodzenie | Użytkownik pomyślnie przejrzał informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik wybiera **wygląda dobrze** na stronie przeglądu informacji zabezpieczających.|
| Informacje zabezpieczające użytkownika | Niepowodzenie | Użytkownik nie może przejrzeć informacji zabezpieczających. | To zdarzenie występuje, gdy użytkownik wybiera **wygląda dobrze** na stronie przeglądu informacji zabezpieczeń, ale coś nie powiedzie się w wewnętrznej bazy danych.|
| Informacje zabezpieczające usunięte przez użytkownika | Powodzenie | Metoda *usunięta*przez użytkownika . | To zdarzenie występuje, gdy użytkownik usuwa indywidualną metodę. *Metoda* może być aplikacja Authenticator, Telefon, E-mail, Pytania zabezpieczające, Hasło aplikacji, Alternatywny telefon, i tak dalej.|
| Informacje zabezpieczające usunięte przez użytkownika | Niepowodzenie | Nie można usunąć *metody*użytkownika . | To zdarzenie występuje, gdy użytkownik próbuje usunąć metodę, ale próba nie powiedzie się z jakiegoś powodu. *Metoda* może być aplikacja Authenticator, Telefon, E-mail, Pytania zabezpieczające, Hasło aplikacji, Alternatywny telefon, i tak dalej.|
| Użytkownik zmienił domyślne informacje zabezpieczające | Powodzenie | Użytkownik zmienił domyślne informacje zabezpieczające dla *metody*. | To zdarzenie występuje, gdy użytkownik zmienia metodę domyślną. *Metodą* może być powiadomienie aplikacji Authenticator, kod z mojej aplikacji uwierzytelniacza lub token, Zadzwoń +X XXXXXXXXXX, Tekst kodu do +X XXXXXXXXX, i tak dalej.|
| Użytkownik zmienił domyślne informacje zabezpieczające | Niepowodzenie | Użytkownik nie może zmienić domyślnych informacji zabezpieczających dla *metody*. | To zdarzenie występuje, gdy użytkownik próbuje zmienić metodę domyślną, ale próba nie powiedzie się z jakiegoś powodu. *Metodą* może być powiadomienie aplikacji Authenticator, kod z mojej aplikacji uwierzytelniacza lub token, Zadzwoń +X XXXXXXXXXX, Tekst kodu do +X XXXXXXXXX, i tak dalej.|

## <a name="troubleshooting-interrupt-mode"></a>Rozwiązywanie problemów z trybem przerwania

| Objaw | Kroki rozwiązywania problemów |
| --- | --- |
| Nie widzę metod, które spodziewałem się zobaczyć. | 1. Sprawdź, czy użytkownik ma rolę administratora usługi Azure AD. Jeśli tak, wyświetl różnice zasad administracyjnych sspr. <br> 2. Określ, czy użytkownik jest przerywany z powodu wymuszania rejestracji uwierzytelniania wieloskładnikowego lub egzekwowania rejestracji SSPR. Zobacz [schemat blokowy](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) w obszarze "Połączone tryby rejestracji", aby określić, które metody powinny być wyświetlane. <br> 3. Określ, jak niedawno zmieniono zasady uwierzytelniania wieloskładnikowego lub samookapła. Jeśli zmiana była niedawno, może upłynąć trochę czasu, aby rozpropagować zaktualizowane zasady.|

## <a name="troubleshooting-manage-mode"></a>Rozwiązywanie problemów z trybem zarządzania

| Objaw | Kroki rozwiązywania problemów |
| --- | --- |
| Nie mam możliwości dodania konkretnej metody. | 1. Określ, czy metoda jest włączona dla uwierzytelniania wieloskładnikowego lub dla samookreślenia. <br> 2. Jeśli metoda jest włączona, zapisz zasady ponownie i odczekaj 1-2 godziny przed ponownym testowaniem. <br> 3. Jeśli metoda jest włączona, upewnij się, że użytkownik nie został jeszcze skonfigurowany maksymalną liczbę tej metody, że są one dozwolone do skonfigurowania.|

## <a name="disable-combined-registration"></a>Wyłącz rejestrację połączoną

Gdy użytkownik rejestruje numer telefonu i / lub aplikację mobilną w nowym połączeniu środowiska, nasz serwis stempluje zestaw flag (StrongAuthenticationMetods) dla tych metod na tym użytkowniku. Ta funkcja umożliwia użytkownikowi wykonywanie uwierzytelniania wieloskładnikowego za pomocą tych metod, gdy wymagane jest uwierzytelnianie wieloskładnikowe.

Jeśli administrator włączy podgląd, użytkownicy rejestrują się za pośrednictwem nowego środowiska, a następnie administrator wyłącza podgląd, użytkownicy mogą nieświadomie być zarejestrowani do uwierzytelniania wieloskładnikowego.

Jeśli użytkownik, który ukończył rejestrację łączoną, przejdzie do bieżącej [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)strony rejestracji resetowania hasła samoobsługowego (SSPR) pod adresem , użytkownik zostanie poproszony o wykonanie uwierzytelniania wieloskładnikowego, zanim będzie mógł uzyskać dostęp do tej strony. Ten krok jest oczekiwany z technicznego punktu widzenia, ale jest nowy dla użytkowników, którzy wcześniej byli zarejestrowani tylko dla sspr. Chociaż ten dodatkowy krok poprawia poziom zabezpieczeń użytkownika, zapewniając inny poziom zabezpieczeń, administratorzy mogą chcieć wycofać swoich użytkowników, aby nie mogli już wykonywać uwierzytelniania wieloskładnikowego.  

### <a name="how-to-roll-back-users"></a>Jak wycofać użytkowników

Jeśli jako administrator chcesz zresetować ustawienia uwierzytelniania wieloskładnikowego użytkownika, możesz użyć skryptu programu PowerShell dostępnego w następnej sekcji. Skrypt wyczyści właściwość StrongAuthenticationMethods dla aplikacji mobilnej użytkownika i/lub numeru telefonu. Jeśli uruchomisz ten skrypt dla użytkowników, będą musieli ponownie zarejestrować się w celu uwierzytelniania wieloskładnikowego, jeśli tego potrzebują. Zalecamy przetestowanie wycofywania z jednym lub dwoma użytkownikami przed wycofaniem wszystkich użytkowników, których dotyczy problem.

Następujące kroki pomogą Ci wycofać użytkownika lub grupę użytkowników.

#### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj odpowiednie moduły programu Azure AD PowerShell. W oknie programu PowerShell uruchom następujące polecenia, aby zainstalować moduły:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Zapisz listę identyfikatorów obiektów użytkownika, których dotyczy problem, na komputerze jako plik tekstowy o jednym identyfikatorze w wierszu. Zanotuj lokalizację pliku.
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

W oknie programu PowerShell uruchom następujące polecenie, podając lokalizacje plików skryptu i użytkownika. Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego. Skrypt spowoduje wynik każdej operacji aktualizacji użytkownika.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Wyłączanie zaktualizowanego środowiska

Aby wyłączyć zaktualizowane środowisko dla użytkowników, wykonaj następujące kroki:

1. Zaloguj się do witryny Azure portal jako administrator użytkownika.
2. Przejdź do**ustawień** > użytkownika **usługi Azure Active Directory** > Zarządzanie**ustawieniami funkcji podglądu panelu dostępu**.
3. W obszarze **Użytkownicy mogą używać funkcji podglądu do rejestrowania informacji zabezpieczających i zarządzania**nimi ustaw selektor na **Brak,** a następnie wybierz pozycję **Zapisz**.

Użytkownicy nie będą już monitowani o rejestrację przy użyciu zaktualizowanego środowiska.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o połączonej rejestracji w celu samodzielnego resetowania haseł i uwierzytelniania wieloskładnikowego platformy Azure](concept-registration-mfa-sspr-combined.md)
