---
title: Informacje na temat aktualizacji urządzeń IoT Hub uwierzytelniania i autoryzacji | Microsoft Docs
description: Dowiedz się, jak aktualizacja urządzenia dla IoT Hub korzysta z usługi Azure RBAC, aby zapewnić uwierzytelnianie i autoryzację dla użytkowników i interfejsów API usług.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ca55b1df347b47a6eb82557658d59a3de666b703
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558401"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Kontrola dostępu oparta na rolach (RBAC) na platformie Azure i aktualizacja urządzenia

Aktualizacja urządzenia korzysta z usługi Azure RBAC, aby zapewnić uwierzytelnianie i autoryzację dla użytkowników i interfejsów API usług.

## <a name="configure-access-control-roles"></a>Konfigurowanie ról kontroli dostępu

Aby inni użytkownicy i aplikacje mieli dostęp do aktualizacji urządzeń, użytkownicy lub aplikacje muszą mieć udzielony dostęp do tego zasobu. Oto role, które są obsługiwane przez aktualizację urządzenia

|   Nazwa roli   | Opis  |
| :--------- | :---- |
|  Administrator aktualizacji urządzenia | Ma dostęp do wszystkich zasobów aktualizacji urządzeń  |
|  Czytnik aktualizacji urządzenia| Może wyświetlać wszystkie aktualizacje i wdrożenia |
|  Administrator zawartości urządzenia z aktualizacją | Może wyświetlać, importować i usuwać aktualizacje  |
|  Czytnik zawartości urządzenia aktualizacji | Może wyświetlać aktualizacje  |
|  Administrator wdrożeń aktualizacji urządzeń | Może zarządzać wdrażaniem aktualizacji na urządzeniach|
|  Czytnik wdrożeń aktualizacji urządzeń| Może wyświetlać wdrożenia aktualizacji na urządzeniach |

Kombinacja ról może służyć do zapewnienia właściwego poziomu dostępu. Na przykład deweloper może zaimportować aktualizacje i zarządzać nimi za pomocą roli administratora zawartości urządzenia aktualizacja, ale do wyświetlenia postępu aktualizacji wymagana jest rola czytnika wdrożenia aktualizacji urządzeń. Z drugiej strony operator rozwiązania z rolą czytnika aktualizacji urządzeń może wyświetlać wszystkie aktualizacje, ale musi użyć roli administratora wdrożenia aktualizacji urządzenia do wdrożenia określonej aktualizacji na urządzeniach.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Uwierzytelnianie w interfejsach API REST aktualizacji urządzenia na potrzeby publikowania i zarządzania

Aktualizacja urządzenia używa również usługi Azure AD do uwierzytelniania w celu publikowania zawartości i zarządzania nią za pośrednictwem interfejsów API usługi. Aby rozpocząć, musisz utworzyć i skonfigurować aplikację kliencką.

### <a name="create-client-azure-ad-app"></a>Utwórz aplikacja usługi Azure AD klienta

Aby zintegrować aplikację lub usługę z usługą Azure AD, [należy najpierw zarejestrować](../active-directory/develop/quickstart-register-app.md) aplikację w usłudze Azure AD. Konfiguracja aplikacji klienckiej różni się w zależności od używanego przepływu autoryzacji.  W poniższej konfiguracji przedstawiono wskazówki dotyczące korzystania z interfejsów API REST aktualizacji urządzenia.

* Ustaw uwierzytelnianie klienta: "Przekieruj identyfikatory URI dla klienta natywnego lub internetowego".
* Ustawianie uprawnień interfejsu API — aktualizacja urządzenia dla IoT Hub uwidacznia:
  * Delegowane uprawnienia: "user_impersonation"
  * **Opcjonalne**, udzielanie zgody administratora

[Następne kroki: Tworzenie zasobów aktualizacji urządzeń i Konfigurowanie ról kontroli dostępu](./create-device-update-account.md)