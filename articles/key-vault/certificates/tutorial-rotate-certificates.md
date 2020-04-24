---
title: Samouczek — aktualizowanie częstotliwości autorotacji certyfikatu w Key Vault | Microsoft Docs
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
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106804"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Samouczek: Konfigurowanie autorotacji certyfikatu w Key Vault

Azure Key Vault umożliwia łatwe inicjowanie obsługi certyfikatów cyfrowych, zarządzanie nimi i ich wdrażanie. Mogą to być publiczne i prywatne certyfikaty SSL/TLS podpisane przez urząd certyfikacji lub certyfikat z podpisem własnym. Key Vault może także zażądać i odnowić certyfikaty przez partnerstwo z urzędami certyfikacji, zapewniając niezawodne rozwiązanie do zarządzania cyklem życia certyfikatów. W tym samouczku zostaną zaktualizowane atrybuty okresu ważności certyfikatu, częstotliwość przestawiania samochodów, urząd certyfikacji. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](../general/overview.md).

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Zarządzanie certyfikatem przy użyciu Azure Portal
> * Dodaj konto dostawcy urzędu certyfikacji
> * Aktualizuj okres ważności certyfikatu
> * Aktualizacja częstotliwości autorotacji certyfikatu
> * Aktualizowanie atrybutów certyfikatu przy użyciu programu Azure PowerShell


Przed rozpoczęciem Przeczytaj [Key Vault podstawowe pojęcia](../general/basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vault"></a>Tworzenie magazynu

Utwórz lub Wybierz istniejące Key Vault, aby wykonać operacje. [(Kroki tworzenia magazynu kluczy).](../quick-create-portal.md) W tym przykładzie nazwa magazynu to **przykład-magazyn**. 

![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Tworzenie certyfikatu w Key Vault

Utwórz lub zaimportuj certyfikat w magazynie. [(Kroki tworzenia certyfikatu w magazynie kluczy).](../quick-create-portal.md) W tym przypadku pracujemy nad certyfikatem o nazwie **ExampleCertificate**.

> [!NOTE]
> W Azure Key Vault atrybuty cyklu życia certyfikatu można aktualizować zarówno w momencie tworzenia certyfikatu, jak i po jego utworzeniu. 
## <a name="updating-certificates-life-cycle-attributes"></a>Aktualizowanie atrybutów cyklu życia certyfikatu

Certyfikat utworzony w Key Vault może być 
- certyfikat z podpisem własnym
- certyfikat utworzony przy użyciu urzędu certyfikacji (CA), który jest partnerem z Key Vault
- certyfikat z urzędem certyfikacji, który nie jest partnerem z Key Vault

Następujący urzędy certyfikacji są obecnie dostawcami partnerskimi z Key Vault:
- DigiCert — Key Vault oferuje OV protokołu TLS/SSL z DigiCert.
- GlobalSign — Key Vault oferuje OV protokołu TLS/SSL z GlobalSign.

Azure Key Vault automatyczne obracanie certyfikatów przez partnerstwo z urzędami certyfikacji. W ramach tego ustalonego powiązania Key Vault automatycznie żądania certyfikatów i odnawiać je. W związku **z tym funkcja autorotacji nie ma zastosowania w przypadku certyfikatów utworzonych przy użyciu urzędów certyfikacji, które nie są partnerskie Key Vault.** 

> [!NOTE]
> Administrator konta dla dostawcy urzędu certyfikacji tworzy poświadczenia, które mają być używane przez Key Vault do tworzenia, odnawiania i używania certyfikatów TLS/SSL za pośrednictwem Key Vault.
![Urząd certyfikacji](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>Aktualizowanie atrybutów cyklu życia certyfikatu w momencie tworzenia certyfikatu

1. Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
2. Kliknij pozycję **Wygeneruj/zaimportuj**.
3. Na ekranie **Tworzenie certyfikatu** zaktualizuj następujące wartości:
    

    - **Okres ważności**: wprowadź wartość (w miesiącach). Tworzenie krótkoterminowych certyfikatów jest zalecaną metodą zabezpieczeń. Domyślnie wartość ważności nowo utworzonego certyfikatu wynosi 12 miesięcy.
    - **Typ akcji okresu istnienia**: Wybierz akcję autoodnawiania certyfikatu i alertu. Zgodnie z wyborem należy zaktualizować wartość "procentowy okres istnienia" lub "liczba dni przed wygaśnięciem". Domyślnie automatyczne odnawianie certyfikatu jest ustawiane na 80% okresu istnienia.<br> Z menu rozwijanego wybierz opcję:

    |  Automatycznie Odnów w danym momencie| Wyślij wiadomość e-mail do wszystkich kontaktów w danym momencie |
    |-----------|------|
    |Wybranie tej opcji spowoduje włączenie autorotacji | Wybranie tej opcji nie spowoduje przerotacji. spowoduje to wyświetlenie alertu tylko dla kontaktów|
        


4. Kliknij pozycję **Utwórz**.

![Cykl życia certyfikatu](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>Aktualizowanie atrybutów cyklu życia przechowywanego certyfikatu

1. Wybierz Key Vault.
2. Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
3. Wybierz certyfikat, który chcesz zaktualizować. W takim przypadku będziemy używać certyfikatu o nazwie **ExampleCertificate**.
4. Wybierz pozycję **zasady wystawiania** z górnego paska menu.

![Właściwości certyfikatu](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. Na ekranie **zasady wystawiania** zaktualizuj następujące wartości:
- **Okres ważności**: zaktualizuj wartość (w miesiącach)
- **Typ akcji okresu istnienia**: Wybierz akcję autoodnawiania certyfikatu i alertu. Zgodnie z wyborem należy zaktualizować wartość "procentowy okres istnienia" lub "liczba dni przed wygaśnięciem". 

![Właściwości certyfikatu](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Kliknij pozycję **Zapisz**.

> [!IMPORTANT]
> Zmiana typu akcji okresu istnienia dla certyfikatu spowoduje natychmiastowe zapisanie modyfikacji istniejących certyfikatów.


### <a name="updating-certificates-attributes-using-powershell"></a>Aktualizowanie atrybutów certyfikatu przy użyciu programu PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Aby zmodyfikować zasady odnawiania dla listy certyfikatów, plik wejściowy. CSV zawierający element Magazynuname, CertName <br/>
>  vault1,Cert1 <br/>
>  vault2, Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Dowiedz się więcej na [temat parametrów](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku został zaktualizowany cykl życia certyfikatu. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

Przeczytaj więcej [na temat zarządzania tworzeniem certyfikatów w Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Zapoznaj się z [omówieniem Key Vault](../general/overview.md)