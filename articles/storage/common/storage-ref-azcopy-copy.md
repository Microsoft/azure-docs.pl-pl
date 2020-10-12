---
title: kopia AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia copy AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 736746cc710e4e22f61edaa7b2dfd1ceef3d90eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89645472"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopiuje dane źródłowe do lokalizacji docelowej.

## <a name="synopsis"></a>Streszczenie

Kopiuje dane źródłowe do lokalizacji docelowej. Obsługiwane są następujące wskazówki:

  - < lokalnego — > obiektu blob platformy Azure (uwierzytelnianie SAS lub OAuth)
  - < lokalnego > Azure Files (uwierzytelnianie SAS udziału/katalogu)
  - Local <-> Azure Data Lake Storage Gen 2 (uwierzytelnianie SAS, OAuth lub klucz współużytkowany)
  - Obiekt blob platformy Azure (SAS lub public) — > obiektów blob platformy Azure (uwierzytelnianie SAS lub OAuth)
  - Obiekt blob platformy Azure (SAS lub public) — > Azure Files (SAS)
  - Azure Files (SAS) — > Azure Files (SAS)
  - Azure Files (SAS) — > obiekt blob platformy Azure (uwierzytelnianie SAS lub OAuth)
  - Amazon Web Services (AWS) S3 (klucz dostępu) — > blokowe obiekty blob platformy Azure (uwierzytelnianie SAS lub OAuth)

Aby uzyskać więcej informacji, zobacz sekcję przykłady w tym artykule.

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Zaawansowane

AzCopy automatycznie wykrywa typ zawartości plików podczas ich przekazywania z dysku lokalnego. AzCopy wykrywa typ zawartości na podstawie rozszerzenia lub zawartości pliku (jeśli nie określono rozszerzenia).

Wbudowana tabela wyszukiwania jest mała, ale w systemie UNIX jest rozszerzana o pliki systemu lokalnego, `mime.types` Jeśli są one dostępne w ramach jednej lub kilku z tych nazw:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębniane z rejestru. Tę funkcję można wyłączyć za pomocą pomocy flagi. Aby uzyskać więcej informacji, zobacz sekcję flag w tym artykule.

W przypadku ustawienia zmiennej środowiskowej przy użyciu wiersza polecenia ta zmienna zostanie odczytana w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne w historii, można użyć skryptu w celu wyświetlenia monitu o podanie poświadczeń użytkownika i ustawienia zmiennej środowiskowej.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Przykłady

Przekaż pojedynczy plik przy użyciu uwierzytelniania OAuth. Jeśli jeszcze nie zalogowano się w usłudze AzCopy, uruchom `azcopy login` polecenie przed uruchomieniem następującego polecenia.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Analogicznie jak powyżej, ale ten czas również oblicza skrót MD5 zawartości pliku i zapisuje go jako właściwość Content-MD5 obiektu BLOB:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Przekaż pojedynczy plik przy użyciu tokenu sygnatury dostępu współdzielonego:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Przekaż pojedynczy plik przy użyciu tokenu SAS i potoków (tylko blokowe obiekty blob):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Przekaż cały katalog przy użyciu tokenu sygnatury dostępu współdzielonego:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

