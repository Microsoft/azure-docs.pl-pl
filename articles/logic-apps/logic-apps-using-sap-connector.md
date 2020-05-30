---
title: Łączenie z systemami SAP
description: Dostęp do zasobów SAP i zarządzanie nimi przez Automatyzowanie przepływów pracy za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: 557e162d9d7f0238d5554c32cb3ae96885877dbe
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220498"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Łączenie z systemami SAP z usługi Azure Logic Apps

> [!IMPORTANT]
> Wcześniejsze łączniki serwera aplikacji SAP i serwera komunikatów SAP są przestarzałe 29 lutego 2020. Bieżący łącznik SAP konsoliduje te poprzednie łączniki SAP, aby nie trzeba było zmieniać typu połączenia, jest w pełni zgodny z poprzednimi łącznikami, zapewnia wiele dodatkowych możliwości i nadal korzysta z biblioteki łącznika SAP .NET (SAP NCo).
>
> W przypadku aplikacji logiki korzystających ze starszych łączników należy [przeprowadzić migrację do najnowszego łącznika](#migrate) przed datą zakończenia. W przeciwnym razie te aplikacje logiki będą powodować błędy wykonywania i nie będą mogły wysyłać komunikatów do systemu SAP.

W tym artykule pokazano, jak można uzyskać dostęp do lokalnych zasobów SAP z wewnątrz aplikacji logiki przy użyciu łącznika SAP. Łącznik współpracuje z klasycznymi wersjami oprogramowania SAP, takimi jak R/3 i systemu ECC w środowisku lokalnym. Łącznik umożliwia także integrację z nowszymi dla platformy SAP opartymi na platformie HANA, takimi jak S/4 HANA, niezależnie od tego, czy są hostowane lokalnie, czy w chmurze. Łącznik SAP obsługuje integrację komunikatów lub danych z systemami opartymi na systemie SAP NetWeaver za pośrednictwem dokumentu pośredniczącego (IDoc), Business Application Programming Interface (BAPI) lub zdalnego wywołania funkcji (RFC).

Łącznik SAP używa [biblioteki SAP .NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html) i udostępnia następujące akcje:

* **Wyślij wiadomość do SAP**: wysyłanie IDOC przez tRFC, wywoływanie funkcji interfejsu BAPI w specyfikacji RFC lub wywoływanie RFC/tRFC w systemach SAP.

* **Po odebraniu komunikatu od SAP**: otrzymywanie IDOC przez tRFC, wywoływanie funkcji interfejsu BAPI przez tRFC lub wywołanie RFC/tRFC w systemach SAP.

* **Generuj schematy**: Generuj schematy dla artefaktów SAP dla IDOC, BAPI lub RFC.

W przypadku tych operacji łącznik SAP obsługuje uwierzytelnianie podstawowe za pomocą nazw użytkowników i haseł. Łącznik obsługuje również [bezpieczną komunikację sieciową (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). Usługi SNC można używać do logowania jednokrotnego (SSO) SAP NetWeaver lub w celu uzyskania dodatkowych możliwości zabezpieczeń zapewnianych przez zewnętrzny produkt zabezpieczeń.

W tym artykule przedstawiono sposób tworzenia przykładowych aplikacji logiki, które integrują się z systemem SAP, oraz obejmują opisane wcześniej scenariusze integracji. W przypadku aplikacji logiki, które używają starszych łączników SAP, w tym artykule przedstawiono sposób migracji aplikacji logiki do najnowszego łącznika SAP.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym artykule, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki z lokalizacji, w której chcesz uzyskać dostęp do systemu SAP, i wyzwalacza, który uruchamia przepływ pracy aplikacji logiki. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zobacz [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Serwer aplikacji SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) lub [serwer komunikatów SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Zawartość komunikatu, którą można wysłać do serwera SAP, na przykład plik IDoc, musi być w formacie XML i zawierać przestrzeń nazw dla akcji SAP, która ma być używana.

* Aby użyć, **gdy komunikat jest odbierany z** wyzwalacza SAP, należy również wykonać następujące czynności konfiguracyjne:

  * Skonfiguruj swoje uprawnienia zabezpieczeń bramy SAP przy użyciu tego ustawienia:

    `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

  * Skonfiguruj rejestrowanie zabezpieczeń bramy SAP, które pomaga znaleźć błędy listy Access Control (ACL) i nie jest domyślnie włączone. W przeciwnym razie zostanie wyświetlony następujący błąd:

    `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    Aby uzyskać więcej informacji, zobacz temat pomocy SAP, [Konfigurowanie rejestrowania bramy](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm).

<a name="multi-tenant"></a>

### <a name="multi-tenant-azure-prerequisites"></a>Wymagania wstępne platformy Azure z wieloma dzierżawcami

Te wymagania wstępne są stosowane, gdy aplikacje logiki działają na platformie Azure z wieloma dzierżawcami, i chcesz użyć zarządzanego łącznika SAP, który nie działa natywnie w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). W przeciwnym razie, jeśli używasz ISE na poziomie Premium i chcesz używać łącznika SAP, który działa natywnie w ISE, zobacz [wymagania wstępne dotyczące programu Integration Service Environment (ISE)](#sap-ise).

Zarządzany łącznik SAP (ISE) jest zintegrowany z lokalnymi systemami SAP za pomocą [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md). Na przykład w przypadku scenariuszy wysyłania komunikatów, gdy komunikat jest wysyłany z aplikacji logiki do systemu SAP, Brama danych działa jako klient RFC i przekazuje żądania otrzymane z aplikacji logiki do oprogramowania SAP. Podobnie w przypadku scenariuszy komunikatów o odbieraniu Brama danych działa jako serwer RFC, który odbiera żądania od SAP i przekazuje je do aplikacji logiki.

* [Pobierz i zainstaluj lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) na komputerze lokalnym. Następnie [Utwórz zasób bramy platformy Azure](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) dla tej bramy w Azure Portal. Brama pomaga w bezpiecznym dostępie do danych i zasobów lokalnych.

  Najlepszym rozwiązaniem jest upewnienie się, że jest używana obsługiwana wersja lokalnej bramy danych. Firma Microsoft udostępnia nową wersję co miesiąc. Obecnie firma Microsoft obsługuje sześć ostatnich wersji. Jeśli wystąpi problem z bramą, spróbuj [uaktualnić do najnowszej wersji](https://aka.ms/on-premises-data-gateway-installer), co może obejmować aktualizacje umożliwiające rozwiązanie problemu.

* [Pobierz i zainstaluj najnowszą bibliotekę klienta SAP](#sap-client-library-prerequisites) na tym samym komputerze, na którym znajduje się lokalna Brama danych.

<a name="sap-ise"></a>

### <a name="integration-service-environment-ise-prerequisites"></a>Wymagania wstępne dotyczące środowiska usługi integracji (ISE)

Te wymagania wstępne są stosowane, gdy aplikacje logiki działają w środowisku usługi integracji na poziomie Premium (nie deweloperem) [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)i chcesz używać łącznika SAP, który działa natywnie w ISE. ISE zapewnia dostęp do zasobów chronionych przez sieć wirtualną platformy Azure i oferuje inne łączniki natywne ISE, które umożliwiają aplikacjom logiki bezpośredni dostęp do zasobów lokalnych bez korzystania z lokalnej bramy danych.

> [!NOTE]
> Mimo że łącznik SAP ISE jest widoczny wewnątrz ISE na poziomie dewelopera, próby zainstalowania łącznika nie powiedzie się.

1. Jeśli nie masz jeszcze konta usługi Azure Storage i kontenera obiektów blob, Utwórz ten kontener przy użyciu [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) lub [Eksplorator usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Pobierz i zainstaluj najnowszą bibliotekę klienta SAP](#sap-client-library-prerequisites) na komputerze lokalnym. Należy mieć następujące pliki zestawu:

   * libicudecnumber. dll
   * rscp4n. dll
   * sapnco. dll
   * sapnco_utils. dll

1. Utwórz plik. zip, który zawiera te zestawy, i przekaż ten pakiet do kontenera obiektów BLOB w usłudze Azure Storage.

1. W Azure Portal lub Eksplorator usługi Azure Storage przejdź do lokalizacji kontenera, do której został przekazany plik. zip.

1. Skopiuj adres URL dla tej lokalizacji, upewniając się, że dołączysz token sygnatury dostępu współdzielonego (SAS).

   W przeciwnym razie token sygnatury dostępu współdzielonego nie zostanie autoryzowany, a wdrożenie łącznika SAP ISE zakończy się niepowodzeniem.

1. Aby można było używać łącznika SAP ISE, należy zainstalować i wdrożyć łącznik w ISE.

   1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz ISE.
   
   1. W menu ISE wybierz pozycję **Łączniki zarządzane**  >  **Dodaj**. Z listy łączniki Znajdź i wybierz pozycję **SAP**.
   
   1. W okienku **Dodaj nowe łączniki zarządzane** w polu **pakiet SAP** Wklej adres URL pliku zip, który zawiera zestawy SAP. *Upewnij się, że dołączysz token sygnatury dostępu współdzielonego.*

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   Aby uzyskać więcej informacji, zobacz [Dodawanie łączników ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

1. Jeśli wystąpienie oprogramowania SAP i ISE znajdują się w różnych sieciach wirtualnych, należy również połączyć [te sieci równorzędne](../virtual-network/tutorial-connect-virtual-networks-portal.md) , aby Sieć wirtualna ISE była połączona z siecią wirtualną Twojego wystąpienia SAP.

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>Wymagania wstępne dotyczące biblioteki klienta SAP

* Upewnij się, że zainstalowano najnowszą wersję, [Łącznik SAP (NCo 3,0) dla Microsoft .NET 3.0.22.0 skompilowany przy użyciu .NET Framework 4,0 — Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019). Wcześniejsze wersje mogą powodować problemy ze zgodnością. Aby uzyskać więcej informacji, zobacz [wersje biblioteki klienta SAP](#sap-library-versions).

* Domyślnie Instalator SAP umieszcza pliki zestawu w domyślnym folderze instalacji. Należy skopiować te pliki zestawu do innej lokalizacji w zależności od scenariusza w następujący sposób:

  W przypadku aplikacji logiki, które są uruchamiane w ISE, wykonaj kroki opisane w sekcji [wymagania wstępne dotyczące środowiska usługi integracji](#sap-ise). W przypadku aplikacji logiki, które działają na platformie Azure z wieloma dzierżawcami i korzystają z lokalnej bramy danych, skopiuj pliki zestawu z domyślnego folderu instalacji do folderu instalacji usługi Data Gateway. Jeśli wystąpią problemy z bramą danych, zapoznaj się z następującymi problemami:

  * Należy zainstalować 64-bitową wersję dla biblioteki klienta SAP, ponieważ Brama danych działa tylko w systemach 64-bitowych. W przeciwnym razie zostanie wyświetlony komunikat o błędzie "zły obraz", ponieważ usługa hosta bramy danych nie obsługuje zestawów 32-bitowych.

  * Jeśli połączenie SAP zakończy się niepowodzeniem z komunikatem o błędzie "Sprawdź informacje o koncie i/lub uprawnienia i spróbuj ponownie", pliki zestawu mogą znajdować się w niewłaściwej lokalizacji. Upewnij się, że skopiowano pliki zestawu do folderu instalacji bramy danych.

    Aby pomóc w rozwiązywaniu problemów, [Użyj przeglądarki dzienników powiązań zestawu .NET](https://docs.microsoft.com/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), która pozwala sprawdzić, czy pliki zestawu znajdują się we właściwym miejscu. Opcjonalnie można wybrać opcję **rejestracji globalnej pamięci podręcznej zestawów** podczas instalowania biblioteki klienta SAP.

<a name="sap-library-versions"></a>

#### <a name="sap-client-library-versions"></a>Wersje biblioteki klienta SAP

Wcześniejsze wersje oprogramowania SAP NCo mogą stać się zakleszczeni po wysłaniu więcej niż jednego komunikatu IDoc w tym samym czasie. Ten warunek blokuje wszystkie późniejsze komunikaty wysyłane do miejsca docelowego SAP, co powoduje przekroczenie limitu czasu komunikatów.

Poniżej przedstawiono relacje między biblioteką klienta SAP, .NET Framework, środowiskiem uruchomieniowym .NET i bramą:

* Zarówno karta Microsoft SAP, jak i usługa hosta bramy używają .NET Framework 4,5.

* Rozwiązanie SAP NCo dla .NET Framework 4,0 współpracuje z procesami, które korzystają z środowiska uruchomieniowego .NET 4,0 do 4.7.1.

* Rozwiązanie SAP NCo dla .NET Framework 2,0 współpracuje z procesami, które korzystają z programu .NET Runtime 2,0 do 3,5, ale nie będą już działać z najnowszą bramą.

### <a name="secure-network-communications-prerequisites"></a>Wymagania wstępne dotyczące bezpiecznej komunikacji sieciowej

W przypadku korzystania z lokalnej bramy danych z opcjonalną bezpieczną komunikacją sieciową (SNC), która jest obsługiwana tylko w przypadku platformy Azure z wieloma dzierżawcami, należy również skonfigurować następujące ustawienia:

* Jeśli używasz SNC z logowaniem jednokrotnym (SSO), upewnij się, że brama danych działa jako użytkownik, który jest mapowany na użytkownika SAP. Aby zmienić domyślne konto, wybierz pozycję **Zmień konto**, a następnie wprowadź poświadczenia użytkownika.

  ![Zmień konto bramy danych](./media/logic-apps-using-sap-connector/gateway-account.png)

* W przypadku włączenia SNC z produktem zewnętrznym zabezpieczeń Skopiuj bibliotekę SNC lub pliki na tym samym komputerze, na którym zainstalowano bramę danych. Niektóre przykłady produktów SNC obejmują [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos i NTLM.

Aby uzyskać więcej informacji na temat włączania usługi SNC dla bramy danych, zobacz [Włączanie bezpiecznej komunikacji sieciowej](#secure-network-communications).

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrowanie do bieżącego łącznika

Aby przeprowadzić migrację ze starszego zarządzanego łącznika SAP (innego niż ISE) do bieżącego zarządzanego łącznika SAP, wykonaj następujące kroki:

1. Jeśli jeszcze tego nie zrobiono, zaktualizuj [lokalną bramę danych](https://www.microsoft.com/download/details.aspx?id=53127) , tak aby była dostępna Najnowsza wersja. Aby uzyskać więcej informacji, zobacz [Instalowanie lokalnej bramy danych dla Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. W aplikacji logiki korzystającej ze starszego łącznika SAP Usuń akcję **Wyślij do SAP** .

1. Z poziomu najnowszego łącznika SAP Dodaj **komunikat Wyślij do działania SAP** . Aby można było użyć tej akcji, należy ponownie utworzyć połączenie z systemem SAP.

1. Gdy skończysz, Zapisz aplikację logiki.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Wyślij wiadomość do oprogramowania SAP

W tym przykładzie zastosowano aplikację logiki, którą można wyzwolić za pomocą żądania HTTP. Aplikacja logiki wysyła IDoc do serwera SAP i zwraca odpowiedź do żądającego, który wywołał aplikację logiki.

### <a name="add-an-http-request-trigger"></a>Dodawanie wyzwalacza żądania HTTP

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

> [!NOTE]
> Gdy aplikacja logiki odbiera pakiety IDoc z oprogramowania SAP, [wyzwalacz żądania](https://docs.microsoft.com/azure/connectors/connectors-native-reqres) nie obsługuje "zwykłego" schematu XML wygenerowanego przez dokumentację WE60 IDOC firmy SAP. Jednak schemat XML "zwykły" jest obsługiwany w scenariuszach, które wysyłają komunikaty z aplikacji logiki *do* oprogramowania SAP. Można użyć wyzwalacza żądania z IDoc XML programu SAP, ale nie z IDocem w dokumencie RFC. Można też przekształcić kod XML w niezbędny format. 

W tym przykładzie utworzysz aplikację logiki z punktem końcowym na platformie Azure, aby można było wysyłać *żądania HTTP Post* do aplikacji logiki. Gdy aplikacja logiki otrzymuje te żądania HTTP, wyzwalacz jest uruchamiany i uruchamia następny krok w przepływie pracy.

1. W [Azure Portal](https://portal.azure.com)Utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki.

1. W polu wyszukiwania wprowadź `http request` jako filtr. Z listy **wyzwalacze** wybierz opcję **po odebraniu żądania HTTP**.

   ![Dodawanie wyzwalacza żądania HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Teraz Zapisz aplikację logiki, aby można było wygenerować adres URL punktu końcowego dla aplikacji logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Adres URL punktu końcowego jest teraz wyświetlany w wyzwalaczu, na przykład:

   ![Generuj adres URL dla punktu końcowego](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Dodaj akcję SAP

W Azure Logic Apps [Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) to krok w przepływie pracy, który następuje po wyzwalaczu lub innej akcji. Jeśli wyzwalacz nie został jeszcze dodany do aplikacji logiki i chcesz postępować zgodnie z tym przykładem, [Dodaj wyzwalacz opisany w tej sekcji](#add-trigger).

1. W Projektancie aplikacji logiki, w obszarze wyzwalacza wybierz pozycję **nowy krok**.

   ![Dodawanie nowego kroku do aplikacji logiki](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. W polu wyszukiwania wprowadź `sap` jako filtr. Z listy **Akcje** wybierz pozycję **Wyślij wiadomość do SAP**.
  
   ![Wybierz akcję "Wyślij wiadomość do SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Możesz też wybrać kartę **Enterprise** , a następnie wybrać akcję SAP.

   ![Wybierz akcję "Wyślij wiadomość do SAP" z karty Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Jeśli połączenie już istnieje, przejdź do następnego kroku, aby móc skonfigurować akcję SAP. Jednak jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, podaj informacje, aby można było utworzyć połączenie z lokalnym serwerem SAP.

   1. Podaj nazwę połączenia.

   1. Jeśli używasz bramy danych, wykonaj następujące kroki:
   
      1. W sekcji **brama danych** w obszarze **subskrypcja**najpierw wybierz subskrypcję platformy Azure dla zasobu bramy danych utworzonego w Azure Portal na potrzeby instalacji bramy danych.
   
      1. W obszarze **Brama połączenia**wybierz zasób bramy danych na platformie Azure.

   1. Kontynuuj dostarczanie informacji o połączeniu. Dla właściwości **Typ logowania** postępuj zgodnie z krokami w zależności od tego, czy właściwość jest ustawiona na **serwer aplikacji** czy **Grupa**:
   
      * W przypadku **serwera aplikacji**te właściwości, które zwykle są opcjonalne, są wymagane:

        ![Utwórz połączenie z serwerem aplikacji SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Dla **grupy**, te właściwości, które zwykle są opcjonalne, są wymagane:

        ![Utwórz połączenie z serwerem komunikatów SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Domyślnie silne wpisywanie jest używane do sprawdzania nieprawidłowych wartości przez wykonywanie walidacji kodu XML względem schematu. Takie zachowanie może pomóc wykryć problemy wcześniej. Opcja **bezpieczne wpisywanie** jest dostępna w celu zapewnienia zgodności z poprzednimi wersjami i sprawdza tylko długość ciągu. Dowiedz się więcej o [opcji bezpiecznego wpisywania](#safe-typing).

   1. Po zakończeniu wybierz pozycję **Utwórz**.

      Logic Apps konfiguruje i testuje połączenie, aby upewnić się, że połączenie działa poprawnie.

1. Teraz Znajdź i wybierz akcję z serwera SAP.

   1. W polu **Akcja SAP** wybierz ikonę folderu. Z listy plik Znajdź i wybierz komunikat SAP, którego chcesz użyć. Aby nawigować po liście, użyj strzałek.

      Ten przykład wybiera IDoc z typem **Orders** .

      ![Znajdź i wybierz akcję IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Jeśli nie możesz znaleźć żądanej akcji, możesz ręcznie wprowadzić ścieżkę, na przykład:

      ![Ręcznie podaj ścieżkę do akcji IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Podaj wartość dla **akcji SAP** za pomocą edytora wyrażeń. W ten sposób można użyć tej samej akcji dla różnych typów komunikatów.

      Aby uzyskać więcej informacji o operacjach IDoc, zobacz [schematy komunikatów dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Kliknij wewnątrz pola **komunikat wejściowy** , aby wyświetlić listę zawartości dynamicznej. Z tej listy, w obszarze **po odebraniu żądania HTTP**, zaznacz pole **treść** .

      Ten krok obejmuje zawartość treści z wyzwalacza żądania HTTP i wysyła te dane wyjściowe do serwera SAP.

      ![Wybierz właściwość "treść" z wyzwalacza](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Po zakończeniu akcja SAP będzie wyglądać następująco:

      ![Ukończ akcję SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Dodaj akcję odpowiedzi HTTP

Teraz Dodaj akcję odpowiedzi do przepływu pracy aplikacji logiki i Uwzględnij dane wyjściowe akcji SAP. Dzięki temu aplikacja logiki zwraca wyniki z serwera SAP do oryginalnego obiektu żądającego.

1. W Projektancie aplikacji logiki w obszarze Akcja SAP wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wprowadź `response` jako filtr. Z listy **Akcje** wybierz pozycję **odpowiedź**.

1. Kliknij wewnątrz pola **treść** , aby wyświetlić listę zawartości dynamicznej. Z tej listy w obszarze **Wyślij wiadomość do SAP**zaznacz pole **treść** .

   ![Ukończ akcję SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Zapisz aplikację logiki.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Jeśli aplikacja logiki nie została już włączona, w menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Włącz**.

1. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Ten krok polega na ręcznym uruchomieniu aplikacji logiki.

1. Wyzwól aplikację logiki, wysyłając żądanie HTTP POST do adresu URL w wyzwalaczu żądania HTTP.
Dołącz zawartość wiadomości do żądania. Aby wysłać żądanie, można użyć narzędzia, takiego jak [Poster](https://www.getpostman.com/apps).

   W tym artykule żądanie wysyła plik IDoc, który musi być w formacie XML i zawiera przestrzeń nazw dla używanej akcji SAP, na przykład:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Po wysłaniu żądania HTTP poczekaj na odpowiedź z aplikacji logiki.

   > [!NOTE]
   > Aplikacja logiki może przekroczyć limit czasu, jeśli wszystkie kroki wymagane do odpowiedzi nie zakończą się w ramach [limitu czasu żądania](./logic-apps-limits-and-config.md). W przypadku tego stanu żądania mogą zostać zablokowane. Aby ułatwić diagnozowanie problemów, Dowiedz się, jak można [sprawdzić i monitorować aplikacje logiki](../logic-apps/monitor-logic-apps.md).

Aplikacja logiki, która może komunikować się z serwerem SAP, została już utworzona. Teraz, po skonfigurowaniu połączenia SAP dla aplikacji logiki, można eksplorować inne dostępne akcje SAP, takie jak BAPI i RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Odbierz wiadomość od SAP

W tym przykładzie jest stosowana aplikacja logiki, która wyzwala, gdy aplikacja otrzymuje komunikat z systemu SAP.

### <a name="add-an-sap-trigger"></a>Dodawanie wyzwalacza SAP

1. W Azure Portal Utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki.

1. W polu wyszukiwania wprowadź `sap` jako filtr. Z listy **wyzwalacze** wybierz opcję **Kiedy komunikat zostanie ODEBRANY z oprogramowania SAP**.

   ![Dodaj wyzwalacz SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Możesz też wybrać kartę **Enterprise** , a następnie wybrać wyzwalacz:

   ![Dodaj wyzwalacz SAP z karty Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Jeśli połączenie już istnieje, przejdź do następnego kroku, aby móc skonfigurować akcję SAP. Jeśli jednak zostanie wyświetlony monit o podanie szczegółów połączenia, podaj informacje, aby teraz można było utworzyć połączenie z lokalnym serwerem SAP.

   1. Podaj nazwę połączenia.

   1. Jeśli używasz bramy danych, wykonaj następujące kroki:

      1. W sekcji **brama danych** w obszarze **subskrypcja**najpierw wybierz subskrypcję platformy Azure dla zasobu bramy danych utworzonego w Azure Portal na potrzeby instalacji bramy danych.

      1. W obszarze **Brama połączenia**wybierz zasób bramy danych na platformie Azure.

   1. Kontynuuj dostarczanie informacji o połączeniu. Dla właściwości **Typ logowania** postępuj zgodnie z krokami w zależności od tego, czy właściwość jest ustawiona na **serwer aplikacji** czy **Grupa**:

      * W przypadku **serwera aplikacji**te właściwości, które zwykle są opcjonalne, są wymagane:

        ![Utwórz połączenie z serwerem aplikacji SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Dla **grupy**, te właściwości, które zwykle są opcjonalne, są wymagane:

        ![Utwórz połączenie z serwerem komunikatów SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Domyślnie silne wpisywanie jest używane do sprawdzania nieprawidłowych wartości przez wykonywanie walidacji kodu XML względem schematu. Takie zachowanie może pomóc wykryć problemy wcześniej. Opcja **bezpieczne wpisywanie** jest dostępna w celu zapewnienia zgodności z poprzednimi wersjami i sprawdza tylko długość ciągu. Dowiedz się więcej o [opcji bezpiecznego wpisywania](#safe-typing).

   1. Po zakończeniu wybierz pozycję **Utwórz**.

      Logic Apps konfiguruje i testuje połączenie, aby upewnić się, że połączenie działa poprawnie.

1. Podaj [wymagane parametry](#parameters) w oparciu o konfigurację systemu SAP.

   Opcjonalnie możesz podać co najmniej jedną akcję SAP. Ta lista akcji określa komunikaty odbierane przez wyzwalacz z serwera SAP. Pusta lista określa, że wyzwalacz odbiera wszystkie komunikaty. Jeśli lista zawiera więcej niż jeden komunikat, wyzwalacz odbiera tylko komunikaty określone na liście. Wszystkie inne komunikaty wysyłane z serwera SAP są odrzucane.

   Można wybrać akcję SAP z selektora plików:

   ![Dodawanie akcji SAP do aplikacji logiki](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Lub można ręcznie określić akcję:

   ![Ręcznie wprowadź akcję SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Oto przykład pokazujący, jak akcja pojawia się po skonfigurowaniu wyzwalacza tak, aby otrzymywał więcej niż jeden komunikat.

   ![Przykład wyzwalacza, który odbiera wiele komunikatów](media/logic-apps-using-sap-connector/example-trigger.png)

   Aby uzyskać więcej informacji na temat akcji SAP, zobacz [schematy komunikatów dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Teraz Zapisz aplikację logiki, aby można było rozpocząć otrzymywanie komunikatów z systemu SAP. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aplikacja logiki jest teraz gotowa do odbierania komunikatów z systemu SAP.

> [!NOTE]
> Wyzwalacz SAP nie jest wyzwalaczem sondowania, ale zamiast niego jest wyzwalaczem opartym na elemencie webhook. W przypadku korzystania z bramy danych wyzwalacz jest wywoływany z bramy danych tylko wtedy, gdy istnieje komunikat, więc nie jest wymagane sondowanie.

<a name="parameters"></a>

#### <a name="parameters"></a>Parametry

Wraz z prostymi danymi wejściowymi typu String i Number łącznik SAP akceptuje następujące parametry tabeli ( `Type=ITAB` dane wejściowe):

* Parametry kierunku tabeli, dane wejściowe i wyjściowe dla starszych wersji SAP.
* Zmienianie parametrów, które zastępują parametry kierunku tabeli dla nowszych wersji SAP.
* Parametry tabeli hierarchicznej

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Aby wyzwolić aplikację logiki, Wyślij komunikat z systemu SAP.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. Przejrzyj **historię uruchomień** dla wszystkich nowych przebiegów dla aplikacji logiki.

1. Otwórz najnowszy przebieg, który pokazuje komunikat wysłany z systemu SAP w sekcji dane wyjściowe wyzwalacza.

## <a name="receive-idoc-packets-from-sap"></a>Odbieranie pakietów IDoc z oprogramowania SAP

Można skonfigurować SAP, aby [wysyłał IDocs w pakietach](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), które są partiami lub grupami IDocs. Do odbierania pakietów IDoc, łącznika SAP i konkretnego wyzwalacza nie jest wymagana dodatkowa konfiguracja. Jednak, aby przetwarzać każdy element w pakiecie IDoc po odebraniu pakietu przez wyzwalacz, należy wykonać pewne dodatkowe kroki, aby podzielić pakiet na poszczególne IDocs.

Oto przykład, który pokazuje, jak wyodrębnić poszczególne IDocs z pakietu przy użyciu [ `xpath()` funkcji](./workflow-definition-language-functions-reference.md#xpath):

1. Przed rozpoczęciem potrzebna jest aplikacja logiki z wyzwalaczem SAP. Jeśli nie masz jeszcze tej aplikacji logiki, wykonaj kroki opisane w tym temacie, aby [skonfigurować aplikację logiki z wyzwalaczem SAP](#receive-from-sap).

   Przykład:

   ![Dodawanie wyzwalacza SAP do aplikacji logiki](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Pobierz główną przestrzeń nazw z IDoc XML, którą aplikacja logiki otrzymuje od SAP. Aby wyodrębnić tę przestrzeń nazw z dokumentu XML, należy dodać krok, który tworzy zmienną ciągu lokalnego i zapisuje tę przestrzeń nazw przy użyciu `xpath()` wyrażenia:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Pobierz główną przestrzeń nazw z IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Aby wyodrębnić pojedyncze IDoc, Dodaj krok, który tworzy zmienną tablicową i zapisuje kolekcję IDoc przy użyciu innego `xpath()` wyrażenia:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Pobierz tablicę elementów](./media/logic-apps-using-sap-connector/get-array.png)

   Zmienna Array sprawia, że każdy IDoc jest dostępny dla aplikacji logiki do przetworzenia indywidualnie przez Wyliczenie w kolekcji. W tym przykładzie aplikacja logiki przesyła każdy IDoc do serwera SFTP przy użyciu pętli:

   ![Wyślij IDoc do serwera SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Każdy IDoc musi zawierać główną przestrzeń nazw, co stanowi powód, dla którego zawartość pliku jest opakowana wewnątrz `<Receive></Receive` elementu wraz z główną przestrzenią nazw przed wysłaniem IDOC do aplikacji podrzędnej lub w tym przypadku serwera SFTP.

Szablonu szybkiego startu można użyć dla tego wzorca, wybierając ten szablon w Projektancie aplikacji logiki podczas tworzenia nowej aplikacji logiki.

![Wybieranie szablonu aplikacji logiki wsadowej](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generowanie schematów dla artefaktów w oprogramowaniu SAP

W tym przykładzie zastosowano aplikację logiki, którą można wyzwolić za pomocą żądania HTTP. Akcja SAP wysyła żądanie do systemu SAP w celu wygenerowania schematów dla określonych IDoc i interfejsu BAPI. Schematy zwracane w odpowiedzi są przekazywane do konta integracji przy użyciu łącznika Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Dodawanie wyzwalacza żądania HTTP

1. W Azure Portal Utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki.

1. W polu wyszukiwania wprowadź `http request` jako filtr. Z listy **wyzwalacze** wybierz opcję **po odebraniu żądania HTTP**.

   ![Dodawanie wyzwalacza żądania HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Teraz Zapisz aplikację logiki, aby można było wygenerować adres URL punktu końcowego dla aplikacji logiki.
Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Adres URL punktu końcowego jest teraz wyświetlany w wyzwalaczu, na przykład:

   ![Generuj adres URL dla punktu końcowego](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Dodaj akcję SAP w celu wygenerowania schematów

1. W Projektancie aplikacji logiki, w obszarze wyzwalacza wybierz pozycję **nowy krok**.

   ![Dodawanie nowego kroku do aplikacji logiki](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. W polu wyszukiwania wprowadź `sap` jako filtr. Z listy **Akcje** wybierz pozycję **Generuj schematy**.
  
   ![Dodaj akcję "Generuj schematy" do aplikacji logiki](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Możesz też wybrać kartę **Enterprise** , a następnie wybrać akcję SAP.

   ![Wybieranie akcji wysyłania SAP z karty Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Jeśli połączenie już istnieje, przejdź do następnego kroku, aby móc skonfigurować akcję SAP. Jeśli jednak zostanie wyświetlony monit o podanie szczegółów połączenia, podaj informacje, aby teraz można było utworzyć połączenie z lokalnym serwerem SAP.

   1. Podaj nazwę połączenia.

   1. W sekcji **brama danych** w obszarze **subskrypcja**najpierw wybierz subskrypcję platformy Azure dla zasobu bramy danych utworzonego w Azure Portal na potrzeby instalacji bramy danych. 
   
   1. W obszarze **Brama połączenia**wybierz zasób bramy danych na platformie Azure.

   1. Kontynuuj dostarczanie informacji o połączeniu. Dla właściwości **Typ logowania** postępuj zgodnie z krokami w zależności od tego, czy właściwość jest ustawiona na **serwer aplikacji** czy **Grupa**:
   
      * W przypadku **serwera aplikacji**te właściwości, które zwykle są opcjonalne, są wymagane:

        ![Utwórz połączenie z serwerem aplikacji SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Dla **grupy**, te właściwości, które zwykle są opcjonalne, są wymagane:

        ![Utwórz połączenie z serwerem komunikatów SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Domyślnie silne wpisywanie jest używane do sprawdzania nieprawidłowych wartości przez wykonywanie walidacji kodu XML względem schematu. Takie zachowanie może pomóc wykryć problemy wcześniej. Opcja **bezpieczne wpisywanie** jest dostępna w celu zapewnienia zgodności z poprzednimi wersjami i sprawdza tylko długość ciągu. Dowiedz się więcej o [opcji bezpiecznego wpisywania](#safe-typing).

   1. Po zakończeniu wybierz pozycję **Utwórz**.

      Logic Apps konfiguruje i testuje połączenie, aby upewnić się, że połączenie działa poprawnie.

1. Podaj ścieżkę do artefaktu, dla którego chcesz wygenerować schemat.

   Można wybrać akcję SAP z selektora plików:

   ![Wybierz akcję SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Możesz też wprowadzić akcję ręcznie:

   ![Ręcznie wprowadź akcję SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Aby wygenerować schematy dla więcej niż jednego artefaktu, podaj szczegóły akcji SAP dla każdego artefaktu, na przykład:

   ![Wybierz pozycję Dodaj nowy element](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Pokaż dwa elementy](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Aby uzyskać więcej informacji na temat akcji SAP, zobacz [schematy komunikatów dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Na pasku narzędzi projektanta wybierz pozycję **Uruchom** , aby wyzwolić przebieg aplikacji logiki.

1. Otwórz przebieg i sprawdź dane wyjściowe akcji **Generuj schematy** .

   Dane wyjściowe zawierają wygenerowane schematy dla określonej listy komunikatów.

### <a name="upload-schemas-to-an-integration-account"></a>Przekazywanie schematów na konto integracji

Opcjonalnie można pobrać lub zapisać wygenerowane schematy w repozytoriach, takich jak obiekt BLOB, magazyn lub konto integracji. Konta integracji zapewniają pierwsze środowisko z innymi akcjami XML, dlatego w tym przykładzie pokazano, jak przekazywać schematy do konta integracji dla tej samej aplikacji logiki przy użyciu łącznika Azure Resource Manager.

1. W Projektancie aplikacji logiki, w obszarze wyzwalacza wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wprowadź `Resource Manager` jako filtr. Wybierz pozycję **Utwórz lub zaktualizuj zasób**.

   ![Wybierz akcję Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Wprowadź szczegóły akcji, w tym subskrypcję platformy Azure, grupę zasobów platformy Azure i konto integracji. Aby dodać tokeny SAP do pól, kliknij wewnątrz pól tych pól i wybierz z wyświetlonej listy zawartości dynamicznej.

   1. Otwórz listę **Dodaj nowy parametr** , a następnie wybierz pola **Lokalizacja** i **Właściwości** .

   1. Podaj szczegóły tych nowych pól, jak pokazano w tym przykładzie.

      ![Wprowadź szczegóły akcji Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Akcja **Generuj schematy** w programie SAP generuje schematy jako kolekcję, więc Projektant automatycznie dodaje do akcji pętlę **for each** . Oto przykład, który pokazuje, jak wygląda ta akcja:

   ![Azure Resource Manager akcji z pętlą "for each"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Schematy używają formatu zakodowanego algorytmem Base64. Aby przekazać schematy do konta integracji, muszą one zostać zdekodowane przy użyciu `base64ToString()` funkcji. Oto przykład, który pokazuje kod dla `"properties"` elementu:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Na pasku narzędzi projektanta wybierz pozycję **Uruchom** , aby ręcznie wyzwolić aplikację logiki.

1. Po pomyślnym uruchomieniu przejdź do konta integracji i sprawdź, czy wygenerowane schematy istnieją.

<a name="secure-network-communications"></a>

## <a name="enable-secure-network-communications"></a>Włącz bezpieczną komunikację sieciową

Przed rozpoczęciem upewnij się, że spełniono wcześniej wymienione [wstępnie wymagania wstępne](#pre-reqs), które są stosowane tylko wtedy, gdy używasz bramy danych i aplikacji logiki w usłudze Azure z wieloma dzierżawcami:

* Upewnij się, że lokalna Brama danych jest zainstalowana na komputerze znajdującym się w tej samej sieci, co system SAP.

* W przypadku logowania jednokrotnego (SSO) Brama danych działa jako użytkownik mapowany do użytkownika SAP.

* Biblioteka SNC, która udostępnia dodatkowe funkcje zabezpieczeń, jest zainstalowana na tym samym komputerze, na którym znajduje się Brama danych. Niektóre przykłady obejmują [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos i NTLM.

   Aby włączyć SNC dla żądań do lub z systemu SAP, zaznacz pole wyboru **Użyj SNC** w połączeniu SAP i podaj następujące właściwości:

   ![Konfigurowanie usługi SAP SNC w ramach połączenia](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Właściwość | Opis |
   |----------| ------------|
   | **Ścieżka biblioteki SNC** | Nazwa biblioteki SNC lub ścieżka względem lokalizacji instalacji NCo lub ścieżki bezwzględnej. Przykłady to `sapsnc.dll` or `.\security\sapsnc.dll` lub `c:\security\sapsnc.dll` . |
   | **SNC LOGOWANIE JEDNOKROTNE** | Po nawiązaniu połączenia za pomocą usługi SNC tożsamość SNC jest zwykle używana do uwierzytelniania obiektu wywołującego. Kolejną opcją jest przesłonięcie, aby informacje o użytkowniku i haśle mogły być używane do uwierzytelniania obiektu wywołującego, ale wiersz jest wciąż szyfrowany. |
   | **SNC moją nazwę** | W większości przypadków ta właściwość może zostać pominięta. Zainstalowane rozwiązanie SNC zazwyczaj zna własną nazwę SNC. Tylko w przypadku rozwiązań, które obsługują wiele tożsamości, może być konieczne określenie tożsamości, która ma być używana dla danego miejsca docelowego lub serwera. |
   | **Nazwa partnera SNC** | Nazwa SNC zaplecza. |
   | **SNC jakości ochrony** | Jakość usług, która ma być używana do komunikacji SNC w tym konkretnym miejscu docelowym lub serwerze. Wartość domyślna jest definiowana przez system zaplecza. Wartość maksymalna jest definiowana przez produkt zabezpieczający używany przez SNC. |
   |||

   > [!NOTE]
   > Nie ustawiaj zmiennych środowiskowych SNC_LIB i SNC_LIB_64 na komputerze, na którym masz bramę danych i bibliotekę SNC. Jeśli jest ustawiona, mają pierwszeństwo przed wartością biblioteki SNC przekazaną przez łącznik.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Bezpieczne wpisywanie

Domyślnie podczas tworzenia połączenia SAP jest używane silne wpisywanie w celu sprawdzenia nieprawidłowych wartości przez wykonanie walidacji kodu XML względem schematu. Takie zachowanie może pomóc wykryć problemy wcześniej. Opcja **bezpieczne wpisywanie** jest dostępna w celu zapewnienia zgodności z poprzednimi wersjami i sprawdza tylko długość ciągu. W przypadku wybrania opcji **bezpieczne wpisywanie**typ dats i typ Tims w oprogramowaniu SAP są traktowane jako ciągi, a nie jako odpowiedniki XML `xs:date` i `xs:time` , gdzie `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . Bezpieczne wpisywanie ma wpływ na zachowanie wszystkich generacji schematu, wysyła komunikat dla "wysłano" i "otrzymano" odpowiedź oraz wyzwalacz. 

W przypadku użycia silnego wpisywania (**bezpieczne wpisywanie** nie jest włączone) schemat mapuje typy dats i Tims na bardziej proste typy XML:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

W przypadku wysyłania komunikatów przy użyciu silnego wpisywania odpowiedź DATS i TIMS jest zgodna z formatem pasującego typu XML:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Po włączeniu **bezpiecznego pisania** schemat mapuje typy dats i Tims do pól ciągów XML z ograniczeniami długości, na przykład:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Gdy wiadomości są wysyłane z włączonym **bezpiecznym wpisywaniem** , odpowiedź dats i Tims wygląda następująco:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Scenariusze zaawansowane

### <a name="confirm-transaction-explicitly"></a>Potwierdź jawnie transakcję

Po wysłaniu transakcji do SAP z Logic Apps, ta wymiana odbywa się w dwóch krokach, zgodnie z opisem w dokumencie SAP, [transakcyjnych programów serwera RFC](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Domyślnie Akcja **Wyślij do SAP** obsługuje zarówno procedurę transferu funkcji, jak i potwierdzenie transakcji w pojedynczym wywołaniu. Łącznik SAP oferuje opcję oddzielenia tych kroków. Można wysłać IDoc i zamiast automatycznie potwierdzić transakcję, można użyć akcji jawnego **potwierdzenia identyfikatora transakcji** .

Ta możliwość rozdzielenia potwierdzenia identyfikatora transakcji jest przydatna, gdy nie chcesz duplikować transakcji w oprogramowaniu SAP, na przykład w scenariuszach, w których mogą wystąpić awarie wynikające z przyczyn takich jak problemy z siecią. Dzięki potwierdzeniu oddzielnego identyfikatora transakcji transakcja jest wykonywana tylko raz w systemie SAP.

Oto przykład, który pokazuje następujący wzorzec:

1. Utwórz pustą aplikację logiki i Dodaj wyzwalacz HTTP.

1. W łączniku SAP Dodaj akcję **Wyślij IDOC** . Podaj szczegóły dotyczące IDoc wysyłanego do systemu SAP.

1. Aby jawnie potwierdzić identyfikator transakcji w osobnym kroku, w polu Potwierdź numer **TID** wybierz pozycję **nie**. Dla pola opcjonalne **Identyfikator GUID transakcji** można ręcznie określić wartość lub łącznik automatycznie generować i zwracać ten identyfikator GUID w odpowiedzi z akcji Wyślij IDOC.

   ![Wyślij właściwości akcji IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Aby jawnie potwierdzić identyfikator transakcji, Dodaj akcję **Potwierdź identyfikator transakcji** . Kliknij wewnątrz pola **Identyfikator transakcji** , aby wyświetlić listę zawartości dynamicznej. Z tej listy wybierz wartość **identyfikatora transakcji** zwracaną z akcji **Wyślij IDOC** .

   ![Akcja potwierdzenia identyfikatora transakcji](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Po uruchomieniu tego kroku bieżąca transakcja jest oznaczona jako ukończona na obu końcach, po stronie łącznika SAP i po stronie systemowej SAP.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Poniżej przedstawiono obecnie znane problemy i ograniczenia dotyczące zarządzanego łącznika SAP (ISE):

* Wyzwalacz SAP nie obsługuje klastrów bramy danych. W niektórych przypadkach pracy awaryjnej węzeł bramy danych, który komunikuje się z systemem SAP, może różnić się od aktywnego węzła, co powoduje nieoczekiwane zachowanie. W przypadku scenariuszy wysyłania obsługiwane są klastry usługi Data Gateway.

* Łącznik SAP nie obsługuje obecnie ciągów routera SAP. Lokalna Brama danych musi znajdować się w tej samej sieci LAN co system SAP, który chcesz połączyć.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej szczegółowych informacji technicznych dotyczących tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Następne kroki

* [Nawiąż połączenie z systemami lokalnymi](../logic-apps/logic-apps-gateway-connection.md) z poziomu Azure Logic Apps.
* Informacje na temat sprawdzania poprawności, przekształcania i używania innych operacji na komunikatach przy użyciu [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md).
