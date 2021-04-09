---
title: Dodawanie lub aktualizowanie klucza tajnego Red Hat na platformie Azure Red Hat OpenShift 4
description: Dodaj lub zaktualizuj klucz tajny Red Hat dla istniejących klastrów 4. x ARO
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/21/2020
keywords: Ściąganie kluczy tajnych, ARO, OpenShift, Red Hat
ms.openlocfilehash: 58c0eb2be3423783a69d005277ffe75aaf59415f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633737"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Dodawanie lub aktualizowanie klucza tajnego Red Hat na platformie Azure Red Hat OpenShift 4

W tym przewodniku opisano Dodawanie lub aktualizowanie wpisu tajnego Red Hat dla istniejącego klastra usługi Azure Red Hat OpenShift (ARO) 4. x.

Jeśli tworzysz klaster po raz pierwszy, możesz dodać klucz tajny ściągania podczas tworzenia klastra. Aby uzyskać więcej informacji na temat tworzenia klastra ARO przy użyciu klucza tajnego Red Hat, zobacz [Tworzenie klastra usługi Azure Red Hat OpenShift 4](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym przewodniku przyjęto założenie, że masz istniejący klaster usługi Azure Red Hat OpenShift 4. Upewnij się, że masz uprawnienia dostępu administratora do klastra.

## <a name="prepare-your-pull-secret"></a>Przygotuj klucz tajny ściągania
Podczas tworzenia klastra wypychania bez dodawania klucza tajnego Red Hat, w klastrze jest nadal tworzony klucz tajny ściągania. Jednak ten klucz tajny ściągania nie jest w pełni wypełniony.

Ta sekcja zawiera instrukcje dotyczące aktualizowania tego ściągania hasła z użyciem dodatkowych wartości z klucza tajnego Red Hat.

1. Pobierz wpis tajny o nazwie `pull-secret` w `openshift-config` przestrzeni nazw i Zapisz go w osobnym pliku, uruchamiając następujące polecenie: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    Dane wyjściowe powinny być podobne do następujących. (Należy zauważyć, że rzeczywista wartość wpisu tajnego została usunięta).

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Przejdź do [portalu Menedżera klastra Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) i wybierz pozycję **Pobierz klucz tajny**. Twoje tajne hasło Red Hat będzie wyglądać następująco. (Należy zauważyć, że rzeczywiste wartości tajne zostały usunięte).

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Edytuj plik klucza tajnego ściągania pochodzący z klastra, dodając wpisy znajdujące się w Twoim kluczu tajnym Red Hat. 

    > [!IMPORTANT]
    > Dołączenie `cloud.openshift.com` wpisu z klucza tajnego Red Hat spowoduje, że klaster zacznie wysyłać dane telemetryczne do Red Hat. Ta sekcja jest uwzględniana tylko wtedy, gdy chcesz wysłać dane telemetryczne. W przeciwnym razie pozostaw poniższe sekcje.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Nie usuwaj ani nie zmieniaj `arosvc.azurecr.io` wpisu z klucza tajnego ściągania. Ta sekcja jest wymagana do poprawnego funkcjonowania klastra.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    Ostatni plik powinien wyglądać podobnie do poniższego. (Należy zauważyć, że rzeczywiste wartości tajne zostały usunięte).

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Upewnij się, że plik jest prawidłowym plikiem JSON. Istnieje wiele sposobów weryfikowania danych JSON. W poniższym przykładzie zastosowano JQ:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Jeśli w pliku znajduje się błąd, zostanie on wyświetlony jako `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>Dodawanie hasła ściągania do klastra

Uruchom następujące polecenie, aby zaktualizować klucz tajny ściągania.

> [!NOTE]
> Uruchomienie tego polecenia spowoduje ponowne uruchomienie węzłów klastra po ich zaktualizowaniu. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Po ustawieniu wpisu tajnego można włączyć operatorów z certyfikatem Red Hat.

### <a name="modify-the-configuration-files"></a>Modyfikowanie plików konfiguracji

Zmodyfikuj następujące obiekty, aby włączyć operatorów Red Hat.

Najpierw zmodyfikuj plik konfiguracji operatora Samples. Następnie można uruchomić następujące polecenie, aby edytować plik konfiguracji:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Zmień `spec.architectures.managementState` wartości i `status.architecture.managementState` z `Removed` na `Managed` . 

Poniższy fragment kodu YAML przedstawia tylko odpowiednie sekcje edytowanego pliku YAML:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Następnie uruchom następujące polecenie, aby edytować plik konfiguracji centrum operatora:  

```console
oc edit operatorhub cluster -o yaml
```

Zmień `Spec.Sources.Disabled` wartości i `Status.Sources.Disabled` z `true` na na `false` dla wszystkich źródeł, które chcesz włączyć.

Poniższy fragment kodu YAML przedstawia tylko odpowiednie sekcje edytowanego pliku YAML:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Zapisz plik, aby zastosować zmiany.

## <a name="validate-that-your-secret-is-working"></a>Weryfikowanie, czy klucz tajny działa

Po dodaniu hasła ściągania i zmodyfikowaniu prawidłowych plików konfiguracji klaster może zająć kilka minut. Aby sprawdzić, czy klaster został zaktualizowany, uruchom następujące polecenie, aby wyświetlić dostępne źródła operatorów i Red Hat:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Jeśli nie widzisz operatorów certyfikowanych i Red Hat, odczekaj kilka minut i spróbuj ponownie.

Aby upewnić się, że klucz tajny ściągania został zaktualizowany i działa prawidłowo, Otwórz OperatorHub i Wyszukaj dowolny operator zweryfikowanej Red Hat. Na przykład sprawdź, czy operator magazynu kontenerów OpenShift jest dostępny, i sprawdź, czy masz uprawnienia do instalacji.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o wpisach tajnych Red Hat, zobacz [using Image](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html)Secret.

Aby dowiedzieć się więcej na temat Red Hat OpenShift 4, zobacz artykuł [Red Hat OpenShift Container platform dokumentacji](https://docs.openshift.com/container-platform/4.6/welcome/index.html).
