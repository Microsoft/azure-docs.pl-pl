---
title: Samouczek — Dostosowywanie maszyny wirtualnej z systemem Linux przy użyciu funkcji Cloud-init na platformie Azure
description: W tym samouczku dowiesz się, jak za pomocą funkcji Cloud-init i Key Vault dostosować maszyny wirtualne z systemem Linux podczas pierwszego rozruchu na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 6dd10039ba5c71d3a787761914b111e7828c47d3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552613"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Samouczek — dostosowywanie maszyny wirtualnej z systemem Linux na platformie Azure podczas pierwszego rozruchu za pomocą pakietu cloud-init

W poprzednim samouczku przedstawiono sposób nawiązywania połączenia SSH z maszyną wirtualną oraz ręcznego instalowania NGINX. Aby w szybki i spójny sposób utworzyć maszyny wirtualne, stosuje się na ogół jakąś formę automatyzacji. Typową metodą dostosowywania maszyny wirtualnej podczas pierwszego rozruchu jest użycie pakietu [cloud-init](https://cloudinit.readthedocs.io). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie pliku konfiguracji cloud-init
> * Tworzenie maszyny wirtualnej korzystającej z pakietu cloud-init
> * Wyświetlanie uruchomionej aplikacji Node.js po utworzeniu maszyny wirtualnej
> * Używanie usługi Key Vault do bezpiecznego przechowywania certyfikatów
> * Automatyzacja bezpiecznych wdrożeń NGINX przy użyciu pakietu cloud-init

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="cloud-init-overview"></a>Omówienie pakietu cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest uruchamiany w trakcie początkowego rozruchu, więc do zastosowania konfiguracji nie są wymagane żadne dodatkowe kroki ani agenci.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Wraz z partnerami pracujemy nad tym, aby pakiet cloud-init był uwzględniany i uruchamiany w obrazach, których dostarczają na platformie Azure. Aby uzyskać szczegółowe informacje o obsłudze inicjowania w chmurze dla każdej dystrybucji, zobacz [Obsługa funkcji Cloud-init dla maszyn wirtualnych na platformie Azure](using-cloud-init.md).


## <a name="create-cloud-init-config-file"></a>Tworzenie pliku konfiguracji cloud-init
Aby zobaczyć pakiet cloud-init w akcji, utwórz maszynę wirtualną instalującą NGINX i uruchamiającą prostą aplikację Node.js „Hello World”. Następująca konfiguracja cloud-init instaluje wymagane pakiety, tworzy aplikację Node.js, a następnie inicjuje i uruchamia aplikację.

W wierszu polecenia bash lub w Cloud Shell Utwórz plik o nazwie *cloud-init.txt* i wklej następującą konfigurację. Na przykład wpisz, `sensible-editor cloud-init.txt` Aby utworzyć plik i wyświetlić listę dostępnych edytorów. Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Aby uzyskać więcej informacji o opcjach konfiguracji pakietu cloud-init, zobacz [przykłady konfiguracji pakietu cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Zanim będzie można utworzyć maszynę wirtualną, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupAutomate* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM*:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Utworzenie maszyny wirtualnej, zainstalowanie pakietów i uruchomienie aplikacji potrwa kilka minut. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Może upłynąć kilka minut, zanim będzie można uzyskać dostęp do aplikacji. Podczas tworzenia maszyny wirtualnej zanotuj wartość `publicIpAddress` wyświetlaną w wierszu polecenia platformy Azure. Ten adres służy do uzyskiwania dostępu do aplikacji Node.js w przeglądarce internetowej.

Aby zezwolić na ruch internetowy do maszyny wirtualnej, otwórz port 80 z Internetu za pomocą polecenia [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>Testowanie aplikacji internetowej
Teraz możesz otworzyć przeglądarkę internetową i wprowadzić *http: \/ \/ \<publicIpAddress>* na pasku adresu. Podaj własny publiczny adres IP z procesu tworzenia maszyny wirtualnej. Aplikacja Node.js jest wyświetlana jak w poniższym przykładzie:

![Wyświetlanie uruchomionej witryny serwera NGINX](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Wstrzykiwanie certyfikatów z usługi Key Vault
Ta opcjonalna sekcja przedstawia sposób bezpiecznego przechowywania certyfikatów w usłudze Azure Key Vault oraz wstrzykiwania ich podczas wdrażania maszyny wirtualnej. Zamiast używania niestandardowego obrazu zawierającego wbudowane certyfikaty, ten proces upewnia się, że najbardziej aktualne certyfikaty są wstrzykiwane do maszyny wirtualnej podczas pierwszego rozruchu. W trakcie procesu certyfikaty nigdy nie opuszczają platformy Azure oraz nie są udostępniane w skrypcie, historii wiersza polecenia ani w szablonie.

Usługa Azure Key Vault chroni klucze kryptograficzne i klucze tajne, takie jak certyfikaty lub hasła. Usługa Key Vault pomaga usprawniać proces zarządzania kluczami i pozwala zachować kontrolę nad kluczami, które mają dostęp do danych i szyfrują je. W tym scenariuszu wprowadzono pewne pojęcia usługi Key Vault w celu utworzenia i użycia certyfikatu, chociaż nie jest to wyczerpujące omówienie sposobu użycia usługi Key Vault.

Poniższa procedura przedstawia:

- Tworzenie usługi Azure Key Vault
- Generowanie lub przekazywanie certyfikatu do usługi Key Vault
- Tworzenie klucza tajnego z certyfikatu w celu wstrzyknięcia do maszyny wirtualnej
- Tworzenie maszyny wirtualnej i wstrzykiwanie certyfikatu

### <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault
Najpierw utwórz usługę Key Vault za pomocą polecenia [az keyvault create](/cli/azure/keyvault#az-keyvault-create) i włącz ją do użycia podczas wdrażania maszyny wirtualnej. Każda usługa Key Vault wymaga unikatowej nazwy, która powinna zawierać tylko małe litery. Zamień wartość `mykeyvault` w poniższym przykładzie na własną unikatową nazwę usługi Key Vault:

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Generowanie certyfikatu i zapisywanie go w usłudze Key Vault
Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę, używając polecenia [az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import). W tym samouczku poniższy przykład przedstawia, jak można wygenerować certyfikat z podpisem własnym za pomocą polecenia [az keyvault certificate create](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) z użyciem domyślnych zasad certyfikatów:

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Przygotowywanie certyfikatu do użycia z maszyną wirtualną
Aby użyć certyfikatu podczas tworzenia maszyny wirtualnej, uzyskaj identyfikator certyfikatu za pomocą polecenia [az keyvault secret list-versions](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions). Maszyna wirtualna wymaga wstrzyknięcia certyfikatu w określonym formacie podczas rozruchu, więc należy skonwertować certyfikat przy użyciu polecenia [az vm secret format](/cli/azure/vm). W poniższym przykładzie przypisano dane wyjściowe tych poleceń do zmiennych w celu łatwiejszego użycia w następnych krokach:

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Tworzenie konfiguracji cloud-init do zabezpieczenia serwera NGINX
Podczas tworzenia maszyny wirtualnej certyfikaty i klucze są przechowywane w chronionym katalogu */var/lib/agentawaagent/*. Aby zautomatyzować dodawanie certyfikatu do maszyny wirtualnej i konfigurowanie serwera NGINX, możesz użyć zaktualizowanego pliku konfiguracji cloud-init z poprzedniego przykładu.

Utwórz plik o nazwie *cloud-init-secured.txt* i wklej następującą konfigurację. Jeśli używasz Cloud Shell, Utwórz plik konfiguracji Cloud-init w tym miejscu, a nie na komputerze lokalnym. Na przykład wpisz, `sensible-editor cloud-init-secured.txt` Aby utworzyć plik i wyświetlić listę dostępnych edytorów. Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Tworzenie bezpiecznej maszyny wirtualnej
Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Dane certyfikatu są wstrzykiwane z usługi Key Vault za pomocą parametru `--secrets`. Tak jak w poprzednim przykładzie możesz też przekazać plik konfiguracji cloud-init za pomocą parametru `--custom-data`:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Utworzenie maszyny wirtualnej, zainstalowanie pakietów i uruchomienie aplikacji potrwa kilka minut. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Może upłynąć kilka minut, zanim będzie można uzyskać dostęp do aplikacji. Podczas tworzenia maszyny wirtualnej zanotuj wartość `publicIpAddress` wyświetlaną w wierszu polecenia platformy Azure. Ten adres służy do uzyskiwania dostępu do aplikacji Node.js w przeglądarce internetowej.

Aby zezwolić na bezpieczny ruch internetowy do maszyny wirtualnej, otwórz port 443 z Internetu za pomocą polecenia [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>Testowanie bezpiecznej aplikacji internetowej
Teraz możesz otworzyć przeglądarkę internetową i wprowadzić *https: \/ \/ \<publicIpAddress>* na pasku adresu. Podaj własny publiczny adres IP, widoczny w danych wyjściowych poprzedniego procesu tworzenia maszyny wirtualnej. Jeśli został użyty certyfikat z podpisem własnym, zaakceptuj ostrzeżenie dotyczące zabezpieczeń:

![Akceptowanie ostrzeżenia dotyczącego zabezpieczeń w przeglądarce sieci Web](./media/tutorial-automate-vm-deployment/browser-warning.png)

Zostanie wyświetlona zabezpieczona witryna serwera NGINX oraz aplikacja Node.js, tak jak w poniższym przykładzie:

![Wyświetlanie uruchomionej zabezpieczonej witryny serwera NGINX](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Następne kroki
W tym samouczku skonfigurowano maszyny wirtualne podczas pierwszego rozruchu przy użyciu pakietu cloud-init. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie pliku konfiguracji cloud-init
> * Tworzenie maszyny wirtualnej korzystającej z pakietu cloud-init
> * Wyświetlanie uruchomionej aplikacji Node.js po utworzeniu maszyny wirtualnej
> * Używanie usługi Key Vault do bezpiecznego przechowywania certyfikatów
> * Automatyzacja bezpiecznych wdrożeń NGINX przy użyciu pakietu cloud-init

Przejdź do następnego samouczka, aby dowiedzieć się, jak tworzyć niestandardowe obrazy maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych obrazów maszyn wirtualnych](./tutorial-custom-images.md)
