---
title: Tworzenie aplikacji internetowej w języku Ruby i korzystającej z bazy danych Postgres w usłudze Azure App Service w systemie Linux | Microsoft Docs
description: Dowiedz się, jak uruchomić aplikację języka Ruby na platformie Azure z użyciem połączenia z bazą danych PostgreSQL na platformie Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 9ae476e21e4935ea555e671c3c7144748d60abb6
ms.sourcegitcommit: 5821eef990c26fa045e4beacce39f6b02b83156b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2018
ms.locfileid: "35662797"
---
# <a name="build-a-ruby-and-postgres-web-app-in-azure-app-service-on-linux"></a>Tworzenie aplikacji internetowej w języku Ruby i korzystającej z bazy danych Postgres w usłudze Azure App Service w systemie Linux

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym samouczku pokazano, jak utworzyć aplikację internetową w języku Ruby i połączyć ją z bazą danych PostgreSQL. Po zakończeniu będziesz mieć aplikację platformy [Ruby on Rails](http://rubyonrails.org/) uruchomioną w usłudze App Service w systemie Linux.

![Aplikacja platformy Ruby on Rails uruchomiona w usłudze Azure App Service](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych PostgreSQL na platformie Azure
> * Łączenie aplikacji platformy Ruby on Rails z bazą danych PostgreSQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Zainstaluj oprogramowanie Git](https://git-scm.com/)
* [Zainstaluj oprogramowanie Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Zainstaluj oprogramowanie Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Zainstaluj i uruchom serwer PostgreSQL](https://www.postgresql.org/download/)

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
sudo -u postgres createuser -d <signed_in_user>
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

Uruchom [migracje platformy Rails](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations), aby utworzyć tabele wymagane przez aplikację. Aby zobaczyć, które tabele zostały utworzone w migracjach, zajrzyj do katalogu _db/migrate_ w repozytorium Git.

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Tworzenie bazy danych Postgres na platformie Azure

W tym kroku utworzysz bazę danych Postgres w usłudze [Azure Database for PostgreSQL](/azure/postgresql/). Następnie skonfigurujesz aplikację platformy Ruby on Rails i połączysz ją z tą bazą danych.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Tworzenie serwera Postgres

Za pomocą polecenia [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) utwórz serwer PostgreSQL.

Uruchom następujące polecenie w usłudze Cloud Shell, wstawiając unikatową nazwę serwera w miejscu ciągu zastępczego *\<postgres_server_name>*. Nazwa serwera musi być unikatowa w obrębie wszystkich serwerów na platformie Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres_server_name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Po utworzeniu serwera usługi Azure Database for PostgreSQL w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres_server_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres_server_name>",
  "location": "westeurope",
  "name": "<postgres_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Konfigurowanie zapory serwera

W usłudze Cloud Shell przy użyciu polecenia [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az_postgres_server_firewall_rule_create) utwórz regułę zapory dla serwera Postgres, aby zezwolić na połączenia klientów. Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. Wstaw unikatową nazwę serwera w miejsce ciągu zastępczego *\<postgres_server_name>*.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres_server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!TIP] 
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Nawiązywanie połączenia z serwerem produkcyjnym Postgres lokalnie

W usłudze Cloud Shell połącz się z serwerem Postgres na platformie Azure. Zamiast ciągów zastępczych _&lt;postgres_server_name>_ użyj wcześniej określonej wartości.

```bash
psql -U adminuser@<postgres_server_name> -h <postgres_server_name>.postgres.database.azure.com postgres
```

Gdy zostanie wyświetlony monit o hasło, wpisz hasło _My5up3r$tr0ngPa$w0rd!_, które określono podczas tworzenia serwera bazy danych.

### <a name="create-a-production-database"></a>Tworzenie produkcyjnej bazy danych

Utwórz bazę danych w wierszu polecenia `postgres`.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Tworzenie użytkownika z uprawnieniami

Utwórz użytkownika bazy danych o nazwie _railsappuser_ i nadaj mu wszystkie uprawnienia w bazie danych `sampledb`.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017'; 
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Zakończ połączenie z serwerem, wpisując polecenie `\q`.

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
export DB_HOST=<postgres_server_name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres_server_name>
export DB_PASSWORD=MyPostgresAzure2017
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
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
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

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

W tym kroku wdrożysz aplikację platformy Rails połączoną z bazą danych Postgres w usłudze Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych

W usłudze App Service zmienne środowiskowe ustawia się jako _ustawienia aplikacji_ za pomocą polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) w usłudze Cloud Shell.

Następujące polecenie usługi Cloud Shell konfiguruje ustawienia aplikacji `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` i `DB_PASSWORD`. Zastąp ciągi zastępcze _&lt;appname>_ i _&lt;postgres_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<postgres_server_name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres_server_name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Konfigurowanie zmiennych środowiskowych platformy Rails

W terminalu lokalnym wygeneruj nowy klucz tajny dla środowiska produkcyjnego platformy Rails na platformie Azure.

```bash
rails secret
```

Skonfiguruj zmienne wymagane przez środowisko produkcyjne platformy Rails.

W następującym poleceniu usługi Cloud Shell zastąp dwa teksty zastępcze _&lt;output_of_rails_secret>_ nowy kluczem tajnym wygenerowanym w terminalu lokalnym.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

Właściwość `ASSETS_PRECOMPILE="true"` informuje domyślny kontener języka Ruby, aby wstępnie skompilował zasoby przy każdym wdrożeniu narzędzia Git.

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

W terminalu lokalnym dodaj zdalną platformę Azure do lokalnego repozytorium Git.

```bash
git remote add azure <paste_copied_url_here>
```

Wykonaj wypchnięcie na zdalną platformę Azure w celu wdrożenia aplikacji platformy Ruby on Rails. Zostanie wyświetlony monit o podanie hasła określonego wcześniej w ramach tworzenia użytkownika wdrożenia.

```bash
git push azure master
```

Podczas wdrażania usługa Azure App Service przekaże postęp za pomocą narzędzi Git.

```bash
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
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Przejdź do adresu `http://<app_name>.azurewebsites.net` i dodaj kilka zadań do listy.

![Aplikacja platformy Ruby on Rails uruchomiona w usłudze Azure App Service](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

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

Po zakończeniu wykonywania polecenia `git push` przejdź do aplikacji internetowej platformy Azure i przetestuj nowe funkcje.

![Zmiany w modelu i bazie danych opublikowane na platformie Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Jeśli dodano jakiekolwiek zadania, zostaną one zachowane w bazie danych. Aktualizacje schematu danych pozostawiają dane bez zmian.

## <a name="manage-the-azure-web-app"></a>Zarządzanie aplikacją internetową platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby zarządzać utworzoną aplikacją internetową.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji internetowej platformy Azure.

![Nawigacja w portalu do aplikacji internetowej platformy Azure](./media/tutorial-php-mysql-app/access-portal.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. W tym miejscu możesz wykonywać podstawowe zadania zarządzania, takie jak zatrzymywanie, uruchamianie, ponowne uruchamianie, przeglądanie i usuwanie.

Menu po lewej stronie zawiera strony służące do konfigurowania aplikacji.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

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

Przejdź do następnego samouczka, aby dowiedzieć się, jak zamapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure Web Apps (Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure)](../app-service-web-tutorial-custom-domain.md)
