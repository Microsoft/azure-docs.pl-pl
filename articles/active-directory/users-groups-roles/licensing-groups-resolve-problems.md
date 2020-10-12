---
title: Rozwiązywanie problemów z przypisaniem licencji grupy — Azure Active Directory | Microsoft Docs
description: Jak identyfikować i rozwiązywać problemy z przypisaniem licencji podczas korzystania z licencjonowania opartego na grupach Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: how-to
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7006ecd760a886fdebe50becb2ab366ff79cf057
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319813"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identyfikowanie i rozwiązywanie problemów z przypisaniem licencji dla grupy w Azure Active Directory

Licencjonowanie oparte na grupach w Azure Active Directory (Azure AD) wprowadza koncepcję użytkowników w stanie błąd licencjonowania. W tym artykule wyjaśniono przyczyny, dla których użytkownicy mogą zakończyć w tym stanie.

Po przypisaniu licencji bezpośrednio do poszczególnych użytkowników bez korzystania z licencjonowania opartego na grupach operacja przypisania może zakończyć się niepowodzeniem. Na przykład po wykonaniu polecenia cmdlet programu PowerShell `Set-MsolUserLicense` w systemie użytkownika polecenie cmdlet może zakończyć się niepowodzeniem z wielu powodów związanych z logiką biznesową. Na przykład może być niewystarczająca liczba licencji lub występuje konflikt między dwoma planami usług, których nie można przypisać w tym samym czasie. Problem jest natychmiast raportowany do Ciebie.

W przypadku korzystania z licencjonowania opartego na grupach te same błędy mogą wystąpić, ale występują w tle, podczas gdy usługa Azure AD przypisuje licencje. Z tego powodu błędy nie mogą być od razu przekazane. Zamiast tego są one rejestrowane w obiekcie użytkownika, a następnie raportowane za pośrednictwem portalu administracyjnego. Oryginalny cel licencji użytkownika nigdy nie jest tracony, ale jest rejestrowany w stanie błędu w celu przyszłego zbadania i rozwiązania problemu.

## <a name="find-license-assignment-errors"></a>Znajdowanie błędów przypisywania licencji

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Aby znaleźć użytkowników w stanie błędu w grupie

