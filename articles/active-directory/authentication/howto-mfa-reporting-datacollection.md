---
title: Zbieranie danych użytkownika usługi Azure MFA — Azure Active Directory
description: Jakie informacje są używane w celu ułatwienia uwierzytelniania użytkowników za pomocą usługi Azure Multi-Factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f3b5af972ad6dd15b7c992d5e264ede97bd1dde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80653629"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Zbieranie danych użytkownika Multi-Factor Authentication platformy Azure

W tym dokumencie wyjaśniono, jak znaleźć informacje o użytkowniku zebrane przez usługę Azure Serwer Multi-Factor Authentication (serwer MFA) i usługę Azure MFA (w chmurze) w przypadku zdarzenia, które chcesz usunąć.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Zebrane informacje

W przypadku serwera MFA, rozszerzenia serwera NPS i karty AD FS usługi Azure MFA dla systemu Windows Server 2016 są zbierane i przechowywane następujące informacje przez 90 dni.

Próby uwierzytelniania (używane na potrzeby raportowania i rozwiązywania problemów):

- Timestamp
- Nazwa użytkownika
- Imię
- Nazwisko
- Adres e-mail
- Grupa użytkowników
- Metoda uwierzytelniania (połączenie telefoniczne, wiadomość tekstowa, aplikacja mobilna, token OATH)
- Tryb połączenia telefonicznego (standardowy, PIN)
- Kierunek wiadomości tekstowych (jednokierunkowe, dwukierunkowe)
- Tryb wiadomości tekstowych (OTP, OTP + kod PIN)
- Tryb aplikacji mobilnej (standardowy, kod PIN)
- Tryb tokenu OATH (standardowy, kod PIN)
- Typ uwierzytelniania
- Nazwa aplikacji
- Kod kraju wywołania podstawowego
- Podstawowy numer telefonu wywołania
- Podstawowe rozszerzenie wywołania
- Uwierzytelnione wywołanie podstawowe
- Wynik wywołania podstawowego
- Kod kraju wywołania kopii zapasowej
- Numer telefonu wywołania kopii zapasowej
- Rozszerzenie wywołania kopii zapasowej
- Uwierzytelnione wywołanie kopii zapasowej
- Wynik wywołania kopii zapasowej
- Całkowita uwierzytelnienie
- Wynik ogólny
- Wyniki
- Uwierzytelnione
- Wynik
- Inicjowanie adresu IP
- Devices
- Token urządzenia
- Typ urządzenia
- Wersja aplikacji mobilnej
- Wersja systemu operacyjnego
- Wynik
- Używane sprawdzanie powiadomień

Aktywacje (próba aktywowania konta w aplikacji mobilnej Microsoft Authenticator):
- Nazwa użytkownika
- Nazwa konta
- Timestamp
- Pobierz wynik kodu aktywacji
- Pomyślne aktywowanie
- Błąd aktywacji
- Wynik stanu aktywacji
- Nazwa urządzenia
- Typ urządzenia
- Wersja aplikacji
- Token OATH włączony

Bloki (używane do określania stanu zablokowanego i raportowania):

- Sygnatura czasowa bloku
- Blokuj według nazwy użytkownika
- Nazwa użytkownika
- Kod kraju
- Numer telefonu
- Format numeru telefonu
- Wewnętrzny
- Wyczyść rozszerzenie
- Zablokowane
- Przyczyna blokady
- Znacznik czasu ukończenia
- Przyczyna zakończenia
- Blokada konta
- Alert o oszustwie
- Alert oszustwa nie jest zablokowany
- Język

Pomijanie (używane na potrzeby raportowania):

- Pomiń sygnaturę czasową
- Obejście w sekundach
- Obejście według nazwy użytkownika
- Nazwa użytkownika
- Kod kraju
- Numer telefonu
- Format numeru telefonu
- Wewnętrzny
- Wyczyść rozszerzenie
- Przyczyna obejścia
- Znacznik czasu ukończenia
- Przyczyna zakończenia
- Użyte obejście

Zmiany (używane do synchronizowania zmian użytkowników z serwerem MFA lub usługą Azure AD):

- Zmień sygnaturę czasową
- Nazwa użytkownika
- Nowy kod kraju
- Nowy numer telefonu
- Nowe rozszerzenie
- Nowy kod kraju kopii zapasowej
- Nowy numer telefonu kopii zapasowej
- Nowe rozszerzenie kopii zapasowej
- Nowy numer PIN
- Wymagana zmiana numeru PIN
- Stary token urządzenia
- Nowy token urządzenia

## <a name="gather-data-from-mfa-server"></a>Zbieranie danych z serwera MFA

W przypadku serwera usługi MFA w wersji 8,0 lub nowszej następujący proces pozwala administratorom eksportować wszystkie dane dla użytkowników:

