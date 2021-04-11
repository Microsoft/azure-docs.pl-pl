---
title: Problemy dotyczące diagnostyki pulpitu wirtualnego systemu Windows — Azure
description: Jak zdiagnozować problemy przy użyciu funkcji diagnostyki pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cf654f07cbbd729fac8feb9e2f8f7ceff471ae6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446999"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Identyfikowanie i diagnozowanie problemów z pulpitem wirtualnym systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Pulpit wirtualny systemu Windows oferuje funkcję diagnostyki, która umożliwia administratorowi identyfikowanie problemów za pomocą jednego interfejsu. Aby dowiedzieć się więcej o możliwościach diagnostycznych pulpitu wirtualnego systemu Windows, zobacz [używanie log Analytics do funkcji diagnostyki](diagnostics-log-analytics.md).

Połączenia, które nie docierają do pulpitu wirtualnego systemu Windows, nie będą wyświetlane w wynikach diagnostyki, ponieważ sama usługa roli diagnostyki jest częścią pulpitu wirtualnego systemu Windows. Problemy z połączeniem pulpitu wirtualnego systemu Windows mogą wystąpić, gdy użytkownik końcowy napotyka problemy z łącznością sieciową.

## <a name="common-error-scenarios"></a>Typowe scenariusze błędów

Tabela WVDErrors śledzi błędy dla wszystkich typów działań. Kolumna o nazwie "serviceerror" zawiera dodatkową flagę oznaczoną jako "true" lub "false". Ta flaga wskazuje, czy błąd jest związany z usługą.

* Jeśli wartość jest równa "true", zespół usługi mógł już zbadać ten problem. Jeśli ma to wpływ na środowisko użytkownika i występuje dużą liczbę razy, zalecamy przesłanie biletu pomocy technicznej dla pulpitu wirtualnego systemu Windows.
* Jeśli wartość jest równa "false", może to być błędna konfiguracja, którą można samodzielnie naprawić. Komunikat o błędzie może zawierać wskazówki dotyczące miejsca, w którym należy zacząć.

W poniższej tabeli wymieniono typowe błędy, w których administratorzy mogą pracować.

>[!NOTE]
>Ta lista zawiera najczęstsze błędy i jest aktualizowana regularnie erze. Aby mieć pewność, że masz najnowsze informacje, pamiętaj o tym, aby ponownie zaewidencjonować ten artykuł co najmniej raz w miesiącu.

## <a name="management-errors"></a>Błędy zarządzania

|Komunikat o błędzie|Sugerowane rozwiązanie|
|---|---|
|Nie można utworzyć klucza rejestracji |Nie można utworzyć tokenu rejestracji. Spróbuj utworzyć go ponownie przy krótszym czasie wygaśnięcia (od 1 godziny do 1 miesiąca). |
|Nie można usunąć klucza rejestracji|Nie można usunąć tokenu rejestracji. Spróbuj usunąć go ponownie. Jeśli nadal nie działa, użyj programu PowerShell, aby sprawdzić, czy token nadal istnieje. Jeśli tak jest, usuń ją za pomocą programu PowerShell.|
|Nie można zmienić trybu opróżniania hosta sesji |Nie można zmienić trybu opróżniania na maszynie wirtualnej. Sprawdź stan maszyny wirtualnej. Jeśli maszyna wirtualna jest niedostępna, nie można zmienić trybu opróżniania.|
|Nie można rozłączyć sesji użytkownika |Nie można rozłączyć użytkownika z maszyny wirtualnej. Sprawdź stan maszyny wirtualnej. Jeśli maszyna wirtualna jest niedostępna, sesja użytkownika nie może zostać odłączona. Jeśli maszyna wirtualna jest dostępna, sprawdź stan sesji użytkownika, aby sprawdzić, czy jest ona rozłączona. |
|Nie można wylogować wszystkich użytkowników w ramach hosta sesji |Nie można wylogować użytkowników z maszyny wirtualnej. Sprawdź stan maszyny wirtualnej. Jeśli nie jest dostępny, nie można wylogować użytkowników. Sprawdź stan sesji użytkownika, aby sprawdzić, czy są już wylogowane. Możesz wymusić wylogowanie za pomocą programu PowerShell. |
|Nie można anulować przypisania użytkownika z grupy aplikacji|Nie można anulować publikacji grupy aplikacji dla użytkownika. Sprawdź, czy użytkownik jest dostępny w usłudze Azure AD. Sprawdź, czy użytkownik jest członkiem grupy użytkowników, w której jest publikowana Grupa aplikacji. |
|Wystąpił błąd podczas pobierania dostępnych lokalizacji |Sprawdź lokalizację maszyny wirtualnej używaną w Kreatorze tworzenia puli hostów. Jeśli obraz nie jest dostępny w tej lokalizacji, Dodaj do niej obraz lub wybierz inną lokalizację maszyny wirtualnej. |

