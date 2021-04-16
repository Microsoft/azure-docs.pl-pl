---
title: Lokalne pisanie zwrotne haseł z samoobsługowego resetowania haseł — Azure Active Directory
description: Dowiedz się, jak można zapisywać zdarzenia zmiany lub resetowania hasła w Azure Active Directory w lokalnym środowisku katalogu
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
ms.openlocfilehash: 4b8a84da331568d36b6f6910054fdb2aea76f490
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530337"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Jak działa zapis zwrotny samoobsługowego resetowania hasła w Azure Active Directory?

Azure Active Directory (Azure AD) samoobsługowe resetowanie haseł umożliwia użytkownikom resetowanie haseł w chmurze, ale większość firm ma również środowisko usług lokalna usługa Active Directory Domain Services (AD DS), w którym znajdują się ich użytkownicy. Funkcja zapisu zwrotnego haseł [](../hybrid/whatis-hybrid-identity.md) jest włączona z Azure AD Connect, która umożliwia zapis zmian haseł w chmurze z powrotem do istniejącego katalogu lokalnego w czasie rzeczywistym. W tej konfiguracji, gdy użytkownicy zmieniają lub resetują swoje hasła przy użyciu funkcji SSPR w chmurze, zaktualizowane hasła są również zapisywane z powrotem w środowisku AD DS lokalnym

> [!IMPORTANT]
> W tym artykule koncepcyjnym wyjaśniono administratorowi, jak działa zapis zwrotny samoobsługowego resetowania hasła. Jeśli jesteś już użytkownikiem końcowym zarejestrowanym na potrzeby samoobsługowego resetowania hasła i musisz wrócić do swojego konta, przejdź na adres https://aka.ms/sspr .
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, należy skontaktować się z działem pomocy technicznej, aby uzyskać dodatkową pomoc.

Zapis zwrotny haseł jest obsługiwany w środowiskach, które korzystają z następujących modeli tożsamości hybrydowej:

