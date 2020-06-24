---
title: Konfigurowanie ustawień eksportu w interfejsie API platformy Azure dla FHIR
description: W tym artykule opisano sposób konfigurowania ustawień eksportu w interfejsie API platformy Azure dla FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "84871806"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>Konfigurowanie Ustawienia eksportu i eksportowanie danych na konto magazynu

Interfejs API platformy Azure dla usługi FHIR obsługuje polecenie $export, które umożliwia eksportowanie danych z usługi Azure API for FHIR do konta magazynu.

W przypadku operacji eksportowania w interfejsie API platformy Azure dla FHIR należy wykonać cztery kroki:

1. Włącz zarządzaną tożsamość w interfejsie API platformy Azure dla usługi FHIR
2. Tworzenie konta usługi Azure Storage (jeśli nie zostało to zrobione) i przypisywanie uprawnień do usługi Azure API for FHIR do konta magazynu
3. Wybieranie konta magazynu w usłudze Azure API for FHIR jako konto magazynu eksportu
4. Wykonywanie operacji eksportowania przez wywoływanie $export polecenia w interfejsie API platformy Azure dla FHIR

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Włączanie zarządzanej tożsamości w interfejsie API platformy Azure dla usługi FHIR

Pierwszym krokiem podczas konfigurowania interfejsu API platformy Azure dla usługi FHIR na potrzeby eksportu jest włączenie w usłudze tożsamości zarządzanej przez system. Wszystkie zarządzane tożsamości na platformie Azure można przeczytać [tutaj](../active-directory/managed-identities-azure-resources/overview.md).

Aby to zrobić, przejdź do usługi Azure API for FHIR i wybierz pozycję blok tożsamości. Zmiana stanu na włączone spowoduje włączenie tożsamości zarządzanej w interfejsie API platformy Azure dla usługi FHIR.

![Włącz tożsamość zarządzaną](media/export-data/fhir-mi-enabled.png)

Teraz możemy przejść do następnego kroku i utworzyć konto magazynu i przypisać uprawnienia do naszej usługi.

## <a name="adding-permission-to-storage-account"></a>Dodawanie uprawnienia do konta magazynu

Następny krok eksportowania polega na przypisaniu uprawnienia do usługi Azure API for FHIR do zapisu na koncie magazynu.

Po utworzeniu konta magazynu przejdź do bloku Access Control (IAM) na koncie magazynu i wybierz pozycję Dodaj przypisania ról

![Włącz tożsamość zarządzaną](media/export-data/fhir-export-role-assignment.png)

Teraz dodamy współautor danych obiektu blob magazynu ról do naszej nazwy usługi.

![Włącz tożsamość zarządzaną](media/export-data/fhir-export-role-add.png)

Teraz jesteśmy gotowi do następnego kroku, w którym można wybrać konto magazynu w usłudze Azure API for FHIR jako domyślne konto magazynu dla $export.

## <a name="selecting-the-storage-account-for-export"></a>Wybieranie konta magazynu dla $export

Ostatnim krokiem przed wywołaniem $export polecenie polega na przypisaniu konta usługi Azure Storage, którego będzie używać usługa Azure API for FHIR do eksportowania danych do programu. W tym celu przejdź do bloku integracja w interfejsie API platformy Azure dla usługi FHIR w Azure Portal i wybierz konto magazynu 

![Włącz tożsamość zarządzaną](media/export-data/fhir-export-storage.png)

Gdy wszystko będzie gotowe do eksportowania danych przy użyciu polecenia $export.

## <a name="exporting-the-data-using-export-command"></a>Eksportowanie danych przy użyciu polecenia $export

Po skonfigurowaniu interfejsu API platformy Azure dla usługi FHIR na potrzeby eksportu można teraz użyć polecenia $export, aby wyeksportować dane z usługi do podanego konta magazynu. Aby dowiedzieć się, jak wywołać polecenie $export na serwerze FHIR, zapoznaj się z dokumentacją dotyczącą $export Specyfikacja pod adresem[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Należy pamiętać, że obecnie usługa Azure API for FHIR obsługuje tylko eksport na poziomie systemu, jak zdefiniowano w specyfikacji eksportu w [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Obecnie nie obsługujemy parametrów zapytania przy użyciu $export.

>[!div class="nextstepaction"]
>[Ustawienia dodatkowe](azure-api-for-fhir-additional-settings.md)