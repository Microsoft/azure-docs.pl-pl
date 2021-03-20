---
title: Rejestrowanie zestawu mowy SDK — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak włączyć rejestrowanie w zestawie mowy SDK (C++, C#, Python, zamierzenia-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f044596cf084e68b6c9ca10e3da4fbdf4c8c2062
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026526"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Włączanie rejestrowania w zestawie mowy SDK

Logowanie do pliku to opcjonalna funkcja dla zestawu Speech SDK. Podczas rejestrowania programistycznego są dostępne dodatkowe informacje i Diagnostyka z podstawowych składników zestawu Speech SDK. Można ją włączyć, ustawiając właściwość `Speech_LogFilename` obiektu konfiguracji mowy na lokalizację i nazwę pliku dziennika. Rejestrowanie zostanie uaktywnione globalnie, gdy aparat rozpoznawania zostanie utworzony na podstawie tej konfiguracji i nie będzie można go wyłączyć później. Nie można zmienić nazwy pliku dziennika podczas uruchamiania sesji rejestrowania.

> [!NOTE]
> Funkcja rejestrowania jest dostępna, ponieważ zestaw SDK mowy w wersji 1.4.0 we wszystkich obsługiwanych językach programowania zestawu SDK mowy, z wyjątkiem języka JavaScript.

## <a name="sample"></a>Przykład

Nazwa pliku dziennika jest określona w obiekcie konfiguracji. Przy `SpeechConfig` użyciu przykładu i przy założeniu, że utworzono wystąpienie o nazwie `config` :

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Aparat rozpoznawania można utworzyć przy użyciu obiektu konfiguracji. Spowoduje to włączenie rejestrowania dla wszystkich aparatów rozpoznawania.

> [!NOTE]
> Jeśli utworzysz `SpeechSynthesizer` obiekt z obiektu konfiguracji, nie będzie on włączał rejestrowania. Jeśli rejestrowanie jest włączone, otrzymasz również diagnostykę z `SpeechSynthesizer` .

## <a name="create-a-log-file-on-different-platforms"></a>Utwórz plik dziennika na różnych platformach

W przypadku systemu Windows lub Linux plik dziennika może znajdować się w dowolnej ścieżce, do której użytkownik ma uprawnienia do zapisu. Uprawnienia do zapisu w lokalizacjach systemu plików w innych systemach operacyjnych mogą być domyślnie ograniczone lub ograniczone.

### <a name="universal-windows-platform-uwp"></a>Platforma uniwersalna systemu Windows (UWP)

Aplikacje platformy UWP muszą być umieszczane w plikach dziennika w jednej z lokalizacji danych aplikacji (lokalnego, mobilnego lub tymczasowego). Plik dziennika można utworzyć w folderze aplikacji lokalnej:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Więcej informacji o uprawnieniach dostępu do plików dla aplikacji platformy UWP jest dostępnych [tutaj](/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Plik dziennika można zapisać w magazynie wewnętrznym, zewnętrznym lub katalogu pamięci podręcznej. Pliki utworzone w magazynie wewnętrznym lub katalogu pamięci podręcznej są prywatne dla aplikacji. Zaleca się utworzenie pliku dziennika w zewnętrznym magazynie.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Powyższy kod zapisze plik dziennika do magazynu zewnętrznego w katalogu głównym katalogu specyficznego dla aplikacji. Użytkownik może uzyskać dostęp do pliku za pomocą Menedżera plików (zazwyczaj w programie `Android/data/ApplicationName/logfile.txt` ). Plik zostanie usunięty, gdy aplikacja zostanie odinstalowana.

Musisz również zażądać `WRITE_EXTERNAL_STORAGE` uprawnień w pliku manifestu:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Więcej informacji na temat magazynu danych i plików dla aplikacji systemu Android jest dostępnych [tutaj](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Dostępne są tylko katalogi wewnątrz piaskownicy aplikacji. Pliki można tworzyć w katalogach Documents, Library i temp. Pliki w katalogu dokumenty mogą być udostępniane użytkownikowi. Poniższy fragment kodu przedstawia tworzenie pliku dziennika w katalogu dokumentu aplikacji:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Aby uzyskać dostęp do utworzonego pliku, Dodaj następujące właściwości do `Info.plist` listy właściwości aplikacji:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Więcej informacji o systemie plików iOS można znaleźć [tutaj](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)