---
title: Informacje o sposobie działania aprowizacji usługi Azure AD | Microsoft Docs
description: Informacje o działaniu aprowizacji usługi Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 07e3ff76886a935bf0b7b5c83052e0e7cd7a0429
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027428"
---
# <a name="how-provisioning-works"></a>Jak działa aprowizacja

Automatyczne Inicjowanie obsługi odnosi się do tworzenia tożsamości i ról użytkowników w aplikacjach w chmurze, do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne Inicjowanie obsługi obejmuje konserwację i usuwanie tożsamości użytkowników jako zmiany stanu lub ról. Przed rozpoczęciem wdrażania możesz przejrzeć ten artykuł, aby dowiedzieć się, jak działa usługa Azure AD i uzyskać zalecenia dotyczące konfiguracji. 

**Usługa Azure AD Provisioning** udostępnia użytkownikom SaaS aplikacje i inne systemy, łącząc się z systemem dla punktu końcowego interfejsu API zarządzania tożsamościami w wielu domenach (standard scim) 2,0 przez dostawcę aplikacji. Ten punkt końcowy Standard scim umożliwia usłudze Azure AD Programistyczne tworzenie, aktualizowanie i usuwanie użytkowników. W przypadku wybranych aplikacji usługa aprowizacji może również tworzyć, aktualizować i usuwać dodatkowe obiekty powiązane z tożsamościami, takie jak grupy i role. Kanał używany do aprowizacji między usługą Azure AD a aplikacją jest szyfrowany przy użyciu szyfrowania HTTPS TLS 1,2.


![Azure AD Provisioning Service ](./media/how-provisioning-works/provisioning0.PNG)
 *— rysunek 1: usługa Azure AD Provisioning*

![Przepływ pracy aprowizacji użytkowników wychodzących ](./media/how-provisioning-works/provisioning1.PNG)
 *rysunek 2: "wychodzące" przepływ pracy aprowizacji użytkowników z usługi Azure AD do popularnych aplikacji SaaS*

![Przepływ pracy aprowizacji przychodzących użytkowników ](./media/how-provisioning-works/provisioning2.PNG)
 *— rysunek 3: "przychodzący" przepływ pracy aprowizacji użytkowników z popularnych aplikacji do zarządzania stolicą Kadr (HCM) do Azure Active Directory i systemu Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Inicjowanie obsługi przy użyciu Standard scim 2,0

