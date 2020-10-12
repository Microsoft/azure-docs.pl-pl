---
title: Informacje o odnowieniu certyfikatu Azure Key Vault
description: W tym artykule omówiono sposób odnawiania Azure Key Vault certyfikatów.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588807"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Odnawianie certyfikatów Azure Key Vault

Za pomocą Azure Key Vault można łatwo udostępniać i wdrażać certyfikaty cyfrowe oraz zarządzać nimi w sieci i zapewniać bezpieczną komunikację z aplikacjami. Więcej informacji o certyfikatach znajduje się w temacie [Informacje o certyfikatach Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates).

Korzystając z certyfikatów krótkoterminowych lub przez zwiększenie częstotliwości rotacji certyfikatów, można zapobiec dostępowi do aplikacji przez nieautoryzowanych użytkowników.

W tym artykule omówiono sposób odnawiania certyfikatów Azure Key Vault.

## <a name="get-notified-about-certificate-expirations"></a>Otrzymywanie powiadomień o wygasaniu certyfikatów
Aby otrzymywać powiadomienia, gdy certyfikaty wkrótce wygasną, wykonaj następujące czynności:

Najpierw Dodaj kontakt z certyfikatem do magazynu kluczy za pomocą polecenia cmdlet [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)programu PowerShell.

Następnie skonfiguruj, Kiedy chcesz otrzymywać powiadomienia o wygaśnięciu certyfikatu. Aby skonfigurować atrybuty cyklu życia certyfikatu, zobacz [Konfigurowanie autorotacji certyfikatów w Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

W Key Vault istnieją trzy kategorie certyfikatów:
-   Certyfikaty utworzone przy użyciu zintegrowanego urzędu certyfikacji (CA), takie jak DigiCert lub GlobalSign
-   Certyfikaty utworzone przy użyciu niezintegrowanego urzędu certyfikacji
-   Certyfikaty z podpisem własnym

## <a name="renew-an-integrated-ca-certificate"></a>Odnawianie certyfikatu zintegrowanego urzędu certyfikacji 
Azure Key Vault obsługuje kompleksową konserwację certyfikatów wystawionych przez zaufanych urzędów certyfikacji firmy Microsoft DigiCert i GlobalSign. Dowiedz się, jak [zintegrować zaufany urząd certyfikacji z Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Odnów certyfikat niezintegrowanego urzędu certyfikacji 
Za pomocą Azure Key Vault można importować certyfikaty z dowolnego urzędu certyfikacji, korzyści, które umożliwiają integrację z kilkoma zasobami platformy Azure i ułatwiają wdrażanie. W przypadku martwisz się o utracie śledzenia dat wygaśnięcia certyfikatu lub, że wykryto, że certyfikat już wygasł, Twój Magazyn kluczy może pomóc zapewnić aktualność. W przypadku certyfikatów niezintegrowanych urzędów certyfikacji Magazyn kluczy umożliwia skonfigurowanie powiadomień e-mail z niemal upływem okresu ważności. Takie powiadomienia można również ustawić dla wielu użytkowników.

> [!IMPORTANT]
> Certyfikat jest obiektem z zainstalowaną wersją. Jeśli bieżąca wersja wygaśnie, należy utworzyć nową wersję. Koncepcyjnie każda nowa wersja jest nowym certyfikatem, który składa się z klucza i obiektu BLOB, który wiąże ten klucz z tożsamością. W przypadku korzystania z niepartnerskiego urzędu certyfikacji Magazyn kluczy generuje parę klucz/wartość i zwróci żądanie podpisania certyfikatu (CSR).

Aby odnowić certyfikat niezintegrowanego urzędu certyfikacji, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal, a następnie otwórz certyfikat, który chcesz odnowić.
1. W okienku certyfikat wybierz pozycję **Nowa wersja**.
1. Wybierz **operację certyfikatu**.
1. Wybierz pozycję **Pobierz CSR** , aby pobrać plik CSR na dysk lokalny.
1. Wyślij CSR do wybranego urzędu certyfikacji w celu podpisania żądania.
1. Przywróć podpisane żądanie i wybierz pozycję **Scal CSR** w tym samym okienku operacji certyfikatu.

> [!NOTE]
> Ważne jest, aby scalić podpisany plik CSR z tym samym żądaniem CSR, które zostało utworzone. W przeciwnym razie klucz nie będzie zgodny.

Aby uzyskać więcej informacji na temat tworzenia nowego CSR, zobacz [Tworzenie i scalanie CSR w Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Odnów certyfikat z podpisem własnym

Azure Key Vault obsługuje również automatyczne odnawianie certyfikatów z podpisem własnym. Aby dowiedzieć się więcej na temat zmiany zasad wystawiania i aktualizowania atrybutów cyklu życia certyfikatu, zobacz [Konfigurowanie autorotacji certyfikatów w Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli wystawiony certyfikat jest w stanie *wyłączenia* w Azure Portal, przejdź do **operacji certyfikat** , aby wyświetlić komunikat o błędzie certyfikatu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Jak można przetestować funkcję autorotacji certyfikatu?**

Utwórz certyfikat o ważności **1 miesiąca**, a następnie ustaw akcję okresu istnienia dla rotacji o **1%**. To ustawienie spowoduje obrócenie certyfikatu co 7,2 godzin.
  
**Czy Tagi zostaną zreplikowane po autoodnowieniu certyfikatu?**

Tak, Tagi są replikowane po autoodnowieniu.

## <a name="next-steps"></a>Następne kroki
*   [Integracja Key Vault z urzędem certyfikacji DigiCert](how-to-integrate-certificate-authority.md)
*   [Samouczek: Konfigurowanie autorotacji certyfikatów w Key Vault](tutorial-rotate-certificates.md)
