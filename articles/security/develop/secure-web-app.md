---
title: Tworzenie bezpiecznej aplikacji internetowej | Dokumenty firmy Microsoft
description: Ta prosta przykładowa aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które poprawiają stan zabezpieczeń aplikacji i organizacji podczas opracowywania na platformie Azure.
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
ms.openlocfilehash: 730e478622da8cd90af1c559e4d0c6fd04151cca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686806"
---
# <a name="develop-a-secure-web-app"></a>Tworzenie bezpiecznej aplikacji internetowej

W tym przykładzie jest prosta aplikacja języka Python, która wyświetla stronę sieci Web zawierającą łącza do zasobów zabezpieczeń do tworzenia aplikacji na platformie Azure. Aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które mogą pomóc w ulepszaniu aplikacji i postawy zabezpieczeń organizacji podczas tworzenia aplikacji na platformie Azure.

Należy wykonać kroki opisane w tym artykule sekwencyjnie, aby upewnić się, że składniki aplikacji są poprawnie skonfigurowane. Baza danych, usługa Azure App Service, wystąpienie usługi Azure Key Vault i wystąpienie bramy aplikacji platformy Azure zależą od siebie nawzajem.

Skrypty wdrażania skonfigurować infrastrukturę. Po uruchomieniu skryptów wdrażania, należy wykonać pewne ręcznej konfiguracji w witrynie Azure Portal, aby połączyć składniki i usługi razem.

Przykładowa aplikacja jest przeznaczona dla początkujących opracowujących aplikacje na platformie Azure, którzy chcą zaimplementować środki bezpieczeństwa w swoich aplikacjach.

Podczas opracowywania i wdrażania tej aplikacji dowiesz się, jak:

