---
title: Zapoznaj się z żądaniami ściągnięcia w środowiskach przedprodukcyjnych w usłudze Azure static Web Apps
description: Dowiedz się, jak używać środowisk przedprodukcyjnych do przeglądania zmian żądań ściągnięcia w usłudze Azure static Web Apps.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: f8f2e352ae458e3e2825c9701437ea652ba07375
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825665"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Przeglądanie żądań ściągnięcia w środowiskach przedprodukcyjnych w usłudze Azure Static Web Apps (wersja zapoznawcza)

W tym artykule pokazano, jak używać środowiska przedprodukcyjnego do przeglądania zmian w aplikacjach wdrożonych za pomocą [usługi Azure Static Web Apps](overview.md).

Środowisko przedprodukcyjne (przygotowanie) to w pełni funkcjonalna wersja aplikacji, która obejmuje zmiany niedostępne w środowisku produkcyjnym.

Usługa Azure static Web Apps generuje przepływ pracy akcji GitHub w repozytorium. Po utworzeniu żądania ściągnięcia w odniesieniu do gałęzi, którą obserwuje przepływ pracy, środowisko przedprodukcyjne jest kompilowane. Środowisko przedprodukcyjne jest etapem aplikacji, dzięki czemu można wykonać przeglądy przed rozpoczęciem wypychania do produkcji.

Wiele środowisk przedprodukcyjnych może współistnieć w tym samym czasie podczas korzystania z usługi Azure static Web Apps. Za każdym razem, gdy tworzysz żądanie ściągnięcia w odniesieniu do rozgałęzienia czujka, przemieszczona wersja ze zmianami jest wdrażana w oddzielnym środowisku przedprodukcyjnym.

Istnieje wiele korzyści wynikających z używania środowiska przedprodukcyjnego. Możesz na przykład:

- Przejrzyj zmiany wizualne między środowiskiem produkcyjnym i tymczasowym. Na przykład wyświetlanie aktualizacji zawartości i układu.
- Zademonstrowanie zmian w zespole.
- Porównaj różne wersje aplikacji.
- Sprawdź poprawność zmian przy użyciu testów akceptacji.
- Przed wdrożeniem w środowisku produkcyjnym wykonaj testy Sanity.

> [!NOTE]
> W trakcie okresu zapoznawczego jest dozwolone [maksymalnie jedno środowisko przejściowe](quotas.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejące repozytorium GitHub skonfigurowane przy użyciu statycznego Web Apps platformy Azure. Zobacz [kompilowanie pierwszej aplikacji statycznej](getting-started.md) , jeśli jej nie masz.

## <a name="make-a-change"></a>Wprowadź zmianę

Zacznij od wprowadzenia zmiany w repozytorium. Możesz to zrobić bezpośrednio w witrynie GitHub, jak pokazano w poniższych krokach.

1. Przejdź do repozytorium projektu w usłudze GitHub, a następnie kliknij przycisk **rozgałęzienie** , aby utworzyć nową gałąź.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Tworzenie nowej gałęzi przy użyciu interfejsu GitHub":::]

    Wpisz nazwę gałęzi, a następnie kliknij pozycję **Utwórz gałąź**.

1. Przejdź do folderu _aplikacji_ i zmień treść tekstu. Na przykład można zmienić tytuł lub akapit. Po znalezieniu pliku do edycji kliknij przycisk **Edytuj** , aby wprowadzić zmiany.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Tworzenie nowej gałęzi przy użyciu interfejsu GitHub":::

1. Po wprowadzeniu zmian kliknij pozycję **Zatwierdź zmiany** , aby zatwierdzić zmiany w gałęzi.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Tworzenie nowej gałęzi przy użyciu interfejsu GitHub":::

## <a name="create-a-pull-request"></a>Tworzenie żądania ściągnięcia

Następnie Utwórz żądanie ściągnięcia z tej zmiany.

1. Otwórz kartę **żądanie ściągnięcia** projektu w witrynie GitHub:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Tworzenie nowej gałęzi przy użyciu interfejsu GitHub":::

1. Kliknij przycisk **porównaj & ściągania żądania ściągnięcia** gałęzi.

1. Opcjonalnie możesz wypełnić niektóre szczegółowe informacje o zmianach, a następnie kliknąć pozycję **Utwórz żądanie ściągnięcia**.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Tworzenie nowej gałęzi przy użyciu interfejsu GitHub":::

W razie potrzeby można przypisywać recenzentów i dodawać komentarze w celu omówienia zmian.

> [!NOTE]
> Możesz wprowadzić wiele zmian, wypychając nowe zatwierdzenia do gałęzi. Żądanie ściągnięcia jest następnie automatycznie aktualizowane w celu odzwierciedlenia wszystkich zmian.

## <a name="review-changes"></a>Przejrzyj zmiany

Po utworzeniu żądania ściągnięcia przepływ pracy wdrożenia [akcji usługi GitHub](https://github.com/features/actions) uruchamia i wdraża zmiany w środowisku przedprodukcyjnym.

Gdy przepływ pracy zakończy Kompilowanie i wdrażanie aplikacji, bot GitHub dodaje komentarz do żądania ściągnięcia, który zawiera adres URL środowiska przedprodukcyjnego. Możesz kliknąć to łącze, aby zobaczyć przygotowane zmiany.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Tworzenie nowej gałęzi przy użyciu interfejsu GitHub":::

Kliknij wygenerowany adres URL, aby zobaczyć zmiany.

Jeśli zobaczysz bliższy adres URL, zobaczysz, że zawiera on następujący: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net` .

Dla danego żądania ściągnięcia adres URL pozostaje taki sam, nawet jeśli wypychane są nowe aktualizacje. Oprócz stałego adresu URL to samo środowisko przedprodukcyjne jest ponownie używane na potrzeby okresu istnienia żądania ściągnięcia.

## <a name="publish-changes"></a>Publikowanie zmian

Po zatwierdzeniu zmian można opublikować zmiany w środowisku produkcyjnym, scalając żądanie ściągnięcia.

Kliknij pozycję **żądanie ściągnięcia scalania**:

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Tworzenie nowej gałęzi przy użyciu interfejsu GitHub":::

Scalanie kopiuje zmiany do śledzonej gałęzi (gałąź "produkcyjna"). Następnie przepływ pracy wdrożenia jest uruchamiany w śledzonej gałęzi, a zmiany są aktywne po ponownym skompilowaniu aplikacji.

Aby sprawdzić zmiany w środowisku produkcyjnym, Otwórz swój produkcyjny adres URL w celu załadowania aktywnej wersji witryny sieci Web.

## <a name="limitations"></a>Ograniczenia

Przygotowane wersje aplikacji są obecnie dostępne publicznie przez ich adresy URL, nawet jeśli repozytorium GitHub jest prywatne.

> [!WARNING]
> Należy zachować ostrożność przy publikowaniu poufnej zawartości do przemieszczonych wersji, ponieważ dostęp do środowisk przedprodukcyjnych nie jest ograniczony.

Liczba środowisk przedprodukcyjnych dostępnych dla każdej aplikacji wdrożonej za pomocą Web Apps statycznej zależy od używanej warstwy jednostki SKU. Na przykład w przypadku warstwy Bezpłatna można korzystać z 1 środowiska przedprodukcyjnego oprócz środowiska produkcyjnego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skonfiguruj domenę niestandardową](custom-domain.md)
