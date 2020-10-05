---
title: 'Szybki Start: pierwsze zapytanie JavaScript'
description: W tym przewodniku szybki start wykonaj kroki umożliwiające włączenie biblioteki grafu zasobów dla języka JavaScript i uruchomienie pierwszego zapytania.
ms.date: 06/23/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: cdc0589829f250851212ad990dde99eb6dcc958f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91251970"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Szybki Start: uruchamianie pierwszego zapytania wykresu zasobów przy użyciu języka JavaScript

Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania bibliotek do instalacji języka JavaScript. Pierwszym krokiem do korzystania z grafu zasobów platformy Azure jest zainicjowanie aplikacji JavaScript z wymaganymi bibliotekami.

Po zakończeniu tego procesu dodaliśmy biblioteki do instalacji języka JavaScript i uruchomimy pierwsze zapytanie dotyczące grafu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- **Node.js**: wymagana jest [Node.js](https://nodejs.org/) wersja 12 lub nowsza.

## <a name="application-setup"></a>Konfiguracja aplikacji

Aby włączyć JavaScript do tworzenia zapytań dotyczących grafu zasobów platformy Azure, należy skonfigurować środowisko. Ta konfiguracja działa wszędzie tam, gdzie można używać języka JavaScript, [w tym bash w systemie Windows 10](/windows/wsl/install-win10).

1. Skonfiguruj nowy projekt Node.js, uruchamiając następujące polecenie.

   ```bash
   npm init -y
   ```

1. Dodaj odwołanie do modułu yargs.

   ```bash
   npm install yargs
   ```

1. Dodaj odwołanie do modułu grafu zasobów platformy Azure.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Dodaj odwołanie do biblioteki uwierzytelniania platformy Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Sprawdź, czy w _package.jsjest w_ `@azure/arm-resourcegraph` wersji **2.0.0** lub nowszej i czy `@azure/ms-rest-nodeauth` jest w wersji **3.0.3** lub nowszej.

## <a name="query-the-resource-graph"></a>Zapytanie dotyczące wykresu zasobów

1. Utwórz nowy plik o nazwie _index.js_ i wprowadź następujący kod.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Wprowadź następujące polecenie w terminalu:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   Pamiętaj, aby zastąpić `<YOUR_SUBSCRIPTION_ID_LIST>` symbol zastępczy rozdzieloną przecinkami listą identyfikatorów subskrypcji platformy Azure.

   > [!NOTE]
   > Ponieważ w tym przykładzie zapytania nie określono modyfikatora sortowania, takiego jak `order by` uruchomienie tego zapytania wiele razy, prawdopodobnie zostanie wyznaczony inny zestaw zasobów dla każdego żądania.

1. Zmień pierwszy parametr na `index.js` i Zmień zapytanie na `order by` Właściwość **name** . Zamień `<YOUR_SUBSCRIPTION_ID_LIST>` na identyfikator subskrypcji:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   Po próbie uwierzytelnienia skryptu w terminalu zostanie wyświetlony komunikat podobny do następującego:

   > Aby się zalogować, użyj przeglądarki sieci Web, aby otworzyć stronę https://microsoft.com/devicelogin i wprowadzić kod FGB56WJUGK do uwierzytelnienia.

   Po uwierzytelnieniu w przeglądarce, skrypt będzie nadal działać.

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zmień pierwszy parametr na `index.js` i Zmień zapytanie na najpierw `order by` Właściwość **name** , a następnie `limit` na pięć pierwszych wyników. Zamień `<YOUR_SUBSCRIPTION_ID_LIST>` na identyfikator subskrypcji:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane biblioteki z aplikacji, uruchom następujące polecenie.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dodano biblioteki grafu zasobów do środowiska JavaScript i uruchomiono pierwsze zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)