- Utwórz wystąpienie usługi Azure Key Vault, przechowuj i pobieraj z niego wpisy tajne.
- Wdrażanie usługi Azure Database dla postgreSql, konfigurowanie bezpiecznych haseł i autoryzowanie dostępu do niego.
- Uruchom kontener systemu Alpine Linux w usłudze Azure Web Apps dla systemu Linux i włącz zarządzane tożsamości zasobów platformy Azure.
- Tworzenie i konfigurowanie wystąpienia bramy aplikacji platformy Azure za pomocą zapory korzystającej z zestawu [reguł OWASP Top 10](https://coreruleset.org/).
- Włącz szyfrowanie danych podczas przesyłania i odpoczynku przy użyciu usług platformy Azure.

Po opracowaniu i wdrożeniu tej aplikacji zostanie skonfigurowana następująca przykładowa aplikacja internetowa wraz z opisanymi środkami konfiguracji i zabezpieczeń.

![Przykładowa aplikacja internetowa](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architektura

Aplikacja jest typową aplikacją n-warstwową z trzema warstwami. Fronton, back end i warstwa bazy danych ze zintegrowanymi komponentami monitorowania i tajnego zarządzania są pokazane w tym miejscu:

![Architektura aplikacji](./media/secure-web-app/architecture.png)

Architektura składa się z następujących składników:

- [Brama aplikacji platformy Azure](../../application-gateway/index.yml). Zapewnia bramę i zaporę dla naszej architektury aplikacji.
- [Usługa Azure Web Apps w systemie Linux](../../app-service/containers/app-service-linux-intro.md). Udostępnia środowisko wykonawcze kontenera do uruchamiania aplikacji Python w środowisku systemu Linux.
- [Usługa Azure Key Vault](../../key-vault/index.yml). Przechowuje i szyfruje wpisy tajne naszej aplikacji i zarządza tworzeniem zasad dostępu wokół nich.
- [Usługa Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/). Bezpiecznie przechowuje dane naszej aplikacji.
- [Usługa Azure Security Center](../../security-center/index.yml) i [usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Zapewnia monitorowanie i alerty dotyczące działania naszej aplikacji.

## <a name="threat-model"></a>Model zagrożenia

Modelowanie zagrożeń to proces identyfikowania potencjalnych zagrożeń bezpieczeństwa dla firmy i aplikacji, a następnie zapewnienia odpowiedniego planu łagodzenia zmiany klimatu.

W tym przykładzie użyto [narzędzia Microsoft Threat Modeling Tool](threat-modeling-tool.md) do zaimplementowania modelowania zagrożeń dla bezpiecznej przykładowej aplikacji. Diagramowanie składników i przepływów danych można zidentyfikować problemy i zagrożenia na wczesnym etapie procesu tworzenia. Pozwala to zaoszczędzić czas i pieniądze później.

Jest to model zagrożenia dla przykładowej aplikacji:

![Model zagrożenia](./media/secure-web-app/threat-model.png)

Niektóre przykładowe zagrożenia i potencjalne luki w zabezpieczeniach generowane przez narzędzie do modelowania zagrożeń są pokazane na poniższym zrzucie ekranu. Model zagrożenia zawiera przegląd powierzchni ataku narażone i monituje deweloperów, aby zastanowić się, jak złagodzić problemy.

![Wyjście modelu zagrożenia](./media/secure-web-app/threat-model-output.png)

Na przykład iniekcji SQL w poprzednim modelu zagrożenia danych wyjściowych jest złagodzone przez odkażanie danych wejściowych użytkownika i przy użyciu przechowywanych funkcji w usłudze Azure Database dla PostgreSQL. To ograniczenie zapobiega arbitralne wykonywanie zapytań podczas odczytów i zapisów danych.

Deweloperzy poprawić ogólne bezpieczeństwo systemu poprzez łagodzenie każdego z zagrożeń w danych wyjściowych modelu zagrożenia.

## <a name="deployment"></a>Wdrożenie

Następujące opcje umożliwiają uruchamianie systemu Linux w usłudze Azure App Service:

- Wybierz kontener z listy wstępnie utworzonych kontenerów firmy Microsoft na platformie Azure, które zostały utworzone przy pomocy technologii pomocniczych (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Użyj kontenera niestandardowego. Wybierz własne rejestry kontenerów jako źródło obrazu i korzystać z wielu dostępnych technologii, które obsługują HTTP.

W tym przykładzie uruchomisz skrypt wdrażania, który wdroży aplikację sieci Web w usłudze App Service i utworzy zasoby.

Aplikacja może korzystać z różnych modeli wdrażania pokazanych poniżej:

![Diagram przepływu danych wdrożenia](./media/secure-web-app/deployment.png)

Istnieje wiele sposobów wdrażania aplikacji na platformie Azure, w tym:

- Szablony usługi Azure Resource Manager
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Azure Portal
- Azure DevOps

Ta aplikacja używana:

- [Docker](https://docs.docker.com/) do tworzenia i tworzenia obrazów kontenera.
- [Narzędzie wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) do wdrożenia.
- [Centrum platformy Docker](https://hub.docker.com/) jako rejestr kontenerów.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

### <a name="network"></a>Sieć

Przykładowa aplikacja używa szyfrowania TLS/SSL end-to-end do przesyłania danych przesyłanych do i z sieci. Brama jest skonfigurowana z certyfikatem z podpisem własnym.
> [!IMPORTANT]
> W tej demonstracji jest używany certyfikat z podpisem własnym. W środowisku produkcyjnym należy uzyskać certyfikaty od zweryfikowanego urzędu certyfikacji(CA).

Zapora aplikacji sprawdza również ruch przychodzący i ostrzega administratorów po wykryciu złośliwego ruchu w ruchu sieciowym.
Brama aplikacji ogranicza możliwość zagrożeń związanych z wtryskiem DDoS i SQL wykrytych w modelu zagrożeń.

### <a name="identity"></a>Tożsamość

Aby zalogować się do portalu, przykładowa aplikacja używa uwierzytelniania wieloskładnikowego dla administratorów usługi Azure Active Directory (Azure AD), którym przypisano dostęp do zasobów.
Przykładowa aplikacja używa tożsamości zarządzanych, aby uzyskać uprawnienia do odczytu i pobierania wpisów tajnych z usługi Azure Key Vault, zapewniając, że aplikacja nie musi mieć twardych poświadczeń kodu i tokenów, aby odczytać wpisy tajne. Usługa Azure AD automatycznie tworzy jednostki usługi, które aplikacja musi odczytać i modyfikuje wpisy tajne, gdy używane są tożsamości zarządzane.

Zarządzane tożsamości zasobów platformy Azure i usługi MFA utrudniają przeciwnikom uzyskanie uprawnień i eskalację ich uprawnień w systemie. To zagrożenie zostało wskazane w modelu zagrożenia.
Aplikacja używa OAuth, co pozwala użytkownikom zarejestrowanym w aplikacji OAuth zalogować się do aplikacji.

### <a name="storage"></a>Magazyn

Dane w bazie danych PostgreSQL są automatycznie szyfrowane w spoczynku przez usługę Azure Database for PostgreSQL. Baza danych autoryzuje adresy IP usługi App Service, dzięki czemu tylko wdrożona aplikacja sieci web usługi App Service może uzyskać dostęp do zasobów bazy danych przy użyciu odpowiednich poświadczeń uwierzytelniania.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Aplikacja implementuje rejestrowanie przy użyciu usługi Application Insights do śledzenia metryk, dzienników i wyjątków, które występują. To rejestrowanie zapewnia wystarczającą ilość metadanych aplikacji, aby poinformować deweloperów i członków zespołu operacyjnego o stanie aplikacji. Zapewnia również wystarczającą ilość danych do wycofania w przypadku incydentów związanych z bezpieczeństwem.

## <a name="cost-considerations"></a>Kwestie związane z kosztami

Jeśli nie masz jeszcze konta platformy Azure, możesz utworzyć bezpłatne. Przejdź do [strony bezpłatnego konta,](https://azure.microsoft.com/free/) aby rozpocząć, zobacz, co możesz zrobić z bezpłatnym kontem platformy Azure i dowiedz się, które produkty są bezpłatne przez 12 miesięcy.

Aby wdrożyć zasoby w przykładowej aplikacji z funkcjami zabezpieczeń, należy zapłacić za niektóre funkcje premium. Ponieważ aplikacja jest skalowana, a bezpłatne warstwy i wersje próbne oferowane przez platformę Azure muszą zostać uaktualnione w celu spełnienia wymagań aplikacji, koszty mogą wzrosnąć. Kalkulator [cen](https://azure.microsoft.com/pricing/calculator/) platformy Azure służy do szacowania kosztów.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

### <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć działania aplikacji, należy zainstalować następujące narzędzia:

- Edytor kodu do modyfikowania i wyświetlania kodu aplikacji. [Visual Studio Code](https://code.visualstudio.com/) jest opcją open source.
- [Narzędzie CLI platformy Azure](/cli/azure/install-azure-cli) na komputerze deweloperskim.
- [Git](https://git-scm.com/) w systemie. Git jest używany do klonowania kodu źródłowego lokalnie.
- [jq](https://stedolan.github.io/jq/), narzędzie unix do wykonywania zapytań JSON w sposób przyjazny dla użytkownika.

Potrzebujesz subskrypcji platformy Azure, aby wdrożyć zasoby przykładowej aplikacji. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto,](https://azure.microsoft.com/free/) aby przetestować przykładową aplikację.

Po zainstalowaniu tych narzędzi można przystąpić do wdrożenia aplikacji na platformie Azure.

### <a name="environment-setup"></a>Konfigurowanie środowiska

Uruchom skrypty wdrażania, aby skonfigurować środowisko i subskrypcję:

1. Aby sklonować repozytorium kodu źródłowego, użyj tego polecenia Git:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Aby przejść do katalogu, użyj tego polecenia:

   ```shell
   cd tutorial-project/scripts
   ```

3. W folderze skryptów znajdują się pliki specyficzne dla używanej platformy (Windows lub Linux). Ponieważ narzędzie interfejsu wiersza polecenia platformy Azure zostało już zainstalowane, zaloguj się do konta platformy Azure w wierszu polecenia, uruchamiając to polecenie interfejsu wiersza polecenia platformy Azure:

   ```azurecli-interactive
   az login
   ```

Przeglądarka otworzy się, zaloguj się przy użyciu poświadczeń. Po zalogowaniu się można rozpocząć wdrażanie zasobów z wiersza polecenia.

Skrypty `deploy-powershell.ps1` wdrażania `deploy-bash.sh` i zawierają kod, który wdraża całą aplikację.
Aby wdrożyć rozwiązanie:

1. Jeśli korzystasz z programu PowerShell, uruchom `deploy-powershell.ps1` plik, wpisując `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` zastępując nazwę regionu i grupy zasobów odpowiednimi regionami platformy Azure i nazwą grupy zasobów
2. Jeśli jesteś na Linuksie `deploy-bash.sh` `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`uruchomić plik, wpisując , może być trzeba zrobić plik wykonywalny przez wpisanie`chmod +x deploy-bash.sh`

Poniższe przykłady prezentują fragmenty kluczowych składników. Przykłady można wdrożyć indywidualnie lub z pozostałymi składnikami, uruchamiając pliki wdrażania.

### <a name="implementation-guidance"></a>Wytyczne dotyczące wdrażania

Skrypt wdrażania jest jeden skrypt, który można podzielić na cztery fazy. Każda faza wdraża i konfiguruje zasób platformy Azure, który znajduje się na [diagramie architektury.](#architecture)

Cztery fazy to:

- Wdrażanie usługi Azure Key Vault.
- Wdrażanie bazy danych platformy Azure dla postgreSql.
- Wdrażanie aplikacji Azure Web Apps w systemie Linux.
- Wdrażanie bramy aplikacji za pomocą zapory aplikacji sieci Web.

Każda faza opiera się na poprzedniej przy użyciu konfiguracji z wcześniej wdrożonych zasobów.

Aby wykonać kroki implementacji, upewnij się, że zainstalowano narzędzia wymienione w obszarze [Wymagania wstępne](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Wdrażanie usługi Azure Key Vault

W tej sekcji należy utworzyć i wdrożyć wystąpienie usługi Azure Key Vault, które jest używane do przechowywania wpisów tajnych i certyfikatów.

Po zakończeniu wdrażania masz wystąpienie usługi Azure Key Vault wdrożone na platformie Azure.

Aby wdrożyć usługę Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure:

1. Zadeklaruj zmienne dla usługi Azure Key Vault.
2. Zarejestruj dostawcę usługi Azure Key Vault.
3. Utwórz grupę zasobów dla wystąpienia.
4. Utwórz wystąpienie usługi Azure Key Vault w grupie zasobów utworzonej w kroku 3.

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

Najlepszym rozwiązaniem jest używanie tożsamości zarządzanych dla zasobów platformy Azure w aplikacjach korzystających z usługi Key Vault w celu uzyskania dostępu do zasobów. Twoja postawa zabezpieczeń wzrasta, gdy klucze dostępu do usługi Key Vault nie są przechowywane w kodzie ani w konfiguracji.

#### <a name="deploy-azure-database-for-postgresql"></a>Wdrażanie bazy danych platformy Azure dla postgreSQL

Usługa Azure Database for PostgreSQL działa w następujący sposób, najpierw utwórz serwer bazy danych, a następnie utwórz bazę danych, na której ma być przechowywana schemat i dane.

Po zakończeniu wdrażania masz serwer PostgreSQL i bazy danych uruchomionej na platformie Azure.

Aby wdrożyć usługę Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure:

1. Otwórz terminal za pomocą interfejsu wiersza polecenia platformy Azure i konfiguracji subskrypcji platformy Azure.
2. Wygeneruj bezpieczną kombinację nazw użytkowników i haseł, która jest używana do uzyskiwania dostępu do bazy danych. (Powinny one być przechowywane w usłudze Azure Key Vault dla aplikacji, które z nich korzystają).
3. Utwórz wystąpienie serwera PostgreSQL.
4. Utwórz bazę danych w wystąpieniu serwera utworzonym w kroku 3.
5. Uruchom skrypty PostgreSQL w wystąpieniu PostgreSQL.

Poniższy kod opiera się na kluczytowych PGUSERNAME i PGPASSWORD przechowywanych w usłudze Azure KeyVault z kroku wdrażania keyvault powyżej.

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

Po wdrożeniu bazy danych należy przechowywać jej poświadczenia i parametry połączenia w usłudze Azure Key Vault.
W folderze skrypty znajduje `functions.sql` się plik zawierający kod PL/pgSQL, który tworzy zapisane funkcje po jego uruchomieniu. Uruchomienie tego pliku parametryzuje dane wejściowe w celu ograniczenia iniekcji SQL.

PostgreSQL jest dołączony do `psql` narzędzia o nazwie, który jest używany do łączenia się z bazą danych. Aby `functions.sql`uruchomić , należy połączyć się z usługi Azure Database for PostgreSQL wystąpienie z komputera lokalnego i uruchomić go z tego miejsca. Instalacja narzędzia psql jest zawarta w domyślnej instalacji PostgreSQL w każdym systemie operacyjnym.
Aby uzyskać więcej informacji, zobacz [dokumentację psql](https://www.postgresql.org/docs/9.3/app-psql.html).

Usługa Azure Cloud `psql` Shell zawiera również narzędzie. Usługa Cloud Shell można używać bezpośrednio z witryny Azure portal, wybierając ikonę powłoki chmury.

Aby włączyć zdalny dostęp do wystąpienia PostgreSQL, należy autoryzować adres IP w postgreSql.
Możesz włączyć ten dostęp, przechodząc do karty **Zabezpieczenia połączenia,** wybierając pozycję **Dodaj adres IP klienta**i zapisując nowe ustawienia.

![Autoryzowanie adresu IP klienta](./media/secure-web-app/add-client-ip-postgres.png)

Jeśli używasz usługi Cloud Shell zamiast lokalnego narzędzia psql, wybierz pozycję **Zezwalaj na dostęp do usług platformy Azure** i zmień jej wartość na **ON,** aby zezwolić na dostęp do usługi Cloud Shell.

Następnie połącz się z wystąpieniem, uruchamiając poniższe polecenie psql z parametrami ciągu połączenia z karty **Parametry połączenia** wystąpienia PostgreSQL w witrynie Azure portal.
Zastąp puste nawiasy klamrowe parametrami z bloku ciąg połączenia bazy danych i hasłem hasłem z usługi Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Uruchom następujący skrypt PL/pgSQL po upewnieniu się, że masz połączenie z bazą danych. Skrypt tworzy przechowywane funkcje używane do wstawiania danych do bazy danych.

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

Aby uzyskać więcej informacji na temat konfigurowania weryfikacji protokołu TLS i urzędu certyfikacji (CA) dla postgreSQL, zobacz [Konfigurowanie łączności TLS w usłudze Azure Database dla postgreSQL](/azure/postgresql/concepts-ssl-connection-security).

Certyfikat główny znajduje się w kontenerze. Kroki podjęte w celu uzyskania certyfikatu są następujące:

1. Pobierz plik certyfikatu z [urzędu certyfikacji](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Pobierz i zainstaluj OpenSSL na swoim komputerze](/azure/postgresql/concepts-ssl-connection-security).
3. Dekodowanie pliku certyfikatu:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Przeczytaj więcej o tym, jak skonfigurować zabezpieczenia TLS dla PostgreSQL tutaj [Konfiguruj zabezpieczenia połączeń TLS](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Wdrażanie aplikacji Azure Web Apps w systemie Linux

Możesz łatwo tworzyć usługi systemu Linux na platformie Azure App Service, ponieważ platforma Azure udostępnia zestaw wstępnie utworzonych kontenerów i obrazów dla powszechnie używanych języków, takich jak Python, Ruby, C#i Java. Platforma Azure obsługuje również kontenery niestandardowe, które umożliwiają uruchamianie praktycznie wszystkich języków programowania na platformie Azure App Service.

Wdrażana aplikacja to prosta aplikacja Python, która działa w najnowszej dystrybucji Systemu Linux Ubuntu. Łączy się z usługi Azure Key Vault i PostgreSQL wystąpień, które zostały utworzone w poprzednich sekcjach do zarządzania poświadczeniami i przechowywania danych, odpowiednio.

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

Dockerfile powyżej służy do tworzenia kontenera, który jest obsługiwany `mcr.microsoft.com/samples/basic-linux-app`w rejestrze kontenerów platformy Azure w .

Poniższy kod:

1. Deklaruje zmienne i nazwy wystąpienia usługi App Service.
2. Tworzy grupę zasobów dla planu usługi app service.
3. Ape zabezpieczające wystąpienie kontenerów sieci Web platformy Azure w systemie Linux.
4. Umożliwia rejestrowanie kontenera aplikacji sieci web.
5. Ustawia niektóre konfiguracje aplikacji w ustawieniach aplikacji kontenera.

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

Ten skrypt tworzy przypisaną tożsamość dla wystąpienia usługi App Service, które może być używane z msi do interakcji z usługą Azure Key Vault bez twardych wpisów tajnych kodowania w kodzie lub konfiguracji.

Przejdź do wystąpienia usługi Azure Key Vault w portalu, aby autoryzować **Add new access policy**przypisaną tożsamość na karcie zasad dostępu. W obszarze **Wybierz podmiot główny**wyszukaj nazwę aplikacji podobną do nazwy utworzonego wystąpienia usługi App Service.
Podmiot usługi dołączony do aplikacji powinny być widoczne. Wybierz ją i zapisz stronę zasad dostępu, jak pokazano na poniższym zrzucie ekranu.

Ponieważ aplikacja musi tylko pobrać klucze, wybierz **Get** uprawnienia w opcjach wpisów tajnych, umożliwiając dostęp przy jednoczesnym zmniejszeniu przyznanych uprawnień.

![Zasady dostępu do magazynu kluczy](./media/secure-web-app/kv-access-policy.png)

*Tworzenie zasad dostępu do usługi Key Vault*

Zapisz zasady dostępu, a następnie zapisz nową zmianę na karcie **Zasady dostępu,** aby zaktualizować zasady.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Wdrażanie bramy aplikacji z włączoną zaporą aplikacji sieci Web

W aplikacjach sieci web nie zaleca się ujawniania usług bezpośrednio na zewnątrz w Internecie.
Równoważenie obciążenia i reguły zapory zapewniają większe bezpieczeństwo i kontrolę nad ruchem przychodzącym oraz pomagają w zarządzaniu nim.

Aby wdrożyć wystąpienie bramy aplikacji:

1. Utwórz grupę zasobów, aby pomieścić bramę aplikacji.
2. Aprowizuj sieć wirtualną, aby dołączyć do bramy.
3. Utwórz podsieć bramy w sieci wirtualnej.
4. Aprowizuj publiczny adres IP.
5. Aprowizuj bramę aplikacji.
6. Włącz zaporę aplikacji sieci web na bramie.

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
2. Pobiera certyfikat z podpisem własnym jako plik zakodowany base64.
3. Generuje hasło dla certyfikatu z podpisem własnym.
4. Eksportuje certyfikat jako plik PFX podpisany hasłem.
5. Przechowuje hasło certyfikatu w usłudze Azure Key Vault.

W tej sekcji wdraża się bramę aplikacji:

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

Po zakończeniu wdrażania masz bramę aplikacji z włączoną zaporą aplikacji sieci web.

Wystąpienie bramy udostępnia port 443 dla protokołu HTTPS. Ta konfiguracja zapewnia, że nasza aplikacja jest dostępna tylko na porcie 443 za pośrednictwem protokołu HTTPS.

Blokowanie nieużywanych portów i ograniczanie ekspozycji na powierzchnię ataku jest najlepszym rozwiązaniem w zakresie bezpieczeństwa.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Dodawanie sieciowych grup zabezpieczeń do wystąpienia usługi App Service

Wystąpienia usługi App Service można zintegrować z sieciami wirtualnymi. Ta integracja umożliwia ich skonfigurowanie za pomocą zasad sieciowej grupy zabezpieczeń, które zarządzają ruchem przychodzącym i wychodzącym aplikacji.

1. Aby włączyć tę funkcję, w bloku wystąpienia usługi aplikacji Azure w obszarze **Ustawienia**wybierz pozycję **Sieć**. W prawym okienku w obszarze **Integracja sieci wirtualnej**wybierz pozycję **Kliknij tutaj, aby skonfigurować**.

   ![Nowa integracja sieci wirtualnej](./media/secure-web-app/app-vnet-menu.png)

    *Nowa integracja sieci wirtualnej dla usługi App Service*

1. Na następnej stronie wybierz pozycję **Dodaj wirtualną (podgląd)**.

1. W następnym menu wybierz sieć wirtualną utworzoną `hello-vnet`we wdrożeniu rozpoczynającym się od programu . Można utworzyć nową podsieć lub wybrać istniejącą.
   W takim przypadku należy utworzyć nową podsieć. Ustaw **zakres adresów** na **10.0.3.0/24** i nazwij **podsieć podsieci**.

   ![Konfiguracja sieci wirtualnej usługi App Service](./media/secure-web-app/app-vnet-config.png)

    *Konfiguracja sieci wirtualnej dla usługi App Service*

Po włączeniu integracji z siecią wirtualną możesz dodać sieciowe grupy zabezpieczeń do naszej aplikacji.

1. Użyj pola wyszukiwania, wyszukaj **sieciowe grupy zabezpieczeń**. W wynikach wybierz pozycję **Sieciowe grupy zabezpieczeń.**

    ![Wyszukiwanie grup zabezpieczeń sieci](./media/secure-web-app/nsg-search-menu.png)

    *Wyszukiwanie grup zabezpieczeń sieci*

2. W następnym menu wybierz pozycję **Dodaj**. Wprowadź **nazwę** grupy zasobów i **zasobu,** w której powinna się znajdować. Ta grupa sieciowa zostanie zastosowana do podsieci bramy aplikacji.

    ![Tworzenie nsg](./media/secure-web-app/nsg-create-new.png)

    *Tworzenie nsg*

3. Po utworzeniu strony bliskiejświatła wybierz ją. W jego bloku w obszarze **Ustawienia**wybierz pozycję **Reguły zabezpieczeń przychodzących**. Skonfiguruj te ustawienia, aby umożliwić połączenia przychodzące do bramy aplikacji przez port 443.

   ![Konfigurowanie ndg](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurowanie ndg*

4. W regułach ruchu wychodzącego dla sieciowej sieciowej bramy dodaj regułę zezwalającą na połączenia wychodzące z `AppService`wystąpieniem usługi App Service, tworząc regułę przeznaczoną dla tagu usługi:

   ![Dodawanie reguł ruchu wychodzącego dla sieciowej sieciowej sieciowej](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Dodawanie reguł ruchu wychodzącego dla sieciowej sieciowej sieciowej*

    Dodaj inną regułę ruchu wychodzącego, aby umożliwić bramie wysyłanie reguł ruchu wychodzącego do sieci wirtualnej.

   ![Dodawanie innej reguły ruchu wychodzącego](./media/secure-web-app/nsg-outbound-vnet.png)

    *Dodawanie innej reguły ruchu wychodzącego*

5. W bloku podsieci sieciowej sieciowej wybierz pozycję **Skojarz**, wybierz sieć wirtualną utworzoną we wdrożeniu i wybierz podsieć bramy o nazwie **gw-podsieć**. Grupa sieciowa jest stosowana do podsieci.

6. Utwórz inną sieć wg, jak we wcześniejszym kroku, tym razem dla wystąpienia usługi App Service. Nadaj mu nazwę. Dodaj regułę ruchu przychodzącego dla portu 443, tak jak w przypadku sieciowej sieciowej bramy aplikacji.

   Jeśli masz wystąpienie usługi App Service wdrożone w wystąpieniu środowiska usługi app service, co nie ma miejsca dla tej aplikacji, można dodać reguły przychodzące, aby zezwolić na sondy usługi Azure Service Health, otwierając porty 454-455 w przychodzących grupach zabezpieczeń sieciowej usługi app Service. Oto konfiguracja:

   ![Dodawanie reguł dla sond kondycji usługi Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Dodawanie reguł dla sond kondycji usługi Azure (tylko środowisko usługi app service)*

7. W regułach zabezpieczeń wychodzących utwórz nową regułę zabezpieczeń wychodzących, która umożliwia wystąpieniu usługi App Service komunikowanie się z bazą danych PostgreSQL. Skonfiguruj go w ten sposób:

   ![Reguła zezwalania na wychodzące połączenia PostgreSQL](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Dodawanie reguły zezwalania na połączenia wychodzące PostgreSQL*

Aby ograniczyć obszar ataku, zmodyfikuj ustawienia sieci usługi App Service, aby zezwolić tylko bramie aplikacji na dostęp do aplikacji.
W tym celu przejdź do karty sieci usługi app service, wybierając kartę **Ograniczenia IP** i tworząc regułę zezwalania, która umożliwia bezpośredni dostęp do usługi tylko na adresIE IP bramy aplikacji.

Adres IP bramy można pobrać ze strony przeglądu. Na karcie **Adres IP CIDR** wprowadź adres IP `<GATEWAY_IP_ADDRESS>/32`w tym formacie: .

![Zezwalaj tylko na bramę](./media/secure-web-app/app-allow-gw-only.png)

*Zezwalaj tylko adresowi IP bramy na dostęp do usługi App Service*

#### <a name="implement-azure-active-directory-oauth"></a>Implementowanie usługi Azure Active Directory OAuth

Dokumenty platformy Azure dystrybuowane na przykładowej stronie aplikacji sieci web to zasoby w naszej aplikacji, które mogą wymagać ochrony. Za pomocą usługi Azure Active Directory (Azure AD) można zaimplementować uwierzytelnianie dla aplikacji sieci web, komputerów i aplikacji mobilnych przy użyciu różnych przepływów uwierzytelniania.
Aplikacja używa **logowania za pomocą firmy Microsoft,** który umożliwia aplikacji do odczytywania profili użytkowników, którzy zostali dodana do naszej listy użytkowników usługi Azure AD jednej dzierżawy.

W witrynie Azure Portal skonfiguruj aplikację tak, aby używała wymaganych poświadczeń:

1. Wybierz **pozycję Azure Active Directory**lub wyszukaj ją za pomocą pola wyszukiwania.

2. Wybierz **nową rejestrację**:

   ![Tworzenie rejestracji](./media/secure-web-app/ad-auth-create.png)

   *Tworzenie rejestracji aplikacji usługi Azure AD*

3. Na następnej stronie wprowadź nazwę aplikacji. W obszarze **Obsługiwane typy kont**wybierz pozycję **Konta tylko w tym katalogu organizacyjnym**.
    W obszarze **Przekierowanie identyfikatora URI**wprowadź domenę podstawową, w którą aplikacja będzie działać plus jeden z punktem końcowym tokenu. Na przykład: *GATEWAY_HASH*.cloudapp.net/token.

   ![Konfigurowanie rejestracji aplikacji usługi Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Konfigurowanie rejestracji aplikacji usługi Azure AD*

4. Zostanie wyświetlony ekran z zarejestrowaną aplikacją i jej informacjami. Należy dodać te informacje do wystąpienia usługi Azure Key Vault.
   1. Skopiuj identyfikator aplikacji (klienta) i `CLIENTID`zapisz go w magazynie kluczy jako .
   2. Skopiuj identyfikator URI przekierowania wprowadzony w `REDIRECTURI`poprzednim kroku i zapisz go jako .
   3. Skopiuj domyślną nazwę katalogu usługi Azure AD, która ma *nazwę*formatu .microsoftonline.com, i zapisz ją w magazynie kluczy jako `TENANT`.
   4. Przejdź do karty **Certyfikaty & wpisy tajne** aplikacji usługi Azure AD utworzonej wcześniej i wybierz pozycję **Nowy klucz tajny klienta**, jak pokazano na poniższym zrzucie ekranu. Ustaw datę wygaśnięcia, a następnie skopiuj wygenerowaną wartość i zapisz ją w magazynie kluczy jako `CLIENTSECRET`.

      ![Klucz tajny autoryzacji usługi Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Klucz tajny autoryzacji usługi Azure AD*

   5. Wygeneruj bezpieczny losowy tajny klucz za pomocą dowolnego narzędzia wiersza polecenia/online. Zapisz go w `FLASKSECRETKEY`magazynie kluczy jako . Struktura aplikacji używa tego klucza do tworzenia sesji.
        Aby dowiedzieć się, jak wygenerować tajny klucz, zobacz [Sesje kolby](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Po skonfigurowaniu logowania należy dodać użytkowników do łącza usługi Azure AD, aby umożliwić im zalogowanie się do zasobu. Aby je dodać, przejdź do karty **Użytkownicy** w usłudze Azure AD, wybierz pozycję **Wszyscy użytkownicy**, a następnie wybierz pozycję **Nowy użytkownik** lub **Nowy użytkownik-gość**. Do testowania można dodać użytkownika-gościa i zaprosić go do katalogu. Możesz też dodać nowego użytkownika, jeśli domena, na której działa aplikacja, została zweryfikowana. W tym przykładzie tylko użytkownicy zarejestrowani w dzierżawie usługi Azure AD mogą być zarejestrowane dla dostępu. Aby uzyskać informacje na temat dostępu do logowania wielodostępnego, zobacz dokumentację.

   ![Dodawanie użytkowników do domeny domyślnej](./media/secure-web-app/ad-auth-add-user.png)

   *Dodawanie użytkowników do domyślnej domeny usługi Azure Active Directory*

Po dodaniu konfiguracji usługi Azure AD i wpisów tajnych do usługi Key Vault użytkownicy mogą być uwierzytelnieni w aplikacji przy użyciu uwierzytelniania usługi Azure OAuth.
W kodzie aplikacji jest to obsługiwane przez bibliotekę uwierzytelniania usługi Azure Active Directory (ADAL).

Po wpisy tajne są w przechowalni kluczy, a aplikacja ma dostęp do wpisów tajnych\/i bazy danych, usługa aplikacji można uzyskać za pośrednictwem adresu URL aplikacji bramy (https: /GATEWAY_HASH.cloudapp.net), które można uzyskać z jego bloku.

Jeśli podczas logowania się do usługi Azure AD pojawia się komunikat "Użytkownik nie jest zarejestrowany w katalogu, do którego próbujesz się zalogować", musisz dodać użytkownika. Aby dodać użytkownika, przejdź do **użytkowników** kartę Usługi Azure AD i dodać użytkownika ręcznie, wprowadzając jego szczegóły lub zaprosić użytkownika, wprowadzając ich adres e-mail jako użytkownik-gość do usługi Azure AD w bloku **Zaproszony gość.**

#### <a name="deploy-application-insights"></a>Wdrażanie usługi Application Insights
Teraz, gdy aplikacja jest wdrożona i działa, należy obsługiwać błędy występujące w aplikacji wraz z rejestrowania i śledzenia zbierania danych.
Rejestrowanie i śledzenie zbierania danych zapewnia widok na zdarzenia inspekcji, które mają miejsce w aplikacji.

Usługa Application Insights to usługa, która zbiera dzienniki, które mogą być generowane przez użytkowników lub przez system.

Aby utworzyć wystąpienie usługi Application Insights:

1. **Wyszukaj aplikację Usługi Insights** przy użyciu pola wyszukiwania w witrynie Azure portal.
2. Wybierz pozycję **Application Insights**. Podaj szczegóły pokazane tutaj, aby utworzyć wystąpienie.

   ![Tworzenie wystąpienia usługi Application Insights](./media/secure-web-app/app-insights-data.png)

Po zakończeniu wdrażania masz wystąpienie usługi Application Insights.

Po utworzeniu wystąpienia usługi Applications Insights należy uświadomić aplikacji klucz instrumentacji, który umożliwia wysyłanie dzienników do chmury. Można to zrobić, pobierając klucz usługi Application Insights i używając go w bibliotekach aplikacji, które platforma Azure zapewnia dla usługi Application Insights. Najlepszym rozwiązaniem jest przechowywanie kluczy i wpisów tajnych w usłudze Azure Key Vault, aby zapewnić im bezpieczeństwo.

W przypadku podstawowej przykładowej aplikacji po utworzeniu wystąpienia usługi Applications Insights należy uświadomić aplikacji klucz instrumentacji, który umożliwia wysyłanie dzienników do chmury.
W przechowalni kluczy `APPINSIGHTSKEY` ustaw klucz tajny i ustaw jego wartość jako klucz instrumentacji. Dzięki temu aplikacja może wysyłać dzienniki i metryki do usługi Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementowanie uwierzytelniania wieloskładnikowego dla usługi Azure Active Directory

Administratorzy muszą upewnić się, że konta subskrypcji w portalu są chronione. Subskrypcja jest narażona na ataki, ponieważ zarządza utworzonymi zasobami. Aby chronić subskrypcję, włącz uwierzytelnianie wieloskładnikowe na karcie **Usługi Azure Active Directory** w ramach subskrypcji.

Usługa Azure AD działa na podstawie zasad, które są stosowane do użytkownika lub grup użytkowników, które pasują do określonych kryteriów.
Platforma Azure tworzy domyślną zasadę określającą, że administratorzy potrzebują uwierzytelniania dwuskładnikowego, aby zalogować się do portalu.
Po włączeniu tej zasady może zostać wyświetlony monit o wylogowanie się i zalogowanie się z powrotem do witryny Azure portal.

Aby włączyć usługę MFA dla logowania administratora:

1. Przejdź do karty **Usługi Azure Active Directory** w witrynie Azure portal
2. W kategorii zabezpieczeń wybierz dostęp warunkowy. Zostanie wyświetlony następujący ekran:

   ![Dostęp warunkowy — zasady](./media/secure-web-app/ad-mfa-conditional-add.png)

Jeśli nie możesz utworzyć nowej zasady:

1. Przejdź do karty **usługi MFA.**
2. Wybierz łącze **wersji próbnej** bezpłatnej usługi Azure AD Premium, aby zasubskrybować bezpłatną wersję próbną.

   ![Bezpłatna wersja próbna usługi Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Powrót do ekranu dostępu warunkowego.

1. Wybierz nową kartę zasad.
2. Wprowadź nazwę zasad.
3. Wybierz użytkowników lub grupy, dla których chcesz włączyć usługę MFA.
4. W obszarze **Formanty dostępu**wybierz kartę **Przyznanie,** a następnie wybierz pozycję **Wymagaj uwierzytelniania wieloskładnikowego** (i innych ustawień).

   ![Wymaganie usługi MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Zasady można włączyć, zaznaczając pole wyboru u góry ekranu lub na karcie **Dostęp warunkowy.** Gdy zasada jest włączona, użytkownicy potrzebują usługi MFA, aby zalogować się do portalu.

Istnieje zasada linii bazowej, która wymaga usługi MFA dla wszystkich administratorów platformy Azure. Można go włączyć natychmiast w portalu. Włączenie tych zasad może unieważnić bieżącą sesję i wymusić ponowne zalogowanie się.

Jeśli zasady bazowe nie są włączone:

1. Wybierz pozycję **Wymagaj usługi MFA dla administratorów**.
2. Natychmiast wybierz pozycję **Użyj zasad**.

   ![Natychmiast wybierz pozycję Użyj zasad](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Monitorowanie aplikacji i zasobów za pomocą usługi Azure Sentinel

W miarę rozwoju aplikacji staje się trudne do agregowania wszystkich sygnałów zabezpieczeń i metryki otrzymane z zasobów i uczynić je przydatne w sposób zorientowany na akcję.

Usługa Azure Sentinel jest przeznaczona do zbierania danych, wykrywania typów zagrożeń i zapewniania wglądu w zdarzenia związane z zabezpieczeniami.
Podczas oczekiwania na ręczną interwencję usługa Azure Sentinel może polegać na wstępnie napisanych podręcznikach, aby rozpocząć alerty i procesy zarządzania incydentami.

Przykładowa aplikacja składa się z kilku zasobów, które usługa Azure Sentinel może monitorować.
Aby skonfigurować usługę Azure Sentinel, należy najpierw utworzyć obszar roboczy usługi Log Analytics, który przechowuje wszystkie dane zebrane z różnych zasobów.

Aby utworzyć ten obszar roboczy:

1. W polu wyszukiwania w witrynie Azure portal wyszukaj **usługę Log Analytics**. Wybierz **obszary robocze usługi Log Analytics**.

   ![Wyszukiwanie obszarów roboczych usługi Log Analytics](./media/secure-web-app/sentinel-log-analytics.png)

    *Wyszukiwanie obszarów roboczych usługi Log Analytics*

2. Na następnej stronie wybierz pozycję **Dodaj,** a następnie podaj nazwę, grupę zasobów i lokalizację obszaru roboczego.
   ![Tworzenie obszaru roboczego usługi Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Tworzenie obszaru roboczego usługi Log Analytics*

3. Użyj pola wyszukiwania, aby wyszukać **usługę Azure Sentinel**.

   ![Wyszukiwanie ciągu „Azure Sentinel”](./media/secure-web-app/sentinel-add.png)

    *Wyszukiwanie ciągu „Azure Sentinel”*

4. Wybierz **pozycję Dodaj,** a następnie wybierz utworzony wcześniej obszar roboczy usługi Log Analytics.

   ![Dodawanie obszaru roboczego usługi Log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Dodawanie obszaru roboczego usługi Log Analytics*

5. Na stronie **Azure Sentinel — łączniki danych** w obszarze **Konfiguracja**wybierz pozycję **Łączniki danych**. Zobaczysz tablicę usług platformy Azure, które można połączyć z wystąpieniem magazynu usługi Log Analytics do analizy w usłudze Azure Sentinel.

   ![Łączniki danych usługi Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Dodawanie łącznika danych do usługi Azure Sentinel*

   Na przykład, aby połączyć bramę aplikacji, wykonaj następujące kroki:

   1. Otwórz blok wystąpienia bramy aplikacji platformy Azure.
   2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
   3. Wybierz **pozycję Dodaj ustawienie diagnostyczne**.

      ![Dodawanie diagnostyki bramy aplikacji](./media/secure-web-app/sentinel-gateway-connector.png)

      *Dodawanie diagnostyki bramy aplikacji*

   4. Na stronie **Ustawienia diagnostyczne** wybierz utworzony obszar roboczy usługi Log Analytics, a następnie wybierz wszystkie metryki, które chcesz zebrać i wysłać do usługi Azure Sentinel. Wybierz pozycję **Zapisz**.

        ![Ustawienia łącznika usługi Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Ustawienia łącznika usługi Azure Sentinel*

  Metryki z zasobu znajdują się w usłudze Azure Sentinel, gdzie można je wysyłać i badać.

   Dodaj te same metryki w ustawieniach diagnostycznych dla usługi Azure Key Vault, publicznego adresu IP, usługi Azure Database for PostgreSQL i wszystkich usług obsługujących dzienniki diagnostyczne na koncie.

Po skonfigurowaniu metryki usługi Azure Sentinel ma dane do analizy.

## <a name="evaluate-and-verify"></a>Oceń i sprawdź

Po opracowaniu i wdrożeniu architektury należy upewnić się, że kod i wdrożone usługi spełniają standardy zabezpieczeń. Oto kilka kroków, które można wykonać w celu weryfikacji oprogramowania:

- Statyczna analiza kodu
- Skanowanie luk w zabezpieczeniach
- Znajdowanie i naprawianie luk w zależnościach aplikacji

Są to podstawowe elementy składowe najlepszych rozwiązań w zakresie bezpiecznego programowania.

### <a name="static-code-analysis"></a>Statyczna analiza kodu

W przypadku przykładowej aplikacji weryfikacja za pomocą narzędzi analizy statycznej polega na znajdowaniu luk w kodzie aplikacji przy użyciu technik, takich jak sprawdzanie skazy i analiza przepływu danych. Narzędzia analizy statycznej języka Python zapewniają większą pewność, że aplikacja jest bezpieczna.

**Zaznaczanie błędów**

PyFlakes, biblioteka linting Python, pomaga usunąć martwy kod i nieużywane funkcje z aplikacji, jak pokazano poniżej:

![Płatki pyflakes](./media/secure-web-app/pyflakes.png)

Linting zawiera wskazówki i możliwe zmiany, które mogą uczynić kod czystszym i mniej podatne na błędy w czasie wykonywania.

**PyLint ( PyLint )**

PyLint pod warunkiem, że największą wartość dla tego projektu. Wykonuje kontrole standardowe kodu, sprawdzanie błędów i refaktoryzacji porady, aby upewnić się, że kod uruchomiony na serwerze jest bezpieczny. Za pomocą PyLint, aby zaktualizować kod, można wyeliminować błędy i poprawić klasyfikację PyLint, jak pokazują poniższe obrazy.

![Przed PyLint](./media/secure-web-app/before-pylint.png)

*Przed PyLint*

Po naprawieniu niektórych błędów kodu znalezionych przez narzędzia linting, masz większą pewność, że kod nie jest podatny na błędy. Naprawienie błędów znacznie zmniejsza zagrożenia bezpieczeństwa, które mogą wystąpić, gdy kod jest wdrażany w środowiskach produkcyjnych.

![Po Pylint](./media/secure-web-app/after-pylint.png)

*Po PyLint*

### <a name="vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach

Narzędzie [ZAP firmy OWASP to](https://www.zaproxy.org/) skaner luk w zabezpieczeniach aplikacji sieci web typu open source, którego można użyć do sprawdzenia przykładowej aplikacji pod kątem luk w zabezpieczeniach. Uruchomienie narzędzia w przykładowej aplikacji ujawnia niektóre możliwe błędy i wektory ataku.

![Narzędzie ZAP](./media/secure-web-app/zap-tool.png)

*Narzędzie ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Znajdowanie i naprawianie luk w zależnościach od aplikacji

Aby znaleźć i naprawić zależności aplikacji, można użyć [funkcji Sprawdzanie zależności programu OWASP](https://owasp.org/www-project-dependency-check/).

Bezpieczeństwo jest podobną aplikacją, która sprawdza zależności. Można go znaleźć na [GitHub](https://github.com/pyupio/safety). Skanowanie bezpieczeństwa w poszukiwaniu luk znalezionych w dobrze znanych bazach danych luk w zabezpieczeniach.

![Bezpieczeństwa](./media/secure-web-app/pysafety.png)

*Bezpieczeństwa*

## <a name="next-steps"></a>Następne kroki

Poniższe artykuły mogą pomóc w projektowaniu, opracowywaniu i wdrażaniu bezpiecznych aplikacji.

- [Projekt](secure-design.md)
- [Programowanie](secure-develop.md)
- [Wdróż](secure-deploy.md)
