---
title: 'Szybki Start: Twoje pierwsze zapytanie Ruby'
description: W tym przewodniku szybki start wykonaj kroki umożliwiające rozwiązania Gem grafu zasobów dla języka Ruby i uruchomienie pierwszego zapytania.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 4ffd7f33fb83b7a1f247f687b77dd2ef9c8e5bc9
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057318"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu języka Ruby

Pierwszym krokiem do korzystania z grafu zasobów platformy Azure jest sprawdzenie, czy wymagane Gems dla języka Ruby są zainstalowane. Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania Gems do instalacji języka Ruby.

Po zakończeniu tego procesu dodaliśmy Gems do instalacji języka Ruby i uruchomimy pierwsze zapytanie dotyczące wykresu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- Nazwa główna usługi platformy Azure, w tym _clientId_ i _clientSecret_.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Tworzenie projektu grafu zasobów

Aby umożliwić Dodawanie zapytania do grafu zasobów platformy Azure za pomocą języka Ruby, rozwiązania Gem należy dodać do `Gemfile` . Ta rozwiązania Gem działa wszędzie tam, gdzie można używać języka Ruby, w tym z [Azure Cloud Shell](https://shell.azure.com), [bash w systemie Windows 10](/windows/wsl/install-win10)lub lokalnie.

1. Sprawdź, czy zainstalowano najnowszą wersję języka Ruby (co najmniej **2.7.1**). Jeśli nie jest jeszcze zainstalowany, Pobierz go pod adresem [ruby-lang.org](https://www.ruby-lang.org/en/downloads/).

1. W wybranym środowisku Ruby zainicjuj pakiet w nowym folderze projektu:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Zaktualizuj swój `Gemfile` program przy użyciu Gems wymaganego dla wykresu zasobów platformy Azure. Zaktualizowany plik powinien wyglądać podobnie do tego przykładu:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. W folderze projektu uruchom polecenie `bundle install` . Upewnij się, że Gems zostały zainstalowane przy użyciu programu `bundle list` .

1. W tym samym folderze projektu Utwórz `argQuery.rb` przy użyciu następującego kodu i Zapisz zaktualizowany plik:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Po zapisaniu skryptu Ruby i przygotowaniu go do użycia czas na wypróbowanie prostego zapytania grafu zasobów. Zapytanie zwraca pierwsze pięć zasobów platformy Azure o **nazwie** i **typie zasobu** każdego zasobu.

W każdym wywołaniu do `argQuery` , istnieją zmienne, które są używane, należy zamienić na własne wartości:

- `{tenantId}` — zastąp identyfikatorem swojej dzierżawy
- `{clientId}` -Zamień na identyfikator klienta nazwy głównej usługi
- `{clientSecret}` -Zamień na klucz tajny klienta jednostki usługi
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

1. Zmień katalogi na folder projektu, w którym zostały utworzone `Gemfile` `argClient.rb` pliki i.

1. Uruchom pierwsze zapytanie dotyczące wykresu zasobów platformy Azure przy użyciu Gems i `resources` metody:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zmień parametr końcowy na `argQuery.rb` i Zmień zapytanie na `order by` Właściwość **name** :

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zmień parametr końcowy na `argQuery.rb` i Zmień zapytanie, tak aby najpierw miało `order by` Właściwość **name** , a następnie `limit` na pięć najważniejszych wyników:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowaną Gems ze środowiska Ruby, możesz to zrobić za pomocą następującego polecenia:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> Rozwiązania Gem `azure_mgmt_resourcegraph` ma zależności, takie jak `ms_rest` i `ms_rest_azure` , które mogły być również zainstalowane w zależności od środowiska. Można odinstalować te Gems również, jeśli nie są już potrzebne.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dodano Gems wykresu zasobów do środowiska Ruby i uruchomiono pierwsze zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
