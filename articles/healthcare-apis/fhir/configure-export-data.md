---
title: Konfigurowanie ustawień eksportu w interfejsie API platformy Azure dla FHIR
description: W tym artykule opisano sposób konfigurowania ustawień eksportu w interfejsie API platformy Azure dla FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: bb728b81d4dad5d880c27d1ebe8f85f1508f8231
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019757"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Konfigurowanie Ustawienia eksportu i Konfigurowanie konta magazynu

Interfejs API platformy Azure dla usługi FHIR obsługuje polecenie $export, które umożliwia eksportowanie danych z usługi Azure API for FHIR do konta magazynu.

W celu skonfigurowania eksportu w interfejsie API platformy Azure dla FHIR należy wykonać trzy czynności:

1. Włącz zarządzaną tożsamość w interfejsie API platformy Azure dla usługi FHIR
2. Tworzenie konta usługi Azure Storage (jeśli nie zostało to zrobione) i przypisywanie uprawnień do usługi Azure API for FHIR do konta magazynu
3. Wybieranie konta magazynu w usłudze Azure API for FHIR jako konto magazynu eksportu

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Włączanie zarządzanej tożsamości w interfejsie API platformy Azure dla usługi FHIR

Pierwszym krokiem konfigurowania usługi Azure API for FHIR na potrzeby eksportu jest włączenie w usłudze tożsamości zarządzanej przez system. Wszystkie zarządzane tożsamości na platformie Azure można przeczytać [tutaj](../../active-directory/managed-identities-azure-resources/overview.md).

Aby to zrobić, przejdź do usługi Azure API for FHIR i wybierz pozycję blok tożsamości. Zmiana stanu na włączone spowoduje włączenie tożsamości zarządzanej w interfejsie API platformy Azure dla usługi FHIR.

![Włącz tożsamość zarządzaną](media/export-data/fhir-mi-enabled.png)

Teraz możemy przejść do następnego kroku i utworzyć konto magazynu i przypisać uprawnienia do naszej usługi.

## <a name="adding-permission-to-storage-account"></a>Dodawanie uprawnienia do konta magazynu

Następny krok eksportowania polega na przypisaniu uprawnienia do usługi Azure API for FHIR do zapisu na koncie magazynu.

Po utworzeniu konta magazynu przejdź do bloku Access Control (IAM) na koncie magazynu i wybierz pozycję Dodaj przypisania ról

![Eksportowanie przypisania roli](media/export-data/fhir-export-role-assignment.png)

Teraz dodamy współautor danych obiektu blob magazynu ról do naszej nazwy usługi.

![Dodaj rolę](media/export-data/fhir-export-role-add.png)

Teraz jesteśmy gotowi do następnego kroku, w którym można wybrać konto magazynu w usłudze Azure API for FHIR jako domyślne konto magazynu dla $export.

## <a name="selecting-the-storage-account-for-export"></a>Wybieranie konta magazynu dla $export

Ostatnim krokiem jest przypisanie konta usługi Azure Storage, które będzie używane przez usługę Azure API for FHIR do eksportowania danych do programu. W tym celu przejdź do bloku integracja w interfejsie API platformy Azure dla usługi FHIR w Azure Portal i wybierz konto magazynu

![Magazyn eksportu FHIR](media/export-data/fhir-export-storage.png)

Gdy wszystko będzie gotowe do eksportowania danych przy użyciu polecenia $export.

>[!div class="nextstepaction"]
>[Ustawienia dodatkowe](azure-api-for-fhir-additional-settings.md)
