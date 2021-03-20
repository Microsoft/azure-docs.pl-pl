---
title: Konfigurowanie programu Microsoft Endpoint Configuration Manager — Azure
description: Jak skonfigurować program Microsoft Endpoint Configuration Manager w celu wdrażania aktualizacji oprogramowania w wielu sesjach systemu Windows 10 Enterprise na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88010127"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Konfigurowanie Configuration Manager punktu końcowego firmy Microsoft

W tym artykule wyjaśniono, jak skonfigurować program Microsoft Endpoint Configuration Manager tak, aby automatycznie stosował aktualizacje do hosta pulpitów wirtualnych systemu Windows z uruchomioną obsługą wielu sesji systemu Windows 10 Enterprise.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować to ustawienie, potrzebne będą następujące elementy:

   - Upewnij się, że na maszynach wirtualnych został zainstalowany agent programu Microsoft Endpoint Configuration Manager.
   - Upewnij się, że wersja programu Microsoft Endpoint Configuration Manager ma co najmniej poziom rozgałęzienia 1906. Aby uzyskać najlepsze wyniki, użyj poziomu rozgałęzienia 1910 lub wyższego.

## <a name="configure-the-software-update-point"></a>Konfigurowanie punktu aktualizacji oprogramowania

Aby otrzymywać aktualizacje dla wielu sesji dla systemu Windows 10 Enterprise, należy włączyć system Windows Server w wersji 1903 lub nowszej jako produkt w programie Microsoft Endpoint Configuration Manager. To ustawienie produktu ma zastosowanie również w przypadku wdrażania aktualizacji w systemach przy użyciu usługi Windows Server Update.

Aby otrzymywać aktualizacje:

1. Otwórz program Microsoft Endpoint Configuration Manager i wybierz pozycję **Lokacje**.
2. Wybierz pozycję **Konfiguruj składniki lokacji**.
3. W menu rozwijanym wybierz pozycję **punkt aktualizacji oprogramowania** .
4. Wybierz kartę **Products** (Produkty).
5. Zaznacz pole wyboru z **systemem Windows Server w wersji 1903 lub nowszej**.
6. Przejdź do pozycji **Biblioteka oprogramowania**  >  **Przegląd**  >  **oprogramowanie aktualizacje**  >  **wszystkie aktualizacje oprogramowania** i wybierz pozycję **Synchronizuj aktualizacje oprogramowania**.
7. Sprawdź plik plik Wsyncmgr. log w **plikach Program Files**  >  **Microsoft Configuration Manager**  >  **Logs** , aby upewnić się, że zmiany zostały zapisane. Synchronizowanie aktualizacji może potrwać kilka minut.

## <a name="create-a-query-based-collection"></a>Tworzenie kolekcji opartej na zapytaniach

Aby utworzyć kolekcję wielosesyjnych maszyn wirtualnych z systemem Windows 10 Enterprise, kolekcja oparta na zapytaniach może służyć do identyfikowania określonej jednostki SKU systemu operacyjnego.

Aby utworzyć kolekcję:

1. Wybierz pozycję **zasoby i zgodność**.
2. Przejdź do **omówienia**  >  **Kolekcje urządzeń** , a następnie kliknij prawym przyciskiem myszy pozycję **Kolekcje urządzeń** , a następnie wybierz pozycję **Utwórz kolekcję urządzeń** z menu rozwijanego.
3. Na karcie **Ogólne** w wyświetlonym menu Wprowadź nazwę opisującą kolekcję w polu **Nazwa** . W polu **komentarz** możesz podać dodatkowe informacje opisujące zawartość kolekcji. W obszarze **ograniczanie kolekcji** Zdefiniuj maszyny, które są umieszczane w kwerendzie kolekcji.
4. Na karcie **reguły członkostwa** Dodaj regułę dla zapytania, wybierając pozycję **Dodaj regułę**, a następnie wybierając pozycję **reguła zapytania**.
5. We **właściwościach reguły zapytania** wprowadź nazwę reguły, a następnie zdefiniuj parametry reguły, wybierając **Edytuj instrukcję zapytania**.
6. Wybierz pozycję **Pokaż instrukcję zapytania**.
7. W instrukcji wprowadź następujący ciąg:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. Wybierz **przycisk OK** , aby utworzyć kolekcję.
9. Aby sprawdzić, czy kolekcja została pomyślnie utworzona, przejdź do pozycji **zasoby i zgodność**  >  **Przegląd**  >  **Kolekcje urządzeń**.