Usługa Azure AD Provisioning używa [protokołu standard scim 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) do automatycznej aprowizacji. Usługa nawiązuje połączenie z punktem końcowym usługi Standard scim dla aplikacji, a następnie używa schematu obiektów użytkownika Standard scim i interfejsów API REST w celu zautomatyzowania aprowizacji i anulowania aprowizacji użytkowników i grup. Łącznik aprowizacji oparty na Standard scim jest dostępny dla większości aplikacji w galerii usługi Azure AD. Podczas kompilowania aplikacji dla usługi Azure AD deweloperzy mogą używać interfejsu API zarządzania użytkownikami Standard scim 2,0 do kompilowania punktu końcowego Standard scim, który integruje usługę Azure AD na potrzeby aprowizacji. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie punktu końcowego Standard scim i Konfigurowanie aprowizacji użytkowników](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Aby zażądać automatycznego łącznika aprowizacji usługi Azure AD dla aplikacji, która aktualnie nie ma, Wypełnij [Azure Active Directory żądanie aplikacji](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autoryzacja

Poświadczenia są wymagane do nawiązania połączenia z interfejsem API zarządzania użytkownikami aplikacji za pomocą usługi Azure AD. Podczas konfigurowania automatycznej aprowizacji użytkowników dla aplikacji należy wprowadzić prawidłowe poświadczenia. W przypadku aplikacji galerii można znaleźć typy poświadczeń i wymagania dotyczące aplikacji, odwołując się do samouczka dotyczącego aplikacji. W przypadku aplikacji innych niż Galeria można zapoznać się z dokumentacją [Standard scim](./use-scim-to-provision-users-and-groups.md#authorization-for-provisioning-connectors-in-the-application-gallery) , aby poznać typy poświadczeń i wymagania. W Azure Portal będziesz mieć możliwość przetestowania poświadczeń, ponieważ usługa Azure AD podejmie próbę nawiązania połączenia z aplikacją aprowizacji aplikacji przy użyciu podanych poświadczeń.

## <a name="mapping-attributes"></a>Mapowanie atrybutów

Po włączeniu aprowizacji użytkowników dla aplikacji SaaS innej firmy Azure Portal kontroluje wartości atrybutów przy użyciu mapowań atrybutów. Mapowania określają atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową, gdy konta użytkowników są inicjowane lub aktualizowane.

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowań atrybutów między obiektami użytkowników usługi Azure AD i obiektami użytkownika aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów razem z użytkownikami, takimi jak grupy.

Podczas konfigurowania aprowizacji należy przejrzeć i skonfigurować mapowania atrybutów i przepływy pracy, które definiują, które właściwości użytkownika (lub grupy) będą przepływać z usługi Azure AD do aplikacji. Przejrzyj i skonfiguruj pasującą Właściwość (**Dopasuj obiekty korzystające z tego atrybutu**), która jest używana do unikatowego identyfikowania i dopasowywania użytkowników/grup między tymi dwoma systemami.

Domyślne mapowania atrybutów można dostosować zgodnie z potrzebami biznesowymi. W związku z tym można zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów. Aby uzyskać szczegółowe informacje, zobacz [Dostosowywanie mapowania atrybutu aprowizacji użytkowników dla aplikacji SaaS](./customize-application-attributes.md).

Podczas konfigurowania aprowizacji do aplikacji SaaS, jeden z typów mapowań atrybutów, które można określić, jest mapowanie wyrażenia. Dla tych mapowań należy napisać wyrażenie podobne do skryptu, które pozwala na Przekształcanie danych użytkowników w formaty, które są bardziej akceptowalne dla aplikacji SaaS. Aby uzyskać szczegółowe informacje, zobacz [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Zakresów 
### <a name="assignment-based-scoping"></a>Określanie zakresu przydziału

W przypadku inicjowania obsługi ruchu wychodzącego z usługi Azure AD do aplikacji SaaS poleganie na [przypisaniach użytkowników lub grup](../manage-apps/assign-user-or-group-access-portal.md) jest najczęstszym sposobem ustalenia, którzy użytkownicy znajdują się w zakresie aprowizacji. Ponieważ przypisania użytkownika są również używane do włączania logowania jednokrotnego, ta sama metoda może służyć do zarządzania dostępem i aprowizacji. Określanie zakresu opartego na przypisaniach nie ma zastosowania do scenariuszy inicjowania obsługi ruchu przychodzącego, takich jak Workday i SuccessFactors.

* **Grupowania.** Za pomocą planu licencjonowania Azure AD — wersja Premium można używać grup do przypisywania dostępu do aplikacji SaaS. Następnie, gdy zakres aprowizacji jest ustawiony do **synchronizowania tylko przypisanych użytkowników i grup**, usługa aprowizacji usługi Azure AD będzie inicjować lub cofać obsługę administracyjną użytkowników w zależności od tego, czy są członkami grupy przypisanej do aplikacji. Sam obiekt grupy nie jest inicjowany, chyba że aplikacja obsługuje obiekty grup. Upewnij się, że grupy przypisane do aplikacji mają właściwość "SecurityEnabled" ustawioną na wartość "true".

* **Grupy dynamiczne.** Usługa aprowizacji użytkowników w usłudze Azure AD może odczytywać i inicjować użytkowników w [grupach dynamicznych](../enterprise-users/groups-create-rule.md). Pamiętaj o następujących zastrzeżeniach i zaleceniach:

  * Dynamiczne grupy mogą mieć wpływ na wydajność kompleksowego udostępniania z usługi Azure AD do aplikacji SaaS.

  * Jak szybki dostęp użytkownika do grupy dynamicznej jest inicjowany lub dezaktywowany w aplikacji SaaS, zależy od tego, jak szybko Grupa dynamiczna może oszacować zmiany członkostwa. Aby uzyskać informacje na temat sprawdzania stanu przetwarzania grupy dynamicznej, zobacz [Sprawdzanie stanu przetwarzania dla reguły członkostwa](../enterprise-users/groups-create-rule.md).

  * Gdy użytkownik utraci członkostwo w grupie dynamicznej, jest traktowany jako zdarzenie anulowania aprowizacji. Ten scenariusz należy wziąć pod uwagę podczas tworzenia reguł dla grup dynamicznych.

* **Grupy zagnieżdżone.** Usługa aprowizacji użytkowników w usłudze Azure AD nie może odczytać ani zainicjować obsługi użytkowników w grupach zagnieżdżonych. Usługa może odczytywać i inicjować tylko użytkowników, którzy są bezpośrednimi członkami jawnie przypisanej grupy. To ograniczenie "przypisań opartych na grupach do aplikacji" wpływa również na logowanie jednokrotne (zobacz [używanie grupy do zarządzania dostępem do aplikacji SaaS](../enterprise-users/groups-saasapps.md)). Zamiast tego należy bezpośrednio przypisywać lub w inny sposób określić [zakres w](define-conditional-rules-for-provisioning-user-accounts.md) grupach, które zawierają użytkowników, którzy muszą zostać zaobsługiwani.

### <a name="attribute-based-scoping"></a>Określanie zakresu na podstawie atrybutów 

Filtry zakresu umożliwiają definiowanie reguł opartych na atrybutach, które określają, którzy użytkownicy są obsługiwani do aplikacji. Ta metoda jest często używana do inicjowania obsługi ruchu przychodzącego z aplikacji HCM w usłudze Azure AD i Active Directory. Filtry zakresu są konfigurowane jako część mapowań atrybutów dla każdego łącznika aprowizacji użytkowników usługi Azure AD. Aby uzyskać szczegółowe informacje na temat konfigurowania filtrów określania zakresu atrybutów, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Użytkownicy B2B (gość)

Możliwe jest użycie usługi aprowizacji użytkowników w usłudze Azure AD w celu udostępnienia użytkownikom B2B (lub gościa) w usłudze Azure AD w celu SaaS aplikacji. Jednak aby użytkownicy B2B mogli logować się do aplikacji SaaS przy użyciu usługi Azure AD, aplikacja SaaS musi mieć skonfigurowaną funkcję logowania jednokrotnego opartego na protokole SAML. Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji SaaS do obsługi logowań użytkowników B2B, zobacz [Konfigurowanie aplikacji SaaS na potrzeby współpracy B2B](../external-identities/configure-saas-apps.md).

Należy pamiętać, że element userPrincipalName dla użytkownika-gościa jest często przechowywany jako "alias # EXT # @domain.com ". gdy element userPrincipalName zostanie uwzględniony w mapowaniu atrybutów jako atrybut źródłowy, #EXT # jest usuwany z elementu userPrincipalName. Jeśli potrzebujesz #EXT #, Zamień element userPrincipalName na originalUserPrincipalName jako atrybut source. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Cykle aprowizacji: początkowe i przyrostowe

Gdy usługa Azure AD jest systemem źródłowym, w usłudze aprowizacji są używane [zapytania różnicowe do śledzenia zmian w Microsoft Graph danych](/graph/delta-query-overview) w celu monitorowania użytkowników i grup. Usługa aprowizacji uruchamia cykl początkowy względem systemu źródłowego i systemu docelowego, a następnie okresowe cykle przyrostowe.

### <a name="initial-cycle"></a>Cykl początkowy

Po uruchomieniu usługi aprowizacji Pierwszy cykl będzie:

1. Wykonaj zapytania dla wszystkich użytkowników i grup z systemu źródłowego, pobierając wszystkie atrybuty zdefiniowane w [mapowaniu atrybutów](customize-application-attributes.md).

2. Filtrowanie zwracanych użytkowników i grup przy użyciu dowolnych skonfigurowanych [przypisań](../manage-apps/assign-user-or-group-access-portal.md) lub [filtrów określania zakresów opartych na atrybutach](define-conditional-rules-for-provisioning-user-accounts.md).

3. Gdy użytkownik jest przypisany lub w zakresie aprowizacji, usługa wysyła zapytanie do systemu docelowego dla pasującego użytkownika przy użyciu określonych [pasujących atrybutów](customize-application-attributes.md#understanding-attribute-mapping-properties). Przykład: Jeśli nazwa userPrincipal w systemie źródłowym ma pasujący atrybut i jest mapowana na nazwę użytkownika w systemie docelowym, usługa aprowizacji wysyła zapytanie do systemu docelowego o nazwy użytkowników, które pasują do wartości nazw userPrincipal w systemie źródłowym.

4. Jeśli w systemie docelowym nie znaleziono pasującego użytkownika, jest on tworzony przy użyciu atrybutów zwróconych z systemu źródłowego. Po utworzeniu konta użytkownika usługa aprowizacji wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika. Ten identyfikator służy do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.

5. Jeśli zostanie znaleziony pasujący użytkownik, jest on aktualizowany przy użyciu atrybutów dostarczonych przez system źródłowy. Po dopasowaniu konta użytkownika usługa aprowizacji wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika. Ten identyfikator służy do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.

6. Jeśli mapowania atrybutu zawierają atrybuty "Reference", usługa wykonuje dodatkowe aktualizacje w systemie docelowym w celu utworzenia i połączenia obiektów, do których się odwołuje. Na przykład użytkownik może mieć atrybut "Manager" w systemie docelowym, który jest połączony z innym użytkownikiem utworzonym w systemie docelowym.

7. Utrwalanie znaku wodnego na końcu początkowego cyklu, który zapewnia punkt początkowy dla późniejszych cykli przyrostowych.

Niektóre aplikacje, takie jak usługi ServiceNow, G Suite i Box, obsługują nie tylko użytkowników aprowizacji, ale również aprowizacji grup i ich członków. W takich przypadkach, jeśli w [mapowaniu](customize-application-attributes.md)włączono obsługę grupy, usługa aprowizacji zsynchronizuje użytkowników i grupy, a następnie zsynchronizuje członkostwo w grupie.

### <a name="incremental-cycles"></a>Cykle przyrostowe

Po początkowym cyklu wszystkie pozostałe cykle będą:

1. Zbadaj system źródłowy dla wszystkich użytkowników i grup, które zostały zaktualizowane od momentu zapisania ostatniego znaku wodnego.

2. Filtrowanie zwracanych użytkowników i grup przy użyciu dowolnych skonfigurowanych [przypisań](../manage-apps/assign-user-or-group-access-portal.md) lub [filtrów określania zakresów opartych na atrybutach](define-conditional-rules-for-provisioning-user-accounts.md).

3. Gdy użytkownik jest przypisany lub w zakresie aprowizacji, usługa wysyła zapytanie do systemu docelowego dla pasującego użytkownika przy użyciu określonych [pasujących atrybutów](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Jeśli w systemie docelowym nie znaleziono pasującego użytkownika, jest on tworzony przy użyciu atrybutów zwróconych z systemu źródłowego. Po utworzeniu konta użytkownika usługa aprowizacji wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika. Ten identyfikator służy do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.

5. Jeśli zostanie znaleziony pasujący użytkownik, jest on aktualizowany przy użyciu atrybutów dostarczonych przez system źródłowy. Jeśli jest to nowo przypisane konto, które jest dopasowane, usługa aprowizacji wykrywa i buforuje identyfikator systemu docelowego dla nowego użytkownika. Ten identyfikator służy do uruchamiania wszystkich przyszłych operacji na tym użytkowniku.

6. Jeśli mapowania atrybutu zawierają atrybuty "Reference", usługa wykonuje dodatkowe aktualizacje w systemie docelowym w celu utworzenia i połączenia obiektów, do których się odwołuje. Na przykład użytkownik może mieć atrybut "Manager" w systemie docelowym, który jest połączony z innym użytkownikiem utworzonym w systemie docelowym.

7. Jeśli użytkownik, który był wcześniej objęty zakresem aprowizacji, zostanie usunięty z zakresu, łącznie z cofniętym przypisaniem, usługa wyłącza użytkownika w systemie docelowym za pośrednictwem aktualizacji.

8. Jeśli użytkownik, który był wcześniej objęty zakresem aprowizacji, jest wyłączony lub nietrwały usunięty w systemie źródłowym, usługa wyłącza użytkownika w systemie docelowym za pośrednictwem aktualizacji.

9. Jeśli użytkownik, który był wcześniej objęty zakresem aprowizacji, został trwale usunięty w systemie źródłowym, usługa usunie użytkownika w systemie docelowym. W usłudze Azure AD użytkownicy są trwale usunięci przez 30 dni po ich usunięciu nietrwałego.

10. Utrwalaj nowy znak wodny na końcu przyrostowego cyklu, który zapewnia punkt początkowy dla późniejszych cykli przyrostowych.

> [!NOTE]
> Opcjonalnie można wyłączyć operacje **tworzenia**, **aktualizowania** lub **usuwania** przy użyciu pól wyboru **Akcje obiektu docelowego** w sekcji [mapowania](customize-application-attributes.md) . Logika, w której należy wyłączyć użytkownika podczas aktualizacji, jest również kontrolowana za pośrednictwem mapowania atrybutów z pola, takiego jak "accountEnabled".

Usługa aprowizacji nadal uruchamia cykliczne cykle przyrostowe, w odstępach czasu zdefiniowanych w [samouczku specyficznym dla każdej aplikacji](../saas-apps/tutorial-list.md). Cykle przyrostowe są kontynuowane do momentu wystąpienia jednego z następujących zdarzeń:

- Usługa została zatrzymana ręcznie przy użyciu Azure Portal lub przy użyciu odpowiedniego polecenia API Microsoft Graph.
- Nowy cykl początkowy jest wyzwalany przy użyciu opcji **Wyczyść stan i uruchom ponownie** w Azure Portal lub przy użyciu odpowiedniego polecenia API Microsoft Graph. Ta akcja Czyści wszystkie przechowywane znaki wodne i powoduje ponowną ocenę wszystkich obiektów źródłowych.
- Nowy początkowy cykl jest wyzwalany ze względu na zmianę mapowań atrybutów lub filtrów określania zakresu. Ta akcja Czyści również wszystkie przechowywane znaki wodne i powoduje ponowne obliczenie wszystkich obiektów źródłowych.
- Proces aprowizacji jest kierowany do kwarantanny (patrz poniżej) z powodu wysokiego współczynnika błędów i pozostaje w kwarantannie przez ponad cztery tygodnie. W tym zdarzeniu usługa zostanie automatycznie wyłączona.

### <a name="errors-and-retries"></a>Błędy i ponowne próby

Jeśli błąd w systemie docelowym uniemożliwia dodanie, zaktualizowanie lub usunięcie poszczególnych użytkowników w systemie docelowym, operacja zostanie ponowiona w następnym cyklu synchronizacji. Jeśli użytkownik będzie nadal kończyć się niepowodzeniem, ponowne próby rozpoczną się z ograniczoną częstotliwością, stopniowo skalowanie z powrotem do zaledwie jednej próby dziennie. Aby rozwiązać ten problem, Administratorzy muszą sprawdzić [dzienniki aprowizacji](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) , aby określić główną przyczynę i podjąć odpowiednie działania. Typowe błędy mogą obejmować:

- Użytkownicy nie mający atrybutu wypełnionego w systemie źródłowym, który jest wymagany w systemie docelowym
- Użytkownicy mający wartość atrybutu w systemie źródłowym, dla którego istnieje ograniczenie UNIQUE w systemie docelowym, a ta sama wartość jest obecna w innym rekordzie użytkownika.

Usuń te błędy, dostosowując wartości atrybutów dla danego użytkownika w systemie źródłowym lub modyfikując mapowania atrybutów, aby nie powodowały konfliktów.

### <a name="quarantine"></a>Kwarantanna

Jeśli większość lub wszystkie wywołania, które są wykonywane względem systemu docelowego, są spójne niepowodzeniem z powodu błędu (na przykład nieprawidłowe poświadczenia administratora) zadanie aprowizacji przejdzie do stanu "Kwarantanna". Ten stan jest wskazany w raporcie dotyczącym [podsumowania aprowizacji](./check-status-user-account-provisioning.md) i za pośrednictwem poczty e-mail, jeśli powiadomienia e-mail zostały skonfigurowane w Azure Portal.

W przypadku kwarantanny Częstotliwość cykli przyrostowych jest stopniowo zmniejszana do raz dziennie.

Zadanie aprowizacji kończy kwarantannę po usunięciu wszystkich błędów powodujących problemy, gdy zostanie rozpoczęty następny cykl synchronizacji. Jeśli zadanie aprowizacji pozostaje w kwarantannie przez ponad cztery tygodnie, zadanie aprowizacji jest wyłączone. [Tutaj](./application-provisioning-quarantine-status.md)znajdziesz więcej informacji na temat stanu kwarantanny.

### <a name="how-long-provisioning-takes"></a>Jak długo trwa aprowizacja

Wydajność zależy od tego, czy zadanie aprowizacji ma uruchomiony początkowy cykl aprowizacji czy cykl przyrostowy. Aby uzyskać szczegółowe informacje o tym, jak długo trwa inicjowanie obsługi i jak monitorować stan usługi aprowizacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Jak stwierdzić, czy użytkownicy są prawidłowo aprowizacji

Wszystkie operacje wykonywane przez usługę aprowizacji użytkowników są rejestrowane w dziennikach aprowizacji usługi Azure AD [(wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Dzienniki obejmują wszystkie operacje odczytu i zapisu dokonane w systemach źródłowych i docelowych oraz dane użytkownika, które zostały odczytane lub zapisane podczas każdej operacji. Aby uzyskać informacje na temat sposobu odczytywania dzienników aprowizacji w Azure Portal, zobacz [Przewodnik po raportowaniu obsługi administracyjnej](./check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Cofnięcie aprowizacji
Usługa Azure AD Provisioning przechowuje systemy źródłowe i docelowe w synchronizacji przy użyciu kont bez obsługi administracyjnej, gdy dostęp użytkownika zostanie usunięty.

Usługa aprowizacji obsługuje zarówno usuwanie, jak i wyłączanie (czasami nazywane usuwaniem nietrwałym) użytkowników. Dokładne definicje Disable i DELETE różnią się w zależności od implementacji aplikacji docelowej, ale ogólnie wyłączenie wskazuje, że użytkownik nie może się zalogować. Usunięcie wskazuje, że użytkownik został całkowicie usunięty z aplikacji. W przypadku aplikacji Standard scim wyłączenie jest żądaniem ustawienia *aktywnej* właściwości na wartość false na użytkowniku. 

**Konfigurowanie aplikacji w celu wyłączenia użytkownika**

Upewnij się, że zaznaczono pole wyboru dla aktualizacji.

Upewnij się, że masz mapowanie dla aplikacji *aktywnej* . Jeśli używasz aplikacji z galerii aplikacji, mapowanie może być nieco inne. Upewnij się, że używasz domyślnego/wyjściowego mapowania pól dla aplikacji galerii.

:::image type="content" source="./media/how-provisioning-works/disable-user.png" alt-text="Wyłącz użytkownika" lightbox="./media/how-provisioning-works/disable-user.png":::


**Konfigurowanie aplikacji do usuwania użytkownika**

Następujące scenariusze spowodują wyzwolenie wyłączenia lub usunięcia: 
* Użytkownik został usunięty z nietrwałego usunięcia w usłudze Azure AD (wysyłany do właściwości kosz/AccountEnabled ustawiony na wartość false).
    po upływie 30 dni od usunięcia użytkownika w usłudze Azure AD zostaną one trwale usunięte z dzierżawy. W tym momencie usługa aprowizacji wyśle żądanie usunięcia, aby trwale usunąć użytkownika w aplikacji. W dowolnym momencie w oknie 30-dniowym można [trwale usunąć użytkownika](../fundamentals/active-directory-users-restore.md), co spowoduje wysłanie żądania usunięcia do aplikacji.
* Użytkownik zostanie trwale usunięty/usunięty z Kosza w usłudze Azure AD.
* Użytkownik nie jest przypisany do aplikacji.
* Użytkownik znajduje się w zakresie poza zakresem (nie przekazuje już filtru określania zakresu).

:::image type="content" source="./media/how-provisioning-works/delete-user.png" alt-text="Usuwanie użytkownika" lightbox="./media/how-provisioning-works/delete-user.png":::

Domyślnie usługa aprowizacji usługi Azure AD nie usuwa lub wyłącza użytkowników, którzy wykraczają poza zakres. Jeśli chcesz zastąpić to zachowanie domyślne, możesz ustawić flagę, aby [pominąć usuwanie poza zakresem.](skip-out-of-scope-deletions.md)

Jeśli wystąpi jedno z powyższych czterech zdarzeń i aplikacja docelowa nie obsługuje usuwania nietrwałego, usługa aprowizacji wyśle żądanie usunięcia, aby trwale usunąć użytkownika z aplikacji.

Jeśli w mapowaniu atrybutów zostanie wyświetlony atrybut IsSoftDeleted, jest on używany do określenia stanu użytkownika oraz tego, czy do usuwania nietrwałego ma być wysyłany żądanie aktualizacji z aktywnym = false.

**Znane ograniczenia**

* Jeśli użytkownik, który był wcześniej zarządzany przez usługę aprowizacji, nie jest przypisany z aplikacji lub z grupy przypisanej do aplikacji, wyślemy żądanie wyłączenia. W tym momencie użytkownik nie jest zarządzany przez usługę i nie wyśle żądania usunięcia, gdy zostaną usunięte z katalogu.
* Inicjowanie obsługi użytkownika, który jest wyłączony w usłudze Azure AD, nie jest obsługiwane. Muszą one być aktywne w usłudze Azure AD przed zainicjowaniem obsługi administracyjnej.
* Gdy użytkownik przejdzie z nietrwałego usunięcia do aktywnego, usługa Azure AD Provisioning uaktywni użytkownika w aplikacji docelowej, ale nie będzie automatycznie przywraca członkostwa w grupie. Aplikacja docelowa powinna zachować członkostwa w grupach dla użytkownika w stanie nieaktywnym. Jeśli aplikacja docelowa nie obsługuje tego programu, można ponownie uruchomić Inicjowanie obsługi administracyjnej, aby zaktualizować członkostwa w grupach. 

**Zalecenie**

Podczas tworzenia aplikacji program zawsze obsługuje zarówno usuwanie nietrwałe, jak i twarde. Umożliwia ona klientom odzyskiwanie po przypadkowe wyłączenie użytkownika.


## <a name="next-steps"></a>Następne kroki

[Planowanie wdrożenia automatycznego aprowizowania użytkowników](../app-provisioning/plan-auto-user-provisioning.md)

[Konfigurowanie aprowizacji dla aplikacji z galerii](./configure-automatic-user-provisioning-portal.md)

[Tworzenie punktu końcowego Standard scim i Konfigurowanie aprowizacji podczas tworzenia własnej aplikacji](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Rozwiązywanie problemów z konfigurowaniem użytkowników i inicjowanie ich obsługi w aplikacji](./application-provisioning-config-problem.md).