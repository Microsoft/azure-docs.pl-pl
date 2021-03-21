---
title: Pobieranie zestawu SDK czujnika usługi Azure urządzenia Kinect
description: Dowiedz się, jak pobrać i zainstalować zestaw SDK czujnika usługi Azure urządzenia Kinect w systemach Windows i Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, urządzenia Kinect, SDK, Pobierz aktualizację, Najnowsza, dostępna, zainstaluj
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179633"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Pobieranie zestawu SDK czujnika usługi Azure urządzenia Kinect

Ta strona zawiera linki do pobrania dla każdej wersji zestawu SDK czujnika usługi Azure urządzenia Kinect. Instalator udostępnia wszystkie pliki, które są konieczne do opracowania w usłudze Azure urządzenia Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Zawartość zestawu SDK czujnika usługi Azure urządzenia Kinect

- Nagłówki i biblioteki do kompilowania aplikacji przy użyciu usługi Azure urządzenia Kinect DK.
- Redystrybucyjne bibliotek DLL wymaganych przez aplikacje korzystające z platformy Azure urządzenia Kinect DK.
- [Przeglądarka Azure urządzenia Kinect](azure-kinect-viewer.md).
- [Usługa Azure urządzenia Kinect Recorder](azure-kinect-recorder.md).
- [Narzędzie oprogramowania układowego Azure urządzenia Kinect](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Instrukcje dotyczące instalacji systemu Windows

W [tym miejscu](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)można znaleźć szczegóły instalacji najnowszych i wcześniejszych wersji zestawu SDK i oprogramowania układowego czujnika usługi Azure urządzenia Kinect.

Kod źródłowy można znaleźć [tutaj](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).

> [!NOTE]
> Podczas instalowania zestawu SDK Pamiętaj o ścieżce instalacji programu. Na przykład "C:\Program Files\Azure urządzenia Kinect SDK 1,2". W tej ścieżce znajdziesz narzędzia, do których odwołuje się artykuł.

## <a name="linux-installation-instructions"></a>Instrukcje instalacji systemu Linux

Obecnie jedyna obsługiwana dystrybucja to Ubuntu 18,04. Aby zażądać obsługi innych dystrybucji, zobacz [Tę stronę](https://aka.ms/azurekinectfeedback).

Najpierw musisz skonfigurować [repozytorium pakietów firmy Microsoft](https://packages.microsoft.com/), postępując zgodnie z instrukcjami znajdującymi się [tutaj](/windows-server/administration/linux-package-repository-for-microsoft-software).

Teraz można zainstalować wymagane pakiety. `k4a-tools`Pakiet obejmuje [usługę Azure urządzenia Kinect Viewer](azure-kinect-viewer.md), [usługę Azure urządzenia Kinect Recorder](record-sensor-streams-file.md)i [Narzędzie oprogramowania układowego Azure urządzenia Kinect](azure-kinect-firmware-tool.md). Aby zainstalować pakiet, uruchom polecenie:

`sudo apt install k4a-tools`
 
To polecenie powoduje zainstalowanie pakietów zależności, które są wymagane do poprawnego działania narzędzi, w tym najnowszej wersji programu `libk4a<major>.<minor>` . Musisz dodać reguły udev, aby uzyskać dostęp do usługi Azure urządzenia Kinect DK bez użytkownika głównego. Aby uzyskać instrukcje, zobacz [Konfiguracja urządzenia z systemem Linux](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup). Alternatywnie można uruchamiać aplikacje korzystające z urządzenia jako główne.

`libk4a<major>.<minor>-dev`Pakiet zawiera nagłówki i pliki CMAKE do kompilowania aplikacji/plików wykonywalnych `libk4a` .

`libk4a<major>.<minor>`Pakiet zawiera obiekty udostępnione, które są konieczne do uruchamiania aplikacji/plików wykonywalnych, które są zależne od programu `libk4a` .

Samouczki podstawowe wymagają `libk4a<major>.<minor>-dev` pakietu. Aby zainstalować pakiet, uruchom polecenie:

`sudo apt install libk4a<major>.<minor>-dev` 

Jeśli polecenie zakończy się pomyślnie, zestaw SDK jest gotowy do użycia.

Upewnij się, że zainstalowano zgodną wersję programu `libk4a<major>.<minor>` z programem `libk4a<major>.<minor>-dev` . Na przykład, jeśli zainstalujesz `libk4a4.1-dev` pakiet, zainstaluj odpowiedni `libk4a4.1` pakiet zawierający zgodną wersję plików obiektów udostępnionych. Aby uzyskać najnowszą wersję programu `libk4a` , zapoznaj się z linkami w następnej sekcji.

## <a name="change-log-and-older-versions"></a>Zmień dziennik i starsze wersje

Dziennik zmian zestawu Azure urządzenia Kinect sensor SDK można znaleźć [tutaj](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Jeśli potrzebujesz starszej wersji zestawu SDK czujnika usługi Azure urządzenia Kinect, Znajdź ją [tutaj](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie zestawu Azure Kinect DK](set-up-azure-kinect-dk.md)
