---
title: Opracowywanie bezpiecznej aplikacji sieci Web | Microsoft Docs
description: Ta prosta Przykładowa aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które ulepszają aplikację i stan bezpieczeństwa organizacji podczas opracowywania na platformie Azure.
keywords: nie dotyczy
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.custom: has-adal-ref, tracking-python
ms.openlocfilehash: 857303009b31945b0fe4f5555cb7e545cd16719d
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558875"
---
# <a name="develop-a-secure-web-app"></a>Tworzenie bezpiecznej aplikacji internetowej

Ten przykład to prosta aplikacja w języku Python wyświetlająca stronę sieci Web zawierającą linki do zasobów zabezpieczeń na potrzeby tworzenia aplikacji na platformie Azure. Aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które mogą pomóc w ulepszaniu aplikacji i stan bezpieczeństwa organizacji podczas opracowywania aplikacji na platformie Azure.

Należy postępować zgodnie z krokami opisanymi w tym artykule, aby upewnić się, że składniki aplikacji są prawidłowo skonfigurowane. Baza danych, Azure App Service, wystąpienie Azure Key Vault i wystąpienie Application Gateway platformy Azure są od siebie zależne.

Skrypty wdrażania skonfigurują infrastrukturę. Po uruchomieniu skryptów wdrażania należy wykonać ręczną konfigurację w Azure Portal, aby połączyć składniki i usługi ze sobą.

Przykładowa aplikacja jest skierowana do początkujących aplikacji na platformie Azure, które chcą zaimplementować miary zabezpieczeń w swoich aplikacjach.

W opracowywaniu i wdrażaniu tej aplikacji dowiesz się, jak:

