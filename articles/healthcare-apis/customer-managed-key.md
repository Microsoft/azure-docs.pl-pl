---
title: Konfigurowanie kluczy zarządzanych przez klienta dla interfejsu API platformy Azure dla usługi FHIR
description: Korzystaj z własnej funkcji klucza obsługiwanej w interfejsie API platformy Azure dla FHIR za pomocą Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: ae78aa80594e46b02d77adcafed961e801780d4f
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430263"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Konfigurowanie kluczy zarządzanych przez klienta w spoczynku

Po utworzeniu nowego konta usługi Azure API for FHIR dane są szyfrowane domyślnie przy użyciu kluczy zarządzanych przez firmę Microsoft. Teraz możesz dodać drugą warstwę szyfrowania danych przy użyciu własnego klucza, który można wybrać i zarządzać nimi.

Na platformie Azure jest to zwykle realizowane przy użyciu klucza szyfrowania w Azure Key Vault klienta. W przypadku usługi Azure SQL, usługi Azure Storage i Cosmos DB są to przykłady, które umożliwiają dzisiejszą obsługę tej funkcji. Interfejs API platformy Azure dla usługi FHIR korzysta z tej obsługi Cosmos DB. Podczas tworzenia konta będzie dostępna opcja określania identyfikatora URI klucza Azure Key Vault. Ten klucz zostanie przesłany do Cosmos DB, gdy zostanie zainicjowany konto bazy danych. Po utworzeniu żądania FHIR Cosmos DB Pobiera klucz i używa go do szyfrowania/odszyfrowywania danych. Aby rozpocząć, możesz skorzystać z następujących linków:

- [Zarejestruj dostawcę zasobów Azure Cosmos DB dla subskrypcji platformy Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Skonfiguruj wystąpienie Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Dodawanie zasad dostępu do wystąpienia Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Wygeneruj klucz w Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Określ klucz Azure Key Vault

Podczas tworzenia konta usługi Azure API for FHIR na Azure Portal można zobaczyć opcję konfiguracji "szyfrowanie danych" w obszarze "ustawienia bazy danych" na karcie "dodatkowe ustawienia". Domyślnie zostanie wybrana opcja klucz zarządzany przez usługę. 

Możesz wybrać swój klucz z klucza wyboru:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Selektora":::

Możesz też określić klucz Azure Key Vault w tym miejscu, wybierając opcję "klucz zarządzany przez klienta". Identyfikator URI klucza można wprowadzić tutaj:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Tworzenie interfejsu API platformy Azure dla usługi FHIR":::

W przypadku istniejących kont usługi FHIR można wyświetlić opcję szyfrowania klucza (klucz zarządzany przez usługę lub klienta) w bloku "baza danych" w następujący sposób. Nie można już zmodyfikować opcji konfiguracji. Można jednak zmodyfikować i zaktualizować klucz.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database"::: (Baza danych)

Ponadto można utworzyć nową wersję określonego klucza, po którym dane będą szyfrowane za pomocą nowej wersji bez żadnych przerw w świadczeniu usług. Możesz również usunąć dostęp do klucza, aby usunąć dostęp do danych. Gdy klucz jest wyłączony, zapytania spowodują wystąpienie błędu. Jeśli klucz zostanie ponownie włączony, zapytania zakończą się pomyślnie.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania kluczy zarządzanych przez klienta w stanie spoczynku. Następnie możesz zapoznać się z sekcją często zadawanych pytań Azure Cosmos DB: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: jak skonfigurować CMK](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
