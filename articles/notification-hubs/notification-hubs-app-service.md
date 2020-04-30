---
title: Notification Hubs integrację z usługą App Service Mobile Apps
description: Dowiedz się, jak usługa Azure Notification Hubs współpracuje z Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e6c4fb767e6237f390cdb467b35c323f637bebf2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76264156"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integracja z usługą App Service Mobile Apps

W celu umożliwienia bezproblemowej i jednorodnej obsługi we wszystkich usługach Azure funkcja [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) ma wbudowaną obsługę powiadomień wypychanych przy użyciu usługi Notification Hubs. Funkcja [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) oferuje wysoce skalowalną, globalnie dostępną platformę tworzenia aplikacji mobilnych dla deweloperów w przedsiębiorstwach i integratorów systemów. Platforma ta oferuje bogaty zestaw funkcji dla deweloperów aplikacji mobilnych.

Deweloperzy aplikacji mobilnych mogą korzystać z usługi Notification Hubs przy użyciu następującego przepływu pracy:

1. Pobranie dojścia do urządzenia w systemie powiadomień platformy
2. Rejestracja urządzenia w usłudze Notification Hubs przy użyciu wygodnego interfejsu API rejestracji w ramach zestawu SDK klienta funkcji Mobile Apps

    > [!NOTE]
    > Zauważ, że usługa Mobile Apps usuwa wszystkie tagi rejestracji ze względów bezpieczeństwa. Pracuj z usługą Notification Hubs bezpośrednio z zaplecza, aby skojarzyć tagi z urządzeniami.

3. Wysyłanie powiadomień z zaplecza aplikacji przy użyciu usługi Notification Hubs

Oto niektóre udogodnienia dla deweloperów wynikające z tej integracji:

- **Zestawy SDK klienta funkcji Mobile Apps**: te wieloplatformowe zestawy SDK oferują proste interfejsy API do rejestracji i komunikacji z centrum powiadomień automatycznie skojarzone z aplikacją mobilną. Deweloperzy nie muszą odnajdywać poświadczeń usługi Notification Hubs i pracować przy użyciu dodatkowych usług.
  - *Wypychanie do użytkownika*: zestawy SDK automatycznie dodają tagi dla danego urządzenia przy użyciu identyfikatora uwierzytelnionego użytkownika funkcji Mobile Apps w celu umożliwienia scenariusza wypychania do użytkownika.
  - *Wypychanie do urządzenia*: zestawy SDK automatycznie używają identyfikatora instalacji funkcji Mobile Apps jako identyfikatora GUID do rejestracji w usłudze Notification Hubs, oszczędzając deweloperom pracy związanej z obsługą identyfikatorów GUID wielu usług.
- **Model instalacji**: funkcja Mobile Apps współpracuje z najnowszym modelem wypychania usługi Notification Hubs w celu reprezentowania wszystkich właściwości wypychania skojarzonych z urządzeniem w instalacji JSON, które są zgodne z usługami powiadomień push i łatwe w użyciu.
- **Elastyczność**: deweloperzy mogą zawsze pracować bezpośrednio za pomocą usługi Notification Hubs nawet po integracji.
- **Zintegrowane środowisko pracy w witrynie [Azure Portal](https://portal.azure.com)**: wypychanie jako możliwość ma wizualną reprezentację w funkcji Mobile Apps, a deweloperzy mogą z łatwością pracować przy użyciu skojarzonego centrum powiadomień za pomocą funkcji Mobile Apps.
