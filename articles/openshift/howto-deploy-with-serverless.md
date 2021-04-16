---
title: Wdrażanie aplikacji na serwerze przy użyciu Azure Red Hat OpenShift OpenShift Serverless
description: Dowiedz się, jak wdrożyć aplikację w Azure Red Hat OpenShift openshift serverless
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, bez serwera
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532812"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>Wdrażanie aplikacji na serwerze przy użyciu Azure Red Hat OpenShift OpenShift Serverless

W tym artykule wdrożysz aplikację w klastrze Azure Red Hat OpenShift [openshift serverless.](https://www.openshift.com/learn/topics/serverless) OpenShift Serverless ułatwia deweloperom wdrażanie i uruchamianie aplikacji, które będą skalowane w górę lub do zera na żądanie, eliminując zużycie zasobów, gdy nie są one w użyciu.

Kod aplikacji można spakować w kontenerze wraz z odpowiednimi środowiskami uruchomieniowym, a funkcje bez serwera uruchamiają kontenery aplikacji, gdy zostaną wyzwolone przez zdarzenie. Aplikacje mogą być wyzwalane przez różne źródła zdarzeń, takie jak zdarzenia z własnych aplikacji, usługi w chmurze od wielu dostawców, systemy oprogramowania jako usługi (SaaS) i inne usługi.

Zarządzanie wszystkimi aspektami wdrażania dowolnego kontenera w sposób bez serwera jest wbudowane bezpośrednio w interfejs OpenShift. Deweloperzy mogą wizualnie identyfikować zdarzenia, które napędzają uruchamianie konteneryzowanych aplikacji, z wieloma sposobami modyfikowania parametrów zdarzenia. Aplikacje bez serwera OpenShift można zintegrować z innymi usługami OpenShift, takimi jak OpenShift Pipelines, Service Mesh i Monitoring, zapewniając kompletne środowisko tworzenia i wdrażania aplikacji bez serwera.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Instalowanie narzędzia wiersza polecenia Knative (kn)

Najnowszą wersję interfejsu wiersza polecenia odpowiednią dla Twojej maszyny możesz pobrać ze strony <https://github.com/knative/client/releases/>

Jeśli uruchamiasz polecenia na platformie Azure Cloud Shell, pobierz najnowszy interfejs wiersza polecenia programu Knative dla systemu Linux.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>Uruchamianie konsoli sieci Web

Znajdź adres URL konsoli sieci Web klastra, uruchamiając:

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

Adres URL powinien być podobny do tego.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

Uruchom adres URL konsoli w przeglądarce i zaloguj się przy użyciu `kubeadmin` poświadczeń.

:::image type="content" source="media/login.png" alt-text="Azure Red Hat OpenShift ekranu logowania":::

## <a name="install-the-openshift-serverless-operator"></a>Instalowanie operatora bez serwera OpenShift

Po zalogowaniu się do konsoli internetowej openshift upewnij się, że jesteś w perspektywie *administratora.* Otwórz centrum *Operatora,* poszukaj operatora **OpenShift Serverless** i wybierz go.

![Poszukaj operatora bez serwera OpenShift](media/serverless/serverless-operatorhub.png)

Następnie otwórz stronę instalacji operatora, klikając pozycję **Zainstaluj**.

![Kliknij pozycję Zainstaluj, aby zainstalować operator](media/serverless/serverless-clickinstall.png)

Wybierz odpowiedni kanał *aktualizacji* dla wersji Azure Red Hat OpenShift klastra i zainstaluj operator w przestrzeni `openshift-serverless` nazw . Przewiń w dół i kliknij pozycję **Zainstaluj**.

![Strona instalacji operatora](media/serverless/serverless-installpage.png)

W ciągu kilku minut na stronie stanu powinna być odzwierciedlone, że operator jest zainstalowany i jest gotowy do użycia. Kliknij przycisk **View Operator (Wyświetl operator),** aby kontynuować.

![Operator jest zainstalowany i jest gotowy do użycia](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Instalowanie obsługi knatywnej

Możliwość uruchamiania dowolnego kontenera w sposób bez serwera na platformie OpenShift Serverless jest możliwa dzięki nadrzędnej platformie Knative. Rozwiązania Knative rozszerzają platformę Kubernetes w celu zapewnienia zestawu składników do wdrażania i uruchamiania nowoczesnych aplikacji oraz zarządzania nimi przy użyciu metodologii bez użycia serwera.

### <a name="create-an-instance-of-the-knative-serving"></a>Tworzenie wystąpienia usługi Knative Serving

Przejdź do przestrzeni nazw, klikając listę rozwijaną projektu w lewym górnym rogu, a następnie w obszarze Dostarczone interfejsy API kliknij pozycję Utwórz wystąpienie na karcie `knative-serving` Knative Serving (Obsługa   *knatywna).*

![Kliknij, aby utworzyć wystąpienie usługi Knative Service](media/serverless/serverless-createknativeserving.png)

Zachowaj wartości domyślne i przewiń w dół na stronie *Tworzenie knatywnej obsługi,* aby kliknąć **przycisk** Utwórz.

![Zachowaj wartości domyślne i kliknij pozycję Utwórz](media/serverless/serverless-createknativeserving2.png)

Poczekaj, aż *w kolumnie Status* (Stan) będzie widać pozycję **Ready** (Gotowe), a następnie powinna zostać zainstalowana aplikacja OpenShift Serverless i wszystko będzie gotowe do utworzenia projektu OpenShift Serverless.

![Gotowa obsługa knatywna](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Tworzenie projektu bez serwera

Aby utworzyć nowy projekt o nazwie `demoserverless` , uruchom polecenie :

```azurecli-interactive
oc new-project demoserverless
```

Powinny zostać wyświetlony dane wyjściowe podobne do:

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

W menu *po* lewej  stronie przejdź do perspektywy dewelopera zamiast perspektywy administratorów i wybierz ją `demoserverless` na liście projektów. Następnie powinien być na *stronie Topologia* dla projektu.

![Azure Red Hat OpenShift topologii projektu](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Wdrażanie przy użyciu konsoli sieci Web

Z dostępnych opcji wdrażania aplikacji wybierz pozycję *Z usługi Git*. Spowoduje to trafić na stronę *Import from Git (Importowanie z usługi Git).* Użyj `https://github.com/sclorg/django-ex.git` polecenia jako adresu URL **repozytorium Git.** Przykładowa aplikacja internetowa jest implementowana przy użyciu języka programowania Python.

![Azure Red Hat OpenShift projektu z usługi Git](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift wykrywa, że jest to projekt języka Python, i wybiera odpowiedni obraz konstruktora.

Przewiń w *dół do opcji Zasoby* i upewnij się, że jako typ zasobu do wygenerowania wybrano opcję **Knative Service.** Ta akcja spowoduje utworzenie usługi Knative Service, typu wdrożenia, które umożliwia skalowanie bez serwera OpenShift do zera w przypadku bezczynności.

![Azure Red Hat OpenShift projektu — Knative](media/serverless/serverless-knative.png)


Gdy wszystko będzie gotowe, w dolnej części strony kliknij pozycję **Utwórz.** Spowoduje to utworzenie zasobów do zarządzania kompilacją i wdrażaniem aplikacji. Nastąpi przekierowanie do przeglądu topologii projektu.

Omówienie topologii zawiera wizualną reprezentację wdrożonej aplikacji. W tym widoku można zobaczyć ogólną strukturę aplikacji.

Poczekaj na zakończenie kompilacji. Może to potrwać kilka minut. Po zakończeniu kompilacji w lewym dolnym rogu usługi zostanie wyświetlony zielony znacznik wyboru.

![Azure Red Hat OpenShift projektu — kompilacja pomyślna](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>Wyświetlanie skali aplikacji

Na liście *Opcje wyświetlania* w górnej części widoku Topologia kliknij pozycję *Liczba zasobników.* Poczekaj, aż liczba zasobników będzie skalowana w dół do zera. Skalowanie w dół może potrwać kilka minut.

![Azure Red Hat OpenShift projektu — skalowana do zera](media/serverless/serverless-scaledtozero.png)

Kliknij *ikonę Otwórz adres URL* w prawym górnym rogu panelu Usługa knatywna. Aplikacja zostanie otwarta na nowej karcie. Zamknij nową kartę przeglądarki i wróć do widoku Topologia. W widoku Topologia można zobaczyć, że aplikacja została przeskalowana w górę do jednego zasobnika w celu uwzględnienia żądania. Po kilku minutach aplikacja będzie skalowana z powrotem do zera zasobników.

![Azure Red Hat OpenShift projektu — skalowana do jednej](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Wymuś nową poprawkę i ustaw dystrybucję ruchu

Przy każdej aktualizacji konfiguracji usługi tworzona jest nowa poprawka usługi. Trasa usługi domyślnie wskazuje cały ruch do najnowszej gotowej poprawki. To zachowanie można zmienić, definiując, która poprawka pobiera część ruchu. Usługi knatywne umożliwiają mapowanie ruchu, co oznacza, że poprawki usługi mogą być mapowane na przydzieloną część ruchu. Mapowanie ruchu zapewnia również opcję tworzenia unikatowych adresów URL dla określonych poprawek.

W sekcji *Topologia* kliknij poprawkę wewnątrz usługi, aby wyświetlić jej szczegóły. Znaczki pod pierścieniem Zasobnik i w górnej części panelu szczegółów powinny być `(REV)` . Na panelu bocznym na karcie *Zasoby* przewiń w dół i kliknij konfigurację skojarzoną z Twoją usługą.

![Azure Red Hat OpenShift projektu — poprawka](media/serverless/serverless-revdetails.png)

Wymusz aktualizację konfiguracji, przełączając się na kartę *YAML* i przewijając w dół, aby edytować wartość `timeoutSeconds` na `301` . Kliknij pozycję **Zapisz,** aby zapisać zaktualizowaną konfigurację. W rzeczywistym scenariuszu taką aktualizację konfiguracji można również wyzwolić, aktualizując tag obrazu kontenera.

![Wymuszanie nowej poprawki przez zaktualizowanie konfiguracji](media/serverless/serverless-confupdate.png)

Wstecz widoku *Topologia* zobaczysz, że została wdrożona nowa poprawka. Kliknij usługę kończącą się na wskaźniku , a następnie kliknij przycisk Ustaw dystrybucję ruchu. Teraz powinno być możliwe podzielenie ruchu między różne `(KSVC)` wersje usługi. 

![Ustawianie dystrybucji ruchu](media/serverless/serverless-trafficdist.png)

Widok *Topologia* będzie teraz pokazywać, w jaki sposób ruch jest dystrybuowany między dwiema poprawkami.

![Przegląd dystrybucji ruchu](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Korzystanie z narzędzia wiersza polecenia Knative (kn)

W poprzednich krokach konsola sieci Web OpenShift została użyta do utworzenia i wdrożenia aplikacji w bez serwerach OpenShift. Ponieważ pod usługą OpenShift Serverless działa system Knative, do tworzenia usług Knative można również użyć narzędzia wiersza polecenia Knative.

> [!NOTE]
> Jeśli interfejs wiersza polecenia nie został jeszcze zainstalowany, wykonaj kroki opisane w sekcji wymagań wstępnych `kn` tego artykułu. Upewnij się również, że zalogowano się przy użyciu narzędzia wiersza polecenia OpenShift `oc` .

Użyjemy obrazu kontenera, który został już sbudowaną w pliku `quay.io/rhdevelopers/knative-tutorial-greeter` .

### <a name="deploy-a-service"></a>Wdrażanie usługi

Aby wdrożyć usługę, uruchom następujące polecenie:

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

Zobaczysz dane wyjściowe podobne do poniższych.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Listę tras w projekcie można pobrać, uruchamiając:

```azurecli-interactive
kn route list
```

Zostanie wyświetlona lista tras w przestrzeni nazw . Otwórz adres URL w przeglądarce, aby wyświetlić wdrożoną usługę.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>Wdrażanie nowej wersji usługi

Wd wdrażaj nową wersję aplikacji, uruchamiając poniższe polecenie, `:latest` przekazując tag obrazu i zmienną środowiskową `MESSAGE_PREFIX` :

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

Otrzymasz potwierdzenie wdrożenia nowej `greeter-v2` poprawki.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Aby wyświetlić listę wszystkich poprawek i ich dystrybucji ruchu, uruchom następujące polecenie:

```azurecli-interactive
kn revision list
```

Zostanie wyświetlona lista podobna do poniższej. Należy pamiętać, że nowa poprawka ma 100% ruchu.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Wdrożenia niebieskie/zielone i canary

Domyślnie po wdrożeniu nowej poprawki uzyskuje ona 100% ruchu. Załóżmy, że chcesz zaimplementować strategię wdrażania niebiesko-zielona, w której możesz szybko wycofać starszą wersję aplikacji. Knative ułatwia to.

Zaktualizuj usługę, aby utworzyć trzy tagi ruchu podczas wysyłania 100% ruchu do

- **current**: wskazuje bieżącą wdrożoną wersję
- **prev**: wskazuje poprzednią wersję
- **latest**: zawsze wskazuje najnowszą wersję

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

Otrzymasz potwierdzenie podobne do poniższego.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Lista tras za pomocą poniższego polecenia:

```azurecli-interactive
kn route describe greeter
```

Otrzymasz dane wyjściowe pokazujące adresy URL poszczególnych tagów wraz z rozkładem ruchu.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Załóżmy, że chcesz szybko wycofać poprzednią wersję. Możesz zaktualizować dystrybucję ruchu, aby wysyłać 100% ruchu do poprzedniego tagu:

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Sprawdź ponownie, wymieniając trasy za pomocą poniższego polecenia:

```azurecli-interactive
kn route describe greeter
```

Otrzymasz dane wyjściowe pokazujące, że 100% dystrybucji ruchu jest w poprzedniej wersji.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Poeksumuj z rozkładem ruchu podczas odświeżania trasy głównej `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` (w tym przypadku) w przeglądarce.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po pracy z aplikacją możesz uruchomić następujące polecenie, aby usunąć projekt:

```azurecli-interactive
oc delete project demoserverless
```

Klaster można również usunąć, korzystając z instrukcji z tematu Samouczek: usuwanie klastra [Azure Red Hat OpenShift 4.](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku opisano sposób:
> [!div class="checklist"]
>
> * Instalowanie bezserwerowego operatora OpenShift i usługi Knative Serving
> * Wdrażanie projektu bez serwera przy użyciu konsoli sieci Web
> * Wdrażanie projektu bez serwera przy użyciu interfejsu wiersza polecenia rozwiązania Knative (kn)
> * Konfigurowanie wdrożeń niebieski/zielony i wdrożeń canary przy użyciu interfejsu wiersza polecenia rozwiązania Knative (kn)

Dowiedz się więcej na temat tworzenia i wdrażania bez serwera aplikacji opartych na zdarzeniach na serwerze Azure Red Hat OpenShift przy użyciu [rozwiązania OpenShift Serverless,](https://www.openshift.com/learn/topics/serverless)korzystając z dokumentacji Wprowadzenie do usługi [OpenShift Serverless](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) i dokumentacji Tworzenie aplikacji bez serwera i zarządzanie [nimi.](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html)
