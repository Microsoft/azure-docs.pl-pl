---
title: azcopy copy| Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c1e265f473c1c6fb70fd97416722e7b863c429b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503562"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopiuje dane źródłowe do lokalizacji docelowej.

## <a name="synopsis"></a>Streszczenie

Kopiuje dane źródłowe do lokalizacji docelowej. Obsługiwane są:

  - local <-> Azure Blob (uwierzytelnianie SAS lub OAuth)
  - local <-> Azure Files (Uwierzytelnianie sygnatury dostępu współdzielonego udziału/katalogu)
  - uwierzytelnianie <-> Azure Data Lake Storage 2. generacji (uwierzytelnianie SAS, OAuth lub klucz współużytony)
  - Obiekt blob platformy Azure (sas lub publiczny) — > Azure Blob (uwierzytelnianie SAS lub OAuth)
  - Obiekt blob platformy Azure (sas lub publiczny) — > Azure Files (SAS)
  - Azure Files (SAS) — > Azure Files (SAS)
  - Azure Files (SAS) — > azure blob (uwierzytelnianie SAS lub OAuth)
  - Amazon Web Services (AWS) S3 (klucz dostępu) — > blokowego obiektu blob platformy Azure (uwierzytelnianie SAS lub OAuth)
  - Google Cloud Storage (klucz konta usługi) — > blokowych obiektów blob platformy Azure (uwierzytelnianie SAS lub OAuth) [wersja zapoznawcza]

Aby uzyskać więcej informacji, zobacz sekcję przykładów w tym artykule.

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Samouczek: migrowanie danych lokalnych do magazynu w chmurze za pomocą programu AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="advanced"></a>Zaawansowany

Narzędzie AzCopy automatycznie wykrywa typ zawartości plików podczas przekazywania ich z dysku lokalnego. Program AzCopy wykrywa typ zawartości na podstawie rozszerzenia pliku lub zawartości (jeśli nie określono rozszerzenia).

Wbudowana tabela odnośników jest mała, ale w systemie Unix jest rozszerzana przez pliki systemu lokalnego, jeśli są one dostępne pod co najmniej jedną z `mime.types` tych nazw:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębnione z rejestru. Tę funkcję można wyłączyć za pomocą flagi . Aby uzyskać więcej informacji, zobacz sekcję flag w tym artykule.

Jeśli ustawisz zmienną środowiskową przy użyciu wiersza polecenia, ta zmienna będzie można odczytać w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych zawierających poświadczenia z historii wiersza polecenia. Aby nie wyświetlać zmiennych w historii, możesz użyć skryptu, aby monitować użytkownika o poświadczenia i ustawić zmienną środowiskową.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Przykłady

Przekaż pojedynczy plik przy użyciu uwierzytelniania OAuth. Jeśli jeszcze nie zalogowano się do programu AzCopy, uruchom `azcopy login` polecenie przed uruchomieniem następującego polecenia.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Tak jak powyżej, ale tym razem również oblicz skrót MD5 zawartości pliku i zapisz go jako właściwość Content-MD5 obiektu blob:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Przekaż pojedynczy plik przy użyciu tokenu SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Przekaż pojedynczy plik przy użyciu tokenu SAS i przesyłania pipingu (tylko blokowe obiekty blob):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Przekaż cały katalog przy użyciu tokenu SAS:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

