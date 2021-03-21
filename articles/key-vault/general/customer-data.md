---
title: Azure Key Vault funkcje danych klienta — Azure Key Vault | Microsoft Docs
description: Informacje o klientach, które Azure Key Vault odbierane podczas tworzenia lub aktualizowania magazynów, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8806ff41edabcd3c0875d3c02360dc9a275e3878
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460822"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault funkcje danych klienta

Azure Key Vault otrzymuje dane klienta podczas tworzenia lub aktualizowania magazynów, pul zarządzanych modułów HSM, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu. Dane klienta są bezpośrednio widoczne w Azure Portal i za pomocą interfejsu API REST. Dane klienta można edytować lub usunąć, aktualizując lub usuwając obiekt zawierający dane.

Dzienniki dostępu do systemu są generowane, gdy użytkownik lub aplikacja uzyskują dostęp do Key Vault. Szczegółowe dzienniki dostępu są dostępne dla klientów korzystających z usługi Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Poniższe informacje identyfikują dane klienta w Azure Key Vault:

- Zasady dostępu dla Azure Key Vault zawierają identyfikatory obiektów reprezentujące użytkowników, grupy lub aplikacje
- Podmioty certyfikacji mogą zawierać adresy e-mail lub inne identyfikatory użytkowników lub organizacji
- Kontakty certyfikatu mogą zawierać adresy e-mail użytkownika, nazwy lub numery telefonów
- Wystawcy certyfikatów mogą zawierać adresy e-mail, nazwy, numery telefonów, poświadczenia konta i szczegóły organizacji
- Dowolne Tagi można zastosować do obiektów w Azure Key Vault. Te obiekty obejmują magazyny, klucze, wpisy tajne, certyfikaty i konta magazynu. Używane Tagi mogą zawierać dane osobowe
- Dzienniki dostępu Azure Key Vault zawierają identyfikatory obiektów, [UPN](../../active-directory/hybrid/plan-connect-userprincipalname.md)i adresy IP dla każdego wywołania interfejsu API REST
- Dzienniki diagnostyczne Azure Key Vault mogą zawierać identyfikatory obiektów i adresy IP dla wywołań interfejsu API REST

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Te same interfejsy API REST, środowisko portalu i zestawy SDK służące do tworzenia magazynów, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu mogą także aktualizować i usuwać te obiekty.

Funkcja usuwania nietrwałego umożliwia odzyskanie usuniętych danych przez 90 dni po usunięciu. W przypadku korzystania z programu unsoft-Delete dane mogą zostać trwale usunięte przed okresem przechowywania 90 dni, przez wykonanie operacji przeczyszczania. Jeśli magazyn lub subskrypcja została skonfigurowana w celu blokowania operacji przeczyszczania, nie jest możliwe trwałe usunięcie danych do momentu przekazanie zaplanowanego okresu przechowywania.

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Te same interfejsy API REST, środowisko portalu i zestawy SDK, które są używane do tworzenia magazynów, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu, umożliwiają również wyświetlanie i eksportowanie tych obiektów.

Azure Key Vault rejestrowanie dostępu to opcjonalna funkcja, którą można włączyć w celu generowania dzienników dla każdego wywołania interfejsu API REST. Te dzienniki zostaną przesłane do konta magazynu w ramach subskrypcji, w której stosowane są zasady przechowywania spełniające wymagania organizacji.

Azure Key Vault dzienników diagnostycznych zawierających dane osobowe można pobrać, wysyłając żądanie eksportu w portalu prywatności użytkownika. To żądanie musi być wykonywane przez administratora dzierżawy.

## <a name="next-steps"></a>Następne kroki

- [Rejestrowanie Azure Key Vault](logging.md)

- [Azure Key Vault — omówienie usuwania nietrwałego](./key-vault-recovery.md)

- [Operacje na kluczu Azure Key Vault](/rest/api/keyvault/key-operations)

- [Azure Key Vault operacji tajnych](/rest/api/keyvault/secret-operations)

- [Azure Key Vault certyfikatów i zasad](/rest/api/keyvault/certificates-and-policies)

- [Operacje na koncie magazynu Azure Key Vault](/rest/api/keyvault/storage-account-key-operations)