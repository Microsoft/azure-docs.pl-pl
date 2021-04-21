---
title: Łączniki dla usługi Azure Logic Apps
description: Omówienie łączników do tworzenia zautomatyzowanych przepływów pracy za pomocą Azure Logic Apps. Dowiedz się, jak działają różne typy łączników, wyzwalaczy i akcji.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771357"
---
# <a name="connectors-for-azure-logic-apps"></a>Łączniki dla usługi Azure Logic Apps

W Azure Logic Apps *łączniki* ułatwiają szybki dostęp do zdarzeń, danych i akcji z innych aplikacji, usług, systemów, protokołów i platform. Często można wykonywać te zadania bez dodatkowego kodu. Łączników można również używać do tworzenia Logic Apps przepływów pracy, które używają tych informacji w środowiskach chmurowych, lokalnych lub hybrydowych.

Istnieją setki łączników dostępnych dla Logic Apps. W związku z tym ta dokumentacja koncentruje się na najpopularniejszych i najczęściej używanych łącznikach dla Logic Apps. Aby uzyskać pełne informacje o łącznikach Logic Apps, Microsoft Power Automate i Microsoft Power Apps, zobacz [dokumentację łączników](/connectors). 

Aby uzyskać informacje na temat cen, [zobacz Logic Apps cennika](../logic-apps/logic-apps-pricing.md)i Logic Apps [szczegóły cennika.](https://azure.microsoft.com/pricing/details/logic-apps/)

Aby zintegrować aplikację logiki z usługą lub interfejsem API, który nie ma łącznika, możesz wywołać usługę za pośrednictwem protokołu, takiego jak HTTP, lub utworzyć [łącznik niestandardowy.](#custom-apis-and-connectors)

## <a name="what-are-connectors"></a>Co to są łączniki?

W Azure Logic Apps łączniki zapewniają *wyzwalacze* i *akcje,* których używasz do wykonywania zadań w przepływie pracy aplikacji logiki. Każdy wyzwalacz i akcja mają właściwości, które można skonfigurować. Niektóre wyzwalacze i akcje wymagają utworzenia i [skonfigurowania](#connection-configuration) połączeń, aby przepływ pracy może uzyskać dostęp do określonej usługi lub systemu.

### <a name="triggers"></a>Wyzwalacze

Wyzwalacz *jest* zawsze pierwszym krokiem w dowolnym przepływie pracy, który określa zdarzenie, które uruchamia przepływ pracy. Istnieje wiele typów wyzwalaczy:

- *Wyzwalacze sondowania* regularnie sprawdzają określoną usługę lub system zgodnie z określonym harmonogramem, aby sprawdzić, czy nie ma nowych danych lub określonego zdarzenia. Jeśli są dostępne nowe dane lub wystąpi określone zdarzenie, te wyzwalacze tworzą i uruchamiają nowe wystąpienie przepływu pracy. To nowe wystąpienie może następnie używać danych przekazywanych jako dane wejściowe.
- *Wyzwalacze wypychania* nasłuchują nowych danych lub zdarzeń bez sondowania. Gdy nowe dane są dostępne lub gdy wystąpi zdarzenie, te wyzwalacze tworzą i uruchamiają nowe wystąpienie aplikacji logiki. To nowe wystąpienie może następnie używać danych przekazywanych jako dane wejściowe.

Możesz na przykład utworzyć przepływ pracy, który robi coś po przesłaniu pliku na serwer FTP. Możesz dodać wyzwalacz łącznika FTP o nazwie **Po dodaniu** lub zmodyfikowaniu pliku jako pierwszy krok w przepływie pracy. Następnie można określić harmonogram regularnego sprawdzania zdarzeń przekazywania.

Wyzwalacz przekazuje również wszelkie dane wejściowe i inne wymagane dane do przepływu pracy, gdzie późniejsze akcje mogą odwoływać się do tych danych i używać ich w całym przepływie pracy. Na przykład możesz użyć wyzwalacza programu Outlook usługi Office 365 o nazwie Po nadejdzie **nowa wiadomość e-mail.** Możesz skonfigurować ten wyzwalacz tak, aby przekazywać zawartość z każdej nowej wiadomości e-mail, takiej jak nadawca, wiersz tematu, treść, załączniki i tak dalej. Następnie możesz przetworzyć te informacje w aplikacji logiki przy użyciu akcji.

### <a name="actions"></a>Akcje

Akcja *to* operacja, która następuje po wyzwalaczu i wykonuje pewnego rodzaju zadanie w przepływie pracy. W aplikacji logiki można używać wielu akcji. Na przykład możesz mieć wyzwalacz SQL, który wykrywa nowe dane klienta w bazie danych SQL. Przepływ pracy może obejmować pierwszą akcję SQL, która pobiera dane klienta, a następnie inną akcję, która niekoniecznie jest akcją SQL, która przetwarza dane.

## <a name="connector-categories"></a>Kategorie łączników

W Logic Apps wyzwalacze i akcje są zwykle wbudowaną lub zarządzaną wersję łącznika. W obu wersjach jest dostępnych kilka wyzwalaczy i akcji. Konkretne wersje zależą od tego, czy tworzysz wielodostępną aplikację logiki, czy aplikację logiki z jedną dzierżawą, która jest obecnie dostępna tylko w wersji [Logic Apps zapoznawczej.](../logic-apps/logic-apps-overview-preview.md)

[Wbudowane wyzwalacze i](built-in.md) akcje są uruchamiane natywnie w środowisku Logic Apps uruchomieniowym, nie wymagają tworzenia połączeń i wykonywania tego rodzaju zadań:

- [Uruchom kod w przepływach pracy.](built-in.md#run-code-from-workflows)
- [Organizowanie i kontrolowanie danych.](built-in.md#control-workflow)
- [Zarządzanie danymi lub manipulowanie nimi.](built-in.md#manage-or-manipulate-data)

[Łączniki zarządzane](managed.md) są wdrażane, hostowane i zarządzane przez firmę Microsoft. Te łączniki zapewniają wyzwalacze i akcje dla usług w chmurze, systemów lokalnych lub obu tych systemów. Są one następujące:

- [Łączniki lokalne,](managed.md#on-premises-connectors) które ułatwiają dostęp do danych i zasobów w systemach lokalnych.
- [Łączniki](managed.md#enterprise-connectors)dla przedsiębiorstw , które są wersjami niektórych łączników Logic Apps, które zapewniają dostęp do systemów przedsiębiorstwa.
- [Łączniki konta integracji,](managed.md#integration-account-connectors)które obsługują scenariusze komunikacji między firmami (B2B).
- [środowisko usługi integracji (ISE),](managed.md#ise-connectors) które są małą grupą łączników zarządzanych dostępnych [tylko dla platform ISE.](#ise-and-connectors)

## <a name="connection-configuration"></a>Konfiguracja połączenia

Większość łączników wymaga, aby najpierw utworzyć połączenie *z* docelową usługą lub systemem, zanim będzie można użyć jego wyzwalaczy lub akcji w aplikacji logiki. Aby utworzyć połączenie, musisz uwierzytelnić swoją tożsamość przy użyciu poświadczeń konta, a czasami innych informacji o połączeniu. Aby na przykład przepływ pracy może uzyskać dostęp do konta e-mail usługi Office 365 Outlook i pracować z nim w aplikacji logiki, musisz autoryzować połączenie z tym kontem.

W przypadku łączników, które używają uwierzytelniania OAuth usługi Azure Active Directory (Azure AD), takiego jak Office 365, Salesforce lub GitHub, musisz zalogować się do usługi, w której token dostępu jest szyfrowany i bezpiecznie przechowywany we tajnym kluczu platformy Azure. [](../security/fundamentals/encryption-overview.md) Inne łączniki, takie jak FTP i SQL, wymagają połączenia ze szczegółami konfiguracji, takimi jak adres serwera, nazwa użytkownika i hasło. Te szczegóły konfiguracji połączenia są również [szyfrowane i bezpiecznie przechowywane na platformie Azure.](../security/fundamentals/encryption-overview.md)

Ustanowione połączenia mogą uzyskać dostęp do docelowej usługi lub systemu tak długo, jak zezwala na to ta usługa lub system. W przypadku usług, które używają połączeń OAuth usługi Azure AD, takich jak Office 365 i Dynamics, Logic Apps odświeża tokeny dostępu przez czas nieokreślony. Inne usługi mogą mieć ograniczenia dotyczące tego, jak długo Logic Apps może używać tokenu bez odświeżania. Ogólnie rzecz biorąc, niektóre akcje, takie jak zmiana hasła, unieważniają wszystkie tokeny dostępu.

Mimo że tworzysz połączenia z poziomu przepływu pracy, połączenia są oddzielnymi zasobami platformy Azure z ich własnymi definicjami zasobów. Aby przejrzeć te definicje zasobów połączeń, [pobierz aplikację logiki](../logic-apps/manage-logic-apps-with-visual-studio.md)z platformy Azure na Visual Studio . Ta metoda jest najprostszym sposobem utworzenia prawidłowego, sparametryzowanego szablonu aplikacji logiki, który jest w większości gotowy do wdrożenia.

> [!TIP]
> Jeśli Twoja organizacja nie zezwala na dostęp do określonych zasobów za [](../logic-apps/block-connections-connectors.md) pośrednictwem łączników usługi Logic Apps, możesz zablokować możliwość tworzenia takich połączeń przy użyciu programu [Azure Policy](../governance/policy/overview.md).

## <a name="recurrence-behavior"></a>Zachowanie cyklu

Cykliczne wyzwalacze wbudowane, takie [](../connectors/connectors-native-recurrence.md)jak wyzwalacz Cykliczne, są uruchamiane natywnie w usłudze Azure Logic Apps i różnią się od wyzwalaczy cyklicznych opartych na połączeniu, takich jak wyzwalacz łącznika programu Outlook usługi Office 365, w przypadku których należy najpierw utworzyć połączenie.

W przypadku obu rodzajów wyzwalaczy, jeśli cykl nie określa określonej daty i godziny rozpoczęcia, pierwszy cykl jest uruchamiany natychmiast po zapisaniu lub wdrożeniu aplikacji logiki, niezależnie od konfiguracji cyklu wyzwalacza. Aby uniknąć tego zachowania, podaj datę i godzinę rozpoczęcia pierwszego cyklu.

### <a name="recurrence-for-built-in-triggers"></a>Cykl dla wyzwalaczy wbudowanych

Wyzwalacze wbudowane cykliczne są zgodne z ustawionym harmonogramem, w tym z dowolną określoną strefą czasową. Jeśli jednak cykl nie określa innych zaawansowanych opcji planowania, takich jak określone czasy uruchamiania przyszłych cyklów, te cykle są oparte na ostatnim wykonaniu wyzwalacza. W związku z tym czasy rozpoczęcia dla tych cyklów mogą dryfować z powodu takich czynników jak opóźnienie podczas wywołań magazynu. Aby uzyskać pomoc w rozwiązywaniu problemów, zobacz [sekcję problemy z cyklem.](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>Cykl dla wyzwalaczy opartych na połączeniach

W wyzwalaczach cyklicznych opartych na połączeniach, takich jak office 365 Outlook, harmonogram nie jest jedynym sterownikiem kontroljącym wykonywanie. Strefa czasowa określa tylko początkowy czas rozpoczęcia. Kolejne przebiegi zależą od harmonogramu cyklu, ostatniego wykonania wyzwalacza i innych czynników, które mogą powodować dryfowanie czasu wykonywania lub spowodować nieoczekiwane zachowanie. Są one następujące:

- Określa, czy wyzwalacz uzyskuje dostęp do serwera, który ma więcej danych, co wyzwalacz natychmiast próbuje pobrać.
- Wszelkie błędy lub ponowne próby, które są naliczane przez wyzwalacz.
- Opóźnienie podczas wywołań magazynu.
- Nie jest zachowywany określony harmonogram, gdy rozpoczyna się i kończy czas letni (DST).
- Inne czynniki, które mogą mieć wpływ na czas następnego uruchomienia.

Aby uzyskać pomoc w rozwiązywaniu problemów, zobacz [sekcję Problemy z cyklem.](#recurrence-issues) 

### <a name="recurrence-issues"></a>Problemy z cyklem

Aby upewnić się, że przepływ pracy jest uruchamiany o określonej godzinie rozpoczęcia i nie pomija cyklu, szczególnie gdy częstotliwość jest w dniach lub dłużej, wypróbuj następujące rozwiązania.

Po uruchomieniu czasu awarii należy ręcznie dostosować cykl, aby przepływ pracy był nadal uruchamiany w oczekiwanym czasie. W przeciwnym razie czas rozpoczęcia przesuwa się o godzinę do przodu, gdy rozpoczyna się czas DST, i o godzinę do tyłu po zakończeniu czasu DST. Aby uzyskać więcej informacji i przykładów, zobacz [Cykl z uwzględnieniem czasu letniego i czasu standardowego.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)

Jeśli używasz wyzwalacza **Cykl,** określ strefę czasową, datę i czas rozpoczęcia. Ponadto skonfiguruj określone godziny uruchamiania kolejnych cyklów we właściwościach W tych godzinach  i O tych **minutach,** które są dostępne tylko dla częstotliwości dzień  **i** tydzień. Jednak okna czasu mogą nadal powodować problemy, gdy czas się zmienia. 

Rozważ użycie [ **wyzwalacza okna przesuwania**](../connectors/connectors-native-sliding-window.md) zamiast **wyzwalacza Cyklicznie,** aby uniknąć pominięcia cyklu.

## <a name="custom-apis-and-connectors"></a>Niestandardowe interfejsy API i łączniki

Aby wywołać interfejsy API, które uruchamiają kod niestandardowy lub nie są dostępne jako łączniki, możesz rozszerzyć platformę Logic Apps, tworząc niestandardowe [API Apps](../logic-apps/logic-apps-create-api-app.md). 

Możesz również utworzyć [łączniki niestandardowe](../logic-apps/custom-connector-overview.md) dla dowolnego interfejsu API opartego na interfejsie REST lub SOAP, dzięki którym te interfejsy API będą dostępne dla dowolnej aplikacji logiki w ramach subskrypcji platformy Azure. 

Aby udostępnić publicznie API Apps łączników dla wszystkich osób na platformie Azure, możesz przesłać łączniki do [firmy Microsoft w celu uzyskania certyfikatu](/connectors/custom-connectors/submit-certification).

## <a name="ise-and-connectors"></a>IsE i łączniki

W przypadku przepływów pracy, które wymagają bezpośredniego dostępu do zasobów w sieci wirtualnej platformy Azure, można utworzyć dedykowane środowisko usługi integracji [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) w którym można kompilować, wdrażać i uruchamiać przepływy pracy w dedykowanych zasobach. Aby uzyskać więcej informacji na temat tworzenia środowisk ISE, zobacz [Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

Łączniki niestandardowe utworzone w środowisku ISE nie działają z lokalną bramą danych. Jednak te łączniki mogą bezpośrednio uzyskać dostęp do lokalnych źródeł danych, które są połączone z siecią wirtualną platformy Azure hostującym platformę ISE. Dlatego aplikacje logiki na platformie ISE najprawdopodobniej nie potrzebują bramy danych podczas komunikacji z tymi zasobami. Jeśli masz łączniki niestandardowe utworzone poza środowisku ISE, które wymagają lokalnej bramy danych, aplikacje logiki w środowisku ISE mogą używać tych łączników.

W programie Logic Apps Designer podczas przeglądania łączników, których chcesz używać dla aplikacji logiki w programie ISE, etykieta **CORE** jest wyświetlana we wbudowanych wyzwalaczach i akcjach, a etykieta **ISE** jest wyświetlana w niektórych łącznikach.

:::row:::
    :::column:::
        ![Przykładowy łącznik CORE](./media/apis-list/example-core-connector.png)
        \
        \
        **Core**
        \
        \
        Wbudowane wyzwalacze i akcje z tą etykietą są uruchamiane w tym samym programie ISE co aplikacje logiki.
    :::column-end:::
    :::column:::
        ![Przykładowy łącznik ISE](./media/apis-list/example-ise-connector.png)
        \
        \
        **Ise**
        \
        \
        Zarządzane łączniki z tą etykietą działają w tym samym programie ISE co aplikacje logiki. W przypadku systemu lokalnego połączonego z siecią wirtualną platformy Azure platforma ISE umożliwia aplikacjom logiki bezpośredni dostęp do tego systemu bez lokalnej [bramy danych.](../logic-apps/logic-apps-gateway-connection.md) Zamiast tego można użyć łącznika **ISE** tego systemu, jeśli jest dostępny, akcji HTTP lub [łącznika niestandardowego](connectors-overview.md#custom-apis-and-connectors). W przypadku systemów lokalnych, które nie mają **łączników ise,** użyj lokalnej bramy danych. Aby zapoznać się z dostępnymi łącznikami ISE, zobacz [Łączniki ISE.](#ise-and-connectors)
    :::column-end:::
    :::column:::
        ![Przykład łącznika wielodostępu](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Brak etykiety     \
        \
        Wszystkie inne łączniki bez **etykiety CORE** lub **ISE,** których można nadal używać, działają w globalnej, wielodostępnej Logic Apps usługi.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono znane problemy dotyczące łączników Logic Apps łączników.

#### <a name="error-badgateway-client-request-id-guid"></a>Błąd: BadGateway. Identyfikator żądania klienta: "{GUID}"

Ten błąd wynika z aktualizowania tagów w aplikacji logiki, w której co najmniej jedno połączenie nie obsługuje uwierzytelniania OAuth usługi Azure Active Directory (Azure AD), takiego jak SFTP ad SQL, przerywając te połączenia. Aby zapobiec takiemu zachowaniu, należy unikać aktualizowania tych tagów.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych interfejsów API, które można wywołać z Logic Apps](/logic-apps/logic-apps-create-api-app)
