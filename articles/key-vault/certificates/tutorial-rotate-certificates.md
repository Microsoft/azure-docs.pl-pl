---
title: Samouczek — aktualizowanie częstotliwości automatycznego obracania certyfikatów w Key Vault | Microsoft Docs
description: Samouczek przedstawiający sposób aktualizowania częstotliwości automatycznej rotacji certyfikatu w programie Azure Key Vault użyciu Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 48a531e054bf92c8ddc7761689b8fdf1df8a28a7
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750018"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Samouczek: konfigurowanie automatycznego obracania certyfikatów w Key Vault

Certyfikaty cyfrowe można łatwo aprowizować i wdrażać oraz zarządzać nimi przy użyciu Azure Key Vault. Certyfikaty mogą być publicznymi i prywatnymi Secure Sockets Layer (SSL)/Transport Layer Security (TLS) podpisanymi przez urząd certyfikacji lub certyfikat z podpisem własnym. Key Vault żądać i odnawiać certyfikaty za pośrednictwem partnerstwa z urzędu certyfikacji, zapewniając niezawodne rozwiązanie do zarządzania cyklem życia certyfikatów. W tym samouczku zaktualizujemy okres ważności certyfikatu, częstotliwość automatycznej rotacji i atrybuty urzędu certyfikacji.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Zarządzanie certyfikatem przy użyciu Azure Portal.
> * Dodaj konto dostawcy urzędu certyfikacji.
> * Zaktualizuj okres ważności certyfikatu.
> * Zaktualizuj częstotliwość automatycznej rotacji certyfikatu.
> * Zaktualizuj atrybuty certyfikatu przy użyciu Azure PowerShell.

