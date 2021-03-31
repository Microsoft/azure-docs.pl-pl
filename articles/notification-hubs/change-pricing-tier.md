---
title: Zmień warstwę cenową Notification Hubs przestrzeni nazw | Microsoft Docs
description: Dowiedz się, jak zmienić warstwę cenową przestrzeni nazw platformy Azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/03/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 1455259bc42aea9d506a9a2a19d725cac3d643f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87562773"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Zmień warstwę cenową przestrzeni nazw centrów powiadomień platformy Azure

Notification Hubs jest oferowana w trzech warstwach: **bezpłatna**, **podstawowa** i **standardowa**. W tym artykule opisano sposób zmiany warstwy cenowej dla przestrzeni nazw platformy Azure Notification Hubs.

## <a name="overview"></a>Omówienie

W usłudze Azure Notification Hubs *centrum powiadomień* jest najmniejszym zasobem/jednostką. Zwykle jest ona mapowana na jedną aplikację i może przechowywać jeden certyfikat dla każdego system powiadomień platformy (PNS), który obsługuje aplikacja. Aplikacja może być hybrydowa lub natywna i międzyplatformowa.

*Przestrzeń nazw* jest kolekcją centrów powiadomień. Każda przestrzeń nazw składa się zazwyczaj z centrów, które są powiązane i używane do określonego celu. Na przykład można mieć trzy różne przestrzenie nazw do celów deweloperskich, testowych i produkcyjnych.

Przestrzeń nazw można skojarzyć z warstwami **bezpłatna**, **podstawowa** lub **standardowa** . Dla każdej przestrzeni nazw można użyć warstwy, która odpowiada Twoim wymaganiom. W poniższych sekcjach pokazano, jak zmienić warstwę cenową Notification Hubs przestrzeni nazw.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

Przy użyciu Azure Portal można zmienić warstwę cenową dla przestrzeni nazw na stronie przestrzeni nazw lub na stronie centrum. Gdy zmienisz ją na stronie centrum, rzeczywiście zmienisz ją na poziomie przestrzeni nazw. Zmiana warstwy cenowej dla przestrzeni nazw i wszystkich centrów w przestrzeni nazw.

### <a name="change-tier-on-the-namespace-page"></a>Zmień warstwę na stronie przestrzeni nazw

Poniższa procedura pokazuje, jak zmienić warstwę cenową dla przestrzeni nazw na stronie przestrzeni nazw. Zmiana warstwy dla przestrzeni nazw ma zastosowanie do wszystkich centrów w tej przestrzeni nazw.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **wszystkie usługi** .
3. W sekcji **Internet rzeczy** wybierz pozycję **przestrzenie nazw centrum powiadomień** . Jeśli wybierzesz gwiazdkę ( `*` ) obok tekstu, zostanie ona dodana na lewym pasku nawigacyjnym w obszarze **Ulubione**. Dzięki temu można szybciej uzyskać dostęp do strony przestrzeni nazw. Po dodaniu go do **ulubionych** wybierz pozycję **przestrzenie nazw centrum powiadomień**.

    ![Wszystkie usługi — > przestrzenie nazw centrum powiadomień](./media/change-pricing-tier/all-services-nhub.png)

4. Na stronie **przestrzenie nazw centrum powiadomień** wybierz przestrzeń nazw, dla której chcesz zmienić warstwę cenową.
5. Na stronie **obszar nazw centrum powiadomień** dla obszaru nazw można wyświetlić bieżącą warstwę cenową dla przestrzeni nazw w sekcji **podstawy** . Na poniższej ilustracji widać, że warstwa cenowa przestrzeni nazw jest **bezpłatna**.

    ![Bieżąca warstwa cenowa na stronie przestrzeni nazw](./media/change-pricing-tier/pricing-tier-before.png)

6. Na stronie **obszar nazw centrum powiadomień** dla obszaru nazw wybierz pozycję **warstwa cenowa** w sekcji **Zarządzanie** .

    ![Wybierz warstwę cenową na stronie przestrzeni nazw](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Zmień warstwę cenową, a następnie kliknij przycisk **Wybierz** .
8. Stan akcji zmiany warstwy można zobaczyć w **alertach**.
9. Przejdź do strony **Przegląd** . Upewnij się, że nowa warstwa jest wyświetlana w polu **warstwa cenowa** w sekcji **podstawowe** informacje.
10. Ta czynność jest opcjonalna. Wybierz dowolne centrum w przestrzeni nazw. Upewnij się, że w sekcji **podstawowe** informacje są wyświetlane te same warstwy cenowe. Powinna zostać wyświetlona ta sama warstwa cenowa dla wszystkich centrów w przestrzeni nazw.

### <a name="change-tier-on-the-hub-page"></a>Zmień warstwę na stronie centrum

Wykonaj następujące kroki, aby zmienić warstwę cenową przestrzeni nazw na stronie centrum. Mimo że te kroki są wykonywane począwszy od strony centrum, w rzeczywistości zmienisz warstwę cenową dla przestrzeni nazw i wszystkich centrów w przestrzeni nazw:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **wszystkie usługi** .
3. Wybierz **Notification Hubs** w sekcji **Internet rzeczy** .
4. Wybierz **centrum** powiadomień.
5. W menu po lewej stronie wybierz pozycję **warstwa cenowa** .
6. Zmień warstwę cenową, a następnie kliknij przycisk **Wybierz** . Ta akcja powoduje zmianę ustawienia warstwy cenowej dla przestrzeni nazw zawierającej centrum. Na stronie przestrzeń nazw zostanie wyświetlona nowa warstwa cenowa i wszystkie strony centrów.

> [!NOTE]
> Wszystkie zmiany warstwy cenowej zaczynają obowiązywać natychmiast.

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Aby uzyskać bieżącą warstwę cenową i zaktualizować ją, można użyć następujących interfejsów API REST dostawcy zasobów.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Pobierz bieżącą warstwę cenową dla przestrzeni nazw

Aby uzyskać bieżącą warstwę przestrzeni nazw, Wyślij polecenie GET, jak pokazano w następującym przykładzie:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aktualizowanie warstwy cenowej dla przestrzeni nazw

Aby zaktualizować warstwę przestrzeni nazw, Wyślij polecenie PUT, jak pokazano w następującym przykładzie:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o tych warstwach i cenach, zobacz [Notification Hubs Cennik](https://azure.microsoft.com/pricing/details/notification-hubs/).
