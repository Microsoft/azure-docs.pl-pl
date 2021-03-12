---
title: Samouczek aplikacji sieci Web — Konfigurowanie interfejsu API platformy Azure dla usługi FHIR
description: Ten samouczek przeprowadzi Cię przez przykład wdrożenia prostej aplikacji sieci Web. Ten pierwszy samouczek zawiera opis wymagań wstępnych i wdrożenia interfejsu API platformy Azure dla usługi FHIR
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: cb183b5c8aff018d4dc73819b938b24ad0daa934
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020012"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>Wdróż aplikację JavaScript w celu odczytania danych z usługi FHIR
W tym samouczku zostanie wdrożona mała aplikacja JavaScript, która odczytuje dane z usługi FHIR. Kroki opisane w tym samouczku:
1. Wdrażanie serwera FHIR
1. Rejestrowanie publicznej aplikacji klienckiej
1. Przetestowanie dostępu do aplikacji
1. Tworzenie aplikacji sieci Web, która odczytuje dane FHIR

## <a name="prerequisites"></a>Wymagania wstępne
Przed uruchomieniem tego zestawu samouczków będą potrzebne następujące elementy:
1. Subskrypcja platformy Azure
1. Dzierżawa usługi Azure Active Directory
1. Zainstalowano [pocztę](https://www.getpostman.com/)

> [!NOTE]
> W tym samouczku usługa FHIR, aplikacja usługi Azure AD i użytkownicy usługi Azure AD znajdują się w tej samej dzierżawie usługi Azure AD. Jeśli tak się nie stanie, możesz nadal postępować zgodnie z tym samouczkiem, ale może być konieczne szczegółowe do niektórych dokumentów, do których istnieją odwołania, aby wykonać dodatkowe kroki.

## <a name="deploy-azure-api-for-fhir"></a>Wdrażanie usługi Azure API for FHIR
Pierwszy krok w samouczku polega na poprawnym skonfigurowaniu interfejsu API platformy Azure dla usługi FHIR.

1. Jeśli jeszcze tego nie zrobiono, wdróż [interfejs API platformy Azure dla usługi FHIR](fhir-paas-portal-quickstart.md).
1. Po wdrożeniu interfejsu API platformy Azure dla usługi FHIR Skonfiguruj ustawienia [CORS](configure-cross-origin-resource-sharing.md) , przechodząc do interfejsu API platformy Azure dla FHIR i wybierając pozycję CORS. 
    1. Ustaw **źródła** *
    1. Ustaw **nagłówki** na *
    1. W obszarze **metody** wybierz pozycję **Zaznacz wszystko** .
    1. Ustaw **Maksymalny wiek** na **600**

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz wdrożony interfejs API platformy Azure dla usługi FHIR, możesz rozpocząć rejestrację publicznej aplikacji klienckiej.

>[!div class="nextstepaction"]
>[Rejestrowanie publicznej aplikacji klienckiej](tutorial-web-app-public-app-reg.md)
