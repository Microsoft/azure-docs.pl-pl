---
title: Korzystanie z emulatora azurite na potrzeby tworzenia lokalnych magazynów platformy Azure
description: Emulator typu open source azurite (wersja zapoznawcza) udostępnia bezpłatne środowisko lokalne do testowania aplikacji usługi Azure Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76029927"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Korzystanie z emulatora azurite na potrzeby programowania i testowania lokalnego magazynu platformy Azure (wersja zapoznawcza)

Azurite w wersji 3,2 Emulator typu Open Source (wersja zapoznawcza) zawiera bezpłatne środowisko lokalne do testowania aplikacji magazynu obiektów blob platformy Azure i kolejek. Gdy aplikacja działa lokalnie, przejdź do korzystania z konta usługi Azure Storage w chmurze. Emulator zapewnia obsługę wielu platform w systemach Windows, Linux i MacOS. Azurite v3 obsługuje interfejsy API zaimplementowane przez Blob service platformy Azure.

Azurite to przyszła platforma emulatora magazynu. Azurite zastępuje [emulator usługi Azure Storage](storage-use-emulator.md). Azurite będzie nadal aktualizowana w celu obsługi najnowszych wersji interfejsów API usługi Azure Storage.

Istnieje kilka różnych sposobów instalowania i uruchamiania programu azurite w systemie lokalnym:

  1. [Instalowanie i uruchamianie rozszerzenia azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalowanie i uruchamianie azurite za pomocą NPM](#install-and-run-azurite-by-using-npm)
  1. [Instalowanie i uruchamianie obrazu platformy Docker azurite](#install-and-run-the-azurite-docker-image)
  1. [Klonowanie, kompilowanie i uruchamianie azurite z repozytorium GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalowanie i uruchamianie rozszerzenia azurite Visual Studio Code

W obszarze Visual Studio Code Wybierz okienko **rozszerzenia** i wyszukaj ciąg *azurite* w **witrynie rozszerzenia: Marketplace**.

![Portal Visual Studio Code rozszerzenia Marketplace](media/storage-use-azurite/azurite-vs-code-extension.png)

Alternatywnie przejdź do [vs Code rynku rozszerzenia](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) w przeglądarce. Wybierz przycisk **Zainstaluj** , aby otworzyć Visual Studio Code i przejdź bezpośrednio do strony rozszerzenia azurite.

Aby szybko uruchomić lub zamknąć azurite, kliknij pozycję **[azurite BLOB Service]** lub **[azurite Queue Service]** na pasku stanu vs Code lub wydawanie następujących poleceń w palecie poleceń vs Code. Aby otworzyć paletę poleceń, naciśnij klawisz **F1** w vs Code.

Rozszerzenie obsługuje następujące polecenia Visual Studio Code:

   * **Azurite: Uruchom** i uruchom wszystkie usługi azurite
   * **Azurite: Zamknij** i Zamknij wszystkie usługi azurite
   * **Azurite: Wyczyść** /Zresetuj wszystkie dane utrwalenie usług azurite Services
   * **Azurite: uruchamianie usługi BLOB Service** — uruchamianie usługi BLOB Service
   * **Azurite: Zamknij usługę BLOB Service** — zamknij usługę BLOB Service
   * **Azurite: czyszczenie usługi BLOB Service** — Clean BLOB Service
   * **Azurite: Uruchom usługę kolejki** — Uruchom usługę kolejki
   * **Azurite: Zamknij usługę kolejki** — zamknij usługę kolejki
   * **Azurite: czyszczenie kolejki** — usługa kolejki czystej

Aby skonfigurować azurite w Visual Studio Code, zaznacz okienko rozszerzenia. Wybierz ikonę **zarządzania** (koła zębatego) dla **azurite**. Wybierz pozycję **Konfiguruj ustawienia rozszerzenia**.

![Azurite Skonfiguruj ustawienia rozszerzenia](media/storage-use-azurite/azurite-configure-extension-settings.png)

Obsługiwane są następujące ustawienia:

   * **Azurite: Host obiektów BLOB** — punkt końcowy nasłuchiwania BLOB Service. Ustawieniem domyślnym jest 127.0.0.1.
   * **Azurite: Port obiektu BLOB** — port nasłuchujący BLOB Service. Domyślnym portem jest 10000.
   * **Azurite: Debuguj** — wyprowadza Dziennik debugowania do kanału azurite. Wartość domyślna to **fałsz**.
   * **Azurite: Location** — ścieżka lokalizacji obszaru roboczego. Wartość domyślna to Visual Studio Code folder roboczy.
   * **Azurite: Host kolejki** — punkt końcowy nasłuchiwania usługa kolejki. Ustawieniem domyślnym jest 127.0.0.1.
   * **Azurite: Port kolejki** — port nasłuchujący usługa kolejki. Domyślnym portem jest 10001.
   * **Azurite: tryb dyskretny** powoduje wyłączenie dziennika dostępu. Wartość domyślna to **fałsz**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instalowanie i uruchamianie azurite za pomocą NPM

Ta metoda instalacji wymaga zainstalowanego programu [Node. js w wersji 8,0 lub nowszej](https://nodejs.org) . **npm** to narzędzie do zarządzania pakietami dołączone do każdej instalacji środowiska Node. js. Po zainstalowaniu środowiska Node. js wykonaj następujące polecenie **npm** , aby zainstalować azurite.

```console
npm install -g azurite
```

Po zainstalowaniu azurite zobacz [Uruchom azurite z wiersza polecenia](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instalowanie i uruchamianie obrazu platformy Docker azurite

Użyj [DockerHub](https://hub.docker.com/) , aby ściągnąć [najnowszy obraz azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) przy użyciu następującego polecenia:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Uruchom obraz platformy Docker azurite**:

Następujące polecenie uruchamia obraz platformy Docker azurite. `-p 10000:10000` Parametr przekierowuje żądania z portu 10000 maszyny hosta do wystąpienia platformy Docker.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Określ lokalizację obszaru roboczego**:

W poniższym przykładzie `-v c:/azurite:/data` parametr określa *c:/azurite* jako utrwaloną lokalizację danych azurite. Przed uruchomieniem polecenia Docker należy utworzyć katalog *c:/azurite*.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Uruchamianie tylko usługi BLOB Service**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Ustaw wszystkie parametry azurite**:

Ten przykład pokazuje, jak ustawić wszystkie parametry wiersza polecenia. Wszystkie parametry poniżej powinny być umieszczone w jednym wierszu polecenia.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Zobacz [Opcje wiersza polecenia](#command-line-options) , aby uzyskać więcej informacji o konfigurowaniu azurite podczas uruchamiania.

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

Po zainstalowaniu i utworzeniu azurite, zobacz [Uruchamianie azurite z wiersza polecenia](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Uruchamianie azurite z poziomu wiersza polecenia

> [!NOTE]
> Nie można uruchomić azurite z wiersza polecenia, jeśli zainstalowano tylko rozszerzenie Visual Studio Code. Zamiast tego należy użyć palety poleceń VS Code. Aby uzyskać więcej informacji, zobacz [Instalowanie i uruchamianie rozszerzenia Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Aby rozpocząć od razu rozpocząć pracę z wierszem polecenia, Utwórz katalog o nazwie **c:\azurite**, a następnie uruchom polecenie azurite, wykonując następujące polecenia:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

To polecenie informuje azurite o przechowywaniu wszystkich danych w określonym katalogu, **c:\azurite**. Jeśli opcja **--Location** zostanie pominięta, będzie korzystać z bieżącego katalogu roboczego.

## <a name="command-line-options"></a>Opcje wiersza polecenia

Ta sekcja zawiera szczegółowe informacje na temat przełączników wiersza polecenia dostępnych podczas uruchamiania azurite. Wszystkie przełączniki wiersza polecenia są opcjonalne.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-D** to skrót do **--Debug**, **-l** Switch jest skrótem do **--Location**, **-s** jest skrótem **--silentnym**i **-h** to skrót dla **--pomocy**.

### <a name="blob-listening-host"></a>Host nasłuchujący obiektów BLOB

**Opcjonalne** Domyślnie azurite będzie nasłuchiwał adres 127.0.0.1 jako serwer lokalny. Aby ustawić adres na wymagania, użyj przełącznika **--blobHost** .

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

**Opcjonalne** Domyślnie azurite nasłuchuje Blob service na porcie 10000. Użyj przełącznika **--blobPort** , aby określić wymagany port nasłuchujący.

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

**Opcjonalne** Domyślnie azurite będzie nasłuchiwał adres 127.0.0.1 jako serwer lokalny. Aby ustawić adres na wymagania, użyj przełącznika **--queueHost** .

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

**Opcjonalne** Domyślnie azurite nasłuchuje usługa kolejki na porcie 10001. Użyj przełącznika **--queuePort** , aby określić wymagany port nasłuchujący.

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

**Opcjonalne** Azurite przechowuje dane na dysku lokalnym podczas wykonywania. Użyj przełącznika **--Location** , aby określić ścieżkę jako lokalizację obszaru roboczego. Domyślnie zostanie użyty katalog roboczy bieżącego procesu.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Dziennik dostępu

**Opcjonalne** Domyślnie dziennik dostępu jest wyświetlany w oknie konsoli. Wyłącz wyświetlanie dziennika dostępu przy użyciu przełącznika **--Silent** .

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Dziennik debugowania

**Opcjonalne** Dziennik debugowania zawiera szczegółowe informacje dotyczące każdego żądania i śladu stosu wyjątków. Włącz Dziennik debugowania, podając prawidłową ścieżkę do przełącznika **--Debug** .

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Tryb luźny

**Opcjonalne** Domyślnie azurite stosuje tryb ścisły do blokowania nieobsługiwanych nagłówków i parametrów żądania. Wyłącz tryb Strict przy użyciu **--luźnego** przełącznika.

```console
azurite --loose
```

Zanotuj wielkie litery "L":

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Autoryzacja narzędzi i zestawów SDK

Łączenie się z usługą azurite z zestawów SDK lub narzędzi usługi Azure Storage, takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), przy użyciu dowolnej strategii uwierzytelniania. Wymagane jest uwierzytelnianie. Azurite obsługuje autoryzację za pomocą klucza współużytkowanego i sygnatur dostępu współdzielonego (SAS). Azurite obsługuje również dostęp anonimowy do kontenerów publicznych.

### <a name="well-known-storage-account-and-key"></a>Dobrze znane konto magazynu i klucz

Możesz użyć następującej nazwy konta i klucza z azurite. Jest to to samo dobrze znane konto i klucz używany przez starszy emulator usługi Azure Storage.

* Nazwa konta:`devstoreaccount1`
* Klucz konta:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Oprócz uwierzytelniania SharedKey azurite obsługuje uwierzytelnianie SAS kont i usług. Dostęp anonimowy jest również dostępny, gdy kontener jest ustawiony tak, aby zezwalać na dostęp publiczny.

### <a name="connection-string"></a>Parametry połączenia

Najprostszym sposobem nawiązywania połączenia z usługą azurite z poziomu aplikacji jest skonfigurowanie parametrów połączenia w pliku konfiguracji aplikacji, który odwołuje się do skrótu *UseDevelopmentStorage = true*. Oto przykład parametrów połączenia w pliku *App. config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Aby uzyskać więcej informacji, zobacz [Konfigurowanie parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md).

### <a name="custom-storage-accounts-and-keys"></a>Niestandardowe konta magazynu i klucze

Azurite obsługuje niestandardowe nazwy kont magazynu i klucze przez ustawienie zmiennej `AZURITE_ACCOUNTS` środowiskowej w następującym formacie: `account1:key1[:key2];account2:key1[:key2];...`.

Na przykład Użyj niestandardowego konta magazynu, które ma jeden klucz:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Lub Użyj wielu kont magazynu z 2 kluczami każdy:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite odświeża niestandardowe nazwy kont i klucze ze zmiennej środowiskowej co minutę domyślnie. Za pomocą tej funkcji można dynamicznie obrócić klucz konta lub dodać nowe konta magazynu bez ponownego uruchamiania azurite.

> [!NOTE]
> Domyślne `devstoreaccount1` konto magazynu jest wyłączone podczas ustawiania niestandardowych kont magazynu.

> [!NOTE]
> Należy odpowiednio zaktualizować parametry połączenia w przypadku korzystania z niestandardowych nazw kont i kluczy.

> [!NOTE]
> Użyj `export` słowa kluczowego, aby ustawić zmienne środowiskowe w środowisku systemu `set` Linux, użyj w systemie Windows.

### <a name="storage-explorer"></a>Storage Explorer

W Eksplorator usługi Azure Storage Połącz się z usługą azurite, klikając ikonę **Dodaj konto** , a następnie wybierz pozycję **Dołącz do lokalnego emulatora** , a następnie kliknij przycisk **Połącz**.

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

Azurite nie jest skalowalną usługą magazynu i nie obsługuje dużej liczby równoczesnych klientów. Nie ma gwarancji wydajności. Azurite jest przeznaczony do celów deweloperskich i testowych.

### <a name="error-handling"></a>Obsługa błędów

Azurite jest wyrównany do logiki obsługi błędów usługi Azure Storage, ale istnieją różnice. Na przykład komunikaty o błędach mogą się różnić, podczas gdy kody stanu błędu są wyrównane.

### <a name="ra-grs"></a>RA-GRS

Azurite obsługuje replikację Geograficznie nadmiarowy do odczytu (RA-GRS). W przypadku zasobów magazynu uzyskaj dostęp do lokalizacji dodatkowej przez dołączenie **-pomocnicze** do nazwy konta. Na przykład następujący adres może być używany do uzyskiwania dostępu do obiektu BLOB za pomocą pomocniczego tylko do odczytu w azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite jest otwartym źródłem

Zamieszczamy wkłady i sugestie dotyczące azurite. Przejdź na stronę [projektu GitHub](https://github.com/Azure/Azurite/projects) azurite lub problemy z usługą [GitHub](https://github.com/Azure/Azurite/issues) dla punktów kontrolnych i elementów roboczych, które są śledzone pod kątem nadchodzących funkcji i poprawek błędów. Szczegółowe elementy robocze są również śledzone w usłudze GitHub.

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z emulatora usługi Azure Storage na potrzeby tworzenia i testowania](storage-use-emulator.md) dokumentów w starszym emulatorze usługi Azure Storage, który jest zastępowany przez azurite.
* [Konfigurowanie parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md) wyjaśnia, jak utworzyć prawidłowe parametry połączenia usługi Azure Storage.