lub

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Przekaż zestaw plików przy użyciu tokenu sygnatury dostępu współdzielonego i symboli wieloznacznych (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Przekaż pliki i katalogi, używając tokenu SAS i symboli wieloznacznych (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Pobierz pojedynczy plik przy użyciu uwierzytelniania OAuth. Jeśli jeszcze nie zalogowano się w usłudze AzCopy, uruchom `azcopy login` polecenie przed uruchomieniem następującego polecenia.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Pobieranie pojedynczego pliku przy użyciu tokenu SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Pobieranie pojedynczego pliku przy użyciu tokenu SAS, a następnie przekazanie danych wyjściowych do pliku (tylko blokowe obiekty blob):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Pobierz cały katalog przy użyciu tokenu sygnatury dostępu współdzielonego:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Uwaga dotycząca używania symbolu wieloznacznego (*) w adresach URL:

Istnieją tylko dwa obsługiwane sposoby używania symbolu wieloznacznego w adresie URL. 

- Możesz użyć jednej tuż po końcowym ukośniku (/) adresu URL. Użycie symboli wieloznacznych kopiuje wszystkie pliki w katalogu bezpośrednio do miejsca docelowego bez umieszczania ich w podkatalogu. 

- Możesz również użyć symbolu wieloznacznego w nazwie kontenera, tak długo, jak adres URL odwołuje się tylko do kontenera, a nie do obiektu BLOB. Takie podejście służy do uzyskiwania plików z podzestawu kontenerów. 

Pobierz zawartość katalogu bez kopiowania zawierającego go katalogu.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Pobierz całe konto magazynu.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Pobierz podzestaw kontenerów w ramach konta magazynu, używając symbolu wieloznacznego (*) w nazwie kontenera.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Skopiuj pojedynczy obiekt BLOB do innego obiektu BLOB przy użyciu tokenu SAS.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Skopiuj pojedynczy obiekt BLOB do innego obiektu BLOB przy użyciu tokenu SAS i tokenu uwierzytelniania. Musisz użyć tokenu SAS na końcu adresu URL konta źródłowego, ale konto docelowe nie jest potrzebne, jeśli zalogujesz się do AzCopy przy użyciu `azcopy login` polecenia. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Kopiowanie jednego katalogu wirtualnego obiektów BLOB do innego przy użyciu tokenu SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Skopiuj wszystkie kontenery obiektów blob, katalogi i obiekty blob z konta magazynu do innego przy użyciu tokenu SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Skopiuj pojedynczy obiekt do Blob Storage z Amazon Web Services (AWS) S3 przy użyciu klucza dostępu i tokenu SAS. Najpierw Ustaw zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Skopiuj cały katalog do Blob Storage z AWS S3 przy użyciu klucza dostępu i tokenu SAS. Najpierw Ustaw zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Zapoznaj się z tematem https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html , aby lepiej zrozumieć symbol zastępczy [folder].

Skopiuj wszystkie zasobniki do Blob Storage z Amazon Web Services (AWS) przy użyciu klucza dostępu i tokenu SAS. Najpierw Ustaw zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Skopiuj wszystkie zasobniki do Blob Storage z regionu Amazon Web Services (AWS) przy użyciu klucza dostępu i tokenu SAS. Najpierw Ustaw zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Skopiuj podzestaw zasobników przy użyciu symbolu wieloznacznego (*) w nazwie zasobnika. Podobnie jak w poprzednich przykładach, potrzebny będzie klucz dostępu i token SAS. Upewnij się, że ustawiono zmienną środowiskową `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` dla źródła AWS S3.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

## <a name="options"></a>Opcje

**--kopia zapasowa** Aktywuje SeBackupPrivilege systemu Windows pod kątem przekazywania lub SeRestorePrivilege do pobrania, aby umożliwić AzCopy wyświetlanie i odczytywanie wszystkich plików, niezależnie od ich uprawnień systemu plików oraz przywracania wszystkich uprawnień. Wymaga, aby konto z systemem AzCopy już miało te uprawnienia (na przykład ma prawa administratora lub jest członkiem `Backup Operators` grupy). Ta flaga aktywuje uprawnienia, które już istnieją dla konta.

**--ciąg typu BLOB** definiuje typ obiektu BLOB w miejscu docelowym. Służy do przekazywania obiektów blob i kopiowania między kontami (domyślnie `Detect` ). Prawidłowe wartości to `Detect` , `BlockBlob` , `PageBlob` , i `AppendBlob` . Podczas kopiowania między kontami, wartość `Detect` powoduje, że AzCopy używa typu źródłowego obiektu BLOB do określenia typu docelowego obiektu BLOB. Podczas przekazywania pliku określa, `Detect` czy plik jest plikiem VHD lub VHDX na podstawie rozszerzenia pliku. Jeśli plik jest eterem pliku VHD lub VHDX, AzCopy traktuje ten plik jako stronicowy obiekt BLOB. (domyślnie "Detect")

**--Block-BLOB-** w ciągu warstwy Przekaż blokowy obiekt BLOB do usługi Azure Storage przy użyciu tej warstwy obiektów BLOB. (domyślnie "none")

**--rozmiar bloku-MB** zmiennoprzecinkowy Użyj tego rozmiaru bloku (określonego w MIB) podczas przekazywania do usługi Azure Storage i pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dozwolone są ułamki dziesiętne (na przykład: 0,25).

**--ciąg sterujący pamięci podręcznej** ustawia nagłówek Cache-Control. Zwrócony przy pobieraniu.

**--check-Length** Sprawdź długość pliku w miejscu docelowym po przeniesieniu. Jeśli występuje niezgodność między źródłem i miejscem docelowym, transfer zostanie oznaczony jako niepowodzenie. (wartość domyślna to `true` )

**--check-MD5** ciąg Określa, jak ściśle skróty MD5 powinny być sprawdzane podczas pobierania. Dostępne tylko podczas pobierania. Dostępne opcje: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (ustawienie domyślne `FailIfDifferent` ) (domyślnie "FailIfDifferent")

**--ciąg dyspozycji** do usuwania zawartości — nagłówek dyspozycji. Zwrócony przy pobieraniu.

**--ciąg kodowania zawartości** ustawia nagłówek Content-Encoding. Zwrócony przy pobieraniu.

**--ciąg języka zawartości** ustawia nagłówek Content-Language. Zwrócony przy pobieraniu.

**--ciąg typu zawartości** określa typ zawartości pliku. Oznacza brak typu MIME-Type. Zwrócony przy pobieraniu.

**--dekompresowanie** Automatycznie Kompresuj pliki podczas pobierania, jeśli ich kodowanie zawartości wskazuje, że są skompresowane. Obsługiwane wartości kodowania zawartości to `gzip` i `deflate` . Rozszerzenia plików `.gz` / `.gzip` lub `.zz` nie są niezbędne, ale zostaną usunięte, jeśli istnieją.

**--exclude-Attribute** (tylko system Windows) wyklucza pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; Wolumin ®

**--exclude-BLOB-Type** , opcjonalnie określa typ obiektu BLOB ( `BlockBlob` /  `PageBlob` /  `AppendBlob` ) do wykluczenia podczas kopiowania obiektów blob z kontenera lub konta. Użycie tej flagi nie ma zastosowania do kopiowania danych z usługi spoza platformy Azure do usługi. Więcej niż jeden obiekt BLOB powinien być rozdzielony przez `;` . 

**--exclude-Path** ciąg Wyklucz te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: `myFolder;myFolder/subDirName/file.pdf` ). W przypadku użycia w połączeniu z przechodzeniem konta ścieżki nie uwzględniają nazwy kontenera.

**--wykluczanie-wzorzec** ciąg Wyklucz te pliki podczas kopiowania. Ta opcja obsługuje symbole wieloznaczne (*).

**--Obserwuj-linków symbolicznych**  Śledź linki symboliczne podczas przekazywania z lokalnego systemu plików.

**--Wymuś-if-tylko do odczytu** Podczas zastępowania istniejącego pliku w systemie Windows lub Azure Files Wymuś zastępowanie, nawet jeśli istniejący plik ma ustawiony atrybut tylko do odczytu.

**--ciąg od do do** , opcjonalnie określa kombinację źródłowej lokalizacji docelowej. Na przykład: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--**  Pomoc dotycząca kopiowania.

**--include-po** ciągu Uwzględnij tylko te pliki, które zostały zmodyfikowane w dniu lub po danej dacie/godzinie. Wartość powinna być w formacie ISO8601. Jeśli nie określono strefy czasowej, zakłada się, że wartość należy do lokalnej strefy czasowej maszyny z systemem AzCopy. na przykład `2020-08-19T15:04:00Z` w przypadku czasu UTC lub `2020-08-19` północy (00:00) w lokalnej strefie czasowej. Podobnie jak w przypadku AzCopy 10,5, ta flaga ma zastosowanie tylko do plików, a nie do folderów, dlatego właściwości folderu nie będą kopiowane w przypadku używania tej flagi z `--preserve-smb-info` lub `--preserve-smb-permissions` .

**--include — ciąg atrybutów** (tylko system Windows) zawiera pliki, których atrybuty są zgodne z listą atrybutów. Na przykład: A; Wolumin ®

**--include-Path** ciąg Uwzględnij tylko te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: `myFolder;myFolder/subDirName/file.pdf` ).

