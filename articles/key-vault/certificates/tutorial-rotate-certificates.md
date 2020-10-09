---
title: Samouczek — aktualizowanie częstotliwości autorotacji certyfikatów w Key Vault | Microsoft Docs
description: Samouczek przedstawiający sposób aktualizowania częstotliwości autorotacji certyfikatu w Azure Key Vault przy użyciu Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: eeceb1279579055bfff33f0a4413f0798418faed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83201509"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Samouczek: Konfigurowanie autorotacji certyfikatów w Key Vault

Za pomocą Azure Key Vault można łatwo udostępniać i wdrażać certyfikaty cyfrowe oraz zarządzać nimi. Certyfikaty mogą być certyfikatami publicznymi i prywatnymi SSL (SSL) podpisywanymi przez urząd certyfikacji (CA) lub certyfikat z podpisem własnym. Key Vault mogą również żądać certyfikatów i odnawiać je poprzez partnerstwo z urzędami certyfikacji, zapewniając niezawodne rozwiązanie do zarządzania cyklem życia certyfikatów. W tym samouczku zostaną zaktualizowane okresy ważności certyfikatu, częstotliwość przestawiania i atrybuty urzędu certyfikacji.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Zarządzanie certyfikatem przy użyciu Azure Portal.
> * Dodaj konto dostawcy urzędu certyfikacji.
> * Zaktualizuj okres ważności certyfikatu.
> * Zaktualizuj częstotliwość ponownego rotacji certyfikatu.
> * Aktualizowanie atrybutów certyfikatu za pomocą Azure PowerShell.

Przed rozpoczęciem Przeczytaj [Key Vault podstawowe pojęcia](../general/basic-concepts.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vault"></a>Tworzenie magazynu

Utwórz magazyn kluczy lub wybierz istniejący magazyn, aby wykonać operacje (zobacz [procedurę tworzenia magazynu kluczy](../quick-create-portal.md)). W tym przykładzie nazwa magazynu kluczy jest **przykładem magazynu**.

![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Tworzenie certyfikatu w Key Vault

Utwórz certyfikat lub zaimportuj certyfikat do magazynu kluczy (zobacz [procedurę tworzenia certyfikatu w Key Vault](../quick-create-portal.md)). W takim przypadku będziesz korzystać z certyfikatu o nazwie **ExampleCertificate**.

## <a name="update-certificate-lifecycle-attributes"></a>Aktualizuj atrybuty cyklu życia certyfikatu

W Azure Key Vault można zaktualizować atrybuty cyklu życia certyfikatu zarówno przed, jak i po chwili utworzenia certyfikatu.

Certyfikat utworzony w Key Vault może być:

- Certyfikat z podpisem własnym.
- Certyfikat utworzony przy użyciu urzędu certyfikacji, który jest partnerem z Key Vault.
- Certyfikat z urzędem certyfikacji, który nie jest partnerem Key Vault.

Następujące urzędy certyfikacji są obecnie partnerskimi dostawcami z Key Vault:

- DigiCert: Key Vault oferuje OV protokołu TLS/SSL.
- GlobalSign: Key Vault oferuje OV protokołu TLS/SSL.

Key Vault automatyczne obracanie certyfikatów poprzez ustalone partnerstwo z urzędami certyfikacji. Ponieważ Key Vault automatycznie żądania certyfikatów i odnawiać je za pomocą partnerstwa, funkcja automatycznego rotacji nie ma zastosowania w przypadku certyfikatów utworzonych przy użyciu urzędów certyfikacji, których nie współpracujeli z Key Vault.

> [!NOTE]
> Administrator konta dla dostawcy urzędu certyfikacji tworzy poświadczenia, których Key Vault używa do tworzenia, odnawiania i używania certyfikatów TLS/SSL.
![Urząd certyfikacji](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Aktualizowanie atrybutów cyklu życia certyfikatu w momencie tworzenia

1. Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
1. Wybierz pozycję **Generuj/Importuj**.
1. Na ekranie **Tworzenie certyfikatu** zaktualizuj następujące wartości:

   - **Okres ważności**: wprowadź wartość (w miesiącach). Tworzenie krótkoterminowych certyfikatów jest zalecaną metodą zabezpieczeń. Domyślnie wartość ważności nowo utworzonego certyfikatu wynosi 12 miesięcy.
   - **Typ akcji okresu istnienia**: Wybierz akcję autoodnawiania i alertu certyfikatu, a następnie zaktualizuj **okres istnienia (w procentach** ) lub **liczbę dni przed wygaśnięciem**. Domyślnie automatyczne odnawianie certyfikatu jest ustawiane na 80% okresu istnienia. Z menu rozwijanego wybierz jedną z następujących opcji.

        |  Automatycznie Odnów w danym momencie| Wyślij wiadomość e-mail do wszystkich kontaktów w danym momencie |
        |-----------|------|
        |Wybranie tej opcji spowoduje *włączenie* autorotacji. | Wybranie tej opcji *nie* spowoduje przerotacji automatyczne, ale spowoduje tylko wyświetlenie alertu.|

1. Wybierz przycisk **Utwórz**.

![Cykl życia certyfikatu](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Aktualizowanie atrybutów cyklu życia przechowywanego certyfikatu

1. Wybierz magazyn kluczy.
1. Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
1. Wybierz certyfikat, który chcesz zaktualizować. W takim przypadku będziesz korzystać z certyfikatu o nazwie **ExampleCertificate**.
1. Wybierz pozycję **zasady wystawiania** z górnego paska menu.

   ![Właściwości certyfikatu](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. Na ekranie **zasady wystawiania** zaktualizuj następujące wartości:

   - **Okres ważności**: zaktualizuj wartość (w miesiącach).
   - **Typ akcji okresu istnienia**: Wybierz akcję autoodnawiania i alertu certyfikatu, a następnie zaktualizuj **okres istnienia (w procentach** ) lub **liczbę dni przed wygaśnięciem**.

   ![Właściwości certyfikatu](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Wybierz pozycję **Zapisz**.

> [!IMPORTANT]
> Zmiana typu akcji okresu istnienia dla certyfikatu spowoduje natychmiastowe zapisanie modyfikacji istniejących certyfikatów.


### <a name="update-certificate-attributes-by-using-powershell"></a>Aktualizowanie atrybutów certyfikatu przy użyciu programu PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Aby zmodyfikować zasady odnawiania dla listy certyfikatów, wprowadź tekst `File.csv` zawierający `VaultName,CertName` jak w następującym przykładzie:
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
Aby dowiedzieć się więcej na temat parametrów, zobacz [AZ webmagazyn Certificate](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne samouczki Key Vault kompilują się na tym samouczku. Jeśli planujesz korzystać z tych samouczków, możesz pozostawić te istniejące zasoby na miejscu.
Gdy nie są już potrzebne, Usuń grupę zasobów, która spowoduje usunięcie magazynu kluczy i powiązanych zasobów.

Aby usunąć grupę zasobów przy użyciu portalu:

1. Wprowadź nazwę grupy zasobów w polu **wyszukiwania** w górnej części portalu. Gdy grupa zasobów używana w tym przewodniku Szybki Start pojawi się w wynikach wyszukiwania, wybierz ją.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. W polu **wpisz nazwę grupy zasobów:** wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały zaktualizowane atrybuty cyklu życia certyfikatu. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do następujących artykułów:

- Przeczytaj więcej [na temat zarządzania tworzeniem certyfikatów w Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios).
- Zapoznaj się z [omówieniem Key Vault](../general/overview.md).