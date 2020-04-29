---
title: kopia AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia copy AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0325a71fb069f3d96f05d106afac1639fc38fe42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253343"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopiuje dane źródłowe do lokalizacji docelowej.

## <a name="synopsis"></a>Streszczenie

Kopiuje dane źródłowe do lokalizacji docelowej. Obsługiwane są następujące wskazówki:

  - < lokalnego — > obiektu blob platformy Azure (uwierzytelnianie SAS lub OAuth)
  - < lokalnego > Azure Files (uwierzytelnianie SAS udziału/katalogu)
  - Local <-> ADLS Gen 2 (uwierzytelnianie SAS, OAuth lub SharedKey)
  - Obiekt blob platformy Azure (SAS lub public) — > obiektów blob platformy Azure (uwierzytelnianie SAS lub OAuth)
  - Obiekt blob platformy Azure (SAS lub public) — > Azure Files (SAS)
  - Azure Files (SAS) — > Azure Files (SAS)
  - Azure Files (SAS) — > obiekt blob platformy Azure (uwierzytelnianie SAS lub OAuth)
  - AWS S3 (klucz dostępu) — > blokowych obiektów blob platformy Azure (uwierzytelnianie SAS lub OAuth)

Aby uzyskać więcej informacji, zapoznaj się z przykładami.

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Zaawansowane

AzCopy automatycznie wykrywa typ zawartości plików podczas przekazywania z dysku lokalnego na podstawie rozszerzenia lub zawartości pliku (jeśli nie określono rozszerzenia).

Wbudowana tabela wyszukiwania jest mała, ale w systemie UNIX jest rozszerzana przez system lokalny. typy plików MIME. Types, jeśli są dostępne w co najmniej jednej z tych nazw:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębniane z rejestru. Tę funkcję można wyłączyć za pomocą pomocy flagi. Zapoznaj się z sekcją flagi.

W przypadku ustawienia zmiennej środowiskowej przy użyciu wiersza polecenia ta zmienna zostanie odczytana w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne w historii, można użyć skryptu w celu wyświetlenia monitu o podanie poświadczeń użytkownika i ustawienia zmiennej środowiskowej.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Przykłady

Przekaż pojedynczy plik przy użyciu uwierzytelniania OAuth. Jeśli jeszcze nie zalogowano się w usłudze AzCopy, uruchom polecenie logowania AzCopy przed uruchomieniem następującego polecenia.

- AzCopy CP "/Path/to/File.txt" "https://[Account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]"

Analogicznie jak powyżej, ale ten czas również oblicza skrót MD5 zawartości pliku i zapisuje go jako właściwość Content-MD5 obiektu BLOB:

- AzCopy CP "/Path/to/File.txt" "https://[Account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]"--Put-MD5

Przekaż pojedynczy plik przy użyciu tokenu sygnatury dostępu współdzielonego:

- AzCopy CP "/Path/to/File.txt" "https://[Account]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]? [SAS] "

Przekaż pojedynczy plik przy użyciu tokenu SAS i potoków (tylko blokowe obiekty blob):
  
- Kot "/Path/to/File.txt" | AzCopy CP "https://[Account]. blob. Core. Windows. NET/[Container]/[ścieżka/do/BLOB]? [SAS] "

Przekaż cały katalog przy użyciu tokenu sygnatury dostępu współdzielonego:
  
- AzCopy CP "/Path/to/dir" "https://[Account]. blob. Core. Windows. NET/[Container]/[path/to/Directory]? [SAS] "--rekursywnie = true

lub

- AzCopy CP "/Path/to/dir" "https://[Account]. blob. Core. Windows. NET/[Container]/[path/to/Directory]? [SAS] "--rekursywny = true--Put-MD5

Przekaż zestaw plików przy użyciu tokenu sygnatury dostępu współdzielonego i symboli wieloznacznych (*):

- AzCopy CP "/Path/*foo/* bar/*. PDF" "https://[Account]. blob. Core. Windows. NET/[Container]/[path/to/Directory]? [SAS] "

