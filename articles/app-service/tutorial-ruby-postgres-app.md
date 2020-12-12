---
title: 'Samouczek: aplikacja Ruby systemu Linux z Postgres'
description: Dowiedz się, jak pobrać aplikację Ruby systemu Linux działającą w Azure App Service z połączeniem z bazą danych PostgreSQL na platformie Azure. Szyny są używane w samouczku.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: f565fcef60b2cb4726b180eb67e6ac1fcaefc24b
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347850"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Tworzenie aplikacji w języku Ruby i korzystającej z bazy danych Postgres w usłudze Azure App Service w systemie Linux

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web. W tym samouczku pokazano, jak utworzyć aplikację w języku Ruby i połączyć ją z bazą danych PostgreSQL. Po zakończeniu będziesz mieć aplikację platformy [Ruby on Rails](https://rubyonrails.org/) uruchomioną w usłudze App Service w systemie Linux.

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Zrzut ekranu przedstawiający przykład zadań aplikacji języka Ruby on-Szyns.":::

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych PostgreSQL na platformie Azure
> * Łączenie aplikacji platformy Ruby on Rails z bazą danych PostgreSQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- [Zainstaluj oprogramowanie Git](https://git-scm.com/)
- [Zainstaluj język Ruby 2,6](https://www.ruby-lang.org/en/documentation/installation/)
- [Zainstaluj oprogramowanie Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
- [Instalowanie i uruchamianie PostgreSQL](https://www.postgresql.org/download/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="prepare-local-postgres"></a>Przygotowywanie lokalnej bazy danych Postgres

W tym kroku utworzysz bazę danych na lokalnym serwerze Postgres przeznaczoną do użytku w tym samouczku.

### <a name="connect-to-local-postgres-server"></a>Łączenie z lokalnym serwerem Postgres

Otwórz okno terminala i uruchom polecenie `psql`, aby nawiązać połączenie z lokalnym serwerem Postgres.

```bash
sudo -u postgres psql
```

Pomyślne nawiązanie połączenia oznacza, że baza danych Postgres działa. W przeciwnym razie upewnij się, że lokalna baza danych Postgres została uruchomiona, wykonując kroki opisane w temacie [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Zasoby do pobrania — dystrybucja podstawowa serwera PostgreSQL).

Wpisz polecenie `\q`, aby zamknąć klienta Postgres. 

Utwórz użytkownika bazy danych Postgres, który może tworzyć bazy danych, uruchamiając następujące polecenie za pomocą zalogowanej nazwy użytkownika systemu Linux.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Tworzenie aplikacji platformy Ruby on Rails lokalnie
W tym kroku uzyskasz przykładową aplikację platformy Ruby on Rails, skonfigurujesz jej połączenie z bazą danych i uruchomisz ją lokalnie. 

### <a name="clone-the-sample"></a>Klonowanie przykładu

W oknie terminalu dodaj element `cd` do katalogu roboczego.

Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

Przy użyciu polecenia `cd` przejdź do sklonowanego katalogu. Zainstaluj wymagane pakiety.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Uruchamianie przykładu lokalnie

Uruchom [migracje platformy Rails](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations), aby utworzyć tabele wymagane przez aplikację. Aby zobaczyć, które tabele zostały utworzone w migracjach, zajrzyj do katalogu _db/migrate_ w repozytorium Git.

```bash
rake db:create
rake db:migrate
```

Uruchom aplikację.

```bash
rails server
```

W przeglądarce przejdź do adresu `http://localhost:3000`. Dodaj na stronie kilka zadań.

![Pomyślne połączenie platformy Ruby on Rails z bazą danych Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Aby zatrzymać serwer platformy Rails, naciśnij w terminalu klawisze `Ctrl + C`.

## <a name="create-postgres-in-azure"></a>Tworzenie bazy danych Postgres na platformie Azure

W tym kroku utworzysz bazę danych Postgres w usłudze [Azure Database for PostgreSQL](../postgresql/index.yml). Następnie skonfigurujesz aplikację platformy Ruby on Rails i połączysz ją z tą bazą danych.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Tworzenie bazy danych Postgres na platformie Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

W tej sekcji utworzysz serwer Azure Database for PostgreSQL i bazę danych. Aby rozpocząć, zainstaluj `db-up` rozszerzenie za pomocą następującego polecenia:

```azurecli
az extension add --name db-up
```

Utwórz bazę danych Postgres na platformie Azure przy użyciu [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) polecenia, jak pokazano w poniższym przykładzie. Zamień na *\<postgresql-name>* *unikatową* nazwę (punkt końcowy serwera to *https:// \<postgresql-name> . Postgres.Database.Azure.com*). Dla programu *\<admin-username>* i *\<admin-password>* Określ poświadczenia, aby utworzyć użytkownika administratora dla tego serwera Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

To polecenie może chwilę potrwać, ponieważ wykonuje następujące czynności:

- Tworzy [grupę zasobów](../azure-resource-manager/management/overview.md#terminology) o nazwie `myResourceGroup` , jeśli nie istnieje. Każdy zasób platformy Azure musi znajdować się w jednym z nich. Parametr `--resource-group` jest opcjonalny.
- Tworzy serwer Postgres z użytkownikiem administracyjnym.
- Tworzy `sampledb` bazę danych.
- Zezwala na dostęp z lokalnego adresu IP.
- Zezwala na dostęp z usług platformy Azure.
- Utwórz użytkownika bazy danych z dostępem do `sampledb` bazy danych.

Wszystkie kroki można wykonać oddzielnie z innymi `az postgres` poleceniami `psql` , ale wszystkie są wykonywane `az postgres up` w jednym kroku.

Po zakończeniu wykonywania polecenia Znajdź linie wyjściowe, z którymi się znajdują `Ran Database Query:` . Pokażą użytkownikowi bazy danych, który został utworzony dla Ciebie, przy użyciu nazwy użytkownika `root` i hasła `Sampledb1` . Będziesz ich używać później, aby połączyć aplikację z bazą danych.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, można ustawić na jeden z [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/). Dostępne regiony można pobrać za pomocą [`az account list-locations`](/cli/azure/account#az-account-list-locations) polecenia. W przypadku aplikacji produkcyjnych Umieść swoją bazę danych i aplikację w tej samej lokalizacji.

## <a name="connect-app-to-azure-postgres"></a>Łączenie aplikacji z bazą danych Postgres na platformie Azure

W tym kroku połączysz aplikację platformy Ruby on Rails z bazą danych Postgres utworzoną w usłudze Azure Database for PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurowanie połączenia z bazą danych

Otwórz plik _config/database.yml_ w repozytorium. W dolnej części pliku zastąp zmienne produkcyjne następującym kodem. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Zapisz zmiany.

### <a name="test-the-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Po powrocie do terminala lokalnego ustaw następujące zmienne środowiskowe:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
```

Uruchom migracje baz danych platformy Rails z właśnie skonfigurowanymi wartościami produkcyjnymi, aby utworzyć tabele w bazie danych Postgres w usłudze Azure Database for PostgreSQL.

```bash
rake db:migrate RAILS_ENV=production
```

Gdy aplikacja platformy Rails jest uruchamiana w środowisku produkcyjnym, wymaga prekompilowanych zasobów. Wygeneruj wymagane zasoby przy użyciu następującego polecenia:

```bash
rake assets:precompile
```

W środowisku produkcyjnym platformy Rails do zarządzania zabezpieczeniami używane są także wpisy tajne. Wygeneruj klucz tajny.

```bash
rails secret
```

Zapisz ten klucz tajny w określonych zmiennych używanych przez środowisko produkcyjne platformy Rails. Dla wygody możesz używać tego samego klucza dla obu zmiennych.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Włącz w środowisku produkcyjnym platformy Rails udostępnianie plików JavaScript i CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Uruchom przykładową aplikację w środowisku produkcyjnym.

```bash
rails server -e production
```

Przejdź do adresu `http://localhost:3000`. Jeśli strona ładuje się bez błędów, oznacza to, że aplikacja platformy Ruby on Rails nawiązuje połączenie z bazą danych Postgres na platformie Azure.

Dodaj na stronie kilka zadań.

![Pomyślne połączenie platformy Ruby on Rails z usługą Azure Database for PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Aby zatrzymać serwer platformy Rails, naciśnij w terminalu klawisze `Ctrl + C`.

### <a name="commit-your-changes"></a>Zatwierdzanie zmian

Aby zatwierdzić zmiany, uruchom następujące polecenia Git:

```bash
git add .
git commit -m "database.yml updates"
```

Aplikacja jest gotowa do wdrożenia.

## <a name="deploy-to-azure"></a>Wdróż na platformie Azure

W tym kroku wdrożysz aplikację platformy Rails połączoną z bazą danych Postgres w usłudze Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych

W usłudze App Service zmienne środowiskowe ustawia się jako _ustawienia aplikacji_ za pomocą polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) w usłudze Cloud Shell.

Następujące polecenie usługi Cloud Shell konfiguruje ustawienia aplikacji `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` i `DB_PASSWORD`. Zastąp symbole zastępcze _&lt; nazwa_aplikacji>_ i _&lt; postgres-Server-Name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Konfigurowanie zmiennych środowiskowych platformy Rails

W terminalu lokalnym [Wygeneruj nowy wpis tajny](configure-language-ruby.md#set-secret_key_base-manually) dla środowiska produkcyjnego szyny na platformie Azure.

```bash
rails secret
```

Skonfiguruj zmienne wymagane przez środowisko produkcyjne platformy Rails.

W poniższym poleceniu Cloud Shell Zastąp dwa symbole zastępcze _&lt; danych wyjściowych-Secret>_ z nowym kluczem tajnym wygenerowanym w lokalnym terminalu.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

Właściwość `ASSETS_PRECOMPILE="true"` informuje domyślny kontener języka Ruby, aby wstępnie skompilował zasoby przy każdym wdrożeniu narzędzia Git. Aby uzyskać więcej informacji, zobacz [prekompilowanie zasobów](configure-language-ruby.md#precompile-assets) i [Obsługiwanie zasobów statycznych](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

W terminalu lokalnym dodaj zdalną platformę Azure do lokalnego repozytorium Git.

```bash
git remote add azure <paste-copied-url-here>
```

Wykonaj wypchnięcie na zdalną platformę Azure w celu wdrożenia aplikacji platformy Ruby on Rails. Zostanie wyświetlony monit o podanie hasła określonego wcześniej w ramach tworzenia użytkownika wdrożenia.

```bash
git push azure master
```

Podczas wdrażania usługa Azure App Service przekaże postęp za pomocą narzędzi Git.

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do adresu `http://<app-name>.azurewebsites.net` i dodaj kilka zadań do listy.

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Zrzut ekranu przedstawiający przykład aplikacji platformy Azure z tytułem zadania z zadaniami dodany do listy.":::

Gratulacje! Masz uruchomioną opartą na danych aplikację platformy Ruby on Rails w usłudze Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Lokalne aktualizowanie modelu i ponowne wdrażanie

W tym kroku wprowadzisz prostą zmianę w modelu danych `task` i aplikacji internetowej, a następnie opublikujesz tę aktualizację na platformie Azure.

W scenariuszu tego zadania zmodyfikujesz aplikację tak, aby można było oznaczyć zadanie jako zakończone.

### <a name="add-a-column"></a>Dodawanie kolumny

W terminalu przejdź do katalogu głównego repozytorium Git.

Wygeneruj nową migrację, która doda kolumnę logiczną o nazwie `Done` do tabeli `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

To polecenie wygeneruje plik nowej migracji w katalogu _db/migrate_.


W terminalu uruchom migracje baz danych platformy Rails, aby wprowadzić zmianę w lokalnej bazie danych.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Aktualizowanie logiki aplikacji

Otwórz plik *app/controllers/tasks_controller.rb*. Na końcu pliku dodaj następujący wiersz:

```rb
params.require(:task).permit(:Description)
```

Zmodyfikuj ten wiersz tak, aby zawierał nowy parametr `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Aktualizowanie widoków

Otwórz plik *app/views/tasks/_form.html.erb*, który jest formularzem edycji.

Znajdź wiersz `<%=f.error_span(:Description) %>` i wstaw bezpośrednio pod nim następujący kod:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Otwórz plik *app/views/tasks/show.html.erb*, który jest stroną widoku z pojedynczym rekordem. 

Znajdź wiersz `<dd><%= @task.Description %></dd>` i wstaw bezpośrednio pod nim następujący kod:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Otwórz plik *app/views/tasks/index.html.erb*, który jest stroną indeksu dla wszystkich rekordów.

Znajdź wiersz `<th><%= model_class.human_attribute_name(:Description) %></th>` i wstaw bezpośrednio pod nim następujący kod:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

W tym samym pliku znajdź wiersz `<td><%= task.Description %></td>` i wstaw bezpośrednio pod nim następujący kod:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Lokalne testowanie zmian

W terminalu lokalnym uruchom serwer platformy Rails.

```bash
rails server
```

Aby zobaczyć zmianę stanu zadania, przejdź do adresu `http://localhost:3000` i dodaj lub edytuj elementy.

![Dodanie pola wyboru do zadania](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Aby zatrzymać serwer platformy Rails, naciśnij w terminalu klawisze `Ctrl + C`.

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

W terminalu uruchom migracje baz danych platformy Rails dla środowiska produkcyjnego, aby wprowadzić zmianę w bazie danych platformy Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Zatwierdź wszystkie zmiany w narzędziu Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Po zakończeniu wykonywania polecenia `git push` przejdź do aplikacji platformy Azure i przetestuj nowe funkcje.

![Zmiany w modelu i bazie danych opublikowane na platformie Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Jeśli dodano jakiekolwiek zadania, zostaną one zachowane w bazie danych. Aktualizacje schematu danych pozostawiają dane bez zmian.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby zarządzać utworzoną aplikacją.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/tutorial-php-mysql-app/access-portal.png)

Zostanie wyświetlona strona omówienia aplikacji. W tym miejscu możesz wykonywać podstawowe zadania zarządzania, takie jak zatrzymywanie, uruchamianie, ponowne uruchamianie, przeglądanie i usuwanie.

Menu po lewej stronie zawiera strony służące do konfigurowania aplikacji.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych Postgres na platformie Azure
> * Łączenie aplikacji platformy Ruby on Rails z bazą danych Postgres
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zamapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Ruby](configure-language-ruby.md)
