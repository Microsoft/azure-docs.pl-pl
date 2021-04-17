---
title: Wdrażanie aplikacji ze źródła do Azure Red Hat OpenShift
description: Dowiedz się, jak wdrożyć aplikację na Azure Red Hat OpenShift z kodu źródłowego przy użyciu strategii kompilacji "źródło do obrazu" (S2I, source-to-image)
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, s2i, źródło do obrazu
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559121"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Wdrażanie aplikacji ze źródła do Azure Red Hat OpenShift

W tym artykule wdrożysz aplikację w klastrze Azure Red Hat OpenShift z kodu źródłowego przy użyciu kompilacji typu źródło-obraz (S2I). [Source-to-Image (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) to proces kompilacji do tworzenia powtarzalnych obrazów kontenerów z kodu źródłowego. S2I tworzy gotowe do uruchomienia obrazy, wstrzykiwając kod źródłowy do obrazu kontenera i umożliwiając kontenerowi przygotowanie tego kodu źródłowego do wykonania. Możesz mieć platformę OpenShift do skompilowania aplikacji ze źródła, aby ją wdrożyć, dzięki czemu nie musisz ręcznie konstruować kontenera przy każdej zmianie. Po powiadomieniu o zmianach kodu źródłowego system OpenShift może automatycznie kompilować i wdrażać nowe wersje.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Tworzenie projektu

Aby utworzyć nowy projekt o nazwie `demoproject` , uruchom polecenie :

```azurecli-interactive
oc new-project demoproject
```

Powinny zostać wyświetlony dane wyjściowe podobne do:

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>Uruchamianie konsoli sieci Web

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

W menu *po* lewej  stronie przejdź do perspektywy dewelopera zamiast perspektywy administratorów i wybierz ją `demoproject` na liście projektów. Następnie powinna być na *stronie Topologia* projektu.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift topologii projektu":::

Ponieważ projekt jest pusty, nie należy znaleźć żadnych obciążeń i zostaną przedstawione różne opcje wdrażania aplikacji.

## <a name="deploying-using-the-web-console"></a>Wdrażanie przy użyciu konsoli sieci Web

Z dostępnych opcji wdrażania aplikacji wybierz pozycję *Z usługi Git*. Spowoduje to trafić na stronę *Import from Git (Importowanie z usługi Git).* Użyj `https://github.com/sclorg/django-ex.git` polecenia jako adresu URL **repozytorium Git.** Przykładowa aplikacja internetowa jest implementowana przy użyciu języka programowania Python.

:::image type="content" source="media/s2i/from-git.png" alt-text="Azure Red Hat OpenShift projektu z usługi Git":::

> [!NOTE]
> OpenShift wykrywa, że jest to projekt w języku Python, i wybiera odpowiedni obraz konstruktora.

Przewiń w dół *do opcji Zaawansowane* i upewnij się, że zaznaczono pole wyboru Utwórz **trasę do** aplikacji. Ta akcja spowoduje utworzenie trasy openshift, czyli sposobu uwidocznienia usługi przez nadanie jej zewnętrznie dostępnej nazwy hosta.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Azure Red Hat OpenShift projektu z usługi Git — konfiguracja trasy":::

Gdy wszystko będzie gotowe, w dolnej części strony kliknij pozycję **Utwórz.** Spowoduje to utworzenie zasobów do zarządzania kompilacją i wdrażaniem aplikacji. Nastąpi przekierowanie do przeglądu topologii projektu.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Azure Red Hat OpenShift z usługi Git — topologia":::

Omówienie topologii zawiera wizualną reprezentację wdrożonej aplikacji. W tym widoku można zobaczyć ogólną strukturę aplikacji.

Można kliknąć ikonę Git, aby przejść do repozytorium Git, z którego został sbudowaną aplikacja. Ikona wyświetlana w lewym dolnym rogu pokazuje stan kompilacji aplikacji. Kliknięcie tej ikony spowoduje kliknięcie sekcji szczegółów kompilacji. Jeśli aplikacja ma widoczne trasy, można kliknąć ikonę w prawym górnym rogu, aby otworzyć adres URL utworzonej trasy aplikacji.

Podczas skalowania w górę lub w dół aplikacji, rozpoczynania wdrażania i ponownego tworzenia zasobników, reprezentacja aplikacji w widoku topologii będzie animowana, aby zapewnić podgląd tego, co się dzieje w czasie rzeczywistym.

Kliknięcie ikony aplikacji spowoduje, że zostanie wyświetlone więcej szczegółów, jak pokazano poniżej.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Azure Red Hat OpenShift projektu z usługi Git — szczegóły":::

## <a name="viewing-the-builder-logs"></a>Wyświetlanie dzienników konstruktora

Po zakończeniu kompilacji kliknij link *Wyświetl dzienniki* wyświetlany na *panelu Zasoby.*

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Azure Red Hat OpenShift projektu z usługi Git — dzienniki kompilacji":::

Pozwoli to monitorować postęp kompilacji podczas jej działania. W tym przypadku obraz konstruktora, Python, wstrzyknie kod źródłowy aplikacji do obrazu końcowego, zanim wypchnie go do wewnętrznego rejestru obrazów OpenShift. Kompilacja zostanie ukończona pomyślnie, gdy zostanie wyświetlony końcowy komunikat "Push successful" (Wypchniesz pomyślnie).

## <a name="accessing-the-application"></a>Uzyskiwanie dostępu do aplikacji

Po zakończeniu kompilacji obrazu aplikacji zostanie on wdrożony.

Kliknij pozycję *Topologia* na pasku menu po lewej stronie, aby wrócić do widoku topologii projektu. Podczas tworzenia aplikacji przy użyciu konsoli  sieci Web automatycznie utworzono trasę dla aplikacji i zostanie ona ujawniona poza klastrem. Adres URL, który może służyć do uzyskiwania dostępu  do aplikacji z przeglądarki internetowej, był widoczny na karcie Zasoby dla aplikacji, która była wcześniej widoczna.

W widoku topologii możesz przejść do adresu URL wdrożonej aplikacji, klikając ikonę w prawym górnym rogu pierścienia. Po zakończeniu wdrażania kliknij ikonę . Powinna zostać wyświetlony wdrożona aplikacja.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Azure Red Hat OpenShift projektu z usługi Git — przeglądanie aplikacji":::

## <a name="deploying-using-the-command-line"></a>Wdrażanie przy użyciu wiersza polecenia

Wiesz już, jak wdrożyć aplikację przy użyciu konsoli sieci Web. Teraz wdrożymy tę samą aplikację internetową, ale tym razem przy użyciu narzędzia `oc` wiersza polecenia.

Uruchom następujące polecenie, aby usunąć projekt i zacząć od początku:

```azurecli-interactive
oc delete project demoproject
```

Powinien zostać wyświetlony komunikat potwierdzający `demoproject` usunięcie.

```output
project.project.openshift.io "demoproject" deleted
```

Utwórz `demoproject` ponownie, uruchamiając:

```azurecli-interactive
oc new-project demoproject
```

W projekcie utwórz nową aplikację ze źródła w usłudze GitHub, określając konstruktor S2I dla najnowszej dostępnej wersji języka Python.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

Powinny zostać wyświetlane dane wyjściowe podobne do:

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift użyje nazwy repozytorium Git jako nazwy aplikacji. Sprawdź stan kompilacji i wdrożenia, uruchamiając:

```azurecli-interactive
oc status
```

Po zakończeniu kompilacji i wdrożenia powinny zostać wyświetlonych dane wyjściowe podobne do.

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

Aby uwidocznić aplikację poza klastrem OpenShift, musisz utworzyć trasę, uruchamiając:

```azurecli-interactive
oc expose service/django-ex
```

Powinno zostać otrzymanie potwierdzenia.

```output
route.route.openshift.io/django-ex exposed
```

Pobierz adres URL, uruchamiając:

```azurecli-interactive
oc get route django-ex
```

Powinna zostać ponownie przypisana nazwa hosta przypisana do trasy, za pomocą których można przejść do wdrożonej aplikacji.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Wyzwalanie nowej kompilacji binarnej

Podczas pracy nad aplikacją prawdopodobnie będziesz chcieć wprowadzić zmiany i zobaczyć, jak są wdrożone. Możesz łatwo skonfigurować webhook, który będzie wyzwalać nową kompilację i wdrożenie przy każdym zatwierdzeniu kodu. Jednak może to nie być pożądane, ponieważ czasami chcesz zobaczyć zmiany bez konieczności wypychania każdej zmiany kodu do repozytorium.

W przypadkach, gdy szybko iteruje się po zmianach, można użyć tak zwanej kompilacji binarnej. Aby zademonstrować ten scenariusz, sklonuj repozytorium Git aplikacji lokalnie, uruchamiając:

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Spowoduje to utworzenie podkata `django-ex` katalogu zawierającego kod źródłowy aplikacji:

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Zmień katalog na podrzędny:

```azurecli-interactive
cd django-ex
```

Otwórz zintegrowany edytor Azure Cloud Shell:

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Przewiń w dół i zmień wiersz, który mówi `Welcome to your Django application on OpenShift` , aby powiedzieć `Welcome to Azure Red Hat OpenShift` . Zapisz plik i zamknij edytor za pomocą `...` menu w prawym górnym rogu.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Azure Red Hat OpenShift z usługi Git — edytowanie aplikacji w Azure Cloud Shell edytorze":::

Uruchom nową kompilację, uruchamiając polecenie :

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

Przekazując flagę , wiersz polecenia openshift przekaże kod źródłowy z określonego katalogu, a następnie zainicjuje `--from-dir=.` proces kompilacji i wdrażania. Powinny zostać wykonane dane wyjściowe podobne do poniższych, a po kilku minutach kompilacja powinna zostać ukończona.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Jeśli odświeżysz przeglądarkę przy użyciu aplikacji, powinien zostać wyświetlony zaktualizowany tytuł.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Azure Red Hat OpenShift z usługi Git — przeglądaj zaktualizowaną aplikację":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po pracy z aplikacją możesz uruchomić następujące polecenie, aby usunąć projekt:

```azurecli-interactive
oc delete project demoproject
```

Klaster można również usunąć, korzystając z instrukcji z tematu Samouczek: usuwanie klastra [Azure Red Hat OpenShift 4.](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku opisano sposób:
> [!div class="checklist"]
>
> * Tworzenie projektu
> * Wdrażanie aplikacji z kodu źródłowego przy użyciu konsoli sieci Web
> * Wdrażanie aplikacji z kodu źródłowego przy użyciu wiersza polecenia openshift
> * Wyzwalanie kompilacji binarnej przy użyciu wiersza polecenia openshift

Dowiedz się więcej na temat sposobu kompilowania i wdrażania aplikacji przy użyciu strategii "źródło-obraz" [i innych strategii kompilacji.](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)
