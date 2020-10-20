---
title: Uruchamianie uprzywilejowanych kontenerów w klastrze Red Hat OpenShift platformy Azure | Microsoft Docs
description: Uruchom uprzywilejowane kontenery, aby monitorować zabezpieczenia i zgodność.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: ARO, OpenShift, aquasec, TwistLock, Red Hat
ms.openlocfilehash: 914b29410a0f30e5c3d3a893c2e278ecbb83b648
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218871"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Uruchamianie uprzywilejowanych kontenerów w klastrze usługi Azure Red Hat OpenShift

> [!IMPORTANT]
> Usługa Azure Red Hat OpenShift 3,11 zostanie wycofana 30 czerwca 2022. Obsługa tworzenia nowych klastrów usługi Azure Red Hat OpenShift 3,11 jest kontynuowana do 30 listopada 2020. Po wycofaniu pozostałe klastry usługi Azure Red Hat OpenShift 3,11 zostaną zamknięte, aby zapobiec występowaniu luk w zabezpieczeniach.
> 
> Postępuj zgodnie z tym przewodnikiem, aby [utworzyć klaster usługi Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Jeśli masz określone pytania, [skontaktuj się z nami](mailto:arofeedback@microsoft.com).

Nie można uruchamiać dowolnych kontenerów uprzywilejowanych w klastrach Red Hat OpenShift platformy Azure.
W przypadku klastrów ARO mogą działać dwa rozwiązania do monitorowania zabezpieczeń i zgodności.
W tym dokumencie opisano różnice między ogólną dokumentacją wdrożenia OpenShift dostawcy produktu zabezpieczeń.


Przeczytaj te instrukcje przed wykonaniem instrukcji dotyczących dostawcy.
Tytuły sekcji w krokach specyficznych dla produktu odnoszą się bezpośrednio do tytułów sekcji w dokumentacji dostawcy.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W dokumentacji większości produktów zabezpieczeń założono, że masz uprawnienia do administrowania klastrem.
Administratorzy klienta nie mają wszystkich uprawnień na platformie Azure Red Hat OpenShift. Uprawnienia wymagane do modyfikacji zasobów na poziomie klastra są ograniczone.

Najpierw upewnij się, że użytkownik jest zalogowany do klastra jako administrator klienta, uruchamiając polecenie `oc get scc` . Wszyscy użytkownicy, którzy są członkami grupy Administratorzy klienta, mają uprawnienia do wyświetlania ograniczeń kontekstu zabezpieczeń (SCCs) w klastrze.

Następnie upewnij się, że `oc` wersja binarna to `3.11.154` .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Kroki specyficzne dla produktu dotyczące zabezpieczeń akwamaryna
Podstawowe instrukcje, które mają zostać zmodyfikowane, można znaleźć w dokumentacji dotyczącej [wdrażania zabezpieczeń](https://docs.aquasec.com/docs/openshift-red-hat)w formie akwamaryna. Kroki opisane w tym miejscu zostaną wykonane w połączeniu z dokumentacją dotyczącą wdrażania.

Pierwszym krokiem jest adnotacja wymaganych SCCs, które zostaną zaktualizowane. Te adnotacje uniemożliwiają synchronizacji klastra pod względem wycofywania zmian wprowadzonych w tych SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Krok 1. Przygotowanie wymagań wstępnych
Pamiętaj, aby zalogować się do klastra jako administrator wystawcy klienta, a nie jako rolę administratora klastra.

Utwórz projekt i konto usługi.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Zamiast przypisywać rolę czytnika klastra, należy przypisać rolę "klient-administrator-klaster" do konta akwamaryna przy użyciu poniższego polecenia.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Kontynuuj zgodnie z pozostałymi instrukcjami w kroku 1.  Te instrukcje opisują Konfigurowanie wpisu tajnego dla rejestru akwamaryna.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Krok 2. Wdrażanie serwera, bazy danych i bramy
Postępuj zgodnie z instrukcjami podanymi w dokumentacji dotyczącej instalacji akwamaryna-Console. YAML.

Zmodyfikuj podaną wartość `aqua-console.yaml` .  Usuń pierwsze dwa obiekty z etykietą `kind: ClusterRole` i `kind: ClusterRoleBinding` .  Te zasoby nie zostaną utworzone, ponieważ administrator klienta nie ma w tym momencie uprawnień do modyfikowania `ClusterRole` i `ClusterRoleBinding` obiektów.

Druga modyfikacja będzie `kind: Route` częścią `aqua-console.yaml` . Zastąp następujący YAML dla `kind: Route` obiektu w `aqua-console.yaml` pliku.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Postępuj zgodnie z pozostałymi instrukcjami.

### <a name="step-3-login-to-the-aqua-server"></a>Krok 3. Logowanie na serwerze z serwerem akwamaryna
Ta sekcja nie jest modyfikowana w żaden sposób.  Postępuj zgodnie z dokumentacją.

Użyj poniższego polecenia, aby pobrać adres konsoli akwamaryna.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Krok 4. wdrażanie wymuszeń akwamaryna
Podczas wdrażania wymuszania należy określić następujące pola:

| Pole          | Wartość         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | akwamaryna — konto  |
| Project        | akwamaryna — zabezpieczenia |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Kroki specyficzne dla produktu Prisma Cloud/TwistLock

Instrukcje podstawowe, które zamierzamy zmodyfikować, można znaleźć w [dokumentacji wdrożenia w chmurze Prisma](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Zacznij od zainstalowania `twistcli` narzędzia zgodnie z opisem w sekcji "Install Prisma Cloud" i "Pobierz oprogramowanie w chmurze Prisma".

Utwórz nowy projekt OpenShift
```
oc new-project twistlock
```

Pomiń sekcję opcjonalną "wypchnij obrazy w chmurze Prisma do prywatnego rejestru". Nie będzie on działał na platformie Azure Red Hat OpenShift. Zamiast tego należy użyć rejestru online.

Oficjalną dokumentację można wykonać przy zastosowaniu poprawek opisanych poniżej.
Rozpocznij od sekcji "Instalowanie konsoli".

### <a name="install-console"></a>Zainstaluj konsolę

`oc create -f twistlock_console.yaml`W kroku 2 wystąpi błąd podczas tworzenia przestrzeni nazw.
Można je bezpiecznie zignorować, dlatego przestrzeń nazw została wcześniej utworzona za pomocą `oc new-project` polecenia.

Użyj `azure-disk` dla typu magazynu.

### <a name="create-an-external-route-to-console"></a>Tworzenie trasy zewnętrznej w konsoli

Możesz wykonać jedną z dokumentacji lub poniższe instrukcje, jeśli wolisz użyć polecenia oC.
Skopiuj poniższą definicję trasy do pliku o nazwie twistlock_route. YAML na komputerze
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
następnie uruchom polecenie:
```
oc create -f twistlock_route.yaml
```

Za pomocą tego polecenia można uzyskać adres URL przypisany do konsoli TwistLock: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Konfiguruj konsolę

Postępuj zgodnie z dokumentacją TwistLock.

### <a name="install-defender"></a>Instalowanie Defender

`oc create -f defender.yaml`W kroku 2 podczas tworzenia roli klastra i powiązania roli klastra wystąpią błędy.
Można je zignorować.

Obrona zostanie wdrożona tylko w węzłach obliczeniowych. Nie trzeba ograniczać ich przy użyciu selektora węzłów.
