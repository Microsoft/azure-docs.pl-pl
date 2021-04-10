---
title: Dostosowywanie CoreDNS dla usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak dostosować CoreDNS, dodając poddomeny lub rozszerzając niestandardowe punkty końcowe DNS przy użyciu usługi Azure Kubernetes Service (AKS)
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368685"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Dostosowywanie serwera CoreDNS w usłudze Azure Kubernetes Service

Usługa Azure Kubernetes Service (AKS) używa projektu [CoreDNS][coredns] do zarządzania i rozpoznawania DNS klastra w przypadku wszystkich klastrów *1.12. x* i wyższych. Wcześniej został użyty projekt polecenia-DNS. Ten projekt polecenia-DNS jest obecnie przestarzały. Aby uzyskać więcej informacji na temat dostosowywania CoreDNS i Kubernetes, zapoznaj się z [oficjalną dokumentacją dla nadrzędnego][corednsk8s].

Ponieważ AKS jest usługą zarządzaną, nie można modyfikować konfiguracji głównej dla CoreDNS ( *CoreFile*). Zamiast tego należy użyć Kubernetes *ConfigMap* , aby zastąpić ustawienia domyślne. Aby wyświetlić domyślny AKS CoreDNS ConfigMaps, użyj `kubectl get configmaps --namespace=kube-system coredns -o yaml` polecenia.

W tym artykule pokazano, jak używać ConfigMaps do podstawowych opcji dostosowania CoreDNS w programie AKS. Takie podejście różni się od konfigurowania CoreDNS w innych kontekstach, takich jak korzystanie z CoreFile. Sprawdź, czy wersja CoreDNS jest uruchomiona, ponieważ wartości konfiguracji mogą ulec zmianie między wersjami.

> [!NOTE]
> `kube-dns` oferowane są różne [Opcje dostosowywania][kubednsblog] za pośrednictwem mapy konfiguracji Kubernetes. CoreDNS **nie** jest wstecznie zgodna z polecenia-DNS. Wszystkie poprzednio używane dostosowania należy zaktualizować do użytku z programem CoreDNS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Podczas tworzenia konfiguracji, takiej jak poniższe przykłady, nazwy w sekcji *danych* muszą kończyć się na *. Server* lub *. override*. Ta konwencja nazewnictwa jest definiowana w domyślnej AKS CoreDNS Configmap, którą można wyświetlić za pomocą `kubectl get configmaps --namespace=kube-system coredns -o yaml` polecenia.

## <a name="what-is-supportedunsupported"></a>Co to jest obsługiwane/nieobsługiwane

Obsługiwane są wszystkie wbudowane wtyczki CoreDNS. Nie są obsługiwane wtyczki dodatków/innych firm.

## <a name="rewrite-dns"></a>Zapisz ponownie system DNS

Jednym z scenariuszy jest wykonanie na bieżąco nazw DNS. W poniższym przykładzie Zastąp wartość `<domain to be written>` własną w pełni kwalifikowaną nazwą domeny. Utwórz plik o nazwie `corednsms.yaml` i wklej następujący Przykładowa konfiguracja:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> W przypadku przekierowania do serwera DNS, takiego jak CoreDNS IP usługi, ten serwer DNS musi być w stanie rozpoznać zapisaną nazwę domeny.

Utwórz ConfigMap za pomocą polecenia [polecenia kubectl Apply ConfigMap][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f corednsms.yaml
```

Aby sprawdzić, czy dostosowania zostały zastosowane, użyj [polecenia kubectl Get configmaps][kubectl-get] i określ swój *coredns-Custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Teraz Wymuś ponowne załadowanie ConfigMap CoreDNS. Polecenie [polecenia kubectl Delete pod][kubectl delete] nie jest szkodliwe i nie powoduje wyłączenia czasu. Te `kube-dns` zasobniki są usuwane, a następnie program Kubernetes Scheduler ponownie tworzy te elementy. Te nowe zasobniki zawierają zmiany wartości TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Powyższe polecenie jest poprawne. Gdy zmieniamy `coredns` , wdrożenie jest pod nazwą **polecenia-DNS** .

## <a name="custom-forward-server"></a>Serwer niestandardowego przesyłania dalej

Jeśli musisz określić serwer usług przesyłania dalej dla ruchu sieciowego, możesz utworzyć ConfigMap, aby dostosować system DNS. W poniższym przykładzie zaktualizuj `forward` nazwę i adres przy użyciu wartości dla własnego środowiska. Utwórz plik o nazwie `corednsms.yaml` i wklej następujący Przykładowa konfiguracja:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Podobnie jak w poprzednich przykładach, należy utworzyć ConfigMap za pomocą polecenia [polecenia kubectl Apply ConfigMap][kubectl-apply] i określić nazwę manifestu YAML. Następnie Wymuś CoreDNS ponownego załadowania ConfigMap przy użyciu [polecenia kubectl Delete pod][kubectl delete] dla harmonogramu Kubernetes, aby je odtworzyć:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Korzystanie z domen niestandardowych

Może być konieczne skonfigurowanie domen niestandardowych, które mogą być rozwiązane wewnętrznie. Na przykład możesz chcieć rozwiązać niestandardową domenę *puglife. Local*, która nie jest prawidłową domeną najwyższego poziomu. Bez niestandardowej domeny ConfigMap klaster AKS nie może rozpoznać adresu.

W poniższym przykładzie zaktualizuj domenę niestandardową i adres IP, aby skierować ruch do wartości dla własnego środowiska. Utwórz plik o nazwie `corednsms.yaml` i wklej następujący Przykładowa konfiguracja:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Podobnie jak w poprzednich przykładach, należy utworzyć ConfigMap za pomocą polecenia [polecenia kubectl Apply ConfigMap][kubectl-apply] i określić nazwę manifestu YAML. Następnie Wymuś CoreDNS ponownego załadowania ConfigMap przy użyciu [polecenia kubectl Delete pod][kubectl delete] dla harmonogramu Kubernetes, aby je odtworzyć:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domeny zastępcze

CoreDNS może również służyć do konfigurowania domen zastępczych. W poniższym przykładzie należy zaktualizować domeny niestandardowe i adresy IP przy użyciu wartości dla własnego środowiska. Utwórz plik o nazwie `corednsms.yaml` i wklej następujący Przykładowa konfiguracja:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Podobnie jak w poprzednich przykładach, należy utworzyć ConfigMap za pomocą polecenia [polecenia kubectl Apply ConfigMap][kubectl-apply] i określić nazwę manifestu YAML. Następnie Wymuś CoreDNS ponownego załadowania ConfigMap przy użyciu [polecenia kubectl Delete pod][kubectl delete] dla harmonogramu Kubernetes, aby je odtworzyć:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hostuje wtyczkę

Ponieważ wszystkie wbudowane wtyczki są obsługiwane, oznacza to, że wtyczka CoreDNS [hosts][coredns hosts] jest dostępna do dostosowania.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Włącz rejestrowanie dla debugowania zapytań DNS 

Aby włączyć rejestrowanie zapytań DNS, zastosuj następującą konfigurację w coredns-Custom ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano przykładowe scenariusze dotyczące dostosowywania CoreDNS. Aby uzyskać informacje o projekcie CoreDNS, zobacz [stronę projektu nadrzędnego CoreDNS][coredns].

Aby dowiedzieć się więcej o podstawowych pojęciach dotyczących sieci, zobacz [pojęcia dotyczące sieci dla aplikacji w AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