Przed rozpoczęciem zapoznaj się [z Key Vault podstawowymi pojęciami.](../general/basic-concepts.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vault"></a>Tworzenie magazynu

Utwórz nową Azure Key Vault przy [użyciu Azure Portal](../general/quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md). W tym przykładzie nazwa magazynu kluczy to **Example-Vault**.

![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Tworzenie certyfikatu w Key Vault

Utwórz certyfikat lub zaimportuj certyfikat do magazynu kluczy (zobacz [Steps to create a certificate in Key Vault](../secrets/quick-create-portal.md)). W tym przypadku będziesz pracować nad certyfikatem o nazwie **ExampleCertificate**.

## <a name="update-certificate-lifecycle-attributes"></a>Aktualizowanie atrybutów cyklu życia certyfikatu

W Azure Key Vault można zaktualizować atrybuty cyklu życia certyfikatu zarówno podczas tworzenia certyfikatu, jak i później.

Certyfikat utworzony w Key Vault może być:

- Certyfikat z podpisem własnym.
- Certyfikat utworzony za pomocą urzędu certyfikacji, który jest partnerem Key Vault.
- Certyfikat z urzędu certyfikacji, który nie jest partnerem Key Vault.

Następujące firmy CA są obecnie dostawcami partnerskimi z Key Vault:

- DigiCert: Key Vault oferuje certyfikaty OV TLS/SSL.
- GlobalSign: Key Vault oferuje certyfikaty OV TLS/SSL.

Key Vault automatycznie obraca certyfikaty za pośrednictwem ustanowionych partnerstwa z urzędu certyfikacji. Ponieważ Key Vault automatycznie żąda i odnawia certyfikaty za pośrednictwem partnerstwa, możliwość automatycznej rotacji nie ma zastosowania do certyfikatów utworzonych za pomocą urzędu certyfikacji, które nie są Key Vault.

> [!NOTE]
> Administrator konta dostawcy urzędu certyfikacji tworzy poświadczenia, które Key Vault do tworzenia, odnawiania i używania certyfikatów TLS/SSL.
![Urząd certyfikacji](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Aktualizowanie atrybutów cyklu życia certyfikatu w czasie tworzenia

1. Na Key Vault właściwości wybierz pozycję **Certyfikaty.**
1. Wybierz **pozycję Generuj/Zaimportuj.**
1. Na **ekranie Tworzenie certyfikatu** zaktualizuj następujące wartości:

   - **Okres ważności:** wprowadź wartość (w miesiącach). Tworzenie certyfikatów o krótkim okresie życia jest zalecanym rozwiązaniem w zakresie zabezpieczeń. Domyślnie wartość ważności nowo utworzonego certyfikatu wynosi 12 miesięcy.
   - **Typ akcji okresu istnienia:** wybierz akcję automatycznego odnawiania  i alertów certyfikatu, a następnie zaktualizuj procent okresu istnienia lub liczbę dni przed **wygaśnięciem**. Domyślnie automatyczne odnawianie certyfikatu jest ustawiane na 80 procent jego okresu istnienia. Z menu rozwijanego wybierz jedną z następujących opcji.

      |  Automatyczne odnawianie w danym momencie| Wyślij wiadomość e-mail do wszystkich kontaktów w danym momencie |
      |-----------|------|
      |Wybranie tej opcji *spowoduje włączenie* autorotacji. | Wybranie tej opcji nie *spowoduje automatycznego* obracania, ale spowoduje tylko alert dla kontaktów.|
      
      Informacje na temat konfigurowania [kontaktu e-mail można znaleźć tutaj](https://docs.microsoft.com/azure/key-vault/certificates/overview-renew-certificate#get-notified-about-certificate-expiration)

1. Wybierz przycisk **Utwórz**.

![Cykl życia certyfikatu](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Aktualizowanie atrybutów cyklu życia przechowywanego certyfikatu

1. Wybierz magazyn kluczy.
1. Na stronach Key Vault właściwości wybierz **pozycję Certyfikaty.**
1. Wybierz certyfikat, który chcesz zaktualizować. W tym przypadku będziesz pracować nad certyfikatem o nazwie **ExampleCertificate**.
1. Wybierz **pozycję Zasady wystawiania** na górnym pasku menu.

   ![Zrzut ekranu przedstawiający przycisk Zasady wystawiania.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. Na **ekranie Zasady wystawiania** zaktualizuj następujące wartości:

   - **Okres ważności:** zaktualizuj wartość (w miesiącach).
   - **Typ akcji okresu istnienia:** wybierz akcję automatycznego odnawiania i  alertów certyfikatu, a następnie zaktualizuj wartość procentową okresu istnienia lub liczbę dni przed **wygaśnięciem**.

   ![Właściwości certyfikatu](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Wybierz pozycję **Zapisz**.

> [!IMPORTANT]
> Zmiana typu akcji okresu istnienia dla certyfikatu spowoduje natychmiastowe rejestrowanie modyfikacji istniejących certyfikatów.


### <a name="update-certificate-attributes-by-using-powershell"></a>Aktualizowanie atrybutów certyfikatu przy użyciu programu PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Aby zmodyfikować zasady odnawiania dla listy certyfikatów, wprowadź wartość zawierającą wartość `File.csv` , jak w poniższym `VaultName,CertName` przykładzie:
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Aby dowiedzieć się więcej na temat parametrów, zobacz [az keyvault certificate](/cli/azure/keyvault/certificate#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne Key Vault samouczków są opierane na tym samouczku. Jeśli planujesz pracę z tymi samouczkami, możesz pozostawić te istniejące zasoby na miejscu.
Gdy nie będą już potrzebne, usuń grupę zasobów, co spowoduje usunięcie magazynu kluczy i powiązanych zasobów.

Aby usunąć grupę zasobów przy użyciu portalu:

1. Wprowadź nazwę grupy zasobów w **polu** Wyszukaj w górnej części portalu. Gdy grupa zasobów używana w tym przewodniku Szybki start pojawi się w wynikach wyszukiwania, wybierz ją.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. W **polu WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zaktualizowano atrybuty cyklu życia certyfikatu. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do następujących artykułów:

- Przeczytaj więcej na [temat zarządzania tworzeniem certyfikatów w Azure Key Vault](./create-certificate-scenarios.md).
- Zapoznaj się [z Key Vault omówieniem.](../general/overview.md)
