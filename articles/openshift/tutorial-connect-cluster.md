---
title: Samouczek — łączenie z klastrem usługi Azure Red Hat OpenShift 4
description: Dowiedz się, jak połączyć klaster Microsoft Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 04/24/2020
ms.openlocfilehash: 5295f0fbd28140bc0b278f3a44915239df200a70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215667"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Samouczek: Nawiązywanie połączenia z klastrem usługi Azure Red Hat OpenShift 4

W tym samouczku druga część trzech z nich nawiązuje połączenie z klastrem usługi Azure Red Hat OpenShift z systemem OpenShift 4 jako użytkownikiem kubeadmin za pomocą konsoli sieci Web OpenShift. Omawiane kwestie:
> [!div class="checklist"]
> * Uzyskiwanie `kubeadmin` poświadczeń dla klastra
> * Instalowanie interfejsu wiersza polecenia OpenShift
> * Nawiązywanie połączenia z klastrem usługi Azure Red Hat OpenShift przy użyciu interfejsu wiersza polecenia OpenShift

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W poprzednich samouczkach został utworzony klaster Red Hat OpenShift platformy Azure. Jeśli nie wykonano tych kroków, a chcesz postępować zgodnie z [samouczkiem 1, Utwórz klaster Red Hat OpenShift 4 platformy Azure.](tutorial-create-cluster.md)

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Możesz zalogować się do klastra przy użyciu `kubeadmin` użytkownika.  Uruchom następujące polecenie, aby znaleźć hasło `kubeadmin` użytkownika.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

Następujące przykładowe dane wyjściowe pokazują, jak będzie zawierać hasło `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Adres URL konsoli klastra można znaleźć, uruchamiając następujące polecenie, które będzie wyglądać następująco `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Uruchom w przeglądarce adres URL konsoli i zaloguj się przy użyciu `kubeadmin` poświadczeń.

![Ekran logowania OpenShift na platformie Azure Red Hat](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instalowanie interfejsu wiersza polecenia OpenShift

Po zalogowaniu się do konsoli sieci Web OpenShift kliknij pozycję **?** w prawym górnym rogu, a następnie w **narzędziach wiersza polecenia**. Pobierz wydanie odpowiednie dla Twojej maszyny.

![Zrzut ekranu, który podświetla opcję narzędzi wiersza polecenia z listy po zaznaczeniu? .](media/aro4-download-cli.png)

Możesz również pobrać najnowszą wersję interfejsu wiersza polecenia odpowiednią dla komputera z programu <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

Jeśli używasz poleceń na Azure Cloud Shell, Pobierz najnowszą wersję interfejsu wiersza polecenia OpenShift 4 dla systemu Linux.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Nawiązywanie połączenia przy użyciu interfejsu wiersza polecenia OpenShift

Pobierz adres serwera interfejsu API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Zaloguj się do serwera interfejsu API klastra OpenShift przy użyciu następującego polecenia. Zamień na **\<kubeadmin password>** hasło, które zostało właśnie pobrane.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Uzyskiwanie `kubeadmin` poświadczeń dla klastra
> * Instalowanie interfejsu wiersza polecenia OpenShift
> * Nawiązywanie połączenia z klastrem usługi Azure Red Hat OpenShift przy użyciu interfejsu wiersza polecenia OpenShift

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)