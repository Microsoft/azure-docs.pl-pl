---
title: Integrowanie konfiguracji aplikacji platformy Azure z wdrożeniem Kubernetes za pomocą Helm
description: Dowiedz się, jak używać konfiguracji dynamicznych we wdrożeniu Kubernetes z Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 4e38366ddcee07f38ca390acf9d580b8764c1c00
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979831"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integracja z wdrażaniem Kubernetes przy użyciu usługi Helm

Helm umożliwia definiowanie, Instalowanie i Uaktualnianie aplikacji działających w Kubernetes. Wykres Helm zawiera informacje niezbędne do utworzenia wystąpienia aplikacji Kubernetes. Konfiguracja jest przechowywana poza wykresem, w pliku o nazwie *Values. YAML*. 

W trakcie procesu zwalniania program Helm Scala wykres z odpowiednią konfiguracją do uruchamiania aplikacji. Na przykład zmienne zdefiniowane w *wartości. YAML* można odwoływać się jako zmienne środowiskowe wewnątrz uruchomionych kontenerów. Helm obsługuje również tworzenie wpisów tajnych Kubernetes, które mogą być instalowane jako woluminy danych lub uwidocznione jako zmienne środowiskowe.

Wartości przechowywane w *wartościach. YAML* można zastąpić przez udostępnienie dodatkowych YAML plików konfiguracji w wierszu polecenia podczas uruchamiania Helm. Konfiguracja aplikacji platformy Azure obsługuje eksportowanie wartości konfiguracji do plików YAML. Integracja tej możliwości eksportu z wdrożeniem umożliwia aplikacjom Kubernetes korzystanie z wartości konfiguracyjnych przechowywanych w konfiguracji aplikacji.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Użyj wartości z konfiguracji aplikacji podczas wdrażania aplikacji do Kubernetes przy użyciu Helm.
> * Utwórz wpis tajny Kubernetes na podstawie odwołania Key Vault w konfiguracji aplikacji.

W tym samouczku założono podstawowe informacje dotyczące zarządzania Kubernetes z Helm. Dowiedz się więcej o instalowaniu aplikacji z programem Helm w [usłudze Azure Kubernetes Service](../aks/kubernetes-helm.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Instalowanie [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (wersja 2.4.0 lub nowsza)
- Zainstaluj [Helm](https://helm.sh/docs/intro/install/) (wersja 2.14.0 lub nowsza)
- Klaster Kubernetes.

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz pozycję **Eksplorator konfiguracji**  >  **Utwórz** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | Settings. Color | Biały |
    | Settings. Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodaj odwołanie Key Vault do konfiguracji aplikacji
1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Dodaj klucz tajny do [Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) przy użyciu **hasła** nazwy i **wartości hasło**. 
2. Wybierz wystąpienie magazynu konfiguracji aplikacji, które zostało utworzone w poprzedniej sekcji.

3. Wybierz pozycję **Eksplorator konfiguracji**.

4. Wybierz pozycję **+ Utwórz**  >  **odwołanie do magazynu kluczy**, a następnie określ następujące wartości:
    - **Klucz**: wybierz pozycję Secret **. Password**.
    - **Etykieta**: pozostaw tę wartość pustą.
    - **Subskrypcja**, **Grupa zasobów** i **Magazyn kluczy**: wprowadź wartości odpowiadające tym w magazynie kluczy utworzonym w poprzednim kroku.
    - **Wpis tajny**: Wybierz **hasło** o nazwie Secret utworzone w poprzedniej sekcji.

## <a name="create-helm-chart"></a>Utwórz wykres Helm ##
Najpierw Utwórz przykładowy wykres Helm za pomocą następującego polecenia
```console
helm create mychart
```

Helm tworzy nowy katalog o nazwie webchart z przedstawioną poniżej strukturą. 

> [!TIP]
> Aby dowiedzieć się więcej, postępuj zgodnie z tym [przewodnikiem wykresów](https://helm.sh/docs/chart_template_guide/getting_started/) .

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Następnie zaktualizuj sekcję **spec: Template: spec: Containers** w pliku *Deployment. YAML* . Poniższy fragment kodu dodaje dwie zmienne środowiskowe do kontenera. Wartości będą ustawiane dynamicznie w czasie wdrażania.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Kompletny plik *Deployment. YAML* po aktualizacji powinien wyglądać jak poniżej.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Aby przechowywać dane poufne jako wpisy tajne Kubernetes, Dodaj plik Secret *. YAML* w folderze Templates.

> [!TIP]
> Dowiedz się więcej na temat korzystania z wpisów [tajnych Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Na koniec Zaktualizuj plik *Values. YAML* o następującej zawartości, aby opcjonalnie podać wartości domyślne ustawień konfiguracji i wpisów tajnych, do których odwołuje się plik *Deployment. YAML* i Secret *. YAML* . Ich rzeczywiste wartości zostaną zastąpione konfiguracją pobieraną z konfiguracji aplikacji.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Przekazanie konfiguracji z konfiguracji aplikacji w instalacji Helm ##
Najpierw Pobierz konfigurację z konfiguracji aplikacji do pliku *YAML.* Użyj filtru kluczy, aby pobrać tylko te klucze, które zaczynają się od **ustawień.**. Jeśli w Twoim przypadku filtr klucza nie wystarcza do wykluczenia kluczy odwołań Key Vault, możesz użyć tego argumentu **--Skip-** Key, aby je wykluczyć. 

> [!TIP]
> Dowiedz się więcej o [poleceniu eksportu](/cli/azure/appconfig/kv#az-appconfig-kv-export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Następnie Pobierz klucze tajne do pliku o nazwie *YAML*. Argument wiersza polecenia **--Rozwiązuj-** kluczy rozwiązuje Key Vault odwołań przez pobranie wartości rzeczywistych w Key Vault. Należy uruchomić to polecenie z poświadczeniami, które mają uprawnienia dostępu do odpowiednich Key Vault.

> [!WARNING]
> Ponieważ ten plik zawiera poufne informacje, Zachowaj ostrożność i oczyść plik, gdy nie jest już potrzebne.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Użyj argumentu Helm upgrade **-f** , aby przekazać dwa utworzone pliki konfiguracji. Zastąpią one wartości konfiguracyjne zdefiniowane w *wartości. YAML* wartościami wyeksportowanymi z konfiguracji aplikacji.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Można również użyć argumentu **--Set** do uaktualnienia Helm, aby przekazać wartości klucza literału. Użycie argumentu **--Set** jest dobrym sposobem na uniknięcie utrwalania poufnych danych na dysku. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Sprawdź, czy konfiguracje i wpisy tajne zostały pomyślnie ustawione, uzyskując dostęp do [pulpitu nawigacyjnego Kubernetes](../aks/kubernetes-dashboard.md). Zobaczysz, że wartości **kolorów** i **komunikatów** z konfiguracji aplikacji zostały wypełnione zmiennymi środowiskowymi kontenera.

![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/kubernetes-dashboard-env-variables.png)

Jeden wpis tajny, **hasło**, sklepy jako odwołanie Key Vault w konfiguracji aplikacji został również dodany do wpisów tajnych Kubernetes. 

![Zrzut ekranu, który podświetla hasło w sekcji danych.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wyeksportowano dane konfiguracji aplikacji platformy Azure, które będą używane we wdrożeniu Kubernetes z Helm. Aby dowiedzieć się więcej na temat korzystania z konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Interfejs wiersza polecenia platformy Azure](/cli/azure/appconfig)
