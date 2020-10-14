---
title: Konfigurowanie kluczy zarządzanych przez klienta dla interfejsu API platformy Azure dla usługi FHIR
description: Korzystaj z własnej funkcji klucza obsługiwanej w interfejsie API platformy Azure dla FHIR za pomocą Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 535bb5d21beafaabb50769a6c03478dbd1f942d4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92051353"
---
# <a name="configure-customer-managed-keys"></a>Konfigurowanie kluczy zarządzanych przez klienta

Po utworzeniu nowego konta usługi Azure API for FHIR dane są szyfrowane domyślnie przy użyciu kluczy zarządzanych przez firmę Microsoft. Teraz możesz dodać drugą warstwę szyfrowania danych przy użyciu własnego klucza, który można wybrać i zarządzać nimi.

Na platformie Azure jest to zwykle realizowane przy użyciu klucza szyfrowania w Azure Key Vault klienta (AKV). W przypadku usługi Azure SQL, usługi Azure Storage i Cosmos DB są to przykłady, które umożliwiają dzisiejszą obsługę tej funkcji. Interfejs API platformy Azure dla usługi FHIR korzysta z tej obsługi Cosmos DB. Podczas tworzenia konta będzie dostępna opcja określania identyfikatora URI klucza AKV. Ten klucz zostanie przekazany do Cosmos DB po zainicjowaniu obsługi konta bazy danych. Po utworzeniu żądania FHIR Cosmos DB Pobiera klucz i używa go do szyfrowania/odszyfrowywania danych. Aby rozpocząć, możesz skorzystać z następujących linków:

- [Zarejestruj dostawcę zasobów Azure Cosmos DB dla subskrypcji platformy Azure](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#register-resource-provider) 
- [Konfigurowanie wystąpienia AKV](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#configure-your-azure-key-vault-instance)
-  [Dodawanie zasad dostępu do wystąpienia AKV](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#add-an-access-policy-to-your-azure-key-vault-instance)
- [Wygeneruj klucz w AKV](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#generate-a-key-in-azure-key-vault)

Po utworzeniu konta usługi Azure API for FHIR na Azure Portal można zobaczyć opcję konfiguracji "szyfrowanie danych" w obszarze "ustawienia bazy danych" na karcie "dodatkowe ustawienia". Domyślnie zostanie wybrana opcja klucz zarządzany przez usługę. Możesz określić klucz AKV w tym miejscu, wybierając opcję "klucz zarządzany przez klienta". W tym miejscu możesz wprowadzić skopiowany identyfikator URI klucza.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Tworzenie interfejsu API platformy Azure dla usługi FHIR":::

Można też wybrać klucz z wybranego klucza:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Tworzenie interfejsu API platformy Azure dla usługi FHIR" w następujący sposób. Nie można już zmodyfikować opcji konfiguracji. Można jednak zmodyfikować i zaktualizować klucz.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Tworzenie interfejsu API platformy Azure dla usługi FHIR"::: (Baza danych)

Ponadto można utworzyć nową wersję określonego klucza, po którym dane będą szyfrowane za pomocą nowej wersji bez żadnych przerw w świadczeniu usług. Możesz również usunąć dostęp do klucza, aby usunąć dostęp do danych.
