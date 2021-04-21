---
title: Informacje Azure Key Vault kontroli dostępu do certyfikatów
description: Omówienie kontroli dostępu Azure Key Vault certyfikatów
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 54874f30384d7f4827b13a597a469bfc67bc8fd2
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752484"
---
# <a name="certificate-access-control"></a>Certyfikat Access Control

 Kontrola dostępu do certyfikatów jest zarządzana przez usługę Key Vault i jest dostarczana przez magazyn kluczy, który zawiera te certyfikaty. Zasady kontroli dostępu dla certyfikatów różnią się od zasad kontroli dostępu dla kluczy i wpisów tajnych w tym samym Key Vault. Użytkownicy mogą utworzyć co najmniej jeden magazyn do przechowywania certyfikatów, aby zachować odpowiednią segmentację certyfikatów i zarządzanie nimi w scenariuszu.  

 Następujące uprawnienia mogą być używane dla każdego podmiotu zabezpieczeń we wpisie kontroli dostępu wpisów tajnych w magazynie kluczy i ściśle dubluje operacje dozwolone w obiekcie wpisu tajnego:  

- Uprawnienia do operacji zarządzania certyfikatami
  - **get**: pobierz bieżącą wersję certyfikatu lub dowolną wersję certyfikatu
  - **list:** lista bieżących certyfikatów lub wersji certyfikatu  
  - **aktualizacja:** aktualizowanie certyfikatu
  - **tworzenie:** tworzenie certyfikatu Key Vault certyfikatu
  - **import:** zaimportuj materiał certyfikatu do Key Vault certyfikatu
  - **delete:** usuwanie certyfikatu, jego zasad i wszystkich jego wersji  
  - **odzyskiwanie:** odzyskiwanie usuniętego certyfikatu
  - **kopia zapasowa:** tworzenie kopii zapasowej certyfikatu w magazynie kluczy
  - **przywracanie:** przywracanie certyfikatu kopii zapasowej do magazynu kluczy
  - **managecontacts:** Zarządzanie kontaktami Key Vault certyfikatów  
  - **manageissuers**: Zarządzanie Key Vault certyfikatów/wystawców
  - **getissuers:** uzyskiwanie urzędów/wystawców certyfikatu
  - **listissuers**: Lista urzędów/wystawców certyfikatu  
  - **setissuers**: Tworzenie lub aktualizowanie Key Vault/wystawców certyfikatu  
  - **deleteissuers**:Delete a Key Vault certificate's authorities/issuers (Usuń urzędy/wystawców certyfikatu)  
 
- Uprawnienia dla operacji uprzywilejowanych
  - **przeczyszczanie:** przeczyszczanie (trwałe usuwanie) usuniętego certyfikatu

Aby uzyskać więcej informacji, zobacz [operacje na certyfikatach w te Key Vault API REST](/rest/api/keyvault). Aby uzyskać informacje na temat ustanawiania uprawnień, [zobacz Magazyny — aktualizowanie zasad dostępu.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Może zostać wyświetlony błąd z powodu braku zasad dostępu. Na przykład ```Error type : Access denied or user is unauthorized to create certificate``` Aby rozwiązać ten błąd, należy dodać uprawnienia do tworzenia/certyfikatów.

## <a name="next-steps"></a>Następne kroki

- [Informacje o usłudze Key Vault](../general/overview.md)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](../general/about-keys-secrets-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