* [Synchronizacja skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Uwierzytelnianie pass-through](../hybrid/how-to-connect-pta.md)
* [Usługi Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Funkcja zapisu zwrotnego haseł zapewnia następujące funkcje:

* Wymuszanie zasad haseł usług **lokalna usługa Active Directory Domain Services (AD DS):** gdy użytkownik resetuje swoje hasło, sprawdza się, czy spełnia on lokalne zasady AD DS przed zatwierdzeniem go w tym katalogu. Ten przegląd obejmuje sprawdzanie historii, złożoności, wieku, filtrów haseł i wszelkich innych ograniczeń haseł, które zdefiniowano w AD DS.
* **Opinie bez opóźnień:** zapis zwrotny haseł jest operacją synchroniczną. Użytkownicy są natychmiast powiadamiani, jeśli ich hasło nie spełnia wymagań zasad lub z jakiegokolwiek powodu nie można ich zresetować ani zmienić.
* Obsługuje zmiany haseł z **panelu** dostępu i Microsoft 365: gdy użytkownicy zsynchronizowani z federacyjną lub synchronizacją skrótów haseł zmieniają swoje wygasłe lub nie wygasłe hasła, te hasła są zapisywane z powrotem AD DS.
* Obsługuje funkcję zapisu zwrotnego haseł, gdy administrator resetuje je z usługi **Azure Portal:** jeśli administrator resetuje hasło użytkownika w programie Azure Portal , [jeśli](https://portal.azure.com)ten użytkownik jest federowany lub synchronizowany jest skrót hasła, hasło jest zapisywane z powrotem w środowisku lokalnym. Ta funkcja nie jest obecnie obsługiwana w portalu administracyjnym usługi Office.
* **Nie wymaga żadnych reguł zapory** dla ruchu przychodzącego: zapis zwrotny haseł używa przekaźnika Azure Service Bus jako podstawowego kanału komunikacyjnego. Cała komunikacja jest wychodząca przez port 443.

> [!NOTE]
> Konta administratorów, które istnieją w grupach chronionych w lokalnej u usługi AD, mogą być używane z zwrotnego zapisu haseł. Administratorzy mogą zmienić swoje hasło w chmurze, ale nie mogą zresetować zapomnianego hasła przy użyciu funkcji resetowania hasła. Aby uzyskać więcej informacji na temat grup chronionych, zobacz [Chronione konta i grupy w AD DS](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Aby rozpocząć pracę z zapisem zwrotny SSPR, wykonaj czynności z następującego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: włączanie samoobsługowego resetowania hasła (SSPR) — zapis zwrotny](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Jak działa zapisywanie zwrotne haseł

Gdy użytkownik federowany lub zsynchronizowany skrót hasła próbuje zresetować lub zmienić swoje hasło w chmurze, są podejmowane następujące akcje:

1. Wykonywane jest sprawdzenie, jakiego typu hasło ma użytkownik. Jeśli hasło jest zarządzane lokalnie:
   * Wykonywane jest sprawdzenie, czy usługa zapisu zwrotnego jest uruchomiona. Jeśli tak, użytkownik może kontynuować.
   * Jeśli usługa zapisu zwrotnego nie działa, użytkownik jest poinformowany, że nie można zresetować jego hasła w tej chwili.
1. Następnie użytkownik przekazuje odpowiednie bramy uwierzytelniania i przechodzi do **strony Resetowanie** hasła.
1. Użytkownik wybiera nowe hasło i potwierdza je.
1. Gdy użytkownik wybierze pozycję **Prześlij,** hasło w postaci zwykłego tekstu zostanie zaszyfrowane przy użyciu klucza symetrycznego utworzonego podczas procesu konfiguracji zapisu zwrotnego.
1. Zaszyfrowane hasło jest zawarte w ładunku, który jest wysyłany za pośrednictwem kanału HTTPS do przekaźnika magistrali usług specyficznego dla dzierżawy (który jest konfigurowany dla Ciebie podczas procesu konfiguracji zapisu zwrotnego). Ten przekaźnik jest chroniony przez losowo wygenerowane hasło, które zna tylko twoja instalacja lokalna.
1. Gdy komunikat dotrze do magistrali usług, punkt końcowy resetowania hasła automatycznie wznawia się i widzi, że oczekuje na żądanie zresetowania.
1. Usługa następnie szuka użytkownika przy użyciu atrybutu zakotwiczenia chmury. Aby to wyszukiwania zakończyło się pomyślnie, muszą zostać spełnione następujące warunki:

   * Obiekt użytkownika musi istnieć w AD DS łącznika.
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem Metaverse (MV).
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem łącznika usługi Azure AD.
   * Link z obiektu AD DS łącznika do MV musi zawierać regułę synchronizacji `Microsoft.InfromADUserAccountEnabled.xxx` w tym linku.

   Gdy wywołanie pochodzi z chmury, aparat synchronizacji używa atrybutu **cloudAnchor** do wyszukiwania obiektu przestrzeni łącznika usługi Azure AD. Następnie następuje połączenie z powrotem z obiektem MV, a następnie następuje z powrotem do AD DS obiektu. Ponieważ może być wiele AD DS (wiele lasów) dla tego samego użytkownika, aparat synchronizacji korzysta z linku, aby wybrać `Microsoft.InfromADUserAccountEnabled.xxx` właściwy.

1. Po odnalezioniu konta użytkownika podejmowana jest próba zresetowania hasła bezpośrednio w odpowiednim AD DS lesie.
1. Jeśli operacja ustawienia hasła powiedzie się, użytkownik będzie informować o zmianie hasła.

   > [!NOTE]
   > Jeśli skrót hasła użytkownika jest synchronizowany z usługą Azure AD przy użyciu synchronizacji skrótów haseł, istnieje prawdopodobieństwo, że lokalne zasady haseł będą słabsze niż zasady haseł w chmurze. W takim przypadku wymuszane są zasady lokalne. Te zasady gwarantują, że zasady lokalne są wymuszane w chmurze, niezależnie od tego, czy używasz synchronizacji skrótów haseł, czy federacji w celu zapewnienia logowania pojedynczego.

1. Jeśli operacja ustawienia hasła nie powiedzie się, zostanie wyświetlony komunikat o błędzie z monitem o próbę. Operacja może się nie powieść z następujących powodów:
    * Usługa nie została przesłoniła.
    * Wybrane hasło nie spełnia zasad organizacji.
    * Nie można odnaleźć użytkownika w środowisku lokalnym AD DS lokalnym.

   Komunikaty o błędach zawierają wskazówki dla użytkowników, dzięki czemu mogą spróbować rozwiązać problem bez interwencji administratora.

## <a name="password-writeback-security"></a>Zabezpieczenia zapisu zwrotnego haseł

Zapis zwrotny haseł to wysoce bezpieczna usługa. W celu zapewnienia ochrony informacji można włączyć czterowarstwowy model zabezpieczeń w następujący sposób:

* **Przekaźnik magistrali usług specyficzny dla dzierżawy**
   * Po skonfigurowaniu usługi przekaźnik magistrali usług specyficzny dla dzierżawy jest kon setowany, który jest chroniony przez losowo wygenerowane silne hasło, do których firma Microsoft nigdy nie ma dostępu.
* **Zablokowany, kryptograficznie silny, klucz szyfrowania hasła**
   * Po utworzeniu przekaźnika usługi Service Bus jest tworzony silny klucz symetryczny, który służy do szyfrowania hasła podczas jego przekazywania. Ten klucz istnieje tylko w magazynie tajnym firmy w chmurze, która jest silnie zablokowana i podlega inspekcji, podobnie jak każde inne hasło w katalogu.
* **Standard branżowy Transport Layer Security (TLS)**
   1. Gdy operacja resetowania lub zmiany hasła odbywa się w chmurze, hasło w postaci zwykłego tekstu jest szyfrowane za pomocą klucza publicznego.
   1. Zaszyfrowane hasło jest umieszczane w komunikacie HTTPS, który jest wysyłany za pośrednictwem szyfrowanego kanału przy użyciu certyfikatów Protokołu TLS/SSL firmy Microsoft do przekaźnika usługi Service Bus.
   1. Po dotareniu komunikatu do magistrali usług agent lokalnie wznawia działanie i uwierzytelnia się w magistrali usług przy użyciu wcześniej wygenerowanego silnego hasła.
   1. Agent on-premises przejmuje zaszyfrowany komunikat i odszyfrowuje go przy użyciu klucza prywatnego.
   1. Agent on-premises próbuje ustawić hasło za pośrednictwem interfejsu API setPassword AD DS. Ten krok umożliwia wymuszanie zasad AD DS lokalnych haseł (takich jak złożoność, wiek, historia i filtry) w chmurze.
* **Zasady wygasania komunikatów**
   * Jeśli komunikat znajduje się w usłudze Service Bus, ponieważ usługa lokalna nie jest w stanie chmurze, zostanie on u góry u dołu i usunięty po kilku minutach. Przeoczycie i usunięcie komunikatu jeszcze bardziej zwiększa bezpieczeństwo.

### <a name="password-writeback-encryption-details"></a>Szczegóły szyfrowania zapisu zwrotnego haseł

Po przesłaniu przez użytkownika hasła żądanie zresetowania przechodzi przez kilka kroków szyfrowania, zanim dotrze do środowiska lokalnego. Te kroki szyfrowania zapewniają maksymalną niezawodność i bezpieczeństwo usługi. Są one opisane w następujący sposób:

1. Szyfrowanie haseł za pomocą **2048-bitowego klucza RSA:** po przesłaniu przez użytkownika hasła do lokalnego wrócić do lokalnego, przesłane hasło jest szyfrowane za pomocą 2048-bitowego klucza RSA.
1. Szyfrowanie na poziomie pakietu za pomocą **AES-GCM:** cały pakiet, hasło oraz wymagane metadane, jest szyfrowany przy użyciu szyfrowania AES-GCM. To szyfrowanie uniemożliwia osobom z bezpośrednim dostępem do kanału Service Bus wyświetlanie zawartości lub manipulowanie jej zawartością.
1. **Cała komunikacja odbywa się za pośrednictwem protokołu TLS/SSL:** cała komunikacja z Service Bus odbywa się w kanale SSL/TLS. To szyfrowanie zabezpiecza zawartość przed nieautoryzowanymi stronami trzecimi.
1. **Automatyczne** przewłacanie kluczy co sześć miesięcy: wszystkie klucze są przenoszane co sześć miesięcy lub za każdym razem, gdy funkcja zapisu zwrotnego haseł jest wyłączona, a następnie ponownie włączana na Azure AD Connect, aby zapewnić maksymalne bezpieczeństwo i bezpieczeństwo usługi.

### <a name="password-writeback-bandwidth-usage"></a>Użycie przepustowości zapisu zwrotnego haseł

Zapis zwrotny haseł to usługa o niskiej przepustowości, która wysyła żądania z powrotem do agenta lokalnego tylko w następujących okolicznościach:

* Dwa komunikaty są wysyłane, gdy funkcja jest włączona lub wyłączona za pośrednictwem Azure AD Connect.
* Jeden komunikat jest wysyłany co pięć minut jako puls usługi, tak długo, jak długo usługa jest uruchomiona.
* Dwa komunikaty są wysyłane za każdym razem, gdy jest przesyłane nowe hasło:
   * Pierwszy komunikat to żądanie wykonania operacji.
   * Drugi komunikat zawiera wynik operacji i jest wysyłany w następujących okolicznościach:
      * Za każdym razem, gdy podczas samoobsługowego resetowania hasła użytkownika jest przesyłane nowe hasło.
      * Za każdym razem, gdy podczas operacji zmiany hasła użytkownika jest przesyłane nowe hasło.
      * Za każdym razem, gdy nowe hasło jest przesyłane podczas resetowania hasła użytkownika zainicjowanego przez administratora (tylko z portalu administracyjnego platformy Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Zagadnienia dotyczące rozmiaru komunikatu i przepustowości

Rozmiar każdego z opisanych wcześniej komunikatów jest zwykle poniżej 1 KB. Nawet przy skrajnych obciążeniach sama usługa zapisu zwrotnego haseł zużywa kilka kilobitów na sekundę przepustowości. Ponieważ każdy komunikat jest wysyłany w czasie rzeczywistym, tylko wtedy, gdy jest to wymagane przez operację aktualizacji hasła, a ponieważ rozmiar komunikatu jest tak mały, użycie przepustowości funkcji zapisu zwrotnego jest zbyt małe, aby miało mierzalny wpływ.

## <a name="supported-writeback-operations"></a>Obsługiwane operacje zapisu zwrotnego

Hasła są zapisywane z powrotem we wszystkich następujących sytuacjach:

* **Obsługiwane operacje użytkownika końcowego**
   * Dowolna samoobsługowa operacja samoobsługowej zmiany hasła przez użytkownika końcowego.
   * Dowolna samoobsługowa operacja zmiany hasła przez użytkownika końcowego, na przykład wygaśnięcie hasła.
   * Dowolne samoobsługowe resetowanie hasła przez użytkownika końcowego pochodzące z [portalu resetowania haseł.](https://passwordreset.microsoftonline.com)

* **Obsługiwane operacje administratora**
   * Dowolna samoobsługowa operacja samoobsługowej zmiany hasła przez administratora.
   * Dowolna samoobsługowa operacja wymuś zmianę hasła przez administratora, na przykład wygaśnięcie hasła.
   * Dowolne samoobsługowe resetowanie hasła administratora pochodzące z portalu [resetowania haseł.](https://passwordreset.microsoftonline.com)
   * Dowolne resetowanie hasła użytkownika końcowego zainicjowane przez [administratora](https://portal.azure.com)z Azure Portal .
   * Dowolne zainicjowane przez administratora resetowanie hasła użytkownika końcowego z [interfejsu API Microsoft Graph .](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http)

## <a name="unsupported-writeback-operations"></a>Nieobsługiwane operacje zapisu zwrotnego

Hasła nie są zapisywane z powrotem w żadnej z następujących sytuacji:

* **Nieobsługiwane operacje użytkownika końcowego**
   * Każdy użytkownik końcowy resetujący własne hasło przy użyciu programu PowerShell w wersji 1, wersji 2 lub interfejsu API Microsoft Graph API.
* **Nieobsługiwane operacje administratora**
   * Resetowanie hasła użytkownika końcowego zainicjowane przez administratora z programu PowerShell w wersji 1, wersji 2 lub interfejsu API Microsoft Graph (obsługiwany jest interfejs [API](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http) Microsoft Graph).
   * Dowolne resetowanie hasła użytkownika końcowego zainicjowane przez [administratora](https://admin.microsoft.com)z centrum administracyjne platformy Microsoft 365 .
   * Żaden administrator nie może użyć narzędzia do resetowania haseł, aby zresetować własne hasło na użytek funkcji zapisu zwrotnego haseł.

> [!WARNING]
> Użyj pola wyboru "Użytkownik musi zmienić hasło podczas następnego logowania" w lokalnych narzędziach administracyjnych programu AD DS, takich jak Użytkownicy i komputery usługi Active Directory lub Centrum administracyjne usługi Active Directory, jest obsługiwana jako funkcja wersji zapoznawczej Azure AD Connect. Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z Azure AD Connect synchronizacji.](../hybrid/how-to-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z zapisem zwrotny SSPR, wykonaj czynności z następującego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: włączanie samoobsługowego resetowania hasła (SSPR) — zapis zwrotny](./tutorial-enable-sspr-writeback.md)