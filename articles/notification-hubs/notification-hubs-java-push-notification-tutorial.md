---
title: Jak korzystać z usługi Azure Notification Hubs w języku Java
description: Dowiedz się, jak używać platformy Azure Notification Hubs z zaplecza języka Java.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-java
ms.openlocfilehash: 09553f587916e8204541b36f259a450d72c1b270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87322945"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Jak używać Notification Hubs języka Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

W tym temacie opisano najważniejsze funkcje nowego, w pełni obsługiwany oficjalny zestaw Java SDK usługi Azure Notification Hub.
Ten projekt jest projektem Open-Source i można wyświetlić cały kod zestawu SDK w [zestawie SDK języka Java].

Ogólnie rzecz biorąc, można uzyskać dostęp do wszystkich funkcji Notification Hubs z języka Java/PHP/Python/Ruby zaplecza przy użyciu interfejsu REST centrum powiadomień zgodnie z opisem w temacie MSDN [Notification Hubs API REST](/previous-versions/azure/reference/dn223264(v=azure.100)). Ten zestaw SDK języka Java udostępnia cienkie otokę dla tych interfejsów REST w języku Java.

Zestaw SDK obecnie obsługuje:

* CRUD na Notification Hubs
* CRUD rejestracji
* Zarządzanie instalacją
* Rejestracje importu/eksportu
* Regularne wysyłanie
* Zaplanowane wysyłanie
* Operacje asynchroniczne za pośrednictwem języka Java NIO
* Obsługiwane platformy: APNS (iOS), FCM (Android), WNS (aplikacje do sklepu Windows), usługi MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android bez usług Google Services)

## <a name="sdk-usage"></a>Użycie zestawu SDK

### <a name="compile-and-build"></a>Kompilowanie i tworzenie kompilacji

Użyj [Maven]

Do kompilacji:

```cmd
mvn package
```

## <a name="code"></a>Kod

### <a name="notification-hub-cruds"></a>CRUDs centrum powiadomień

**Tworzenie elementu NamespaceManager:**

```java
NamespaceManager namespaceManager = new NamespaceManager("connection string")
```

**Utwórz centrum powiadomień:**

```java
NotificationHubDescription hub = new NotificationHubDescription("hubname");
hub.setWindowsCredential(new WindowsCredential("sid","key"));
hub = namespaceManager.createNotificationHub(hub);
```

 LUB

```java
hub = new NotificationHub("connection string", "hubname");
```

**Pobierz centrum powiadomień:**

```java
hub = namespaceManager.getNotificationHub("hubname");
```

**Aktualizuj centrum powiadomień:**

```java
hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
hub = namespaceManager.updateNotificationHub(hub);
```

**Usuń centrum powiadomień:**

```java
namespaceManager.deleteNotificationHub("hubname");
```

### <a name="registration-cruds"></a>CRUDs rejestracji

**Utwórz klienta centrum powiadomień:**

```java
hub = new NotificationHub("connection string", "hubname");
```

**Utwórz rejestrację systemu Windows:**

```java
WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

**Utwórz rejestrację systemu iOS:**

```java
AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

Podobnie można utworzyć rejestracje dla systemu Android (FCM), Windows Phone (usługi MPNS) i Kindle (ADM).

**Tworzenie rejestracji szablonów:**

```java
WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
reg.getHeaders().put("X-WNS-Type", "wns/toast");
hub.createRegistration(reg);
```

**Tworzenie rejestracji przy użyciu wzorca tworzenia identyfikatora rejestracji + upsert:**

Usuwa duplikaty ze względu na utracone odpowiedzi w przypadku przechowywania identyfikatorów rejestracji na urządzeniu:

```java
String id = hub.createRegistrationId();
WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
hub.upsertRegistration(reg);
```

**Aktualizacje rejestracji:**

```java
hub.updateRegistration(reg);
```

**Usuń rejestracje:**

```java
hub.deleteRegistration(regid);
```

**Rejestracje zapytań:**

* **Pobierz rejestrację pojedynczą:**

