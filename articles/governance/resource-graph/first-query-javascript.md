---
title: 'Szybki start: pierwsze zapytanie JavaScript'
description: W tym przewodniku Szybki start wykonaj kroki, aby włączyć bibliotekę Resource Graph dla języka JavaScript i uruchomić pierwsze zapytanie.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: ef93378acaf2c92cf4f7761345cea326af63d300
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533044"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Szybki start: uruchamianie pierwszego zapytania Resource Graph pomocą języka JavaScript

Ten przewodnik Szybki start umożliwia rozpoczęcie procesu dodawania bibliotek do instalacji języka JavaScript. Pierwszym krokiem do korzystania z Azure Resource Graph jest zainicjowanie aplikacji JavaScript z wymaganymi bibliotekami.

Po zakończeniu tego procesu biblioteki zostaną dodane do instalacji języka JavaScript i uruchomione pierwsze Resource Graph zapytania.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure:** jeśli nie masz subskrypcji [](https://azure.microsoft.com/free/) platformy Azure, przed rozpoczęciem utwórz bezpłatne konto.

- **Node.js:** [Node.js](https://nodejs.org/) wymagana jest wersja 12 lub nowsza.

## <a name="application-initialization"></a>Inicjowanie aplikacji

Aby umożliwić językowi JavaScript wykonywanie Azure Resource Graph, należy skonfigurować środowisko. Ta konfiguracja działa wszędzie tam, gdzie można używać języka JavaScript, w tym [bash na Windows 10](/windows/wsl/install-win10).

1. Zaimicjuj nowy Node.js, uruchamiając następujące polecenie.

   ```bash
   npm init -y
   ```

1. Dodaj odwołanie do modułu yargs.

   ```bash
   npm install yargs
   ```

1. Dodaj odwołanie do Azure Resource Graph modułu.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Dodaj odwołanie do biblioteki uwierzytelniania platformy Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Sprawdź, _package.jsw programie_ jest w wersji 2.0.0 lub wyższej i czy jest to wersja `@azure/arm-resourcegraph`  `@azure/ms-rest-nodeauth` **3.0.3 lub** nowsza.

## <a name="query-the-resource-graph"></a>Wykonywanie zapytania Resource Graph

1. Utwórz nowy plik o _nazwieindex.js_ i wprowadź następujący kod.

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

   Pamiętaj, aby zastąpić symbol zastępczy rozdzielaną przecinkami `<YOUR_SUBSCRIPTION_ID_LIST>` listą identyfikatorów subskrypcji platformy Azure.

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie zapewnia modyfikatora sortowania, takiego jak , wielokrotne uruchomienie tego zapytania prawdopodobnie spowoduje uzyskanie innego zestawu `order by` zasobów dla każdego żądania.

1. Zmień pierwszy parametr na `index.js` i zmień zapytanie na właściwość `order by` Name.  Zastąp `<YOUR_SUBSCRIPTION_ID_LIST>` identyfikatorem subskrypcji:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   Podczas próby uwierzytelnienia skryptu w terminalu zostanie wyświetlony komunikat podobny do następującego:

   > Aby się zalogować, otwórz stronę w przeglądarce internetowej i wprowadź kod https://microsoft.com/devicelogin FGB56WJUGK w celu uwierzytelnienia.

   Po uwierzytelnieniu w przeglądarce skrypt będzie nadal działać.

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie je zamówia.

1. Zmień pierwszy parametr na i zmień zapytanie na pierwszą właściwość Name, a następnie na `index.js` `order by` pięć pierwszych  `limit` wyników. Zastąp `<YOUR_SUBSCRIPTION_ID_LIST>` identyfikatorem subskrypcji:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Gdy końcowe zapytanie jest uruchamiane kilka razy, przy założeniu, że nic się nie zmienia w twoim środowisku, zwrócone wyniki są spójne i uporządkowane według właściwości **Name,** ale nadal ograniczone do pięciu najlepszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane biblioteki z aplikacji, uruchom następujące polecenie.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dodano biblioteki Resource Graph do środowiska języka JavaScript i uruchamiano pierwsze zapytanie. Aby dowiedzieć się więcej o języku Resource Graph, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
