---
title: Lokalne zapisywanie zwrotne haseł przy użyciu funkcji samoobsługowego resetowania hasła — Azure Active Directory
description: Dowiedz się, jak zdarzenia zmiany lub resetowania hasła w Azure Active Directory mogą być zapisywane z powrotem do lokalnego środowiska katalogów
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53f416a23dbb47660097c41ada09c8c135434bcb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743653"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Jak działa funkcja stornowania samoobsługowego resetowania haseł w Azure Active Directory?

Azure Active Directory (usługa Azure AD) funkcja samoobsługowego resetowania haseł (SSPR) umożliwia użytkownikom resetowanie haseł w chmurze, ale większość firm ma także lokalne środowisko Active Directory Domain Services (AD DS), w którym znajdują się użytkownicy. Funkcja zapisywania zwrotnego haseł jest funkcją włączoną [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) , która umożliwia zapisywanie zmian haseł w chmurze z powrotem do istniejącego katalogu lokalnego w czasie rzeczywistym. W tej konfiguracji, gdy użytkownicy zmienią lub zresetują swoje hasła przy użyciu usługi SSPR w chmurze, zaktualizowane hasła również są zapisywane z powrotem do środowiska lokalnego AD DS

> [!IMPORTANT]
> Ten artykuł koncepcyjny wyjaśnia, jak działa funkcja zapisywania zwrotnego funkcji samoobsługowego resetowania hasła. Jeśli jesteś użytkownikiem końcowym już zarejestrowanym do samoobsługowego resetowania hasła i chcesz wrócić do swojego konta, przejdź do strony https://aka.ms/sspr .
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

Funkcja zapisywania zwrotnego haseł jest obsługiwana w środowiskach, w których używane są następujące hybrydowe modele tożsamości:

* [Synchronizacja skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta.md)
* [Usługi Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Funkcja zapisywania zwrotnego haseł zapewnia następujące funkcje:

* **Wymuszanie lokalnych zasad haseł Active Directory Domain Services (AD DS)**: gdy użytkownik resetuje swoje hasło, jest sprawdzane pod kątem zgodności lokalnych zasad AD DS przed zatwierdzeniem go do tego katalogu. Ten przegląd obejmuje Sprawdzanie historii, złożoności, wieku, filtrów haseł i innych ograniczeń haseł zdefiniowanych w AD DS.
* **Opinia o zerowej opóźnieniu**: zapisywanie zwrotne haseł jest operacją synchroniczną. Użytkownicy są powiadamiani natychmiast, jeśli ich hasło nie spełnia zasad lub nie można go zresetować ani zmienić z jakiegokolwiek powodu.
* **Program obsługuje zmiany haseł z poziomu panelu dostępu i Microsoft 365**: gdy użytkownicy z dostępem federacyjnym lub skrótem hasła mogą zmienić wygasłe lub niewygasłe hasła, te hasła są zapisywane z powrotem do AD DS.
* **Obsługuje funkcję zapisywania zwrotnego haseł, gdy administrator resetuje je z Azure Portal**: gdy administrator resetuje hasło użytkownika w [Azure Portal](https://portal.azure.com), jeśli ten użytkownik jest zsynchronizowany z zabezpieczeniami federacyjnymi lub hasła, hasło zostanie zapisane z powrotem do lokalnego. Ta funkcja nie jest obecnie obsługiwana w portalu administracyjnym pakietu Office.
* **Nie wymaga żadnych reguł zapory dla ruchu przychodzącego**: funkcja zapisywania zwrotnego haseł używa przekaźnika Azure Service Bus jako podstawowego kanału komunikacyjnego. Cała komunikacja jest wychodząca przez port 443.

> [!NOTE]
> Konta administratorów istniejące w grupach chronionych w lokalnej usłudze AD mogą być używane z funkcją zapisywania zwrotnego haseł. Administratorzy mogą zmienić swoje hasło w chmurze, ale nie mogą zresetować zapomnianego hasła przy użyciu resetowania hasła. Aby uzyskać więcej informacji na temat grup chronionych, zobacz [chronione konta i grupy w AD DS](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Aby rozpocząć pracę z funkcją zapisywania zwrotnego SSPR, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie zapisywania zwrotnego funkcji samoobsługowego resetowania hasła (SSPR)](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Jak działa zapisywanie zwrotne haseł

Gdy użytkownik z synchronizacją federacyjnego lub skrótem hasła próbuje zresetować lub zmienić hasło w chmurze, wystąpią następujące akcje:

1. Sprawdzanie jest wykonywane, aby zobaczyć, jakiego typu hasła dotyczy użytkownik. Jeśli hasło jest zarządzane lokalnie:
   * Sprawdzanie jest przeprowadzane w celu sprawdzenia, czy usługa zapisywania zwrotnego jest uruchomiona. W takim przypadku użytkownik może wykonać operację.
   * Jeśli usługa zapisywania zwrotnego nie działa, użytkownik zostanie poinformowany o tym, że nie można teraz zresetować hasła.
1. Następnie użytkownik przekazuje odpowiednie bramy uwierzytelniania i osiągnie stronę **resetowania hasła** .
1. Użytkownik wybierze nowe hasło i potwierdzi je.
1. Gdy użytkownik wybierze opcję **Prześlij**, hasło w postaci zwykłego tekstu jest szyfrowane przy użyciu klucza symetrycznego utworzonego podczas procesu konfiguracji zapisywania zwrotnego.
1. Zaszyfrowane hasło jest dołączane do ładunku, który jest wysyłany przez kanał HTTPS do przekaźnika usługi Service Bus określonego dla dzierżawy (który jest skonfigurowany dla Ciebie w procesie konfiguracji zapisywania zwrotnego). Ten przekaźnik jest chroniony przez losowo generowane hasło, które zna tylko instalacji lokalnej.
1. Gdy komunikat dociera do usługi Service Bus, punkt końcowy resetowania hasła zostanie automatycznie wznowiony i zobaczy, że ma oczekujące żądanie resetowania.
1. Następnie usługa szuka użytkownika przy użyciu atrybutu zakotwiczenia chmury. Aby to wyszukiwanie zakończyło się pomyślnie, muszą zostać spełnione następujące warunki:

   * Obiekt User musi znajdować się w przestrzeni łącznika AD DS.
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem Metaverse (MV).
   * Obiekt User musi być połączony z odpowiednim obiektem łącznika usługi Azure AD.
   * Łącze z obiektu łącznika AD DS do MV musi mieć regułę synchronizacji `Microsoft.InfromADUserAccountEnabled.xxx` na tym łączu.

   Gdy wywołanie pochodzi z chmury, aparat synchronizacji używa atrybutu **cloudAnchor** , aby wyszukać obiekt przestrzeni łącznika usługi Azure AD. Następnie następuje po łączu z powrotem do obiektu MV, a następnie następuje po linku z powrotem do obiektu AD DS. Ponieważ może istnieć wiele obiektów AD DS (wiele lasów) dla tego samego użytkownika, aparat synchronizacji korzysta z `Microsoft.InfromADUserAccountEnabled.xxx` linku w celu wybrania poprawnego działania.

1. Po znalezieniu konta użytkownika następuje próba zresetowania hasła bezpośrednio w odpowiednim lesie AD DS.
1. Jeśli operacja ustawiania hasła zakończyła się pomyślnie, użytkownik otrzyma informację, że hasło zostało zmienione.

   > [!NOTE]
   > Jeśli skrót hasła użytkownika jest synchronizowany z usługą Azure AD przy użyciu funkcji synchronizacji skrótów haseł, istnieje możliwość, że lokalne zasady haseł są słabsze niż zasady haseł w chmurze. W takim przypadku zasady lokalne są wymuszane. Te zasady zapewniają, że zasady lokalne są wymuszane w chmurze, niezależnie od tego, czy w celu zapewnienia logowania jednokrotnego używasz synchronizacji skrótów haseł lub Federacji.

1. Jeśli operacja ustawiania hasła nie powiedzie się, zostanie wyświetlony komunikat o błędzie, aby spróbować ponownie. Operacja może zakończyć się niepowodzeniem z następujących powodów:
    * Usługa nie działa.
    * Wybrane hasło nie spełnia zasad organizacji.
    * Nie można znaleźć użytkownika w lokalnym środowisku AD DS.

   Komunikaty o błędach zawierają wskazówki dla użytkowników, dzięki czemu mogą próbować rozwiązać problem bez interwencji administratora.

## <a name="password-writeback-security"></a>Zabezpieczenia zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł jest wysoce bezpieczną usługą. Aby zapewnić ochronę informacji, model zabezpieczeń z czterema warstwami jest włączony w następujący sposób:

* **Usługa magistrali usług specyficznych dla dzierżawy**
   * Po skonfigurowaniu usługi przekaźnik usługi Service Bus specyficzny dla dzierżawy jest skonfigurowany do ochrony za pomocą losowo wygenerowanego silnego hasła, do którego firma Microsoft nigdy nie ma dostępu.
* **Zablokowany, kryptograficznie silnie, klucz szyfrowania hasła**
   * Po utworzeniu przekaźnika usługi Service Bus zostaje utworzony silny klucz symetryczny, który jest używany do szyfrowania hasła w postaci, w jakiej się znajduje w sieci. Ten klucz znajduje się tylko w magazynie wpisów tajnych firmy w chmurze, który jest silnie zablokowany i objęty inspekcją, podobnie jak każde inne hasło w katalogu.
* **Standard branżowy Transport Layer Security (TLS)**
   1. Gdy w chmurze jest wykonywana operacja resetowania lub zmiany hasła, hasło w postaci zwykłego tekstu jest szyfrowane przy użyciu klucza publicznego.
   1. Szyfrowane hasło jest umieszczane w wiadomości HTTPS wysyłanej przez szyfrowany kanał przy użyciu certyfikatów Microsoft TLS/SSL do przekaźnika usługi Service Bus.
   1. Po nadejściu wiadomości w usłudze Service Bus Agent lokalny wznawia działanie i uwierzytelnia się do usługi Service Bus przy użyciu silnego hasła, które zostało wcześniej wygenerowane.
   1. Agent lokalny odbiera zaszyfrowany komunikat i odszyfrowuje go przy użyciu klucza prywatnego.
   1. Agent lokalny próbuje ustawić hasło za pomocą interfejsu API AD DS SetPassword. Ten krok umożliwia wymuszanie AD DS lokalnych zasad haseł (takich jak złożoność, wiek, historia i filtry) w chmurze.
* **Zasady wygasania komunikatów**
   * Jeśli komunikat znajduje się w usłudze Service Bus, ponieważ Usługa lokalna nie działa, przetrwa limit czasu i jest usuwany po kilku minutach. Limit czasu i usunięcie wiadomości zwiększają jeszcze więcej zabezpieczeń.

### <a name="password-writeback-encryption-details"></a>Szczegóły szyfrowania zapisywania zwrotnego haseł

Po przesłaniu przez użytkownika resetowania hasła żądanie resetowania przechodzi przez kilka kroków szyfrowania przed ich nadejściem do środowiska lokalnego. Te kroki szyfrowania zapewniają maksymalną niezawodność i bezpieczeństwo usługi. Są one opisane w następujący sposób:

1. **Szyfrowanie hasła przy użyciu 2048-bitowego klucza RSA**: po przesłaniu przez użytkownika hasła do zapisu z powrotem do lokalnego przesłane hasło jest szyfrowane przy 2048 użyciu klucza RSA-bitowego.
1. **Szyfrowanie na poziomie pakietu przy użyciu algorytmu AES-GCM**: cały pakiet, hasło i wymagane metadane są szyfrowane przy użyciu algorytmu AES-GCM. To szyfrowanie uniemożliwia osobie mającej bezpośredni dostęp do podstawowego kanału Service Bus nie można wyświetlać ani modyfikować zawartości.
1. **Cała komunikacja odbywa się za pośrednictwem protokołu TLS/SSL**: cała komunikacja z Service Bus odbywa się w kanale SSL/TLS. To Szyfrowanie zabezpiecza zawartość przed nieautoryzowanymi stronami trzecimi.
1. **Automatyczne Przerzucanie kluczy co sześć miesięcy**: wszystkie klucze są wycofywane co sześć miesięcy, lub za każdym razem, gdy funkcja zapisywania zwrotnego haseł jest wyłączona, a następnie ponownie włączona w Azure AD Connect, aby zapewnić maksymalne bezpieczeństwo i bezpieczeństwo usług.

### <a name="password-writeback-bandwidth-usage"></a>Użycie przepustowości zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł to usługa o niskiej przepustowości, która wysyła żądania z powrotem do lokalnego agenta w następujących okolicznościach:

* Po włączeniu lub wyłączeniu tej funkcji za pomocą Azure AD Connect są wysyłane dwa komunikaty.
* Jeden komunikat jest wysyłany co pięć minut jako puls usługi przez czas, gdy usługa jest uruchomiona.
* Za każdym razem, gdy zostanie przesłane nowe hasło, wysyłane są dwa komunikaty:
   * Pierwszy komunikat to żądanie wykonania operacji.
   * Drugi komunikat zawiera wynik operacji i jest wysyłany w następujących okolicznościach:
      * Za każdym razem, gdy w trakcie samoobsługowego resetowania hasła użytkownika zostanie przesłane nowe hasło.
      * Za każdym razem, gdy w trakcie operacji zmiany hasła użytkownika zostanie przesłane nowe hasło.
      * Za każdym razem, gdy nowe hasło zostanie przesłane podczas resetowania hasła użytkownika zainicjowanego przez administratora (tylko z portali administratora platformy Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Zagadnienia dotyczące rozmiaru i przepustowości wiadomości

Każdy opisany wcześniej komunikat ma zwykle mniej niż 1 KB. Nawet w przypadku skrajnych obciążeń sama usługa zapisywania zwrotnego haseł zużywa kilka kilobitów na sekundę przepustowości. Ponieważ każdy komunikat jest wysyłany w czasie rzeczywistym, tylko wtedy, gdy jest to wymagane przez operację aktualizacji hasła, a ponieważ rozmiar komunikatu jest mały, użycie przepustowości przez funkcję zapisywania zwrotnego jest zbyt małe i ma wymierny wpływ.

## <a name="supported-writeback-operations"></a>Obsługiwane operacje zapisywania zwrotnego

Hasła są zapisywane z powrotem we wszystkich następujących sytuacjach:

* **Obsługiwane operacje wykonywane przez użytkowników końcowych**
   * Każda operacja dobrowolnej zmiany hasła samoobsługowego użytkownika końcowego.
   * Każda operacja zmiany hasła przez użytkownika końcowego, na przykład wygaśnięcie hasła.
   * Wszelkie Samoobsługowe resetowanie haseł użytkowników końcowych, które pochodzą z [portalu resetowania haseł](https://passwordreset.microsoftonline.com).

* **Obsługiwane operacje administratora**
   * Każda operacja automatycznej zmiany hasła samoobsługowego administratora.
   * Każda operacja zmiany hasła przez samoobsługowy administrator, na przykład wygaśnięcie hasła.
   * Wszelkie Samoobsługowe resetowanie hasła administratora pochodzące z [portalu resetowania haseł](https://passwordreset.microsoftonline.com).
   * Wszystkie zainicjowane przez administratora Resetowanie hasła użytkownika końcowego z [Azure Portal](https://portal.azure.com).
   * Wszystkie zainicjowane przez administratora Resetowanie hasła użytkownika końcowego z [Microsoft Graph API beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta).

## <a name="unsupported-writeback-operations"></a>Nieobsługiwane operacje zapisywania zwrotnego

Hasła nie są zapisywane z powrotem w żadnej z następujących sytuacji:

* **Nieobsługiwane operacje użytkownika końcowego**
   * Każdy użytkownik końcowy resetuje własne hasło przy użyciu programu PowerShell w wersji 1, wersji 2 lub Microsoft Graph API.
* **Nieobsługiwane operacje administratora**
   * Wszystkie zainicjowane przez administratora hasło użytkownika końcowego w programie PowerShell w wersji 1, 2 lub Microsoft Graph (obsługiwane jest [Microsoft Graph API beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) ).
   * Wszystkie zainicjowane przez administratora ustawienia resetowania hasła użytkownika końcowego w [centrum administracyjnym Microsoft 365](https://admin.microsoft.com).
   * Każdy administrator nie może użyć narzędzia resetowania hasła do resetowania własnego hasła do zapisywania zwrotnego haseł.

> [!WARNING]
> Użycie pola wyboru "użytkownik musi zmienić hasło przy następnym logowaniu" w lokalnych AD DS narzędziach administracyjnych, takich jak Active Directory Użytkownicy i komputery, lub Centrum administracyjne usługi Active Directory jest obsługiwana jako funkcja w wersji zapoznawczej programu Azure AD Connect. Aby uzyskać więcej informacji, zobacz [implementowanie synchronizacji skrótów haseł przy użyciu synchronizacji Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z funkcją zapisywania zwrotnego SSPR, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie zapisywania zwrotnego funkcji samoobsługowego resetowania hasła (SSPR)](./tutorial-enable-sspr-writeback.md)