1. Otwórz grupę na stronie Przegląd i wybierz pozycję **licencje**. Zostanie wyświetlone powiadomienie, jeśli wystąpią jakieś użytkownicy w stanie błędu.

   ![Komunikat dotyczący grup i powiadomień o błędzie](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Wybierz powiadomienie, aby otworzyć listę wszystkich użytkowników, których to dotyczy. Możesz wybrać każdego użytkownika osobno, aby zobaczyć więcej szczegółów.

   ![Lista użytkowników w stanie błędu licencjonowania grupy](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Aby znaleźć wszystkie grupy zawierające co najmniej jeden błąd, w bloku **Azure Active Directory** wybierz pozycję **licencje**, a następnie wybierz pozycję **Przegląd**. Pole informacji jest wyświetlane, gdy grupy wymagają Twojej uwagi.

   ![Omówienie i informacje o grupach w stanie błąd](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Zaznacz pole, aby wyświetlić listę wszystkich grup z błędami. Aby uzyskać więcej informacji, możesz wybrać każdą grupę.

   ![Omówienie i lista grup z błędami](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

Poniższe sekcje zawierają opis każdego potencjalnego problemu i sposobu jego rozwiązania.

## <a name="not-enough-licenses"></a>Za mało licencji

**Problem:** Za mało dostępnych licencji dla jednego z produktów określonych w grupie. Musisz zakupić więcej licencji dla produktu lub zwolnić niewykorzystane licencje od innych użytkowników lub grup.

Aby sprawdzić, ile licencji jest dostępnych, przejdź do pozycji **Azure Active Directory**  >  **licencje**  >  **wszystkie produkty**.

Aby sprawdzić, którzy użytkownicy i które grupy korzystają z licencji, wybierz produkt. W obszarze **Licencjonowani użytkownicy**zobaczysz listę wszystkich użytkowników, którzy mają przypisane licencje bezpośrednio lub za pośrednictwem co najmniej jednej grupy. W obszarze **grupy licencjonowane**zostaną wyświetlone wszystkie grupy, które mają przypisane te produkty.

Program **PowerShell:** Polecenia cmdlet programu PowerShell zgłaszają ten błąd jako _CountViolation_.

## <a name="conflicting-service-plans"></a>Sprzeczne plany usługi

**Problem:** Jeden z produktów określonych w grupie zawiera plan usługi, który powoduje konflikt z innym planem usługi, który został już przypisany do użytkownika za pośrednictwem innego produktu. Niektóre plany usług są konfigurowane w taki sposób, że nie mogą być przypisane do tego samego użytkownika, który jest powiązany z innym planem usługi.

Rozważmy następujący przykład. Użytkownik ma licencję na pakiet Office 365 Enterprise *E1* przypisaną bezpośrednio, ze wszystkimi włączonymi planami. Użytkownik został dodany do grupy, która ma przypisany pakiet Office 365 Enterprise *E3* . Produkt E3 zawiera plany usług, które nie mogą nakładać się na plany, które są uwzględnione w E1, więc przypisanie licencji grupy kończy się niepowodzeniem z powodu błędu "plany usługi w konflikcie". W tym przykładzie plany usługi powodujące konflikt są następujące:

- SharePoint Online (plan 2) powoduje konflikt z usługą SharePoint Online (plan 1).
- Usługa Exchange Online (plan 2) powoduje konflikt z usługą Exchange Online (plan 1).

Aby rozwiązać ten konflikt, należy wyłączyć dwa plany. Można wyłączyć licencję E1, która jest bezpośrednio przypisana do użytkownika. Lub należy zmodyfikować całe przypisanie licencji grupy i wyłączyć plany w licencji E3. Alternatywnie, możesz zdecydować o usunięciu licencji E1 od użytkownika, jeśli jest nadmiarowy w kontekście licencji E3.

Decyzja o sposobie rozwiązania sprzecznych licencji produktu zawsze należy do administratora. Usługa Azure AD nie rozwiązuje automatycznie konfliktów licencji.

Program **PowerShell:** Polecenia cmdlet programu PowerShell zgłaszają ten błąd jako _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Inne produkty są zależne od tej licencji

**Problem:** Jeden z produktów określonych w grupie zawiera plan usługi, który musi być włączony dla innego planu usługi, w innym produkcie. Ten błąd występuje, gdy usługa Azure AD próbuje usunąć bazowy plan usługi. Na przykład może się to zdarzyć po usunięciu użytkownika z grupy.

Aby rozwiązać ten problem, należy się upewnić, że wymagany plan jest nadal przypisany do użytkowników za pomocą innej metody lub że usługi zależne są wyłączone dla tych użytkowników. Po wykonaniu tej czynności można prawidłowo usunąć licencję grupy z tych użytkowników.

Program **PowerShell:** Polecenia cmdlet programu PowerShell zgłaszają ten błąd jako _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Lokalizacja użycia nie jest dozwolona

**Problem:** Niektóre usługi firmy Microsoft nie są dostępne we wszystkich lokalizacjach ze względu na lokalne przepisy i przepisy. Aby można było przypisać licencję do użytkownika, należy określić właściwość **Lokalizacja użycia** dla użytkownika. Lokalizację można określić w **User**  >  **Profile**  >  sekcji**Edycja** profilu użytkownika w Azure Portal.

Gdy usługa Azure AD podejmie próbę przypisania licencji grupy do użytkownika, którego lokalizacja użycia nie jest obsługiwana, kończy się niepowodzeniem i rejestruje błąd użytkownika.

Aby rozwiązać ten problem, Usuń użytkowników z nieobsługiwanych lokalizacji z grupy licencjonowanej. Alternatywnie, jeśli bieżące wartości lokalizacji użycia nie reprezentują rzeczywistej lokalizacji użytkownika, można je zmodyfikować w taki sposób, aby licencje były prawidłowo przypisane po następnym (jeśli jest obsługiwana Nowa lokalizacja).

Program **PowerShell:** Polecenia cmdlet programu PowerShell zgłaszają ten błąd jako _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Gdy usługa Azure AD przypisuje licencje grupowe, wszyscy użytkownicy bez określonej lokalizacji użycia dziedziczą lokalizację katalogu. Zaleca się, aby administratorzy ustawili odpowiednie wartości lokalizacji użycia na użytkownikach przed użyciem licencjonowania opartego na grupach, aby zapewnić zgodność z lokalnymi przepisami i przepisami.

## <a name="duplicate-proxy-addresses"></a>Zduplikowane adresy serwerów proxy

Jeśli używasz usługi Exchange Online, niektórzy użytkownicy w organizacji mogą być niepoprawnie skonfigurowani z tą samą wartością adresu serwera proxy. Gdy Licencjonowanie oparte na grupach próbuje przypisać licencję do takiego użytkownika, nie powiedzie się i pokaże "adres serwera proxy jest już używany".

> [!TIP]
> Aby sprawdzić, czy istnieje zduplikowany adres serwera proxy, wykonaj następujące polecenie cmdlet programu PowerShell dla usługi Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Aby uzyskać więcej informacji o tym problemie, zobacz ["adres serwera proxy jest już używany" w usłudze Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Artykuł zawiera również informacje dotyczące [sposobu nawiązywania połączenia z usługą Exchange Online przy użyciu zdalnego programu PowerShell](/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps).

Po rozwiązaniu problemów z adresem serwera proxy dla użytkowników, których dotyczy problem, należy wymusić przeprowadzenie przetwarzania licencji w grupie, aby upewnić się, że licencje można teraz zastosować.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Zmiana atrybutów poczty i ProxyAddresses usługi Azure AD

**Problem:** Podczas aktualizowania przypisania licencji dla użytkownika lub grupy, może się okazać, że atrybut poczta i ProxyAddresses usługi Azure AD niektórych użytkowników jest zmieniany.

Aktualizacja przypisania licencji dla użytkownika powoduje, że Obliczanie adresu serwera proxy jest wyzwalane, co może spowodować zmianę atrybutów użytkownika. Aby zrozumieć dokładną przyczynę zmiany i rozwiązać problem, zobacz ten artykuł dotyczący [sposobu wypełnienia atrybutu proxyAddresses w usłudze Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException w dziennikach inspekcji

**Problem:** Użytkownik ma LicenseAssignmentAttributeConcurrencyException do przypisania licencji w dziennikach inspekcji.
Gdy Licencjonowanie oparte na grupach próbuje przetworzyć współbieżne przypisanie licencji tej samej licencji do użytkownika, ten wyjątek jest rejestrowany na użytkowniku. Zwykle dzieje się tak, gdy użytkownik jest członkiem więcej niż jednej grupy z tą samą przypisaną licencją. Usługa Azure AD podejmie ponowną próbę przetworzenia licencji użytkownika i rozwiąże ten problem. Klient nie wymaga żadnych działań w celu rozwiązania tego problemu.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Do grupy przypisano więcej niż jedną licencję produktu

Do grupy można przypisać więcej niż jedną licencję produktu. Na przykład możesz przypisać do grupy pakiet Office 365 Enterprise E3 i Enterprise Mobility + Security, aby łatwo włączyć wszystkie dołączone usługi dla użytkowników.

Usługa Azure AD próbuje przypisać wszystkie licencje, które są określone w grupie dla każdego użytkownika. Jeśli usługa Azure AD nie może przypisać jednego z produktów ze względu na problemy z logiką biznesową, nie przypisze innych licencji w grupie. Przykładem jest brak wystarczającej liczby licencji lub w przypadku konfliktów z innymi usługami, które są włączone dla użytkownika.

Zobaczysz użytkowników, którzy nie mogli uzyskać przypisanej i sprawdź, które produkty mają wpływ na ten problem.

## <a name="when-a-licensed-group-is-deleted"></a>Po usunięciu licencjonowanej grupy

Przed usunięciem grupy należy usunąć wszystkie przypisane do niej licencje. Jednak usunięcie licencji ze wszystkich użytkowników w grupie może zająć trochę czasu. Podczas usuwania przypisań licencji z grupy mogą wystąpić błędy, jeśli użytkownik ma przypisaną licencję zależną lub występuje problem z konfliktem adresu serwera proxy, który uniemożliwia usunięcie licencji. Jeśli użytkownik ma licencję zależną od licencji, która jest usuwana ze względu na usunięcie grupy, przypisanie licencji do użytkownika jest konwertowane z dziedziczone do bezpośredniego.

Rozważmy na przykład grupę, która ma pakiet Office 365 E3/E5 przypisany z włączonym planem usługi Skype dla firm. Załóżmy również, że kilku członków grupy ma przypisane bezpośrednio licencje na konferencje audio. Po usunięciu grupy Licencjonowanie oparte na grupach będzie podejmować próby usunięcia pakietu Office 365 E3/E5 ze wszystkich użytkowników. Ponieważ konferencje audio są zależne od programu Skype dla firm, dla wszystkich użytkowników z przypisaną konferencją audio, Licencjonowanie oparte na grupach konwertuje Licencje pakietu Office 365 E3/E5 na bezpośrednie przypisanie licencji.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Zarządzanie licencjami na produkty z wymaganiami wstępnymi

Niektóre z dostępnych produktów online firmy Microsoft to *Dodatki*. Dodatki wymagają włączenia planu usługi wymaganego wstępnie dla użytkownika lub grupy, zanim będzie można przypisać licencję. W przypadku licencjonowania opartego na grupach system wymaga, aby w tej samej grupie były obecne zarówno warunki wstępne, jak i dodatkowe. Jest to wykonywane w celu zapewnienia, że wszyscy użytkownicy, którzy są dodawani do grupy, mogą uzyskać w pełni pracujący produkt. Rozważmy następujący przykład:

Analiza w miejscu pracy firmy Microsoft jest produktem dodatkowym. Zawiera jeden plan usługi o tej samej nazwie. Ten plan usługi można przypisać tylko do użytkownika lub grupy, tylko po przypisaniu jednego z następujących warunków wstępnych:

- Exchange Online (plan 1)
- Exchange Online (plan 2)

Jeśli spróbujesz przypisać ten produkt do grupy, Portal zwróci komunikat z powiadomieniem. W przypadku wybrania szczegółów elementu zostanie wyświetlony następujący komunikat o błędzie:

  "Operacja licencji nie powiodła się. Przed dodaniem lub usunięciem usługi zależnej upewnij się, że grupa ma wymagane usługi. **Aby można było włączyć usługę Exchange Online (plan 2), usługa Microsoft miejsce w miejscu pracy.**

Aby przypisać tę licencję dodatku do grupy, musimy upewnić się, że grupa zawiera również plan usługi wymaganie wstępne. Na przykład firma Microsoft może zaktualizować istniejącą grupę, która zawiera już pełny produkt pakietu Office 365 E3, a następnie dodać do niego produkt dodatkowy.

Istnieje również możliwość utworzenia grupy autonomicznej zawierającej tylko minimalne wymagane produkty, aby można było korzystać z dodatku. Może służyć do licencjonowania tylko wybranych użytkowników dodatku. Na podstawie poprzedniego przykładu do tej samej grupy należy przypisać następujące produkty:

- Pakiet Office 365 Enterprise E3 z włączonym planem usługi Exchange Online (plan 2)
- Microsoft Workplace Analytics

Od teraz wszyscy użytkownicy dodani do tej grupy wykorzystują jedną licencję produktu E3 i jedną licencję produktu do analizy w miejscu pracy. W tym samym czasie Ci użytkownicy mogą należeć do innej grupy, która daje im pełny produkt E3 i nadal używa tylko jednej licencji dla tego produktu.

> [!TIP]
> Dla każdego wymaganego planu usługi można utworzyć wiele grup. Jeśli na przykład w przypadku użytkowników korzystasz z pakietu Office 365 Enterprise E1 i pakietu Office 365 Enterprise E3, możesz utworzyć dwie grupy, aby uzyskać licencję na analizę w miejscu pracy firmy Microsoft: jedną, która korzysta z E1 jako warunek wstępny, a druga, która używa E3. Pozwala to na dystrybucję dodatku do użytkowników E1 i E3 bez używania dodatkowych licencji.

## <a name="force-group-license-processing-to-resolve-errors"></a>Wymuś przetwarzanie licencji grupy w celu rozwiązania błędów

W zależności od tego, jakie czynności zostały podjęte w celu rozwiązania błędów, może być konieczne ręczne Wyzwól przetwarzanie grupy w celu zaktualizowania stanu użytkownika.

Jeśli na przykład zwolnisz Niektóre licencje, usuwając bezpośrednie przypisania licencji od użytkowników, musisz wyzwolić przetwarzanie grup, które wcześniej nie powiodło się w pełni licencjonować wszystkich członków. Aby ponownie przetworzyć grupę, przejdź do okienka Grupa, Otwórz pozycję **licencje**, a następnie wybierz przycisk **przetwórz** ponownie na pasku narzędzi.

## <a name="force-user-license-processing-to-resolve-errors"></a>Wymuś przetwarzanie licencji użytkownika w celu rozwiązania błędów

W zależności od tego, jakie czynności zostały podjęte w celu rozwiązania błędów, może być konieczne ręczne Wyzwól przetwarzanie użytkownika w celu zaktualizowania stanu użytkowników.

Na przykład po rozwiązaniu problemu ze zduplikowanym adresem serwera proxy dla użytkownika, którego dotyczy problem, należy wyzwolić przetwarzanie użytkownika. Aby ponownie przetworzyć użytkownika, przejdź do okienka użytkownika, Otwórz pozycję **licencje**, a następnie wybierz przycisk **przetwórz** ponownie na pasku narzędzi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innych scenariuszach związanych z zarządzaniem licencjami za pomocą grup, zobacz następujące tematy:

* [Co to jest Licencjonowanie oparte na grupach w Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](licensing-groups-migrate-users.md)
* [Jak migrować użytkowników między licencjami produktu przy użyciu licencjonowania opartego na grupach w programie Azure Active Directory](licensing-groups-change-licenses.md)
* [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-group-advanced.md)
* [Przykłady programu PowerShell dla licencjonowania opartego na grupach w Azure Active Directory](licensing-ps-examples.md)
