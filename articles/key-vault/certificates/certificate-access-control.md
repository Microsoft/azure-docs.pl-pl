---
title: Informacje o kontroli dostępu Azure Key Vault certyfikatów
description: Omówienie kontroli dostępu do Azure Key Vault certyfikatów
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92128648"
---
# <a name="certificate-access-control"></a>Access Control certyfikatów

 Kontrola dostępu do certyfikatów jest zarządzana przez usługę Key Vault i jest dostarczana przez magazyn kluczy, który zawiera te certyfikaty. Zasady kontroli dostępu dla certyfikatów różnią się od zasad kontroli dostępu dla kluczy i wpisów tajnych w tym samym Key Vault. Użytkownicy mogą utworzyć jeden lub więcej magazynów w celu przechowywania certyfikatów, aby zachować scenariusz odpowiedniej segmentacji i zarządzania certyfikatami.  

 Następujące uprawnienia mogą być używane dla każdego podmiotu zabezpieczeń w pozycji kontrola dostępu do kluczy tajnych w magazynie kluczy i ściśle odzwierciedlają operacje dozwolone na obiekcie tajnym:  

- Uprawnienia do operacji zarządzania certyfikatami
  - **Pobierz**: Pobierz bieżącą wersję certyfikatu lub dowolną wersję certyfikatu
  - **Lista**: lista bieżących certyfikatów lub wersji certyfikatu  
  - **Aktualizacja**: aktualizowanie certyfikatu
  - **Tworzenie**: tworzenie certyfikatu Key Vault
  - **Import**: Importowanie materiału certyfikatu do certyfikatu Key Vault
  - **usuwanie**: Usuwanie certyfikatu, jego zasad i wszystkich wersji  
  - **odzyskiwanie**: Odzyskiwanie usuniętego certyfikatu
  - **kopia zapasowa**: Tworzenie kopii zapasowej certyfikatu w magazynie kluczy
  - **przywracanie**: Przywracanie certyfikatu z kopią zapasową do magazynu kluczy
  - **managecontacts**: Zarządzaj kontaktami z certyfikatem Key Vault  
  - **manageissuers**: Zarządzanie Key Vault urzędów certyfikacji/wystawców
  - **getemitencis**: pobieranie urzędów certyfikacji/wystawców certyfikatów
  - **listissuers**: Wyświetl listę urzędów certyfikacji/wystawców certyfikatów  
  - **setemitencis**: Tworzenie lub aktualizowanie urzędów/wystawców certyfikatu Key Vault  
  - **deleteissuers**: Usuwanie urzędów certyfikacji i wystawców certyfikatów Key Vault  
 
- Uprawnienia dla operacji uprzywilejowanych
  - **przeczyszczanie**: przeczyszczanie (trwałe usuwanie) usuniętego certyfikatu

Aby uzyskać więcej informacji, zobacz [operacje na certyfikatach w dokumentacji interfejsu API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — aktualizacje zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Może zostać wyświetlony błąd spowodowany brakiem zasad dostępu. Aby na przykład ```Error type : Access denied or user is unauthorized to create certificate``` rozwiązać ten problem, należy dodać certyfikaty/uprawnienia do tworzenia.

## <a name="next-steps"></a>Następne kroki

- [Informacje o usłudze Key Vault](../general/overview.md)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](../general/about-keys-secrets-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