lub

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Przekaż zestaw plików przy użyciu tokenu SAS i symboli wieloznacznych (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Przekazywanie plików i katalogów przy użyciu tokenu SAS i symboli wieloznacznych (*) :

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Przekaż pliki i katalogi na konto usługi Azure Storage i ustaw tagi zakodowane w ciągu zapytania w obiekcie blob. 

- Aby ustawić tagi {key = "bla", val = "foo"} i {key = "bla bla 2", val = "bar"}, użyj następującej składni: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Klucze i wartości są zakodowane w adresie URL, a pary klucz-wartość są oddzielone przecinkami ampersand('&')

- Podczas ustawiania tagów w obiektach blob istnieją dodatkowe uprawnienia (nie dla tagów) w sygnaturze dostępu współdzielonego, bez których usługa zwraca błąd autoryzacji.

Pobierz pojedynczy plik przy użyciu uwierzytelniania OAuth. Jeśli jeszcze nie zalogowano się do programu AzCopy, uruchom `azcopy login` polecenie przed uruchomieniem następującego polecenia.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Pobierz pojedynczy plik przy użyciu tokenu SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Pobierz pojedynczy plik przy użyciu tokenu SAS, a następnie przekieruj dane wyjściowe do pliku (tylko blokowe obiekty blob):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Pobierz cały katalog przy użyciu tokenu SAS:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Uwaga na temat używania symbolu wieloznacznego (*) w adresach URL:

Istnieją tylko dwa obsługiwane sposoby używania symbolu wieloznacznego w adresie URL. 

- Możesz użyć jednego z nich tuż po końcowym ukośniku (/) adresu URL. To użycie symbolu wieloznacznego kopiuje wszystkie pliki w katalogu bezpośrednio do miejsca docelowego bez umieszczania ich w podkatalogu. 

- Można również użyć symbolu wieloznacznego w nazwie kontenera, o ile adres URL odwołuje się tylko do kontenera, a nie do obiektu blob. Tej metody można użyć do uzyskania plików z podzbioru kontenerów. 

Pobierz zawartość katalogu bez kopiowania samego katalogu zawierającego.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Pobierz całe konto magazynu.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Pobierz podzbiór kontenerów w ramach konta magazynu przy użyciu symbolu wieloznacznego (*) w nazwie kontenera.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Kopiowanie pojedynczego obiektu blob do innego obiektu blob przy użyciu tokenu SAS.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Skopiuj pojedynczy obiekt blob do innego obiektu blob przy użyciu tokenu SAS i tokenu uwierzytelniania. Musisz użyć tokenu SAS na końcu adresu URL konta źródłowego, ale konto docelowe nie będzie potrzebne, jeśli zalogujesz się do narzędzia AzCopy za pomocą `azcopy login` polecenia . 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Skopiuj jeden katalog wirtualny obiektu blob do innego przy użyciu tokenu SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Skopiuj wszystkie kontenery obiektów blob, katalogi i obiekty blob z konta magazynu do innego przy użyciu tokenu SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Skopiuj pojedynczy obiekt do usługi Blob Storage z usługi Amazon Web Services (AWS) S3 przy użyciu klucza dostępu i tokenu SAS. Najpierw ustaw zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Skopiuj cały katalog do usługi Blob Storage Z usługi AWS S3 przy użyciu klucza dostępu i tokenu SAS. Najpierw ustaw zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Zapoznaj się z https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html tematem , aby lepiej zrozumieć symbol zastępczy [folder].

Skopiuj wszystkie zasobniki do Blob Storage z Amazon Web Services (AWS) przy użyciu klucza dostępu i tokenu SAS. Najpierw ustaw zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Skopiuj wszystkie zasobniki do Blob Storage z regionu Amazon Web Services (AWS) przy użyciu klucza dostępu i tokenu SAS. Najpierw ustaw zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Skopiuj podzbiór zasobników przy użyciu symbolu wieloznacznego (*) w nazwie zasobnika. Podobnie jak w poprzednich przykładach, będziesz potrzebować klucza dostępu i tokenu sygnatury dostępu współdzielonego. Upewnij się, że ustawiono zmienną `AWS_ACCESS_KEY_ID` środowiskową i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Przenieś pliki i katalogi na konto usługi Azure Storage i ustaw dla obiektu blob tagi zakodowane w ciągu zapytania. 

- Aby ustawić tagi {key = "bla", val = "foo"} i {key = "bla bla 2", val = "bar"}, użyj następującej składni: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Klucze i wartości są zakodowane w adresie URL, a pary klucz-wartość są oddzielone przecinkami ampersand('&')
    
- Podczas ustawiania tagów obiektów blob istnieją dodatkowe uprawnienia ('t' dla tagów) w sygnaturze dostępu współdzielonego, bez których usługa zwraca błąd autoryzacji.

Skopiuj pojedynczy obiekt do usługi Blob Storage z usługi Google Cloud Storage przy użyciu klucza konta usługi i tokenu SAS. Najpierw ustaw zmienną środowiskową GOOGLE_APPLICATION_CREDENTIALS źródła usługi Google Cloud Storage.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Skopiuj cały katalog do usługi Blob Storage z usługi Google Cloud Storage przy użyciu klucza konta usługi i tokenu SAS. Najpierw ustaw zmienną środowiskową dla źródła GOOGLE_APPLICATION_CREDENTIALS Google Cloud Storage.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Skopiuj cały zasobnik, aby Blob Storage z usługi Google Cloud Storage przy użyciu klucza konta usługi i tokenu SAS. Najpierw ustaw zmienną środowiskową dla źródła GOOGLE_APPLICATION_CREDENTIALS Google Cloud Storage.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Skopiuj wszystkie zasobniki do usługi Blob Storage z usługi Google Cloud Storage przy użyciu klucza konta usługi i tokenu SAS. Najpierw należy ustawić zmienne środowiskowe GOOGLE_APPLICATION_CREDENTIALS i GOOGLE_CLOUD_PROJECT =<identyfikator-projektu> dla źródła GCS

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Skopiuj podzbiór zasobników przy użyciu symbolu wieloznacznego (*) w nazwie zasobnika z usługi Google Cloud Storage przy użyciu klucza konta usługi i tokenu SAS dla miejsca docelowego. Najpierw należy ustawić zmienne środowiskowe GOOGLE_APPLICATION_CREDENTIALS i GOOGLE_CLOUD_PROJECT =<identyfikator projektu> źródła usługi Google Cloud Storage.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Opcje

**— kopia zapasowa** Aktywuje sebackupPrivilege systemu Windows dla przekazywania lub SeRestorePrivilege do pobrania, aby umożliwić programowi AzCopy wyświetlanie i odczytywanie wszystkich plików, niezależnie od ich uprawnień systemu plików, oraz przywracanie wszystkich uprawnień. Wymaga, aby konto z uruchomionym programem AzCopy ma już te uprawnienia (na przykład ma uprawnienia administratora lub jest członkiem `Backup Operators` grupy). Ta flaga aktywuje uprawnienia, które konto już ma.

**--blob-tags ciąg** Ustaw tagi na obiektach blob w celu kategoryzowania danych na koncie magazynu.

**--blob-type** string Definiuje typ obiektu blob w miejscu docelowym. Służy do przekazywania obiektów blob i kopiowania między kontami (wartość `Detect` domyślna). Prawidłowe wartości to `Detect` , `BlockBlob` , i `PageBlob` `AppendBlob` . Podczas kopiowania między kontami wartość powoduje, że program AzCopy używa typu źródłowego obiektu blob do określenia `Detect` typu docelowego obiektu blob. Podczas przekazywania pliku program określa, czy plik jest plikiem `Detect` VHD, czy VHDX, na podstawie rozszerzenia pliku. Jeśli plik jest plikiem VHD lub VHDX, program AzCopy traktuje ten plik jako stronicowy obiekt blob. (domyślnie "Wykryj")

**Ciąg --block-blob-tier** Przekaż blokowy obiekt blob do usługi Azure Storage przy użyciu tej warstwy obiektów blob. (domyślnie "Brak")

**--block-size-mb** float Użyj tego rozmiaru bloku (określonego w mib) podczas przekazywania do usługi Azure Storage i pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Ułamki dziesiętne są dozwolone (na przykład: 0,25).

**--cache-control** string Ustaw nagłówek cache-control. Zwracane po pobraniu.

**--check-length** Sprawdź długość pliku w miejscu docelowym po przeniesieniu. W przypadku niezgodności między źródłem i miejscem docelowym transfer jest oznaczony jako niepowodzenie. (wartość domyślna to `true` )

**--check-md5** string Określa, jak ściśle mają być weryfikowane skróty MD5 podczas pobierania. Dostępne tylko podczas pobierania. Dostępne opcje: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . (wartość `FailIfDifferent` domyślna ) (wartość domyślna "FailIf Nieudane")

**--content-disposition** string Ustaw nagłówek content-disposition. Zwracane po pobraniu.

**--content-encoding** string Ustaw nagłówek content-encoding. Zwracane po pobraniu.

**--content-language** string Ustaw nagłówek content-language. Zwracane po pobraniu.

**--content-type** string Określa typ zawartości pliku. Oznacza no-guess-mime-type. Zwracane po pobraniu.

**— dekompresja** Automatycznie dekompresuj pliki podczas pobierania, jeśli ich kodowanie zawartości wskazuje, że są one skompresowane. Obsługiwane wartości kodowania zawartości to `gzip` i `deflate` . Rozszerzenia plików lub `.gz` / `.gzip` nie `.zz` są konieczne, ale zostaną usunięte, jeśli są obecne.

**Ciąg --exclude-attributes** (tylko system Windows) Wyklucza pliki, których atrybuty są zgodne z listą atrybutów. Na przykład: A; S; R

**--exclude-blob-type** ciąg opcjonalnie określa typ obiektu blob ( ), który ma być wykluczany podczas kopiowania obiektów blob z kontenera `BlockBlob` /  `PageBlob` /  `AppendBlob` lub konta. Użycie tej flagi nie ma zastosowania do kopiowania danych z usług spoza platformy Azure do usługi. Więcej niż jeden obiekt blob powinien być oddzielony . `;` 

**--exclude-path** string Wyklucz te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: `myFolder;myFolder/subDirName/file.pdf` ). W połączeniu z przechodzeniem konta ścieżki nie zawierają nazwy kontenera.

