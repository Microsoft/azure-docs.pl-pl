---
title: Obsługa mapy źródłowej dla aplikacji JavaScript — Azure Monitor Application Insights
description: Dowiedz się, jak przekazać mapy źródłowe do własnego kontenera obiektów BLOB konta magazynu za pomocą Application Insights.
ms.topic: conceptual
author: DavidCBerry13
ms.author: daberry
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 79462dcb503e5835254b89f7ebaa7a9ee34946d3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933941"
---
# <a name="source-map-support-for-javascript-applications"></a>Obsługa mapy źródłowej dla aplikacji JavaScript

Application Insights obsługuje przekazywanie map źródłowych do własnego kontenera obiektów BLOB konta magazynu.
Mapy źródłowe mogą służyć do unminify stosów wywołań znajdujących się na stronie szczegółów transakcji końca do końca. Każdy wyjątek wysyłany przez [zestaw SDK języka JavaScript][ApplicationInsights-JS] lub [ zestawNode.js SDK][ApplicationInsights-Node.js] może być unminified z mapami źródłowymi.

![Unminify stos wywołań przez połączenie z kontem magazynu](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Tworzenie nowego konta magazynu i kontenera obiektów BLOB

Jeśli masz już istniejące konto magazynu lub kontener obiektów blob, możesz pominąć ten krok.

1. [Utwórz nowe konto magazynu][create storage account]
2. [Utwórz kontener obiektów BLOB na][create blob container] koncie magazynu. Upewnij się, że ustawienie "publiczny poziom dostępu" ma wartość `Private` , aby upewnić się, że mapy źródłowe nie są publicznie dostępne.

> [!div class="mx-imgBorder"]
>![Poziom dostępu do kontenera musi być ustawiony na wartość prywatny](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Wypchnij mapy źródłowe do kontenera obiektów BLOB

Potoku ciągłego wdrażania należy zintegrować z kontem magazynu przez skonfigurowanie go do automatycznego przekazywania mapowań źródłowych do skonfigurowanego kontenera obiektów BLOB.

Mapy źródeł można przekazać do kontenera Blob Storage z tą samą strukturą folderów, które zostały skompilowane & wdrożone za pomocą programu. Typowym przypadkiem użycia jest przedrostek folderu wdrożenia z jego wersją, np. `1.2.3/static/js/main.js` . Gdy unminifying za pośrednictwem kontenera obiektów blob platformy Azure `sourcemaps` , zostanie podjęta próba pobrania mapy źródłowej znajdującej się w `sourcemaps/1.2.3/static/js/main.js.map` .

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Przekaż mapy źródłowe za pośrednictwem Azure Pipelines (zalecane)

Jeśli używasz Azure Pipelines do ciągłego kompilowania i wdrażania aplikacji, Dodaj zadanie [kopiowania plików platformy Azure][azure file copy] do potoku, aby automatycznie przekazywać mapy źródłowe.

> [!div class="mx-imgBorder"]
> ![Dodaj zadanie kopiowania plików platformy Azure do potoku, aby przekazać mapy źródłowe do usługi Azure Blob Storage](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Konfigurowanie zasobu Application Insights przy użyciu konta magazynu mapy źródłowej

### <a name="from-the-end-to-end-transaction-details-page"></a>Na stronie Szczegóły końca transakcji

Na karcie Szczegóły transakcji kompleksowe można kliknąć pozycję *Unminify* i wyświetlić monit o skonfigurowanie, czy zasób nie jest skonfigurowany.

1. W portalu Sprawdź szczegóły wyjątku, który jest zminimalizowanego.
2. Kliknij pozycję *Unminify*
3. Jeśli zasób nie został skonfigurowany, zostanie wyświetlony komunikat z monitem o skonfigurowanie.

### <a name="from-the-properties-page"></a>Na stronie właściwości

Jeśli chcesz skonfigurować lub zmienić konto magazynu lub kontener obiektów BLOB połączony z zasobem Application Insights, możesz to zrobić, wyświetlając kartę *Właściwości* zasobu Application Insights.

1. Przejdź do karty *Właściwości* zasobu Application Insights.
2. Kliknij pozycję *Zmień kontener obiektów BLOB mapy źródłowej*.
3. Wybierz inny kontener obiektów BLOB jako kontener mapowań źródłowych.
4. Kliknij pozycję `Apply` (Dalej).

> [!div class="mx-imgBorder"]
> ![Zmień konfigurację wybranego kontenera obiektów blob platformy Azure, przechodząc do bloku właściwości](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="required-azure-role-based-access-control-azure-rbac-settings-on-your-blob-container"></a>Wymagane ustawienia kontroli dostępu opartej na rolach (Azure RBAC) w kontenerze obiektów BLOB

Każdy użytkownik w portalu korzystający z tej funkcji musi być co najmniej przypisany jako [czytnik danych magazynu obiektów BLOB][storage blob data reader] do kontenera obiektów BLOB. Tę rolę należy przypisać do wszystkich innych użytkowników, którzy będą korzystać z map źródłowych za pośrednictwem tej funkcji.

> [!NOTE]
> W zależności od sposobu utworzenia kontenera może to nie zostać automatycznie przypisane do Ciebie lub Twojego zespołu.

### <a name="source-map-not-found"></a>Nie znaleziono mapy źródłowej

1. Sprawdź, czy odpowiednia mapa źródłowa jest przekazywana do poprawnego kontenera obiektów BLOB
2. Sprawdź, czy plik mapy źródłowej jest nazwany po pliku JavaScript, do którego jest mapowany, z sufiksem `.map` .
    - Na przykład program przeszuka `/static/js/main.4e2ca5fa.chunk.js` Wyszukiwanie obiektu BLOB o nazwie `main.4e2ca5fa.chunk.js.map`
3. Sprawdź konsolę przeglądarki, aby sprawdzić, czy występują błędy. Uwzględnij ją w dowolnym biletu pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

* [Zadanie kopiowania plików platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
