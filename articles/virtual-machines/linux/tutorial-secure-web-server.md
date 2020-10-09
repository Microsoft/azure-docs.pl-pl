---
title: 'Samouczek: Zabezpieczanie serwera sieci Web z systemem Linux przy użyciu certyfikatów TLS/SSL na platformie Azure'
description: Z tego samouczka dowiesz się, jak można korzystać z interfejsu wiersza polecenia platformy Azure do zabezpieczania maszyny wirtualnej z systemem Linux, na której działa internetowy serwer NGINX z certyfikatami SSL przechowywanymi w usłudze Azure Key Vault.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7964f96afc59464c28cabb9e1d5c7961fc765a3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501776"
---
# <a name="tutorial-secure-a-web-server-on-a-linux-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Samouczek: Zabezpieczanie serwera sieci Web na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu certyfikatów TLS/SSL przechowywanych w Key Vault
Aby zabezpieczyć serwery sieci Web, Transport Layer Security (TLS), wcześniej znany jako SSL (SSL), można użyć certyfikatu do szyfrowania ruchu w sieci Web. Te certyfikaty TLS/SSL mogą być przechowywane w Azure Key Vault i umożliwiają bezpieczne wdrażanie certyfikatów na maszynach wirtualnych z systemem Linux na platformie Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi Azure Key Vault
> * Generowanie lub przekazywanie certyfikatu do usługi Key Vault
> * Tworzenie maszyny wirtualnej i instalowanie serwera sieci Web NGINX
> * Wsuń certyfikat do maszyny wirtualnej i skonfiguruj NGINX z powiązaniem TLS

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


## <a name="overview"></a>Omówienie
Usługa Azure Key Vault chroni klucze kryptograficzne i klucze tajne, takie jak certyfikaty lub hasła. Usługa Key Vault pomaga uprościć proces zarządzania certyfikatami i pozwala zachować kontrolę nad kluczami, które uzyskują dostęp do tych certyfikatów. Możesz utworzyć certyfikat z podpisem własnym wewnątrz usługi Key Vault lub przekazać istniejący zaufany certyfikat, który już posiadasz.

Zamiast używania niestandardowego obrazu maszyny wirtualnej, który zawiera wbudowane certyfikaty, należy wstrzyknąć certyfikaty do uruchomionej maszyny wirtualnej. Ten proces zapewnia, że podczas wdrażania na serwerze sieci Web zostaną zainstalowane najbardziej aktualne certyfikaty. Jeśli odnowisz lub zamienisz certyfikat, nie musisz też tworzyć nowego niestandardowego obrazu maszyny wirtualnej. Najnowsze certyfikaty są automatycznie wstrzykiwane podczas tworzenia dodatkowych maszyn wirtualnych. W trakcie całego procesu certyfikaty nigdy nie opuszczają platformy Azure oraz nie są udostępniane w skrypcie, historii wiersza polecenia ani w szablonie.


## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault
Aby można było utworzyć usługę Key Vault i certyfikaty, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupSecureWeb* w lokalizacji *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Następnie utwórz usługę Key Vault za pomocą polecenia [az keyvault create](/cli/azure/keyvault) i włącz ją do użycia podczas wdrażania maszyny wirtualnej. Każda usługa Key Vault wymaga unikatowej nazwy, która powinna zawierać tylko małe litery. Zamień *\<mykeyvault>* w poniższym przykładzie na własną unikatową nazwę Key Vault:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generowanie certyfikatu i zapisywanie go w usłudze Key Vault
Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę, używając polecenia [az keyvault certificate import](/cli/azure/keyvault/certificate). W tym samouczku poniższy przykład przedstawia, jak można wygenerować certyfikat z podpisem własnym za pomocą polecenia [az keyvault certificate create](/cli/azure/keyvault/certificate) z użyciem domyślnych zasad certyfikatów:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Przygotowywanie certyfikatu do użycia z maszyną wirtualną
Aby użyć certyfikatu podczas tworzenia maszyny wirtualnej, uzyskaj identyfikator certyfikatu za pomocą polecenia [az keyvault secret list-versions](/cli/azure/keyvault/secret). Przekonwertuj certyfikat za pomocą polecenia [az vm secret format](/cli/azure/vm/secret#az-vm-secret-format). W poniższym przykładzie przypisano dane wyjściowe tych poleceń do zmiennych w celu łatwiejszego użycia w następnych krokach:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret" -g myResourceGroupSecureWeb --keyvault $keyvault_name)
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Tworzenie konfiguracji cloud-init do zabezpieczenia serwera NGINX
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest uruchamiany w trakcie początkowego rozruchu, więc do zastosowania konfiguracji nie są wymagane żadne dodatkowe kroki ani agenci.

Podczas tworzenia maszyny wirtualnej certyfikaty i klucze są przechowywane w chronionym katalogu */var/lib/agentawaagent/*. Aby zautomatyzować dodawanie certyfikatu do maszyny wirtualnej i konfigurowanie serwera sieci Web, użyj pakietu cloud-init. W tym przykładzie zainstalujesz i skonfigurujesz serwer sieci Web NGINX. Ten sam proces można zastosować do zainstalowania i skonfigurowania serwera Apache. 

Utwórz plik o nazwie *cloud-init-web-server.txt* i wklej następującą konfigurację:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Tworzenie bezpiecznej maszyny wirtualnej
Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Dane certyfikatu są wstrzykiwane z usługi Key Vault za pomocą parametru `--secrets`. Konfiguracja pakietu cloud-init jest przekazywana za pomocą parametru `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Utworzenie maszyny wirtualnej, zainstalowanie pakietów i uruchomienie aplikacji potrwa kilka minut. Podczas tworzenia maszyny wirtualnej zanotuj wartość `publicIpAddress` wyświetlaną w wierszu polecenia platformy Azure. Ten adres służy do uzyskiwania dostępu do witryny w przeglądarce sieci Web.

Aby zezwolić na bezpieczny ruch internetowy do maszyny wirtualnej, otwórz port 443 z Internetu za pomocą polecenia [az vm open-port](/cli/azure/vm):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testowanie bezpiecznej aplikacji internetowej
Teraz możesz otworzyć przeglądarkę internetową i wprowadzić *https: \/ \/ \<publicIpAddress> * na pasku adresu. Podaj własny publiczny adres IP z procesu tworzenia maszyny wirtualnej. Jeśli został użyty certyfikat z podpisem własnym, zaakceptuj ostrzeżenie dotyczące zabezpieczeń:

![Akceptowanie ostrzeżenia dotyczącego zabezpieczeń w przeglądarce sieci Web](./media/tutorial-secure-web-server/browser-warning.png)

Zostanie wyświetlona zabezpieczona witryna serwera NGINX, tak jak w poniższym przykładzie:

![Wyświetlanie uruchomionej zabezpieczonej witryny serwera NGINX](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Następne kroki

W tym samouczku serwer sieci Web NGINX został zabezpieczony za pomocą certyfikatu TLS/SSL przechowywanego w Azure Key Vault. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie usługi Azure Key Vault
> * Generowanie lub przekazywanie certyfikatu do usługi Key Vault
> * Tworzenie maszyny wirtualnej i instalowanie serwera sieci Web NGINX
> * Wsuń certyfikat do maszyny wirtualnej i skonfiguruj NGINX z powiązaniem TLS

Użyj tego linku, aby wyświetlić przykłady wstępnie utworzonych skryptów maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Przykłady skryptów maszyn wirtualnych z systemem Linux](./cli-samples.md)
