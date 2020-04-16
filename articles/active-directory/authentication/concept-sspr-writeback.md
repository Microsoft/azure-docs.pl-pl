---
title: Funkcja zapisywania zwrotnego haseł w środowisku lokalnym z samoobsługowym resetowaniem hasła — usługa Azure Active Directory
description: Dowiedz się, jak można zapisywać zdarzenia zmiany lub resetowania haseł w usłudze Azure Active Directory w lokalnym środowisku katalogowym
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787c15c11c995c7eb30662131302658175c7f877
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393022"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Jak działa samoobsługowy resetowanie hasła w usłudze Azure Active Directory?

Samoobsługowe resetowanie haseł usługi Azure Active Directory (Azure AD) umożliwia użytkownikom resetowanie haseł w chmurze, ale większość firm ma również lokalne środowisko usług domenowych Active Directory (AD DS), w którym istnieją ich użytkownicy. Zapisywanie hasła to funkcja włączona za pomocą [usługi Azure AD Connect,](../hybrid/whatis-hybrid-identity.md) która umożliwia zapisywanie zmian haseł w chmurze w istniejącym katalogu lokalnym w czasie rzeczywistym. W tej konfiguracji, gdy użytkownicy zmieniają lub resetują hasła przy użyciu samookapowania w chmurze, zaktualizowane hasła są również zapisywane z powrotem w lokalnym środowisku usług AD DS

Zapisywanie zwrotne hasła jest obsługiwane w środowiskach korzystających z następujących modeli tożsamości hybrydowej:

