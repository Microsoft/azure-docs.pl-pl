---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8b3c1992a1cff18390f9d1332103e0650af418e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97347731"
---
## <a name="prepare-your-web-app"></a>Przygotowywanie aplikacji internetowej

Aby utworzyć niestandardowe powiązania protokołu TLS/SSL lub włączyć certyfikaty klienta dla aplikacji App Service, [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) musi znajdować się w **warstwie Podstawowa**, **standardowa**, **Premium** lub **izolowana** . W tym kroku musisz się upewnić, że Twoja aplikacja internetowa jest w obsługiwanej warstwie cenowej.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz witrynę [Azure Portal](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Przejdź do swojej aplikacji internetowej

Wyszukaj i wybierz **App Services**.

![Wybierz App Services](./media/app-service-ssl-prepare-app/app-services.png)

Na stronie **App Services** wybierz nazwę aplikacji sieci Web.

![Zrzut ekranu strony App Services w Azure Portal przedstawiający listę wszystkich uruchomionych aplikacji sieci Web, z pierwszą aplikacją na liście wyróżnioną.](./media/app-service-ssl-prepare-app/select-app.png)

Na stronie Zarządzanie aplikacji sieci Web.  

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

W lewym obszarze nawigacji na stronie Twojej aplikacji internetowej przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan usługi App Service)**.

![Menu skalowania w górę](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Upewnij się, że Twoja aplikacja internetowa nie znajduje się w warstwie **F1** ani **D1**. Bieżąca warstwa Twojej aplikacji internetowej jest wyróżniona ciemnoniebieskim polem.

![Sprawdzanie warstwy cenowej](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Niestandardowy protokół SSL nie jest obsługiwany w warstwie **F1** ani **D1**. Jeśli musisz skalować w górę, wykonaj kroki opisane w następnej sekcji. W przeciwnym razie zamknij stronę **Skalowanie w górę** i przejdź do sekcji [Skalowanie w górę planu usługi App Service](#scale-up-your-app-service-plan).

### <a name="scale-up-your-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Wybierz jedną z płatnych warstw (**B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

Kliknij pozycję **Zastosuj**.

![Wybieranie warstwy cenowej](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

![Powiadomienie o skalowaniu w górę](./media/app-service-ssl-prepare-app/scale-notification.png)

