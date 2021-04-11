---
title: Wdróż platformę Azure Industrial IoT
description: W tym samouczku dowiesz się, jak wdrożyć platformę IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787872"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Samouczek: wdrażanie platformy Azure Industrial IoT

W ramach tego samouczka nauczysz się:

> [!div class="checklist"]
> * Informacje o głównych składnikach platformy IIoT
> * Informacje o różnych typach instalacji
> * Jak wdrożyć platformę przemysł IoT

## <a name="prerequisites"></a>Wymagania wstępne

- Należy utworzyć subskrypcję platformy Azure
- Pobierz usługę [git](https://git-scm.com/downloads)
- Rejestracje aplikacji usługi Azure Active Directory (AAD) używane na potrzeby uwierzytelniania wymagają uprawnień administratora globalnego, administratora aplikacji lub administratora aplikacji w chmurze, aby zapewnić zgodę administratora całej dzierżawy (zobacz poniżej, aby uzyskać więcej opcji).
- Obsługiwane systemy operacyjne wdrożenia to Windows, Linux i Mac
- IoT Edge obsługuje systemy Windows 10 IoT Enterprise LTSC i Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Główne składniki

- Minimalne zależności: IoT Hub, Cosmos DB, Service Bus, centrum zdarzeń, Key Vault, magazyn
- Zależności standardowe: minimalna usługa sygnalizująca, Rejestracja aplikacji usługi AAD, usługa Device Provisioning, Time Series Insights, skoroszyt, Log Analytics, Application Insights
- Mikrousługi: plan App Service, App Service
- Interfejs użytkownika (aplikacja sieci Web): App Service Plan (udostępniony dla mikrousług), App Service
- Symulacja: maszyna wirtualna, Sieć wirtualna, IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>Typy instalacji

- Minimum: minimalne zależności
- Lokalne: minimalne i standardowe zależności
- Usługi: lokalne i mikrousługi
- Symulacja: minimalna zależność i składniki symulacji
- Aplikacja: usługi i interfejs użytkownika
- Wszystkie (domyślnie): aplikacja i symulacja

## <a name="deployment"></a>Wdrożenie

1. Aby rozpocząć wdrażanie platformy IIoT, Sklonuj repozytorium z poziomu wiersza polecenia lub terminalu.

    dysk CD klonowania git https://github.com/Azure/Industrial-IoT  Industrial-IoT

2. Rozpocznij wdrożenie z przewodnikiem, skrypt zbiera wymagane informacje, takie jak konto platformy Azure, subskrypcja, zasób docelowy i Grupa i nazwa aplikacji.

W systemie Windows:
    ```
    .\deploy
    ```

W systemie Linux lub Mac:
    ```
    ./deploy.sh
    ```

3. Mikrousługi i interfejs użytkownika są aplikacjami sieci Web, które wymagają uwierzytelniania, w usłudze AAD wymagane są trzy rejestracje aplikacji. Jeśli brakuje wymaganych praw, istnieją dwa możliwe rozwiązania:

- Poproszenie administratora usługi AAD o przyznanie zgody administratora dla całej dzierżawy dla aplikacji
- Administrator usługi AAD może tworzyć aplikacje usługi AAD. Folder Wdróż/scripts zawiera skrypt AAD-register.ps1 do przeprowadzenia rejestracji w usłudze AAD niezależnie od wdrożenia. Dane wyjściowe skryptu to plik zawierający odpowiednie informacje, które mają być użyte jako część wdrożenia i muszą zostać przesłane do skryptu deploy.ps1 w tym samym folderze, przy użyciu argumentu-aadConfig.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

W przypadku wdrożeń produkcyjnych wymagających przemieszczania, wycofywania, skalowania i odporności można wdrożyć platformę w [usłudze Azure Kubernetes Service (AKS)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)

Wołują
- [Wdrażanie platformy Azure Industrial IoT](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Jak wdrożyć wszystko w jednym](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Jak wdrożyć platformę w programie AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Następne kroki
Po wdrożeniu platformy IIoT można dowiedzieć się, jak dostosować konfigurację składników:

> [!div class="nextstepaction"]
> [Dostosuj konfigurację składników](tutorial-configure-industrial-iot-components.md)
