---
title: Jak rozwiązywać problemy z usługą Container Insights | Microsoft Docs
description: W tym artykule opisano, jak rozwiązywać problemy z usługą Container Insights i je rozwiązywać.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: b7618e9073308da67a8e17c82375a0f05925a542
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627119"
---
# <a name="troubleshooting-container-insights"></a>Rozwiązywanie problemów dotyczących usługi Container Insights

W przypadku skonfigurowania monitorowania klastra usługi Azure Kubernetes Service (AKS) z usługą Container Insights może wystąpić problem uniemożliwiający zbieranie danych lub raportowanie stanu. W tym artykule opisano niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Błąd autoryzacji podczas operacji dołączania lub aktualizowania

Podczas włączania usługi Container Insights lub aktualizowania klastra w celu obsługi zbierania metryk może zostać wyświetlony błąd podobny do poniższego: *klient <tożsamość użytkownika> "z identyfikatorem obiektu" <objectid użytkownika> "nie ma autoryzacji do wykonania akcji" Microsoft. Authorization/roleAssignments/Write "w zakresie*

Podczas procesu dołączania lub aktualizacji należy podjąć próbę przypisania roli **wydawcy metryk monitorowania** do zasobu klastra. Użytkownik inicjujący proces włączania usługi Container Insights lub aktualizacji do obsługi kolekcji metryk musi mieć dostęp do uprawnienia **Microsoft. Authorization/roleAssignments/Write** w zakresie zasobów klastra AKS. Dostęp do tego uprawnienia mają tylko członkowie roli wbudowanej **administratora dostępu** **właściciela** i użytkownika. Jeśli zasady zabezpieczeń wymagają przypisania uprawnień na poziomie szczegółowości, zalecamy wyświetlenie [ról niestandardowych](../../role-based-access-control/custom-roles.md) i przypisanie ich do użytkowników, którzy ich potrzebują.

Możesz również ręcznie udzielić tej roli z Azure Portal, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Kubernetes**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Azure Kubernetes**.
3. Na liście klastrów Kubernetes wybierz jedną z listy.
2. W menu po lewej stronie kliknij pozycję **Kontrola dostępu (IAM)**.
3. Wybierz pozycję **+ Dodaj** , aby dodać przypisanie roli i wybierz rolę **wydawcy metryk monitorowania** i w obszarze **Wybierz** typ **AKS** , aby filtrować wyniki tylko do podmiotów usługi klastrów zdefiniowanych w ramach subskrypcji. Wybierz z listy odpowiedni dla tego klastra.
4. Wybierz pozycję **Zapisz** , aby zakończyć Przypisywanie roli.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Usługi Container Insights są włączone, ale nie zgłaszają żadnych informacji

Jeśli szczegółowe informacje o kontenerze zostały pomyślnie włączone i skonfigurowane, ale nie będzie można wyświetlić informacji o stanie lub nie zostaną zwrócone żadne wyniki zapytania dziennika, można zdiagnozować problem, wykonując następujące czynności:

1. Sprawdź stan agenta, uruchamiając polecenie:

    `kubectl get ds omsagent --namespace=kube-system`

    Dane wyjściowe powinny wyglądać podobnie do poniższego przykładu, który wskazuje, że został wdrożony prawidłowo:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Jeśli masz węzły systemu Windows Server, sprawdź stan agenta, uruchamiając polecenie:

    `kubectl get ds omsagent-win --namespace=kube-system`

    Dane wyjściowe powinny wyglądać podobnie do poniższego przykładu, który wskazuje, że został wdrożony prawidłowo:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Sprawdź stan wdrożenia z agentem w wersji *06072018* lub nowszej przy użyciu polecenia:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Dane wyjściowe powinny wyglądać podobnie do poniższego przykładu, który wskazuje, że został wdrożony prawidłowo:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Sprawdź stan usługi pod, aby sprawdzić, czy działa ona przy użyciu polecenia: `kubectl get pods --namespace=kube-system`

    Dane wyjściowe powinny wyglądać podobnie do następującego przykładu ze stanem *uruchomione* dla omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Komunikaty o błędach

W poniższej tabeli zestawiono znane błędy, które można napotkać podczas korzystania z usługi Container Insights.

