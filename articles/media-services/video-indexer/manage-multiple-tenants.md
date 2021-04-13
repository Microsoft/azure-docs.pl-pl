---
title: Zarządzanie wieloma dzierżawcami przy użyciu Video Indexer — Azure
description: W tym artykule przedstawiono różne opcje integracji umożliwiające zarządzanie wieloma dzierżawcami przy użyciu Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 7596aa4cef6c4bc601c15018e8ede4265f395eb7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312862"
---
# <a name="manage-multiple-tenants"></a>Zarządzanie wieloma dzierżawami

W tym artykule omówiono różne opcje zarządzania wieloma dzierżawcami przy użyciu Video Indexer. Wybierz metodę, która jest najbardziej odpowiednia dla Twojego scenariusza:

* Konto usługi Video Indexer na dzierżawę
* Pojedyncze konto usługi Video Indexer dla wszystkich dzierżaw
* Subskrypcja platformy Azure na dzierżawę

## <a name="video-indexer-account-per-tenant"></a>Konto usługi Video Indexer na dzierżawę

W przypadku korzystania z tej architektury konto Video Indexer jest tworzone dla każdej dzierżawy. Dzierżawcy mają pełną izolację w warstwie trwałej i obliczeniowej.  

![Konto usługi Video Indexer na dzierżawę](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci nie współdzielą kont magazynu (o ile nie zostały ręcznie skonfigurowane przez klienta).
* Klienci nie współdzielą zasobów obliczeniowych (jednostek zarezerwowanych) i nie wpływają na czasy przetwarzania zadań.
* Dzierżawę można łatwo usunąć z systemu, usuwając konto Video Indexer.
* Nie ma możliwości udostępniania niestandardowych modeli między dzierżawcami.

    Upewnij się, że nie ma wymagań firmy do udostępniania niestandardowych modeli.
* Trudniejsze do zarządzania ze względu na wiele kont Video Indexer (i skojarzonych Media Services) na dzierżawę.

> [!TIP]
> Utwórz użytkownika administracyjnego dla systemu w [portalu Video Indexer Developer](https://api-portal.videoindexer.ai/) i użyj interfejsu API autoryzacji, aby zapewnić dzierżawcom odpowiedni [token dostępu do konta](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Pojedyncze konto Video Indexer dla wszystkich użytkowników

W przypadku korzystania z tej architektury klient jest odpowiedzialny za izolację dzierżawców. Wszyscy dzierżawcy muszą używać jednego konta Video Indexer z pojedynczym kontem usługi Azure Media. Podczas przekazywania, wyszukiwania lub usuwania zawartości Klient będzie musiał odfiltrować odpowiednie wyniki dla tej dzierżawy.

![Pojedyncze konto Video Indexer dla wszystkich użytkowników](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

W przypadku tej opcji modele dostosowania (osoba, język i marki) mogą być udostępniane lub izolowane między dzierżawcami przez filtrowanie modeli według dzierżawców.

Podczas [przekazywania filmów wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)można określić inny atrybut partycji dla dzierżawy. Pozwoli to na izolację w [interfejsie API wyszukiwania](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos). Określenie atrybutu Partition w interfejsie API wyszukiwania spowoduje uzyskanie tylko wyników określonej partycji. 

### <a name="considerations"></a>Zagadnienia do rozważenia

* Możliwość udostępniania zawartości i modeli dostosowywania między dzierżawcami.
* Jeden dzierżawca ma wpływ na wydajność innych dzierżawców.
* Klient musi utworzyć złożoną warstwę zarządzania na Video Indexer.

> [!TIP]
> Aby określić priorytety zadań dzierżawców, można użyć atrybutu [priorytetu](upload-index-videos.md) .

## <a name="azure-subscription-per-tenant"></a>Subskrypcja platformy Azure na dzierżawę 

W przypadku korzystania z tej architektury każda dzierżawa będzie miała własną subskrypcję platformy Azure. Dla każdego użytkownika zostanie utworzone nowe konto Video Indexer w ramach subskrypcji dzierżawy.

![Subskrypcja platformy Azure na dzierżawę](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Zagadnienia do rozważenia

* Jest to jedyna opcja, która umożliwia rozdzielenie rozliczeń.
* Ta integracja ma więcej kosztów związanych z zarządzaniem niż Video Indexer konta na dzierżawcę. Jeśli rozliczenia nie są wymagane, zaleca się użycie jednej z innych opcji opisanych w tym artykule.

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)
