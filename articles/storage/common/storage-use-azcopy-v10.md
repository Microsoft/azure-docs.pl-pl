---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage przy użyciu narzędzia AzCopy w wersji 10 | Microsoft Docs
description: AzCopy to narzędzie wiersza polecenia, które umożliwia kopiowanie danych do, z lub między kontami magazynu. Ten artykuł zawiera informacje, które pomogą Ci pobrać narzędzie AzCopy, nawiązać połączenie z kontem magazynu, a następnie przenieść pliki.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34d3bd45d2c0bf0260a4f8524cff6f8ac03b746c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501723"
---
# <a name="get-started-with-azcopy"></a>Wprowadzenie do narzędzia AzCopy

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł zawiera informacje, które pomogą Ci pobrać narzędzie AzCopy, nawiązać połączenie z kontem magazynu, a następnie przenieść pliki.

> [!NOTE]
> AzCopy **V10** to obecnie obsługiwana wersja programu AzCopy.
>
> Jeśli musisz użyć poprzedniej wersji programu AzCopy, zobacz sekcję Korzystanie z poprzedniej wersji programu [AzCopy](#previous-version) w tym artykule.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Pobieranie narzędzia AzCopy

Najpierw pobierz plik wykonywalny programu AzCopy w wersji 10 do dowolnego katalogu na komputerze. AzCopy V10 to po prostu plik wykonywalny, więc nie ma nic do zainstalowania.

- [64-bitowy system Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [32-bitowy system Windows](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Te pliki są kompresowane jako plik zip (systemy Windows i Mac) lub plik tar (Linux). Aby pobrać i zdekompresować plik tar w systemie Linux, zapoznaj się z dokumentacją dystrybucji systemu Linux.

> [!NOTE]
> Jeśli chcesz skopiować dane do i z usługi [Azure Table Storage,](../tables/table-storage-overview.md) zainstaluj narzędzie [AzCopy w wersji 7.3.](https://aka.ms/downloadazcopynet)

## <a name="run-azcopy"></a>Uruchamianie narzędzia AzCopy

Dla wygody rozważ dodanie lokalizacji katalogu pliku wykonywalnego narzędzia AzCopy do ścieżki systemowej w celu ułatwienia użytkowania. W ten sposób można wpisać `azcopy` z dowolnego katalogu w systemie.

Jeśli nie chcesz dodawać katalogu programu AzCopy do ścieżki, musisz zmienić katalogi na lokalizację pliku wykonywalnego i wpisać polecenie lub Windows PowerShell wiersza `azcopy` `.\azcopy` polecenia.

Jako właściciel konta usługi Azure Storage nie masz automatycznie przypisanych uprawnień dostępu do danych. Zanim będzie można wykonać jakąś znaczącą decyzje za pomocą programu AzCopy, musisz zdecydować, jak poświadczeń autoryzacji udostępnić usłudze magazynu. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Autoryzowanie programu AzCopy

Poświadczenia autoryzacji można podać przy użyciu Azure Active Directory (AD) lub tokenu sygnatury dostępu współdzielonego (SAS).

Skorzystaj z tej tabeli jako przewodnika:

| Typ magazynu | Obecnie obsługiwana metoda autoryzacji |
|--|--|
|**Blob Storage** | Usługa Azure AD i sygnatura dostępu współdzielonego |
|**Blob Storage (hierarchiczna przestrzeń nazw)** | Usługa Azure AD i sygnatura dostępu współdzielonego |
|**File Storage** | Tylko sygnatura dostępu współdzielonego |

#### <a name="option-1-use-azure-active-directory"></a>Opcja 1. Użyj Azure Active Directory

Ta opcja jest dostępna tylko dla usługi Blob Storage. Za pomocą Azure Active Directory można podać poświadczenia raz, zamiast dołączać token SAS do każdego polecenia.  

> [!NOTE]
> Jeśli w bieżącej wersji zamierzasz skopiować obiekty blob między kontami magazynu, musisz dołączyć token SAS do każdego źródłowego adresu URL. Token sygnatury dostępu współdzielonego można pominąć tylko z docelowego adresu URL. Aby uzyskać przykłady, [zobacz Kopiowanie obiektów blob między kontami magazynu.](#transfer-data)

Aby autoryzować dostęp przy użyciu usługi Azure AD, zobacz Autoryzowanie dostępu do obiektów blob za pomocą narzędzia [AzCopy i Azure Active Directory (Azure AD).](storage-use-azcopy-authorize-azure-active-directory.md)

#### <a name="option-2-use-a-sas-token"></a>Opcja 2. Używanie tokenu SAS

Token SAS można dołączyć do każdego źródłowego lub docelowego adresu URL, który jest używanych w poleceniach programu AzCopy.

To przykładowe polecenie rekursywnie kopiuje dane z katalogu lokalnego do kontenera obiektów blob. Na końcu adresu URL kontenera jest dołączany fikcyjny token SAS.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Aby dowiedzieć się więcej na temat tokenów SAS i sposobu ich uzyskiwania, zobacz [Using shared access signatures (SAS) (Używanie](./storage-sas-overview.md)sygnatur dostępu współdzielonego).

> [!NOTE]
> Ustawienie [Wymagany bezpieczny transfer](storage-require-secure-transfer.md) konta magazynu określa, czy połączenie z kontem magazynu jest zabezpieczone przy użyciu Transport Layer Security (TLS). To ustawienie jest domyślnie włączone.   

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Transfer danych

Po autoryzowaniu tożsamości lub uzyskaniu tokenu SAS możesz rozpocząć transfer danych.

Aby znaleźć przykładowe polecenia, zobacz dowolny z tych artykułów.

| Usługa | Artykuł |
|--------|-----------|
|Azure Blob Storage|[Przekazywanie plików do Azure Blob Storage](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Storage|[Pobieranie obiektów blob z Azure Blob Storage](storage-use-azcopy-blobs-download.md)|
|Azure Blob Storage|[Kopiowanie obiektów blob między kontami usługi Azure Storage](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Storage|[Synchronizowanie z Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)|
|Amazon S3|[Kopiowanie danych z usługi Amazon S3 do usługi Azure Storage](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Kopiowanie danych z usługi Google Cloud Storage do usługi Azure Storage (wersja zapoznawcza)](storage-use-azcopy-google-cloud.md)|
|Azure Stack magazynu|[Transferowanie danych za pomocą programu AzCopy Azure Stack magazynu](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>Uzyskiwanie pomocy dla poleceń

Aby wyświetlić listę poleceń, wpisz , `azcopy -h` a następnie naciśnij klawisz ENTER.

Aby dowiedzieć się więcej o konkretnym poleceniu, po prostu dołącz nazwę polecenia (na przykład: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Pomoc w tekście](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>Lista poleceń

W poniższej tabeli wymieniono wszystkie polecenia programu AzCopy w wersji 10. Każde polecenie łączy się z artykułem referencyjnym. 

|Polecenie|Opis|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|Uruchamia test porównawczy wydajności, przesyłając lub pobierając dane testowe do lub z określonej lokalizacji.|
|[azcopy copy](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|Kopiuje dane źródłowe do lokalizacji docelowej|
|[azcopy doc](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Generuje dokumentację narzędzia w formacie Markdown.|
|[azcopy env](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|Przedstawia zmienne środowiskowe, które mogą konfigurować zachowanie programu AzCopy.|
|[azcopy jobs](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|Podgrupy związane z zarządzaniem zadaniami.|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|Usuń wszystkie pliki dziennika i planu dla wszystkich zadań.|
|[azcopy jobs list](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|Wyświetla informacje o wszystkich zadaniach.|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|Usuń wszystkie pliki skojarzone z danym identyfikatorem zadania.|
|[azcopy jobs resume](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|Wznawia istniejące zadanie o danym identyfikatorze zadania.|
|[azcopy jobs show](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|Przedstawia szczegółowe informacje dotyczące danego identyfikatora zadania.|
|[azcopy load](storage-ref-azcopy-load.md)|Podgrupy związane z przesyłaniem danych w określonych formatach.|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|Transferuje dane lokalne do kontenera i zapisuje je w formacie Avere Cloud FileSystem (CLFS) firmy Microsoft.|
|[azcopy list](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|Wyświetla listę jednostek w danym zasobie.|
|[azcopy login](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Zaloguj się, aby Azure Active Directory uzyskać dostęp do zasobów usługi Azure Storage.|
|[azcopy logout](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|Wyloguje użytkownika i zakończy dostęp do zasobów usługi Azure Storage.|
|[azcopy make](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|Tworzy kontener lub udział plików.|
|[azcopy remove](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Usuwanie obiektów blob lub plików z konta usługi Azure Storage.|
|[azcopy sync](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|Replikuje lokalizację źródłową do lokalizacji docelowej.|

## <a name="use-in-a-script"></a>Używanie w skrypcie

#### <a name="obtain-a-static-download-link"></a>Uzyskiwanie statycznego linku pobierania

Z czasem link pobierania do programu [AzCopy](#download-and-install-azcopy) będzie wskaże nowe wersje programu AzCopy. Jeśli skrypt pobierze program AzCopy, skrypt może przestać działać, jeśli nowsza wersja programu AzCopy zmodyfikuje funkcje, od których zależy skrypt.

Aby uniknąć tych problemów, uzyskaj statyczny (niezmieniony) link do bieżącej wersji programu AzCopy. Dzięki temu skrypt pobiera dokładnie tę samą wersję programu AzCopy przy każdym jego uruchamianiu.

Aby uzyskać link, uruchom to polecenie:

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> W przypadku systemu Linux polecenie usuwa folder najwyższego poziomu, który zawiera nazwę wersji, a zamiast tego wyodrębnia plik binarny `--strip-components=1` `tar` bezpośrednio do bieżącego folderu. Dzięki temu skrypt można zaktualizować przy użyciu nowej wersji programu `azcopy` , aktualizując tylko adres `wget` URL.

Adres URL zostanie wyświetlony w danych wyjściowych tego polecenia. Skrypt może następnie pobrać program AzCopy przy użyciu tego adresu URL.

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Znaki specjalne ucieczki w tokenach SAS

W plikach wsadowych, które mają rozszerzenie, należy odsunąć znaki `.cmd` `%` ucieczki, które pojawiają się w tokenach SAS. Możesz to zrobić, dodając dodatkowy znak `%` obok istniejących znaków w ciągu `%` tokenu SYGNATURY dostępu współdzielonego.

#### <a name="run-scripts-by-using-jenkins"></a>Uruchamianie skryptów przy użyciu narzędzia Jenkins

Jeśli planujesz używać serwera [Jenkins](https://jenkins.io/) do uruchamiania skryptów, pamiętaj, aby umieścić następujące polecenie na początku skryptu.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Używanie w programie Eksplorator usługi Azure Storage

[Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) używa programu AzCopy do wykonywania wszystkich operacji transferu danych. Możesz użyć [Eksplorator usługi Storage,](https://azure.microsoft.com/features/storage-explorer/) jeśli chcesz korzystać z zalet wydajności narzędzia AzCopy, ale wolisz używać graficznego interfejsu użytkownika, a nie wiersza polecenia, aby wchodzić w interakcje z plikami.

Eksplorator usługi Storage używa klucza konta do wykonywania operacji, więc po zalogowaniu się do usługi Eksplorator usługi Storage nie trzeba poświadczeń autoryzacji dodatkowe.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Konfigurowanie, optymalizowanie i naprawianie

Zobacz dowolny z następujących zasobów:

- [Ustawienia konfiguracji programu AzCopy](storage-ref-azcopy-configuration-settings.md)

- [Optymalizowanie wydajności programu AzCopy](storage-use-azcopy-optimize.md)

- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Używanie poprzedniej wersji

Jeśli musisz użyć poprzedniej wersji programu AzCopy, skorzystaj z jednego z następujących linków:

- [Narzędzie AzCopy w systemie Windows (wersja 8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy w systemie Linux (wersja 7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania, problemy lub ogólną opinię, prześlij je na [stronie usługi GitHub.](https://github.com/Azure/azure-storage-azcopy)
