---
title: Informacje Azure Key Vault odnawiania certyfikatu
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
ms.openlocfilehash: 0492575bef93a1b08d48475c3ab32ecbc7becd6e
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364298"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Odnawianie Azure Key Vault certyfikatów

Dzięki Azure Key Vault można łatwo aprowizować i wdrażać certyfikaty cyfrowe dla sieci oraz zarządzać nimi, a także włączyć bezpieczną komunikację dla aplikacji. Aby uzyskać więcej informacji na temat certyfikatów, zobacz [Informacje Azure Key Vault certyfikatów](./about-certificates.md).

Korzystając z certyfikatów o krótkim okresie życia lub zwiększając częstotliwość rotacji certyfikatów, możesz pomóc zapobiec dostępowi do aplikacji przez nieautoryzowanych użytkowników.

W tym artykule omówiono sposób odnawiania Azure Key Vault certyfikatów.

## <a name="get-notified-about-certificate-expiration"></a>Otrzymuj powiadomienia o wygaśnięciu certyfikatu
Aby uzyskać powiadomienie o zdarzeniach dotyczących cyklu życia certyfikatu, należy dodać kontakt z certyfikatem. Kontakty certyfikatów zawierają informacje kontaktowe do wysyłania powiadomień wyzwalanych przez zdarzenia okresu istnienia certyfikatu. Informacje o kontaktach są współdzielone przez wszystkie certyfikaty w magazynie kluczy. Powiadomienie o zdarzeniu dla dowolnego certyfikatu w magazynie kluczy jest wysyłane do wszystkich określonych kontaktów.

### <a name="steps-to-set-certificate-notifications"></a>Kroki do ustawienia powiadomień o certyfikatach:
Najpierw dodaj kontakt z certyfikatem do magazynu kluczy. Możesz dodać za pomocą polecenia Azure Portal lub polecenia cmdlet programu [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact) PowerShell.

Po drugie skonfiguruj, kiedy chcesz być powiadamiany o wygaśnięciu certyfikatu. Aby skonfigurować atrybuty cyklu życia certyfikatu, zobacz [Konfigurowanie autorotacji](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)certyfikatu w programie Key Vault .

Jeśli zasady certyfikatu są ustawione na automatyczne odnawianie, wysyłane jest powiadomienie dotyczące następujących zdarzeń.

- Przed odnowieniem certyfikatu
- Po odnowieniu certyfikatu z informacją o tym, czy certyfikat został pomyślnie odnowiony lub czy wystąpił błąd, co wymaga ręcznego odnowienia certyfikatu.  

  W przypadku ręcznego odnawiania zasad certyfikatów (tylko wiadomości e-mail) wysyłane jest powiadomienie o terminie odnowienia certyfikatu.  

W Key Vault istnieją trzy kategorie certyfikatów:
-    Certyfikaty tworzone za pomocą zintegrowanego urzędu certyfikacji, takiego jak DigiCert lub GlobalSign
-    Certyfikaty, które są tworzone za pomocą niezintegrowanych urzędu certyfikacji
-    Certyfikaty z podpisem własnym

## <a name="renew-an-integrated-ca-certificate"></a>Odnawianie zintegrowanego certyfikatu urzędu certyfikacji 
Azure Key Vault obsługuje end-to-end konserwację certyfikatów wystawionych przez zaufane urzędy certyfikacji firmy Microsoft DigiCert i GlobalSign. Dowiedz się, jak [zintegrować zaufany urząd certyfikacji z Key Vault](./how-to-integrate-certificate-authority.md).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Odnawianie nieseggrowanych certyfikatów urzędu certyfikacji 
Za pomocą Azure Key Vault można zaimportować certyfikaty z dowolnego urzędu certyfikacji, co umożliwia integrację z kilkoma zasobami platformy Azure i ułatwia wdrażanie. Jeśli martwisz się o utratę śledzenia dat wygaśnięcia certyfikatu lub, co gorsza, odkryliśmy, że certyfikat już wygasł, magazyn kluczy może pomóc Ci na bieżąco. W przypadku nieseggrowanych certyfikatów urzędu certyfikacji magazyn kluczy umożliwia konfigurowanie powiadomień e-mail o zbliżaniu się wygaśnięcia. Takie powiadomienia można również ustawić dla wielu użytkowników.

> [!IMPORTANT]
> Certyfikat jest obiektem w wersji. Jeśli bieżąca wersja wygasa, należy utworzyć nową wersję. Koncepcyjnie każda nowa wersja jest nowym certyfikatem, który składa się z klucza i obiektu blob, który wiąże ten klucz z tożsamością. W przypadku korzystania z niepartnerowanego urzędu certyfikacji magazyn kluczy generuje parę klucz/wartość i zwraca żądanie podpisania certyfikatu (CSR).

Aby odnowić nieseggrowany certyfikat urzędu certyfikacji, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal, a następnie otwórz certyfikat, który chcesz odnowić.
1. W okienku certyfikatu wybierz pozycję **Nowa wersja.**
1. Wybierz **pozycję Operacja na certyfikacie.**
1. Wybierz **pozycję Pobierz plik CSR,** aby pobrać plik CSR na dysk lokalny.
1. Wyślij żądanie CSR do wybranego urzędu certyfikacji w celu podpisania żądania.
1. Przywróć podpisane żądanie i wybierz pozycję **Scal żądanie CSR** w tym samym okienku operacji certyfikatu.

> [!NOTE]
> Ważne jest scalenie podpisanego żądania CSR z tym samym żądaniem CSR, które zostało utworzone. W przeciwnym razie klucz nie będzie odpowiadać.

Aby uzyskać więcej informacji na temat tworzenia nowego csr, zobacz [Tworzenie i scalanie CSR]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)w Key Vault .

## <a name="renew-a-self-signed-certificate"></a>Odnawianie certyfikatu z podpisem własnym

Azure Key Vault obsługuje również automatyczną rezyduacyjną certyfikaty z podpisem własnym. Aby dowiedzieć się więcej na temat zmieniania zasad wystawiania i aktualizowania atrybutów cyklu życia certyfikatu, zobacz [Konfigurowanie autorotacji](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)certyfikatu w programie Key Vault .

## <a name="troubleshoot"></a>Rozwiązywanie problemów
* Jeśli wystawiony certyfikat jest wyłączony *w* Azure Portal, przejdź  do operacji certyfikatu, aby wyświetlić komunikat o błędzie certyfikatu.
* Typ błędu "Csr użyty do uzyskania certyfikatu został już użyty. Spróbuj wygenerować nowy certyfikat przy użyciu nowego żądania CSR".
  Przejdź do sekcji "Zasady zaawansowane" certyfikatu i sprawdź, czy opcja **"Użyj ponownie** klucza przy odnawianiu" jest wyłączona.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Jak przetestować funkcję autorotacji certyfikatu?**

Utwórz certyfikat z podpisem własnym o ważności **1** miesiąca, a następnie ustaw akcję okresu istnienia dla rotacji na **wartość 1%.** W ciągu kilku następnych dni powinno być możliwe wyświetlenie historii tworzenia wersji certyfikatu.
  
**Czy tagi zostaną zreplikowane po automatycznej rejestracji certyfikatu?**

Tak, tagi są replikowane po autorenwalu.

## <a name="next-steps"></a>Następne kroki
*    [Integracja Key Vault z urzędu certyfikacji firmy DigiCert](how-to-integrate-certificate-authority.md)
*    [Samouczek: konfigurowanie autorotacji certyfikatów w Key Vault](tutorial-rotate-certificates.md)
