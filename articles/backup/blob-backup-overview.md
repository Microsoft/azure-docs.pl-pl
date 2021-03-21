---
title: Przegląd operacyjnej kopii zapasowej dla obiektów blob platformy Azure
description: Informacje o operacyjnej kopii zapasowej obiektów blob platformy Azure (w wersji zapoznawczej).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b10191c8a01d3cc7a92dee8ca9bf59a506497a60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745100"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Przegląd operacyjnej kopii zapasowej dla obiektów blob platformy Azure (w wersji zapoznawczej)

Operacyjna kopia zapasowa dla obiektów BLOB to zarządzane, lokalne rozwiązanie do ochrony danych, które umożliwia ochronę blokowych obiektów blob z różnych scenariuszy utraty danych, takich jak uszkodzenia, usuwanie obiektów blob i przypadkowe usuwanie konta magazynu. Dane są przechowywane lokalnie na koncie magazynu źródłowego i mogą być odzyskiwane do wybranego punktu w czasie, gdy będzie to konieczne. Zapewnia to proste, bezpieczne i ekonomiczne środki do ochrony obiektów BLOB.

Operacyjna kopia zapasowa dla obiektów BLOB integruje się z [centrum kopii zapasowych](backup-center-overview.md), między innymi funkcjami zarządzania kopiami zapasowymi, aby zapewnić pojedyncze okienko szkła, które może pomóc w zarządzaniu, monitorowaniu, obsłudze i analizowaniu kopii zapasowych na dużą skalę.

## <a name="how-operational-backup-works"></a>Jak działa operacyjna kopia zapasowa

Operacyjna kopia zapasowa obiektów BLOB to lokalne rozwiązanie do **tworzenia kopii zapasowych** . W związku z tym dane kopii zapasowej nie są transferowane do magazynu kopii zapasowych, ale są przechowywane na źródłowym koncie magazynu. Jednak magazyn kopii zapasowych nadal służy jako jednostka zarządzania kopiami zapasowymi. Jest to również ciągłe rozwiązanie do **tworzenia kopii zapasowych** , co oznacza, że nie trzeba planować żadnych kopii zapasowych i wszystkie zmiany zostaną zachowane i dostępnych ze stanu w wybranym momencie.

Operacyjna kopia zapasowa używa możliwości platformy obiektów BLOB do ochrony danych i zezwalania na odzyskiwanie, gdy jest to wymagane:

- **Przywracanie do punktu w czasie**: [przywracanie w ramach punktu obiektu BLOB w czasie](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview) umożliwia przywracanie danych obiektów BLOB do wcześniejszego stanu. To z kolei powoduje użycie **nietrwałego usunięcia**, **źródła zmian** i **przechowywania wersji obiektów BLOB** w celu zachowania danych przez określony czas. Operacyjna kopia zapasowa obsługuje przywracanie do określonego momentu oraz podstawowe funkcje zapewniające, że dane są przechowywane przez określony czas.

- **Usuń blokadę**: Usunięcie blokady zapobiega przypadkowemu usunięciu konta magazynu lub przez nieautoryzowanych użytkowników. Działająca kopia zapasowa po skonfigurowaniu również automatycznie stosuje blokadę usuwania w celu zmniejszenia możliwości utraty danych z powodu usunięcia konta magazynu.

Zapoznaj się z [matrycą pomocy technicznej](blob-backup-support-matrix.md) , aby dowiedzieć się o ograniczeniach bieżącego rozwiązania.

### <a name="protection"></a>Protection

Operacyjna kopia zapasowa jest konfigurowana i zarządzana na poziomie **konta magazynu** i ma zastosowanie do wszystkich blokowych obiektów BLOB w ramach konta magazynu. Operacyjna kopia zapasowa korzysta z **zasad tworzenia kopii zapasowych** w celu zarządzania czasem trwania, przez który dane kopii zapasowej (w tym starsze wersje i usunięte obiekty blob) mają być zachowywane w ten sposób, definiując okres, w którym można przywrócić dane. Zasady tworzenia kopii zapasowych mogą mieć maksymalnie 360 dni lub równoważną liczbę pełnych tygodni (51) lub miesięcy (11).

Podczas konfigurowania kopii zapasowej dla konta magazynu i przypisywania zasad tworzenia kopii zapasowej z zachowaniem wartości "n", właściwości podstawowe są ustawiane poniżej. Te właściwości można wyświetlić na karcie **Ochrona danych** usługi BLOB na koncie magazynu.

- **Przywracanie** do określonego momentu: ustawione na "n" dni zgodnie z definicją w zasadach tworzenia kopii zapasowych. Jeśli konto magazynu miało już włączony punkt w czasie z przechowywaniem, należy powiedzieć "x" dni, zanim skonfigurujesz kopię zapasową, czas trwania przywracania do punktu w czasie zostanie ustawiony na wartość większą z dwóch wartości, czyli wartość maksymalna (n, x). Jeśli włączono już funkcję przywracania do określonego momentu i określono, że przechowywanie będzie większe niż w zasadach tworzenia kopii zapasowych, pozostanie bez zmian.