* [Synchronizacja skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta.md)
* [Usługi Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Funkcja zapisywania hasła zapewnia następujące funkcje:

* **Wymuszanie lokalnych zasad haseł usług domenowych Active Directory (AD DS):** Gdy użytkownik resetuje swoje hasło, jest sprawdzany, aby upewnić się, że spełnia lokalne zasady usług AD DS przed zatwierdzeniem go do tego katalogu. Ta recenzja obejmuje sprawdzanie historii, złożoności, wieku, filtrów haseł i innych ograniczeń haseł zdefiniowanych w usługach AD DS.
* **Zero-delay feedback**: Writeback hasło jest operacją synchronicznego. Użytkownicy są natychmiast powiadamiani, jeśli ich hasło nie spełnia zasad lub nie można ich zresetować ani zmienić z jakiegokolwiek powodu.
* **Obsługuje zmiany hasła z panelu dostępu i usługi Office 365**: Gdy zsynchronizowani użytkownicy federacyjne lub synchronizowane hasła zmieniają wygasłe lub nieuczesane hasła, hasła te są zapisywane z powrotem w usługach AD DS.
* **Obsługuje zapisywanie haseł, gdy administrator resetuje je z witryny Azure Portal:** Gdy administrator resetuje hasło użytkownika w [witrynie Azure portal](https://portal.azure.com), jeśli ten użytkownik jest sfederowany lub hash hash zsynchronizowane, hasło jest zapisywane z powrotem do lokalnego. Ta funkcja nie jest obecnie obsługiwana w portalu administracyjnym pakietu Office.
* **Nie wymaga żadnych reguł zapory przychodzącej:** stornowania hasła używa przekaźnika usługi Azure Service Bus jako podstawowy kanał komunikacji. Cała komunikacja jest wychodząca przez port 443.

> [!NOTE]
> Konta administratora, które istnieją w grupach chronionych w lokalnej układzie AD, mogą być używane z zapisywaniem hasła. Administratorzy mogą zmieniać swoje hasło w chmurze, ale nie mogą resetować zapomnianego hasła za pomocą hasła. Aby uzyskać więcej informacji na temat chronionych grup, zobacz [Chronione konta i grupy w usłudze Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="how-password-writeback-works"></a>Jak działa zapisywanie zwrotne haseł

Gdy zsynchronizowany użytkownik federowany lub haszem hasło próbuje zresetować lub zmienić hasło w chmurze, występują następujące akcje:

1. Sprawdzaj, czy użytkownik ma hasło. Jeśli hasło jest zarządzane lokalnie:
   * Sprawdzaj jest wykonywana, aby sprawdzić, czy usługa writeback jest uruchomiona. Jeśli tak, użytkownik może kontynuować.
   * Jeśli usługa stornowania jest wyłączna, użytkownik jest informowany, że ich hasło nie można zresetować w tej chwili.
1. Następnie użytkownik przechodzi odpowiednie bramy uwierzytelniania i dociera do strony **Resetowanie hasła.**
1. Użytkownik wybiera nowe hasło i potwierdza je.
1. Gdy użytkownik wybierze **opcję Prześlij,** hasło w postaci zwykłego tekstu jest szyfrowane przy pomocą klucza symetrycznego utworzonego podczas procesu konfiguracji stortowania.
1. Zaszyfrowane hasło znajduje się w ładunku, który jest wysyłany za pośrednictwem kanału HTTPS do przekaźnika magistrali usług specyficzne dla dzierżawy (który jest skonfigurowany dla Ciebie podczas procesu konfiguracji stornowania). Ten przekaźnik jest chroniony losowo wygenerowanym hasłem, które zna tylko instalacja lokalna.
1. Po dotarciu do magistrali usług, punkt końcowy resetowania hasła automatycznie wznawia się i widzi, że ma żądanie resetowania oczekujących.
1. Usługa następnie wyszukuje użytkownika przy użyciu atrybutu zakotwiczenia w chmurze. Aby to wyszukiwanie zakończyło się pomyślnie, muszą być spełnione następujące warunki:

   * Obiekt użytkownika musi istnieć w przestrzeni łącznika usługi Active Directory.
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem metaverse (MV).
   * Obiekt użytkownika musi być połączony z odpowiednim obiektem łącznika usługi Azure Active Directory.
   * Łącze z obiektu łącznika usługi Active Directory do `Microsoft.InfromADUserAccountEnabled.xxx` pliku MV musi mieć regułę synchronizacji łącza.

   Gdy wywołanie pochodzi z chmury, aparat synchronizacji używa **atrybutu cloudAnchor** do wyszukiwania obiektu przestrzeni łącznika usługi Azure Active Directory. Następnie następuje łącze z powrotem do obiektu MV, a następnie następuje łącze z powrotem do obiektu usługi Active Directory. Ponieważ może istnieć wiele obiektów usługi Active Directory (multi-forest) dla `Microsoft.InfromADUserAccountEnabled.xxx` tego samego użytkownika, aparat synchronizacji opiera się na łączu, aby wybrać właściwy.

1. Po znalezieniu konta użytkownika podejmowana jest próba zresetowania hasła bezpośrednio w odpowiednim lesie usługi Active Directory.
1. Jeśli operacja zestawu haseł zakończy się pomyślnie, użytkownik zostanie poinformowany, że jego hasło zostało zmienione.

   > [!NOTE]
   > Jeśli hash hasła użytkownika jest synchronizowany z usługą Azure AD przy użyciu synchronizacji skrótu hasła, istnieje prawdopodobieństwo, że lokalne zasady haseł jest słabszy niż zasady haseł w chmurze. W takim przypadku zasady lokalne są wymuszane. Ta zasada gwarantuje, że zasady lokalne są wymuszane w chmurze, niezależnie od tego, czy używasz synchronizacji skrótów haseł lub federacji w celu zapewnienia logowania jednokrotnego.

1. Jeśli operacja ustawiania hasła nie powiedzie się, błąd monituje użytkownika o ponowną próbę. Operacja może zakończyć się niepowodzeniem z następujących powodów:
    * Usługa została wyłączna.
    * Wybrane przez nie hasło nie spełnia zasad organizacji.
    * Nie można znaleźć użytkownika w lokalnej usłudze Active Directory.

   Komunikaty o błędach zawierają wskazówki dla użytkowników, dzięki czemu mogą próbować rozwiązać bez interwencji administratora.

## <a name="password-writeback-security"></a>Zabezpieczenia przed zapisywaniem zwrotnego hasła

Zapisywanie hasła jest wysoce bezpieczną usługą. Aby upewnić się, że informacje są chronione, czterowarstwowy model zabezpieczeń jest włączony w następujący sposób:

* **Przekaźnik magistrali usługowej specyficznej dla dzierżawcy**
   * Podczas konfigurowania usługi jest skonfigurowany przekaźnik magistrali usług specyficzne dla dzierżawy, który jest chroniony przez losowo generowane silne hasło, do którego firma Microsoft nigdy nie ma dostępu.
* **Zablokowany, kryptograficznie silny, klucz szyfrowania hasła**
   * Po utworzeniu przekaźnika magistrali usług tworzony jest silny klucz symetryczny, który jest używany do szyfrowania hasła, gdy pojawi się przez przewód. Ten klucz działa tylko w tajnym magazynie twojej firmy w chmurze, który jest mocno zablokowany i poddany audytowi, podobnie jak każde inne hasło w katalogu.
* **Standard branżowy Transport Layer Security (TLS)**
   1. Gdy operacja resetowania lub zmiany hasła występuje w chmurze, hasło w postaci zwykłego tekstu jest szyfrowane za pomocą klucza publicznego.
   1. Zaszyfrowane hasło jest umieszczane w wiadomości HTTPS, która jest wysyłana za pośrednictwem zaszyfrowanego kanału przy użyciu certyfikatów Microsoft TLS/SSL do przekaźnika magistrali usług.
   1. Po odebraniu wiadomości w magistrali usług agenta lokalnego wznawia się i uwierzytelnia do magistrali usług przy użyciu silnego hasła, który został wcześniej wygenerowany.
   1. Agent lokalny odbiera zaszyfrowaną wiadomość i odszyfrowuje ją przy użyciu klucza prywatnego.
   1. Agent lokalny próbuje ustawić hasło za pośrednictwem interfejsu API SetPassword usługi AD DS. Ten krok umożliwia wymuszanie lokalnych zasad haseł usługi Active Directory (takich jak złożoność, wiek, historia i filtry) w chmurze.
* **Zasady wygasania wiadomości**
   * Jeśli komunikat znajduje się w magistrali usług, ponieważ usługa lokalna jest wyłączna, upływ czasu i jest usuwany po kilku minutach. Limit czasu i usunięcie wiadomości jeszcze bardziej zwiększa bezpieczeństwo.

### <a name="password-writeback-encryption-details"></a>Szczegóły szyfrowania stornadzki haseł

Po przesłaniu hasła przez użytkownika żądanie resetowania przechodzi kilka kroków szyfrowania, zanim pojawi się w środowisku lokalnym. Te kroki szyfrowania zapewniają maksymalną niezawodność i bezpieczeństwo usług. Są one opisane w następujący sposób:

1. **Szyfrowanie hasła za pomocą 2048-bitowego klucza RSA**: Po przesłaniu hasła do lokalnego, samo przesłane hasło jest szyfrowane za pomocą 2048-bitowego klucza RSA.
1. **Szyfrowanie na poziomie pakietu za pomocą AES-GCM:** Cały pakiet, hasło i wymagane metadane, jest szyfrowany przy użyciu AES-GCM. To szyfrowanie uniemożliwia osobom mającym bezpośredni dostęp do podstawowego kanału ServiceBus przeglądanie lub manipulowanie zawartością.
1. **Cała komunikacja odbywa się za pomocą protokołu TLS/SSL:** Cała komunikacja z ServiceBus odbywa się w kanale SSL/TLS. To szyfrowanie zabezpiecza zawartość przed nieautoryzowanymi osobami trzecimi.
1. **Automatyczne przewracanie kluczy co sześć miesięcy:** wszystkie klucze są przetaczane co sześć miesięcy lub za każdym razem, gdy zapisywanie zwrotne haseł jest wyłączone, a następnie ponownie włączone w usłudze Azure AD Connect, aby zapewnić maksymalne bezpieczeństwo i bezpieczeństwo usług.

### <a name="password-writeback-bandwidth-usage"></a>Użycie przepustowości storna resetu zwrotnego hasła

Stornowania hasła to usługa o niskiej przepustowości, która wysyła żądania tylko do agenta lokalnego w następujących okolicznościach:

* Dwie wiadomości są wysyłane, gdy funkcja jest włączona lub wyłączona za pośrednictwem usługi Azure AD Connect.
* Jedna wiadomość jest wysyłana raz na pięć minut jako puls usługi tak długo, jak usługa jest uruchomiona.
* Za każdym razem, gdy zostanie przesłane nowe hasło, wysyłane są dwie wiadomości:
   * Pierwszy komunikat jest żądanie do wykonania operacji.
   * Druga wiadomość zawiera wynik operacji i jest wysyłana w następujących okolicznościach:
      * Za każdym razem, gdy nowe hasło jest przesyłane podczas samodzielnego resetowania hasła przez użytkownika.
      * Za każdym razem, gdy nowe hasło jest przesyłane podczas operacji zmiany hasła użytkownika.
      * Za każdym razem, gdy nowe hasło jest przesyłane podczas resetowania hasła użytkownika inicjowanego przez administratora (tylko z portali administracyjnych platformy Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Zagadnienia dotyczące rozmiaru i przepustowości wiadomości

Rozmiar każdej z opisanych wcześniej wiadomości jest zazwyczaj poniżej 1 KB. Nawet przy ekstremalnych obciążeniach sama usługa zapisywania zwrotnego haseł zużywa kilka kilobitów na sekundę przepustowości. Ponieważ każda wiadomość jest wysyłana w czasie rzeczywistym, tylko wtedy, gdy jest to wymagane przez operację aktualizacji hasła i ponieważ rozmiar wiadomości jest tak mały, użycie przepustowości funkcji stornia jest zbyt małe, aby mieć wymierny wpływ.

## <a name="supported-writeback-operations"></a>Obsługiwane operacje stornia zwrotnego

Hasła są zapisywane we wszystkich następujących sytuacjach:

* **Obsługiwane operacje użytkowników końcowych**
   * Każda samoobsługa użytkownika końcowego dobrowolna zmiana hasła operacji.
   * Wszystkie samoobsługowe działanie użytkownika końcowego wymuszą zmianę hasła, na przykład wygasanie hasła.
   * Wszelkie hasło samoobsługowe użytkownika końcowego, które pochodzi z [portalu resetowania hasła](https://passwordreset.microsoftonline.com).

* **Obsługiwane operacje administratora**
   * Każdy administrator samoobsługi dobrowolnej zmiany hasła operacji.
   * Każdy administrator samoobsługi wymusza zmianę hasła, na przykład wygasanie hasła.
   * Wszelkie administratora samoobsługowego resetowania hasła, który pochodzi z [portalu resetowania hasła](https://passwordreset.microsoftonline.com).
   * Wszelkie hasło użytkownika końcowego zainicjowane przez administratora są resetowane z [witryny Azure portal](https://portal.azure.com).

## <a name="unsupported-writeback-operations"></a>Nieobsługiwały operacje stornia

Hasła nie są zapisywane w żadnej z następujących sytuacji:

* **Nieobsługiwały operacje użytkownika końcowego**
   * Każdy użytkownik końcowy resetujący własne hasło przy użyciu programu PowerShell w wersji 1, 2 lub interfejsu API programu Microsoft Graph.
* **Nieobsługiwały operacje administratora**
   * Wszelkie hasło użytkownika końcowego zainicjowane przez administratora są resetowane z programu PowerShell w wersji 1, 2 lub interfejsu API programu Microsoft Graph.
   * Każde hasło użytkownika końcowego zainicjowane przez administratora z [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com).

> [!WARNING]
> Użycie pola wyboru "Użytkownik musi zmienić hasło przy następnym logowaniu" w lokalnych narzędziach administracyjnych usług AD DS, takich jak Użytkownicy i komputery usługi Active Directory lub Centrum administracyjne usługi Active Directory jest obsługiwane jako funkcja w wersji zapoznawczej usługi Azure AD Connect. Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z zapisywaniem samooczyszczeniu, wykonaj następujący samouczek:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie samoobsługowego resetowania hasła (SSPR)](tutorial-enable-writeback.md)
