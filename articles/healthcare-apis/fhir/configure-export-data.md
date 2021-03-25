---
title: Konfigurowanie ustawień eksportu w interfejsie API platformy Azure dla FHIR
description: W tym artykule opisano sposób konfigurowania ustawień eksportu w interfejsie API platformy Azure dla FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046995"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Konfigurowanie Ustawienia eksportu i Konfigurowanie konta magazynu

Interfejs API platformy Azure dla usługi FHIR obsługuje polecenie $export, które umożliwia eksportowanie danych z usługi Azure API for FHIR do konta magazynu.

W celu skonfigurowania eksportu w interfejsie API platformy Azure dla FHIR należy wykonać trzy czynności:

1. Włącz zarządzaną tożsamość w interfejsie API platformy Azure dla usługi FHIR.
2. Utworzenie konta usługi Azure Storage (jeśli nie zostało to zrobione) i przypisanie uprawnień do usługi Azure API for FHIR do konta magazynu.
3. Wybierz konto magazynu w interfejsie API platformy Azure dla FHIR jako konto magazynu eksportu.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Włączanie zarządzanej tożsamości w interfejsie API platformy Azure dla usługi FHIR

Pierwszym krokiem konfigurowania usługi Azure API for FHIR na potrzeby eksportu jest włączenie w usłudze tożsamości zarządzanej przez system. Aby uzyskać więcej informacji na temat tożsamości zarządzanych na platformie Azure, zobacz [Informacje o zarządzanych tożsamościach dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Aby to zrobić, przejdź do usługi Azure API for FHIR i wybierz pozycję **Identity (tożsamość**). Zmiana stanu na **włączone** spowoduje włączenie tożsamości zarządzanej w interfejsie API platformy Azure dla usługi FHIR.

![Włącz tożsamość zarządzaną](media/export-data/fhir-mi-enabled.png)

Teraz możesz przejść do następnego kroku, tworząc konto magazynu i przypisując mu uprawnienie do naszej usługi.

## <a name="adding-permission-to-storage-account"></a>Dodawanie uprawnienia do konta magazynu

Następny krok eksportowania polega na przypisaniu uprawnienia do usługi Azure API for FHIR do zapisu na koncie magazynu.

Po utworzeniu konta magazynu przejdź do obszaru **Access Control (IAM)** na koncie magazynu i wybierz pozycję **Dodaj przypisanie roli**.

![Eksportowanie przypisania roli](media/export-data/fhir-export-role-assignment.png)

Tutaj należy dodać **współautor danych obiektów blob magazynu** roli do naszej nazwy usługi, a następnie wybrać pozycję **Zapisz**.

![Dodaj rolę](media/export-data/fhir-export-role-add.png)

Teraz możesz przystąpić do wybierania konta magazynu w usłudze Azure API for FHIR jako domyślnego konta magazynu dla $export.

## <a name="selecting-the-storage-account-for-export"></a>Wybieranie konta magazynu dla $export

Ostatnim krokiem jest przypisanie konta usługi Azure Storage, które będzie używane przez usługę Azure API for FHIR do eksportowania danych do programu. W tym celu przejdź do pozycji **integracja** w interfejsie API platformy Azure dla usługi FHIR i wybierz konto magazynu.

![Magazyn eksportu FHIR](media/export-data/fhir-export-storage.png)

Po zakończeniu tego końcowego kroku możesz teraz przystąpić do eksportowania danych przy użyciu polecenia $export.

> [!Note]
> Jako miejsce docelowe dla operacji $export mogą być rejestrowane tylko konta magazynu w tej samej subskrypcji, co w przypadku usługi Azure API for FHIR.

Aby uzyskać więcej informacji na temat konfigurowania ustawień bazy danych, kontroli dostępu, włączania rejestrowania diagnostycznego i używania nagłówków niestandardowych do dodawania danych do dzienników inspekcji, zobacz:

>[!div class="nextstepaction"]
>[Ustawienia dodatkowe](azure-api-for-fhir-additional-settings.md)