### <a name="connection-error-codes"></a>Kody błędów połączeń

|Kod liczbowy|Kod błędu|Sugerowane rozwiązanie|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Host sesji nie został poprawnie przyłączony do Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Połączenia nie powiodły się, ponieważ Host sesji jest niedostępny. Sprawdź kondycję hosta sesji.|
|-2146233088|ConnectionFailedClientDisconnect|Jeśli ten błąd występuje często, upewnij się, że komputer użytkownika jest połączony z siecią.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Sesja, z którą użytkownik hosta próbował się połączyć, nie jest w dobrej kondycji. Debuguj maszynę wirtualną.|
|-2146233088|ConnectionFailedUserNotAuthorized|Użytkownik nie ma uprawnień dostępu do opublikowanej aplikacji lub pulpitu. Ten błąd może pojawić się po usunięciu opublikowanych zasobów przez administratora. Poproszenie użytkownika o odświeżenie źródła danych w aplikacji Pulpit zdalny.|
|2|FileNotFound|Aplikacja, do której próbowano uzyskać dostęp, jest nieprawidłowo zainstalowana lub ustawiona na niepoprawną ścieżkę.<br><br>Podczas publikowania nowych aplikacji, gdy użytkownik ma aktywną sesję, użytkownik nie będzie mógł uzyskać dostępu do tej aplikacji. Aby użytkownik mógł uzyskać dostęp do aplikacji, należy zamknąć i ponownie uruchomić sesję. |
|3|InvalidCredentials|Wprowadzona nazwa użytkownika lub hasło nie są zgodne z istniejącymi nazwami użytkowników ani hasłami. Przejrzyj poświadczenia pod kątem pisowni i spróbuj ponownie.|
|8|ConnectionBroken|Połączenie między klientem a bramą lub serwerem zostało usunięte. Nie trzeba wykonywać żadnych czynności, chyba że wystąpi nieoczekiwany.|
|14|UnexpectedNetworkDisconnect|Połączenie z siecią zostało usunięte. Poproszenie użytkownika o ponowne nawiązanie połączenia.|
|24|ReverseConnectFailed|Maszyna wirtualna hosta nie ma bezpośredniego wglądu w szczegółowe informacje z bramą usług pulpitu zdalnego. Upewnij się, że można rozpoznać adres IP bramy.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Błąd: nie można dodać przypisań użytkowników do grupy aplikacji

Po przypisaniu użytkownika do grupy aplikacji Azure Portal zostanie wyświetlone ostrzeżenie informujące o tym, że "kończenie sesji" lub "napotkano problemy z uwierzytelnianiem Microsoft_Azure_WVD". Strona przypisanie nie zostanie załadowana, a następnie strony zatrzymają ładowanie w całej Azure Portal (na przykład Azure Monitor, Log Analytics, Service Health itd.).

**Przyczyna:** Wystąpił problem z zasadami dostępu warunkowego. Azure Portal próbuje uzyskać token dla Microsoft Graph, który jest zależny od usługi SharePoint Online. Klient ma zasady dostępu warunkowego o nazwie "Microsoft Office 365 warunki korzystania z magazynu danych", które wymagają od użytkowników zaakceptowania warunków użytkowania w celu uzyskania dostępu do magazynu danych. Jednak nie zostały jeszcze zalogowane, więc Azure Portal nie może pobrać tokenu.

**Poprawka:** Przed zalogowaniem się do Azure Portal administrator najpierw musi zalogować się do programu SharePoint i zaakceptować warunki użytkowania. Po tym powinny być w stanie zalogować się do Azure Portal jak zwykle.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rolach w ramach pulpitu wirtualnego systemu Windows, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).

Aby wyświetlić listę dostępnych poleceń cmdlet programu PowerShell dla pulpitu wirtualnego systemu Windows, zobacz [Dokumentacja programu PowerShell](/powershell/windows-virtual-desktop/overview).
