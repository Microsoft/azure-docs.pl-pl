---
title: Pobieranie zestawu SDK czujnika usługi Azure urządzenia Kinect
description: Dowiedz się, jak pobrać i zainstalować zestaw SDK czujnika usługi Azure urządzenia Kinect w systemach Windows i Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, urządzenia Kinect, SDK, Pobierz aktualizację, Najnowsza, dostępna, zainstaluj
ms.openlocfilehash: 48a3df3962796cf561057e8ec85b754c974200f4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358509"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Pobieranie zestawu SDK czujnika usługi Azure urządzenia Kinect

Ta strona zawiera linki do pobrania dla każdej wersji zestawu SDK czujnika usługi Azure urządzenia Kinect. Instalator udostępnia wszystkie pliki, które są konieczne do opracowania w usłudze Azure urządzenia Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Zawartość zestawu SDK czujnika usługi Azure urządzenia Kinect

- Nagłówki i biblioteki do kompilowania aplikacji przy użyciu usługi Azure urządzenia Kinect DK.
- Redystrybucyjne bibliotek DLL wymaganych przez aplikacje korzystające z platformy Azure urządzenia Kinect DK.
- [Przeglądarka Azure urządzenia Kinect](azure-kinect-viewer.md).
- [Usługa Azure urządzenia Kinect Recorder](azure-kinect-recorder.md).
- [Narzędzie oprogramowania układowego Azure urządzenia Kinect](azure-kinect-firmware-tool.md).

## <a name="windows-download-link"></a>Link do pobierania systemu Windows

[Instalator](https://download.microsoft.com/download/3/d/6/3d6d9e99-a251-4cf3-8c6a-8e108e960b4b/Azure%20Kinect%20SDK%201.4.1.exe)  |  firmy Microsoft [Kod źródłowy usługi GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> Podczas instalowania zestawu SDK Pamiętaj o ścieżce instalacji programu. Na przykład "C:\Program Files\Azure urządzenia Kinect SDK 1,2". W tej ścieżce znajdziesz narzędzia, do których odwołuje się artykuł.

Poprzednie wersje zestawu SDK i oprogramowania układowego czujnika urządzenia Kinect platformy Azure można znaleźć w witrynie [GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="linux-installation-instructions"></a>Instrukcje instalacji systemu Linux

Obecnie jedyna obsługiwana dystrybucja to Ubuntu 18,04. Aby zażądać obsługi innych dystrybucji, zobacz [Tę stronę](https://aka.ms/azurekinectfeedback).

Najpierw musisz skonfigurować [repozytorium pakietów firmy Microsoft](https://packages.microsoft.com/), postępując zgodnie z instrukcjami znajdującymi się [tutaj](/windows-server/administration/linux-package-repository-for-microsoft-software).

Teraz można zainstalować wymagane pakiety. `k4a-tools`Pakiet obejmuje [usługę Azure urządzenia Kinect Viewer](azure-kinect-viewer.md), [usługę Azure urządzenia Kinect Recorder](record-sensor-streams-file.md)i [Narzędzie oprogramowania układowego Azure urządzenia Kinect](azure-kinect-firmware-tool.md). Aby go zainstalować, uruchom polecenie

 `sudo apt install k4a-tools`

 `libk4a<major>.<minor>-dev`Pakiet zawiera nagłówki i pliki CMAKE do skompilowania `libk4a` .
`libk4a<major>.<minor>`Pakiet zawiera obiekty udostępnione, które są konieczne do uruchamiania plików wykonywalnych, które są zależne od programu `libk4a` .

 Samouczki podstawowe wymagają `libk4a<major>.<minor>-dev` pakietu. Aby go zainstalować, uruchom polecenie

 `sudo apt install libk4a1.1-dev`

Jeśli polecenie zakończy się pomyślnie, zestaw SDK jest gotowy do użycia.

## <a name="change-log-and-older-versions"></a>Zmień dziennik i starsze wersje

Dziennik zmian zestawu Azure urządzenia Kinect sensor SDK można znaleźć [tutaj](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Jeśli potrzebujesz starszej wersji zestawu SDK czujnika usługi Azure urządzenia Kinect, Znajdź ją [tutaj](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie zestawu Azure Kinect DK](set-up-azure-kinect-dk.md)