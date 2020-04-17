---
title: Konfigurowanie certyfikatu szyfrowania w klastrach systemu Windows
description: Dowiedz się, jak skonfigurować certyfikat szyfrowania i zaszyfrować wpisy tajne w klastrach systemu Windows.
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d563b338169ab26649b42c73f5fb7ed2fe8c0312
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460192"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Windows
W tym artykule pokazano, jak skonfigurować certyfikat szyfrowania i używać go do szyfrowania wpisów tajnych w klastrach systemu Windows. W przypadku klastrów systemu Linux zobacz [Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Linux.][secret-management-linux-specific-link]

[Usługa Azure Key Vault][key-vault-get-started] jest tutaj używana jako bezpieczna lokalizacja magazynu dla certyfikatów i jako sposób na zainstalowanie certyfikatów w klastrach sieci szkieletowej usług na platformie Azure. Jeśli nie wdrażasz na platformie Azure, nie trzeba używać usługi Key Vault do zarządzania wpisami tajnymi w aplikacjach sieci szkieletowej usług. Jednak *przy użyciu* wpisów tajnych w aplikacji jest niezależny od platformy w chmurze, aby umożliwić aplikacje, które mają być wdrażane w klastrze hostowane w dowolnym miejscu. 

## <a name="obtain-a-data-encipherment-certificate"></a>Uzyskiwanie certyfikatu szyfrowania danych
Certyfikat szyfrowania danych jest używany wyłącznie do szyfrowania i odszyfrowywania [parametrów][parameters-link] w pliku Settings.xml usługi i [zmiennych środowiskowych][environment-variables-link] w pliku ServiceManifest.xml usługi. Nie jest używany do uwierzytelniania lub podpisywania tekstu szyfrowego. Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Certyfikat musi zostać utworzony w celu wymiany kluczy, który można wyeksportować do pliku wymiany informacji osobistych (pfx).
* Użycie klucza certyfikatu musi zawierać szyfrowanie danych (10) i nie powinno zawierać uwierzytelniania serwera ani uwierzytelniania klienta. 
  
  Na przykład podczas tworzenia certyfikatu z podpisem własnym `KeyUsage` przy użyciu `DataEncipherment`programu PowerShell flaga musi być ustawiona na:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalowanie certyfikatu w klastrze
Ten certyfikat musi być zainstalowany w każdym węźle w klastrze. Zobacz, [jak utworzyć klaster przy użyciu usługi Azure Resource Manager][service-fabric-cluster-creation-via-arm] dla instrukcji konfiguracji. 

## <a name="encrypt-application-secrets"></a>Szyfrowanie wpisów tajnych aplikacji
Następujące polecenie programu PowerShell służy do szyfrowania klucza tajnego. To polecenie tylko szyfruje wartość; **nie** podpisuje tekstu szyfru. Do tworzenia szyfrowania dla wartości tajnych należy użyć tego samego certyfikatu szyfrowania, który jest zainstalowany w klastrze:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Wynikowy ciąg zakodowany base-64 zawiera zarówno tajny szyfr, jak i informacje o certyfikacie, który został użyty do zaszyfrowania.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [określić zaszyfrowane wpisy tajne w aplikacji.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
