---
title: Przegląd usługi kudu
description: Dowiedz się więcej o aparacie, który umożliwia ciągłe wdrażanie w App Service i jego funkcjach.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609632"
---
# <a name="kudu-service-overview"></a>Przegląd usługi kudu

Kudu to aparat związany z liczbą funkcji w [Azure App Service](overview.md) związany z wdrożeniem na podstawie kontroli źródła oraz inne metody wdrażania, takie jak Dropbox i synchronizacja z usługą OneDrive. 

## <a name="access-kudu-for-your-app"></a>Dostęp do kudu aplikacji
Za każdym razem, gdy tworzysz aplikację, App Service tworzy aplikację towarzyszącą, która jest zabezpieczona za pomocą protokołu HTTPS. Ta aplikacja kudu jest dostępna pod adresem:

- Aplikacja nie znajduje się w warstwie izolowanej: `https://<app-name>.scm.azurewebsites.net`
- Aplikacja w warstwie izolowanej (App Service Environment): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do usługi kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## <a name="kudu-features"></a>Funkcje kudu

Kudu zapewnia przydatne informacje o aplikacji App Service, na przykład:

- Ustawienia aplikacji
- Parametry połączeń
- Zmienne środowiskowe
- Zmienne serwera
- Nagłówki HTTP

Udostępnia również inne funkcje, takie jak:

- Uruchom polecenia w [konsoli kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Pobieranie zrzutów diagnostycznych usług IIS lub dzienników platformy Docker.
- Zarządzanie procesami i rozszerzeniami usług IIS.
- Dodaj elementy webhook wdrożenia dla programu APS dla systemu Windows.
- Zezwalaj na interfejs użytkownika wdrożenia ZIP z programem `/ZipDeploy` .
- Generuje [niestandardowe skrypty wdrażania](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
- Zezwala na dostęp za pomocą [interfejsu API REST](https://github.com/projectkudu/kudu/wiki/REST-API).

## <a name="more-resources"></a>Dalsze zasoby

Kudu to projekt typu " [Open Source](https://github.com/projectkudu/kudu)", który ma swoją dokumentację w witrynie [kudu wiki](https://github.com/projectkudu/kudu/wiki).