- Zaloguj się do serwera usługi MFA, przejdź do karty **Użytkownicy** , wybierz odpowiednie dane użytkownika, a następnie kliknij przycisk **Edytuj** . Wykonaj zrzuty ekranu (Alt-PrtScn) każdej karty, aby zapewnić użytkownikowi swoje bieżące ustawienia usługi MFA.
- W wierszu polecenia serwera usługi MFA Uruchom następujące polecenie, zmieniając ścieżkę zgodnie z instalacją, `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` Aby utworzyć plik sformatowany w formacie JSON.
- Administratorzy mogą również użyć operacji GetUserGdpr zestawu SDK usługi sieci Web, aby wyeksportować wszystkie informacje o usłudze w chmurze usługi MFA zebrane dla danego użytkownika lub włączyć je do większego rozwiązania do raportowania.
- Wyszukaj `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` i wszystkie kopie zapasowe dla " \<username> " (Uwzględnij cudzysłowy w wyszukiwaniu), aby znaleźć wszystkie wystąpienia rekordu użytkownika, które są dodawane lub zmieniane.
   - Te rekordy mogą być ograniczone (ale nie można ich wyeliminować) przez wyszukanie opcji **"Rejestruj zmiany użytkownika"** w obszarze UX serwera usługi MFA, sekcji rejestrowanie na karcie pliki dziennika.
   - Jeśli skonfigurowano dziennik systemowy i w środowisku użytkownika serwera usługi MFA jest zaznaczone pole wyboru **Rejestruj zmiany użytkowników** , w sekcji rejestrowanie na karcie dziennik systemu, a następnie wpisy dziennika mogą być zbierane z dziennika systemowego.
- Inne wystąpienia nazwy użytkownika w MultiFactorAuthSvc. log i innych plikach dziennika usługi MFA związanych z próbami uwierzytelniania są uznawane za operacyjne i zduplikowane w informacjach dostarczonych przy użyciu MultiFactorAuthGdpr.exe eksportu lub zestawu SDK usługi sieci Web GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Usuń dane z serwera MFA

W wierszu polecenia serwera usługi MFA Uruchom następujące polecenie, zmieniając ścieżkę zgodnie z instalacją, `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` Aby usunąć wszystkie informacje o usłudze w chmurze usługi MFA zebrane dla tego użytkownika.

- Dane zawarte w eksporcie są usuwane w czasie rzeczywistym, ale może upłynąć do 30 dni, aby można było całkowicie usunąć dane operacyjne lub duplikowane.
- Administratorzy mogą również użyć operacji DeleteUserGdpr zestawu SDK usługi sieci Web, aby usunąć wszystkie informacje o usłudze w chmurze usługi MFA zebrane dla danego użytkownika lub włączyć je do większego rozwiązania do raportowania.

## <a name="gather-data-from-nps-extension"></a>Zbierz dane z rozszerzenia serwera NPS

Użyj [portalu Microsoft privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) , aby utworzyć żądanie eksportu.

- Informacje usługi MFA są uwzględniane podczas eksportowania, co może potrwać kilka godzin lub dni.
- Wystąpienia nazwy użytkownika w dziennikach zdarzeń AzureMfa/AuthN/AuthNOptCh, AzureMfa/autoryzacji/AuthZAdminCh i AzureMfa/autoryzacji/AuthZOptCh są uznawane za operacyjne i duplikowane do informacji dostępnych w ramach eksportu.

## <a name="delete-data-from-nps-extension"></a>Usuń dane z rozszerzenia serwera NPS

Użyj [portalu prywatności firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) , aby zażądać zamknięcia konta do usunięcia wszystkich informacji o usłudze w chmurze usługi MFA zebranych dla tego użytkownika.

- Całkowite usunięcie danych może potrwać do 30 dni.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Zbieranie danych z karty AD FS Azure MFA w systemie Windows Server 2016

Użyj [portalu Microsoft privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) , aby utworzyć żądanie eksportu. 

- Informacje usługi MFA są uwzględniane podczas eksportowania, co może potrwać kilka godzin lub dni.
- Wystąpienia nazwy użytkownika w dziennikach zdarzeń śledzenia/debugowania AD FS (jeśli są włączone) są uznawane za operacyjne i duplikowane do informacji dostępnych w ramach eksportu.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Usuwanie danych z karty AD FS Azure MFA w systemie Windows Server 2016

Użyj [portalu prywatności firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) , aby zażądać zamknięcia konta do usunięcia wszystkich informacji o usłudze w chmurze usługi MFA zebranych dla tego użytkownika.

- Całkowite usunięcie danych może potrwać do 30 dni.

## <a name="gather-data-for-azure-mfa"></a>Zbieranie danych dla usługi Azure MFA

Użyj [portalu Microsoft privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) , aby utworzyć żądanie eksportu.

- Informacje usługi MFA są uwzględniane podczas eksportowania, co może potrwać kilka godzin lub dni.

## <a name="delete-data-for-azure-mfa"></a>Usuwanie danych usługi Azure MFA

Użyj [portalu prywatności firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) , aby zażądać zamknięcia konta do usunięcia wszystkich informacji o usłudze w chmurze usługi MFA zebranych dla tego użytkownika.

- Całkowite usunięcie danych może potrwać do 30 dni.

## <a name="next-steps"></a>Następne kroki

[Raportowanie serwera usługi MFA](howto-mfa-reporting.md)
