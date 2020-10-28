---
title: Eksplorator usługi Azure Storage Przewodnik po wersji obiektów BLOB | Microsoft Docs
description: Wskazówki dotyczące obsługi wersji obiektów BLOB dla Eksplorator usługi Azure Storage
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783831"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Przewodnik dotyczący obsługi wersji obiektów BLOB Eksplorator usługi Azure Storage

Eksplorator usługi Microsoft Azure Storage zapewnia łatwy dostęp do wersji obiektów blob i zarządzanie nimi. Ten przewodnik pomoże Ci zrozumieć, jak działa wersja obiektów BLOB w Eksplorator usługi Storage. Przed kontynuowaniem zaleca się zapoznanie się z informacjami na temat [przechowywania wersji obiektów BLOB](../blobs/versioning-overview.md).

## <a name="terminology"></a>Terminologia

Ta sekcja zawiera definicje ułatwiające zrozumienie ich użycia w tym artykule.

- Usuwanie nietrwałe: alternatywna funkcja automatycznej ochrony danych. Więcej informacji na temat usuwania nietrwałego można znaleźć [tutaj](../blobs/soft-delete-blob-overview.md).
- Aktywny obiekt BLOB: w stanie aktywnym zostanie utworzona wersja obiektu BLOB lub obiektu BLOB. Operacje można wykonywać tylko na obiektach Blob lub wersjach obiektów BLOB w stanie aktywnym.
- Obiekt BLOB usunięty z niewygładzony: obiekt BLOB lub wersja obiektu BLOB oznaczona jako nietrwała. Nietrwałe usunięte obiekty blob są przechowywane tylko dla swojego okresu przechowywania.
- Wersja obiektu BLOB: obiekt BLOB utworzony z włączoną obsługą wersji obiektów BLOB. Każda wersja obiektu BLOB jest skojarzona z IDENTYFIKATORem wersji.
- Bieżąca wersja: wersja obiektu BLOB oznaczona jako bieżąca wersja.
- Poprzednia wersja: wersja obiektu BLOB, która nie jest bieżącą wersją.
- Obiekt BLOB spoza wersji: obiekt BLOB utworzony z wyłączonymi wersjami obiektów BLOB. Obiekt BLOB spoza wersji nie ma identyfikatora wersji.

## <a name="view-blob-versions"></a>Wyświetl wersje obiektów BLOB

Eksplorator usługi Storage obsługuje cztery różne widoki do wyświetlania obiektów BLOB.

| Widok | Aktywne obiekty blob niebędące wersjami | Nietrwałe usunięte obiekty blob niebędące wersjami | Wersje obiektów BLOB |
| ---- | :----------: | :-----------: | :------------------: |
| Aktywne obiekty blob | Tak | Nie | Tylko bieżąca wersja |
| Aktywne obiekty blob i nietrwałe usunięte obiekty blob | Tak | Tak | Tylko bieżąca wersja |
| Aktywne obiekty blob i obiekty blob bez bieżącej wersji | Tak | Nie | Bieżąca wersja lub Najnowsza aktywna wersja |
| Wszystkie obiekty blob i obiekty blob bez bieżącej wersji | Tak | Tak | Bieżąca wersja lub Najnowsza wersja |

### <a name="active-blobs"></a>Aktywne obiekty blob

W tym widoku Eksplorator usługi Storage są wyświetlane:

- Aktywne obiekty blob niebędące wersjami
- Bieżące wersje

### <a name="active-blobs-and-soft-deleted-blobs"></a>Aktywne obiekty blob i nietrwałe usunięte obiekty blob

W tym widoku Eksplorator usługi Storage są wyświetlane:

- Aktywne obiekty blob niebędące wersjami
- Nietrwałe usunięte obiekty blob niebędące wersjami
- Bieżące wersje.

### <a name="active-blobs-and-blobs-without-current-version"></a>Aktywne obiekty blob i obiekty blob bez bieżącej wersji

W tym widoku Eksplorator usługi Storage są wyświetlane:

- Aktywne obiekty blob niebędące wersjami
- Bieżące wersje
- Najnowsze aktywne starsze wersje. 

W przypadku obiektów blob, które nie mają bieżącej wersji, ale mają aktywną poprzednią wersję, Eksplorator usługi Storage wyświetla swoją najnowszą aktywną poprzednią wersję jako reprezentację tego obiektu BLOB.

### <a name="all-blobs-and-blobs-without-current-version"></a>Wszystkie obiekty blob i obiekty blob bez bieżącej wersji

W tym widoku Eksplorator usługi Storage są wyświetlane:

- Aktywne obiekty blob niebędące wersjami
- Nietrwałe usunięte obiekty blob niebędące wersjami
- Bieżące wersje
- Najnowsze poprzednie wersje. 

W przypadku obiektów blob, które nie mają bieżącej wersji, Eksplorator usługi Storage wyświetla swoją najnowszą poprzednią wersję jako reprezentację tego obiektu BLOB.

> [!Note]
> Ze względu na ograniczenie usługi, Eksplorator usługi Storage potrzebuje dodatkowego przetwarzania, aby uzyskać hierarchiczny widok katalogów wirtualnych podczas wyświetlania listy wersji obiektów BLOB. Wyświetlenie listy obiektów BLOB w następujących widokach zajmie więcej czasu:
> 
> - Aktywne obiekty blob i obiekty blob bez bieżącej wersji
> - Wszystkie obiekty blob i obiekty blob bez bieżącej wersji

## <a name="manage-blob-versions"></a>Zarządzanie wersjami obiektów BLOB

### <a name="view-versions-of-a-blob"></a>Wyświetlanie wersji obiektu BLOB

Eksplorator usługi Storage udostępnia polecenie **Zarządzaj wersjami** , aby wyświetlić wszystkie wersje obiektu BLOB. Aby wyświetlić wersje obiektów blob, wybierz obiekt BLOB, dla którego chcesz wyświetlić wersje, i wybierz pozycję **Zarządzaj historią &rarr; Zarządzanie wersjami** z paska narzędzi lub menu kontekstowego.

### <a name="download-blob-versions"></a>Pobierz wersje obiektów BLOB

Aby pobrać co najmniej jedną wersję obiektu BLOB, wybierz wersje obiektów blob, które chcesz pobrać, a następnie wybierz pozycję **Pobierz** z paska narzędzi lub menu kontekstowego.

Jeśli pobierasz wiele wersji obiektu BLOB, pobrane pliki będą miały identyfikatory wersji na początku nazw plików.

### <a name="delete-blob-versions"></a>Usuń wersje obiektów BLOB

Aby usunąć co najmniej jedną wersję obiektu BLOB, wybierz wersje obiektów blob, które chcesz usunąć, a następnie wybierz pozycję **Usuń** z paska narzędzi lub menu kontekstowego.

Wersje obiektów BLOB podlegają zasadom usuwania nietrwałego. Jeśli funkcja usuwania nietrwałego jest włączona, wersje obiektów BLOB zostaną usunięte z nietrwałego. W przypadku jednego specjalnego przypadku jest usuwana bieżąca wersja. Usunięcie bieżącej wersji spowoduje, że zostanie ona automatycznie uaktywniona w poprzedniej wersji.

### <a name="promote-blob-version"></a>Podwyższ poziom wersji obiektu BLOB

Zawartość obiektu BLOB można przywrócić, promując poprzednią wersję jako bieżącą. Wybierz wersję obiektu BLOB, którą chcesz podwyższyć, i wybierz pozycję **Podwyższ poziom wersji** na pasku narzędzi lub menu kontekstowego.

Obiekty blob spoza wersji zostaną zastąpione przez promowaną wersję obiektu BLOB. Upewnij się, że dane lub wykonaj kopię zapasową danych przed potwierdzeniem operacji. Bieżące wersje automatycznie staną się poprzednimi wersjami, więc Eksplorator usługi Storage nie monitują o potwierdzenie.

### <a name="undelete-blob-version"></a>Cofanie usunięcia wersji obiektu BLOB

Wersje obiektów BLOB nie mogą być cofnięte osobno. Wszystkie te osoby muszą być cofnięte. Aby usunąć wszystkie wersje obiektów BLOB obiektów blob, wybierz jeden z wersji obiektu BLOB i wybierz pozycję **Cofnij usunięcie wybrane** z paska narzędzi lub menu kontekstowego.

### <a name="change-access-tier-of-blob-versions"></a>Zmień warstwę dostępu do wersji obiektów BLOB

Każda wersja obiektu BLOB ma własną warstwę dostępu. Aby zmienić warstwę dostępu do wersji obiektów blob, wybierz wersje obiektów blob, dla których chcesz zmienić warstwę dostępu, a następnie wybierz pozycję **Zmień warstwę dostępu..** . z menu kontekstowego.

## <a name="see-also"></a>Zobacz też

* [Przechowywanie wersji obiektów BLOB](../blobs/versioning-overview.md)
* [Usuwanie nietrwałe dla obiektów blob](../blobs/soft-delete-blob-overview.md)