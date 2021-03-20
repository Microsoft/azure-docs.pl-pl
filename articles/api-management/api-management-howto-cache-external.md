---
title: Używanie zewnętrznej pamięci podręcznej w usłudze Azure API Management | Microsoft Docs
description: Dowiedz się, jak skonfigurować zewnętrzną pamięć podręczną i używać jej w usłudze Azure API Management. Użycie zewnętrznej pamięci podręcznej umożliwia przezwyciężenie niektórych ograniczeń dotyczących wbudowanej pamięci podręcznej.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018226"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Używanie zewnętrznej pamięci podręcznej zgodnej z Redis na platformie Azure API Management

Oprócz używania wbudowanej pamięci podręcznej usługa Azure API Management umożliwia buforowanie odpowiedzi w zewnętrznej pamięci podręcznej zgodnej z Redis, np. Azure cache for Redis.

Użycie zewnętrznej pamięci podręcznej umożliwia przezwyciężenie kilku ograniczeń wbudowanej pamięci podręcznej:

* uniknąć okresowego czyszczenia pamięci podręcznej podczas aktualizacji usługi API Management;
* mieć większą kontrolę nad konfiguracją pamięci podręcznej;
* buforować większe ilości danych, niż pozwala warstwa usługi API Management;
* używać buforowania z warstwą Zużycie usługi API Management.
* Włącz buforowanie w [API Management samodzielnych bram](self-hosted-gateway-overview.md)

Aby uzyskać bardziej szczegółowe informacje na temat buforowania, zobacz [API Management caching policies](api-management-caching-policies.md) (Zasady buforowania w usłudze API Management) i [Custom caching in Azure API Management](api-management-sample-cache-by-key.md) (Buforowanie niestandardowe w usłudze Azure API Management).

![Dodawanie własnej pamięci podręcznej do usługi APIM](media/api-management-howto-cache-external/overview.png)

Zawartość:

> [!div class="checklist"]
> * Dodawanie zewnętrznej pamięci podręcznej w usłudze API Management

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

+ [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md)
+ Zrozumienie [buforowania w usłudze Azure API Management](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Tworzenie pamięci podręcznej Azure Cache for Redis

W tej sekcji opisano, jak utworzyć pamięć podręczną Azure Cache for Redis na platformie Azure. Jeśli masz już pamięć podręczną Azure Cache for Redis w obrębie platformy Azure lub poza nią, możesz <a href="#add-external-cache">pominąć</a> te kroki i przejść do następnej sekcji.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"></a> Wdróż pamięć podręczną Redis w usłudze Kubernetes

W przypadku pamięci podręcznej bramy samoobsługowe korzystają wyłącznie z zewnętrznych pamięci podręcznych. Aby pamięć podręczna była skuteczna dla autonomicznych bram, a pamięci podręcznej, w której się znajdują, muszą znajdować się blisko siebie, aby zminimalizować opóźnienia wyszukiwania i przechowywania. Najlepszym rozwiązaniem jest wdrożenie pamięci podręcznej Redis w tym samym klastrze Kubernetes lub w oddzielnym klastrze. Skorzystaj z tego [linku](https://github.com/kubernetes/examples/tree/master/guestbook) , aby dowiedzieć się, jak wdrożyć pamięć podręczną Redis w klastrze Kubernetes.

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Dodawanie zewnętrznej pamięci podręcznej

Wykonaj poniższe kroki, aby dodać zewnętrzną pamięć podręczną Azure Cache for Redis w usłudze Azure API Management.

![Zrzut ekranu pokazujący, jak dodać zewnętrzną pamięć podręczną platformy Azure dla Redis na platformie Azure API Management.](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Ustawienie **Użyj z** ustawienia określa region platformy Azure lub autonomiczną lokalizację bramy, która będzie używać skonfigurowanej pamięci podręcznej. Pamięci podręczne skonfigurowane jako **domyślne** zostaną przesłonięte przez pamięć podręczną z określonym regionem lub wartością lokalizacji.
>
> Jeśli na przykład usługa API Management jest hostowana w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia, i skonfigurowano dwie pamięci podręczne, jedną jako **domyślną** i jedną dla regionu **Azja Południowo-Wschodnia**, to usługa API Management w regionie **Azja Południowo-Wschodnia** będzie używać własnej pamięci podręcznej, a dwa pozostałe regiony będą używać **domyślnej** pamięci podręcznej.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Dodawanie pamięci podręcznej Azure Cache for Redis z tej samej subskrypcji

1. Przejdź do wystąpienia usługi API Management w witrynie Azure Portal.
2. Wybierz kartę **Zewnętrzna pamięć podręczna** z menu po lewej stronie.
3. Kliknij przycisk **+ Dodaj**.
4. Wybierz swoją pamięć podręczną w polu rozwijanym **Wystąpienie pamięci podręcznej**.
5. Wybierz opcję **domyślny** lub określ żądany region w polu **Użyj z** listy rozwijanej.
6. Kliknij pozycję **Zapisz**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Dodawanie pamięci podręcznej Azure Cache for Redis hostowanej poza bieżącą subskrypcją platformy Azure lub poza samą platformą Azure

1. Przejdź do wystąpienia usługi API Management w witrynie Azure Portal.
2. Wybierz kartę **Zewnętrzna pamięć podręczna** z menu po lewej stronie.
3. Kliknij przycisk **+ Dodaj**.
4. Wybierz pozycję **Niestandardowe** w polu rozwijanym **Wystąpienie pamięci podręcznej**.
5. Wybierz opcję **domyślny** lub określ żądany region w polu **Użyj z** listy rozwijanej.
6. W polu **Parametry połączenia** podaj parametry połączenia swojej pamięci podręcznej Azure Cache for Redis.
7. Kliknij pozycję **Zapisz**.

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>Dodawanie pamięci podręcznej Redis do bramy samoobsługowej

1. Przejdź do wystąpienia usługi API Management w witrynie Azure Portal.
2. Wybierz kartę **Zewnętrzna pamięć podręczna** z menu po lewej stronie.
3. Kliknij przycisk **+ Dodaj**.
4. Wybierz pozycję **Niestandardowe** w polu rozwijanym **Wystąpienie pamięci podręcznej**.
5. Określ żądaną lokalizację własnej bramy lub **wartość domyślną** w polu **Użyj z** listy rozwijanej.
6. W polu **Parametry połączenia** podaj parametry połączenia swojej pamięci podręcznej Redis.
7. Kliknij pozycję **Zapisz**.

## <a name="use-the-external-cache"></a>Używanie zewnętrznej pamięci podręcznej

Gdy pamięć zewnętrzna zostanie skonfigurowana w usłudze API Management, można jej używać za pośrednictwem zasad buforowania. Zobacz [Dodawanie buforowania w celu poprawy wydajności usługi Azure API Management](api-management-howto-cache.md), aby zapoznać się ze szczegółowymi instrukcjami.

## <a name="next-steps"></a><a name="next-steps"> </a>Następne kroki

* Więcej informacji na temat zasad buforowania, można znaleźć w temacie [Caching policies][Caching policies] (Zasady buforowania) w artykule [API Management policy reference][API Management policy reference] (Dokumentacja zasad usługi API Management).
* Aby poznać informacje na temat buforowania elementów według kluczy przy użyciu wyrażeń zasad, zobacz artykuł [Custom caching in Azure API Management](api-management-sample-cache-by-key.md) (Niestandardowe buforowanie w usłudze Azure API Management).

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