**--ciąg include-Pattern** zawiera tylko te pliki podczas kopiowania. Ta opcja obsługuje symbole wieloznaczne (*). Oddziel pliki przy użyciu `;` .

**--ciąg listy wersji** określa plik, w którym każdy identyfikator wersji jest wymieniony w osobnym wierszu. Upewnij się, że źródło musi wskazywać na pojedynczy obiekt BLOB, a wszystkie identyfikatory wersji określone w pliku używające tej flagi muszą należeć tylko do źródłowego obiektu BLOB. Program AzCopy pobierze określone wersje w udostępnionym folderze docelowym. Aby uzyskać więcej informacji, zobacz [pobieranie poprzednich wersji obiektu BLOB](storage-use-azcopy-blobs.md#download-previous-versions-of-a-blob).

**--ciąg na poziomie dziennika** definiuje szczegółowość dziennika dla pliku dziennika, dostępne poziomy: info (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna `INFO` ). 

**—** przesyłanie ciągu metadanych do usługi Azure Storage przy użyciu tych par klucz-wartość jako metadanych.

**--no-zgadywanie-MIME-Type**  Uniemożliwia programowi AzCopy wykrywanie typu zawartości na podstawie rozszerzenia lub zawartości pliku.

**--Zastąp** ciąg nadpisać pliki powodujące konflikt i obiekty blob w miejscu docelowym, jeśli ta flaga jest ustawiona na wartość true. (ustawienie domyślne `true` ) Możliwe wartości to `true` , `false` , `prompt` , i `ifSourceNewer` . W przypadku miejsc docelowych, które obsługują foldery, sprzeczne właściwości na poziomie folderu będą zastępowane flagą `true` lub jeśli do monitu zostanie dostarczona pozytywna odpowiedź. (wartość domyślna to "true")

**--Strona-ciąg warstwy obiektu** BLOB przekazywanie stronicowego obiektu BLOB do usługi Azure Storage przy użyciu tej warstwy obiektów BLOB. (wartość domyślna `None` ). (domyślnie "none")

**--Preserve-Last-Modified-Time**  Dostępne tylko wtedy, gdy miejscem docelowym jest system plików.

**--Preserve-Owner**    Ma wpływ tylko na pobieranie i tylko wtedy, gdy `--preserve-smb-permissions` jest używany. Jeśli wartość jest równa true (domyślnie), właściciel pliku i Grupa są zachowywane w plikach do pobrania. W przypadku wybrania wartości false `--preserve-smb-permissions` nadal będą zachowywane listy ACL, ale właściciel i Grupa będą oparte na użytkowniku z systemem AzCopy (wartość domyślna to true).

**--Preserve-SMB-info**   Domyślnie wartość false. Zachowuje informacje o właściwościach protokołu SMB (czas ostatniego zapisu, czas utworzenia, bity atrybutów) między zasobami opartymi na protokole SMB (Windows i Azure Files). Tylko bity atrybutu obsługiwane przez Azure Files zostaną przesłane; wszystkie inne zostaną zignorowane. Ta flaga ma zastosowanie do plików i folderów, chyba że określony jest filtr tylko dla plików (na przykład include-Pattern). Informacje przesyłane do folderów są takie same jak dla plików, z wyjątkiem czasu ostatniego zapisu, który nigdy nie jest zachowywany dla folderów.

**--Preserve-SMB-uprawnienia**   Domyślnie wartość false. Zachowuje listy ACL protokołu SMB między zasobami zależnymi (Windows i Azure Files). W przypadku plików do pobrania potrzebna jest również `--backup` Flaga przywracania uprawnień, w których nowy właściciel nie będzie użytkownikiem AzCopy. Ta flaga ma zastosowanie do plików i folderów, chyba że określony jest filtr tylko dla plików (na przykład `include-pattern` ).

**--Put-MD5**    Utwórz skrót MD5 każdego pliku i Zapisz skrót jako właściwość Content-MD5 docelowego obiektu BLOB lub pliku. (Domyślnie skrót nie jest tworzony). Dostępne tylko podczas przekazywania.

**--rekursywnie**    Poszukaj podkatalogów cyklicznie podczas przekazywania z lokalnego systemu plików.

**--S2S-Detect-Source-zmiana**   Wykryj, czy źródłowy plik/obiekt BLOB zmienia się podczas odczytywania. (Ten parametr ma zastosowanie tylko do kopii między usługą, ponieważ odpowiednie sprawdzenie jest trwale włączone do przekazywania i pobierania).

**--S2S-Handle-nieprawidłowy** ciąg Określa sposób obsługi nieprawidłowych kluczy metadanych. Dostępne opcje: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (wartość domyślna `ExcludeIfInvalid` ). (domyślnie "ExcludeIfInvalid")

**--S2S-Preserve-dostęp-warstwa**   Zachowaj warstwę dostępu podczas kopiowania usługi do usługi. Zapoznaj się z [usługą Azure Blob Storage: warstwami dostępu gorąca, chłodna i archiwalna](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) , aby upewnić się, że docelowe konto magazynu obsługuje ustawienie Warstwa dostępu. W przypadkach, w których ustawienie warstwy dostępu nie jest obsługiwane, użyj s2sPreserveAccessTier = false, aby obejść kopiowanie warstwy dostępu. (wartość domyślna `true` ).  (wartość domyślna to "true")

**--S2S-Preserve-Properties**   Zachowaj pełne właściwości podczas kopiowania usługi do usługi. W przypadku AWS S3 i plików platformy Azure, które nie są pojedynczym źródłem plików, operacja listy nie zwraca pełnych właściwości obiektów i plików. Aby zachować pełne właściwości, AzCopy musi wysłać jedno dodatkowe żądanie dla każdego obiektu lub pliku. (wartość domyślna to true)

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--Cap-MB/s**   Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.

**--** format ciągu typu danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to `text`. (domyślny "tekst")

**--Zaufane — ciąg sufiksów firmy Microsoft** określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
