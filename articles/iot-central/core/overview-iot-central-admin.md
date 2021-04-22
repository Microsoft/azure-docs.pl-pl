---
title: Azure IoT Central administratora
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT. Ten artykuł zawiera omówienie roli administratora w programie IoT Central.
author: philmea
ms.author: philmea
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 658ebe027565a3acaf427a7b3dbf3963701069d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868649"
---
# <a name="iot-central-administrator-guide"></a>IoT Central administratora

*Ten artykuł dotyczy administratorów.*

Aplikacja IoT Central umożliwia monitorowanie milionów urządzeń i zarządzanie nimi w całym cyklu życia. Ten przewodnik jest dla administratorów, którzy zarządzają IoT Central aplikacjami.

W IoT Central administrator:

- Zarządza użytkownikami i rolami w aplikacji.
- Zarządza zabezpieczeniami, takimi jak uwierzytelnianie urządzeń.
- Konfiguruje ustawienia aplikacji.
- Uaktualnia aplikacje.
- Eksportuje i udostępnia aplikacje.
- Monitoruje kondycję aplikacji.

## <a name="users-and-roles"></a>Użytkownicy i role

IoT Central używa systemu kontroli dostępu opartej na rolach do zarządzania uprawnieniami użytkowników w aplikacji. IoT Central ma trzy wbudowane role dla administratorów, konstruktorów rozwiązań i operatorów. Administrator może tworzyć role niestandardowe z określonymi zestawami uprawnień. Administrator jest odpowiedzialny za dodawanie użytkowników do aplikacji i przypisywanie ich do ról.

Aby dowiedzieć się więcej, zobacz [Zarządzanie użytkownikami i rolami w IoT Central aplikacji.](howto-manage-users-roles.md)

## <a name="application-security"></a>Zabezpieczenia aplikacji

Urządzenia, które łączą się z aplikacją IoT Central zwykle używają certyfikatów X.509 lub sygnatur dostępu współdzielonego (SAS) jako poświadczeń. Administrator zarządza certyfikatami grupy lub kluczami, z których pochodzą poświadczenia urządzenia.

Aby dowiedzieć się więcej, zobacz [Rejestracja grupowa X.509,](concepts-get-connected.md#x509-group-enrollment)Rejestracja grupowa [sygnatur](concepts-get-connected.md#sas-group-enrollment)dostępu współdzielonego i Jak wycofać [certyfikaty urządzeń X.509.](how-to-roll-x509-certificates.md)

Administrator może również tworzyć tokeny interfejsu API używane przez aplikację kliency do uwierzytelniania w aplikacji IoT Central api i zarządzać nimi. Aplikacje klienckie używają interfejsu API REST do interakcji z IoT Central.

## <a name="configure-an-application"></a>Konfigurowanie aplikacji

Administrator może skonfigurować zachowanie i wygląd aplikacji IoT Central aplikacji. Aby dowiedzieć się więcej, zobacz:

- [Zmienianie nazwy i adresu URL aplikacji](howto-administer.md#change-application-name-and-url)
- [Dostosowywanie interfejsu użytkownika](howto-customize-ui.md)
- [Przenoszenie aplikacji do innych planów cenowych](howto-view-bill.md)
- [Konfigurowanie przekazywania plików](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Eksportowanie aplikacji

Administrator może:

- Utwórz kopię aplikacji, jeśli potrzebujesz tylko zduplikowanej kopii aplikacji. Na przykład do testowania może być potrzebna zduplikowana kopia.
- Utwórz szablon aplikacji na podstawie istniejącej aplikacji, jeśli planujesz utworzyć wiele kopii.

Aby dowiedzieć się więcej, zobacz [Eksportowanie aplikacji usługi Azure IoT.](howto-use-app-templates.md)

## <a name="migrate-to-a-new-version"></a>Migrowanie do nowej wersji

Administrator może migrować aplikację do nowszej wersji. Obecnie nowo utworzona aplikacja jest aplikacją w wersji 3. Administrator może potrzebować migracji aplikacji w wersji 2 do aplikacji w wersji 3.

Aby dowiedzieć się więcej, zobacz [Migrowanie aplikacji w wersji 2 IoT Central do wersji 3.](howto-migrate.md)

## <a name="monitor-application-health"></a>Monitorowanie kondycji aplikacji

Administrator może używać metryk IoT Central do oceny kondycji połączonych urządzeń i kondycji uruchomionych eksportów danych.

Aby wyświetlić metryki, administrator może używać wykresów w witrynie Azure Portal, interfejsie API REST, programie PowerShell lub zapytaniach interfejsu wiersza polecenia platformy Azure.

Aby dowiedzieć się więcej, [zobacz Monitorowanie ogólnej kondycji IoT Central aplikacji](howto-monitor-application-health.md).

## <a name="tools"></a>Narzędzia

Wiele narzędzi, których używasz jako administrator, jest dostępnych w sekcji **Administracja** każdej IoT Central aplikacji. Do wykonania niektórych zadań administracyjnych można również użyć następujących narzędzi:

- [Interfejs wiersza polecenia platformy Azure](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Witryna Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak administrować aplikacją Azure IoT Central, sugerowanym [](howto-manage-users-roles.md) następnym krokiem jest nauczenia się zarządzania użytkownikami i rolami w Azure IoT Central.
