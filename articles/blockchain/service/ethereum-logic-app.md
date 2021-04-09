---
title: Używanie łącznika Ethereum łańcucha bloków z usługą Azure Logic Apps platformy Azure łańcucha bloków
description: Użyj łącznika łańcucha bloków Ethereum z Azure Logic Apps, aby wyzwolić funkcje kontraktu inteligentnego i odpowiedzieć na zdarzenia inteligentne kontraktu.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 411337908553e58c252a0ed1a42d17f76195c720
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763795"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Używanie łącznika Ethereum łańcucha bloków z Azure Logic Apps

Użyj [łącznika łańcucha bloków Ethereum](/connectors/blockchainethereum/) z [Azure Logic Apps](../../logic-apps/index.yml) , aby wykonywać inteligentne akcje kontraktowe i odpowiadać na zdarzenia inteligentnego kontraktu. W tym artykule wyjaśniono, jak można użyć łącznika Ethereum łańcucha bloków do wysyłania informacji łańcucha bloków do innej usługi lub wywołania funkcji łańcucha bloków. Załóżmy na przykład, że chcesz utworzyć mikrousługi opartej na protokole REST, która zwraca informacje z księgi łańcucha bloków. Za pomocą aplikacji logiki można akceptować żądania HTTP, które wysyłają zapytania do informacji przechowywanych w księdze łańcucha bloków.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj opcjonalne wymagania wstępne [szybkiego startu: użyj Visual Studio Code, aby nawiązać połączenie z siecią konsorcjum usługi Azure łańcucha bloków](connect-vscode.md). Przewodnik Szybki Start przeprowadzi Cię przez instalację [usługi Azure łańcucha bloków Development Kit dla Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) i skonfigurowanie środowiska deweloperskiego łańcucha bloków.
- Jeśli dopiero zaczynasz Azure Logic Apps, rozważ przejrzenie Microsoft Learn modułów, [aby Azure Logic Apps](/learn/modules/intro-to-logic-apps/) i [wywołać interfejs API z poziomu przepływu pracy Logic Apps przy użyciu łącznika niestandardowego](/learn/modules/logic-apps-and-custom-connectors/).

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Azure Logic Apps pomaga zaplanować i zautomatyzować procesy biznesowe i przepływy pracy, gdy trzeba zintegrować systemy i usługi. Najpierw należy utworzyć logikę korzystającą z łącznika Ethereum łańcucha bloków.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Integracja** > **Aplikacja logiki**.
1. W obszarze **Tworzenie aplikacji logiki** podaj szczegółowe informacje o tym, gdzie utworzyć aplikację logiki. Po zakończeniu wybierz pozycję **Utwórz**.

    Aby uzyskać więcej informacji na temat tworzenia aplikacji logiki, zobacz [Tworzenie zautomatyzowanych przepływów pracy za pomocą Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Po wdrożeniu aplikacji przez platformę Azure wybierz zasób aplikacji logiki.
1. W projektancie Logic Apps w obszarze **Szablony** wybierz pozycję **pusta aplikacja logiki**.

Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy.

Łącznik Ethereum łańcucha bloków ma jeden wyzwalacz i kilka akcji. Używany wyzwalacz lub akcja zależy od danego scenariusza. Postępuj zgodnie z sekcją w tym artykule, która najlepiej odpowiada Twojemu scenariuszowi.

Jeśli przepływ pracy:

* Wyzwalane, gdy wystąpi zdarzenie w łańcucha bloków, [Użyj wyzwalacza zdarzenia](#use-the-event-trigger).
* Wykonuje zapytania lub wdraża inteligentne kontraktu, [Użyj akcji](#use-actions).
* W ramach typowego scenariusza [Wygeneruj przepływ pracy za pomocą zestawu deweloperów](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Korzystanie z wyzwalacza zdarzeń

Użyj wyzwalaczy zdarzeń Ethereum łańcucha bloków, jeśli chcesz, aby aplikacja logiki działała po wystąpieniu zdarzenia w ramach kontraktu inteligentnego. Na przykład, chcesz wysłać wiadomość e-mail, gdy wywoływana jest funkcja kontraktu inteligentnego.

1. W projektancie Logic Apps wybierz łącznik Ethereum łańcucha bloków.
1. Na karcie **wyzwalacze** wybierz opcję **kiedy występuje wydarzenie inteligentne kontraktu**.
1. Zmień lub [Utwórz połączenie interfejsu API](#create-an-api-connection) z usługą Azure łańcucha bloków.
1. Wprowadź szczegóły dotyczące kontraktu inteligentnego, który ma być sprawdzany pod kątem zdarzeń.

    ![Projektant Logic Apps z właściwościami wyzwalacza zdarzeń](./media/ethereum-logic-app/event-properties.png)

    | Właściwość | Opis |
    |----------|-------------|
    | **ABI kontraktu** | Interfejs binarny aplikacji kontraktu (ABI) definiuje inteligentne interfejsy kontraktu. Aby uzyskać więcej informacji, zobacz artykuł [pobieranie kontraktu ABI](#get-the-contract-abi). |
    | **Adres kontraktu inteligentnego** | Adres kontraktu jest adresem docelowym kontraktu inteligentnego na Ethereum łańcucha bloków. Aby uzyskać więcej informacji, zobacz [pobieranie adresu kontraktu](#get-the-contract-address). |
    | **Nazwa zdarzenia** | Wybierz inteligentne zdarzenie kontraktu do sprawdzenia. Zdarzenie wyzwala aplikację logiki. |
    | **Interwał** i **częstotliwość** | Wybierz, jak często chcesz sprawdzać obecność zdarzenia. |

1. Wybierz pozycję **Zapisz**.

Aby ukończyć aplikację logiki, można dodać nowy krok wykonujący akcję na podstawie wyzwalacza zdarzenia Ethereum łańcucha bloków. Na przykład Wyślij wiadomość e-mail.

## <a name="use-actions"></a>Korzystanie z akcji

Użyj akcji Ethereum łańcucha bloków, jeśli chcesz, aby aplikacja logiki wykonywała akcję w księdze łańcucha bloków. Na przykład chcesz utworzyć mikrousługi opartej na protokole REST, która wywoła funkcję kontraktu inteligentnego, gdy żądanie HTTP zostanie wysłane do aplikacji logiki.

Akcje łącznika wymagają wyzwalacza. Możesz użyć akcji łącznika Ethereum łańcucha bloków jako następnego kroku po wyzwalaczu, takiego jak wyzwalacz żądania HTTP dla mikrousługi.

1. W projektancie Logic Apps wybierz pozycję **nowy krok** po wyzwalaczu.
1. Wybierz łącznik Ethereum łańcucha bloków.
1. Na karcie **Akcje** wybierz jedną z dostępnych akcji.

    ![Projektant Logic Apps z właściwościami akcji](./media/ethereum-logic-app/action-properties.png)

1. Zmień lub [Utwórz połączenie interfejsu API](#create-an-api-connection) z usługą Azure łańcucha bloków.
1. W zależności od wybranej akcji podaj następujące informacje o funkcji kontraktu inteligentnego.

    | Właściwość | Opis |
    |----------|-------------|
    | **ABI kontraktu** | ABI kontraktu definiuje inteligentne interfejsy kontraktu. Aby uzyskać więcej informacji, zobacz artykuł [pobieranie kontraktu ABI](#get-the-contract-abi). |
    | **Kod bajtowy kontraktu** | Skompilowany kod bajtowy w postaci skompilowanej inteligentnej. Aby uzyskać więcej informacji, zobacz [pobieranie kodu bajtowego kontraktu](#get-the-contract-bytecode). |
    | **Adres kontraktu inteligentnego** | Adres kontraktu jest adresem docelowym kontraktu inteligentnego na Ethereum łańcucha bloków. Aby uzyskać więcej informacji, zobacz [pobieranie adresu kontraktu](#get-the-contract-address). |
    | **Nazwa funkcji kontraktu inteligentnego** | Wybierz nazwę funkcji kontraktu inteligentnego dla akcji. Lista zostanie wypełniona ze szczegółowych informacji w ABI kontraktu. |

    Po wybraniu nazwy funkcji kontraktu inteligentnego może zostać wyświetlonych wymaganych pól parametrów funkcji. Wprowadź wartości lub zawartość dynamiczną wymaganą w danym scenariuszu.

Możesz teraz korzystać z aplikacji logiki. Po wyzwoleniu zdarzenia aplikacji logiki zostanie uruchomione działanie Ethereum łańcucha bloków. Na przykład wyzwalacz żądania HTTP uruchamia akcję Ethereum łańcucha bloków, aby wykonać zapytanie o wartość stanu kontraktu inteligentnego. To zapytanie powoduje odpowiedź HTTP, która zwraca wartość.

## <a name="generate-a-workflow"></a>Generowanie przepływu pracy

Zestaw Azure łańcucha bloków Development Kit dla rozszerzenia Ethereum Visual Studio Code może generować przepływy pracy aplikacji logiki dla typowych scenariuszy. Dostępne są cztery scenariusze:

* Publikowanie danych w wystąpieniu Azure SQL Database
* Publikowanie zdarzeń w wystąpieniu Azure Event Grid lub Azure Service Bus
* Publikowanie raportów
* Mikrousługa oparta na protokole REST

 Zestaw Azure łańcucha bloków Development Kit używa Truffle, aby uprościć programowanie łańcucha bloków. Aby wygenerować aplikację logiki na podstawie kontraktu inteligentnego, musisz mieć rozwiązanie Truffle dla kontraktu inteligentnego. Potrzebne jest również połączenie z siecią konsorcjum usługi Azure łańcucha bloków. Aby uzyskać więcej informacji, zobacz [używanie Visual Studio Code do nawiązywania połączenia z siecią Azure łańcucha bloków Service Consortium — szybki start](connect-vscode.md).

Na przykład następujące kroki generują aplikację logiki mikrousług opartą na protokole REST na podstawie **HelloBlockchainego** przewodnika Szybki Start:

1. Na pasku bocznym Eksploratora Visual Studio Code rozwiń folder **kontrakty** w rozwiązaniu.
1. Kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Generuj mikrousługi dla inteligentnych kontraktów** z menu.

    ![Visual Studio Code okienku z opcją Generuj mikrousługi dla wybranych kontraktów inteligentnych](./media/ethereum-logic-app/generate-logic-app.png)

1. W palecie poleceń wybierz pozycję **aplikacja logiki**.
1. Wprowadź **adres kontraktu**. Aby uzyskać więcej informacji, zobacz [pobieranie adresu kontraktu](#get-the-contract-address).
1. Wybierz subskrypcję platformy Azure i grupę zasobów dla aplikacji logiki.

    Pliki konfiguracji i kodu aplikacji logiki są generowane w katalogu **generatedLogicApp** .

1. Wyświetl katalog **generatedLogicApp/HelloBlockchain** . Istnieje plik JSON aplikacji logiki dla każdej funkcji, zdarzenia i właściwości kontraktu inteligentnego.
1. Otwórz plik **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.jsw** pliku i skopiuj jego zawartość.

    ![Plik JSON z kodem do skopiowania](./media/ethereum-logic-app/requestmessage.png)

1. W aplikacji logiki wybierz pozycję **Widok kodu aplikacji logiki**. Zamień istniejący plik JSON na wygenerowany kod JSON aplikacji logiki.

    ![Widok kodu aplikacji logiki z nowym zastąpionym kodem aplikacji](./media/ethereum-logic-app/code-view.png)

1. Wybierz pozycję **Projektant** , aby przełączyć się do widoku projektanta.
1. Aplikacja logiki zawiera podstawowe kroki dla scenariusza. Należy jednak zaktualizować szczegóły konfiguracji łącznika usługi Ethereum łańcucha bloków.
1. Wybierz krok **połączenia** i Zmień lub [Utwórz połączenie interfejsu API](#create-an-api-connection) z usługą Azure łańcucha bloków.

    ![Widok projektanta z wybranym połączeniem](./media/ethereum-logic-app/microservice-logic-app.png)

1. Możesz teraz korzystać z aplikacji logiki. Aby przetestować mikrousługi oparte na protokole REST, wydaj żądanie HTTP POST na adres URL żądania aplikacji logiki. Skopiuj zawartość **adresu URL post protokołu HTTP** z kroku **po odebraniu żądania HTTP** .

    ![Logic Apps okienku projektanta z adresem URL POST protokołu HTTP](./media/ethereum-logic-app/post-url.png)

1. Użyj Zwinięciea, aby utworzyć żądanie HTTP POST. Zamień tekst zastępczy na *\<HTTP POST URL\>* adres URL z poprzedniego kroku.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Polecenie zwinięcie zwraca odpowiedź z aplikacji logiki. W takim przypadku odpowiedzią jest dane wyjściowe z funkcji **RequestMessage** Smart Contract.

    ![Dane wyjściowe kodu z funkcji RequestMessage Smart Contract](./media/ethereum-logic-app/curl.png)

Aby uzyskać więcej informacji na temat korzystania z zestawu Development Kit, zobacz [stronę typu wiki usługi Azure łańcucha bloków Development Kit](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Tworzenie połączenia interfejsu API

Do łącznika łańcucha bloków Ethereum jest wymagane połączenie interfejsu API z łańcucha bloków. Łącznika interfejsu API można użyć dla wielu aplikacji logiki. Niektóre właściwości są wymagane, a inne zależą od danego scenariusza.

> [!IMPORTANT]
> Do tworzenia transakcji na łańcucha bloków jest wymagany klucz prywatny lub konto i hasło. Wymagana jest tylko jedna forma uwierzytelniania. Nie musisz podawać zarówno klucza prywatnego, jak i szczegółów konta. Wykonywanie zapytań dotyczących kontraktów nie wymaga transakcji. Jeśli używasz akcji, które wykonują zapytania o stan kontraktu, klucz prywatny lub adres konta i hasło nie są wymagane.

Aby ułatwić skonfigurowanie połączenia z członkiem usługi Azure łańcucha bloków, Poniższa lista zawiera możliwe właściwości, które mogą być potrzebne w zależności od danego scenariusza.

| Właściwość | Opis |
|----------|-------------|
|**Nazwa połączenia** | Nazwa połączenia interfejsu API. Wymagane. |
|**Punkt końcowy RPC Ethereum** | Adres HTTP węzła transakcji usługi Azure łańcucha bloków. Wymagane. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie punktu końcowego RPC](#get-the-rpc-endpoint). |
|**Klucz prywatny** | Klucz prywatny konta Ethereum. Do transakcji są wymagane klucze prywatne lub adresy i hasła. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza prywatnego](#get-the-private-key). |
|**Adres konta** | Adres konta członka usługi Azure łańcucha bloków. Do transakcji są wymagane klucze prywatne lub adresy i hasła. Aby uzyskać więcej informacji, zobacz [pobieranie adresu konta](#get-the-account-address). |
|**Hasło konta** | Hasło konta jest ustawiane podczas tworzenia elementu członkowskiego. Aby uzyskać informacje na temat resetowania hasła, zobacz [Ethereum Account (konto](consortium.md#ethereum-account)).|

## <a name="get-the-rpc-endpoint"></a>Pobieranie punktu końcowego RPC

Adres punktu końcowego RPC usługi Azure łańcucha bloków Service jest wymagany do nawiązania połączenia z siecią łańcucha bloków. Adres punktu końcowego można uzyskać, korzystając z zestawu Azure łańcucha bloków Development Kit dla Ethereum lub Azure Portal.

**Aby skorzystać z zestawu Development Kit:**

1. W obszarze **usługa Azure łańcucha bloków** w Visual Studio Code kliknij prawym przyciskiem myszy konsorcjum.
1. Wybierz pozycję **Kopiuj adres punktu końcowego usługi RPC**.

    ![Okienko Visual Studio Code pokazujące konsorcjum z wybranym adresem punktu końcowego kopiowania RPC](./media/ethereum-logic-app/devkit-rpc.png)

    Punkt końcowy RPC jest kopiowany do Schowka.

**Aby użyć Azure Portal:**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków. Wybierz pozycję **węzły transakcji** i domyślne łącze węzła transakcji.

    ![Strona węzłów transakcji z wyborem (domyślny węzeł)](./media/ethereum-logic-app/transaction-nodes.png)

1. Wybierz pozycję **Parametry połączenia**  >  **klucze dostępu**.
1. Skopiuj adres punktu końcowego z **protokołu HTTPS (dostęp do klucza 1)** lub **https (dostęp do klucza 2)**.

    ![Azure Portal z kluczami dostępu do parametrów połączenia](./media/ethereum-logic-app/connection-string.png)

    Punkt końcowy RPC to adres URL HTTPS, który zawiera adres i klucz dostępu węzła transakcji elementu członkowskiego usługi Azure łańcucha bloków.

## <a name="get-the-private-key"></a>Pobierz klucz prywatny

Za pomocą klucza prywatnego konta Ethereum można uwierzytelniać się podczas wysyłania transakcji do łańcucha bloków. Klucze publiczne i prywatne konta usługi Ethereum są generowane na podstawie ciągu 12-słowa. Zestaw Azure łańcucha bloków Development Kit dla Ethereum generuje skrót po nawiązaniu połączenia z członkiem programu Azure łańcucha bloków Service Consortium. Adres punktu końcowego można uzyskać przy użyciu rozszerzenia Development Kit.

1. W Visual Studio Code Otwórz paletę poleceń (F1).
1. Wybierz **łańcucha bloków: Pobierz klucz prywatny**.
1. Wybierz skrót zapisany podczas nawiązywania połączenia z członkiem konsorcjum.

    ![Paleta poleceń z opcją wyboru](./media/ethereum-logic-app/private-key.png)

    Klucz prywatny jest kopiowany do Schowka.

## <a name="get-the-account-address"></a>Pobierz adres konta

Przy wysyłaniu transakcji do łańcucha bloków można użyć konta i hasła elementu członkowskiego. Hasło jest ustawiane podczas tworzenia elementu członkowskiego.

1. W Azure Portal przejdź do strony Przegląd usługi Azure łańcucha bloków.
1. Skopiuj adres **konta elementu członkowskiego** .

    ![Strona przegląd z adresem konta elementu członkowskiego](./media/ethereum-logic-app/member-account.png)

Aby uzyskać więcej informacji na temat adresu i hasła konta, zobacz [Ethereum Account (konto](consortium.md#ethereum-account)).

## <a name="get-the-contract-abi"></a>Pobierz kontrakt ABI

ABI kontraktu definiuje inteligentne interfejsy kontraktu. Opisuje sposób korzystania z kontraktu inteligentnego. Umowę ABI można uzyskać przy użyciu zestawu Azure łańcucha bloków Development Kit dla Ethereum. Można go również pobrać z pliku metadanych kontraktu utworzonego przez kompilator zapewniający trwałość.

**Aby skorzystać z zestawu Development Kit:**

Jeśli używasz zestawu Development Kit lub Truffle do skompilowania swojego inteligentnego kontraktu, możesz użyć rozszerzenia, aby skopiować ABI kontraktu do Schowka.

1. W okienku Eksploratora Visual Studio Code rozwiń folder **kompilacja/kontrakty** w projekcie o trwałym stanie.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Wybierz pozycję **Kopiuj kontrakt ABI**.

    ![Visual Studio Code okienku z opcją Kopiuj kontrakt ABI](./media/ethereum-logic-app/abi-devkit.png)

    ABI kontraktu jest kopiowany do Schowka.

**Aby użyć pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu znajdujący się w folderze **kompilacja/kontrakty** w projekcie o trwałym stanie. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Znajdź sekcję **ABI** w pliku JSON.
1. Skopiuj tablicę JSON **ABI** .

    ![Kod ABI w pliku metadanych kontraktu](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Pobierz kod bajtowy kontraktu

Kod bajtowy kontraktu jest skompilowanym inteligentnym kontraktem wykonywanym przez maszynę wirtualną Ethereum. Kod bajtowy kontraktu można uzyskać przy użyciu zestawu Azure łańcucha bloków Development Kit dla Ethereum. Można go również uzyskać od kompilatora kryjącego.

**Aby skorzystać z zestawu Development Kit:**

Jeśli używasz zestawu Development Kit lub Truffle do skompilowania swojego inteligentnego kontraktu, możesz użyć rozszerzenia, aby skopiować kod bajtowy kontraktu do Schowka.

1. W okienku Eksploratora Visual Studio Code rozwiń folder **kompilacja/kontrakty** w projekcie o trwałym stanie.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Wybierz pozycję **Kopiuj kod bajtowy kontraktu**.

    ![Visual Studio Code okienku z opcją kopiowania kodu bajtowego kontraktu](./media/ethereum-logic-app/bytecode-devkit.png)

    Kod bajtowy kontraktu jest kopiowany do Schowka.

**Aby użyć pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu znajdujący się w folderze **kompilacja/kontrakty** w projekcie o trwałym stanie. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Znajdź element **kodu bajtowego** w pliku JSON.
1. Skopiuj wartość **kod bajtowy** .

    ![Visual Studio Code okienku z użyciem kodu bajtowego w metadanych](./media/ethereum-logic-app/bytecode-metadata.png)

**Aby użyć kompilatora do wypełniania:**

Użyj polecenia, `solc --bin <smart contract>.sol` Aby wygenerować kod bajtowy kontraktu.

## <a name="get-the-contract-address"></a>Pobierz adres kontraktu

Adres kontraktu jest adresem docelowym kontraktu inteligentnego na Ethereum łańcucha bloków. Ten adres jest używany do wysyłania transakcji lub stanu zapytania dla kontraktu inteligentnego. Adres kontraktu można uzyskać z danych wyjściowych migracji Truffle lub pliku metadanych kontraktu.

**Aby użyć Truffle migracji danych wyjściowych:**

Truffle wyświetla adres kontraktu po wdrożeniu kontraktu inteligentnego. Skopiuj **adres kontraktu** z danych wyjściowych.

![Truffle dane wyjściowe migracji z adresem kontraktu w Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Aby użyć pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu znajdujący się w folderze **kompilacja/kontrakty** w projekcie o trwałym stanie. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Znajdź sekcję **Networks** w pliku JSON.
1. Sieci prywatne są identyfikowane przez identyfikator sieci liczb całkowitych. Znajdź wartość adresu w sekcji Sieć.
1. Skopiuj wartość **adresu** .

![Metadane z wartością adresu w Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Następne kroki

Obejrzyj typowe scenariusze w filmie wideo, [wykonując więcej czynności w Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
