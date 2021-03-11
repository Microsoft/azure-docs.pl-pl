---
title: Przywracanie do punktu w czasie dla blokowych obiektów BLOB
titleSuffix: Azure Storage
description: Przywracanie do określonego momentu dla blokowych obiektów BLOB zapewnia ochronę przed przypadkowym usunięciem lub uszkodzeniem przez umożliwienie przywrócenia konta magazynu do poprzedniego stanu w danym momencie.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: da869091fb1f7bf31a29ba1bc6db8c1c42254dc4
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618087"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Przywracanie do punktu w czasie dla blokowych obiektów BLOB

Przywracanie do punktu w czasie zapewnia ochronę przed przypadkowym usunięciem lub uszkodzeniem przez umożliwienie przywrócenia blokowych danych obiektów BLOB do wcześniejszego stanu. Przywracanie do punktu w czasie jest przydatne w scenariuszach, w których użytkownik lub aplikacja przypadkowo usuwa dane lub w przypadku, gdy błąd aplikacji uszkadza dane. Przywracanie do określonego momentu umożliwia również scenariuszom testowania, które wymagają przywrócenia zestawu danych do znanego stanu przed uruchomieniem dalszych testów.

Przywracanie do punktu w czasie jest obsługiwane w przypadku kont magazynu ogólnego przeznaczenia w wersji 2 tylko w warstwie wydajności standardowa. Tylko dane w warstwach dostępu gorąca i chłodna mogą być przywracane z przywróceniem do punktu w czasie.

