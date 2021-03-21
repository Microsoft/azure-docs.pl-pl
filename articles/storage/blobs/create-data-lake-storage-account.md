---
title: Utwórz konto magazynu dla Azure Data Lake Storage Gen2
description: Dowiedz się, jak utworzyć konto magazynu do użycia z Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624341"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Utwórz konto magazynu do użycia z Azure Data Lake Storage Gen2

Aby użyć funkcji Data Lake Storage Gen2, Utwórz konto magazynu, które ma hierarchiczną przestrzeń nazw.

## <a name="choose-a-storage-account-type"></a>Wybierz typ konta magazynu

Funkcje Data Lake Storage są obsługiwane w następujących typach kont magazynu:

- Ogólnego przeznaczenia, wersja 2
- BlockBlobStorage

Aby dowiedzieć się, jak wybierać między nimi, zobacz temat [konto magazynu — Omówienie](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Tworzenie konta magazynu z hierarchiczną przestrzenią nazw

Utwórz [konto ogólnego przeznaczenia w wersji 2](../common/storage-account-create.md) lub konto [BlockBlobStorage](storage-blob-create-account-block-blob.md) z włączonym ustawieniem **hierarchicznej przestrzeni nazw** .

Odblokuj możliwości Data Lake Storage podczas tworzenia konta, włączając ustawienie **hierarchicznej przestrzeni nazw** na karcie **Zaawansowane** na stronie **Tworzenie konta magazynu** . To ustawienie należy włączyć podczas tworzenia konta. Nie można włączyć go później.

Na poniższej ilustracji przedstawiono to ustawienie na stronie **Tworzenie konta magazynu** .

> [!div class="mx-imgBorder"]
> ![Ustawienie hierarchicznej przestrzeni nazw](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Jeśli masz istniejące konto magazynu, które ma być używane z Data Lake Storage, a ustawienie hierarchicznej przestrzeni nazw jest wyłączone, musisz przeprowadzić migrację danych do nowego konta magazynu, dla którego włączono ustawienie.

> [!NOTE]
> Nie można jednocześnie włączyć **ochrony danych** i **hierarchicznej przestrzeni nazw** .

## <a name="next-steps"></a>Następne kroki

- [Omówienie kont magazynu](../common/storage-account-overview.md)
- [Używanie Azure Data Lake Storage Gen2 do wymagań dotyczących danych Big Data](data-lake-storage-data-scenarios.md)
- [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
