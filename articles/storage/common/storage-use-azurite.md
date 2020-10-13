---
title: Korzystanie z emulatora azurite na potrzeby tworzenia lokalnych magazynów platformy Azure
description: Emulator typu open source azurite udostępnia bezpłatne środowisko lokalne do testowania aplikacji usługi Azure Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: f18746242ef9f680f44be1fd614c6c769289aadb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331577"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>Korzystanie z emulatora azurite na potrzeby tworzenia lokalnych magazynów platformy Azure

Emulator typu open source azurite udostępnia bezpłatne środowisko lokalne do testowania aplikacji magazynu obiektów blob platformy Azure i usługi Queue. Gdy aplikacja działa lokalnie, przejdź do korzystania z konta usługi Azure Storage w chmurze. Emulator zapewnia obsługę wielu platform w systemach Windows, Linux i macOS.

Azurite to przyszła platforma emulatora magazynu. Azurite zastępuje [emulator usługi Azure Storage](storage-use-emulator.md). Azurite będzie nadal aktualizowana w celu obsługi najnowszych wersji interfejsów API usługi Azure Storage.

Istnieje kilka różnych sposobów instalowania i uruchamiania programu azurite w systemie lokalnym:

  1. [Instalowanie i uruchamianie rozszerzenia azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalowanie i uruchamianie azurite za pomocą NPM](#install-and-run-azurite-by-using-npm)
  1. [Instalowanie i uruchamianie obrazu platformy Docker azurite](#install-and-run-the-azurite-docker-image)
  1. [Klonowanie, kompilowanie i uruchamianie azurite z repozytorium GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalowanie i uruchamianie rozszerzenia azurite Visual Studio Code

W obszarze Visual Studio Code Wybierz okienko **rozszerzenia** i wyszukaj ciąg *azurite* w **witrynie rozszerzenia: Marketplace**.

![Portal Visual Studio Code rozszerzenia Marketplace](media/storage-use-azurite/azurite-vs-code-extension.png)

Możesz również przejść do [Visual Studio Code rynku rozszerzenia](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) w przeglądarce. Wybierz przycisk **Zainstaluj** , aby otworzyć Visual Studio Code i przejdź bezpośrednio do strony rozszerzenia azurite.

Rozszerzenie obsługuje następujące polecenia Visual Studio Code. Aby otworzyć paletę poleceń, naciśnij klawisz F1 w Visual Studio Code. 

   - **Azurite: Wyczyść** /Zresetuj wszystkie dane utrwalenie usług azurite Services
   - **Azurite: czyszczenie usługi BLOB Service** — Clean BLOB Service
   - **Azurite: czyszczenie kolejki** — usługa kolejki czystej
   - **Azurite: Zamknij** i Zamknij wszystkie usługi azurite
   - **Azurite: Zamknij usługę BLOB Service** — zamknij usługę BLOB Service
   - **Azurite: Zamknij usługę kolejki** — zamknij usługę kolejki
   - **Azurite: Uruchom** i uruchom wszystkie usługi azurite
   - **Azurite: uruchamianie usługi BLOB Service** — uruchamianie usługi BLOB Service
   - **Azurite: Uruchom usługę kolejki** — Uruchom usługę kolejki

Aby skonfigurować azurite w Visual Studio Code, zaznacz okienko rozszerzenia. Wybierz ikonę **zarządzania** (koła zębatego) dla **azurite**. Wybierz pozycję **Ustawienia rozszerzenia**.

![Azurites Skonfiguruj ustawienia rozszerzenia](media/storage-use-azurite/azurite-configure-extension-settings.png)

Obsługiwane są następujące ustawienia:

   - **Azurite: Host obiektów BLOB** — punkt końcowy nasłuchiwania BLOB Service. Ustawieniem domyślnym jest 127.0.0.1.
   - **Azurite: Port obiektu BLOB** — port nasłuchujący BLOB Service. Domyślnym portem jest 10000.
   - **Azurite: CERT** -Path z lokalną zaufaną PEM lub ścieżką pliku certyfikatu PFX, aby włączyć tryb https.
   - **Azurite: Debuguj** — wyprowadza Dziennik debugowania do kanału azurite. Wartość domyślna to **fałsz**.
   - **Azurite: klucz** -ścieżka do lokalnego pliku klucza PEM, wymagane, gdy **azurite: certyfikat** wskazuje plik PEM.
   - **Azurite: Location** — ścieżka lokalizacji obszaru roboczego. Wartość domyślna to Visual Studio Code folder roboczy.
   - **Azurite: luźno** Włącz tryb luźny, który ignoruje nieobsługiwane nagłówki i parametry.
   - **Azurite:** poziom OAuth z opcjonalnym uwierzytelnianiem OAuth.
   - **Azurite: PWD** — hasło dla pliku PFX. Wymagane, gdy **azurite:** przywołuje do pliku PFX.
   - **Azurite: Host kolejki** — punkt końcowy nasłuchiwania usługa kolejki. Ustawieniem domyślnym jest 127.0.0.1.
   - **Azurite: Port kolejki** — port nasłuchujący usługa kolejki. Domyślnym portem jest 10001.
   - **Azurite: tryb dyskretny** powoduje wyłączenie dziennika dostępu. Wartość domyślna to **fałsz**.
   - **Azurite: Pomiń sprawdzanie wersji interfejsu API** — Pomiń sprawdzanie wersji interfejsu API żądania. Wartość domyślna to **fałsz**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instalowanie i uruchamianie azurite za pomocą NPM

Ta metoda instalacji wymaga, aby zainstalowano [Node.js w wersji 8,0 lub nowszej](https://nodejs.org) . Menedżer pakietów node (npm) to narzędzie do zarządzania pakietami dołączone do każdej instalacji Node.js. Po zainstalowaniu Node.js wykonaj następujące polecenie, `npm` Aby zainstalować azurite.

```console
npm install -g azurite
```

Po zainstalowaniu azurite należy zapoznać się z tematem [Run azurite z poziomu wiersza polecenia](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instalowanie i uruchamianie obrazu platformy Docker azurite

Użyj [DockerHub](https://hub.docker.com/) , aby ściągnąć [najnowszy obraz azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) przy użyciu następującego polecenia:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Uruchom obraz platformy Docker azurite**:

Następujące polecenie uruchamia obraz platformy Docker azurite. `-p 10000:10000`Parametr przekierowuje żądania z portu 10000 maszyny hosta do wystąpienia platformy Docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Określ lokalizację obszaru roboczego**:

W poniższym przykładzie `-v c:/azurite:/data` parametr określa *c:/azurite* jako utrwaloną lokalizację danych azurite. Przed uruchomieniem polecenia Docker należy utworzyć katalog *c:/azurite*.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Uruchamianie tylko usługi BLOB Service**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Aby uzyskać więcej informacji o konfigurowaniu azurite podczas uruchamiania, zobacz [Opcje wiersza polecenia](#command-line-options).

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Klonowanie, kompilowanie i uruchamianie azurite z repozytorium GitHub

Ta metoda instalacji wymaga, aby program [git](https://git-scm.com/) został zainstalowany. Sklonuj [repozytorium GitHub](https://github.com/azure/azurite) dla projektu azurite za pomocą następującego polecenia konsoli.

```console
git clone https://github.com/Azure/Azurite.git
```

Po sklonowaniu kodu źródłowego wykonaj następujące polecenia z katalogu głównego sklonowanego repozytorium, aby skompilować i zainstalować azurite.

```console
npm install
npm run build
npm install -g
```

Po zainstalowaniu i zbudowaniu azurite zobacz [Uruchom azurite z wiersza polecenia](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Uruchamianie azurite z poziomu wiersza polecenia

> [!NOTE]
> Nie można uruchomić azurite z wiersza polecenia, jeśli zainstalowano tylko rozszerzenie Visual Studio Code. Zamiast tego należy użyć palety poleceń Visual Studio Code. Aby uzyskać więcej informacji, zobacz [Instalowanie i uruchamianie rozszerzenia Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Aby natychmiast rozpocząć pracę z wierszem polecenia, Utwórz katalog o nazwie *c:\azurite*, a następnie uruchom polecenie azurite przez wystawienie następującego polecenia:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

To polecenie informuje azurite o przechowywaniu wszystkich danych w określonym katalogu, *c:\azurite*. Jeśli `--location` opcja zostanie pominięta, będzie używać bieżącego katalogu roboczego.

## <a name="command-line-options"></a>Opcje wiersza polecenia

Ta sekcja zawiera szczegółowe informacje dotyczące przełączników wiersza polecenia dostępnych podczas uruchamiania azurite.

### <a name="help"></a>Pomoc

**Opcjonalne** — Uzyskiwanie pomocy wiersza polecenia za pomocą `-h` przełącznika lub `--help` .

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Host nasłuchujący obiektów BLOB

**Opcjonalna** — domyślnie azurite nasłuchuje adres 127.0.0.1 jako serwer lokalny. Użyj `--blobHost` przełącznika, aby ustawić adres na wymagania.

Akceptuj tylko żądania na komputerze lokalnym:

```console
azurite --blobHost 127.0.0.1
```

Zezwalaj na żądania zdalne:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Zezwalanie na żądania zdalne może sprawić, że system będzie narażony na ataki zewnętrzne.

### <a name="blob-listening-port-configuration"></a>Konfiguracja portu nasłuchiwania obiektów BLOB

**Opcjonalna** — domyślnie azurite nasłuchuje BLOB Service na porcie 10000. Użyj `--blobPort` przełącznika, aby określić wymagany port nasłuchujący.

> [!NOTE]
> Po użyciu dostosowanego portu należy zaktualizować parametry połączenia lub odpowiednią konfigurację w narzędziach lub zestawach SDK usługi Azure Storage.

Dostosuj port nasłuchujący Blob service:

```console
azurite --blobPort 8888
```

Pozwól, aby system automatycznie wybierał dostępny port:

```console
azurite --blobPort 0
```

Używany port jest wyświetlany podczas uruchamiania azurite.

### <a name="queue-listening-host"></a>Host nasłuchiwania kolejki

**Opcjonalna** — domyślnie azurite nasłuchuje adres 127.0.0.1 jako serwer lokalny. Użyj `--queueHost` przełącznika, aby ustawić adres na wymagania.

Akceptuj tylko żądania na komputerze lokalnym:

```console
azurite --queueHost 127.0.0.1
```

Zezwalaj na żądania zdalne:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Zezwalanie na żądania zdalne może sprawić, że system będzie narażony na ataki zewnętrzne.

### <a name="queue-listening-port-configuration"></a>Kolejka konfiguracji portu nasłuchiwania

**Opcjonalna** — domyślnie azurite nasłuchuje usługa kolejki na porcie 10001. Użyj `--queuePort` przełącznika, aby określić wymagany port nasłuchujący.

> [!NOTE]
> Po użyciu dostosowanego portu należy zaktualizować parametry połączenia lub odpowiednią konfigurację w narzędziach lub zestawach SDK usługi Azure Storage.

Dostosuj port nasłuchujący usługa kolejki:

```console
azurite --queuePort 8888
```

Pozwól, aby system automatycznie wybierał dostępny port:

```console
azurite --queuePort 0
```

Używany port jest wyświetlany podczas uruchamiania azurite.

### <a name="workspace-path"></a>Ścieżka obszaru roboczego

**Opcjonalne** -azurite przechowuje dane na dysku lokalnym podczas wykonywania. Użyj `-l` przełącznika lub, `--location` Aby określić ścieżkę jako lokalizację obszaru roboczego. Domyślnie zostanie użyty katalog roboczy bieżącego procesu. Zwróć uwagę na małe litery "l".

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Dziennik dostępu

**Opcjonalna** — domyślnie Dziennik dostępu jest wyświetlany w oknie konsoli. Wyłącz wyświetlanie dziennika dostępu przy użyciu `-s` `--silent` przełącznika lub.

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Dziennik debugowania

**Opcjonalne** — Dziennik debugowania zawiera szczegółowe informacje dotyczące wszystkich żądań i śladów stosu wyjątków. Włącz Dziennik debugowania, podając prawidłową ścieżkę do pliku lokalnego `-d` lub `--debug` przełącznika.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Tryb luźny

**Opcjonalna** — domyślnie azurite stosuje tryb ścisły do blokowania nieobsługiwanych nagłówków i parametrów żądania. Wyłącz tryb Strict przy użyciu `-L` przełącznika lub `--loose` . Zanotuj wielką literę "L".

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Wersja

**Opcjonalne** — wyświetla zainstalowany numer wersji azurite przy użyciu `-v` `--version` przełącznika lub.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Konfiguracja certyfikatu (HTTPS)

**Opcjonalna** — domyślnie azurite używa protokołu HTTP. Włącz tryb HTTPS, podając ścieżkę do pliku certyfikatu Privacy Enhanced Mail (PEM) lub [wymiany informacji osobistych (pfx)](https://docs.microsoft.com/windows-hardware/drivers/install/personal-information-exchange---pfx--files) do `--cert` przełącznika.

Gdy `--cert` jest dostarczany dla pliku PEM, należy podać odpowiedni `--key` przełącznik.

```console
azurite --cert path/server.pem --key path/key.pem
```

Gdy `--cert` jest dostarczany dla pliku PFX, należy podać odpowiedni `--pwd` przełącznik.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Aby uzyskać szczegółowe informacje na temat tworzenia plików PEM i PFX, zobacz [Konfiguracja protokołu HTTPS](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>Konfiguracja protokołu OAuth

**Opcjonalne** — Włącz uwierzytelnianie OAuth dla azurite za pomocą `--oauth` przełącznika.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> Uwierzytelnianie OAuth wymaga punktu końcowego HTTPS. Upewnij się, że protokół HTTPS jest włączony, dostarczając `--cert` przełącznik wraz z `--oauth` przełącznikiem.

Azurite obsługuje uwierzytelnianie podstawowe przez określenie `basic` parametru `--oauth` przełącznika. Azurite będzie wykonywać uwierzytelnianie podstawowe, takie jak Weryfikowanie przychodzącego tokenu okaziciela, sprawdzanie wystawcy, odbiorców i wygasanie. Azurite nie sprawdza podpisu ani uprawnień tokenu.

### <a name="skip-api-version-check"></a>Pomiń sprawdzanie wersji interfejsu API

**Opcjonalne** — podczas uruchamiania, azurite sprawdza, czy żądana wersja interfejsu API jest prawidłowa. Następujące polecenie pomija Sprawdzanie wersji interfejsu API:

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>Autoryzacja narzędzi i zestawów SDK

Łączenie się z usługą azurite z zestawów SDK lub narzędzi usługi Azure Storage, takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), przy użyciu dowolnej strategii uwierzytelniania. Wymagane jest uwierzytelnianie. Azurite obsługuje autoryzację z uwierzytelnianiem OAuth, kluczem współdzielonym i sygnaturami dostępu współdzielonego (SAS). Azurite obsługuje również dostęp anonimowy do kontenerów publicznych.

Jeśli używasz zestawów SDK platformy Azure, zacznij azurite z `--oauth basic and --cert --key/--pwd` opcjami.

### <a name="well-known-storage-account-and-key"></a>Dobrze znane konto magazynu i klucz

Azurite akceptuje to samo dobrze znane konto i klucz używany przez starszy emulator usługi Azure Storage.

- Nazwa konta: `devstoreaccount1`
- Klucz konta: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Niestandardowe konta magazynu i klucze

Azurite obsługuje niestandardowe nazwy kont magazynu i klucze przez ustawienie `AZURITE_ACCOUNTS` zmiennej środowiskowej w następującym formacie: `account1:key1[:key2];account2:key1[:key2];...` .

Na przykład Użyj niestandardowego konta magazynu, które ma jeden klucz:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Lub Użyj wielu kont magazynu z dwoma kluczami każdy:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite odświeża niestandardowe nazwy kont i klucze ze zmiennej środowiskowej co minutę domyślnie. Za pomocą tej funkcji można dynamicznie obrócić klucz konta lub dodać nowe konta magazynu bez ponownego uruchamiania azurite.

> [!NOTE]
> Domyślne `devstoreaccount1` konto magazynu jest wyłączone podczas ustawiania niestandardowych kont magazynu.

### <a name="connection-strings"></a>Parametry połączeń

Najprostszym sposobem nawiązywania połączenia z usługą azurite z poziomu aplikacji jest skonfigurowanie parametrów połączenia w pliku konfiguracji aplikacji, który odwołuje się do skrótu *UseDevelopmentStorage = true*. Oto przykład parametrów połączenia w pliku *app.config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>Parametry połączenia HTTP

Można przekazać następujące parametry połączenia do [zestawów SDK lub narzędzi platformy Azure](https://aka.ms/azsdk) , takich jak interfejs wiersza polecenia platformy Azure 2,0 lub Eksplorator usługi Storage.

Pełne parametry połączenia:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Aby nawiązać połączenie z usługą BLOB tylko, parametry połączenia:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Aby nawiązać połączenie tylko z usługą kolejki, parametry połączenia są następujące:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>Parametry połączenia HTTPS

Pełne parametry połączenia HTTPS:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Aby używać tylko usługi BLOB, parametry połączenia HTTPS:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Aby używać tylko usługi kolejki, parametry połączenia HTTPS:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Jeśli użyto `dotnet dev-certs` do wygenerowania certyfikatu z podpisem własnym, użyj następujących parametrów połączenia.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Zaktualizuj parametry połączenia w przypadku korzystania z [niestandardowych kont magazynu i kluczy](#custom-storage-accounts-and-keys).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>Zestawy Azure SDK

Aby używać azurite z zestawami [SDK platformy Azure](https://aka.ms/azsdk), użyj opcji OAuth i https:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Następnie można utworzyć wystąpienie elementu BlobContainerClient, BlobServiceClient lub BlobClient.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Usługa Azure Queue Storage

Można również utworzyć wystąpienie QueueClient lub QueueServiceClient.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Za pomocą Eksplorator usługi Storage można wyświetlić dane przechowywane w azurite.

#### <a name="connect-to-azurite-using-http"></a>Nawiązywanie połączenia z usługą azurite przy użyciu protokołu HTTP

W Eksplorator usługi Storage Połącz się z usługą azurite, wykonując następujące czynności:

 1. Wybierz ikonę **Zarządzaj kontami**
 1. Wybierz pozycję **Dodaj konto**
 1. Wybierz opcję **Dołącz do lokalnego emulatora**
 1. Wybierz pozycję **Dalej**
 1. Edytuj pole **nazwy wyświetlanej** pod wybraną nazwą
 1. Ponownie wybierz pozycję **dalej**
 1. Wybierz pozycję **Połącz**

#### <a name="connect-to-azurite-using-https"></a>Nawiązywanie połączenia z usługą azurite przy użyciu protokołu HTTPS

Domyślnie Eksplorator usługi Storage nie otworzy punktu końcowego HTTPS, który używa certyfikatu z podpisem własnym. Jeśli korzystasz z programu azurite z protokołem HTTPS, możesz użyć certyfikatu z podpisem własnym. W Eksplorator usługi Storage zaimportuj certyfikaty SSL za pomocą okna dialogowego **Edytowanie**certyfikatów  ->  **SSL**  ->  **Importuj certyfikaty** .

##### <a name="import-certificate-to-storage-explorer"></a>Importuj certyfikat do Eksplorator usługi Storage

1. Znajdź certyfikat na komputerze lokalnym.
1. W Eksplorator usługi Storage przejdź do pozycji **Edycja**  ->  **certyfikatów SSL**  ->  **Importuj certyfikaty** i zaimportuj certyfikat.

Jeśli certyfikat nie zostanie zaimportowany, zostanie wyświetlony komunikat o błędzie:

`unable to verify the first certificate` lub `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Dodawanie azurite za pomocą parametrów połączenia HTTPS

Wykonaj następujące kroki, aby dodać azurite HTTPS do Eksplorator usługi Storage:

1. Wybierz pozycję **Przełącz Eksploratora**
1. Wybierz **załączony & lokalny**
1. Kliknij prawym przyciskiem myszy pozycję **konta magazynu** i wybierz pozycję **Połącz z usługą Azure Storage**.
1. Wybierz opcję **Użyj parametrów połączenia**
1. Wybierz opcję **Dalej**.
1. Wprowadź wartość w polu **Nazwa wyświetlana** .
1. Wprowadź [Parametry połączenia HTTPS](#https-connection-strings) z poprzedniej sekcji tego dokumentu
1. Wybierz pozycję **Dalej**
1. Wybierz pozycję **Połącz**

## <a name="workspace-structure"></a>Struktura obszaru roboczego

Podczas inicjowania azurite można utworzyć następujące pliki i foldery w lokalizacji obszaru roboczego.

- `__blobstorage__` -Katalog zawierający utrwalone dane binarne usługi azurite BLOB
- `__queuestorage__` -Katalog zawierający utrwalone dane binarne usługi azurite Queue
- `__azurite_db_blob__.json` — Plik metadanych usługi BLOB azurite
- `__azurite_db_blob_extent__.json` -Plik metadanych zakresu usługi azurite BLOB
- `__azurite_db_queue__.json` — Plik metadanych usługi kolejki azurite
- `__azurite_db_queue_extent__.json` -Plik metadanych zakresu usługi azurite Queue

Aby oczyścić azurite, Usuń powyższe pliki i foldery, a następnie uruchom ponownie emulator.

## <a name="differences-between-azurite-and-azure-storage"></a>Różnice między azurite i usługą Azure Storage

Istnieją różnice funkcjonalne między lokalnym wystąpieniem azurite i kontem usługi Azure Storage w chmurze.

### <a name="endpoint-and-connection-url"></a>Punkt końcowy i adres URL połączenia

Punkty końcowe usługi azurite różnią się od punktów końcowych konta usługi Azure Storage. Komputer lokalny nie obsługuje rozpoznawania nazw domen, wymagając punktów końcowych azurite jako adresów lokalnych.

Gdy adresowanie zasobu odbywa się na koncie usługi Azure Storage, nazwa konta jest częścią nazwy hosta identyfikatora URI. Zasób, który jest adresowany, jest częścią ścieżki URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Następujący identyfikator URI jest prawidłowym adresem obiektu BLOB na koncie usługi Azure Storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Ponieważ komputer lokalny nie wykonuje rozpoznawania nazw domen, nazwa konta jest częścią ścieżki URI zamiast nazwy hosta. Użyj następującego formatu identyfikatora URI dla zasobu w azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Następujący adres może służyć do uzyskiwania dostępu do obiektu BLOB w azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skalowanie i wydajność

Azurite nie obsługuje dużej liczby połączonych klientów. Nie ma gwarancji wydajności. Azurite jest przeznaczony do celów deweloperskich i testowych.

### <a name="error-handling"></a>Obsługa błędów

Azurite jest wyrównany do logiki obsługi błędów usługi Azure Storage, ale istnieją różnice. Na przykład komunikaty o błędach mogą się różnić, podczas gdy kody stanu błędu są wyrównane.

### <a name="ra-grs"></a>RA-GRS

Azurite obsługuje replikację Geograficznie nadmiarowy do odczytu (RA-GRS). W przypadku zasobów magazynu uzyskaj dostęp do lokalizacji dodatkowej, dołączając `-secondary` do nazwy konta. Na przykład następujący adres może być używany do uzyskiwania dostępu do obiektu BLOB za pomocą pomocniczego tylko do odczytu w azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Obsługa tabel

Wsparcie dla tabel w azurite jest obecnie opracowywane i otwarte na potrzeby udziału! Aby uzyskać najnowszy postęp, sprawdź projekt [tabeli azurite v3](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) .

Obsługa trwałych funkcji wymaga tabel.

## <a name="azurite-is-open-source"></a>Azurite jest otwartym źródłem

Zamieszczamy wkłady i sugestie dotyczące azurite. Przejdź na stronę [projektu GitHub](https://github.com/Azure/Azurite/projects) azurite lub problemy z usługą [GitHub](https://github.com/Azure/Azurite/issues) dla punktów kontrolnych i elementów roboczych, które są śledzone pod kątem nadchodzących funkcji i poprawek błędów. Szczegółowe elementy robocze są również śledzone w usłudze GitHub.

## <a name="next-steps"></a>Następne kroki

- [Korzystanie z emulatora usługi Azure Storage na potrzeby tworzenia i testowania](storage-use-emulator.md) dokumentów w starszym emulatorze usługi Azure Storage, który jest zastępowany przez azurite.
- [Konfigurowanie parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md) wyjaśnia, jak utworzyć prawidłowe parametry połączenia usługi Azure Storage.