Aby dowiedzieć się, jak włączyć przywracanie do punktu w czasie dla konta magazynu, zobacz [wykonywanie przywracania do punktu w czasie na danych blokowych obiektów BLOB](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Jak działa przywracanie do punktu w czasie

Aby włączyć przywracanie do określonego momentu, należy utworzyć zasady zarządzania dla konta magazynu i określić okres przechowywania. W okresie przechowywania można przywrócić blokowe obiekty blob ze stanu obecne do stanu w określonym punkcie czasu.

Aby zainicjować przywracanie do określonego momentu, wywołaj operację [Przywróć zakresy obiektów BLOB](/rest/api/storagerp/storageaccounts/restoreblobranges) i określ punkt przywracania w czasie UTC. Można określić zakresy lexicographical nazw kontenerów i obiektów BLOB do przywrócenia lub pominąć zakres, aby przywrócić wszystkie kontenery na koncie magazynu. Dla operacji przywracania obsługiwane są maksymalnie 10 zakresów lexicographical.

Usługa Azure Storage analizuje wszystkie zmiany wprowadzone w określonych obiektach Blob między żądanym punktem przywracania, określonym w polu czas UTC, a obecnym chwilą. Operacja przywracania jest niepodzielna, dlatego w całości przywraca wszystkie zmiany lub nie powiedzie się. Jeśli istnieją jakiekolwiek obiekty blob, których nie można przywrócić, operacja kończy się niepowodzeniem, a operacje odczytu i zapisu w kontenerach, których dotyczy problem, są wznawiane.

Na poniższym diagramie przedstawiono sposób, w jaki działa przywracanie do punktu w czasie. Co najmniej jeden kontener lub zakresy obiektów BLOB są przywracane do stanu *n* dni temu, gdzie *n* jest mniejsze niż lub równe okresowi przechowywania zdefiniowanym dla przywracania do określonego momentu. Efektem jest przywrócenie operacji zapisu i usuwania, które wystąpiły w okresie przechowywania.

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="Diagram pokazujący, jak punkt w czasie przywraca kontenery do poprzedniego stanu":::

Jednocześnie można uruchomić tylko jedną operację przywracania na koncie magazynu. Nie można anulować operacji przywracania, gdy jest w toku, ale w celu cofnięcia pierwszej operacji można wykonać drugą operację przywracania.

Operacja **przywracania zakresów obiektów BLOB** zwraca identyfikator przywracania, który jednoznacznie identyfikuje operację. Aby sprawdzić stan przywracania do punktu w czasie, wywołaj operację **Pobierz stan przywracania** z identyfikatorem przywracania zwróconym z operacji **przywracania zakresów obiektów BLOB** .

> [!IMPORTANT]
> Podczas wykonywania operacji przywracania usługa Azure Storage blokuje operacje na danych w obiektach Blob w zakresach przywracanych przez czas trwania operacji. Operacje odczytu, zapisu i usuwania są blokowane w lokalizacji podstawowej. Z tego powodu operacje, takie jak kontenery list w Azure Portal, mogą nie działać zgodnie z oczekiwaniami podczas operacji przywracania.
>
> Operacje odczytu z lokalizacji pomocniczej mogą być przetwarzane w trakcie operacji przywracania, jeśli konto magazynu ma replikację geograficzną.

> [!CAUTION]
> Przywracanie do punktu w czasie obsługuje przywracanie do operacji, które działały tylko dla blokowych obiektów BLOB. Nie można przywrócić żadnych operacji, które działały w kontenerach. Na przykład po usunięciu kontenera z konta magazynu przez wywołanie operacji [usuwania kontenera](/rest/api/storageservices/delete-container) nie można przywrócić tego kontenera z użyciem operacji przywracania do punktu w czasie. Zamiast usuwać cały kontener, Usuń pojedyncze obiekty blob, jeśli chcesz je później przywrócić.

### <a name="prerequisites-for-point-in-time-restore"></a>Wymagania wstępne dotyczące przywracania do punktu w czasie

Przywracanie do punktu w czasie wymaga włączenia następujących funkcji usługi Azure Storage, aby można było włączyć przywracanie do punktu w czasie:

- [Usuwanie nietrwałe](./soft-delete-blob-overview.md)
- [Źródło zmian](storage-blob-change-feed.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)

### <a name="retention-period-for-point-in-time-restore"></a>Okres przechowywania dla przywracania do punktu w czasie

Po włączeniu przywracania do określonego momentu dla konta magazynu należy określić okres przechowywania. Blokowe obiekty blob na koncie magazynu mogą zostać przywrócone w okresie przechowywania.

Okres przechowywania rozpoczyna się kilka minut po włączeniu przywracania do punktu w czasie. Należy pamiętać, że nie można przywrócić obiektów BLOB do stanu przed rozpoczęciem okresu przechowywania. Jeśli na przykład włączono funkcję przywracania do punktu w czasie od 1 maja i trwa okres utrzymywania 30 dni, w dniu 15 maja można przywrócić maksymalnie 15 dni. 1 czerwca można przywrócić dane z przedziału od do 30 dni.

Okres przechowywania dla przywracania do określonego momentu musi być co najmniej jeden dzień krótszy niż okres przechowywania określony dla operacji usuwania nietrwałego. Jeśli na przykład okres przechowywania nietrwałego usuwania ma wartość 7 dni, okres przechowywania przywracania do określonego momentu może wynosić od 1 do 6 dni.

> [!IMPORTANT]
> Czas potrzebny na przywrócenie zestawu danych zależy od liczby operacji zapisu i usuwania dokonanych w okresie przywracania. Na przykład konto z 1 000 000 obiektami o 3 000 obiektach dodane dziennie, a 1 000 obiektów, które zostały usunięte dziennie, będzie wymagało około dwóch godzin, aby przywrócić je do punktu 30 dni w przeszłości. Okres przechowywania i przywrócenie ponad 90 dni w przeszłości nie będą zalecane dla konta z tą szybkością zmiany.

### <a name="permissions-for-point-in-time-restore"></a>Uprawnienia do przywracania do punktu w czasie

Aby zainicjować operację przywracania, klient musi mieć uprawnienia do zapisu dla wszystkich kontenerów na koncie magazynu. Aby udzielić uprawnień do autoryzacji operacji przywracania za pomocą Azure Active Directory (Azure AD), przypisz rolę **współautor konta magazynu** do podmiotu zabezpieczeń na poziomie konta magazynu, grupy zasobów lub subskrypcji.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

Przywracanie do punktu w czasie dla blokowych obiektów BLOB ma następujące ograniczenia i znane problemy:

- Tylko blokowe obiekty blob w standardowym koncie magazynu ogólnego przeznaczenia w wersji 2 można przywrócić w ramach operacji przywracania do punktu w czasie. Nie są przywracane obiekty blob, stronicowe obiekty blob i blokowe obiekty blob w warstwie Premium. 
- Jeśli kontener został usunięty w okresie przechowywania, ten kontener nie zostanie przywrócony z operacją przywracania do punktu w czasie. Jeśli podjęto próbę przywrócenia zakresu obiektów blob, które zawierają obiekty blob w usuniętym kontenerze, operacja przywracania do punktu w czasie zakończy się niepowodzeniem. Aby dowiedzieć się więcej o ochronie kontenerów, zobacz [usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)](soft-delete-container-overview.md).
- Jeśli obiekt BLOB został przeniesiony między warstwami gorąca i chłodna w okresie między obecnym chwilą a punktem przywracania, obiekt BLOB zostanie przywrócony do poprzedniej warstwy. Przywracanie blokowych obiektów BLOB w warstwie archiwum nie jest obsługiwane. Na przykład jeśli obiekt blob z warstwy Gorąca został przeniesiony do warstwy Archiwum dwa dni temu, a operacja przywracania spowodowała jego przywrócenie do punktu sprzed trzech dni, obiekt blob nie zostanie przywrócony do warstwy Gorąca. Aby przywrócić zarchiwizowany obiekt BLOB, najpierw przenieś go z warstwy archiwum. Aby uzyskać więcej informacji, zobacz informacje o [rehydratacji danych obiektów blob z warstwy archiwum](storage-blob-rehydration.md).
- Blok, który został przekazany za pośrednictwem bloku [Put](/rest/api/storageservices/put-block) lub [Put z adresu URL](/rest/api/storageservices/put-block-from-url), ale nie został przekazany za pośrednictwem [listy bloków Put](/rest/api/storageservices/put-block-list), nie jest częścią obiektu BLOB i dlatego nie jest przywracany jako część operacji przywracania.
- Nie można przywrócić obiektu BLOB z aktywną dzierżawą. Jeśli obiekt BLOB z aktywną dzierżawą znajduje się w zakresie obiektów BLOB do przywrócenia, operacja przywracania zakończy się niepowodzeniem. Przerwij wszystkie aktywne dzierżawy przed zainicjowaniem operacji przywracania.
- Migawki nie są tworzone ani usuwane w ramach operacji przywracania. Tylko podstawowy obiekt BLOB zostanie przywrócony do poprzedniego stanu.
- Przywracanie Azure Data Lake Storage Gen2 płaskich i hierarchicznych przestrzeni nazw nie jest obsługiwane.

