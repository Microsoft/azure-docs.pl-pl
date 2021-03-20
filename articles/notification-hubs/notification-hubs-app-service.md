---
title: Notification Hubs integrację z usługą App Service Mobile Apps
description: Dowiedz się, jak usługa Azure Notification Hubs współpracuje z Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88004054"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integracja z usługą App Service Mobile Apps

Aby ułatwić bezproblemowe i spójne środowisko w ramach usług platformy Azure, [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) ma wbudowaną obsługę powiadomień za pomocą usługi Azure Notification Hubs. [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) oferuje wysoce skalowalną, globalnie dostępną platformę tworzenia aplikacji mobilnych dla deweloperów w przedsiębiorstwach i integratorów systemów, która oferuje bogaty zestaw funkcji dla deweloperów rozwiązań mobilnych.

Mobile Apps deweloperzy mogą używać Notification Hubs z następującym przepływem pracy:

1. Pobierz dojście PNS urządzenia.
2. Rejestrowanie urządzenia w Notification Hubs przy użyciu Mobile Apps interfejsów API rejestracji zestawu SDK klienta.

    > [!NOTE]
    > Zauważ, że usługa Mobile Apps usuwa wszystkie tagi rejestracji ze względów bezpieczeństwa. Pracuj z usługą Notification Hubs bezpośrednio z zaplecza, aby skojarzyć tagi z urządzeniami.

3. Wysyłanie powiadomień z zaplecza aplikacji przy użyciu Notification Hubs.

Dostępne są następujące zalety integracji:

- **Mobile Apps zestawy SDK klienta**: te zestawy SDK dla wielu platform zapewniają interfejsy API do rejestracji i komunikują się z centrum powiadomień, które jest połączone z aplikacją mobilną. Nie potrzebujesz Notification Hubs poświadczeń lub pracuj z dodatkową usługą.
  - *Wypchnij do użytkownika*: zestawy SDK automatycznie oznaczą określone urządzenie identyfikatorem uwierzytelnionego użytkownika Mobile Apps, aby włączyć scenariusz "wypychanie do użytkownika".
  - *Wypchnij do urządzenia*: zestawy SDK automatycznie używają Mobile Apps identyfikator instalacji jako identyfikatora GUID, aby zarejestrować się w usłudze Notification Hubs, więc nie ma potrzeby obsługi wielu identyfikatorów GUID usługi.
- **Model instalacji**: Mobile Apps współpracuje z Notification Hubs najnowszym modelem wypychania do reprezentowania wszystkich właściwości wypychania skojarzonych z urządzeniem w instalacji JSON, które są zgodne z usługami powiadomień wypychanych i są łatwe w użyciu.
- **Elastyczność**: deweloperzy mogą zawsze pracować bezpośrednio za pomocą usługi Notification Hubs nawet po integracji.
- **Zintegrowane środowisko [Azure Portal](https://portal.azure.com)**: wypchnięcie jako możliwość jest reprezentowana wizualizacją w Mobile Apps, a deweloperzy mogą łatwo współpracować ze skojarzonym centrum powiadomień za pomocą Mobile Apps.
