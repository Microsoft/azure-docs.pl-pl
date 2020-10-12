---
title: Uwierzytelnianie oparte na tokenach (HTTP/2) dla usług APNS na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak używać nowego uwierzytelniania tokenów dla usługi APNS.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: e8258aeef33be9192608f0d4cb29b46f08da64e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89010634"
---
# <a name="token-based-http2-authentication-for-apns"></a>Uwierzytelnianie oparte na tokenach (HTTP/2) dla usługi APNS

## <a name="overview"></a>Omówienie

W tym artykule wyjaśniono, jak używać nowego protokołu HTTP/2 APNS z uwierzytelnianiem opartym na tokenach.

Najważniejsze zalety korzystania z nowego protokołu obejmują:

* Generowanie tokenu jest stosunkowo proste (w porównaniu z certyfikatami)
* Nie ma więcej dat wygaśnięcia — masz kontrolę nad tokenami uwierzytelniania i ich odwołaniem.
* Ładunki mogą teraz mieć do 4 KB
* Synchroniczna opinia
* Korzystasz z najnowszego protokołu firmy Apple — certyfikaty nadal używają protokołu binarnego, który jest oznaczony jako przestarzały

Za pomocą tego nowego mechanizmu można wykonać dwa kroki:

* Uzyskaj niezbędne informacje z portalu konta dewelopera firmy Apple.
* Skonfiguruj centrum powiadomień przy użyciu nowych informacji.

Notification Hubs jest teraz skonfigurowany do korzystania z nowego systemu uwierzytelniania z usługą APNS.

Należy pamiętać, że w przypadku migracji z używania poświadczeń certyfikatu dla usługi APNS właściwości tokenu zastępują certyfikat w naszym systemie, ale aplikacja nadal będzie odbierać powiadomienia bezproblemowo.

## <a name="obtaining-authentication-information-from-apple"></a>Uzyskiwanie informacji o uwierzytelnianiu od firmy Apple

Aby włączyć uwierzytelnianie oparte na tokenach, potrzebne są następujące właściwości konta dewelopera firmy Apple:

### <a name="key-identifier"></a>Identyfikator klucza

Identyfikator klucza można uzyskać ze strony **klucze** w obszarze **certyfikaty, identyfikatory & profile**na koncie dewelopera firmy Apple:

![Certyfikaty](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![Identyfikatory](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identyfikator aplikacji i nazwa aplikacji

Nazwa i identyfikator aplikacji są również dostępne na stronie **certyfikaty, identyfikatory & profile** na koncie dewelopera:

![Certyfikaty i identyfikatory](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Skonfiguruj przy użyciu zestawu .NET SDK lub Azure Portal

Koncentrator można skonfigurować tak, aby korzystał z uwierzytelniania opartego na tokenach przy użyciu [najnowszego zestawu SDK klienta](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)lub w Azure Portal. Aby włączyć uwierzytelnianie oparte na tokenach w portalu, zaloguj się do Azure Portal i przejdź do pozycji Ustawienia centrum powiadomień **> panelu Apple (APNs)** . Wybierz opcję **token** z właściwości **tryb uwierzytelniania** , aby zaktualizować centrum przy użyciu wszystkich odpowiednich właściwości tokenu.

![Skonfiguruj token](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Wprowadź właściwości pobrane z konta dewelopera firmy Apple.
* Wybierz tryb aplikacji (w**środowisku produkcyjnym** lub **piaskownicy**).
* Kliknij przycisk **Zapisz** , aby zaktualizować poświadczenia usługi APNs.

Poświadczenia oparte na tokenach składają się z następujących pól:

* **Identyfikator klucza**: Identyfikator klucza prywatnego, który został wygenerowany w portalu dla deweloperów firmy Apple; na przykład `2USFGKSKLT` .
* **Identyfikator zespołu**: nazywany także "prefiksem" lub "prefiksem aplikacji". To jest identyfikator organizacji w portalu dla deweloperów firmy Apple. na przykład `S4V3D7CHJR` .
* **Identyfikator pakietu**: określany również jako "Identyfikator aplikacji". Jest to identyfikator pakietu dla aplikacji; na przykład `com.example.myapp` . Należy pamiętać, że można użyć jednego klucza dla wielu aplikacji. Ta wartość jest mapowana na `apns-topic` nagłówek HTTP podczas wysyłania powiadomienia i służy do określania docelowej aplikacji. Należy pamiętać, że nie można ustawić wartości `apns-topic` jawnie.
* **Token**: nazywany także "kluczem" lub "kluczem prywatnym". Jest to uzyskiwane z pliku. P8 wygenerowanego w portalu dla deweloperów firmy Apple. Klucz musi mieć włączoną funkcję APN (wybraną w portalu dla deweloperów firmy Apple podczas generowania klucza). Ta wartość musi mieć usunięte z nagłówka PEM/Stopka podczas dostarczania go do portalu NH/interfejsu API.
* **Punkt końcowy**: jest to przełącznik w bloku Notification Hubs Portal i pola ciągu w interfejsie API. Prawidłowe wartości to `https://api.development.push.apple.com:443/3/device` lub `https://api.sandbox.push.apple.com:443/3/device` . Notification Hubs używa tej wartości dla środowiska produkcyjnego lub piaskownicy do wysyłania powiadomień. Ta wartość musi być zgodna z `aps-environment` uprawnieniem w aplikacji, w przeciwnym razie wygenerowane tokeny urządzeń APNs nie są zgodne ze środowiskiem i powiadomienia nie są wysyłane.

Oto przykładowy kod ilustrujący poprawne użycie:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie centrum powiadomień platformy Azure w witrynie Azure Portal](create-notification-hub-portal.md)
* [Skonfiguruj centrum powiadomień w Azure Portal](create-notification-hub-portal.md)