> [!IMPORTANT]
> Jeśli przywracasz blokowe obiekty blob do punktu, który jest wcześniejszy niż 22 września, 2020, zostaną zastosowane ograniczenia dotyczące przywracania do punktu w czasie. Firma Microsoft zaleca, aby wybrać punkt przywracania o wartości równej lub nowszej niż 22 września 2020, aby korzystać z ogólnie dostępnej funkcji przywracania do punktu w czasie.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Naliczanie opłat za przywracanie do punktu w czasie zależy od ilości danych przetworzonych w celu wykonania operacji przywracania. Ilość przetwarzanych danych opiera się na liczbie zmian, które wystąpiły między punktem przywracania a obecnym chwilą. Na przykład przy założeniu, że stosunkowo stała szybkość zmiany w celu zablokowania danych obiektów BLOB na koncie magazynu, operacja przywracania, która wraca z powrotem w czasie 1 dnia, będzie kosztować 1/10 ostatnich operacji przywracania, która wraca w czasie 10 dni.

Aby oszacować koszt operacji przywracania, zapoznaj się z dziennikiem źródła zmian, aby oszacować ilość danych zmodyfikowanych w okresie przywracania. Na przykład jeśli okres przechowywania dla kanału informacyjnego zmiany wynosi 30 dni, a rozmiar kanału informacyjnego zmiany wynosi 10 MB, a następnie przywracanie do punktu 10 dni wcześniej będzie kosztować około jednej trzeciej ceny na koncie LRS w tym regionie. Przywrócenie do punktu, który jest 27 dni wcześniej, będzie kosztowało około dziewięciu dziesiątych cen na liście.

Aby uzyskać więcej informacji na temat cen przywracania do punktu w czasie, zobacz [ceny blokowych obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Następne kroki

- [Wykonaj przywracanie do punktu w czasie dla danych blokowych obiektów BLOB](point-in-time-restore-manage.md)
- [Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage](storage-blob-change-feed.md)
- [Włączanie usuwania nietrwałego dla obiektów blob](./soft-delete-blob-enable.md)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)
