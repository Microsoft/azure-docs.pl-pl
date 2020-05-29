---
title: Przywracanie do punktu w czasie dla blokowych obiektów BLOB (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Przywracanie do określonego momentu dla blokowych obiektów BLOB zapewnia ochronę przed przypadkowym usunięciem lub uszkodzeniem przez umożliwienie przywrócenia konta magazynu do poprzedniego stanu w danym momencie.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 38f6cfef60cf3bfe66742cba204d74db1c22ca77
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84169291"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Przywracanie do punktu w czasie dla blokowych obiektów BLOB (wersja zapoznawcza)

Przywracanie do punktu w czasie zapewnia ochronę przed przypadkowym usunięciem lub uszkodzeniem przez umożliwienie przywrócenia blokowych danych obiektów BLOB do wcześniejszego stanu. Przywracanie do punktu w czasie jest przydatne w scenariuszach, w których użytkownik lub aplikacja przypadkowo usuwa dane lub w przypadku, gdy błąd aplikacji uszkadza dane. Przywracanie do określonego momentu umożliwia również scenariuszom testowania, które wymagają przywrócenia zestawu danych do znanego stanu przed uruchomieniem dalszych testów.

Aby dowiedzieć się, jak włączyć przywracanie do punktu w czasie dla konta magazynu, zobacz temat [Włączanie i zarządzanie przywracaniem do punktu w czasie dla blokowych obiektów BLOB (wersja zapoznawcza)](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Jak działa przywracanie do punktu w czasie

Aby włączyć przywracanie do określonego momentu, należy utworzyć zasady zarządzania dla konta magazynu i określić okres przechowywania. W okresie przechowywania można przywrócić blokowe obiekty blob ze stanu obecne do stanu w określonym punkcie czasu.

Aby zainicjować przywracanie do określonego momentu, wywołaj operację [Przywróć zakresy obiektów BLOB](/rest/api/storagerp/storageaccounts/restoreblobranges) i określ punkt przywracania w czasie UTC. Można określić zakresy lexicographical nazw kontenerów i obiektów BLOB do przywrócenia lub pominąć zakres, aby przywrócić wszystkie kontenery na koncie magazynu. Dla operacji przywracania obsługiwane są maksymalnie 10 zakresów lexicographical.

Usługa Azure Storage analizuje wszystkie zmiany wprowadzone w określonych obiektach Blob między żądanym punktem przywracania, określonym w polu czas UTC, a obecnym chwilą. Operacja przywracania jest niepodzielna, dlatego w całości przywraca wszystkie zmiany lub nie powiedzie się. Jeśli istnieją jakiekolwiek obiekty blob, których nie można przywrócić, operacja kończy się niepowodzeniem, a operacje odczytu i zapisu w kontenerach, których dotyczy problem, są wznawiane.

Jednocześnie można uruchomić tylko jedną operację przywracania na koncie magazynu. Nie można anulować operacji przywracania, gdy jest w toku, ale w celu cofnięcia pierwszej operacji można wykonać drugą operację przywracania.

Operacja **przywracania zakresów obiektów BLOB** zwraca identyfikator przywracania, który jednoznacznie identyfikuje operację. Aby sprawdzić stan przywracania do punktu w czasie, wywołaj operację **Pobierz stan przywracania** z identyfikatorem przywracania zwróconym z operacji **przywracania zakresów obiektów BLOB** .

Należy pamiętać o następujących ograniczeniach dotyczących operacji przywracania:

- Blok, który został przekazany za pośrednictwem bloku [Put](/rest/api/storageservices/put-block) lub [Put z adresu URL](/rest/api/storageservices/put-block-from-url), ale nie został przekazany za pośrednictwem [listy bloków Put](/rest/api/storageservices/put-block-list), nie jest częścią obiektu BLOB i dlatego nie jest przywracany jako część operacji przywracania.
- Nie można przywrócić obiektu BLOB z aktywną dzierżawą. Jeśli obiekt BLOB z aktywną dzierżawą znajduje się w zakresie obiektów BLOB do przywrócenia, operacja przywracania zakończy się niepowodzeniem.
- Migawki nie są tworzone ani usuwane w ramach operacji przywracania. Tylko podstawowy obiekt BLOB zostanie przywrócony do poprzedniego stanu.
- Jeśli obiekt BLOB został przeniesiony między warstwami gorąca i chłodna w okresie między obecnym chwilą a punktem przywracania, obiekt BLOB zostanie przywrócony do poprzedniej warstwy. Jednak obiekt BLOB przeniesiony do warstwy archiwum nie zostanie przywrócony.

> [!IMPORTANT]
> Podczas wykonywania operacji przywracania usługa Azure Storage blokuje operacje na danych w obiektach Blob w zakresach przywracanych przez czas trwania operacji. Operacje odczytu, zapisu i usuwania są blokowane w lokalizacji podstawowej. Z tego powodu operacje, takie jak kontenery list w Azure Portal, mogą nie działać zgodnie z oczekiwaniami podczas operacji przywracania.
>
> Operacje odczytu z lokalizacji pomocniczej mogą być przetwarzane w trakcie operacji przywracania, jeśli konto magazynu ma replikację geograficzną.

> [!CAUTION]
> Przywracanie do punktu w czasie obsługuje operacje przywracania tylko dla blokowych obiektów BLOB. Nie można przywrócić operacji na kontenerach. W przypadku usunięcia kontenera z konta magazynu przez wywołanie operacji [usuwania kontenera](/rest/api/storageservices/delete-container) w podglądzie przywracania do punktu w czasie nie można przywrócić tego kontenera przy użyciu operacji przywracania. W trakcie okresu zapoznawczego zamiast usuwania kontenera Usuń pojedyncze obiekty blob, jeśli chcesz je przywrócić.

### <a name="prerequisites-for-point-in-time-restore"></a>Wymagania wstępne dotyczące przywracania do punktu w czasie

Przywracanie do punktu w czasie wymaga włączenia następujących funkcji usługi Azure Storage:

- [Usuwanie nietrwałe](soft-delete-overview.md)
- [Źródło zmian (wersja zapoznawcza)](storage-blob-change-feed.md)
- [Przechowywanie wersji obiektów BLOB (wersja zapoznawcza)](versioning-overview.md)

Te funkcje należy włączyć dla konta magazynu przed włączeniem przywracania do punktu w czasie. Pamiętaj, aby zarejestrować się w celu uzyskania źródła zmian i wersji zapoznawczych obiektów BLOB przed ich włączeniem.

### <a name="retention-period-for-point-in-time-restore"></a>Okres przechowywania dla przywracania do punktu w czasie

Po włączeniu przywracania do określonego momentu dla konta magazynu należy określić okres przechowywania. Blokowe obiekty blob na koncie magazynu mogą zostać przywrócone w okresie przechowywania.

Okres przechowywania rozpoczyna się po włączeniu przywracania do punktu w czasie. Należy pamiętać, że nie można przywrócić obiektów BLOB do stanu przed rozpoczęciem okresu przechowywania. Jeśli na przykład włączono funkcję przywracania do punktu w czasie od 1 maja i trwa okres utrzymywania 30 dni, w dniu 15 maja można przywrócić maksymalnie 15 dni. 1 czerwca można przywrócić dane z przedziału od do 30 dni.

Okres przechowywania dla przywracania do określonego momentu musi być co najmniej jeden dzień krótszy niż okres przechowywania określony dla operacji usuwania nietrwałego. Jeśli na przykład okres przechowywania nietrwałego usuwania ma wartość 7 dni, okres przechowywania przywracania do określonego momentu może wynosić od 1 do 6 dni.

### <a name="permissions-for-point-in-time-restore"></a>Uprawnienia do przywracania do punktu w czasie

Aby zainicjować operację przywracania, klient musi mieć uprawnienia do zapisu dla wszystkich kontenerów na koncie magazynu. Aby udzielić uprawnień do autoryzacji operacji przywracania za pomocą Azure Active Directory (Azure AD), przypisz rolę **współautor konta magazynu** do podmiotu zabezpieczeń na poziomie konta magazynu, grupy zasobów lub subskrypcji.

## <a name="about-the-preview"></a>Informacje o wersji zapoznawczej

Przywracanie do punktu w czasie jest obsługiwane tylko dla kont magazynu ogólnego przeznaczenia w wersji 2. Tylko dane w warstwach dostępu gorąca i chłodna mogą być przywracane z przywróceniem do punktu w czasie.

Poniższe regiony obsługują przywracanie do punktu w czasie w wersji zapoznawczej:

- Kanada Środkowa
- Kanada Wschodnia
- Francja Środkowa

Wersja zapoznawcza obejmuje następujące ograniczenia:

- Przywracanie blokowych obiektów BLOB w warstwie Premium nie jest obsługiwane.
- Przywracanie obiektów BLOB w warstwie archiwum nie jest obsługiwane. Na przykład jeśli obiekt BLOB w warstwie gorąca został przeniesiony do warstwy archiwum dwa dni temu, a operacja przywracania zostanie przywrócona do punktu trzy dni temu, obiekt BLOB nie zostanie przywrócony do warstwy gorąca.
- Przywracanie Azure Data Lake Storage Gen2 płaskich i hierarchicznych przestrzeni nazw nie jest obsługiwane.
- Przywracanie kont magazynu przy użyciu kluczy dostarczonych przez klienta nie jest obsługiwane.

> [!IMPORTANT]
> Wersja zapoznawcza przywracania do punktu w czasie jest przeznaczona wyłącznie do użytku w trybie nieprodukcyjnym. Umowy dotyczące poziomu usług produkcyjnych (umowy SLA) nie są obecnie dostępne.

### <a name="register-for-the-preview"></a>Zarejestruj się w wersji zapoznawczej

Aby zarejestrować się w celu korzystania z wersji zapoznawczej, uruchom następujące polecenia w Azure PowerShell:

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="check-registration-status"></a>Sprawdź stan rejestracji

Aby sprawdzić stan rejestracji, uruchom następujące polecenia:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed
```

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Naliczanie opłat za przywracanie do punktu w czasie zależy od ilości danych przetworzonych w celu wykonania operacji przywracania. Ilość przetwarzanych danych opiera się na liczbie zmian, które wystąpiły między punktem przywracania a obecnym chwilą. Na przykład przy założeniu, że stosunkowo stała szybkość zmiany w celu zablokowania danych obiektów BLOB na koncie magazynu, operacja przywracania, która wraca z powrotem w czasie 1 dnia, będzie kosztować 1/10 ostatnich operacji przywracania, która wraca w czasie 10 dni.

Aby oszacować koszt operacji przywracania, zapoznaj się z dziennikiem źródła zmian, aby oszacować ilość danych zmodyfikowanych w okresie przywracania. Na przykład jeśli okres przechowywania dla kanału informacyjnego zmiany wynosi 30 dni, a rozmiar kanału informacyjnego zmiany wynosi 10 MB, a następnie przywracanie do punktu 10 dni wcześniej będzie kosztować około jednej trzeciej ceny na koncie LRS w tym regionie. Przywrócenie do punktu, który jest 27 dni wcześniej, będzie kosztowało około dziewięciu dziesiątych cen na liście.

Aby uzyskać więcej informacji na temat cen przywracania do punktu w czasie, zobacz [ceny blokowych obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="ask-questions-or-provide-feedback"></a>Zadawaj pytania lub przekazanie opinii

Aby zadawać pytania dotyczące podglądu przywracania do punktu w czasie lub aby przekazać opinię, skontaktuj się z firmą Microsoft pod adresem pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>Następne kroki

- [Włącz przywracanie do punktu w czasie i zarządzaj nimi w przypadku blokowych obiektów BLOB (wersja zapoznawcza)](point-in-time-restore-manage.md)
- [Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-change-feed.md)
- [Włącz usuwanie nietrwałe dla obiektów BLOB](soft-delete-enable.md)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)