- Utwórz wystąpienie Azure Key Vault, przechowuj i pobieraj z niego wpisy tajne.
- Wdróż Azure Database for PostgreSQL, skonfiguruj bezpieczne hasła i Autoryzuj do niego dostęp.
- Uruchom kontener Alpine Linux na platformie Azure Web Apps dla systemu Linux i Włącz zarządzane tożsamości dla zasobów platformy Azure.
- Utwórz i skonfiguruj wystąpienie usługi Azure Application Gateway przy użyciu zapory korzystającej z [OWASP 10 najważniejszych zestawów reguł](https://coreruleset.org/).
- Włącz szyfrowanie danych podczas przesyłania i w spoczynku przy użyciu usług platformy Azure.

Po utworzeniu i wdrożeniu tej aplikacji skonfigurujesz następującą przykładową aplikację internetową wraz z opisanymi przez siebie środkami konfiguracji i zabezpieczeń.

![Przykładowa aplikacja internetowa](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architektura

Aplikacja jest typową aplikacją n-warstwową z trzema warstwami. Warstwy frontonu, zaplecza i warstwa bazy danych ze zintegrowanymi składnikami monitorowania i zarządzania kluczami tajnymi przedstawiono tutaj:

![Architektura aplikacji](./media/secure-web-app/architecture.png)

Architektura składa się z następujących składników:

- [Application Gateway platformy Azure](../../application-gateway/index.yml). Zapewnia bramę i zaporę dla naszej architektury aplikacji.
- [Web Apps platformy Azure w systemie Linux](../../app-service/containers/app-service-linux-intro.md). Zapewnia środowisko uruchomieniowe kontenera do uruchamiania aplikacji języka Python w środowisku systemu Linux.
- [Azure Key Vault](../../key-vault/index.yml). Przechowuje i szyfruje wpisy tajne aplikacji i zarządza tworzeniem zasad dostępu.
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/). Bezpieczne przechowywanie danych aplikacji.
- [Azure Security Center](../../security-center/index.yml) i [Application Insights platformy Azure](../../azure-monitor/app/app-insights-overview.md). Oferuje monitorowanie i alerty dotyczące operacji aplikacji.

## <a name="threat-model"></a>Model zagrożeń

Modelowanie zagrożeń polega na zidentyfikowaniu potencjalnych zagrożeń bezpieczeństwa dla Twojej firmy i aplikacji, a następnie zapewnieniu prawidłowego planu zaradczego.

W tym przykładzie użyto [Microsoft Threat Modeling Tool](threat-modeling-tool.md) do zaimplementowania modelowania zagrożeń dla bezpiecznej przykładowej aplikacji. Dzięki utworzeniu diagramu składników i przepływów danych można wcześnie identyfikować problemy i zagrożenia w procesie tworzenia oprogramowania. Pozwala to zaoszczędzić czas i pieniądze później.

Jest to model zagrożeń dla przykładowej aplikacji:

![Model zagrożeń](./media/secure-web-app/threat-model.png)

Niektóre przykładowe zagrożenia i potencjalne luki w zabezpieczeniach generowane przez narzędzie do modelowania zagrożeń przedstawiono na poniższym zrzucie ekranu. Model zagrożeń zawiera przegląd narażonych obszarów ataków i umożliwia deweloperom zaprezentowanie informacji o sposobach łagodzenia problemów.

![Dane wyjściowe modelu zagrożeń](./media/secure-web-app/threat-model-output.png)

Na przykład iniekcja kodu SQL w poprzednim wyjściu modelu zagrożeń jest zmniejszana przez oczyszczanie danych wejściowych użytkownika i używanie funkcji przechowywanych w Azure Database for PostgreSQL. To ograniczenie uniemożliwia wykonywanie zapytań podczas odczytu i zapisu danych.

Deweloperzy zwiększają ogólne zabezpieczenia systemu przez łagodzenie poszczególnych zagrożeń w danych wyjściowych modelu zagrożeń.

## <a name="deployment"></a>Wdrożenie

Poniższe opcje umożliwiają uruchamianie systemu Linux na Azure App Service:

- Wybierz kontener z listy wstępnie skompilowanych kontenerów Microsoft na platformie Azure, które zostały utworzone za pomocą technologii pomocniczych (Python, Ruby, PHP, Java, Node. js, .NET Core).
- Użyj wbudowanego kontenera. Wybierz własne rejestry kontenerów jako źródło obrazu i skompiluj z wieloma dostępnymi technologiami obsługującymi protokół HTTP.

W tym przykładzie zostanie uruchomiony skrypt wdrażania, który wdroży webapp na App Service i utworzy zasoby.

Aplikacja może korzystać z różnych modeli wdrożenia przedstawionych poniżej:

![Diagram przepływu danych wdrożenia](./media/secure-web-app/deployment.png)

Istnieje wiele sposobów wdrażania aplikacji na platformie Azure, w tym:

- Szablony usługi Azure Resource Manager
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Azure Portal
- Azure DevOps

Używana aplikacja:

- Aparat [Docker](https://docs.docker.com/) umożliwia tworzenie i kompilowanie obrazów kontenerów.
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) do wdrożenia.
- [Centrum platformy Docker](https://hub.docker.com/) jako rejestr kontenerów.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

### <a name="network"></a>Sieć

Przykładowa aplikacja używa kompleksowego szyfrowania TLS/SSL dla danych przesyłanych strumieniowo do i z sieci. Brama jest skonfigurowana z certyfikatem z podpisem własnym.
> [!IMPORTANT]
> W tej prezentacji jest używany certyfikat z podpisem własnym. W środowisku produkcyjnym należy uzyskać certyfikaty ze zweryfikowanego urzędu certyfikacji.

Zapora aplikacji sprawdza także przychodzący ruch i alerty administratorów w przypadku wykrycia złośliwego ruchu w ruchu sieciowym.
Application Gateway zmniejszają możliwości dla zagrożeń iniekcji DDoS i SQL odnalezionych w modelu zagrożeń.

### <a name="identity"></a>Tożsamość

Aby zalogować się do portalu, przykładowa aplikacja używa Multi-Factor Authentication dla administratorów usługi Azure Active Directory (Azure AD), którym przypisano dostęp do zasobów.
Aplikacja Przykładowa używa tożsamości zarządzanych, aby uzyskać uprawnienia do odczytu i pobierania wpisów tajnych z Azure Key Vault, dzięki czemu aplikacja nie musi mieć poświadczeń i tokenów kodu, aby odczytać wpisy tajne. Usługa Azure AD automatycznie tworzy jednostki usługi, które aplikacja musi odczytać i zmodyfikować wpisy tajne, gdy są używane tożsamości zarządzane.

Zarządzane tożsamości dla zasobów platformy Azure i usługi MFA utrudniają źródłami ataków uprawnień i eskalacji ich uprawnień w systemie. To zagrożenie zostało wskazane w modelu zagrożeń.
Aplikacja używa protokołu OAuth, który umożliwia użytkownikom zarejestrowanym w aplikacji OAuth logowanie się do aplikacji.

### <a name="storage"></a>Magazyn

Dane w bazie danych PostgreSQL są szyfrowane automatycznie przez Azure Database for PostgreSQL. Baza danych autoryzuje adresy IP App Service tak, aby tylko wdrożona App Service aplikacja internetowa mogła uzyskać dostęp do zasobów bazy danych przy użyciu odpowiednich poświadczeń uwierzytelniania.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Aplikacja implementuje rejestrowanie przy użyciu Application Insights do śledzenia metryk, dzienników i wyjątków, które wystąpiły. To rejestrowanie zapewnia wystarczającą ilość metadanych aplikacji do informowania deweloperów i członków zespołu o stanie aplikacji. Zapewnia również wystarczającą ilość danych do nawrotu w przypadku zdarzeń zabezpieczeń.

## <a name="cost-considerations"></a>Kwestie związane z kosztami

Jeśli nie masz jeszcze konta platformy Azure, możesz utworzyć bezpłatny. Przejdź do [strony bezpłatnego konta](https://azure.microsoft.com/free/) , aby rozpocząć pracę, zobacz co możesz zrobić za pomocą bezpłatnego konta platformy Azure i Dowiedz się, które produkty są bezpłatne przez 12 miesięcy.

Aby wdrożyć zasoby w przykładowej aplikacji z funkcjami zabezpieczeń, musisz uregulować pewne funkcje w warstwie Premium. Gdy aplikacja jest skalowana i warstwy bezpłatne i wersje próbne oferowane przez platformę Azure muszą zostać uaktualnione w celu spełnienia wymagań aplikacji, koszty mogą ulec zwiększeniu. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) platformy Azure, aby oszacować swoje koszty.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

### <a name="prerequisites"></a>Wymagania wstępne

Aby można było uruchomić aplikację, należy zainstalować następujące narzędzia:

- Edytor kodu służący do modyfikowania i wyświetlania kodu aplikacji. [Visual Studio Code](https://code.visualstudio.com/) jest opcją Open Source.
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na komputerze deweloperskim.
- System [git](https://git-scm.com/) w systemie. Git służy do lokalnego klonowania kodu źródłowego.
- [JQ](https://stedolan.github.io/jq/), narzędzie systemu UNIX do wykonywania zapytań w formacie JSON w sposób przyjazny dla użytkownika.

Do wdrożenia zasobów przykładowej aplikacji potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/) w celu przetestowania przykładowej aplikacji.

Po zainstalowaniu tych narzędzi możesz przystąpić do wdrażania aplikacji na platformie Azure.

### <a name="environment-setup"></a>Konfigurowanie środowiska

Uruchom skrypty wdrażania, aby skonfigurować środowisko i subskrypcję:

1. Aby sklonować repozytorium kodu źródłowego, użyj tego polecenia git:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Aby przejść do katalogu, użyj tego polecenia:

   ```shell
   cd tutorial-project/scripts
   ```

3. Istnieją pliki w folderze skryptów, które są specyficzne dla używanej platformy (system Windows lub Linux). Ponieważ interfejs wiersza polecenia platformy Azure został już zainstalowany, zaloguj się do konta platformy Azure przy użyciu monitu o wykonanie tego polecenia platformy Azure:

   ```azurecli-interactive
   az login
   ```

Zostanie otwarta przeglądarka, zaloguj się przy użyciu swoich poświadczeń. Po zalogowaniu można rozpocząć wdrażanie zasobów z poziomu wiersza polecenia.

Skrypty wdrażania `deploy-powershell.ps1` i `deploy-bash.sh` zawierają kod, który wdraża całą aplikację.
Aby wdrożyć rozwiązanie:

1. Jeśli używasz programu PowerShell, uruchom `deploy-powershell.ps1` plik, wpisując `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` zastępowanie regionu i nazwy grupy zasobów odpowiednimi regionami świadczenia usługi Azure i nazwą grupy zasobów
2. Jeśli używasz systemu Linux `deploy-bash.sh` , uruchom plik przez wpisanie polecenia `/deploy-bash.sh REGION RESOURCE_GROUP_NAME` , aby plik wykonywalny mógł być wpisywany przez wpisanie`chmod +x deploy-bash.sh`

W poniższych przykładach przedstawiono fragmenty najważniejszych składników. Przykłady można wdrożyć pojedynczo lub z pozostałymi składnikami, uruchamiając pliki Deploy.

### <a name="implementation-guidance"></a>Wskazówki dotyczące implementacji

Skrypt wdrażania to jeden skrypt, który można podzielić na cztery etapy. Każda faza wdraża i konfiguruje zasób platformy Azure, który znajduje się na [diagramie architektury](#architecture).

Cztery etapy:

- Wdróż Azure Key Vault.
- Wdróż Azure Database for PostgreSQL.
- Wdróż usługę Azure Web Apps w systemie Linux.
- Wdróż Application Gateway za pomocą zapory aplikacji sieci Web.

Każda faza kompiluje się w poprzednim z nich przy użyciu konfiguracji z wcześniej wdrożonych zasobów.

Aby wykonać kroki implementacji, upewnij się, że zainstalowano narzędzia wymienione w sekcji [wymagania wstępne](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Wdróż Azure Key Vault

W tej sekcji utworzysz i wdrażasz wystąpienie Azure Key Vault, które jest używane do przechowywania wpisów tajnych i certyfikatów.

Po zakończeniu wdrażania masz Azure Key Vault wystąpienie wdrożone na platformie Azure.

Aby wdrożyć Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure:

1. Zadeklaruj zmienne dla Azure Key Vault.
2. Zarejestruj dostawcę Azure Key Vault.
3. Utwórz grupę zasobów dla tego wystąpienia.
4. Utwórz wystąpienie Azure Key Vault w grupie zasobów utworzonej w kroku 3.

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

Najlepszym rozwiązaniem jest użycie zarządzanych tożsamości dla zasobów platformy Azure w aplikacjach korzystających z Key Vault w celu uzyskania dostępu do zasobów. Stan zabezpieczeń zwiększa się, gdy klucze dostępu do Key Vault nie są przechowywane w kodzie ani w konfiguracji.

#### <a name="deploy-azure-database-for-postgresql"></a>Wdróż Azure Database for PostgreSQL

Azure Database for PostgreSQL działa w następujący sposób, najpierw utwórz serwer bazy danych, a następnie Utwórz bazę danych, w której ma zostać zapisany schemat i dane.

Po zakończeniu wdrażania masz serwer PostgreSQL i bazę danych działającą na platformie Azure.

Aby wdrożyć Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure:

1. Otwórz Terminal przy użyciu interfejsu wiersza polecenia platformy Azure i konfiguracji subskrypcji platformy Azure.
2. Generuj bezpieczną kombinację nazwy użytkownika i hasła, która jest używana do uzyskiwania dostępu do bazy danych. (Te elementy powinny być przechowywane w Azure Key Vault dla aplikacji, które ich używają).
3. Utwórz wystąpienie serwera PostgreSQL.
4. Utwórz bazę danych w wystąpieniu serwera, która została utworzona w kroku 3.
5. Uruchom skrypty PostgreSQL w wystąpieniu PostgreSQL.

Poniższy kod opiera się na wpisach tajnych PGUSERNAME i PGPASSWORD przechowywanych w magazynie kluczy platformy Azure z powyższego kroku wdrażanie magazynu kluczy.

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Po wdrożeniu bazy danych należy przechowywać jej poświadczenia i parametry połączenia w Azure Key Vault.
W folderze skrypty znajduje się `functions.sql` plik, który zawiera kod pl/pgsql, który tworzy funkcje przechowywane podczas jego uruchamiania. Uruchomienie tego pliku parameterizes dane wejściowe, aby ograniczyć iniekcję kodu SQL.

PostgreSQL jest powiązany z narzędziem o nazwie `psql` , które jest używane do łączenia się z bazą danych. Aby uruchomić `functions.sql` , musisz nawiązać połączenie z wystąpieniem Azure Database for PostgreSQL z komputera lokalnego i uruchomić je stamtąd. Instalacja narzędzia PSQL jest dołączana do domyślnej instalacji programu PostgreSQL w każdym systemie operacyjnym.
Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją PSQL](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell również zawiera `psql` Narzędzie. Cloud Shell można użyć bezpośrednio z Azure Portal, wybierając ikonę Cloud Shell.

Aby włączyć dostęp zdalny do wystąpienia PostgreSQL, należy autoryzować adres IP w PostgreSQL.
Ten dostęp można włączyć, przechodząc do karty **zabezpieczenia połączeń** , wybierając pozycję **Dodaj adres IP klienta**i zapisując nowe ustawienia.

![Autoryzuj adres IP klienta](./media/secure-web-app/add-client-ip-postgres.png)

Jeśli używasz Cloud Shell zamiast lokalnego narzędzia PSQL, wybierz opcję **Zezwalaj na dostęp do usług platformy Azure** i zmień wartość **na na** , aby zezwolić na dostęp Cloud Shell.

Następnie połącz się z wystąpieniem, uruchamiając poniższe polecenie PSQL z parametrami parametrów połączenia z karty parametry **połączenia** w wystąpieniu PostgreSQL na Azure Portal.
Zastąp puste nawiasy klamrowe parametrami w bloku parametry połączenia bazy danych i hasłem z hasłem z Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Po upewnieniu się, że masz połączenie z bazą danych, uruchom następujący skrypt PL/pgSQL. Skrypt tworzy przechowywane funkcje używane do wstawiania danych do bazy danych.

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

Aby uzyskać więcej informacji na temat sposobu konfigurowania weryfikacji protokołu TLS i urzędu certyfikacji dla usługi PostgreSQL, zobacz [Konfigurowanie łączności TLS w Azure Database for PostgreSQL](/azure/postgresql/concepts-ssl-connection-security).

Certyfikat główny znajduje się w kontenerze. Poniżej przedstawiono kroki, które należy wykonać w celu uzyskania certyfikatu:

1. Pobierz plik certyfikatu z [urzędu certyfikacji](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Pobierz i zainstaluj program OpenSSL na swojej maszynie](/azure/postgresql/concepts-ssl-connection-security).
3. Dekodowanie pliku certyfikatu:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Przeczytaj więcej na temat sposobu konfigurowania zabezpieczeń protokołu TLS dla PostgreSQL w tym miejscu [skonfiguruj zabezpieczenia połączeń TLS](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Wdrażanie Web Apps platformy Azure w systemie Linux

Możesz łatwo tworzyć usługi systemu Linux na Azure App Service, ponieważ platforma Azure udostępnia zestaw wstępnie skompilowanych kontenerów i obrazów dla powszechnie używanych języków, takich jak Python, Ruby, C# i Java. Platforma Azure obsługuje również kontenery niestandardowe, co pozwala na uruchamianie praktycznie wszystkich języków programowania na platformie Azure App Service.

Wdrażana aplikacja to prosta aplikacja w języku Python, która jest uruchamiana w najnowszej dystrybucji Ubuntu Linux. Nawiązuje połączenie z wystąpieniami Azure Key Vault i PostgreSQL, które zostały utworzone w poprzednich sekcjach dotyczących zarządzania poświadczeniami i przechowywania danych.

Następujący plik platformy Docker znajduje się w folderze głównym aplikacji:

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

Pliku dockerfile powyżej jest używany do kompilowania kontenera hostowanego na Azure Container Registry w `mcr.microsoft.com/samples/basic-linux-app` .

Poniższy kod:

1. Deklaruje zmienne i nazwy dla wystąpienia App Service.
2. Tworzy grupę zasobów dla planu App Service.
3. Inicjuje obsługę Web Apps platformy Azure w wystąpieniu kontenerów systemu Linux.
4. Włącza rejestrowanie dla kontenera aplikacji sieci Web.
5. Ustawia konfiguracje aplikacji w ustawieniach aplikacji kontenera.

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

Ten skrypt tworzy przypisaną tożsamość dla wystąpienia App Service, które może być używane z programem MSI do współpracy z Azure Key Vault bez wprowadzania stałych kodów tajnych w kodzie lub konfiguracji.

Przejdź do wystąpienia Azure Key Vault w portalu, aby autoryzować przypisaną tożsamość na karcie zasady dostępu. Wybierz pozycję **Dodaj nowe zasady dostępu**. W obszarze **Wybierz podmiot zabezpieczeń**Wyszukaj nazwę aplikacji podobną do nazwy utworzonego wystąpienia App Service.
Nazwa główna usługi dołączona do aplikacji powinna być widoczna. Wybierz ją i Zapisz zasady dostępu, jak pokazano na poniższym zrzucie ekranu.

Ponieważ aplikacja wymaga tylko pobrania kluczy, wybierz uprawnienie **Get** w opcjach wpisów tajnych, co umożliwia dostęp przy jednoczesnym zmniejszeniu przyznanych uprawnień.

![Zasady dostępu Key Vault](./media/secure-web-app/kv-access-policy.png)

*Tworzenie zasad dostępu Key Vault*

Zapisz zasady dostępu, a następnie Zapisz nową zmianę na karcie **zasady dostępu** , aby zaktualizować zasady.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Wdróż Application Gateway z włączoną zaporą aplikacji sieci Web

W usłudze Web Apps nie zaleca się ujawniania usług bezpośrednio w świecie zewnętrznym w Internecie.
Równoważenie obciążenia i reguły zapory zapewniają większe bezpieczeństwo i kontrolę nad ruchem przychodzącym oraz ułatwia zarządzanie nim.

Aby wdrożyć wystąpienie Application Gateway:

1. Utwórz grupę zasobów, w której znajduje się Brama aplikacji.
2. Zainicjuj obsługę sieci wirtualnej w celu dołączenia do bramy.
3. Utwórz podsieć dla bramy w sieci wirtualnej.
4. Zapewnij publiczny adres IP.
5. Zainicjuj obsługę administracyjną bramy aplikacji.
6. Włącz zaporę aplikacji sieci Web na bramie.

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Poprzedni skrypt:

1. Tworzy nowy certyfikat z podpisem własnym na platformie Azure.
2. Pobiera certyfikat z podpisem własnym jako plik kodowany algorytmem Base64.
3. Generuje hasło dla certyfikatu z podpisem własnym.
4. Eksportuje certyfikat jako plik PFX podpisany przy użyciu hasła.
5. Przechowuje hasło certyfikatu w Azure Key Vault.

Ta sekcja wdraża usługę Application Gateway:

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Po zakończeniu wdrażania masz bramę aplikacji z włączoną zaporą aplikacji sieci Web.

Wystąpienie bramy uwidacznia port 443 dla protokołu HTTPS. Ta konfiguracja gwarantuje, że nasza aplikacja jest dostępna tylko na porcie 443 za pośrednictwem protokołu HTTPS.

Najlepszym rozwiązaniem w zakresie zabezpieczeń jest blokowanie nieużywanych portów i ograniczanie ekspozycji narażonych na ataki.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Dodawanie sieciowych grup zabezpieczeń do wystąpienia App Service

Wystąpienia App Service mogą być zintegrowane z sieciami wirtualnymi. Ta Integracja umożliwia skonfigurowanie zasad grupy zabezpieczeń sieci, które zarządzają ruchem przychodzącym i wychodzącym aplikacji.

1. Aby włączyć tę funkcję, w bloku wystąpienie usługi Azure App Service w obszarze **Ustawienia**wybierz pozycję **Sieć**. W prawym okienku w obszarze integracja z siecią **wirtualną**wybierz **pozycję kliknij tutaj, aby ją skonfigurować**.

   ![Nowa integracja sieci wirtualnej](./media/secure-web-app/app-vnet-menu.png)

    *Nowa integracja sieci wirtualnej dla App Service*

1. Na następnej stronie wybierz pozycję **Dodaj sieć wirtualną (wersja zapoznawcza)**.

1. W następnym menu wybierz sieć wirtualną utworzoną w ramach wdrożenia, które zaczyna się od `hello-vnet` . Można utworzyć nową podsieć lub wybrać istniejącą.
   W takim przypadku należy utworzyć nową podsieć. Ustaw **zakres adresów** na **10.0.3.0/24** i nadaj nazwę podsieci **aplikacji**podsieć.

   ![App Service konfigurację sieci wirtualnej](./media/secure-web-app/app-vnet-config.png)

    *Konfiguracja sieci wirtualnej dla App Service*

Po włączeniu integracji sieci wirtualnej możesz dodać sieciowe grupy zabezpieczeń do naszej aplikacji.

1. Użyj pola wyszukiwania, aby wyszukać **sieciowe grupy zabezpieczeń**. W wynikach wybierz pozycję **sieciowe grupy zabezpieczeń** .

    ![Wyszukaj sieciowe grupy zabezpieczeń](./media/secure-web-app/nsg-search-menu.png)

    *Wyszukaj sieciowe grupy zabezpieczeń*

2. W następnym menu wybierz pozycję **Dodaj**. Wprowadź **nazwę** sieciowej grupy zabezpieczeń i **grupę zasobów** , w której ma się znajdować. Ta sieciowej grupy zabezpieczeń zostanie zastosowana do podsieci bramy aplikacji.

    ![Utwórz sieciowej grupy zabezpieczeń](./media/secure-web-app/nsg-create-new.png)

    *Utwórz sieciowej grupy zabezpieczeń*

3. Po utworzeniu sieciowej grupy zabezpieczeń wybierz ją. W jego bloku w obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego**. Skonfiguruj te ustawienia, aby zezwalać na połączenia przychodzące do bramy aplikacji przez port 443.

   ![Konfigurowanie sieciowej grupy zabezpieczeń](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurowanie sieciowej grupy zabezpieczeń*

4. W regułach ruchu wychodzącego dla bramy sieciowej grupy zabezpieczeń Dodaj regułę, która zezwala na połączenia wychodzące z wystąpieniem App Service, tworząc regułę, która odwołuje się do tagu usługi `AppService` :

   ![Dodaj reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Dodaj reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń*

    Dodaj kolejną regułę ruchu wychodzącego, aby umożliwić bramie wysyłanie reguł ruchu wychodzącego do sieci wirtualnej.

   ![Dodaj kolejną regułę ruchu wychodzącego](./media/secure-web-app/nsg-outbound-vnet.png)

    *Dodaj kolejną regułę ruchu wychodzącego*

5. W bloku podsieci sieciowej grupy zabezpieczeń wybierz opcję **Skojarz**, wybierz sieć wirtualną utworzoną we wdrożeniu i wybierz podsieć bramy o nazwie **GW-Subnet**. SIECIOWEJ grupy zabezpieczeń jest stosowana do podsieci.

6. Utwórz kolejną sieciowej grupy zabezpieczeń jak w poprzednim kroku, tym razem z wystąpieniem App Service. Nadaj mu nazwę. Dodaj regułę ruchu przychodzącego dla portu 443, jak w przypadku usługi Application Gateway sieciowej grupy zabezpieczeń.

   Jeśli masz wystąpienie App Service wdrożone w wystąpieniu App Service Environment, które nie jest używane w przypadku tej aplikacji, możesz dodać reguły ruchu przychodzącego, aby umożliwić Azure Service Healthe sondy przez otwarcie portów 454-455 w przychodzących grupach zabezpieczeń App Service sieciowej grupy zabezpieczeń. Oto konfiguracja:

   ![Dodaj reguły dla sond Azure Service Health](./media/secure-web-app/nsg-create-healthprobes.png)

    *Dodaj reguły dla sond Azure Service Health (tylko App Service Environment)*

7. W regułach zabezpieczeń ruchu wychodzącego Utwórz nową regułę zabezpieczeń dla ruchu wychodzącego, która umożliwia App Serviceemu wystąpienie komunikacji z bazą danych PostgreSQL. Skonfiguruj ją następująco:

   ![Reguła zezwalająca na wychodzące połączenia PostgreSQL](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Dodawanie reguły zezwalającej na wychodzące połączenia PostgreSQL*

Aby ograniczyć obszar narażony na ataki, zmodyfikuj ustawienia sieci App Service tak, aby zezwalały na dostęp do aplikacji tylko bramie aplikacji.
W tym celu należy przejść do karty sieć App Service, wybierając kartę **Ograniczenia adresów IP** i utworzyć regułę zezwalania, która zezwala na bezpośredni dostęp do usługi tylko adres IP bramy aplikacji.

Adres IP bramy można pobrać ze strony przeglądowej. Na karcie **adres IP CIDR** wprowadź adres IP w tym formacie: `<GATEWAY_IP_ADDRESS>/32` .

![Zezwalaj tylko na bramę](./media/secure-web-app/app-allow-gw-only.png)

*Zezwalanie na dostęp do App Service tylko za pomocą adresu IP bramy*

#### <a name="implement-azure-active-directory-oauth"></a>Implementowanie Azure Active Directory OAuth

Dokumenty platformy Azure dystrybuowane na przykładowej stronie aplikacji sieci Web są zasobami w naszej aplikacji, które mogą wymagać ochrony. Za pomocą Azure Active Directory (Azure AD) można zaimplementować uwierzytelnianie dla aplikacji internetowych, klasycznych i mobilnych przy użyciu różnych przepływów uwierzytelniania.
Aplikacja używa **logowania w firmie Microsoft**, dzięki czemu aplikacja może odczytywać profile użytkowników, którzy zostali dodani do listy użytkowników usługi Azure AD z jedną dzierżawą.

W Azure Portal Skonfiguruj aplikację tak, aby korzystała z wymaganych poświadczeń:

1. Wybierz pozycję **Azure Active Directory**lub wyszukaj ją przy użyciu pola wyszukiwania.

2. Wybierz pozycję **Nowa rejestracja**:

   ![Utwórz rejestrację](./media/secure-web-app/ad-auth-create.png)

   *Tworzenie rejestracji aplikacji usługi Azure AD*

3. Na następnej stronie Wprowadź nazwę aplikacji. W obszarze **obsługiwane typy kont**wybierz opcję **konta tylko w tym katalogu organizacji**.
    W obszarze **Identyfikator URI przekierowania**wprowadź domenę bazową, w której aplikacja będzie uruchomiona, oraz jeden z punktem końcowym tokenu. Na przykład: *GATEWAY_HASH*. cloudapp.NET/token.

   ![Konfigurowanie rejestracji aplikacji usługi Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Konfigurowanie rejestracji aplikacji usługi Azure AD*

4. Zostanie wyświetlony ekran pokazujący zarejestrowaną aplikację i jej informacje. Musisz dodać te informacje do wystąpienia Azure Key Vault.
   1. Skopiuj identyfikator aplikacji (klienta) i Zapisz go w Key Vault jako `CLIENTID` .
   2. Skopiuj identyfikator URI przekierowania wprowadzony w poprzednim kroku i Zapisz go jako `REDIRECTURI` .
   3. Skopiuj domyślną nazwę katalogu usługi Azure AD, która ma format *name*. microsoftonline.com i Zapisz ją w Key Vault jako `TENANT` .
   4. Przejdź do karty **certyfikaty & dane tajne** utworzonej wcześniej aplikacji usługi Azure AD i wybierz pozycję **Nowy wpis tajny klienta**, jak pokazano na poniższym zrzucie ekranu. Ustaw datę wygaśnięcia, a następnie skopiuj wygenerowaną wartość i Zapisz ją w Key Vault jako `CLIENTSECRET` .

      ![Wpis tajny autoryzacji usługi Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Wpis tajny autoryzacji usługi Azure AD*

   5. Wygeneruj bezpieczny losowy klucz tajny przy użyciu dowolnego narzędzia wiersza polecenia/trybu online. Zapisz ją w Key Vault jako `FLASKSECRETKEY` . Platforma aplikacji używa tego klucza do tworzenia sesji.
        Aby dowiedzieć się, jak wygenerować klucz tajny, zobacz temat [sesje](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Po skonfigurowaniu logowania należy dodać użytkowników do linku usługi Azure AD, aby umożliwić im zalogowanie się do zasobu. Aby je dodać, przejdź do karty **Użytkownicy** w usłudze Azure AD, wybierz pozycję **Wszyscy użytkownicy**, a następnie wybierz pozycję **nowy użytkownik** lub **nowy użytkownik Gość**. Do testowania można dodać użytkownika-gościa i zaprosić użytkownika do katalogu. Możesz też dodać nowego użytkownika, jeśli domena, na której uruchomiona jest aplikacja, została zweryfikowana. W tym przykładzie tylko użytkownicy zarejestrowani w dzierżawie usługi Azure AD mogą być rejestrowani na potrzeby dostępu. Informacje o dostępie do wielodostępnego logowania można znaleźć w dokumentacji.

   ![Dodawanie użytkowników do domeny domyślnej](./media/secure-web-app/ad-auth-add-user.png)

   *Dodawanie użytkowników do domyślnej domeny Azure Active Directory*

Po dodaniu konfiguracji i wpisów tajnych usługi Azure AD do Key Vault użytkownicy mogą uwierzytelniać się w aplikacji przy użyciu uwierzytelniania OAuth platformy Azure.
W kodzie aplikacji jest to obsługiwane przez bibliotekę uwierzytelniania Azure Active Directory (ADAL).

Gdy wpisy tajne znajdują się w Key Vault i aplikacja ma dostęp do wpisów tajnych i bazy danych, Usługa aplikacji może zostać osiągnięta za pośrednictwem adresu URL aplikacji bramy (https: \/ /GATEWAY_HASH. cloudapp. NET), którą można pobrać z jego bloku.

Jeśli po zalogowaniu się do usługi Azure AD zostanie wyświetlony komunikat o błędzie "użytkownik nie jest zarejestrowany w katalogu, do którego próbujesz się zalogować", musisz dodać użytkownika. Aby dodać użytkownika, przejdź do karty **Użytkownicy** usługi Azure AD i ręcznie Dodaj użytkownika, wprowadzając ich szczegóły lub Zaproś użytkownika, wprowadzając swój adres e-mail jako użytkownika-gościa do usługi Azure AD w bloku **zapraszanego gościa** .

#### <a name="deploy-application-insights"></a>Wdrażanie usługi Application Insights
Teraz, gdy aplikacja jest wdrażana i działa, należy obsłużyć błędy występujące w aplikacji wraz z rejestrowaniem i zbieraniem danych śledzenia.
Rejestrowanie i zbieranie danych śledzenia zapewniają wgląd w zdarzenia inspekcji wykonywane w aplikacji.

Application Insights to usługa, która zbiera dzienniki, które mogą być generowane przez użytkowników lub przez system.

Aby utworzyć wystąpienie Application Insights:

1. Wyszukaj **Application Insights** przy użyciu pola wyszukiwania w Azure Portal.
2. Wybierz pozycję **Application Insights**. Podaj szczegóły pokazane tutaj, aby utworzyć wystąpienie.

   ![Tworzenie wystąpienia Application Insights](./media/secure-web-app/app-insights-data.png)

Po zakończeniu wdrażania masz wystąpienie Application Insights.

Po utworzeniu wystąpienia usługi Application Insights należy udostępnić aplikacji klucz instrumentacji, który umożliwia wysyłanie dzienników do chmury. W tym celu można pobrać klucz Application Insights i użyć go w bibliotekach aplikacji udostępnianych przez platformę Azure Application Insights. Najlepszym rozwiązaniem jest przechowywanie kluczy i wpisów tajnych w Azure Key Vault, aby zapewnić ich bezpieczeństwo.

W przypadku podstawowej aplikacji przykładowej, po utworzeniu wystąpienia usługi Application Insights, należy udostępnić aplikacji klucz instrumentacji, który umożliwia działowi IT wysyłanie dzienników do chmury.
W Key Vault Ustaw klucz `APPINSIGHTSKEY` tajny i ustaw jego wartość jako klucz Instrumentacji. Dzięki temu aplikacja może wysyłać dzienniki i metryki do Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Zaimplementuj Multi-Factor Authentication dla Azure Active Directory

Administratorzy muszą upewnić się, że konta subskrypcji w portalu są chronione. Subskrypcja jest narażona na ataki, ponieważ zarządza utworzonymi przez siebie zasobami. Aby chronić subskrypcję, Włącz Multi-Factor Authentication na karcie **Azure Active Directory** subskrypcji.

Usługa Azure AD działa na podstawie zasad, które są stosowane do użytkowników lub grup użytkowników spełniających określone kryteria.
Platforma Azure tworzy domyślne zasady określające, że administratorzy muszą mieć uwierzytelnianie dwuskładnikowe, aby zalogować się do portalu.
Po włączeniu tych zasad może zostać wyświetlony monit o wylogowanie się i ponowne zalogowanie do Azure Portal.

Aby włączyć usługę MFA dla logowania administratora:

1. Przejdź do karty **Azure Active Directory** w Azure Portal
2. W kategorii zabezpieczenia wybierz pozycję dostęp warunkowy. Zostanie wyświetlony następujący ekran:

   ![Dostęp warunkowy — zasady](./media/secure-web-app/ad-mfa-conditional-add.png)

Jeśli nie możesz utworzyć nowych zasad:

1. Przejdź do karty **MFA** .
2. Wybierz link Azure AD — wersja Premium **bezpłatnych wersji próbnych** , aby subskrybować bezpłatną wersję próbną.

   ![Azure AD — wersja Premium bezpłatna wersja próbna](./media/secure-web-app/ad-trial-premium.png)

Wróć do ekranu dostęp warunkowy.

1. Wybierz kartę nowe zasady.
2. Wprowadź nazwę zasad.
3. Wybierz użytkowników lub grupy, dla których chcesz włączyć usługę MFA.
4. W obszarze **kontroli dostępu**wybierz kartę **Grant (Udziel** ), a następnie wybierz opcję **Wymagaj uwierzytelniania wieloskładnikowego** (i inne ustawienia, jeśli chcesz).

   ![Wymaganie uwierzytelniania wieloskładnikowego](./media/secure-web-app/ad-mfa-conditional-add.png)

Zasady można włączyć, zaznaczając pole wyboru w górnej części ekranu lub na karcie **dostęp warunkowy** . Gdy zasady są włączone, użytkownicy potrzebują uwierzytelniania wieloskładnikowego w celu zalogowania się do portalu.

Istnieją zasady podstawowe, które wymagają uwierzytelniania wieloskładnikowego dla wszystkich administratorów platformy Azure. Możesz ją natychmiast włączyć w portalu. Włączenie tych zasad może unieważnić bieżącą sesję i wymusić ponowne zalogowanie.

Jeśli zasady bazowe nie są włączone:

1. Wybierz opcję **Wymagaj uwierzytelniania wieloskładnikowego dla administratorów**.
2. Wybierz pozycję **Użyj zasad natychmiast**.

   ![Wybierz pozycję Użyj zasad natychmiast](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Monitorowanie aplikacji i zasobów przy użyciu wskaźnikowego platformy Azure

W miarę zwiększania się aplikacji trudno jest agregować wszystkie sygnały zabezpieczeń i metryki otrzymane z zasobów i zwiększyć ich użyteczność w sposób zorientowany na działania.

Wskaźnik na platformie Azure jest przeznaczony do zbierania danych, wykrywania możliwych typów zagrożeń i zapewnienia wglądu w zdarzenia związane z bezpieczeństwem.
Podczas oczekiwania na ręczną interwencję Wskaźnik produkcyjny platformy Azure może polegać na przedpisanych elementy playbookie, aby uruchomić alerty i procesy zarządzania zdarzeniami.

Przykładowa aplikacja składa się z kilku zasobów, które mogą być monitorowane przez platformę Azure.
Aby skonfigurować platformę Azure, musisz najpierw utworzyć obszar roboczy Log Analytics, w którym są przechowywane wszystkie dane zebrane z różnych zasobów.

Aby utworzyć ten obszar roboczy:

1. W polu wyszukiwania w Azure Portal Wyszukaj ciąg **log Analytics**. Wybierz **log Analytics obszary robocze**.

   ![Wyszukaj Log Analytics obszary robocze](./media/secure-web-app/sentinel-log-analytics.png)

    *Wyszukaj Log Analytics obszary robocze*

2. Na następnej stronie wybierz pozycję **Dodaj** , a następnie podaj nazwę, grupę zasobów i lokalizację dla obszaru roboczego.
   ![Tworzenie obszaru roboczego usługi Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Tworzenie obszaru roboczego usługi Log Analytics*

3. Użyj pola wyszukiwania, aby wyszukać **platformę Azure**.

   ![Wyszukiwanie ciągu „Azure Sentinel”](./media/secure-web-app/sentinel-add.png)

    *Wyszukiwanie ciągu „Azure Sentinel”*

4. Wybierz pozycję **Dodaj** , a następnie wybierz utworzony wcześniej obszar roboczy log Analytics.

   ![Dodawanie Log Analyticsego obszaru roboczego](./media/secure-web-app/sentinel-workspace-add.png)

    *Dodawanie Log Analyticsego obszaru roboczego*

5. Na stronie **Łączniki danych platformy Azure —** w obszarze **Konfiguracja**wybierz pozycję **Łączniki danych**. Zostanie wyświetlona tablica usług platformy Azure, którą można połączyć z wystąpieniem magazynu Log Analytics, aby przeprowadzić analizę na platformie Azure.

   ![Łączniki danych Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Dodawanie łącznika danych do usługi Azure wskaźnikowego*

   Aby na przykład połączyć się z bramą aplikacji, wykonaj następujące czynności:

   1. Otwórz blok wystąpienia usługi Azure Application Gateway.
   2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
   3. Wybierz pozycję **Dodaj ustawienie diagnostyczne**.

      ![Dodawanie Application Gateway diagnostyki](./media/secure-web-app/sentinel-gateway-connector.png)

      *Dodawanie Application Gateway diagnostyki*

   4. Na stronie **Ustawienia diagnostyczne** wybierz utworzony przez siebie obszar roboczy log Analytics a następnie wybierz wszystkie metryki, które mają być zbierane i wysyłane do funkcji wskaźnikowej platformy Azure. Wybierz pozycję **Zapisz**.

        ![Ustawienia łącznika wskaźnikowego platformy Azure](./media/secure-web-app/sentinel-connector-settings.png)

        *Ustawienia łącznika wskaźnikowego platformy Azure*

  Metryki zasobu znajdują się w wskaźniku na platformie Azure, w którym można wykonywać zapytania i badać je.

   Dodaj te same metryki w ustawieniach diagnostycznych dla Azure Key Vault, publicznego adresu IP, Azure Database for PostgreSQL i wszelkich usług, które obsługują dzienniki diagnostyczne na Twoim koncie.

Po skonfigurowaniu metryk wskaźnik platformy Azure będzie miał dane do analizy.

## <a name="evaluate-and-verify"></a>Oceń i Weryfikuj

Po opracowaniu i wdrożeniu architektury należy upewnić się, że kod i wdrożone usługi spełniają standardy zabezpieczeń. Oto kilka kroków, które można wykonać w celu zweryfikowania oprogramowania:

- Statyczna analiza kodu
- Skanowanie luk w zabezpieczeniach
- Znajdowanie i naprawianie luk w zabezpieczeniach zależności aplikacji

Są to podstawowe bloki konstrukcyjne dla najlepszych rozwiązań związanych z bezpiecznym programowaniem.

### <a name="static-code-analysis"></a>Statyczna analiza kodu

W przypadku przykładowej aplikacji weryfikacja za pomocą narzędzi do analizy statycznej polega na znalezieniu luk w zabezpieczeniach kodu aplikacji przy użyciu technik takich jak sprawdzanie zmian i analiza przepływu danych. Narzędzia do analizy statycznej języka Python zapewniają większą pewność, że aplikacja jest bezpieczna.

**Zaznaczanie błędów**

PyFlakes, biblioteka języka Python Zaznaczanie błędów, pomaga usunąć niedziałający kod i nieużywane funkcje z aplikacji, jak pokazano poniżej:

![PyFlakes](./media/secure-web-app/pyflakes.png)

Zaznaczanie błędów zawiera wskazówki i możliwe zmiany, które mogą sprawić, że kod czyszczący i mniej podatny na błędy w czasie wykonywania.

**PyLint**

PyLint z największą wartością dla tego projektu. Wykonuje on standardowe Sprawdzanie kodu, sprawdzanie błędów i porady dotyczące refaktoryzacji, aby upewnić się, że kod uruchomiony na serwerze jest bezpieczny. Korzystając z PyLint do aktualizowania kodu, możesz wyeliminować usterki i poprawić klasyfikację PyLint, jak pokazują następujące obrazy.

![Przed PyLint](./media/secure-web-app/before-pylint.png)

*Przed PyLint*

Po naprawieniu niektórych błędów kodu znalezionych przez narzędzia Zaznaczanie błędów, masz pewność, że kod nie jest podatny na błędy. Rozwiązanie błędów znacząco zmniejsza ryzyko związane z bezpieczeństwem, które mogą wystąpić, gdy kod jest wdrażany w środowisku produkcyjnym.

![Po pylint](./media/secure-web-app/after-pylint.png)

*Po PyLint*

### <a name="vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach

Narzędzie [zap OWASP](https://www.zaproxy.org/) to skaner luk w zabezpieczeniach aplikacji sieci Web, którego można użyć, aby sprawdzić przykładową aplikację w poszukiwaniu luk w zabezpieczeniach. Uruchomienie narzędzia w przykładowej aplikacji ujawnia pewne błędy i wektory ataków.

![ZAP — narzędzie](./media/secure-web-app/zap-tool.png)

*ZAP — narzędzie*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Znajdowanie i rozwiązywanie luk w zabezpieczeniach zależności aplikacji

Aby znaleźć i naprawić zależności aplikacji, można użyć [kontroli zależności OWASP](https://owasp.org/www-project-dependency-check/).

Bezpieczeństwo jest podobną aplikacją, która sprawdza zależności. Można je znaleźć w serwisie [GitHub](https://github.com/pyupio/safety). Skanuje w poszukiwaniu luk w zabezpieczeniach, które znajdują się w dobrze znanych bazach danych.

![Szkodliw](./media/secure-web-app/pysafety.png)

*Szkodliw*

## <a name="next-steps"></a>Następne kroki

Poniższe artykuły mogą pomóc w projektowaniu, projektowaniu i wdrażaniu bezpiecznych aplikacji.

- [Projekt](secure-design.md)
- [Programowanie](secure-develop.md)
- [Wdrażanie](secure-deploy.md)
