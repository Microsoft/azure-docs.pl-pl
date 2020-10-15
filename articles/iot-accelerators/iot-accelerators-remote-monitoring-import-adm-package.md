---
title: Pakiet importowania rozwiązania do monitorowania zdalnego — Azure | Microsoft Docs
description: W tym artykule opisano sposób importowania automatycznego pakietu zarządzania urządzeniami do akceleratora rozwiązania do zdalnego monitorowania
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 266e31ae9865c8fb427e06e89cd755e7ff38b27f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073873"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Zaimportuj automatyczny pakiet zarządzania urządzeniami do akceleratora rozwiązania do monitorowania zdalnego

Automatyczna konfiguracja zarządzania urządzeniami definiuje zmiany konfiguracji do wdrożenia w grupie urządzeń. W tym artykule założono, że deweloper w organizacji utworzył już automatyczną konfigurację zarządzania urządzeniami. Aby dowiedzieć się, jak projektant tworzy konfigurację, zobacz jeden z następujących IoT Hub artykuły z artykułami:

- [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę przy użyciu Azure Portal](../iot-hub/iot-hub-automatic-device-management.md)
- [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure](../iot-hub/iot-hub-automatic-device-management-cli.md)

Deweloper tworzy i testuje automatyczną konfigurację zarządzania urządzeniami w środowisku programistycznym. Gdy wszystko będzie gotowe, możesz zaimportować konfigurację do akceleratora rozwiązania do monitorowania zdalnego.

## <a name="export-a-configuration"></a>Eksportowanie konfiguracji

Użyj Azure Portal, aby wyeksportować automatyczną konfigurację zarządzania urządzeniami ze środowiska programistycznego:

1. W Azure Portal przejdź do centrum IoT Hub używanego do tworzenia i testowania urządzeń IoT. Kliknij pozycję **Konfiguracja urządzeń IoT**:

    [![Konfiguracja urządzenia IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Kliknij konfigurację, której chcesz użyć. Na stronie **szczegóły konfiguracji urządzenia** są wyświetlane następujące informacje:

    [![Szczegóły konfiguracji urządzenia IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Kliknij pozycję **Pobierz plik konfiguracji**:

    [![Pobierz plik konfiguracji](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Zapisz plik JSON jako plik lokalny o nazwie **configuration.json**.

Teraz masz plik zawierający automatyczną konfigurację zarządzania urządzeniami. W następnej sekcji należy zaimportować tę konfigurację jako pakiet do rozwiązania do zdalnego monitorowania.

## <a name="import-a-package"></a>Importowanie pakietu

Wykonaj poniższe kroki, aby zaimportować automatyczną konfigurację zarządzania urządzeniami jako pakiet do rozwiązania:

1. Przejdź do strony **pakiety** w interfejsie użytkownika sieci Web do monitorowania zdalnego:  ![ pakiety](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Kliknij pozycję **+ nowy pakiet**, wybierz opcję **Konfiguracja** jako typ pakietu, a następnie kliknij przycisk **Przeglądaj** , aby wybrać **configuration.jsw** pliku zapisanym w poprzedniej sekcji:

    ![Wybierz konfigurację](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Kliknij przycisk **Przekaż** , aby dodać pakiet do rozwiązania do zdalnego monitorowania:

    ![Przekazany pakiet](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Przekazano automatyczną konfigurację zarządzania urządzeniami jako pakiet. Na stronie **wdrożenia** można wdrożyć ten pakiet na podłączonych urządzeniach.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak utworzyć pakiet konfiguracyjny i zaimportować go do programu z rozwiązania do monitorowania zdalnego, następnym krokiem jest zapoznanie się z tematem jak [zarządzać urządzeniami połączonymi z zdalne monitorowaniem](iot-accelerators-remote-monitoring-bulk-configuration-update.md).