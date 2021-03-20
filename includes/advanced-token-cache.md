---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94628022"
---
Można użyć implementacji pamięci podręcznej tokenów MSAL, aby umożliwić aplikacjom w tle, interfejsom API i usługom używanie pamięci podręcznej tokeny dostępu w celu kontynuowania działania w imieniu użytkowników w ich nieobecności. Jest to szczególnie przydatne, jeśli aplikacje i usługi w tle wymagają dalszego działania w imieniu użytkownika po zakończeniu przez użytkownika aplikacji sieci Web frontonu.

Obecnie większość procesów w tle korzysta z [uprawnień aplikacji](/graph/auth/auth-concepts#microsoft-graph-permissions) , gdy muszą one współpracować z danymi użytkownika bez ich obecności w celu uwierzytelnienia lub ponownego uwierzytelnienia. Ze względu na to, że uprawnienia do aplikacji często wymagają zgody administratora, co wymaga podniesienia uprawnień, nastąpiło niepotrzebne tarcie, ponieważ Deweloper nie uzyskał uprawnień, które pierwotnie wyraziły zgodę na dostęp do aplikacji.

Ten przykładowy kod w usłudze GitHub pokazuje, jak uniknąć niepotrzebnego tarcia przez dostęp do pamięci podręcznej tokenów MSAL z aplikacji w tle:

 [Dostęp do pamięci podręcznej tokenów zalogowanego użytkownika z poziomu aplikacji, interfejsów API i usług w tle](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)