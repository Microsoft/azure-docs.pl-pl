---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą AzCopy v10 | Microsoft Docs
description: AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować dane do, z lub między kontami magazynu. Ten artykuł zawiera informacje, które pomogą Ci pobrać narzędzie AzCopy, nawiązać połączenie z kontem magazynu, a następnie przenieść pliki.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 826effb152d3f069e0a7c5b8e169f7083117ed22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494499"
---
# <a name="get-started-with-azcopy"></a>Wprowadzenie do narzędzia AzCopy

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł zawiera informacje, które pomogą Ci pobrać narzędzie AzCopy, nawiązać połączenie z kontem magazynu, a następnie przenieść pliki.

> [!NOTE]
> AzCopy **v10** jest obecnie obsługiwaną wersją AzCopy.
>
> Jeśli potrzebujesz użyć poprzedniej wersji programu AzCopy, zobacz sekcję [Używanie poprzedniej wersji programu AzCopy](#previous-version) w tym artykule.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Pobieranie narzędzia AzCopy

Najpierw Pobierz plik wykonywalny AzCopy v10 do dowolnego katalogu na komputerze. AzCopy v10 to tylko plik wykonywalny, więc nie ma nic do zainstalowania.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86 — 64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Te pliki są kompresowane jako plik zip (Windows i Mac) lub plik tar (Linux). Aby pobrać i zdekompresować plik tar w systemie Linux, zapoznaj się z dokumentacją dystrybucji systemu Linux.

> [!NOTE]
> Jeśli chcesz skopiować dane do i z usługi [Azure Table Storage](../tables/table-storage-overview.md) , zainstaluj program [AzCopy w wersji 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Uruchamianie narzędzia AzCopy

Dla wygody rozważ dodanie lokalizacji katalogu pliku wykonywalnego narzędzia AzCopy do ścieżki systemowej w celu ułatwienia użytkowania. W ten sposób można wpisać `azcopy` z dowolnego katalogu w systemie.

Jeśli nie dodasz katalogu AzCopy do ścieżki, trzeba będzie zmienić katalogi do lokalizacji pliku wykonywalnego AzCopy i wpisać `azcopy` lub w wierszach `.\azcopy` polecenia programu Windows PowerShell.

Aby wyświetlić listę poleceń, wpisz, `azcopy -h` a następnie naciśnij klawisz ENTER.

Aby dowiedzieć się więcej na temat konkretnego polecenia, należy dołączyć nazwę polecenia (na przykład: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Wbudowana pomoc](media/storage-use-azcopy-v10/azcopy-inline-help.png)


Aby znaleźć szczegółową dokumentację referencyjną dla każdego polecenia i parametru polecenia, zobacz [AzCopy](storage-ref-azcopy.md)

> [!NOTE] 
> Jako właściciel konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych. Przed wprowadzeniem jakichkolwiek istotnych informacji w usłudze AzCopy należy określić sposób dostarczania poświadczeń autoryzacji do usługi magazynu. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Autoryzuj AzCopy

Poświadczenia autoryzacji można podawać przy użyciu Azure Active Directory (AD) lub tokenu sygnatury dostępu współdzielonego (SAS).

Użyj tej tabeli jako przewodnika:

| Typ magazynu | Obecnie obsługiwana metoda autoryzacji |
|--|--|
|**Blob Storage** | Usługa Azure AD i sygnatura dostępu współdzielonego |
|**BLOB Storage (hierarchiczna przestrzeń nazw)** | Usługa Azure AD i sygnatura dostępu współdzielonego |
|**File Storage** | Tylko sygnatura dostępu współdzielonego |

#### <a name="option-1-use-azure-active-directory"></a>Opcja 1: Użyj Azure Active Directory

Ta opcja jest dostępna tylko w przypadku usługi BLOB Storage. Za pomocą Azure Active Directory można podać poświadczenia jeden raz zamiast konieczności dołączania tokenu sygnatury dostępu współdzielonego do każdego polecenia.  

> [!NOTE]
> W bieżącej wersji, jeśli planujesz skopiować obiekty blob między kontami magazynu, musisz dołączyć token sygnatury dostępu współdzielonego do każdego źródłowego adresu URL. Token sygnatury dostępu współdzielonego można pominąć tylko z docelowego adresu URL. Aby zapoznać się z przykładami, zobacz [Kopiuj obiekty blob między kontami magazynu](#transfer-data).

Aby autoryzować dostęp za pomocą usługi Azure AD, zobacz temat [Autoryzuj dostęp do obiektów BLOB za pomocą AzCopy i Azure Active Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Opcja 2: używanie tokenu SAS

Do każdego źródłowego lub docelowego adresu URL, który będzie używany w poleceniach AzCopy, można dołączyć token sygnatury dostępu współdzielonego.

To przykładowe polecenie cyklicznie kopiuje dane z katalogu lokalnego do kontenera obiektów BLOB. Fikcyjny token sygnatury dostępu współdzielonego jest dołączany na końcu adresu URL kontenera.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Aby dowiedzieć się więcej o tokenach SAS i sposobach ich uzyskania, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](./storage-sas-overview.md).

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Transfer danych

Po autoryzowaniu tożsamości lub uzyskaniu tokenu SAS można rozpocząć transfer danych.

> [!NOTE]
> Ustawienie [wymagane przez bezpieczny transfer](storage-require-secure-transfer.md) dla konta magazynu określa, czy połączenie z kontem magazynu jest zabezpieczone za pomocą usługi Transport Layer Security (TLS). To ustawienie jest domyślnie włączone.   

Aby znaleźć przykładowe polecenia, zapoznaj się z dowolnym z tych artykułów.

| Usługa | Artykuł |
|--------|-----------|
|Azure Blob Storage |[Przekazywanie plików do usługi Azure Blob Storage](storage-use-azcopy-blobs-upload.md)<br><br>[Pobieranie obiektów blob z usługi Azure Blob Storage](storage-use-azcopy-blobs-download.md)<br><br>[Kopiowanie obiektów BLOB między kontami usługi Azure Storage](storage-use-azcopy-blobs-copy.md)<br><br>[Synchronizuj z usługą Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)|
|Amazon S3|[Kopiowanie danych z usług Amazon S3 do usługi Azure Storage](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Kopiowanie danych z magazynu Google Cloud Storage do usługi Azure Storage (wersja zapoznawcza)](storage-use-azcopy-google-cloud.md)|
|Magazyn Azure Stack|[Transferowanie danych za pomocą AzCopy i magazynu Azure Stack](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>Używanie w skrypcie

#### <a name="obtain-a-static-download-link"></a>Uzyskaj statyczny link pobierania

W miarę upływu czasu [link pobierania](#download-and-install-azcopy) AzCopy będzie wskazywał nowe wersje AzCopy. Jeśli skrypt pobiera AzCopy, skrypt może przestać działać, jeśli nowsza wersja AzCopy modyfikuje funkcje, od których zależy skrypt.

Aby uniknąć tych problemów, Uzyskaj statyczny (niezmieniony) link do bieżącej wersji programu AzCopy. Dzięki temu skrypt pobiera tę samą dokładną wersję AzCopy przy każdym uruchomieniu.

Aby uzyskać link, uruchom następujące polecenie:

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> W przypadku systemu Linux `--strip-components=1` `tar` polecenie usuwa folder najwyższego poziomu, który zawiera nazwę wersji, a zamiast tego wyodrębnia plik binarny bezpośrednio do bieżącego folderu. Umożliwia to zaktualizowanie skryptu przy użyciu nowej wersji programu `azcopy` przez zaktualizowanie `wget` adresu URL.

W danych wyjściowych tego polecenia zostanie wyświetlony adres URL. Skrypt może następnie pobrać AzCopy przy użyciu tego adresu URL.

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Znaki specjalne ucieczki w tokenach SAS

W plikach wsadowych, które mają `.cmd` rozszerzenie, musisz wprowadzić `%` znaki, które pojawiają się w tokenach sygnatury dostępu współdzielonego. Można to zrobić, dodając znak dodatkowy `%` obok istniejących `%` znaków w ciągu tokenu sygnatury dostępu współdzielonego.

#### <a name="run-scripts-by-using-jenkins"></a>Uruchamianie skryptów przy użyciu Jenkins

Jeśli planujesz używać [Jenkins](https://jenkins.io/) do uruchamiania skryptów, pamiętaj, aby umieścić następujące polecenie na początku skryptu.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Użyj w Eksplorator usługi Azure Storage

[Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) używa AzCopy do wykonywania wszystkich operacji transferu danych. [Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) można użyć, jeśli chcesz wykorzystać zalety wydajności AzCopy, ale wolisz używać graficznego interfejsu użytkownika, a nie wiersza polecenia, aby pracować z plikami.

Eksplorator usługi Storage używa klucza konta do wykonywania operacji, więc po zalogowaniu się do Eksplorator usługi Storage nie trzeba podawać dodatkowych poświadczeń autoryzacji.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Konfigurowanie, optymalizowanie i rozwiązywanie

Zobacz [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Użyj poprzedniej wersji

Jeśli potrzebujesz użyć poprzedniej wersji programu AzCopy, zobacz następujące linki:

- [Narzędzie AzCopy w systemie Windows (wersja 8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy w systemie Linux (wersji 7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania, problemy lub ogólne informacje zwrotne, prześlij je [na](https://github.com/Azure/azure-storage-azcopy) stronę usługi GitHub.