| Komunikaty o błędach  | Akcja |
| ---- | --- |
| Komunikat o błędzie `No data for selected filters`  | Uruchomienie przepływu danych monitorowania z nowo utworzonych klastrów może zająć trochę czasu. Zezwalaj na wyświetlanie danych dla klastra co najmniej 10 do 15 minut. |
| Komunikat o błędzie `Error retrieving data` | Podczas konfigurowania klastra usługi Azure Kubernetes na potrzeby monitorowania kondycji i wydajności jest nawiązywane połączenie między klastrem i obszarem roboczym usługi Azure Log Analytics. Obszar roboczy Log Analytics służy do przechowywania wszystkich danych monitorowania dla klastra. Ten błąd może wystąpić, gdy obszar roboczy Log Analytics został usunięty. Sprawdź, czy obszar roboczy został usunięty, a jeśli był, należy ponownie włączyć monitorowanie klastra przy użyciu usługi Container Insights i określić istniejący lub nowy obszar roboczy. Aby ponownie włączyć, należy [wyłączyć](container-insights-optout.md) monitorowanie dla klastra i ponownie [włączyć](container-insights-enable-new-cluster.md) usługi Container Insights. |
| `Error retrieving data` Po dodaniu szczegółowych informacji o kontenerach za poorednictwem polecenia AZ AKS CLI | Po włączeniu monitorowania za pomocą usługi `az aks cli` , szczegółowe informacje kontenera mogą nie być poprawnie wdrożone. Sprawdź, czy rozwiązanie zostało wdrożone. Aby sprawdzić, przejdź do obszaru roboczego Log Analytics i sprawdź, czy rozwiązanie jest dostępne, wybierając pozycję **rozwiązania** z okienka po lewej stronie. Aby rozwiązać ten problem, należy ponownie wdrożyć rozwiązanie, postępując zgodnie z instrukcjami dotyczącymi wdrażania usługi [Container Insights](container-insights-onboard.md) |

Aby ułatwić zdiagnozowanie problemu, podano [skrypt rozwiązywania problemów](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot).

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>ReplicaSetki agentów usługi Container Insights nie są planowane w klastrze Kubernetes innym niż Azure

Moduł ReplicaSets agenta usługi Container Insights ma zależność od następujących selektorów węzłów w węzłach procesu roboczego (lub agenta) do planowania:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Jeśli węzły procesu roboczego nie mają dołączonych etykiet, to nie zostanie zaplanowana usługa ReplicaSet agentów. Aby uzyskać instrukcje na temat sposobu dołączania etykiety, zapoznaj się z [Kubernetesą selektorów etykiet](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) .

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Wykresy wydajności nie pokazują procesora ani pamięci węzłów i kontenerów w klastrze nienależącym do platformy Azure

Moduł do zbierania informacji o kontenerach usługi Container Insights używa punktu końcowego cAdvisor w agencie węzła w celu zebrania metryk wydajności. Upewnij się, że kontener kontenera w węźle został skonfigurowany tak, aby zezwalać na `cAdvisor port: 10255` otwieranie go we wszystkich węzłach w klastrze w celu zbierania metryk wydajności.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>Klaster Kubernetes spoza platformy Azure nie jest wyświetlany w usłudze Container Insights

Aby wyświetlić klaster programu spoza platformy Azure Kubernetes w usłudze Container Insights, należy uzyskać dostęp do odczytu w obszarze roboczym Log Analytics, który obsługuje tę usługę Insights, oraz w obszarze roboczym rozwiązania usługi Container Insights **ContainerInsights (*przestrzeń robocza*)**.

## <a name="metrics-arent-being-collected"></a>Metryki nie są zbierane

1. Sprawdź, czy klaster jest w [obsługiwanym regionie dla metryk niestandardowych](../essentials/metrics-custom-overview.md#supported-regions).

2. Sprawdź, czy przypisanie roli **wydawcy metryk monitorowania** istnieje przy użyciu poniższego polecenia interfejsu CLI:

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    W przypadku klastrów z pakietem MSI identyfikator klienta przypisany przez użytkownika dla omsagent zmienia się przy każdym włączeniu i wyłączaniu monitorowania, dlatego przypisanie roli powinno istnieć na bieżącym identyfikatorze klienta MSI. 

3. W przypadku klastrów z włączoną tożsamością Azure Active Directory pod i użyciem pliku MSI:

   - Sprawdź wymaganą etykietę **Kubernetes.Azure.com/ManagedBy: AKS**  jest obecny w zasobnikach omsagent przy użyciu następującego polecenia:

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - Upewnij się, że wyjątki są włączone, gdy tożsamość pod jest włączona przy użyciu jednej z obsługiwanych metod w https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity .

        Uruchom następujące polecenie, aby zweryfikować:

        `kubectl get AzurePodIdentityException -A -o yaml`

        Powinny zostać wyświetlone dane wyjściowe podobne do następujących:

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```



## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania pod kątem przechwytywania metryk kondycji zarówno dla węzłów klastra AKS, jak i dla każdego zasobnika te metryki kondycji są dostępne w Azure Portal. Aby dowiedzieć się, jak używać usługi Container Insights, zobacz [Wyświetlanie kondycji usług Kubernetes platformy Azure](container-insights-analyze.md).
