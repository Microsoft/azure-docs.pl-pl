---
title: Korzystanie z witryny Azure IoT Solutions — Azure | Microsoft Docs
description: Zawiera opis sposobu wdrażania akceleratora rozwiązań przy użyciu witryny sieci Web AzureIoTSolutions.com.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 5a088cb07fa74b65e497de54be8c97bd51d31c16
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387840"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Wdrażanie akceleratora rozwiązań za pomocą witryny azureiotsolutions.com

Akceleratory rozwiązań usługi Azure IoT można wdrożyć w ramach subskrypcji platformy Azure z usługi [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com obsługuje Akceleratory rozwiązań Microsoft Open Source i partnera. Te Akceleratory rozwiązań są wyrównane z [architekturą referencyjną usługi Azure IoT](/azure/architecture/reference-architectures/iot). Możesz użyć witryny, aby szybko wdrożyć Akcelerator rozwiązania jako demonstrację lub środowisko produkcyjne.

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="Strona główna rozwiązań IoT":::

> [!TIP]
> Jeśli potrzebna jest większa kontrola nad procesem wdrażania, można użyć interfejsu wiersza polecenia do wdrożenia akceleratora rozwiązania.

Akceleratory rozwiązań można wdrożyć w następujących konfiguracjach:

* **Standard**: rozwinięte wdrożenie infrastruktury na potrzeby opracowywania środowiska produkcyjnego. Mikrousługi są wdrażane na kilku maszynach wirtualnych platformy Azure za pomocą usługi Azure Container Service. Platforma Kubernetes zarządza kontenerami aparatu Docker, w których są hostowane poszczególne mikrousługi.
* **Podstawowa**: zmniejszona wersja kosztów dla demonstracji lub testowania wdrożenia. Wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej platformy Azure.
* **Local**: wdrożenie komputera lokalnego na potrzeby testowania i programowania. Takie podejście wdraża mikrousługi do lokalnego kontenera Docker i łączy się z IoT Hub, Azure Cosmos DB i usługi Azure Storage w chmurze.

Każdy akcelerator rozwiązań używa innej kombinacji usług platformy Azure, takich jak IoT Hub, Azure Stream Analytics i Cosmos DB. Aby uzyskać więcej informacji, odwiedź stronę [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) i wybierz Akcelerator rozwiązania.

## <a name="sign-in-at-azureiotsolutionscom"></a>Zaloguj się w witrynie azureiotsolutions.com

Aby można było wdrożyć Akcelerator rozwiązania, należy zalogować się pod adresem AzureIoTSolutions.com przy użyciu poświadczeń skojarzonych z subskrypcją platformy Azure. Jeśli Twoje konto jest skojarzone z więcej niż jedną dzierżawą Microsoft Azure Active Directory (AD), możesz użyć **listy rozwijanej wyboru konta** , aby wybrać katalog do użycia.

Twoje uprawnienia do wdrażania akceleratorów rozwiązań, zarządzania użytkownikami i zarządzania usługami platformy Azure zależą od roli w wybranym katalogu. Typowe role usługi Azure AD skojarzone z akceleratorami rozwiązań obejmują:

* **Administrator globalny**: może istnieć wielu [administratorów globalnych](../active-directory/roles/permissions-reference.md) na dzierżawę usługi Azure AD:

  * Podczas tworzenia dzierżawy usługi Azure AD jesteś domyślnie administratorem globalnym tej dzierżawy.
  * Administrator globalny może wdrażać Akceleratory rozwiązań Basic i Standard.

* **Użytkownik domeny**: może istnieć wielu użytkowników domeny dla dzierżawy usługi Azure AD. Użytkownik domeny może wdrożyć podstawowy akcelerator rozwiązań.

* **Gość**: dla dzierżawy usługi Azure AD może być wielu użytkowników-Gości. Użytkownicy-Goście nie mogą wdrożyć akceleratora rozwiązania w dzierżawie usługi Azure AD.

Aby uzyskać więcej informacji o użytkownikach i rolach w usłudze Azure AD, zobacz następujące zasoby:

* [Tworzenie użytkowników w Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Przypisywanie użytkowników do aplikacji](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Wybierz urządzenie

Lokacja AzureIoTSolutions.com zawiera linki do [wykazu urządzeń z certyfikatem platformy Azure dla IoT](https://catalog.azureiotsolutions.com/).

Wykaz zawiera setki certyfikowanych urządzeń IoT, które można połączyć z akceleratorami rozwiązań, aby rozpocząć tworzenie rozwiązania IoT.

![Wykaz urządzeń](media/iot-accelerators-permissions/devicecatalog.png)

Jeśli jesteś producentem sprzętu, kliknij przycisk **Zostań partnerem** , aby dowiedzieć się więcej o partnerze firmy Microsoft w programie Certified for IoT.

## <a name="next-steps"></a>Następne kroki

Aby wypróbować jeden z akceleratorów rozwiązań IoT, zapoznaj się z przewodnikiem Szybki Start: [wypróbuj rozwiązanie połączonej fabryki](quickstart-connected-factory-deploy.md).
