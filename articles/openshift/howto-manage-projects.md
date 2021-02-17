---
title: Zarządzanie zasobami na platformie Azure Red Hat OpenShift | Microsoft Docs
description: Zarządzanie projektami, szablonami, strumieniami obrazów w klastrze Red Hat OpenShift na platformie Azure
services: openshift
keywords: projekty Red Hat OpenShift żądają samoobsługowego udostępniania
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: azure-redhat-openshift
ms.openlocfilehash: bf2cf5a0d41af15821035c615fe071c8580e125f
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633108"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Zarządzanie projektami, szablonami, strumieniami obrazów w klastrze Red Hat OpenShift na platformie Azure

> [!IMPORTANT]
> Usługa Azure Red Hat OpenShift 3,11 zostanie wycofana 30 czerwca 2022. Obsługa tworzenia nowych klastrów usługi Azure Red Hat OpenShift 3,11 jest kontynuowana do 30 listopada 2020. Po wycofaniu pozostałe klastry usługi Azure Red Hat OpenShift 3,11 zostaną zamknięte, aby zapobiec występowaniu luk w zabezpieczeniach.
> 
> Postępuj zgodnie z tym przewodnikiem, aby [utworzyć klaster usługi Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Jeśli masz określone pytania, [skontaktuj się z nami](mailto:arofeedback@microsoft.com).

Na platformie kontenera OpenShift projekty są używane do grupowania i izolowania powiązanych obiektów. Jako administrator możesz dać deweloperom dostęp do określonych projektów, zezwolić im na tworzenie własnych projektów i udzielanie im praw administracyjnych do poszczególnych projektów.

## <a name="self-provisioning-projects"></a>Projekty samoobsługowego udostępniania

Możesz umożliwić deweloperom tworzenie własnych projektów. Punkt końcowy interfejsu API jest odpowiedzialny za obsługę administracyjną projektu zgodnie z szablonem o nazwie Project-Request. Konsola sieci Web i `oc new-project` polecenie korzystają z tego punktu końcowego podczas tworzenia nowego projektu przez dewelopera.

Gdy żądanie projektu zostanie przesłane, interfejs API zastępuje następujące parametry w szablonie:

| Parametr               | Opis                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | Nazwa projektu. Wymagane.             |
| PROJECT_DISPLAYNAME     | Nazwa wyświetlana projektu. Może być pusty. |
| PROJECT_DESCRIPTION     | Opis projektu. Może być pusty.  |
| PROJECT_ADMIN_USER      | Nazwa użytkownika administratora.       |
| PROJECT_REQUESTING_USER | Nazwa użytkownika żądającego.           |

Dostęp do interfejsu API jest udzielany deweloperom z powiązaniem roli klastra samoobsługi. Ta funkcja jest domyślnie dostępna dla wszystkich uwierzytelnionych deweloperów.

## <a name="modify-the-template-for-a-new-project"></a>Modyfikuj szablon dla nowego projektu 

1. Zaloguj się jako użytkownik z `customer-admin` uprawnieniami.

2. Edytuj domyślny szablon żądania projektu.

   ```
   oc edit template project-request -n openshift
   ```

3. Usuń domyślny szablon projektu z procesu aktualizacji Red Hat OpenShift (ARO) platformy Azure, dodając następującą adnotację: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Szablon żądania projektu nie zostanie zaktualizowany przez proces aktualizacji ARO. Dzięki temu klienci mogą dostosować szablon i zachować te dostosowania podczas aktualizowania klastra.

## <a name="disable-the-self-provisioning-role"></a>Wyłącz rolę samoobsługowego aprowizacji

Można uniemożliwić uwierzytelnionej grupie użytkowników możliwość samoobsługowego udostępniania nowych projektów.

1. Zaloguj się jako użytkownik z `customer-admin` uprawnieniami.

2. Edytuj powiązanie roli klastra Provisioning.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Usuń rolę z procesu aktualizacji ARO, dodając następującą adnotację: `openshift.io/reconcile-protect: "true"` .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Zmień powiązanie roli klastra, aby zapobiec `system:authenticated:oauth` tworzeniu projektów:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Zarządzanie szablonami domyślnymi i imageStreams

Na platformie Azure Red Hat OpenShift można wyłączyć aktualizacje dla wszystkich domyślnych szablonów i strumieni obrazów w `openshift` przestrzeni nazw.
Aby wyłączyć aktualizacje dla wszystkich `Templates` i `ImageStreams` w `openshift` przestrzeni nazw:

1. Zaloguj się jako użytkownik z `customer-admin` uprawnieniami.

2. Edytuj `openshift` przestrzeń nazw:

   ```
   oc edit namespace openshift
   ```

3. Usuń `openshift` przestrzeń nazw z procesu aktualizacji ARO, dodając następującą adnotację: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Każdy pojedynczy obiekt w `openshift` przestrzeni nazw można usunąć z procesu aktualizacji, dodając `openshift.io/reconcile-protect: "true"` do niego adnotację.

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
