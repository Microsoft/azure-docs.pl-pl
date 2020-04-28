---
title: Konfigurowanie i używanie emulatora magazynu w programie Visual Studio | Microsoft Docs
description: Konfigurowanie i używanie emulatora magazynu, narzędzia, które symuluje usługi obiektów blob, kolejek i tabel dostępne na platformie Azure na lokalnym komputerze deweloperskim.
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: a6f853924416cce2440ca15767044029b20e651f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75450726"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurowanie i używanie emulatora usługi Storage za pomocą programu Visual Studio

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Omówienie

Środowisko deweloperskie zestawu Azure SDK obejmuje emulator magazynu, narzędzie, które symuluje usługi obiektów blob, kolejek i tabel, które są dostępne na platformie Azure na lokalnym komputerze deweloperskim. Jeśli tworzysz usługę w chmurze korzystającą z usług Azure Storage lub pisząc dowolną aplikację zewnętrzną, która wywołuje usługi magazynu, możesz testować kod lokalnie na emulator magazynu. Narzędzia platformy Azure dla Microsoft Visual Studio integrują zarządzanie emulatorem magazynu w programie Visual Studio. Narzędzia platformy Azure inicjują bazę danych emulatora magazynu przy pierwszym użyciu, uruchamiają usługę emulatora magazynu podczas uruchamiania lub debugowania kodu z programu Visual Studio i zapewniają dostęp tylko do odczytu do danych emulatora magazynu za pośrednictwem Eksplorator usługi Azure Storage.

Aby uzyskać szczegółowe informacje na temat emulatora magazynu, w tym wymagania systemowe i instrukcje dotyczące konfiguracji niestandardowej, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](storage/common/storage-use-emulator.md).

> [!NOTE]
> Istnieją pewne różnice między funkcjami symulacji emulatora magazynu a usługami Azure Storage. Zapoznaj się z [różnicami między emulatorem magazynu a usługami Azure Storage](storage/common/storage-use-emulator.md) w dokumentacji zestawu Azure SDK, aby uzyskać informacje na temat określonych różnic.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurowanie parametrów połączenia dla emulatora magazynu

Aby uzyskać dostęp do emulatora magazynu z kodu w ramach roli, należy skonfigurować parametry połączenia wskazujące emulator magazynu i później można je zmienić, aby wskazywały na konto usługi Azure Storage. Parametry połączenia to ustawienia konfiguracji, które rola może odczytać w czasie wykonywania, aby nawiązać połączenie z kontem magazynu. Aby uzyskać więcej informacji na temat sposobu tworzenia parametrów połączenia, zobacz [Konfigurowanie parametrów połączenia usługi Azure Storage](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Można zwrócić odwołanie do konta emulatora magazynu z kodu za pomocą właściwości **DevelopmentStorageAccount** . To podejście działa prawidłowo, jeśli chcesz uzyskać dostęp do emulatora magazynu z kodu, ale jeśli planujesz opublikowanie aplikacji na platformie Azure, musisz utworzyć parametry połączenia w celu uzyskania dostępu do konta usługi Azure Storage i zmodyfikować swój kod, aby używał tych parametrów połączenia przed opublikowaniem. Jeśli przełączasz się między kontem emulatora magazynu i kontem usługi Azure Storage często, parametry połączenia upraszczają ten proces.

## <a name="initializing-and-running-the-storage-emulator"></a>Inicjowanie i uruchamianie emulatora magazynu

Możesz określić, że podczas uruchamiania lub debugowania usługi w programie Visual Studio program Visual Studio automatycznie uruchamia emulator magazynu. W Eksplorator rozwiązań otwórz menu skrótów dla projektu **platformy Azure** i wybierz polecenie **Właściwości**. Na karcie **programowanie** na liście **Start emulatora usługi Azure Storage** wybierz **wartość PRAWDA** (jeśli nie jest jeszcze ustawiona na tę wartość).  Niektóre typy projektów nie mają karty **programowanie** . W takim przypadku można włączyć lub wyłączyć uruchamianie emulatora magazynu przez ustawienie `StartDevelopmentStorage` elementu w pliku projektu. Ustaw wartość na **true** , aby ją włączyć, lub **wartość false** , aby ją wyłączyć.  Na przykład w projekcie Azure Functions Otwórz plik projektu do edycji i modyfikacji kodu XML w następujący sposób:

```xml
  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
    <StartDevelopmentStorage>True</StartDevelopmentStorage>
  </PropertyGroup>
```

Podczas pierwszego uruchomienia lub debugowania usługi w programie Visual Studio Emulator magazynu uruchamia proces inicjalizacji. Ten proces rezerwuje porty lokalne dla emulatora magazynu i tworzy bazę danych emulatora magazynu. Po zakończeniu tego procesu nie trzeba uruchamiać go ponownie, chyba że baza danych emulatora magazynu zostanie usunięta.

> [!NOTE]
> Począwszy od 2012 czerwca wersji narzędzi platformy Azure, Emulator magazynu jest domyślnie uruchamiany w programie SQL Express LocalDB. We wcześniejszych wersjach narzędzi platformy Azure emulator magazynu jest uruchamiany z domyślnym wystąpieniem programu SQL Express 2005 lub 2008, które należy zainstalować przed zainstalowaniem zestawu Azure SDK. Możesz również uruchomić emulator magazynu dla nazwanego wystąpienia programu SQL Express lub nazwanego lub domyślnego wystąpienia Microsoft SQL Server. Jeśli trzeba skonfigurować emulator magazynu do uruchamiania względem wystąpienia innego niż domyślne, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](storage/common/storage-use-emulator.md).

Emulator magazynu udostępnia interfejs użytkownika służący do wyświetlania stanu lokalnych usług magazynu oraz uruchamiania, zatrzymywania i resetowania. Po uruchomieniu usługi emulatora magazynu można wyświetlić interfejs użytkownika lub uruchomić lub zatrzymać usługę, klikając prawym przyciskiem myszy ikonę obszaru powiadomień dla Emulator Microsoft Azure na pasku zadań systemu Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Wyświetlanie danych emulatora magazynu w Eksplorator serwera

Węzeł usługi Azure Storage w Eksplorator serwera umożliwia wyświetlanie danych i zmienianie ustawień dla danych obiektów blob i tabel na kontach magazynu, w tym emulatora magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami BLOB Storage platformy Azure przy użyciu Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) .