```java
hub.getRegistration(regid);
```

* **Pobierz wszystkie rejestracje w centrum:**

```java
hub.getRegistrations();
```

* **Pobierz rejestracje za pomocą tagu:**

```java
hub.getRegistrationsByTag("myTag");
```

* **Pobierz rejestracje według kanału:**

```java
hub.getRegistrationsByChannel("devicetoken");
```

Wszystkie zapytania kolekcji obsługują tokeny $top i kontynuacji.

### <a name="installation-api-usage"></a>Użycie interfejsu API instalacji

Interfejs API instalacji to alternatywny mechanizm zarządzania rejestracją. Zamiast utrzymywać wiele rejestracji, które nie są proste i mogą być łatwo wykonywane niepoprawnie lub niewydajnie, można teraz używać jednego obiektu instalacyjnego.

Instalacja zawiera wszystko, czego potrzebujesz: kanał wypychania (token urządzenia), Tagi, szablony, kafelki pomocnicze (dla WNS i APN). Nie musisz wywoływać usługi, aby uzyskać już identyfikator GUID lub dowolny inny identyfikator, przechowuj go na urządzeniu i wysyłaj do zaplecza przy użyciu kanału push (token urządzenia).

W zapleczu należy wykonać tylko jedno wywołanie, które jest w `CreateOrUpdateInstallation` pełni idempotentne, więc możesz ponowić próbę, jeśli będzie to konieczne.

Przykład dotyczący usługi Amazon Kindle Fire:

```java
Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
hub.createOrUpdateInstallation(installation);
```

Jeśli chcesz ją zaktualizować:

```java
installation.addTag("foo");
installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
hub.createOrUpdateInstallation(installation);
```

W przypadku zaawansowanych scenariuszy Użyj funkcji aktualizacji częściowej, która umożliwia modyfikowanie tylko określonych właściwości obiektu instalacyjnego. Aktualizacja częściowa jest podzbiorem operacji poprawek w formacie JSON, które można uruchomić względem obiektu instalacyjnego.

```java
PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
```

Usuń instalację:

```java
hub.deleteInstallation(installation.getInstallationId());
```

`CreateOrUpdate`, `Patch` , i `Delete` są ostatecznie spójne z `Get` . Żądana operacja właśnie przechodzi do kolejki systemowej w trakcie wywołania i jest wykonywana w tle. Pobieranie nie jest przeznaczone do głównego scenariusza środowiska uruchomieniowego, ale tylko w celu debugowania i rozwiązywania problemów, jest ściśle ograniczone przez usługę.

Wysyłanie przepływu dla instalacji jest takie samo jak w przypadku rejestracji. Aby określić docelowe powiadomienie do określonej instalacji — po prostu Użyj tagu "Identyfikator InstallationID: {żądana-ID}". W tym przypadku kod jest następujący:

```java
Notification n = Notification.createWindowsNotification("WNS body");
hub.sendNotification(n, "InstallationId:{installation-id}");
```

