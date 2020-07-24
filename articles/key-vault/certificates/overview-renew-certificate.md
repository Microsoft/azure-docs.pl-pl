---
title: Informacje o odnowieniu certyfikatu Azure Key Vault
description: Informacje o odnowieniu certyfikatu Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: c6999b67a5c0a0f4ca7cb943ae8de3afd8b6a11e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098275"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Informacje o odnowieniu certyfikatu Azure Key Vault

Azure Key Vault umożliwia łatwe inicjowanie obsługi i wdrażanie certyfikatów cyfrowych dla sieci oraz zarządzanie nimi oraz zapewnia bezpieczną komunikację dla aplikacji. Aby uzyskać więcej ogólnych informacji o certyfikatach, zobacz [Azure Key Vault Certificates](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

Posiadanie krótkoterminowego certyfikatu lub zwiększenie częstotliwości rotacji certyfikatów ogranicza zakres atakującej dla uszkodzeń.

Istnieją trzy kategorie tworzenia certyfikatów w magazynie kluczy. Ten przewodnik pomoże Ci zrozumieć, jak można osiągnąć odnowienie certyfikatów.
-   Certyfikaty utworzone przy użyciu zintegrowanego urzędu certyfikacji (DigiCert lub GlobalSign)
-   Certyfikaty utworzone przy użyciu niezintegrowanego urzędu certyfikacji
-   Certyfikaty z podpisem własnym

## <a name="renewal-of-integrated-ca-certificate"></a>Odnawianie certyfikatu zintegrowanego urzędu certyfikacji 
Dobra wiadomość! Magazyny kluczy Azure zajmują kompleksową konserwację certyfikatów wystawianych przez zaufane urzędy certyfikacji firmy Microsoft, np. DigiCert i GlobalSign. Dowiedz się, jak [zintegrować zaufany urząd certyfikacji z magazynem kluczy](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renewal-of-non-integrated-ca-certificate"></a>Odnawianie niezintegrowanego certyfikatu urzędu certyfikacji 
Usługa Azure Key model udostępnia swoim użytkownikom korzyści wynikające z importowania certyfikatów z dowolnego urzędu certyfikacji, aby umożliwić użytkownikom integrację z kilkoma zasobami platformy Azure i ułatwić wdrażanie. W przypadku martwisz się o utracie wygasłej ścieżki certyfikatu lub gorszej wykryte, że certyfikat już wygasł; następnie Key Vault może pomóc Ci w Aktualności. W przypadku niezintegrowanego certyfikatu urzędu certyfikacji Magazyn kluczy umożliwia użytkownikowi skonfigurowanie niemal wygasających powiadomień e-mail. Te powiadomienia można również ustawić dla wielu użytkowników.

Teraz, aby odnowić certyfikat, ważne jest, aby zrozumieć, że certyfikat Azure Key Vault jest obiektem z uruchomioną wersją. Jeśli bieżąca wersja wygaśnie, należy utworzyć nową wersję. Koncepcyjnie każda nowa wersja będzie całkowicie nowym certyfikatem składającym się z klucza i obiektu BLOB, który wiąże ten klucz z tożsamością. W przypadku korzystania z niepartnerskiego urzędu certyfikacji Magazyn kluczy generuje parę klucz-wartość i zwróci CSR.

**Kroki, które należy wykonać w Azure Portal:-**
1.  Otwórz certyfikat, który chcesz odnowić.
2.  Na ekranie certyfikatu wybierz pozycję **+ Nowa wersja** .
3.  Wybieranie **operacji certyfikatu**
4.  Wybierz pozycję **Pobierz CSR**. Spowoduje to pobranie pliku. CSR na dysku lokalnym.
5.  Przenieś CSR do wybranego urzędu certyfikacji w celu podpisania żądania
6.  Przywróć podpisane żądanie i wybierz pozycję **Scal CSR** na tym samym ekranie operacji certyfikatu.

> [!NOTE]
> Ważne jest, aby scalić podpisany plik CSR z tym samym żądaniem CSR, które zostało utworzone, w przeciwnym razie klucz nie jest zgodny.

Kroki są podobne do tworzenia nowego certyfikatu i szczegółowe informacje znajdują się [tutaj]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renewal-of-self-signed-certificate"></a>Odnawianie certyfikatu z podpisem własnym

Dobre wiadomości ponownie! Magazyny kluczy platformy Azure będą również korzystać z automatycznego odnawiania certyfikatów z podpisem własnym dla swoich użytkowników. Aby dowiedzieć się więcej na temat zmiany zasad wystawiania i aktualizowania okresu istnienia certyfikatu, Przeczytaj więcej [tutaj](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

### <a name="troubleshoot"></a>Rozwiąż problemy
Jeśli wystawiony certyfikat jest w stanie "Disabled" w Azure Portal, należy wyświetlić operację certyfikatu, aby wyświetlić komunikat o błędzie dla tego certyfikatu.

### <a name="see-also"></a>Zobacz też
*   [Integrowanie usługi Key Vault z urzędem certyfikacji DigiCert](how-to-integrate-certificate-authority.md)
*   [Samouczek: Konfigurowanie autorotacji certyfikatów w Key Vault](tutorial-rotate-certificates.md)