Przekaż pliki i katalogi, używając tokenu SAS i symboli wieloznacznych (*):

- AzCopy CP "/Path/*foo/* bar *" "https://[Account]. blob. Core. Windows. NET/[Container]/[path/to/Directory]? [SAS] "--rekursywnie = true

Pobierz pojedynczy plik przy użyciu uwierzytelniania OAuth. Jeśli jeszcze nie zalogowano się w usłudze AzCopy, uruchom polecenie logowania AzCopy przed uruchomieniem następującego polecenia.

- AzCopy CP "https://[Account]. blob. Core. Windows. NET/[Container]/[ścieżka/do/BLOB]" ""/Path/to/File.txt "

Pobieranie pojedynczego pliku przy użyciu tokenu SAS:

- AzCopy CP "https://[Account]. blob. Core. Windows. NET/[Container]/[ścieżka/do/BLOB]? [SAS] ""/Path/to/File.txt "

Pobieranie pojedynczego pliku przy użyciu tokenu SAS, a następnie przekazanie danych wyjściowych do pliku (tylko blokowe obiekty blob):
  
- AzCopy CP "https://[Account]. blob. Core. Windows. NET/[Container]/[ścieżka/do/BLOB]? [SAS] ">"/Path/to/File.txt "

Pobierz cały katalog przy użyciu tokenu sygnatury dostępu współdzielonego:
  
- AzCopy CP "https://[Account]. blob. Core. Windows. NET/[Container]/[path/to/Directory]? [Sygnatura dostępu współdzielonego] ""/Path/to/dir "--rekursywnie = prawda

Uwaga dotycząca używania symbolu wieloznacznego (*) w adresach URL:

Istnieją tylko dwa obsługiwane sposoby używania symbolu wieloznacznego w adresie URL. 

- Możesz użyć jednej tuż po końcowym ukośniku (/) adresu URL. Spowoduje to skopiowanie wszystkich plików znajdujących się w katalogu bezpośrednio do miejsca docelowego bez umieszczania ich w podkatalogu.

- Można go również użyć w nazwie kontenera, tak długo, jak adres URL odwołuje się tylko do kontenera, a nie do obiektu BLOB. Takie podejście służy do uzyskiwania plików z podzestawu kontenerów.

