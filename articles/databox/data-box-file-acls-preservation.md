---
title: Zachowanie list ACL plików, atrybutów i sygnatur czasowych przy użyciu Azure Data Box
description: Listy ACL, sygnatury czasowe i atrybuty zachowywane podczas kopiowania danych za pośrednictwem protokołu SMB do Azure Data Box. Kopiowanie metadanych przy użyciu narzędzi do kopiowania danych systemu Windows i Linux.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950316"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Zachowanie list ACL plików, atrybutów i sygnatur czasowych przy użyciu Azure Data Box

Azure Data Box pozwala zachować listy kontroli dostępu (ACL), sygnatury czasowe i atrybuty plików podczas wysyłania danych na platformę Azure. W tym artykule opisano metadane, które można przenieść podczas kopiowania danych do urządzenie Data Box za pośrednictwem bloku komunikatów serwera (SMB) w celu przekazania go do Azure Files. 

Do kopiowania metadanych przy użyciu narzędzi do kopiowania danych systemu Windows i Linux służą określone kroki. Metadane nie są zachowywane podczas przesyłania danych do magazynu obiektów BLOB.

W tym artykule listy ACL, sygnatury czasowe i atrybuty plików, które są transferowane, są określane zbiorczo jako *metadane*.

## <a name="transferred-metadata"></a>Przesłane metadane

Następujące metadane są transferowane, gdy dane z urządzenie Data Box są przekazywane do Azure Files.

#### <a name="timestamps"></a>Znaczniki czasu

Następujące sygnatury czasowe są transferowane:
- CreationTime
- LastWriteTime

Następujący sygnatura czasowa nie jest przenoszona:
- LastAccessTime
  
#### <a name="file-attributes"></a>Atrybuty pliku

Atrybuty plików dla obu plików i katalogów są transferowane, o ile nie wskazano inaczej.

Następujące atrybuty plików są transferowane:
- FILE_ATTRIBUTE_READONLY (tylko plik)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (tylko katalog)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (tylko plik)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Następujące atrybuty pliku nie są transferowane:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Atrybuty tylko do odczytu w katalogach nie są transferowane.

#### <a name="acls"></a>ACL

Wszystkie listy ACL dla katalogów i plików, które można skopiować do urządzenie Data Box za pośrednictwem protokołu SMB, są kopiowane i przesyłane. Transfery obejmują zarówno arbitralne listy kontroli dostępu (DACL), jak i listy ACL systemu (SACL). W systemie Linux transferowane są tylko listy ACL systemu Windows NT.

Listy ACL nie są transferowane podczas kopiowania danych przez system plików sieciowych (NKTY przerwania) i używane do transferowania danych za pomocą usługi kopiowania danych. Usługa kopiowania danych odczytuje dane bezpośrednio z Twoich udziałów i nie może odczytywać list ACL.

Nawet jeśli narzędzie do kopiowania danych nie kopiuje list ACL, domyślne listy ACL katalogów i plików są przesyłane do Azure Files. Domyślne listy ACL mają uprawnienia dla wbudowanego konta administratora, konta SYSTEMowego i konta użytkownika udziału SMB służącego do instalowania i kopiowania danych w urządzenie Data Box.

Listy ACL zawierają deskryptory zabezpieczeń z następującymi właściwościami: list ACL, właściciel, Grupa, SACL.

Transfer list ACL jest domyślnie włączony. To ustawienie można wyłączyć w lokalnym interfejsie użytkownika sieci Web na urządzenie Data Box. Aby uzyskać więcej informacji, zobacz [Korzystanie z lokalnego interfejsu użytkownika sieci Web do administrowania urządzenie Data Box i Data Box Heavy](./data-box-local-web-ui-admin.md).

> [!NOTE]
> Pliki z listami ACL zawierającymi ciągi wpisów kontroli dostępu warunkowego (ACE) nie są kopiowane. Jest to znany problem. Aby obejść ten krok, skopiuj te pliki do udziału Azure Files ręcznie, instalując udział, a następnie używając narzędzia kopiowania, które obsługuje kopiowanie list ACL.

## <a name="copying-data-and-metadata"></a>Kopiowanie danych i metadanych

Aby przenieść listy ACL, sygnatury czasowe i atrybuty danych, wykonaj poniższe procedury, aby skopiować dane do urządzenie Data Box. 

### <a name="windows-data-copy-tool"></a>Narzędzie do kopiowania danych systemu Windows

Aby skopiować dane do urządzenie Data Box za pośrednictwem protokołu SMB, użyj narzędzia do kopiowania plików zgodnego z protokołem SMB, takiego jak `robocopy` . Następujące przykładowe polecenie kopiuje wszystkie pliki i katalogi, a następnie przesyła metadane wraz z danymi.

W przypadku korzystania `/copyall` z `/dcopy:DAT` opcji lub upewnij się, że wymagane uprawnienia operatora kopii zapasowych nie są wyłączone. Aby uzyskać więcej informacji, zobacz [Korzystanie z lokalnego interfejsu użytkownika sieci Web do administrowania urządzenie Data Box i Data Box Heavy](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

gdzie

|Opcja |Opis |
|------------------- | ----- |
|`/copyall` |Kopiuje wszystkie atrybuty.|
|`/e`      |Kopiuje podkatalogi, w tym puste katalogi.         |
|`/dcopy:DAT`  |Kopiuje dane, atrybuty i sygnatury czasowe. Uwaga: opcja/DCOPY: DAT musi być używana do transferowania `CreationTime` katalogów. |
|`/r:3`    |Określa 3 ponawianie prób w przypadku kopii zakończonych niepowodzeniem.         |
|`/w:60`   |Określa czas oczekiwania 60 sekund między ponownymi próbami.         |
|`/is`     |Zawiera te same pliki.         |
|`/nfl`    |Nie rejestruje nazw plików.         |
|`/ndl`    |Nie rejestruje nazw katalogów.        |
|`/np`     |Nie wyświetla postępu operacji kopiowania.         |
|`/MT:32 or 64`  |Używa wielowątkowości z 32 lub 64 wątków.           |
|`/fft`    |Skraca stopień szczegółowości sygnatur czasowych dla każdego systemu plików.        |
|`/log+:<LogFile>`  |Dołącza dane wyjściowe do istniejącego pliku dziennika.|

Aby uzyskać więcej informacji na temat tych `robocopy` parametrów, zobacz [Samouczek: kopiowanie danych do Azure Data Box za pośrednictwem protokołu SMB](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Narzędzie do kopiowania danych systemu Linux

Transferowanie metadanych w systemie Linux jest procesem dwuetapowym. Najpierw należy skopiować dane źródłowe za pomocą narzędzia takiego jak `rsync` , które nie kopiuje metadanych. Po skopiowaniu danych można skopiować metadane za pomocą narzędzia takiego jak `smbcacls` lub `cifsacl` . 

Następujące przykładowe polecenia wykonują pierwszy krok, kopiując dane przy użyciu `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie danych na urządzenie Azure Data Box Disk za pośrednictwem protokołu SMB](./data-box-deploy-copy-data.md)