Dla jednego z kilku szablonów:

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("value3", "some value");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
```

### <a name="schedule-notifications-available-for-standard-tier"></a>Zaplanuj powiadomienia (dostępne dla warstwy Standardowa)

Taki sam jak zwykłe wysyłanie, ale z jednym dodatkowym parametrem-scheduledTime, który informuje o dostarczeniu powiadomienia. Usługa akceptuje dowolny punkt czasu między teraz i 5 minut, a teraz + 7 dni.

**Zaplanuj powiadomienie natywne systemu Windows:**

```java
Calendar c = Calendar.getInstance();
c.add(Calendar.DATE, 1);
Notification n = Notification.createWindowsNotification("WNS body");
hub.scheduleNotification(n, c.getTime());
```

### <a name="importexport-available-for-standard-tier"></a>Import/Export (dostępne dla warstwy Standardowa)

Może być konieczne wykonanie operacji zbiorczej w odniesieniu do rejestracji. Zazwyczaj służy do integracji z innym systemem lub z ogromną poprawką w celu zaktualizowania tagów. Nie zalecamy korzystania z przepływu pobierania/aktualizacji, jeśli chodzi o tysiące rejestracji. Możliwość importowania/eksportowania systemu została zaprojektowana w celu pokrycia tego scenariusza. Zapewnisz dostęp do kontenera obiektów BLOB w ramach konta magazynu jako źródła danych przychodzących i lokalizacji dla danych wyjściowych.

**Prześlij zadanie eksportu:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Prześlij zadanie importu:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
job.setImportFileUri("input file uri with SAS signature");
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Poczekaj, aż zadanie zostanie wykonane:**

```java
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}
```

**Pobierz wszystkie zadania:**

```java
List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
```

**Identyfikator URI z podpisem SAS:**

 Ten adres URL to adres URL pliku obiektu BLOB lub kontenera obiektów blob oraz zestaw parametrów, takich jak uprawnienia i czas wygaśnięcia, oraz sygnatura wszystkich tych elementów przy użyciu klucza SAS konta. Zestaw SDK Java usługi Azure Storage oferuje bogate możliwości, w tym tworzenie tych identyfikatorów URI. Jako alternatywę zapoznaj się z `ImportExportE2E` klasą testową (z lokalizacji GitHub), która ma podstawową i kompaktową implementację algorytmu podpisywania.

### <a name="send-notifications"></a>Wysyłanie powiadomień

Obiekt powiadomienia jest po prostu treścią z nagłówkami, ale niektóre metody narzędziowe ułatwiają tworzenie obiektów powiadomień natywnych i szablonów.

* **Sklep Windows i Windows Phone 8,1 (bez Silverlight)**

```java
String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
Notification n = Notification.createWindowsNotification(toast);
hub.sendNotification(n);
```

* **iOS**

```java
String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
Notification n = Notification.createAppleNotification(alert);
hub.sendNotification(n);
```

* **Android**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createFcmNotification(message);
hub.sendNotification(n);
```

* **Windows Phone 8,0 i 8,1 Silverlight**

```java
String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
            "<wp:Notification xmlns:wp=\"WPNotification\">" +
                "<wp:Toast>" +
                    "<wp:Text1>Hello from Java!</wp:Text1>" +
                "</wp:Toast> " +
            "</wp:Notification>";
Notification n = Notification.createMpnsNotification(toast);
hub.sendNotification(n);
```

* **Kindle pożar**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createAdmNotification(message);
hub.sendNotification(n);
```

* **Wyślij do tagów**
  
```java
Set<String> tags = new HashSet<String>();
tags.add("boo");
tags.add("foo");
hub.sendNotification(n, tags);
```

* **Wyrażenie wysyłania do tagu**

```java
hub.sendNotification(n, "foo && ! bar");
```

* **Wyślij powiadomienie dotyczące szablonu**

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("prop1", "v1");
prop.put("prop2", "v2");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n);
```

Uruchomienie kodu Java powinno teraz generować powiadomienie na urządzeniu docelowym.

## <a name="next-steps"></a><a name="next-steps"></a>Następne kroki

W tym temacie pokazano, jak utworzyć prosty klient protokołu Java REST dla Notification Hubs. W tym miejscu można wykonać następujące czynności:

* Pobierz pełny [zestaw SDK języka Java], który zawiera cały kod zestawu SDK.
* Odtwórz z przykładami:
  * [Wprowadzenie do Notification Hubs]
  * [Wyślij najświeższe wiadomości]
  * [Wyślij zlokalizowane najświeższe wiadomości]
  * [Wysyłanie powiadomień do uwierzytelnionych użytkowników]
  * [Wysyłanie powiadomień między platformami do uwierzytelnionych użytkowników]

[Zestaw SDK Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Wprowadzenie do Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Wyślij najświeższe wiadomości]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Wyślij zlokalizowane najświeższe wiadomości]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Wysyłanie powiadomień do uwierzytelnionych użytkowników]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Wysyłanie powiadomień między platformami do uwierzytelnionych użytkowników]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