Pobierz zawartość katalogu bez kopiowania zawierającego go katalogu.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[Container]/[ścieżka/do/folder]/*? [SAS] ""/Path/to/dir "

Pobierz całe konto magazynu.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/" "/Path/to/dir"-rekursywnie

Pobierz podzestaw kontenerów w ramach konta magazynu, używając symbolu wieloznacznego (*) w nazwie kontenera.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[Container * Name]" "/Path/to/dir"--rekursywnie

Skopiuj pojedynczy obiekt BLOB do innego obiektu BLOB przy użyciu tokenu SAS.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[Container]/[ścieżka/do/BLOB]? [SAS] "" https://[destaccount]. blob. Core. Windows. NET/[Container]/[ścieżka/do/BLOB]? [SAS] "

Skopiuj pojedynczy obiekt BLOB do innego obiektu BLOB przy użyciu tokenu SAS i tokenu OAuth. Musisz użyć tokenu sygnatury dostępu współdzielonego na końcu adresu URL konta źródłowego, ale konto docelowe nie jest potrzebne, jeśli zalogujesz się do AzCopy przy użyciu polecenia logowania AzCopy. 

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[Container]/[ścieżka/do/BLOB]? [SAS] "" https://[destaccount]. blob. Core. Windows. NET/[Container]/[ścieżka/do/BLOB] "

Kopiowanie jednego katalogu wirtualnego obiektów BLOB do innego przy użyciu tokenu SAS:

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET/[Container]/[ścieżka/do/katalog]? [SAS] "" https://[destaccount]. blob. Core. Windows. NET/[Container]/[ścieżka/do/katalog]? [SAS] "--rekursywnie = true

Skopiuj wszystkie kontenery obiektów blob, katalogi i obiekty blob z konta magazynu do innego przy użyciu tokenu SAS:

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. NET? [SAS] "" https://[destaccount]. blob. Core. Windows. NET? [SAS] "--rekursywnie = true

Skopiuj pojedynczy obiekt do Blob Storage z Amazon Web Services (AWS) S3 przy użyciu klucza dostępu i tokenu SAS. Najpierw Ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.
  
- AzCopy CP "https://s3.amazonaws.com/[przedział]/[Object]" "https://[destaccount]. blob. Core. Windows. NET/[Container]/[path/to/BLOB]? [SAS] "

Skopiuj cały katalog do Blob Storage z AWS S3 przy użyciu klucza dostępu i tokenu SAS. Najpierw Ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.

- AzCopy CP "https://s3.amazonaws.com/[zasobnik]/[folder]" "https://[destaccount]. blob. Core. Windows. NET/[Container]/[path/to/Directory]? [SAS] "--rekursywnie = true

Zapoznaj się https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html z tematem, aby lepiej zrozumieć symbol zastępczy [folder].

Skopiuj wszystkie zasobniki do Blob Storage z Amazon Web Services (AWS) przy użyciu klucza dostępu i tokenu SAS. Najpierw Ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.

- AzCopy CP "https://s3.amazonaws.com/" "https://[destaccount]. blob. Core. Windows. NET? [SAS] "--rekursywnie = true

Skopiuj wszystkie zasobniki do Blob Storage z regionu Amazon Web Services (AWS) przy użyciu klucza dostępu i tokenu SAS. Najpierw Ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.

- AzCopy CP "https://s3-[region]. amazonaws. com/" "https://[destaccount]. blob. Core. Windows. NET? [SAS] "--rekursywnie = true

Skopiuj podzestaw zasobników przy użyciu symbolu wieloznacznego (*) w nazwie zasobnika. Podobnie jak w poprzednich przykładach, potrzebny będzie klucz dostępu i token SAS. Upewnij się, że ustawienie zmienna środowiskowa AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.

- AzCopy CP "https://s3.amazonaws.com/[zasobnik * Name]/" "https://[destaccount]. blob. Core. Windows. NET? [SAS] "--rekursywnie = true

## <a name="options"></a>Opcje

**--kopia zapasowa**                               Aktywuje SeBackupPrivilege systemu Windows pod kątem przekazywania lub SeRestorePrivilege do pobrania, aby zezwolić AzCopy na odczytywanie wszystkich plików, niezależnie od ich uprawnień systemu plików oraz przywracanie wszystkich uprawnień. Wymaga, aby konto z systemem AzCopy już miało te uprawnienia (np. ma prawa administratora lub jest członkiem grupy "Operatorzy kopii zapasowych"). Wszystkie te flagi są uaktywniane, gdy konto ma już uprawnienia.

**--ciąg typu BLOB** definiuje typ obiektu BLOB w miejscu docelowym. Służy do przekazywania obiektów blob i kopiowania między kontami (domyślnie "wykrywanie"). Prawidłowe wartości to "Detection", "BlockBlob", "PageBlob" i "AppendBlob". Podczas kopiowania między kontami, wartość "Detect" powoduje, że AzCopy używają typu źródłowego obiektu BLOB do określenia typu docelowego obiektu BLOB. Podczas przekazywania pliku polecenie "Detection" określa, czy plik jest dyskiem VHD lub plikiem VHDX na podstawie rozszerzenia pliku. Jeśli plik jest eterem pliku VHD lub VHDX, AzCopy traktuje ten plik jako stronicowy obiekt BLOB. (domyślnie "Detect")

**--Block-String-warstwowe** przekazywanie bloków BLOB bezpośrednio do wybranej [warstwy dostępu](../blobs/storage-blob-storage-tiers.md) . (domyślnie "none"). Prawidłowe wartości to "none", "gorąca", "chłodna" i "archiwalne". Jeśli nie przeszedł żadnej warstwy, obiekt BLOB będzie dziedziczyć warstwę konta magazynu.

**--rozmiar bloku-MB** zmiennoprzecinkowy Użyj tego rozmiaru bloku (określonego w MIB) podczas przekazywania do usługi Azure Storage i pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dozwolone są ułamki dziesiętne (na przykład: 0,25).

**--ciąg sterujący pamięci podręcznej** ustawia nagłówek Cache-Control. Zwrócony przy pobieraniu.

**--check-Length**                         Sprawdź długość pliku w miejscu docelowym po przeniesieniu. Jeśli występuje niezgodność między źródłem i miejscem docelowym, transfer zostanie oznaczony jako niepowodzenie. (wartość domyślna to true)

**--check-MD5** ciąg Określa, jak ściśle skróty MD5 powinny być sprawdzane podczas pobierania. Dostępne tylko podczas pobierania. Dostępne opcje: NOCHECK, LogOnd, FailIfDifferent, FailIfDifferentOrMissing. (domyślnie "FailIfDifferent")

**--ciąg dyspozycji** do usuwania zawartości — nagłówek dyspozycji. Zwrócony przy pobieraniu.

**--ciąg kodowania zawartości** ustawia nagłówek Content-Encoding. Zwrócony przy pobieraniu.

**--ciąg języka zawartości** ustawia nagłówek Content-Language. Zwrócony przy pobieraniu.

**--ciąg typu zawartości** określa typ zawartości pliku. Oznacza brak typu MIME-Type. Zwrócony przy pobieraniu.

**--dekompresowanie**                           Automatycznie Kompresuj pliki podczas pobierania, jeśli ich kodowanie zawartości wskazuje, że są skompresowane. Obsługiwane wartości kodowania zawartości to "gzip" i "Wklęśnięcie". Rozszerzenia plików ". gz"/". gzip" lub ". ZZ" nie są konieczne, ale zostaną usunięte, jeśli istnieją.

**--exclude-Attribute** (tylko system Windows) wyklucza pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; Wolumin ®

**--exclude-BLOB-Type** , opcjonalnie określa typ obiektu BLOB (BlockBlob/PageBlob/AppendBlob) do wykluczenia podczas kopiowania obiektów blob z kontenera lub konta. Użycie tej flagi nie ma zastosowania do kopiowania danych z usług spoza usługi Azure do usługi. Więcej niż jeden obiekt BLOB powinien być oddzielony znakiem ";".

**--exclude-Path** ciąg Wyklucz te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: Moja folder; folder/subDirName/plik. PDF). W przypadku użycia w połączeniu z przechodzeniem konta ścieżki nie uwzględniają nazwy kontenera.

**--wykluczanie-wzorzec** ciąg Wyklucz te pliki podczas kopiowania. Ta opcja obsługuje symbole wieloznaczne (*)

**--Obserwuj-linków symbolicznych**                      Śledź linki symboliczne podczas przekazywania z lokalnego systemu plików.

**--ciąg od do do** , opcjonalnie określa kombinację źródłowej lokalizacji docelowej. Na przykład: LocalBlob, BlobLocal, LocalBlobFS.

**-h,--** Pomoc dotycząca kopiowania

**--include — ciąg atrybutów** (tylko system Windows) Uwzględnij pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; Wolumin ®

**--include-Path** ciąg Uwzględnij tylko te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: Moja folder; folder/subDirName/plik. PDF).

**--ciąg include-Pattern** zawiera tylko te pliki podczas kopiowania. Ta opcja obsługuje symbole wieloznaczne (*). Oddziel pliki przy użyciu ";".

**--ciąg na poziomie dziennika** definiuje szczegółowość dziennika dla pliku dziennika, dostępne poziomy: info (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna: "INFO")

**—** przesyłanie ciągu metadanych do usługi Azure Storage przy użyciu tych par klucz-wartość jako metadanych.

**--no-zgadywanie-MIME-Type**                   Uniemożliwia programowi AzCopy wykrywanie typu zawartości na podstawie rozszerzenia lub zawartości pliku.

**--Zastąp** ciąg nadpisać pliki powodujące konflikt i obiekty blob w miejscu docelowym, jeśli ta flaga jest ustawiona na wartość true. Możliwe wartości to "true", "false", "ifSourceNewer" i "Prompt". (wartość domyślna to "true")

**--Strona-ciąg warstwy obiektu** BLOB przekazywanie stronicowego obiektu BLOB do usługi Azure Storage przy użyciu tej warstwy obiektów BLOB. (domyślnie "none")

**--Preserve-Last-Modified-Time**          Dostępne tylko wtedy, gdy miejscem docelowym jest system plików.

**--Preserve-SMB —** domyślnie wartość false. Zachowuje listy ACL protokołu SMB między zasobami zależnymi (Windows i Azure Files). W przypadku pobierania należy również użyć `--backup` flagi, aby przywrócić uprawnienia, w których nowy właściciel nie będzie użytkownikiem, na którym jest uruchomiony program AzCopy. Ta flaga ma zastosowanie do plików i folderów, chyba że określono filtr tylko dla plików (np. `include-pattern`).

**--Preserve-SMB-info** parametry false domyślnie. Zachowuje informacje o właściwościach protokołu SMB (czas ostatniego zapisu, czas utworzenia, bity atrybutów) między zasobami opartymi na protokole SMB (Windows i Azure Files). Tylko bity atrybutu obsługiwane przez Azure Files zostaną przesłane; wszystkie inne zostaną zignorowane. Ta flaga ma zastosowanie do plików i folderów, chyba że określony jest filtr tylko dla plików (np. include-wzorzec). Informacje przesyłane do folderów są takie same jak dla plików, z wyjątkiem czasu ostatniego zapisu, który nigdy nie jest zachowywany dla folderów.

**--Preserve-Owner**                       Tylko podczas pobierania danych i tylko wtedy, gdy `--preserve-smb-permissions` jest używany. Jeśli wartość jest równa true (domyślnie), właściciel pliku i Grupa są zachowywane w plikach do pobrania. Jeśli flaga jest ustawiona na wartość false, `--preserve-smb-permissions` nadal zachowuje listy ACL, ale właściciel i Grupa będą oparte na użytkowniku, na którym działa AzCopy.

**--Put-MD5**                             Utwórz skrót MD5 każdego pliku i Zapisz skrót jako właściwość Content-MD5 docelowego obiektu BLOB lub pliku. (Domyślnie skrót nie jest tworzony). Dostępne tylko podczas przekazywania.

**--rekursywnie**                            Poszukaj podkatalogów cyklicznie podczas przekazywania z lokalnego systemu plików.

**--S2S-Detect-Source-zmiana**           Sprawdź, czy po wyliczyć źródła uległo zmianie.

**--S2S-Handle-nieprawidłowy** ciąg Określa sposób obsługi nieprawidłowych kluczy metadanych. Dostępne opcje: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (domyślnie "ExcludeIfInvalid")

**--S2S-Preserve-dostęp-warstwa**             Zachowaj warstwę dostępu podczas kopiowania usługi do usługi. Zapoznaj się z [usługą Azure Blob Storage: warstwami dostępu gorąca, chłodna i archiwalna](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) , aby upewnić się, że docelowe konto magazynu obsługuje ustawienie Warstwa dostępu. W przypadkach, gdy ustawienie warstwy dostępu nie jest obsługiwane, użyj s2sPreserveAccessTier = false, aby obejść kopiowanie warstwy dostępu. (wartość domyślna to true)

**--S2S-Preserve-Properties**              Zachowaj pełne właściwości podczas kopiowania usługi do usługi. W przypadku AWS S3 i plików platformy Azure, które nie są pojedynczym źródłem plików, operacja listy nie zwraca pełnych właściwości obiektów i plików. Aby zachować pełne właściwości, AzCopy musi wysłać jedno dodatkowe żądanie dla każdego obiektu lub pliku. (wartość domyślna to true)

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--Cap-MB/s UInt32**      Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.

**--** format ciągu typu danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text". (domyślny "tekst")

## <a name="see-also"></a>Zobacz także

- [azcopy](storage-ref-azcopy.md)
