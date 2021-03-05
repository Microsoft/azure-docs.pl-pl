---
title: Włącz i Zarządzaj usuwaniem nietrwałym dla kontenerów (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Włącz funkcję usuwania nietrwałego kontenera (wersja zapoznawcza), aby ułatwić odzyskiwanie danych w przypadku ich błędnej modyfikacji lub usunięcia.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2097c1743e07b5563bc75d3d1cce48aa11b98e5f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216347"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Włącz i Zarządzaj usuwaniem nietrwałym dla kontenerów (wersja zapoznawcza)

Usuwanie nietrwałe kontenera (wersja zapoznawcza) chroni dane przed przypadkowym lub błędnym modyfikacją lub usunięciem. Po włączeniu usuwania nietrwałego kontenera dla konta magazynu kontener i jego zawartość mogą zostać odzyskane po jego usunięciu w okresie przechowywania, który określisz.

Jeśli istnieje możliwość, że dane mogą zostać przypadkowo zmodyfikowane lub usunięte przez aplikację lub innego użytkownika konta magazynu, firma Microsoft zaleca włączenie usuwania nietrwałego kontenera. W tym artykule przedstawiono sposób włączania usuwania nietrwałego dla kontenerów. Aby uzyskać więcej informacji na temat usuwania nietrwałego kontenera, w tym sposobu rejestracji w wersji zapoznawczej, zobacz [usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)](soft-delete-container-overview.md).

Aby zapewnić kompleksową ochronę danych, firma Microsoft zaleca również włączenie usuwania nietrwałego dla obiektów blob i obsługi wersji obiektów BLOB. Aby dowiedzieć się, jak również włączyć usuwanie nietrwałe dla obiektów blob, zobacz [Włączanie i zarządzanie nietrwałego usuwania dla obiektów BLOB](soft-delete-blob-enable.md). Aby dowiedzieć się, jak włączyć obsługę wersji obiektów blob, zobacz temat [przechowywanie wersji obiektów BLOB](versioning-overview.md).

> [!IMPORTANT]
>
> Usuwanie nietrwałe kontenera jest obecnie w **wersji zapoznawczej**. Zapoznaj się z [dodatkowymi postanowieniami dotyczącymi](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych w Microsoft Azure wersjach zapoznawczych, które mają zastosowanie do funkcji platformy Azure w wersjach beta, Preview lub innych, które nie zostały jeszcze ogólnie udostępnione.

## <a name="enable-container-soft-delete"></a>Włącz usuwanie nietrwałe kontenera

Można włączać lub wyłączać usuwanie nietrwałe kontenera dla konta magazynu w dowolnym momencie przy użyciu szablonu Azure Portal lub Azure Resource Manager.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby włączyć usuwanie nietrwałe kontenera dla konta magazynu za pomocą Azure Portal, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do swojego konta magazynu.
1. Znajdź ustawienia **ochrony danych** w obszarze **BLOB Service**.
1. Ustaw właściwość " **nietrwałego usuwania kontenera** " na wartość *włączone*.
1. W obszarze **zasady przechowywania** Określ, jak długo usuwane nietrwałe kontenery są przechowywane przez usługę Azure Storage.
1. Zapisz zmiany.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usuwania nietrwałego kontenera w Azure Portal":::

# <a name="template"></a>[Szablon](#tab/template)

Aby włączyć usuwanie nietrwałe kontenera z szablonem Azure Resource Manager, Utwórz szablon, który ustawia właściwość **containerDeleteRetentionPolicy** . Poniższe kroki opisują sposób tworzenia szablonu w Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
1. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
1. Wybierz **Template Deployment**, wybierz pozycję **Utwórz**, a następnie wybierz opcję **Kompiluj własny szablon w edytorze**.
1. W edytorze szablonów wklej poniższy kod JSON. Zastąp symbol zastępczy `<account-name>` nazwą konta magazynu.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. Określ okres przechowywania. Wartość domyślna to 7.
1. Zapisz szablon.
1. Określ grupę zasobów konta, a następnie wybierz przycisk **Recenzja + Utwórz** , aby wdrożyć szablon i włączyć usuwanie nietrwałe kontenera.

## <a name="view-soft-deleted-containers"></a>Wyświetlanie kontenerów usuniętych nietrwale

Po włączeniu usuwania nietrwałego można wyświetlić nietrwałe kontenery usunięte w Azure Portal. Kontenery usunięte nietrwałe są widoczne w określonym okresie przechowywania. Po upływie okresu przechowywania usunięty z nietrwałego kontenera zostanie trwale usunięty i nie będzie widoczny.

Aby wyświetlić nietrwałe kontenery usunięte w Azure Portal, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu w Azure Portal i Wyświetl listę kontenerów.
1. Przełącz przełącznik Pokaż usunięte kontenery w celu uwzględnienia usuniętych kontenerów na liście.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania nietrwałych usuniętych kontenerów w Azure Portal":::

## <a name="restore-a-soft-deleted-container"></a>Przywracanie usuniętego z nietrwałego kontenera

Można przywrócić usunięty trwale kontener i jego zawartość w okresie przechowywania. Aby przywrócić usunięty w Azure Portal kontener, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu w Azure Portal i Wyświetl listę kontenerów.
1. Wyświetl menu kontekstowe dla kontenera, który chcesz przywrócić, a następnie wybierz polecenie **Cofnij usunięcie** z menu.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Zrzut ekranu przedstawiający sposób przywracania usuniętego nietrwałego kontenera w Azure Portal":::

## <a name="next-steps"></a>Następne kroki

- [Usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)](soft-delete-container-overview.md)
- [Usuwanie nietrwałe dla obiektów blob](soft-delete-blob-overview.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)