**--exclude-pattern** string Wyklucz te pliki podczas kopiowania. Ta opcja obsługuje symbole wieloznaczne (*).

**--follow-symlinks**  Podczas przekazywania z lokalnego systemu plików postępuj zgodnie z linkami symbolicznymi.

**--force-if-read-only** W przypadku zastępowania istniejącego pliku w systemie Windows lub Azure Files, wymuś zastąpienie, aby działało, nawet jeśli istniejący plik ma ustawiony atrybut tylko do odczytu.

**--from-to** string Opcjonalnie określa kombinację miejsca docelowego źródła. Na przykład: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--help**  help for copy (Pomoc w przypadku kopiowania).

**--include-after** ciąg uwzględnij tylko te pliki zmodyfikowane w danej dacie/godzinie lub po tej dacie. Wartość powinna być w formacie ISO8601. Jeśli strefa czasowa nie zostanie określona, przyjmuje się, że wartość znajduje się w lokalnej strefie czasowej maszyny z uruchomionym programem AzCopy. na przykład dla godziny UTC lub dla `2020-08-19T15:04:00Z` `2020-08-19` północy (00:00) w lokalnej strefie czasowej. Podobnie jak w programie AzCopy 10.5, ta flaga ma zastosowanie tylko do plików, a nie folderów, więc właściwości folderów nie będą kopiowane w przypadku używania tej flagi z lub `--preserve-smb-info` `--preserve-smb-permissions` .

 **--include-before** ciąg Uwzględnij tylko te pliki zmodyfikowane przed lub w danej dacie/godzinie. Wartość powinna być w formacie ISO8601. Jeśli strefa czasowa nie zostanie określona, przyjmuje się, że wartość znajduje się w lokalnej strefie czasowej maszyny z uruchomionym programem AzCopy. Na przykład `2020-08-19T15:04:00Z` na czas UTC lub `2020-08-19` o północy (00:00) w lokalnej strefie czasowej. W programie AzCopy 10.7 ta flaga ma zastosowanie tylko do plików, a nie folderów, więc właściwości folderów nie będą kopiowane w przypadku używania tej flagi z programem `--preserve-smb-info` lub `--preserve-smb-permissions` .

