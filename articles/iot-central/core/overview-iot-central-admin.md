---
title: Podręcznik administratora usługi Azure IoT Central
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT. Ten artykuł zawiera omówienie roli administrator w programie IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110495"
---
# <a name="iot-central-administrator-guide"></a>Podręcznik administratora IoT Central

*Ten artykuł ma zastosowanie do administratorów programu.*

Aplikacja IoT Central umożliwia monitorowanie milionów urządzeń i zarządzanie nimi w całym cyklu życia. Ten przewodnik jest przeznaczony dla administratorów, którzy zarządzają IoT Central aplikacjami.

W IoT Central administrator:

- Zarządza użytkownikami i rolami w aplikacji.
- Zarządza zabezpieczeniami, takimi jak uwierzytelnianie urządzeń.
- Konfiguruje ustawienia aplikacji.
- Uaktualnia aplikacje.
- Eksportuje i udostępnia aplikacje.
- Monitoruje kondycję aplikacji.

## <a name="users-and-roles"></a>Użytkownicy i role

IoT Central używa systemu kontroli dostępu opartego na rolach do zarządzania uprawnieniami użytkowników w ramach aplikacji. IoT Central ma trzy wbudowane role dla administratorów, konstruktorów rozwiązań i operatorów. Administrator może tworzyć role niestandardowe z określonymi zestawami uprawnień. Administrator jest odpowiedzialny za Dodawanie użytkowników do aplikacji i przypisywanie ich do ról.

Aby dowiedzieć się więcej, zobacz [Zarządzanie użytkownikami i rolami w aplikacji IoT Central](howto-manage-users-roles.md).

## <a name="application-security"></a>Zabezpieczenia aplikacji

Urządzenia łączące się z aplikacją IoT Central zwykle używają certyfikatów X. 509 lub sygnatur dostępu współdzielonego (SAS) jako poświadczeń. Administrator zarządza certyfikatami grup lub kluczami, z których pochodzą poświadczenia urządzenia.

Aby dowiedzieć się więcej, zobacz [rejestrowanie grup x. 509](concepts-get-connected.md#x509-group-enrollment), [rejestrowanie grup SAS](concepts-get-connected.md#sas-group-enrollment)i [sposób przytaczania certyfikatów urządzeń x. 509](how-to-roll-x509-certificates.md).

Administrator może również tworzyć tokeny interfejsu API używane przez aplikację kliencką do uwierzytelniania w aplikacji IoT Central i zarządzać nimi. Aplikacje klienckie używają interfejsu API REST do współpracy z IoT Central.

## <a name="configure-an-application"></a>Konfigurowanie aplikacji

Administrator może skonfigurować zachowanie i wygląd aplikacji IoT Central. Aby dowiedzieć się więcej, zobacz:

- [Zmień nazwę i adres URL aplikacji](howto-administer.md#change-application-name-and-url)
- [Dostosowywanie interfejsu użytkownika](howto-customize-ui.md)
- [Przenoszenie aplikacji do innych planów cenowych](howto-view-bill.md)
- [Konfigurowanie przekazywania plików](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Eksportowanie aplikacji

Administrator może:

- Utwórz kopię aplikacji, jeśli potrzebujesz tylko zduplikowanej kopii aplikacji. Na przykład może być wymagana zduplikowana kopia do testowania.
- Utwórz szablon aplikacji z istniejącej aplikacji, jeśli planujesz utworzyć wiele kopii.

Aby dowiedzieć się więcej, zobacz [Eksportowanie aplikacji Azure IoT](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Migrowanie do nowej wersji

Administrator może przeprowadzić migrację aplikacji do nowszej wersji. Obecnie nowo utworzona aplikacja jest aplikacją v3. Administrator może potrzebować migracji aplikacji w wersji 2 do aplikacji v3.

Aby dowiedzieć się więcej, zobacz [Migrowanie aplikacji V2 IoT Central do wersji v3](howto-migrate.md).

## <a name="monitor-application-health"></a>Monitorowanie kondycji aplikacji

Administrator może używać metryk IoT Central do oceny kondycji połączonych urządzeń i kondycji wykonywanych eksportów danych.

Aby wyświetlić metryki, administrator może używać wykresów w Azure Portal, interfejsie API REST lub kwerendach interfejsu wiersza polecenia platformy Azure.

Aby dowiedzieć się więcej, zobacz [monitorowanie ogólnej kondycji aplikacji IoT Central](howto-monitor-application-health.md).

## <a name="tools"></a>Narzędzia

Wiele narzędzi używanych jako administrator jest dostępnych w sekcji **Administracja** każdej IoT Central aplikacji. Do wykonania niektórych zadań administracyjnych można także użyć następujących narzędzi:

- [Interfejs wiersza polecenia platformy Azure](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Witryna Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak administrować aplikacją IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Zarządzanie użytkownikami i rolami](howto-manage-users-roles.md) w usłudze Azure IoT Central.
