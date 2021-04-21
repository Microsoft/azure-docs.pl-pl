---
title: Azure Key Vault funkcji danych klienta — Azure Key Vault | Microsoft Docs
description: Dowiedz się więcej o danych klientów, które Azure Key Vault odbierane podczas tworzenia lub aktualizowania magazynów, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4d45c019a6ba073d7553c09784736959faf89d27
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749784"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault funkcji danych klienta

Azure Key Vault odbiera dane klienta podczas tworzenia lub aktualizowania magazynów, zarządzanych pul modułów HSM, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu. Te dane klienta są bezpośrednio widoczne w Azure Portal i za pośrednictwem interfejsu API REST. Dane klienta można edytować lub usuwać, aktualizując lub usuwając obiekt, który zawiera dane.

Dzienniki dostępu do systemu są generowane, gdy użytkownik lub aplikacja uzyskuje dostęp Key Vault. Szczegółowe dzienniki dostępu są dostępne dla klientów korzystających z usługi Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klientów

Poniższe informacje identyfikują dane klientów w ramach Azure Key Vault:

- Zasady dostępu dla Azure Key Vault zawierają identyfikatory obiektów reprezentujące użytkowników, grupy lub aplikacje
- Podmioty certyfikatów mogą zawierać adresy e-mail lub inne identyfikatory użytkowników lub organizacji
- Kontakty z certyfikatami mogą zawierać adresy e-mail, nazwy lub numery telefonów użytkowników
- Wystawcy certyfikatów mogą zawierać adresy e-mail, nazwy, numery telefonów, poświadczenia konta i szczegóły organizacji
- Dowolne tagi można stosować do obiektów w Azure Key Vault. Te obiekty obejmują magazyny, klucze, wpisy tajne, certyfikaty i konta magazynu. Użyte tagi mogą zawierać dane osobowe
- Azure Key Vault dostępu zawierają identyfikatory obiektów, [upny](../../active-directory/hybrid/plan-connect-userprincipalname.md)i adresy IP dla każdego wywołania interfejsu API REST
- Azure Key Vault diagnostyczne mogą zawierać identyfikatory obiektów i adresy IP dla wywołań interfejsu API REST

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Te same interfejsy API REST, środowisko portalu i zestawy SDK używane do tworzenia magazynów, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu mogą również aktualizować i usuwać te obiekty.

Usuwanie nie soft-delete umożliwia odzyskanie usuniętych danych przez 90 dni po usunięciu. W przypadku korzystania z usuwania nietrwałych dane mogą zostać trwale usunięte przed wygaśnięciem 90-dniowego okresu przechowywania przez wykonanie operacji przeczyszczania. Jeśli magazyn lub subskrypcja została skonfigurowana tak, aby blokować operacje przeczyszczania, nie można trwale usunąć danych, dopóki nie minie zaplanowany okres przechowywania.

## <a name="exporting-customer-data"></a>Eksportowanie danych klientów

Te same interfejsy API REST, środowisko portalu i zestawy SDK, które są używane do tworzenia magazynów, kluczy, wpisów tajnych, certyfikatów i kont magazynu zarządzanego, umożliwiają również wyświetlanie i eksportowanie tych obiektów.

Azure Key Vault rejestrowania dostępu jest opcjonalną funkcją, która może być włączona w celu generowania dzienników dla każdego wywołania interfejsu API REST. Te dzienniki zostaną przeniesione na konto magazynu w ramach subskrypcji, w którym są stosowane zasady przechowywania spełniające wymagania organizacji.

Azure Key Vault diagnostyczne zawierające dane osobowe można pobrać, żądać eksportu w portalu zachowania poufności informacji użytkowników. To żądanie musi zostać wykonane przez administratora dzierżawy.

## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault rejestrowania](logging.md)

- [Azure Key Vault — omówienie usuwania nietrwałego](./key-vault-recovery.md)

- [Azure Key Vault operacji klucza](/rest/api/keyvault/key-operations)

- [Azure Key Vault operacji tajnych](/rest/api/keyvault/secret-operations)

- [Azure Key Vault certyfikatów i zasad](/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault operacji konta magazynu](/rest/api/keyvault/storage-account-key-operations)