**--include-attributes** string (tylko system Windows) Zawiera pliki, których atrybuty są zgodne z listą atrybutów. Na przykład: A; S; R

**--include-path** string Uwzględnij tylko te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: `myFolder;myFolder/subDirName/file.pdf` ).

**--include-pattern** string Uwzględnij tylko te pliki podczas kopiowania. Ta opcja obsługuje symbole wieloznaczne (*). Oddziel pliki przy użyciu `;` .

**--list-of-versions** string Określa plik, w którym każdy identyfikator wersji jest wymieniony w osobnym wierszu. Upewnij się, że źródło musi wskazać pojedynczy obiekt blob, a wszystkie identyfikatory wersji określone w pliku przy użyciu tej flagi muszą należeć tylko do źródłowego obiektu blob. Program AzCopy pobierze określone wersje z udostępnionego folderu docelowego. Aby uzyskać więcej informacji, zobacz [Pobieranie poprzednich wersji obiektu blob](./storage-use-azcopy-v10.md#transfer-data).

**--log-level** string Define the log verbosity for the log file, available levels: INFO(all requests/responses), WARNING(slow responses), ERROR(only failed requests) and NONE (no output logs). (wartość `INFO` domyślna). 

**--metadata** string Przekaż do usługi Azure Storage przy użyciu tych par klucz-wartość jako metadanych.

**--no-guess-mime-type**  Uniemożliwia programowi AzCopy wykrywanie typu zawartości na podstawie rozszerzenia lub zawartości pliku.

**--overwrite** string Zastąp pliki i obiekty blob powodujące konflikt w miejscu docelowym, jeśli ta flaga jest ustawiona na wartość true. (ustawienie domyślne `true` ) Możliwe wartości to `true` , `false` , i `prompt` `ifSourceNewer` . W przypadku miejsc docelowych, które obsługują foldery, sprzeczne właściwości na poziomie folderu zostaną zastąpione tą flagą jest lub jeśli do monitu zostanie udostępniona `true` pozytywna odpowiedź. (wartość domyślna "true")

**--page-blob-tier** string Przekazywanie stronicowego obiektu blob do usługi Azure Storage przy użyciu tej warstwy obiektów blob. (wartość `None` domyślna). (wartość domyślna "Brak")

**--preserve-last-modified-time**  Dostępne tylko wtedy, gdy miejscem docelowym jest system plików.

**--preserve-owner**    Ma wpływ tylko na pobieranie i tylko wtedy, gdy `--preserve-smb-permissions` jest używany. W przypadku wartości true (ustawienie domyślne) pliki Właściciel i Grupa są zachowywane podczas pobierania. W przypadku ustawienia wartości false nadal będą zachowywane adresy ACL, ale właściciel i grupa będą oparte na użytkowniku z uruchomionym `--preserve-smb-permissions` programem AzCopy (wartość domyślna true)

**--preserve-smb-info**   Wartość domyślna to False. Zachowuje informacje o właściwościach SMB (czas ostatniego zapisu, czas tworzenia, bity atrybutów) między zasobami z systemem SMB (system Windows i Azure Files). Tylko bity atrybutów obsługiwane przez Azure Files zostaną przeniesione; inne zostaną zignorowane. Ta flaga ma zastosowanie zarówno do plików, jak i folderów, chyba że określono filtr tylko do plików (na przykład include-pattern). Informacje przesyłane dla folderów są takie same jak w przypadku plików, z wyjątkiem czasu ostatniego zapisu, który nigdy nie jest zachowywany dla folderów.

**--preserve-smb-permissions**   Wartość domyślna to False. Zachowuje adresy ACL SMB między świadomymi zasobami (windows i Azure Files). W przypadku pobierania będzie również potrzebna flaga , aby przywrócić uprawnienia, w przypadku których nowy właściciel nie będzie użytkownikiem `--backup` z uruchomionym programem AzCopy. Ta flaga ma zastosowanie zarówno do plików, jak i folderów, chyba że określono filtr tylko do plików (na przykład `include-pattern` ).

**--put-md5**    Utwórz skrót MD5 każdego pliku i zapisz skrót jako właściwość Content-MD5 docelowego obiektu blob lub pliku. (Domyślnie skrót NIE jest tworzony). Dostępne tylko podczas przekazywania.

**--rekursywny**    Rekursywnie przyjrzyj się podkatalogom podczas przekazywania z lokalnego systemu plików.

**--s2s-detect-source-changed**   Wykryj, czy plik źródłowy/obiekt blob zmienia się podczas odczytywania. (Ten parametr dotyczy tylko kopii typu usługa-usługa, ponieważ odpowiednie sprawdzanie jest trwale włączone dla przekazywania i pobierania).

**--s2s-handle-invalid-metadata** string Określa sposób obsługi nieprawidłowych kluczy metadanych. Dostępne opcje: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (wartość `ExcludeIfInvalid` domyślna). (domyślnie "ExcludeIfInvalid")

**--s2s-preserve-access-tier**   Zachowaj warstwę dostępu podczas kopiowania z usługi do usługi. Zapoznaj się [z tematem Azure Blob Storage: hot, cool, and archive access tiers](../blobs/storage-blob-storage-tiers.md) (Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum), aby upewnić się, że docelowe konto magazynu obsługuje ustawianie warstwy dostępu. Jeśli ustawienie warstwy dostępu nie jest obsługiwane, użyj wartości s2sPreserveAccessTier=false, aby pominąć kopiowanie warstwy dostępu. (wartość `true` domyślna).  (wartość domyślna "true")

**--s2s-preserve-properties**   Zachowaj pełne właściwości podczas kopiowania z usługi do usługi. W przypadku usług AWS S3 i Azure File, które nie są pojedynczym źródłem plików, operacja na liście nie zwraca pełnych właściwości obiektów i plików. Aby zachować pełne właściwości, rozszerzenie AzCopy musi wysłać jedno dodatkowe żądanie na obiekt lub plik. (wartość domyślna true)

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--cap-mb/s float**   Limituje szybkość transferu w megabitach na sekundę. Przepływność chwila po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.

**--output-type** ciąg Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to `text`. (domyślna wartość "text")

**--trusted-microsoft-suffixes** ciąg Określa dodatkowe sufiksy domeny, Azure Active Directory tokeny logowania mogą być wysyłane.  Wartość domyślna to `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Wszystkie wymienione tutaj wartości są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
