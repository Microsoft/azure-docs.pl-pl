---
title: Pobieranie zestawu SDK czujnika usługi Azure urządzenia Kinect
description: Dowiedz się, jak pobrać i zainstalować zestaw SDK czujnika usługi Azure urządzenia Kinect w systemach Windows i Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, urządzenia Kinect, SDK, Pobierz aktualizację, Najnowsza, dostępna, zainstaluj
ms.openlocfilehash: 2c23977c7e01a9137b72b44d1bdc0e1373bafa0a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "85277594"
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

[Instalator](https://download.microsoft.com/download/4/5/a/45aa3917-45bf-4f24-b934-5cff74df73e1/Azure%20Kinect%20SDK%201.4.0.exe)  |  firmy Microsoft [Kod źródłowy usługi GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> Podczas instalowania zestawu SDK Pamiętaj o ścieżce instalacji programu. Na przykład "C:\Program Files\Azure urządzenia Kinect SDK 1,2". W tej ścieżce znajdziesz narzędzia, do których odwołuje się artykuł.

Poprzednie wersje zestawu SDK i oprogramowania układowego czujnika urządzenia Kinect platformy Azure można znaleźć w witrynie [GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="linux-installation-instructions"></a>Instrukcje instalacji systemu Linux

Obecnie jedyna obsługiwana dystrybucja to Ubuntu 18,04. Aby zażądać obsługi innych dystrybucji, zobacz [Tę stronę](https://aka.ms/azurekinectfeedback).

Najpierw musisz skonfigurować [repozytorium pakietów firmy Microsoft](https://packages.microsoft.com/), postępując zgodnie z instrukcjami znajdującymi się [tutaj](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

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