- **Usuwanie nietrwałe**: Ustaw wartość "n + 5" dni, czyli pięć dni oprócz czasu określonego w zasadach tworzenia kopii zapasowych. Jeśli konto magazynu, które jest skonfigurowane do obsługi kopii zapasowych, ma już włączone nietrwałe usuwanie z zachowaniem wartości, należy powiedzieć "y" dni, a następnie nietrwałego zatrzymania usuwania zostanie ustawiona wartość maksymalna z dwóch wartości, czyli Max (n + 5, y). Jeśli funkcja usuwania nietrwałego została już włączona i określone przechowywanie będzie większe niż te, które są zgodne z zasadami tworzenia kopii zapasowych, pozostanie bez zmian.

- **Przechowywanie wersji dla obiektów blob i źródła zmian obiektów BLOB**: w przypadku kont magazynu skonfigurowanych do obsługi kopii zapasowych są włączone przechowywanie wersji i źródła zmian.

- **Usuń blokadę**: skonfigurowanie operacyjnej kopii zapasowej na koncie magazynu ma również zastosowanie blokadę usuwania na koncie magazynu. Blokadę usuwania zastosowana przez kopię zapasową można wyświetlić na karcie **blokady** konta magazynu.

Aby umożliwić tworzenie kopii zapasowych w celu włączenia tych właściwości na kontach magazynu, które mają być chronione, magazyn kopii zapasowych musi mieć przydzielone rolę **współautor kopii zapasowej konta magazynu** na odpowiednich kontach magazynu.

>[!NOTE]
>Operacyjna kopia zapasowa obsługuje operacje w blokowych obiektach Blob, a operacje na kontenerach nie mogą zostać przywrócone. W przypadku usunięcia kontenera z konta magazynu przez wywołanie operacji **usuwania kontenera** nie można przywrócić tego kontenera przy użyciu operacji przywracania. Zaleca się włączenie usuwania nietrwałego w celu zwiększenia ochrony i odzyskiwania danych.

### <a name="management"></a>Zarządzanie

Po włączeniu tworzenia kopii zapasowej na koncie magazynu tworzone jest wystąpienie kopii zapasowej odpowiadające kontu magazynu w magazynie kopii zapasowych. Można wykonać wszystkie operacje związane z kopiami zapasowymi dla konta magazynu, takie jak inicjowanie przywracania, monitorowanie, zatrzymywanie ochrony itd., za pomocą odpowiedniego wystąpienia kopii zapasowej.

Operacyjna kopia zapasowa integruje się również bezpośrednio z centrum kopii zapasowych, co ułatwia zarządzanie ochroną wszystkich kont magazynu centralnie oraz przy użyciu wszystkich innych obsługiwanych obciążeń związanych z tworzeniem kopii zapasowych. Centrum kopii zapasowych to pojedyncze okienko szkła do wszystkich wymagań związanych z tworzeniem kopii zapasowych, takich jak monitorowanie zadań i stan kopii zapasowych oraz przywracanie, zapewnienie zgodności i zarządzania, analizowanie użycia kopii zapasowych oraz wykonywanie operacji związanych z tworzeniem kopii zapasowych i przywracaniem danych.

### <a name="restore"></a>Przywracanie

Dane można przywrócić z dowolnego punktu w czasie, dla którego istnieje punkt odzyskiwania. Punkt odzyskiwania jest tworzony, gdy konto magazynu jest w stanie chronionym, i może służyć do przywracania danych, o ile jest to możliwe w okresie przechowywania zdefiniowanym przez zasady tworzenia kopii zapasowych (i dlatego Funkcja przywracania do określonego momentu usługi BLOB na koncie magazynu). Operacyjna kopia zapasowa używa przywracania do punktu w czasie do przywracania danych z punktu odzyskiwania.

Operacyjna kopia zapasowa umożliwia przywrócenie wszystkich blokowych obiektów BLOB na koncie magazynu, przeglądanie i przywracanie określonych kontenerów albo używanie dopasowań prefiksów do przywracania podzbioru obiektów BLOB. Wszystkie operacje przywracania można wykonać tylko na koncie magazynu źródłowego.

## <a name="pricing"></a>Ceny

W przypadku używania operacyjnej kopii zapasowej dla obiektów BLOB nie są naliczane żadne opłaty za zarządzanie ani opłaty za wystąpienie. Zostaną jednak naliczone następujące opłaty:

- Przywracanie odbywa się przy użyciu przywracania do punktu w czasie i naliczane są opłaty na podstawie ilości przetworzonych danych. Aby uzyskać więcej informacji, zobacz [Cennik przywracania do punktu w czasie](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview#pricing-and-billing).

- Przechowywanie danych ze względu [na nietrwałe usunięcie dla obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview), [obsługę kanału informacyjnego zmiany w usłudze Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-change-feed)i [przechowywanie wersji obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/versioning-overview).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie kopii zapasowych obiektów blob platformy Azure i zarządzanie nimi](blob-backup-configure-manage.md)
