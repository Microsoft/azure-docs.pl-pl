---
title: Łączenie z systemami SAP
description: Dostęp do zasobów SAP i zarządzanie nimi przez Automatyzowanie przepływów pracy za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/21/2020
tags: connectors
ms.openlocfilehash: 4afd6f0cc3b4b5e135d80b420d8260c50d9ca46c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488851"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Łączenie z systemami SAP z usługi Azure Logic Apps

> [!IMPORTANT]
> Wcześniejsze łączniki serwera aplikacji SAP i serwera komunikatów SAP są przestarzałe 29 lutego 2020. Bieżący łącznik SAP konsoliduje te poprzednie łączniki SAP, aby nie trzeba było zmieniać typu połączenia, jest w pełni zgodny z poprzednimi łącznikami, zapewnia wiele dodatkowych możliwości i nadal korzysta z biblioteki łącznika SAP .NET (SAP NCo).
>
> W przypadku aplikacji logiki korzystających ze starszych łączników należy [przeprowadzić migrację do najnowszego łącznika](#migrate) przed datą zakończenia. W przeciwnym razie te aplikacje logiki będą powodować błędy wykonywania i nie będą mogły wysyłać komunikatów do systemu SAP.

W tym artykule pokazano, jak można uzyskać dostęp do lokalnych zasobów SAP z wewnątrz aplikacji logiki przy użyciu łącznika SAP. Łącznik współpracuje z klasycznymi wersjami oprogramowania SAP, takimi jak R/3 i systemu ECC w środowisku lokalnym. Ponadto łącznik umożliwia integrację z nowszymi systemami SAP opartymi na rozwiązaniu HANA, takimi jak S/4 HANA, niezależnie od tego, czy są hostowane lokalnie, czy w chmurze. Łącznik SAP obsługuje integrację komunikatów lub danych wysyłanych do systemów opartych na oprogramowaniu SAP NetWeaver i z nich za pośrednictwem dokumentu przejściowego (IDoc), interfejsu programowania aplikacji biznesowych (BAPI) lub zdalnego wywołania funkcji (RFC).

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

* Zawartość wiadomości wysyłana do serwera SAP, taka jak przykładowy plik IDoc, musi być w formacie XML i zawierać przestrzeń nazw dla akcji SAP, która ma zostać użyta.

* Aby użyć, **gdy komunikat jest odbierany z** wyzwalacza SAP, należy również wykonać następujące czynności konfiguracyjne:
  
  > [!NOTE]
  > Ten wyzwalacz używa tej samej lokalizacji identyfikatora URI do odnawiania i anulowania subskrypcji elementu webhook. Operacja odnowienia używa metody HTTP `PATCH` , podczas gdy operacja anulowania subskrypcji używa `DELETE` metody http. Takie zachowanie może spowodować, że operacja odnowienia zostanie wyświetlona jako operacja anulowania subskrypcji w historii wyzwalacza, ale operacja nadal jest odnawiana, ponieważ wyzwalacz używa `PATCH` metody http, a `DELETE` nie.

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

   * libicudecnumber.dll
   * rscp4n.dll
   * sapnco.dll
   * sapnco_utils.dll

1. Utwórz plik. zip, który zawiera te zestawy, i przekaż ten pakiet do kontenera obiektów BLOB w usłudze Azure Storage.

1. W Azure Portal lub Eksplorator usługi Azure Storage przejdź do lokalizacji kontenera, do której został przekazany plik. zip.

1. Skopiuj adres URL dla tej lokalizacji, upewniając się, że dołączysz token sygnatury dostępu współdzielonego (SAS).

   W przeciwnym razie token sygnatury dostępu współdzielonego nie zostanie autoryzowany, a wdrożenie łącznika SAP ISE zakończy się niepowodzeniem.

1. Aby można było używać łącznika SAP ISE, należy zainstalować i wdrożyć łącznik w ISE.

   1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz ISE.
   
   1. W menu ISE wybierz pozycję **Łączniki zarządzane**  >  **Dodaj**. Z listy łączniki Znajdź i wybierz pozycję **SAP**.
   
   1. W okienku **Dodaj nowe łączniki zarządzane** w polu **pakiet SAP** Wklej adres URL pliku zip, który zawiera zestawy SAP. *Upewnij się, że dołączysz token sygnatury dostępu współdzielonego.*

   1. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   Aby uzyskać więcej informacji, zobacz [Dodawanie łączników ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

1. Jeśli wystąpienie oprogramowania SAP i ISE znajdują się w różnych sieciach wirtualnych, należy również połączyć [te sieci równorzędne](../virtual-network/tutorial-connect-virtual-networks-portal.md) , aby Sieć wirtualna ISE była połączona z siecią wirtualną Twojego wystąpienia SAP.

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>Wymagania wstępne dotyczące biblioteki klienta SAP

* Upewnij się, że zainstalowano najnowszą wersję, [Łącznik SAP (NCo 3,0) dla Microsoft .NET 3.0.22.0 skompilowany przy użyciu .NET Framework 4,0 — Windows 64-bit (x64)](https://support.sap.com/en/product/connectors/msnet.html). Wcześniejsze wersje mogą powodować problemy ze zgodnością. Aby uzyskać więcej informacji, zobacz [wersje biblioteki klienta SAP](#sap-library-versions).

* Domyślnie Instalator SAP umieszcza pliki zestawu w domyślnym folderze instalacji. Należy skopiować te pliki zestawu do innej lokalizacji w zależności od scenariusza w następujący sposób:

  * W przypadku aplikacji logiki, które są uruchamiane w ISE, wykonaj kroki opisane w sekcji [wymagania wstępne dotyczące środowiska usługi integracji](#sap-ise). W przypadku aplikacji logiki, które działają na platformie Azure z wieloma dzierżawcami i korzystają z lokalnej bramy danych, skopiuj pliki zestawu z domyślnego folderu instalacji do folderu instalacji usługi Data Gateway. Jeśli wystąpią problemy z bramą danych, zapoznaj się z następującymi problemami:

  * Należy zainstalować 64-bitową wersję dla biblioteki klienta SAP, ponieważ Brama danych działa tylko w systemach 64-bitowych. W przeciwnym razie zostanie wyświetlony komunikat o błędzie "zły obraz", ponieważ usługa hosta bramy danych nie obsługuje zestawów 32-bitowych.

  * Jeśli połączenie SAP zakończy się niepowodzeniem z komunikatem o błędzie "Sprawdź informacje o koncie i/lub uprawnienia i spróbuj ponownie", pliki zestawu mogą znajdować się w niewłaściwej lokalizacji. Upewnij się, że skopiowano pliki zestawu do folderu instalacji bramy danych.

    Aby pomóc w rozwiązywaniu problemów, [Użyj przeglądarki dzienników powiązań zestawu .NET](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), która pozwala sprawdzić, czy pliki zestawu znajdują się we właściwym miejscu. Opcjonalnie można wybrać opcję **rejestracji globalnej pamięci podręcznej zestawów** podczas instalowania biblioteki klienta SAP.

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
> Gdy aplikacja logiki odbiera pakiety IDoc z oprogramowania SAP, [wyzwalacz żądania](../connectors/connectors-native-reqres.md) nie obsługuje "zwykłego" schematu XML wygenerowanego przez dokumentację WE60 IDOC firmy SAP. Jednak schemat XML "zwykły" jest obsługiwany w scenariuszach, które wysyłają komunikaty z aplikacji logiki *do* oprogramowania SAP. Można użyć wyzwalacza żądania z IDoc XML programu SAP, ale nie z IDocem w dokumencie RFC. Można też przekształcić kod XML w niezbędny format. 

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

      Aby uzyskać więcej informacji o operacjach IDoc, zobacz [schematy komunikatów dla operacji IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

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

#### <a name="add-rfc-request-response"></a>Dodaj odpowiedź na żądanie RFC

> [!NOTE]
> Wyzwalacz SAP odbiera IDocs przez tRFC, który nie ma parametru odpowiedzi według konstrukcji. 

Należy utworzyć wzorzec żądania i odpowiedzi, jeśli trzeba otrzymywać odpowiedzi przy użyciu zdalnego wywołania funkcji (RFC) do Logic Apps z platformy SAP ABAP. Aby odebrać IDocs w aplikacji logiki, należy wykonać swoją pierwszą akcję jako [żądanie HTTP](../connectors/connectors-native-reqres.md#add-a-response-action) z kodem stanu `200 OK` i bez zawartości. Ten zalecany krok polega na zakończeniu natychmiastowego transferu SAP LUW przez tRFC, co spowoduje ponowne udostępnienie konwersacji SAP CPIC. Następnie można dodać dalsze akcje w aplikacji logiki, aby przetwarzać odebrane IDoc bez blokowania dalszych transferów.

Aby zaimplementować wzorzec żądania i odpowiedzi, najpierw należy wykryć schemat RFC przy użyciu [ `generate schema` polecenia](#generate-schemas-for-artifacts-in-sap). Wygenerowany schemat ma dwa możliwe węzły główne: 

1. Węzeł żądania, który jest wywołaniem otrzymanym od SAP.
1. Węzeł odpowiedzi, który jest odpowiedzią z powrotem do SAP.

W poniższym przykładzie wzorzec żądania i odpowiedzi jest generowany na podstawie `STFC_CONNECTION` modułu RFC. KOD XML żądania jest analizowany w celu wyodrębnienia wartości węzła, w której są wysyłane żądania SAP `<ECHOTEXT>` . Odpowiedź Wstawia bieżącą sygnaturę czasową jako wartość dynamiczną. Po wysłaniu `STFC_CONNECTION` specyfikacji RFC z aplikacji logiki do oprogramowania SAP otrzymujesz podobną odpowiedź.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

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

   Można [filtrować komunikaty otrzymane z serwera SAP przez określenie listy akcji SAP](#filter-with-sap-actions).

   Można wybrać akcję SAP z selektora plików:

   ![Dodawanie akcji SAP do aplikacji logiki](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Lub można ręcznie określić akcję:

   ![Ręcznie wprowadź akcję SAP, która ma być używana](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Oto przykład pokazujący, jak akcja pojawia się po skonfigurowaniu wyzwalacza tak, aby otrzymywał więcej niż jeden komunikat.

   ![Przykład wyzwalacza, który odbiera wiele komunikatów](media/logic-apps-using-sap-connector/example-trigger.png)

   Aby uzyskać więcej informacji na temat akcji SAP, zobacz [schematy komunikatów dla operacji IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

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

<a name="filter-with-sap-actions"></a>

#### <a name="filter-with-sap-actions"></a>Filtrowanie za pomocą akcji SAP

Opcjonalnie można filtrować komunikaty odbierane z serwera SAP przez aplikację logiki, dostarczając listę lub tablicę z jedną lub wieloma akcjami SAP. Domyślnie ta tablica jest pusta, co oznacza, że aplikacja logiki odbiera wszystkie komunikaty z serwera SAP bez filtrowania. 

Po skonfigurowaniu filtru tablicy wyzwalacz odbiera tylko komunikaty z określonych typów akcji SAP i odrzuca wszystkie inne komunikaty z serwera SAP. Jednak ten filtr nie ma wpływu na to, czy wpisywanie odebranego ładunku jest słabe czy silne.

Wszelkie filtrowanie akcji SAP odbywa się na poziomie karty SAP dla lokalnej bramy danych. Aby uzyskać więcej informacji, zobacz [jak wysyłać test IDocs do Logic Apps z oprogramowania SAP](#send-idocs-from-sap).

Jeśli nie możesz wysyłać pakietów IDoc z oprogramowania SAP do wyzwalacza aplikacji logiki, zapoznaj się z komunikatem o odrzuceniu wywołania transakcyjnego RFC (tRFC) w oknie dialogowym SAP tRFC (T-Code SM58). W interfejsie SAP można uzyskać następujące komunikaty o błędach, które są przycinane z powodu limitów podciągów w polu **tekstowym stan** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: Nieoczekiwane błędy występują, gdy procedura obsługi catch dla kanału kończy działanie kanału z powodu błędu i ponownie kompiluje kanał w celu przetworzenia innych komunikatów.

  * Aby potwierdzić, że aplikacja logiki otrzymała IDoc, [Dodaj akcję odpowiedzi](../connectors/connectors-native-reqres.md#add-a-response-action) , która zwraca `200 OK` kod stanu. IDoc jest transportowana przez tRFC, która nie zezwala na ładunek odpowiedzi.

  * Jeśli chcesz odrzucić IDoc zamiast tego, Odpowiedz przy użyciu dowolnego kodu stanu HTTP innego niż `200 OK` tak, aby karta SAP zwracała wyjątek z powrotem do SAP w Twoim imieniu. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: Oczekiwane błędy są spowodowane innymi błędami, takimi jak błąd generowania ładunku IDoc XML, ponieważ jego segmenty nie są uwalniane przez system SAP, więc brakuje metadanych typu segmentu wymaganych do konwersji. 

  * Aby zwolnić te segmenty przez SAP, należy skontaktować się z inżynierem ABAP w systemie SAP.

<a name="find-extended-error-logs"></a>

## <a name="find-extended-error-logs"></a>Znajdowanie rozszerzonych dzienników błędów

W przypadku pełnych komunikatów o błędach Sprawdź rozszerzone dzienniki karty SAP. 

W przypadku wersji lokalnej bramy danych z czerwca 2020 i nowszych można [włączyć dzienniki bramy w ustawieniach aplikacji](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app).

W przypadku wersji lokalnej bramy danych z kwietnia 2020 i wcześniejszych dzienniki są domyślnie wyłączone. Aby pobrać dzienniki rozszerzone, wykonaj następujące kroki:

1. Otwórz plik w folderze instalacyjnym lokalnej bramy danych `Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` . 

1. Dla ustawienia **SapExtendedTracing** Zmień wartość z **false** na **true**.

1. Opcjonalnie w przypadku mniejszych zdarzeń Zmień wartość **SapTracingLevel** z **informacyjnego** (domyślnie) na **błąd** lub **Ostrzeżenie**. Lub, aby uzyskać więcej zdarzeń, Zmień **informacje** na **pełne**.

1. Zapisz plik konfiguracji.

1. Uruchom ponownie bramę Data Gateway. Otwórz aplikację instalatora lokalnej bramy danych i przejdź do menu **Ustawienia usługi** . W obszarze **Uruchom ponownie bramę**wybierz pozycję **Uruchom ponownie teraz**.

1. Odtwórz problem.

1. Wyeksportuj dzienniki bramy. W aplikacji Instalatora bramy danych przejdź do menu **Diagnostyka** . W obszarze **dzienniki bramy**wybierz pozycję **Eksportuj dzienniki**. Te pliki obejmują dzienniki SAP uporządkowane według daty. W zależności od rozmiaru dziennika może istnieć wiele plików dziennika dla jednej daty.

1. W pliku konfiguracji Przywróć ustawienie **SapExtendedTracing** na **wartość false**.

1. Uruchom ponownie usługę bramy.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Aby wyzwolić aplikację logiki, Wyślij komunikat z systemu SAP.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. Przejrzyj **historię uruchomień** dla wszystkich nowych przebiegów dla aplikacji logiki.

1. Otwórz najnowszy przebieg, który pokazuje komunikat wysłany z systemu SAP w sekcji dane wyjściowe wyzwalacza.

<a name="send-idocs-from-sap"></a>

### <a name="test-sending-idocs-from-sap"></a>Testowanie wysyłania IDocs z oprogramowania SAP

Aby wysłać IDocs z oprogramowania SAP do aplikacji logiki, wymagana jest następująca minimalna konfiguracja:

> [!IMPORTANT]
> Te kroki należy wykonać tylko podczas testowania konfiguracji SAP za pomocą aplikacji logiki. Środowiska produkcyjne wymagają dodatkowej konfiguracji.

1. [Konfigurowanie miejsca docelowego RFC w oprogramowaniu SAP](#create-rfc-destination)

1. [Tworzenie połączenia ABAP z miejscem docelowym RFC](#create-abap-connection)

1. [Tworzenie portu odbiornika](#create-receiver-port)

1. [Tworzenie portu nadawcy](#create-sender-port)

1. [Tworzenie partnera systemu logicznego](#create-logical-system-partner)

1. [Tworzenie profilu partnera](#create-partner-profiles)

1. [Testowanie wysyłania komunikatów](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Utwórz miejsce docelowe RFC

1. Aby otworzyć **konfigurację ustawień połączeń RFC** , w interfejsie SAP Użyj kodu transakcji **sm59** (T-Code) z prefiksem **/n** .

1. Wybierz pozycję **połączenia TCP/IP**  >  **Utwórz**.

1. Utwórz nową lokalizację docelową RFC z następującymi ustawieniami:
    
    * Dla **obiektu docelowego RFC**wprowadź nazwę.
    
    * Na karcie **Ustawienia techniczne** w **polu Typ aktywacji**wybierz pozycję **zarejestrowany serwer programu**. Wprowadź wartość w obszarze **Identyfikator programu**. W oprogramowaniu SAP wyzwalacz aplikacji logiki zostanie zarejestrowany przy użyciu tego identyfikatora.
    
    * Na karcie **Unicode** w **polu Typ komunikacji z systemem docelowym**wybierz opcję **Unicode**.

1. Zapisz zmiany.

1. Zarejestruj nowy **Identyfikator programu** przy użyciu Azure Logic Apps.

1. Aby przetestować połączenie, w interfejsie SAP pod nową **lokalizacją docelową RFC**wybierz pozycję **test połączenia**.

#### <a name="create-abap-connection"></a>Utwórz połączenie ABAP

1. Aby otworzyć **konfigurację ustawień połączeń RFC** , w interfejsie SAP Użyj kodu transakcji **Sm59*** (T-Code) z prefiksem **/n** .

1. Wybierz pozycję **połączenia ABAP**  >  **Utwórz**.

1. Dla elementu **docelowego RFC**wprowadź identyfikator [testowego systemu SAP](#create-rfc-destination).

1. Zapisz zmiany.

1. Aby przetestować połączenie, wybierz pozycję **Testuj połączenie**.

#### <a name="create-receiver-port"></a>Utwórz port odbiornika

1. Aby otworzyć **porty w ustawieniach przetwarzania IDOC** , w interfejsie SAP Użyj kodu transakcji **WE21** (T-Code) z prefiksem **/n** .

1. Wybierz kolejno pozycje **porty**  >  **transakcyjny dokument RFC**  >  **Create**.

1. W otwartym oknie Ustawienia wybierz pozycję **Nazwa własnych portów**. Dla portu testowego wprowadź **nazwę**. Zapisz zmiany.

1. W obszarze Ustawienia dla nowego portu odbiornika dla elementu **docelowego RFC**wprowadź identyfikator dla [testowanego miejsca docelowego RFC](#create-rfc-destination).

1. Zapisz zmiany.

#### <a name="create-sender-port"></a>Utwórz port nadawcy

1.  Aby otworzyć **porty w ustawieniach przetwarzania IDOC** , w interfejsie SAP Użyj kodu transakcji **WE21** (T-Code) z prefiksem **/n** .

1. Wybierz kolejno pozycje **porty**  >  **transakcyjny dokument RFC**  >  **Create**.

1. W otwartym oknie Ustawienia wybierz pozycję **Nazwa własnych portów**. Dla portu testowego wprowadź **nazwę** rozpoczynającą się od **SAP**. Wszystkie nazwy portów nadawcy muszą zaczynać się literą **SAP**, na przykład **SAPTEST**. Zapisz zmiany.

1. W obszarze Ustawienia dla nowego portu nadawcy dla elementu **docelowego RFC**wprowadź identyfikator [połączenia usługi ABAP](#create-abap-connection).

1. Zapisz zmiany.

#### <a name="create-logical-system-partner"></a>Utwórz partnera systemu logicznego

1. Aby otworzyć **Widok zmian "systemy logiczne": Omówienie** ustawień w interfejsie SAP, użyj kodu transakcji **bd54** (T-Code).

1. Zaakceptuj wyświetlony komunikat ostrzegawczy: **Przestroga: tabela jest klientem krzyżowym**

1. Nad listą, w której znajdują się istniejące systemy logiczne, wybierz pozycję **nowe wpisy**.

1. W przypadku nowego systemu logicznego wprowadź identyfikator **Log.System** i opis krótkiej **nazwy** . Zapisz zmiany.

1. Po wyświetleniu **monitu o Workbench** Utwórz nowe żądanie, podając jego opis, lub jeśli zostało już utworzone żądanie, Pomiń ten krok.

1. Po utworzeniu żądania Workbench należy połączyć to żądanie z żądaniem aktualizacji tabeli. Aby potwierdzić, że tabela została zaktualizowana, Zapisz zmiany.

#### <a name="create-partner-profiles"></a>Tworzenie profilów partnera

W przypadku środowisk produkcyjnych należy utworzyć dwa profile partnerskie. Pierwszy profil jest przeznaczony dla nadawcy, który jest używany przez organizację i system SAP. Drugi profil jest przeznaczony dla odbiornika, który jest aplikacją logiki.

1. Aby otworzyć ustawienia **profilów partnera** , w interfejsie SAP Użyj kodu transakcji **WE20** (T-Code) z prefiksem **/n** .

1. W obszarze **Profile partnera**wybierz pozycję **Typ partnera**  >  **Utwórz**.

1. Utwórz nowy profil partnera przy użyciu następujących ustawień:

    * W obszarze **Nr partnera**wprowadź [Identyfikator partnera systemu logicznego](#create-logical-system-partner).

    * Dla **partn. Wpisz**, wprowadź **ls**.

    * W polu **Agent**wprowadź identyfikator konta użytkownika SAP, który ma być używany podczas rejestrowania identyfikatorów programu dla Azure Logic Apps lub innych systemów innych niż SAP.

1. Zapisz zmiany. Jeśli nie [utworzono partnera systemu logicznego](#create-logical-system-partner), występuje błąd, **Wprowadź prawidłowy numer partnera**.

1. W ustawieniach profilu partnera w obszarze **wychodzące parmtrs**wybierz pozycję **Utwórz parametr wychodzący**.

1. Utwórz nowy parametr wychodzący z następującymi ustawieniami:

    * Wprowadź **Typ wiadomości**, na przykład **CREMAS**.

    * Wprowadź [Identyfikator portu odbiornika](#create-receiver-port).

    * Wprowadź rozmiar IDoc dla **pakietu. Rozmiar**. Lub, aby [wysłać IDocs jeden na raz z SAP](#receive-idoc-packets-from-sap), wybierz **natychmiast opcję Przekaż IDOC**.

1. Zapisz zmiany.

#### <a name="test-sending-messages"></a>Testowanie wysyłania komunikatów

1. Aby otworzyć **Narzędzie testowe dla ustawień przetwarzania IDOC** , w interfejsie SAP Użyj kodu transakcji **WE19** (T-Code) z prefiksem **/n** .

1. W obszarze **szablon do testowania**wybierz pozycję **za pośrednictwem pozycji typ komunikatu**, a następnie wprowadź typ wiadomości, na przykład **CREMAS**. Wybierz przycisk **Utwórz**.

1. Potwierdź, **który typ IDOC?** komunikat, wybierając pozycję **Kontynuuj**.

1. Wybierz węzeł **EDIDC** . Wprowadź odpowiednie wartości dla swojego odbiornika i portów nadawcy. Wybierz pozycję **Continue** (Kontynuuj).

1. Wybierz **standardowe przetwarzanie wychodzące**.

1. Aby rozpocząć przetwarzanie IDoc wychodzącego, wybierz pozycję **Kontynuuj**. Po zakończeniu przetwarzania zostanie wyświetlony komunikat **IDOC wysyłany do systemu SAP lub programu zewnętrznego** .

1.  Aby sprawdzić, czy są przetwarzane błędy, użyj kodu transakcji **SM58** (T-Code) z prefiksem **/n** .

## <a name="receive-idoc-packets-from-sap"></a>Odbieranie pakietów IDoc z oprogramowania SAP

Można skonfigurować SAP, aby [wysyłał IDocs w pakietach](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), które są partiami lub grupami IDocs. Do odbierania pakietów IDoc, łącznika SAP i konkretnego wyzwalacza nie jest wymagana dodatkowa konfiguracja. Jednak, aby przetwarzać każdy element w pakiecie IDoc po odebraniu pakietu przez wyzwalacz, należy wykonać pewne dodatkowe kroki, aby podzielić pakiet na poszczególne IDocs.

Oto przykład, który pokazuje, jak wyodrębnić poszczególne IDocs z pakietu przy użyciu [ `xpath()` funkcji](./workflow-definition-language-functions-reference.md#xpath):

1. Przed rozpoczęciem potrzebna jest aplikacja logiki z wyzwalaczem SAP. Jeśli nie masz jeszcze tej aplikacji logiki, wykonaj kroki opisane w tym temacie, aby [skonfigurować aplikację logiki z wyzwalaczem SAP](#receive-from-sap).

   Na przykład:

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

W tym przykładzie zastosowano aplikację logiki, którą można wyzwolić za pomocą żądania HTTP. Aby wygenerować schematy dla określonych IDoc i interfejsu BAPI, Akcja SAP **Generuj schemat** wysyła żądanie do systemu SAP.

Ta akcja SAP zwraca [schemat XML](#sample-xml-schemas), a nie zawartość lub dane dokumentu XML. Schematy zwrócone w odpowiedzi są przekazywane do konta integracji przy użyciu łącznika Azure Resource Manager. Schematy zawierają następujące części:

* Struktura komunikatu żądania. Te informacje służą do tworzenia listy interfejsu BAPI `get` .
* Struktura komunikatu odpowiedzi. Użyj tych informacji, aby przeanalizować odpowiedź. 

Aby wysłać komunikat żądania, użyj ogólnej akcji SAP **Wyślij komunikat do SAP**lub zamierzone akcje **interfejsu BAPI wywołania** .

### <a name="sample-xml-schemas"></a>Przykładowe schematy XML

Jeśli wiesz, jak wygenerować schemat XML do użycia podczas tworzenia przykładowego dokumentu, zobacz następujące przykłady. W poniższych przykładach pokazano, jak można współpracować z wieloma rodzajami ładunków, w tym:

* [Żądania RFC](#xml-samples-for-rfc-requests)
* [Żądania interfejsu BAPI](#xml-samples-for-bapi-requests)
* [Żądania IDoc](#xml-samples-for-idoc-requests)
* Proste lub złożone typy danych schematu XML
* Parametry tabeli
* Opcjonalne zachowania XML

Schemat XML można rozpocząć z opcjonalnym prologem XML. Łącznik SAP współpracuje z lub bez użycia prologu XML.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>Przykłady XML dla żądań RFC

Poniższy przykład to podstawowe wywołanie RFC. Nazwa specyfikacji RFC to `STFC_CONNECTION` . To żądanie używa domyślnej przestrzeni nazw `xmlns=` , jednak można przypisywać i używać aliasów przestrzeni nazw, takich jak `xmmlns:exampleAlias=` . Wartość przestrzeni nazw jest przestrzenią nazw dla wszystkich dokumentów RFC w oprogramowaniu SAP dla usług firmy Microsoft. W żądaniu istnieje prosty parametr wejściowy `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

Poniższy przykład to wywołanie RFC z parametrem tabeli. Tego przykładowego wywołania i jego grupy testów RFC są dostępne jako część wszystkich systemów SAP. Nazwa parametru tabeli to `TCPICDAT` . Typ wiersza tabeli to `ABAPTEXT` , a ten element powtarza się dla każdego wiersza w tabeli. Ten przykład zawiera pojedynczy wiersz o nazwie `LINE` . Żądania z parametrem tabeli mogą zawierać dowolną liczbę pól, gdzie liczba jest dodatnią liczbą całkowitą (*n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

Poniższy przykład to wywołanie RFC z parametrem tabeli, który ma pole anonimowe. Pole anonimowe ma wartość, gdy pole nie ma przypisanej nazwy. Typy złożone są zadeklarowane w oddzielnym obszarze nazw, w którym deklaracja ustawia nową wartość domyślną dla bieżącego węzła i wszystkich jego elementów podrzędnych. W przykładzie zastosowano kod szesnastkowy `x002F` jako znak ucieczki dla symbolu */* , ponieważ ten symbol jest zarezerwowany w nazwie pola SAP.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

Poniższy przykład zawiera prefiksy dla przestrzeni nazw. Można zadeklarować wszystkie prefiksy jednocześnie lub można zadeklarować dowolną liczbę prefiksów jako atrybuty węzła. Alias przestrzeni nazw RFC `ns0` jest używany jako element główny i parametry dla typu podstawowego.

> [!NOTE]
> typy złożone są deklarowane pod inną przestrzenią nazw dla typów RFC o aliasie `ns3` zamiast zwykłej przestrzeni nazw RFC z aliasem `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>Przykłady XML dla żądań interfejsu BAPI

> [!TIP]
> Jeśli używasz projektanta Logic Apps do edytowania żądania interfejsu BAPI, możesz użyć następujących funkcji wyszukiwania: 
> 
> * Wybierz obiekt w projektancie, aby wyświetlić menu rozwijane dostępnych metod.
> * Filtruj typy obiektów biznesowych według słowa kluczowego za pomocą listy z możliwością przeszukiwania dostarczaną przez wywołanie API interfejsu BAPI.

> [!NOTE]
> SAP sprawia, że obiekty biznesowe są dostępne dla systemów zewnętrznych przez opisywanie ich w odpowiedzi na specyfikację RFC `RPY_BOR_TREE_INIT` , która Logic Apps problemy bez filtru wejściowego. Logic Apps sprawdza tabelę wyjściową `BOR_TREE` . To `SHORT_TEXT` pole jest używane dla nazw obiektów firmowych. Obiekty biznesowe, które nie są zwracane przez SAP w tabeli wyjściowej, nie są dostępne do Logic Apps.
> Jeśli używasz niestandardowych obiektów biznesowych, musisz upewnić się, że publikujesz i wystawią te obiekty biznesowe w oprogramowaniu SAP. W przeciwnym razie SAP nie wyświetla niestandardowych obiektów biznesowych w tabeli wyjściowej `BOR_TREE` . Nie można uzyskać dostępu do niestandardowych obiektów biznesowych w Logic Apps, dopóki nie zostaną ujawnione obiekty biznesowe z platformy SAP. 

Poniższy przykład pobiera listę banków przy użyciu metody interfejsu BAPI `GETLIST` . Ten przykład zawiera obiekt biznesowy dla banku, `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

Poniższy przykład tworzy obiekt Bank przy użyciu `CREATE` metody. Ten przykład używa tego samego obiektu biznesowego, co w poprzednim przykładzie `BUS1011` . Jeśli używasz `CREATE` metody do utworzenia banku, pamiętaj o zatwierdzeniu zmian, ponieważ ta metoda nie jest domyślnie zatwierdzana.

> [!TIP]
> Upewnij się, że dokument XML jest zgodny z regułami walidacji skonfigurowanymi w systemie SAP. Na przykład w tym przykładowym dokumencie klucz banku ( `<BANK_KEY>` ) musi być numerem rozliczeniowym banku, znanym również jako numer ABA w Stanach Zjednoczonych.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

Poniższy przykład pobiera szczegóły dla banku przy użyciu numeru rozliczeniowego banku, wartości dla `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>Przykłady XML dla żądań IDoc

Aby wygenerować zwykły schemat XML środowiska SAP IDoc, użyj aplikacji **logowania SAP** i T-Code `WE-60` . Uzyskaj dostęp do dokumentacji SAP za pomocą graficznego interfejsu użytkownika i Generuj schematy XML w formacie XSD dla typów i rozszerzeń IDoc. Aby uzyskać wyjaśnienie ogólnych formatów i ładunków SAP oraz wbudowanych okien dialogowych, zapoznaj się z [dokumentacją SAP](https://help.sap.com/viewer/index).

Ten przykład deklaruje węzeł główny i przestrzenie nazw. Identyfikator URI w przykładowym kodzie, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` deklaruje następującą konfigurację:

* `/IDoc` jest główną uwagą dla wszystkich IDocs
* `/3` jest wersją typów rekordów dla wspólnych definicji segmentu
* `/ORDERS05` jest typem IDoc
* `//` jest pustym segmentem, ponieważ nie ma rozszerzenia IDoc
* `/700` jest wersją SAP
* `/Send` jest akcją wysłania informacji do SAP

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

Można powtórzyć ten `idocData` węzeł, aby wysłać partię IDocs w jednym wywołaniu. W poniższym przykładzie istnieje jeden rekord kontrolny, `EDI_DC40` i wiele rekordów danych.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

Poniższy przykład to Przykładowy rekord formantu IDoc, który używa prefiksu `EDI_DC` . Należy zaktualizować wartości, aby były zgodne z instalacją SAP i typem IDoc. Na przykład kod klienta IDoc może nie być `800` . Skontaktuj się z zespołem SAP, aby upewnić się, że używasz prawidłowych wartości instalacji SAP.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

Poniższy przykład to Przykładowy rekord danych z zwykłymi segmentami. W tym przykładzie jest stosowany format daty SAP. Dokumenty o silnym typie mogą korzystać z natywnych formatów daty XML, takich jak `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

Poniższy przykład jest rekordem danych z zgrupowanymi segmentami. Rekord zawiera węzeł nadrzędny grupy, `E2EDKT1002GRP` i wiele węzłów podrzędnych, w tym `E2EDKT1002` i `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

Zalecaną metodą jest utworzenie identyfikatora IDoc do użycia z tRFC. Można ustawić ten identyfikator transakcji, `tid` używając [operacji wysyłania IDOC](/connectors/sap/#send-idoc) w interfejsie API łącznika SAP.

Poniższy przykład jest alternatywną metodą ustawiania identyfikatora transakcji lub `tid` . W tym przykładzie ostatni węzeł segmentu rekordu danych i węzeł danych IDoc są zamknięte. Następnie identyfikator GUID, `guid` , jest używany jako identyfikator tRFC do wykrywania duplikatów. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

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

   Aby uzyskać więcej informacji na temat akcji SAP, zobacz [schematy komunikatów dla operacji IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

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

### <a name="change-language-headers"></a>Zmień nagłówki języka

Po nawiązaniu połączenia z programem SAP z Logic Apps, językiem domyślnym dla połączenia jest angielski. Możesz ustawić język dla połączenia przy użyciu [standardowego nagłówka `Accept-Language` http](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) z żądaniami przychodzącymi.

> [!TIP]
> Większość przeglądarek internetowych dodaje `Accept-Language` nagłówek na podstawie ustawień użytkownika. Przeglądarka sieci Web stosuje ten nagłówek podczas tworzenia nowego połączenia SAP w projektancie Logic Apps. Jeśli nie chcesz tworzyć połączeń SAP w preferowanym języku przeglądarki sieci Web, zaktualizuj ustawienia przeglądarki sieci Web tak, aby korzystały z preferowanego języka, lub Utwórz połączenie SAP przy użyciu Azure Resource Manager zamiast projektanta Logic Apps. 

Na przykład można wysłać żądanie z `Accept-Language` nagłówkiem do aplikacji logiki przy użyciu wyzwalacza **żądania HTTP** . Wszystkie akcje w aplikacji logiki otrzymują nagłówek. Następnie SAP używa określonych języków w swoich komunikatach systemowych, takich jak komunikaty o błędach interfejsu BAPI.

Parametry połączenia SAP dla aplikacji logiki nie mają właściwości Language. W przypadku użycia `Accept-Language` nagłówka może zostać wyświetlony następujący błąd: **Sprawdź informacje o koncie i/lub uprawnienia i spróbuj ponownie.** W takim przypadku należy zamiast tego sprawdzić dzienniki błędów składnika SAP. Błąd w rzeczywistości występuje w składniku SAP, który używa nagłówka, więc może zostać wyświetlony jeden z następujących komunikatów o błędach:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Potwierdź jawnie transakcję

Po wysłaniu transakcji do SAP z Logic Apps, ta wymiana odbywa się w dwóch krokach, zgodnie z opisem w dokumencie SAP, [transakcyjnych programów serwera RFC](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Domyślnie Akcja **Wyślij do SAP** obsługuje zarówno procedurę transferu funkcji, jak i potwierdzenie transakcji w pojedynczym wywołaniu. Łącznik SAP oferuje opcję oddzielenia tych kroków. Można wysłać IDoc i zamiast automatycznie potwierdzić transakcję, można użyć akcji jawnego **potwierdzenia identyfikatora transakcji** .

Ta możliwość rozdzielenia potwierdzenia identyfikatora transakcji jest przydatna, gdy nie chcesz duplikować transakcji w oprogramowaniu SAP, na przykład w scenariuszach, w których mogą wystąpić awarie wynikające z przyczyn takich jak problemy z siecią. Dzięki potwierdzeniu oddzielnego identyfikatora transakcji transakcja jest wykonywana tylko raz w systemie SAP.

Oto przykład, który pokazuje następujący wzorzec:

1. Utwórz pustą aplikację logiki i Dodaj wyzwalacz HTTP.

1. W łączniku SAP Dodaj akcję **Wyślij IDOC** . Podaj szczegóły dotyczące IDoc wysyłanego do systemu SAP.

1. Aby jawnie potwierdzić identyfikator transakcji w osobnym kroku, w polu Potwierdź numer **TID** wybierz pozycję **nie**. Dla pola opcjonalne **Identyfikator GUID transakcji** można ręcznie określić wartość lub łącznik automatycznie generować i zwracać ten identyfikator GUID w odpowiedzi z akcji Wyślij IDOC.

   ![Wyślij właściwości akcji IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Aby jawnie potwierdzić identyfikator transakcji, Dodaj akcję **Potwierdź identyfikator transakcji** , aby [uniknąć wysyłania duplikatów IDocs do SAP](#avoid-sending-duplicate-idocs). Kliknij wewnątrz pola **Identyfikator transakcji** , aby wyświetlić listę zawartości dynamicznej. Z tej listy wybierz wartość **identyfikatora transakcji** zwracaną z akcji **Wyślij IDOC** .

   ![Akcja potwierdzenia identyfikatora transakcji](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Po uruchomieniu tego kroku bieżąca transakcja jest oznaczona jako ukończona na obu końcach, po stronie łącznika SAP i po stronie systemowej SAP.

#### <a name="avoid-sending-duplicate-idocs"></a>Unikaj wysyłania duplikatów IDocs

Jeśli wystąpi problem ze zduplikowanymi IDocs wysyłanymi do SAP z aplikacji logiki, wykonaj następujące kroki, aby utworzyć zmienną ciągu, która będzie używana jako identyfikator transakcji IDoc. Tworzenie tego identyfikatora transakcji pomaga zapobiegać duplikowaniu sieci w przypadku problemów, takich jak tymczasowe przerwy, problemy z siecią lub utrata potwierdzeń.

> [!NOTE]
> Systemy SAP zapominają o identyfikatorze transakcji po określonym czasie lub domyślnie przez 24 godziny. W związku z tym w programie SAP nigdy nie można potwierdzić identyfikatora transakcji, jeśli identyfikator lub identyfikator GUID są nieznane.
> Jeśli potwierdzenie identyfikatora transakcji nie powiedzie się, ten błąd wskazuje, że Communication z systemem SAP nie powiodło się, zanim SAP był w stanie potwierdzić potwierdzenie.

1. W projektancie Logic Apps Dodaj **zmienną Inicjuj** akcję do aplikacji logiki. 
1. W edytorze dla **zmiennej inicjowania**akcji skonfiguruj następujące ustawienia. Następnie Zapisz zmiany.
    1. W obszarze **Nazwa**wprowadź nazwę zmiennej. Na przykład `IDOCtransferID`.
    2. W polu **Typ**wybierz pozycję **ciąg** jako typ zmiennej.
    3. W polu **wartość**zaznacz pole tekstowe **wprowadź wartość początkową** , aby otworzyć menu zawartość dynamiczna. Wybierz kartę **wyrażenia** . Na liście funkcji wprowadź funkcję `guid()` . Następnie wybierz przycisk **OK** , aby zapisać zmiany. Pole **wartość** jest teraz ustawione na `guid()` funkcję, która generuje identyfikator GUID.
1. Po akcji **inicjowania zmiennej** Dodaj akcję **Wyślij IDOC**.
1. W edytorze akcji **Wyślij IDOC**skonfiguruj następujące ustawienia. Następnie Zapisz zmiany.
    1. W obszarze **Typ IDOC** wybierz swój typ wiadomości i dla **wejściowego komunikatu IDOC**Określ swój komunikat.
    1. W przypadku **wersji SAP wydania**wybierz wartości konfiguracji SAP.
    1. W obszarze **Typ rekordu wersja**wybierz wartości konfiguracji SAP.
    1. W obszarze **Potwierdź numer TID**wybierz pozycję **nie**.
    1. Wybierz pozycję **Dodaj nowy parametr**  >  **Identyfikator transakcji GUID**. Zaznacz pole tekstowe, aby otworzyć menu zawartość dynamiczna. Na karcie **zmienne** wybierz nazwę utworzonej zmiennej. Na przykład `IDOCtransferID`.
1. Na pasku tytułu akcji **Wyślij IDOC**, wybierz pozycję.. **.**  >  **Ustawienia**. W obszarze **zasady ponawiania**wybierz pozycję **Brak**  >  **gotowe**.
1. Po **wysłaniu akcji IDOC**Dodaj akcję **Potwierdź identyfikator transakcji**.
1. W edytorze dla akcji **Potwierdź identyfikator transakcji**skonfiguruj następujące ustawienia. Następnie Zapisz zmiany.
    1. W polu **Identyfikator transakcji**wprowadź ponownie nazwę zmiennej. Na przykład `IDOCtransferID`.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Poniżej przedstawiono obecnie znane problemy i ograniczenia dotyczące zarządzanego łącznika SAP (ISE):

* Wyzwalacz SAP nie obsługuje klastrów bramy danych. W niektórych przypadkach pracy awaryjnej węzeł bramy danych, który komunikuje się z systemem SAP, może różnić się od aktywnego węzła, co powoduje nieoczekiwane zachowanie. W przypadku scenariuszy wysyłania obsługiwane są klastry usługi Data Gateway.

* Łącznik SAP nie obsługuje obecnie ciągów routera SAP. Lokalna Brama danych musi znajdować się w tej samej sieci LAN co system SAP, który chcesz połączyć.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej szczegółowych informacji technicznych dotyczących tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/sap/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Następne kroki

* [Nawiąż połączenie z systemami lokalnymi](../logic-apps/logic-apps-gateway-connection.md) z poziomu Azure Logic Apps.
* Informacje na temat sprawdzania poprawności, przekształcania i używania innych operacji na komunikatach przy użyciu [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md).
